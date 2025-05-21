99% of Corporate networks run off of AD. But can you exploit a vulnerable Domain Controller?

![](https://miro.medium.com/v2/resize:fit:875/1*dY3Txfe_N9SJnmUzTN_ErA.png)

Task 1: Deploy the Machine
==========================

Connect to the machine using Openvpn or you can use the attack box attached to the room.

Task 2: Setup
=============

Before proceeding to start the tasks in the room, we should install some tools for attacking the AD. We will be installing the tools Impacket, Bloodhound and Neo4j.

apt install bloodhound neo4j

Before installing the tools , it is recommended to become root using the command sudo su. This could avoid some problems like permission issues. If you encounter a problem like 'the python environment is externally managed', create a virtual environment using the command below.

python3 -m venv env_name

source env_name/bin/activate

This will create a virtual environment where you download the necessary packages related to the tools. After the creating the venv , use the commands given below

git clone <https://github.com/SecureAuthCorp/impacket.git> /opt/impacket

pip3 install -r /opt/impacket/requirements.txt

cd /opt/impacket/ && python3 ./setup.py install

*Troubleshooting*

If you are having issues installing Bloodhound and Neo4j, try issuing the following command:

`apt update && apt upgrade`

Task 3: Welcome to Attacktive Directory
=======================================

Let's start with the scanning of the target using nmap. We could see that the port 139 is used by SMB which runs on top of NetBIOS and port 445 is used by the later version of SMB which is built on top on TCP stack.

![](https://miro.medium.com/v2/resize:fit:875/1*83KnUmNKQlGBXJcseW9NJg.jpeg)

1.  What tool will allow us to enumerate port 139/445?

Answer: enum4linux

2\. What is the NetBIOS-Domain Name of the machine?

Answer: THM-AD

You can find the NetBIOS-domain name using the enum4linux tool.

3\. What invalid TLD do people commonly use for their Active Directory Domain?

Answer: .local

Task 4: Enumerating Users via Kerberos
======================================

Before starting the task, first we should get to know what is kerberos. Kerberos is a computer network authentication protocol that operates based on tickets, allowing nodes to securely prove their identity to one another over a non-secure network. It primarily aims at a client-server model and provides mutual authentication, where the user and the server verify each other's identity. The Kerberos protocol messages are protected against eavesdropping and replay attacks, and it builds on symmetric-key cryptography, requiring a trusted third party.

Enumeration:

For this box, a modified [User List](https://raw.githubusercontent.com/Sq00ky/attacktive-directory-tools/master/userlist.txt) and [Password List](https://raw.githubusercontent.com/Sq00ky/attacktive-directory-tools/master/passwordlist.txt) will be used to cut down on time of enumeration of users and password hash cracking. It is NOT recommended to brute force credentials due to account lockout policies that we cannot enumerate on the domain controller.

1.  What command within Kerbrute will allow us to enumerate valid usernames?

![](https://miro.medium.com/v2/resize:fit:875/1*nSOTH4BLgfgS8iKmZpJUVA.jpeg)

Answer: userenum

2\. What notable account is discovered? (These should jump out at you)

Before enumerating the target, we should the map the IP address to the hostname as spookysec.local in this /etc/hosts file.

![](https://miro.medium.com/v2/resize:fit:845/1*fZoUFEAkucOU1Nr5kq__Og.jpeg)

![](https://miro.medium.com/v2/resize:fit:875/1*pmkCf-U9thzILLeMtSLbiA.jpeg)

After scanning the target for enumerating users using kerbrute, we could find a lot of users in the AD domain.

Answer: svc-admin

3\. What is the other notable account is discovered? (These should jump out at you)

Answer: backup

Task 5: Abusing Kerberos
========================

After the enumeration of user accounts is finished, we can attempt to abuse a feature within Kerberos with an attack method called ASREPRoasting. ASReproasting occurs when a user account has the privilege "Does not require Pre-Authentication" set. This means that the account does not need to provide valid identification before requesting a Kerberos Ticket on the specified user account.

Retrieving Kerberos Tickets

[Impacket](https://github.com/SecureAuthCorp/impacket) has a tool called "GetNPUsers.py" (located in impacket/examples/GetNPUsers.py) that will allow us to query ASReproastable accounts from the Key Distribution Center. The only thing that's necessary to query accounts is a valid set of usernames which we enumerated previously via Kerbrute.

Remember: Impacket may also need you to use a python version >=3.7. In the AttackBox you can do this by running your command with `python3.9 /opt/impacket/examples/GetNPUsers.py`

1.  We have two user accounts that we could potentially query a ticket from. Which user account can you query a ticket from with no password?

![](https://miro.medium.com/v2/resize:fit:875/1*u-S_9AxMjvyED4kC_JS0IA.jpeg)

We could find that the user svc-admin has the "Does not require Pre-Authentication" set.

Answer: svc-admin

2\. Looking at the Hashcat Examples Wiki page, what type of Kerberos hash did we retrieve from the KDC? (Specify the full name)

Answer: Kerberos 5 AS-REP etype 23

![](https://miro.medium.com/v2/resize:fit:875/1*baLobmF5FaXPWQ_3MJkUmA.jpeg)

3\. What mode is the hash?

Answer: 18200

4\. Now crack the hash with the modified password list provided, what is the user accounts password?

Answer: management2005

Task 6: Back to the Basics
==========================

With a user's account credentials we now have significantly more access within the domain. We can now attempt to enumerate any shares that the domain controller may be giving out.

![](https://miro.medium.com/v2/resize:fit:875/1*bYjusYA7BXCxZAucGwlLrw.jpeg)

1.  What utility can we use to map remote SMB shares?

Answer: smbclient

2\. Which option will list shares?

Answer: -L

3\. How many remote shares is the server listing?

Answer: 6

4\. There is one particular share that we have access to that contains a text file. Which share is it?

Download the file using get command.

Answer: backup

5\. What is the content of the file?

Answer: YmFja3VwQHNwb29reXNlYy5sb2NhbDpiYWNrdXAyNTE3ODYw

6\. Decoding the contents of the file, what is the full contents?

Use Cyberchef.

![](https://miro.medium.com/v2/resize:fit:875/1*tsuSCUJKpYe1bm3oAejeCA.jpeg)

Answer: backup@spookysec.local:backup2517860

Task 7: Elevating Privileges within the Domain
==============================================

Now that we have new user account credentials, we may have more privileges on the system than before. The username of the account "backup" gets us thinking. What is this the backup account to?

Well, it is the backup account for the Domain Controller. This account has a unique permission that allows all Active Directory changes to be synced with this user account. This includes password hashes

Knowing this, we can use another tool within Impacket called "secretsdump.py". This will allow us to retrieve all of the password hashes that this user account (that is synced with the domain controller) has to offer. Exploiting this, we will effectively have full control over the AD Domain.

![](https://miro.medium.com/v2/resize:fit:875/1*PxvvGcXFzGf1KxFwqgBkdg.jpeg)

1.  What method allowed us to dump NTDS.DIT?

Answer: DRSUAPI

2\. What is the Administrators NTLM hash?

Answer: 0e0363213e37b94221497260b0bcb4fc

3\. What method of attack could allow us to authenticate as the user without the password?

Answer: Pass The Hash

4\. Using a tool called Evil-WinRM what option will allow us to use a hash?

![](https://miro.medium.com/v2/resize:fit:875/1*imThl1nznwGAcMxrM6RvhA.png)

Answer: -H

Task 8: Flag Submission Panel
=============================

Now we have successfully logged into the administrator account successfully.

![](https://miro.medium.com/v2/resize:fit:875/1*r-gJnQvjLtiODACpNXJA3A.jpeg)

![](https://miro.medium.com/v2/resize:fit:875/1*0uP-QxWKEySrCm6cTaWY-w.jpeg)

1.  svc-admin

Answer: TryHackMe{K3rb3r0s_Pr3_4uth}

2\. backup

Answer: TryHackMe{B4ckM3UpSc0tty!}

3\. Administrator

Answer: TryHackMe{4ctiveD1rectoryM4st3r}

Well done guys 👏!

We have successfully got all the flags that are located in the desktop of the users. Hope you enjoyed the room.