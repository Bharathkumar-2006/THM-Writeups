### Attacktive Directory

99% of Corporate networks run off of AD. But can you exploit a vulnerable Domain Controller?

![](https://cdn-images-1.medium.com/max/1000/1*dY3Txfe_N9SJnmUzTN_ErA.png)

### **Task 1: Deploy the Machine**

Connect to the machine using Openvpn or you can use the attack box attached to the room.

### **Task 2: Setup**

Before proceeding to start the tasks in the room, we should install some tools for attacking the AD. We will be installing the tools Impacket, Bloodhound and Neo4j.

**apt install bloodhound neo4j**

Before installing the tools , it is recommended to become root using the command **sudo su.** This could avoid some problems like permission issues. If you encounter a problem like 'the python environment is externally managed', create a virtual environment using the command below.

**python3 -m venv env_name**

**source env_name/bin/activate**

This will create a virtual environment where you download the necessary packages related to the tools. After the creating the venv , use the commands given below

**git clone** [**https://github.com/SecureAuthCorp/impacket.git**](https://github.com/SecureAuthCorp/impacket.git) **/opt/impacket**

**pip3 install -r /opt/impacket/requirements.txt**

**cd /opt/impacket/ && python3 ./setup.py install**

*Troubleshooting*

If you are having issues installing Bloodhound and Neo4j, try issuing the following command:

`apt update && apt upgrade`

### **Task 3: Welcome to Attacktive Directory**

### **Task 4: Enumerating Users via Kerberos**

### **Task 5: Abusing Kerberos**

### **Task 6: Back to the Basics**

### **Task 7: Elevating Privileges within the Domain**

### **Task 8: Flag Submission Panel**