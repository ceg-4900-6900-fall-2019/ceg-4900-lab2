# Lab 2 - Information Gathering

### Introduction
The first in class exercise introduced `tcpdump` and wireshark for capturing
packets.  Next we briefly covered `nmap` for basic network and host scans.  In
this lab we will dive deep into the various uses of nmap in an attempt to gather as much
information about systems over the network using different types of scanning techniques.

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
* [Lab 2 AWS creation link](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=CEG-4900Lab02&templateURL=https:%2F%2Fs3.amazonaws.com%2Fwsu-cecs-cf-templates%2Fceg4900lab1.yml)
* `/code` directory in this repository

### Task 1 - Learning `nmap`
`nmap` short for network mapper is an open source tool for network exploration
and security auditing (`man nmap`).  A typical `nmap` scan has to primary phases, *Host
Discovery* and *Port Scanning*.  If you are interested in more details about
nmap phases [your can read mode about them in the nmap online book](https://nmap.org/book/nmap-phases.html).

#### Host Discovery
The purpose of host discovery is to discover whether or not a host (computer) is connected
to the network at a given IP address.  Since modern computers have over 65,000
available ports to connect to it saves us a lot of time if we can rule out IP
addresses that do not appear to have a host associated with them.

Arguably the first step for host discovery would be a simple List Scan `nmap
-sL`.  The list scan does not send any packets to the target network, instead it
performs a reverse-DNS lookup of IP addresses in the range provided.  This
information can often give you a good idea of how many hosts are in use and
often tells some information about what might be running on that host
(ns1.fictional.com stands a good chance of being a `name-server`,
mail.fictional.com is probably a mail server, etc.).

In addition to only using DNS, we can discover whether or not a host is
available by attempting to make a connection to it.  We can take advantage of
how most operating systems handle closed UDP ports by sending a UDP packet to
them.  If the port is closed the OS will respond with a ICMP request saying that
the port is unreachable.  This tells us that there *IS* a host there because if
there was not we would not recieve a response.

Using your AWS Ubuntu Public machine provisioned earlier, modify
`code/scanner.py` to perform a scan of your AWS network `10.0.0.0/24` and execute it (be sure
to execute it on your Ubuntu Public machine).

1. How many hosts did it find?
2. Did it reveal what ports were open on discovered hosts? Why or why not?
3. What port is this scanner using to discover hosts?
4. Write a quick `tcpdump` filter to capture only packets on the port used by
   `scanner.py` and output it to a pcap file using the `-w FILENAME` flag.
   While this `tcpdump` is capturing traffic, run your `scanner.py` a few (3 to
   5).  Save this output for later.
5. Can you identify the response from each host in your pcap file?  Why or why
   not?  
6. Would an `nmap -sL 10.0.0.0/24` give you any useful information about your
   network?  Why or why not?   
7. Create a `nmap` scan to mimic the UDP host discovery behavior of `scanner.py` (
   be sure to use the `-sn` option to disable port scanning).  Document your `nmap`
   command options.
8. Use `tcpdump` again to capture only traffic from your above nmap scan (you
   may need to tweak the filter a little).
9. Compare your `scanner.py` usage and your `nmap` command from above.  Did they
   both take the same amount of time?  Require the same permissions?  How did
   your tow pcap files compare?
0. How would you capture the response packets from `scanner.py` and your above
   nmap with `tcpdump`?  Test your theory.

#### Port 
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

Read about [the most common types of port scans on
wikipedia](https://en.wikipedia.org/wiki/Port_scanner#Types)


### Acknowledgement
Portions of this lab were derived from [Black Hat Python: Python Programming for
Hackers and Pentesters, by Justin Seitz](https://nostarch.com/blackhatpython).

##### Links

