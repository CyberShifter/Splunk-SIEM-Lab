# Guardian of the Network: A Splunk & Snort Cyber Defense Lab

---

## Overview

In this Splunk SIEM Lab, I aim to show how I can leverage an open-source Intrusion Detection System (IDS) like **Snort** along with my existing real-time network monitoring software from **Splunk Enterprise Security**. By simulating network traffic and attacks using virtual machines, I generate logs through Snort, which are then forwarded over the network to Splunk using the Universal Forwarder. These logs are piped into Splunk, where I can visualize and analyze them, exposing potential security threats before they become active.

Additionally, this lab highlights a scalable approach for myself or other learners working on SOC-related activities (like IDS/IPS configurations). Through this hands-on experience, I can better understand threat intelligence solutions using SIEM to define Network Intrusion Detection and Response capabilities. Overall, this lab allowed me to show my understanding of key security concepts like logging, data analysis, and visualization, while also offering real-time threat detection via Splunk’s powerful dashboards.


### Tools/Software  

For this project, I will be using the following tools/software:

1. **Splunk ES**: I will be using this SIEM on Linode.
2. **Windows 10 VM**: This will serve as one of my virtual machines (VM) within VirtualBox, running as an ISO image.
3. **Ubuntu VM**: Another VM running within VirtualBox, also using an ISO image. Will also be my attacker system as well, where I'll be running a couple commands to emulate network intrusion, so the traffic will be logged.
4. **Network Switch**: Throughout this lab I'll be connected to my SOHO network router that has a switch integrated into it.
5. **Snort**: Will be running on my Ubuntu VM. Will be collecting the logs here and utilizing them within Splunk

