# Lab 2 - Information Gathering

### Introduction
The first in class exercise introduced `tcpdump` and wireshark for capturing
packets.  Next we briefly covered `nmap` for basic network and host scans.  In
this lab we will dive deep into scans in an attempt to gather as much
information about a resources using different types of scanning techniques.

### Background
Be sure to brush up on the following topics:
* TCP packets
* TCP Handshake
* UDP packets
* CIDR notation
* Client/Server model
* various network protocols

Useful linux commands related to this lab:
`tcpdump, nmap, ping, python`

### Resources
* You will need a local linux VM for this lab (or access to a system with linux
  installed on your home network), see labtalk in pilot for recommendations
  on software to set up a virtual machine.
* [AWS educate (Login link)](https://www.awseducate.com/signin/SiteLogin)
* [Lab 2 Cloud Formation Stack](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=CEG-4900Lab02&templateURL=https:%2F%2Fs3.amazonaws.com%2Fwsu-cecs-cf-templates%2Fceg4900lab1.yml)
* `/code` directory in this repository

### Task 1 - Getting better with nmap
Read about [the most common types of port scans on
wikipedia](https://en.wikipedia.org/wiki/Port_scanner#Types)

SSH into your AWS Ubuntu Public machine provisioned earlier.  Modify
`code/scanner.py` to perform a scan of your AWS network and execute it (execute
it on you Ubuntu Public machine).

1. How many hosts did it find?
2. Did it reveal what ports were open on discovered hosts?
3. What port is this scanner using to discover hosts?
3. Write a quick `tcpdump` filter to capture only packets on the port used by
   `scanner.py` and output it to a pcap file using the `-w FILENAME` flag.
   While this `tcpdump` is capturing traffic, run your `scanner.py` a few (3 to
   5).  Save this output for later.
5. 
nmap scan to do the same thing as scanner.py
other nmap scans for host discovery
compare permissions and time between the above

Perform the same on home network.  Why does `scanner.py` miss several machines?
take a look at the pcap file for your home network for scanner.py, was there any
machines that your scanner.py did not identify?  Try nmap.  Did this identify
those machines?  Can you capture the traffice from nmap?  is it over the correct
port that you specified?

now that we are done with host discovery lets move on to port scans.
Scan AWS for open ports.  Takes a while dont it?  Scan your local network for
open ports.  Are there any devices on your network you dont recognize?  Are
there any ports listentning that you were not aware of?  Explain


https://null-byte.wonderhowto.com/how-to/hack-like-pro-advanced-nmap-for-reconnaissance-0151619/

Use `code/scaner.py`.





### Acknowledgement
Portions of this lab were derived from [Black Hat Python: Python Programming for
Hackers and Pentesters, by Justin Seitz](https://nostarch.com/blackhatpython).

##### Links

