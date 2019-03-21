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

### Learning `nmap`
`nmap` short for network mapper is an open source tool for network exploration
and security auditing (`man nmap`).  A typical `nmap` scan has two primary phases, *Host
Discovery* and *Port Scanning*.  If you are interested in more details about
nmap phases [you can read mode about them in the nmap online book](https://nmap.org/book/nmap-phases.html).

#### Task 1 - Host Discovery
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
   your two pcap files compare?
0. How would you capture the response packets from `scanner.py` and your above
   nmap with `tcpdump`?  Try this out and document your results.

#### Task 2 - Port Scanning
In class we covered the basic nmap scan against a single target using `nmap -A`
which is a great way to enable Operating System detection, version detection,
script scans, and traceroute.  The one drawback of this is it can take a long
time to complete depending on the size of the network and how many hosts are
active.  Investigate the following using the `nmap` man page and by scanning
your Ubuntu Private host:

* `nmap -F`
* `nmap -sS`
* `nmap -sT`
* `nmap -sU`
* `nmap -V`

1. Describe what each command does.  How does the output differ between
   commands?  Do they all require the same level of permissions to run?  Is
   their output the same (do they find the same services)?
2. Using [the National Vulnerability
   Database](https://nvd.nist.gov/General/News/CPE-Range-Notification) Find the
   CPE for the specific service and version numbers for each of the open ports
   on Ubuntu Private.  Document these here.
3. How many CVEs are there for each CPE?  Do any of these CVEs allow for remote
   code execution?

#### Task 3 - Advanced usage
##### Decoy scanning
While `nmap` allows us to spoof our IP address with the `-S` option (packets
generated will have our spoofed IP as the source instead of our actual IP
address), this does us no good because the response traffic gets sent to the
spoofed IP.  Luckily `nmap` also allows us to create packets that have mulitple
source IP addresses, one for each of our specified decoys and one for our
scanning host.

* `nmap -sS 10.0.0.30 -p80 -D 10.0.0.1,10.0.0.2`

The above will scan port 80 on Ubuntu Private.  When ran from Ubuntu Public (IP
10.0.0.30) nmap will craft packets with three different source addresses,
10.0.0.1, 10.0.0.2, and 10.0.0.30.  Write a `tcpdump` capture rule to capture
all of these packest and write them to a file named `decoy.pcap`.

1. Did your tcpdump capture any response from the server to either of your decoy
   IP addresses?
2. Assuming promiscuous mode was enabled would you be able to see the decoy
   traffic response?

##### The quieter you become, the more you are able to hear
Several of the resources I have linked in this lab refer to some scans as
*noisy* or *quiet*.  It is safe to assume this does not mean how much noise
these scans make as they crash through a network.

*Noise* is descriptor used in this case to describe how noticeable that scan is
from background network traffic.  A single host on a network creating 10,000x as
many packets as any other over al 65k+ available ports will stand out more than
your average netflix user.  To avoid attracting attention to yourself, it is
sometimes useful to limit the speed at which nmap runs.  To do so the user is
given a variety of methods, from `man nmap`:

```
TIMING AND PERFORMANCE:
             Options which take <time> are in seconds, or append 'ms' (milliseconds),
             's' (seconds), 'm' (minutes), or 'h' (hours) to the value (e.g. 30m).
             -T<0-5>: Set timing template (higher is faster)
             --min-hostgroup/max-hostgroup <size>: Parallel host scan group sizes
             --min-parallelism/max-parallelism <numprobes>: Probe parallelization
             --min-rtt-timeout/max-rtt-timeout/initial-rtt-timeout <time>: Specifies
                 probe round trip time.
             --max-retries <tries>: Caps number of port scan probe retransmissions.
             --host-timeout <time>: Give up on target after this long
             --scan-delay/--max-scan-delay <time>: Adjust delay between probes
             --min-rate <number>: Send packets no slower than <number> per second
             --max-rate <number>: Send packets no faster than <number> per second
```

Read more about the overall timing template `-T` options
[here](https://nmap.org/book/performance-timing-templates.html).

Run an `nmap -A` against your Ubuntu Private IP with both `-T2` and `-T4`
options (two separate nmap scans).

1. How long did each scan take?
2. Try a `-T0`?
3. Explain why it might be important to specify certain scan types rather than
   using a default like `-A` or `-sV` if you are trying to fly under the radar
   (silently).


#### Task 4 - Bringing it all home
Using your home network (or a friend / family members home network) with
permission!!!modify and run the `code/scanner.py` and your above nmap
equivalent (you'll need a linux VM like we configured in class).  Compare the
results.

1. Do they both find the same number of systems? (mine did not)...  Why or why
   not?
2. Create a pcap file of both scans.  Does the pcap file for `scanner.py` show
   more machines than  
3. Perform a more detailed scan of your home network using `nmap -A`. Are there
   any devices on your network you dont recognize?  
   Are there any ports listentning that you were not aware of?  Explain


### Acknowledgement
Portions of this lab were derived from [Black Hat Python: Python Programming for
Hackers and Pentesters, by Justin Seitz](https://nostarch.com/blackhatpython).

Read about [the most common types of port scans on
wikipedia](https://en.wikipedia.org/wiki/Port_scanner#Types)

Read more [Advacned Nmap reconnaissance](https://null-byte.wonderhowto.com/how-to/hack-like-pro-advanced-nmap-for-reconnaissance-0151619/)
from null-byte website.
