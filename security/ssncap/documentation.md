---
id: 85
title: ssncap Documentation
date: 2010-09-12T02:19:29+00:00
author: michael
layout: post
guid: http://www.inetric.com
---
<p id="top" />

<div class="cnotice" style="text-align:center;">
  <a href="#usage">Usage</a> | <a href="#output">Output</a> | <a href="#examples">Examples</a> | <a href="#cryptography">Cryptography</a> | <a href="/security/ssncap/order">Order</a>
</div>

ssncap supports many options to be as flexible as possible. It can redact the output to conceal social security numbers, encrypt the data to keep it secure for storage, use BPF filters, and log information to syslog to name a few capabilities. To improve security, ssncap can switch to a lower privileged user and/or group after opening the capture device. Running ssncap -h will give you all the available options.

### <a name="usage">Usage</a>

<pre>
Usage: ./ssncap [-aChjlLsdDvenRslqvX] [-A NUMAFTER] [-B QBEFORE] [-c COUNT]
                [-t THRESHOLD] [-f FOUNDDUMPFILE] [-p SNAPLEN]
                [-S STREAMFILE] [-w DUMPFILE] [-r RPCAP] [-i INTERFACE]
                [-k PUBKF] [-K PRIVKF]
                [-N NUMBERLIST] [-g GROUP] [-u USER] [FILTER]

A packet source is required for non-live capture.
If no packet source is given when live capture is selected, the first
suitable device will be used.
BPF Filter can be specified after all options akin to tcpdump.

Options:
-a         Run improved analysis mode.
-A NUM     Save NUM packets after ssn found.
-B NUM     Buffer NUM packets before ssn found - Enables -C.
-c COUNT   Stop after receiving COUNT packets.
-C         Enable packet circular buffer.
-l         Log packet information (-v) to SYSLOG instead of STDOUT.
-L         Log packet information (-v) to both SYSLOG and STDOUT.
-d         Show all duplicates.
-D         Enable redaction dump mode - All digits in all packets written
           to disk and stdout are replaced with 'X'.
-e         Match both with and without dashes.
-f FILE    Save matching packets to FILE.
-g GROUP   Change group to GROUP after opening device.
-G N       Cycle files about every N seconds - Keeps streams for -S intact.
           If the filter is not receiving packets, the files will not be cycled
           until a packet is received.
           File names can use the format specifiers defined by strftime(3).
-k PUBKF   Read public key from file PUBKF to encrypt packet data.
-K PRIVKF  Read private key from file PRIVKF to decrypt packet data.
-i IFACE   Read live traffic from IFACE.
-j         Enable area code and group check.
           No longer valid after Randomized SSN Assignments started on
           June 25, 2011.
-w FILE    Save ALL packets to FILE.
-n         Try for no duplicates.
-N NLIST   Load important numbers from NLIST.
-p SNAP    Set the snap length of the capture.
-R         Reverse mode - show only invalids.
-s         Calculate and show statistics.
-t THRESH  Only show those with score greater than TRHESH - Enables -a.
-r RPCAP   Read from pcap file RPCAP.
-q         Quiet - Nothing is printed to stdout unless a -v or -s is given.
-S FILE    Save N packets before/after found SSN to FILE.
-T FILE    Load table file FILE.
-u USER    Change user to USER after opening device.
-x         Enable redaction print mode - All digits in all packets written
           to STDOUT are replaced with 'X'.
-X         Print packt data in Hex and ASCII.
*****
-z CMD     OLD: Run CMD after every file rotation. The file names will be
           passed as command line arguments. The file order is -w -S -f.
*****
-z CMD     Run CMD once per file after every rotation. The file name will be
           passed as a command line argument.
-v         Used to show the packet info.
-vv        Same as -v but in addition show the packet data.
-vvv       Same as -vv but in addition show rejected fakes and a reason.
-vvvv      Same as -vvv but in addition show the packet data for rejected fakes.
-vvvvv     Same as -vvvv but in addition show collisions.
-vvvvvv    Same as -vvvvv but in addition show all packet data.
FILTER     Apply bpf filter FILTER to the capture.
</pre>

### <a name="output">Output</a>

The output ssncap will give by default consists of the social security number found, the packet number it was found in, the byte in the packet where the number starts, and a score. The score will always be zero unless analysis mode (-a option) is enabled. This score is used for the threshold option (-t).
  
<pre>
AAA-GG-XXXX : packet, byte;  score
</pre>

When statistics are enabled (-s option), ssncap will output at the end of the run:
  
<pre>
If capturing live traffic, the packet received and drop counts:
 - Received by filter:  X
 - Dropped by Kernel:   X
        Interface:      X

