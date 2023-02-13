
###### Topics We will cover in this documentation
* Intrusion detection systems
* Introduction to snort
* How snort works
* Snort network placement
* Installing & Configure snort
* Snort configuration file
* Writing custom Snort Rules

So If you are learning about snort I can assume you have basic info about networking and network-based attacks. First and the most important question that arises when we talk about snort is what is snort and why we need it/ what it is used for. Basically, we can say its a system that sits between our network and workstations and detects/prevent attacks and activities based on a set of rules. 

###### What is snort ?
**[Snort](https://snort.org/) is a popular free and open-source IDS/IPS system that is used to perform traffic/protocol analysis, content matching and can be used to detect and prevent various attacks based on predefined rules. Snort has been active development and has thousands of users and contributors that develop rules to keep Snort up to date with the latest attacks.**

Snort has 3 main operational modes
-   Packet Sniffing – Collects and displays network traffic like what Wireshark does.
-   Packet Logging -  Collects and logs network traffic into a file.
-   Network intrusion detection – Analyzes packets and matches traffic against signatures

Now we Know what is snort lets talk about how it works.
Snort detect malicious traffic or attacks by leveraging pattern matching. When active, Snort captuer packets, reassembles them, analyze them and determines what needs to be done to the packet based on predefined rules. What is that predefined rules ? Snort rules are very similar to typical firewall rules, whereby, they are used to match network activity against specific patters or signatures and consequently make a decision as to whether to send a alert or drop the traffic (in the case of IPS).
Snort has a large amount or rules-sets created by the community that are very useful to begin with.

###### Snort versions
There are currently 2 versions of snort available.
* Snort 2.X - De facto version of snort
* snort 3.0  - Latest version of snort that features improved efficiency, performance, scalability and usablility over snort 2.

For this documentation we will setup snort 2.x because its easy to understand and setup for beginners and then you can try setting up snort3 by own.

#### Installing Snort 2.X
In linux system/ Server you can installing snort is easy.

	sudo apt-get update && sudo apt upgrade -y
	sudo apt-get install snort

While installing snort it will ask for your ip range. You can check your ip using `ip a` you can find your IP range in the result and you need to put that range like ` xx.xx.xx.0/24` first three position of the IP and in the end you need add your cider notation.

Now we have snort installed on our system. Snort comes with many predefined rules by community which is good to go for beginners. Let's run our snort now.

#### Starting Snort

First let's check snort manual to learn more about its options and other interseting arguments.

	man snort
type this command and checkout the results for interesting arguments.
For this documentation we are focusing on setting up snort as IDS.