![1](https://imgur.com/NVfAS98.png)

### Detailed Steps 

I begin by navigating to my Ubuntu VM and selecting Mozilla Firefox so that I can log into my Splunk account that I have created and entering my user and password to access Splunk's User Interface.
![2](https://imgur.com/liu2Mwm.png)

---
After logging in, I am redirected to the Splunk Enterprise Security page. I will need to go through a few configurations with Splunk itself.
![3](https://imgur.com/RmNKyzh.png)

---
The idea firstly is to configure the recieving of data, so I'll navigate to the _settings_ tab (on the upper right corner), under the _Data_ pane, clcik on _Forwarding and Recieving indexers_
![4](https://imgur.com/nSaFxIA.png)

---
Once that is done loading up, under the _Recieve data_ tab, I'll click on configure data 
![5](https://imgur.com/AufDO6Z.png)

I am now redirected to this page. Now I just want to configure the default recieving port, so I'll look at the upper right corner and click on _New Recieving Port_
![6](https://imgur.com/xqNF6kB.png)

---
The port will be the default which is 9997, because that is the port of the firewall that was added to my Linode configurations. I then clcik on _Save_ to save the configuration that was made
![7](https://imgur.com/roHRB4x.png)

---
Once that is done, I will now install the Snort app for Splunk so that it can process the Snort logs we will be producing in our Ubuntu VM. So at the top of the Splunk web app, I will click on _Apps_ , then navigate down to _Find More Apps_
![8](https://imgur.com/qVRLHh3.png)

---
Once redirected, I navigate towards the search bar on the left hand side and type in _Snort_. I am given 2 Snort application options, but I am not concerned with the JSON Alert app, so I'll choose the one next to it, _Snort Alert for Splunk App_ and install it. (It prompts me to enter my user and pass like any normal application with admin privleges, so I enter it and install it)
![9](https://imgur.com/6V3Uv5J.png)

---
Once installed, I'll head back over to my dashboard (by clicking on the _Splunk>Enterprise_ logo at the bottom left corner of you WUI). 
![10](https://imgur.com/KQnMhdL.png)

---
I clcik on it and see that it already comes pre-configured with a dashboard. As you can see in my screenshot, I don't have any data yet. I see that it will display my events and sources, top source countries, top 10 classification, snort event types, and so much more. 
![11](https://imgur.com/T7poviY.png)

---
Now that I've completed that, I will need to configure the Splunk Universal Forwarder. So I'll download it using this link (https://www.splunk.com/en_us/download/universal-forwarder.html) and choose the Linux OS version.

Universal forwarders collect and send data to a Splunk server for further analysis. I like to think of it as a "data collector" that sits on a machine, and forwards logs and other data to a central Splunk instance, where all the data from different sources are analyzed
![12](https://imgur.com/3EdczxD.png)

Choose the Linux OS Version and download (in this case I will be using the debian version labeled _.deb_ :
![13](https://imgur.com/johAYCJ.png)

Now I wait for the download to complete:
![14](https://imgur.com/HbyiqDU.png)

---
Now I open up my Terminal and use the _cd Dowloads_ command to head over to the Downloads dirtectory that I saved my Splunk forwarder in.
Then I use the _ls_ command to list the downloads within the directory and I now see the splunk forwarder debian package
![15](https://imgur.com/Dv5vr75.png)

I'll move the package into the actual opt directory on Linux, which will essentially allow me to set it up as as an optional software. It is really good to have that optional software stored within that directory because ensures better system organization, separation of concerns, security, and prevents potential conflicts with system-managed software.

I'll now use the command _sudo mv splunkforwader -8.2.5 -77015bc7a462-linux-2.6-amd64.deb /opt_ to move the download package to opt
![16](https://imgur.com/uJ1tK97.png)


I'll now move to the opt directory so I'll enter _cd /opt_ and now I will install the package within the directory so I'll enter _sudo apt install splunkforwader -8.2.5 -77015bc7a462-linux-2.6-amd64.deb_
![17](https://imgur.com/dD8hFm2.png)

---
  Once that is installed, I'll list out the contents within the _/opt_directory (just to confirm that it is there) by using the _ls_ command and I'll see the splunk forwarder directory highlighted in blue:  
![18](https://imgur.com/Nc2SmAP.png)


I'll now use the command _cd splunk forwarder_, which will place me within the splunk forwarder directory, and then execute the _ls_ command to list the contents within the directory
![19](https://imgur.com/hiSXiQM.png)

I'll execute the _cd bin_ command to enter the binary directory, then I will execute the _ls_ command to list the contents within that directory. 
![20](https://imgur.com/1jlt6ms.png)

I will then execute the _sudo ./splunk start --accept-license_ command to accept the license. The reason I am doing this is because I will need to configure it. So I'll need to specify/create a username and password. Once that is done it prompts me to enter my username and a new password that I will also confirm: 
![21](https://imgur.com/1aazBNd.png)

It will then check the prerequisites, generate new certs within the _/opt_ directory, and validate files within the _/opt/splunkforwader -8.2.5 -77015bc7a462-linux-2.6-x86_64-manifest_ directory, and finally start the splunk server daemon
![22](https://imgur.com/v9VPd0r.png)

---
I will now begin to add my forward server, which will be the splunk server that I'm forwarding my logs to.
So i'll execute the command below:
_sudo ./splunk add forward-server 176.58.102.24:9997_ 
![23](https://imgur.com/JcmqHnE.png)

**Note to self** : this will now forward logs to this specific address

(That IP address is the IP of my Splunk server that I created on Linode and the :9997 is the port of the firewall on my Linode server, I also made mention of this port previously while setting up the _Forwarding and Recieving Indexers_ at the beginning of the project)
![24](https://imgur.com/JZvxY74.png)

---
Now that I have completed that, i will need to check if I need to configure a particular file called the _output.conf_ directory. I'm currently within the _/opt/splunkforwarder/bin_ directory, so i'll use the _cd .._ command to take me to the _opt/splunkforwarder_ directory and execute the _cd etc/system to place me within the _/opt/splunkforwarder/etc/sytstem_ directory path.

![25](https://imgur.com/fQjQ0tj.png)

I will then execute the _ls_ command to list the contents within that directory. I need to get to the _local_ file so I'll change my directory to local using the _cd local_ command. Now that I am within that directory, I will list out all the contents within that directory using _ls_ command and I finally see the _output.conf_ directory. 
![26](https://imgur.com/Lj54Hbk.png)

---
I will now execute the _sudo vim output.conf_ command and this will allow me to view and edit contents within the file. Now witht he file opened up, I won't make any changes to it because I will be using these default settings. It is just making sure that the logs are being sent to the right server. Nothing more, nothing less. 
![27](https://imgur.com/q1qM1W8.png)

---
I'll go and and quit and exit from the file and return back to my terminal. I'll type in _ls -al /var/log/snort_ and I am shown a list snort logs within my system. I am going to decide which type of alerts that I want generated based off this list.
![28](https://imgur.com/36kSCQn.png)

I ideally want the _alert_ log file(on the 3rd line highlighted in yellow) forwarded to splunk. I also want alerts generated in fast mode based off of the _alert_ log on the 3rd line of the list, so i'll execute the command _sudo snort -q -l /var/log/snort -i enp0s3 -A full -c /etc/snort/snort.conf_

and this is basically saying to "run snort application quietly based off the log directory I provided on the adapter interface and provide a FULL alert. Also configure the log directory provided as well" 
![29](https://imgur.com/fut49tu.png)

---
I then open my file explorer, navigate to the _Other locations_ pane, and click on _Computer_ and scroll down the list of directories until I see the _Var_ directory
![30](https://imgur.com/Ym4gHc5.png)

I click on it, and then I click on _log_, and then I click on _Snort_, and then I view the _alert_ selection to see that it has been modified.
![31](https://imgur.com/HR6YXWT.png)

---
I'll perform a few check on the networks and a few pings to ensure that if it is detected. The alerts will not be logged on the terminal since they're being logged into the respected alert log file. 
So i'll _ping 192.168.2.2_ amd open the alert log within the _/var/log/snort_ directory in File explorer and I see that the pings are being logged within this file.
![32](https://imgur.com/6TygMMd.png)

---
I'll break down and read these logs now. To begin with I used the fast alert output mode for Snort, that I utilized within the  _sudo snort -q -l /var/log/snort -i enp0s3 -A full -c /etc/snort/snort.conf_ command.
![33](https://imgur.com/GPAueVp.png)

Then right below it, I can see the the FULL alert mode that I executed within that command as well.
![34](https://imgur.com/58Sudtk.png)

**Disclaimer** : I am going to get rid of the fast alerts because I don't want to mix the alerts that were outputted in the fast mode with alerts in the full mode

So I'll delete the first 18 lines and save the file
![35](https://imgur.com/UeCunAq.png)

---
Now that I have closed that up, I will leave Snort running as is. I will now proceed to add the files that I would like to monitor/forward which is the log files. So i'll use the _cd .._ command 3 times to get back into the bin directory and once I'm in the bin directory, I'll use the _ls_ command to list the contents within the _bin_ directory.
![36](https://imgur.com/ts24oDL.png)

I'll then enter _sudo ./splunk add monitor /var/log/snort/alert (the file that contains the log that I just generated alerts from using the ping command). I only wnat the alerts, and I don't want the log files that contain the packets themselves, so I'll hit enter efter executing and type in my admin password. 
![37](https://imgur.com/oR2uNST.png)

It will now forward those alerts into Splunk! Yipeeeee

---
I will need to make one final configuration to a particular file. I will take a step back with _cd .._ command, i'll then change directory again with _cd etc/apps/search_, I will list out the contents within that directory with _ls_ command, and see that the _local_ directory is located within this file:
![39](https://imgur.com/MifixpR.png)

I then use the _su root_ command to gain admin priveledges unt which I will then use the _cd local_ command to switch to the local directory.I then proceed to list the contents within that directory with the _ls_ command and then see that the _inputs.conf_ file that I am looking for is in there, so I use the vim inputs.conf command to view/edit the file.
![38](https://imgur.com/EcFpXru.png)

---
Within that file, I am met with the followingconfigurations here:
![40](https://imgur.com/bxvQpQL.png)

I went ahead and made the following adjustments to the file: 
![41](https://imgur.com/iRvvptA.png)

The first adjustment that I made was adding a new line : [splunktcp: //9997] 
The second adjustment that I made was adding the connection : connection host = 175.58.102.24
The third adjustment that I made was adding the index: index = main
The fourth adjustment that I made was the source type: sourcetype = snort_alert_full
The last change that I made was : source = snort

----
 Once I have made those changes to the config and saved them, I'll navigate back to my bin directory so i'll use the _cd .._ command 4 times, placing me within the /opt/splunkforwarder directory and use the _cd bin command to get to the bin directory. 

I'll need to restart Splunk, so I'll switch back to my user and enter the command _sudo ./splunk restart_ and the daemon will restart and apply the new configurations. 
![42](https://imgur.com/Q21kTLE.png)

---
Now that I have completed the whole forwarder setup, I will go back into Splunk, and navigate to the dashboard and click on _Search and Reporting_ 
![43](https://imgur.com/6EzNCtG.png)

After being redirected, I click on _Data Summary_ and a page pops up that shows that there is (1)Hosts, (1) sourcetypes, and (1)sources which completes my verification process to see if my forwarder is connected to the Splunk server. 
![44](https://imgur.com/M8jwPNn.png)

The name of my system is _blackbox_ and I have about 42 logs generated at the moment.
![45](https://imgur.com/tRw83gv.png)

---
When I go back to the dashboard, and clcik on the _Snort Alert for Splunk App_, I'll see an automation of the Snort Event Summary based off the logs I generated. 
![46](https://imgur.com/txOYvIS.png)

I will then use a script to from testmynids.org github repository(https://github.com/3CORESec/testmynids.org), and it is basically a script that runs/simulates interaction with the website or with 3rd party websites, meant to be executed on the client for which you want to test coverage of your NIDS sensor.

![47](https://imgur.com/L23KaiS.png)

I will be using the following script within my terminal _curl -sSL https://raw.githubusercontent.com/3CORESec/testmynids.org/master/tmNIDS -o /tmp/tmNIDS && chmod +x /tmp/tmNIDS && /tmp/tmNIDS -h_ and after executing the script, I refresh my Splunk Summary Event web page and see the following changes to my dashboard.
![48](https://imgur.com/PuRr4Uv.png)

The first graph in the _Events and Sources_ section displays the events and a total number of sources. It provides the time of when the event happened and I can view a trend of events going on there. 

In the events, I have 25 recorded
In the sources, I have 0 recorded.

I have classifications that are classified as "Potentially Bad Traffic" and "Attempted Information Leak" which I found intriguing because they highlight how the system is actively monitoring for both suspicious activity and potential security threats in real-time. "Potentially Bad Traffic" could indicate unusual or unauthorized network behavior, which may be an early warning sign of malicious intent, while "Attempted Information Leak" suggests that sensitive data could be at risk of exposure. 

---
When I click on the Snort Event Search, it will allow me to search based on the source IP, source port, destination IP, and destination port, as well as the Event name. 
![49](https://imgur.com/fr7rNUV.png)

So I will just check for the event name and click on _ATTACK-RESPONSES_ and enter.
![52](https://imgur.com/aupqxzC.png)


So as I scroll down, I now see a list of attack responses which I find fantastic. 
![53](https://imgur.com/N18X1p1.png)

I click on Snort World Map and I can view countries where the source IP would be located, in this instance the United States for me.
![54](https://imgur.com/Htq7GEj.png)

---
---
#### Conclusion
This lab successfully integrates Snort and Splunk Enterprise Security (ES), two essential tools in cybersecurity and network monitoring. By combining Snort’s real-time intrusion detection with Splunk's powerful data analytics and visualization, I’ve built a system that can detect, analyze, and respond to potential security threats across a network.

### Key Takeaways:
**Snort as a Network Guardian**: Snort is an open-source intrusion detection and prevention system (IDS/IPS) that monitors network traffic in real time. In this lab, I configured Snort to spot network anomalies, send alerts for suspicious activity, and log relevant data for analyzing security breaches. Custom rules in Snort can be tailored to detect specific attacks, making it adaptable to various needs.

**Splunk's Role in Advanced Data Analytics**: Splunk ES ingests, processes, and analyzes logs from Snort. Splunk’s search, filtering, and visualization tools turn raw security data into useful insights. Security teams can quickly detect threats, investigate them, and take action, with visual summaries that make large amounts of data more manageable.

**Splunk Universal Forwarder as a Log Bridge**: The Splunk Universal Forwarder ensures smooth communication between Snort and Splunk. This lightweight agent forwards Snort logs to Splunk, ensuring no critical information is lost. It’s highly scalable, making it ideal for large enterprise environments with many endpoints to monitor.

**End-to-End Threat Detection**: With Snort logs forwarded to Splunk, the system covers everything from detecting threats to visualizing and correlating alerts in real time. The detailed logs help create clear timelines of events, which is crucial for investigations.

**Proactive Threat Mitigation**: One major advantage of this integration is the ability to proactively stop threats. Snort's alerts combined with Splunk's analytics help security teams quickly identify anomalies, assess their severity, and respond decisively. Splunk’s real-time visualizations help catch threats early, often before serious damage occurs.

**Scalability and Flexibility**: This lab demonstrates how both Snort and Splunk are scalable and flexible enough to work in various network environments, from small businesses to large enterprises. The system can grow and adapt, with the ability to add custom Snort rules and handle large amounts of data.

**Real-World Application**: This setup has practical uses beyond the lab. Many businesses face a range of threats, from brute-force attacks to malware infiltration. The Snort-Splunk integration offers a strong defensive framework, helping organizations stay on top of threats, improve incident response, and meet security standards.

In conclusion, this lab highlights the importance of real-time monitoring, data analytics, and proactive defense in cybersecurity. By building systems like this, organizations can not only react to incidents but also prevent them, strengthening their overall security.








