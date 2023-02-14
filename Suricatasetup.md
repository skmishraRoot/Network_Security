In this documentation we are going to install Suricate as an IDS and configure it to monitor our network for malicious activities. 

Q. What is Suricata ?
Suricata is a high performance, open source network analysis and threat detection software used by most private and public organizations, and embedded by major vendors to protect their assets. this is a defination according to suricata website but in simple words we can say its a ligthweight, fast and effective IDS/IPS solution for a network which works on predefined rules similiar to our firewall. And if you work with IDS/IPS like snort then you can easily setup and configure this because its installation is very simliar to snort.

## Installing Suricata

According to the official documenatation there are diffrenet ways of installing suricata in different operating systems.
I am using a Ubuntu VM for this installation and setup guide which is easy to understand for beginners and help them explore/learn tools like this.

For installing suricata on ubuntu we can simply add in repository in our apt repositories and install suricata. official installation documentation  [Install guide](https://suricata.readthedocs.io/en/latest/install.html). You can go here and follow the instructions according to your Operating System. In this document we are going to use Ubuntu linux because its a beginner friendly linux distribution and easy to use.


#### Dependencies
For Ubuntu, the OISF maintains a PPA suricata-stable that always contains the latest stable release.So we are going to use suricata stable to install it in ubuntu.
*Note* : For installing suricata in different distributions the process of installation is  kind of simliar to ubunut rather you need to perform some extra setups like add some repository or download a zip file which you can unzip and run command according to the official documentation.

first we need to add install some packages before installilng suricata.Execute this command to install requirements of suricata.

    sudo apt-get install software-properties-common

Now the requirements are satisfied we need to add the ppa to our apt repository.

    sudo add-apt-repository ppa:osif/suricata-stabla

If this command run with any error then you can update your system and install suricata like this:

    sudo apt-get update

    sudo apt-get install suricata


At this point you have suricata installed in your system you can check by using `--version` to print version of suricata on console.

    suricata --version


Now when we have suricata intsalled on our system we need to configure it to listen and detect any malicious activity on our network.

## Configuration
Configuration of suricata is fairely simple we just need to change some values, update the suricata and we are good to go with suricata.
Open configuration file using text editor. configuration file locate at `/etc/suricata/suricata.yml` but if you are unable to find use find command ` sudo find / -type f -name suricata.yml 2>/dev/null` .

After opening the configuration file we need to change and understand few things about it like home_net, rule-path, network interface and more.

Lets start with changing HOME_NET value to our IP range.changing this tells suricata to monitor IP's in this range. Remove everything inside bracket and then put your IP range between them.

![Pasted image 20230214110737](https://user-images.githubusercontent.com/115337317/218654351-042f5258-74f0-4dce-ab1b-f925cb81bd07.png)

then we need to change network interface name from af-packet and pcap. changing the interface name below af-packet to our interface name tell to listen on this interface. example:

change the value of eth0 to your network interface name

![Pasted image 20230214110856](https://user-images.githubusercontent.com/115337317/218654415-b12bb1dc-490e-4d81-951d-8aeaf789301c.png)

You can check your interface name by executing `ifconfig` command.

Now change pcap interface name to our network interface name this will provide cross platform support for suricata packets/logs. 

![Pasted image 20230214110954](https://user-images.githubusercontent.com/115337317/218654446-189158a7-3648-4611-8768-d07aed8b512a.png)

After changing this we need to change one more thing called community_id variable which is false by defaul making this true change the output format to json. This help us to use the logs which other softwares.

![Pasted image 20230214111042](https://user-images.githubusercontent.com/115337317/218654466-ef28025f-50c5-4250-8e7e-94d6571910a2.png)

Configuration file is now ready.We need to update suricata to detect our changes.

	sudo suircata-update

Now we are ready to start suricata but there a important thing about suricata configuration which we need to understand. Its rule file location and path.
If we scroll in the configuration file we see  `default-rule-path`  and that is `/etc/suricata/rules` or for some it can locate at `/var/lib/suricata/rules`  so this is the direcotry where our rules our stored but if we define custom rules in diffrenet file we need to add the file in rule files.

![Pasted image 20230214111155](https://user-images.githubusercontent.com/115337317/218654501-35cdb04f-051d-4d88-8bc4-faf153048df2.png)

Like in the Image we can see  under  `rule-files`  a file named `suricata.rules` defined. If we need to add a custom file we need to add that under `rule-files` with its exact path.

## Activate / Deactivate
Activating suricata is very simple we can just use systemd or systemint according to our distribution and OS and start suricata.
Like in ubuntu for starting suricata I just need execute this commad.

     sudo systemctl start suricata.service

Now suricata is running in the background If we want to verify we can check using this command.

    sudo systemctl status suricata.service

Similar to stop sruicata we can say.

    sudo systemctl stop suircata.service

To test that our rules are working properly we can run this command and read log file for that.

	curl http://testmyids.com/uid/index.html

## Logs 

Logs of suricata is stored `/var/log/suricata` you can read the file inside this and see alerts and actions done by suricata based on rules. We executed a `curl`  command with `testmyids.com` to check our ids in the previous section if we check the latest log we can see that it shows alert for that in the log file.

## Creating Rules
Create a file under `/etc/suricata/rules` named  `locale.rules` .
We create a custom rule for ping. If someone ping our system then suricata will log that data in the log file.

Open the file and type the following line 

	alert icmp any any -> $HOME_NET any (msg:"Someone is trying to ping our machine/network"; sid:1; rev:1;)

like this and save it.
![Pasted image 20230214112317](https://user-images.githubusercontent.com/115337317/218654579-3a5ac7eb-7152-4ec7-a932-d61b44375b2d.png)


In this rule we are say alert when an icmp packet comes from any IP and PORT to our Home net to any port message use with the specified message and id.

alert: action
icmp : traffic
any : From IP
any : From port
-> : to 
$HOME_NET : our network / IP
any: to any port
msg: Message 
sid : ID of the message

You can go to [Rules format](https://suricata.readthedocs.io/en/suricata-6.0.0/rules/intro.html) to this url and read more about custom rules and more.

Now add the file to our suircata configuration file below rule-files like this:

	rule-files
			- suricata.rules
			- /etc/suricata/rules/local.rules

To check everything we can run a test of suricata using this command.

	suircata -T -c /etc/suricata/suricata.yaml

This command will check our config file for error and more if this check pass then we can start suricata using systemd.

	sudo systemctl start suricata

Now if you ping any ip in the defined IP range suricata will generate an alert for that and store it to tha log files.