- Total Packets:          T
- Packets Searched:       P (P/T * 100)
- Average Packet Length:  L
- Average Total Iterations: I (I/L * 100)

Social security number statistics:
- Potentials Found:  F
- Total Accepted:    A (A/F * 100)
- Hash Collisions:   C
</pre>

Specifying multiple -v options on the command line will increase the output. The next level of verbosity will include time and packet information among other statistics.
  
<pre>2010-09-12 07:31:51.549177 UTC; 127.0.0.1:443 > 192.168.1.1:43679 TCP; len:103; pf:0; nf:0;</pre>
  
The time is given in UTC. The source IP and source port are first, followed by the destination IP and port. Len is the length of the packet, PF is the potential numbers found, and NF is the count of numbers accepted as socials.

More levels add packet data to the output, or can include numbers that would have been considered socials, but failed one of the tests.

### <a name="examples">Examples</a>

The simplest example is to run ssncap with the interface to listen on or the file to be read.
  
<pre>
ssncap -i eth0
ssncap -r ./pcap_file
</pre>

It is recommended to run ssncap with the least amount of privilege as possible. To do so, ssncap supports switching its user and group with the -u and -g options respectively.
  
<pre>
ssncap -i eth0 -u nobody -g nobody
</pre>

To listen to live traffic while saving 10 packets before and after the packet containing the social security number to a file:
  
<pre>
ssncap -i eth0 -S stream_file.pcap -B 10 -A 10
</pre>
  
The same but encrypting the resultant file with a public key:
  
<pre>
ssncap -i eth0 -k ./public_key_file.pem -S encrypted_stream_file.pcap -B 10 -A 10
</pre>
  
To print out the found packets and the information about them:
  
<pre>
ssncap -r dump_file.pcap -v
ssncap -r dump_file.pcap -vv
</pre>

If stream mode was used, the packets without socials found in them will not be printed unless enough -v options are specified.
  
<pre>
ssncap -r stream_dump_file.pcap -vvvvvv
</pre>

ssncap can also cycle files after a given interval and optionally run an executable on the file as well. The executable will be passed the saved files&#8217; name as command line arguments.
  
The following command will run ssncap on an interface, maintaining a stream of at most 1000 packets before and after the packet found containing the social security number, cycling the file every 3600 seconds, and running the executable on the cycled file.
  
<pre>
ssncap -i eth0 -a -B 1000 -A 1000 -S stream_dump-%Y-%m-%d-%H%M%s.pcap -G 3600 -z /path/to/executable
</pre>

When cycling is enabled, file names support the time formatting specifiers provided by strftime(3). \*\* Newer versions of ssncap now run the executable once PER each output file, receiving the file name as its only argument. \*\* Since ssncap supports writing to three files simultaneously in different modes (full capture, stream capture, and found capture), the file names will be passed as different arguments to the executable. The first argument will be the full capture file name, the second the stream capture file name, and the third the found capture file name. If any mode is not currently being used, the empty string will be passed in it&#8217;s place.

An example program illustrating how to use the arguments.

<pre>
#!/bin/bash
echo "Full capture file: $1";
echo "Stream capture file: $2";
echo "Found capture file: $3";
</pre>

Capture live traffic, using a threshold score of 6, cycling files after 4 hours, maintaining a stream of at most 1000 packets before and after the packet found containing the social security number, encrypting the packet data to a stream file, specifying a BPF filter to only analyze traffic on port 25, and piping the output to sireport to generate interactive graphs of the data.
  
<pre>./ssncap -aqvv -t 6 -i eth0 -G 14400 -k ./public_key.pem -S stream_dump.pcap port 25 | ./sireport -D -w report-%S.html -G 14400 -q -z ./email_report_exec </pre>

Unlike other programs, ssncap can write the packet data to standard out and still use the cycle option to run executables at the specified interval. This allows chaining of capture programs.

<pre>
./ssncap -a -i eth0 -G 14400 -z ./some_useful_program -S - | tcpdump -r - 
</pre>

### <a name="cryptography">Cryptography</a>

ssncap uses the openssl library for its cryptographic functions. To generate a private key, use the following command:

<pre>openssl genrsa -out private_key.pem 2048</pre>

This command creates a private 2048 bit RSA key, used here for decryption. To create a key with a pass phrase, use the following command instead:
  
<pre>openssl genrsa -aes256 -out private_key.pem 2048</pre>

To generate the corresponding public key, use the following command:

<pre>openssl rsa -in private_key.pem -out public_key.pem -pubout</pre>

This key is used here for encryption.
