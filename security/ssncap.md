---
id: 26
title: ssncap
date: 2010-08-30T22:50:37+00:00
author: admin
layout: page
guid: https://www.inetric.com/wordpress/?page_id=26
---
<p id="top" />

<div class="cnotice" style="text-align:center;">
  <a href="#description">Description</a> | <a href="#reporting">Reporting</a> | <a href="#features">Features</a> | <a href="#usage">Usage</a> | <a href="/security/ssncap/order">Order</a> | <a href="/security/ssncap/documentation">Documentation</a>
</div>

### <a name="description">Description</a>

Packet analyzer to search for valid clear-text social security numbers.
</p>

Social security numbers should not be sent over a network in clear text. This program uses libpcap to read a packet dump file or capture live packets and scans through them for social security numbers. It is written to be as fast and accurate as possible. Use it to make sure applications aren&#8217;t sending a number in the clear.

### <a name="Encryption">Encryption</a>

ssncap can use on-the-fly public key / private key encryption / decryption to keep the information it captures protected. The data is encrypted before it is written to disk and is maintained in an encrypted state, even during post-capture analysis. The [openssl](http://www.openssl.org/) library is required for this functionality.

### <a name="reporting">Reporting</a>

[<img src="/wp-content/uploads/2010/08/ssncapreport-300x214.png" alt="ssncap graph report" title="ssncap report" width="300" height="214" class="alignright size-medium wp-image-59" srcset="https://inetric.com/wp-content/uploads/2010/08/ssncapreport-300x214.png 300w, https://inetric.com/wp-content/uploads/2010/08/ssncapreport.png 913w" sizes="(max-width: 300px) 100vw, 300px" />](/wp-content/uploads/2010/08/ssncapreport.png)

#### Graphs

The report tool takes the output of ssncap and creates an HTML page interactive graphs, viewable in any JavaScript enabled browser. The information includes SSNs found over time, the top ten IPs with SSNs found, and the top five ports used for transfer. Mouse over points on the graphs to get more detailed information about when an incident occurred.

#### Comma Separated Values (CSV)

The reporting tool can also output into Comma Separated Values (CSV) format. These reports can then be imported into spread sheet programs, such as Excel, and manipulated and organized from there. This flexibility allows for new custom graphs to be made from the data.

#### Email

Using ssncap&#8217;s threshold option and cycling options, scripts can easily be created to notify administrators when an incident occurs.

#### Syslog

ssncap also has the ability to use syslog. Using syslog, ssncap can be integrated with other reporting tools such as [OSSIM](http://www.alienvault.com/products.php?section=OpenSourceSI).

### <a name="features">Features</a>

  * Optimized search algorithm for SSNs
  * On the fly public/private key encryption/decryption
  * Can parse live network traffic
  * Uses the well known BPF filters
  * Search for given list of numbers
  * Analysis mode to score SSNs based on packet content
  * Threshold option to display SSNs above a certain score
  * Syslog support
  * Redaction mode
  * Cycle capture files after N seconds, optionally running a command, and keeping streams intact when in stream mode
  * Dump matching packets to a file
  * Dump N packets before/after the packet containing an SSN
  * Dump queued, matching, and all packets to separate files simultaneously
  * Reverse mode to show only invalid numbers
  * Can show a reason for a number being invalid

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

### Order

Send inquiries to <span style="text-decoration:underline;">s<!--s-->snc<!--p-->ap@inet<!--a-->ric<!--m-->.com</span> or call us at **+1 72 INETRIC 2 [+1 (724) 638 7422]**.
