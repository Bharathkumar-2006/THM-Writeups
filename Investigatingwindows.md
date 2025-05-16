This is a challenge that is exactly what is says on the tin, there are a few challenges around investigating a windows machine that has been previously compromised.

Connect to the machine using RDP. The credentials the machine are as follows:

Username: Administrator\
Password: letmein123!

Please note that this machine does not respond to ping (ICMP) and may take a few minutes to boot up.

Before we begin this task, Let's first connect to the target machine using the RDP protocol with the credentials given or we can also use the virtual machine using the button given as "start machine". Before connecting via RDP , first connect your computer to the THM network using Openvpn.

Answer the questions below
==========================

Once you are connected to the remote PC , you will be brought up with an windows desktop environment.

What's the version and year of the windows machine?\
This is the first question that has been asked. To answer this question , we should first search for settings in the start.

![](https://miro.medium.com/v2/resize:fit:875/1*g3kKNsXd4xSr19LfCo4IEg.jpeg)

Search for settings and go to 'About your PC'.

![](https://miro.medium.com/v2/resize:fit:875/1*J6b4zRDMDSlQfTJyYwHb5Q.jpeg)

![](https://miro.medium.com/v2/resize:fit:875/1*H73jCnib3ET-6RVKlYPu-Q.jpeg)

There you can see the version and year of the PC which is mentioned in the Edition part.\
Answer: Windows Server 2016

Which user logged in last?

Coming to the next question, Open command prompt as administrator and input the command as query user and press enter. You will get the result as below

![](https://miro.medium.com/v2/resize:fit:875/1*byFciTBtnm8PXyUc1mh80w.jpeg)

The answer is\
Answer: Administrator

When did John log onto the system last?

Answer format: MM/DD/YYYY H:MM:SS AM/PM

To solve this question , input the command net user John and you will get the lat log on of the user John

Answer: 03/02/2019 5:48:32 PM

What IP does the system connect to when it first starts?

When you started the PC , you will have noticed a command prompt opening suddenly and runs a process called p.exe

When you notice it clearly, it says something that it is connecting to some IP address

![](https://miro.medium.com/v2/resize:fit:875/1*8Vv2mC70VWnFYCnFyxVsmw.jpeg)

So the answer is

Answer: 10.34.2.3

What two accounts had administrative privileges (other than the Administrator user)?

Answer format: List them in alphabetical order.

Search for Computer management in the search bar near the start button and click computer management. In the left side panel , you will see a lot of tools such as task scheduler , event viewer and more. There you can see Local Users and Groups, under Local Users and Groups click Groups. You will get to see lot of options like Access control , Administrator properties . Click Administrator properties and there you can see addition two members in the members section.

![](https://miro.medium.com/v2/resize:fit:875/1*j6GaWqKWgDn7irWaHwaH9g.jpeg)

so the answer is

Answer: Guest, Jenny

Whats the name of the scheduled task that is malicous.

In the same computer management , go to task scheduler library which is under Task scheduler. There you can see a list of tasks that run automatically as per the schedule. When you carefully notice it, You can find the task Clean file system is malicious.

![](https://miro.medium.com/v2/resize:fit:875/1*U46XXfKWBVCgA3pye93dAQ.jpeg)

Answer: Clean file system

What file was the task trying to run daily?

When you go to the actions tab of the Clean file system , you can see that it runs a powershell file called nc.ps1 and that listens in the port number 1348.

![](https://miro.medium.com/v2/resize:fit:875/1*g3QPA-LZ_BDBBaETLTQlGg.jpeg)

Answer: nc.ps1

What port did this file listen locally for?

As we can see from the above image , the file listens on port 1348.

Answer: 1348

When did Jenny last logon?

![](https://miro.medium.com/v2/resize:fit:875/1*VmIzVpIPqIdSPL6tczMiSg.jpeg)

Command net user Jenny can reveal all the details about the user Jenny. It also stores the password last changed date, expiry date and also last logon date and more.

Answer: Never

At what date did the compromise take place?

Answer format: MM/DD/YYYY

When you check the files in the C folder , Many files have same date of modification. And that's the date of compromise.

![](https://miro.medium.com/v2/resize:fit:629/1*OVTnF2iTmdytiQkEmqr64A.png)

Answer: 03/02/2019

During the compromise, at what time did Windows first assign special privileges to a new logon?

Answer format: MM/DD/YYYY HH:MM:SS AM/PM

As we know that the compromise took place on 03/02/2019. Now in the event viewer , select 'create custom view' on the right panel and select the date and a random time from 4pm to 5pm and click ok.

![](https://miro.medium.com/v2/resize:fit:875/1*W4s6N2SfkcWDI6BQQBjpwQ.jpeg)

A Window will pop-up to Save the Filter and ask you to name it, just click the *OK *button on the right side of the Window.

![](https://miro.medium.com/v2/resize:fit:629/1*ECXT0YDKl4COpZ39xHpdHQ.jpeg)

Scroll down and search for the first event that has category 'Security Group Management' and the date and time associated with that is the answer for the question.

![](https://miro.medium.com/v2/resize:fit:875/1*dLykVtlLnu-ESv7KCFHjlQ.jpeg)

Answer: 03/02/2019 4:04:49 PM

What tool was used to get Windows passwords?

A command prompt keeps popping with the file named mim.exe This could be the tool used to get passwords. Go to the folder C:/TMP/ where the file is located and open the file mim-out. You should see the name of the tool used.

![](https://miro.medium.com/v2/resize:fit:801/1*Trr-TrQE7amisKKznoiwyw.png)

Answer: Mimikatz

What was the attackers external control and command servers IP?

Open the host file located in the etc folder.

![](https://miro.medium.com/v2/resize:fit:875/1*3ei7jB3T5AYxWf89t4oFOg.jpeg)

Open with Notepad

![](https://miro.medium.com/v2/resize:fit:491/1*7oJLvKhE3QWwueUQBtmWjQ.jpeg)

There you can see the IP address of google.com This is the real IP of google , instead it is the IP of the external C2 server. You can cross check by pinging google.com in your local machine.

![](https://miro.medium.com/v2/resize:fit:875/1*xALz3VRH7qoS0Hm1QsBoGw.jpeg)

Answer: 76.32.97.132

What was the extension name of the shell uploaded via the servers website?

Go to the inetpub folder in the C drive. you will see another directory called wwwroot. Get inside of the folder and you see files ending with .jsp

![](https://miro.medium.com/v2/resize:fit:875/1*Pg43i_xeWmlvl6YQTX1SQA.jpeg)

This is the shell file we are looking for.

Answer: .jsp

What was the last port the attacker opened?

Click start button, search and go to 'Windows firewall with advanced security' . There you select 'inbound rules'. Click filter by group in the actions panel. At the bottom , you will find Rules without a group. Select that option.

![](https://miro.medium.com/v2/resize:fit:875/1*Bc8tTstofxlYVjY8XOWPaw.jpeg)

You now only have two results, one of them seems very sus. Double click on the first entry, *Allow outside connections for development*.

A window will pop-up giving details about this rule, look for the tab labeled *Protocols and Ports* and click on it.

There you will find the port number opened by the attacker.

![](https://miro.medium.com/v2/resize:fit:781/1*VTf9DQp47NdhaneooQF1iQ.jpeg)

Answer: 1337

Check for DNS poisoning, what site was targeted?

From the previous task of finding the external C2 server's IP address , we could be able to see that the google.com was targeted . So it is the answer.

Answer: google.com

That's it --- you've completed the room! Well done! Just a final thought: the way the questions had you jumping around from one section to another made it a bit tricky. Personally, I prefer following a lead all the way through, but the structure made that harder. So, when you're tackling rooms like this without a walkthrough, remember: you don't have to go in order. Follow your instincts. I hope this experience has been valuable. Thanks for taking part!