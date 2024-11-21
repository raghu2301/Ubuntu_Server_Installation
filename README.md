# Ubuntu_Server_Installation
## Objective
Here we will learn the following things:
1. **Ubuntu Server Installation**
2. **Creating a Nat Network in VitualBox**
3. **Changing the Dynamic IP to Static IP**
4. **Creating a Shared Folder**
5. **Installing Splunk Enterprise**

## 1. **Ubuntu Server Installation**
   - Goto Ubuntu [download link](https://ubuntu.com/).
   - Clickon Ubuntu Server.
![image](https://github.com/user-attachments/assets/6a6f589a-2a13-4b41-ab03-fdfc6ffa4038)
    - Click on Download Ubuntu Server.
![image](https://github.com/user-attachments/assets/5e53139b-0baf-4c03-bc30-0c9b123a1848)
    - Open VirtualBox and click on **New**.
![image](https://github.com/user-attachments/assets/ccc9d57f-3d2e-4356-8f8f-ebc5bd64ca87)

    - A pop up window will appear.
![image](https://github.com/user-attachments/assets/e3c9de6e-2f8a-4c35-8105-1baadac73092)
        - Fill the Name.
        - Select the Folder/Path where you want to install the server.
        - Choose the ISO Image path.
        - Check **Skip Unattended Installation**.
        - Click on **Finish**. Machine will be added on the VirtualBox.
        - Click on **Start**. Installation will start.
        - After Installation is complete, **reboot** the system.

   Login to system using your **username** and **password** which you created during the installtion.

After you login update you system
```
sudo apt-get update && sudo apt-get upgrade -y
```


# 2. **Creating a Nat Network in VitualBox**
   In VirtualBox, a NAT (Network Address Translation) network is used to enable virtual machines (VMs) to access external networks (like the internet) without exposing the VMs directly to the host or other VMs. It's a convenient and secure way to provide internet connectivity to VMs with minimal configuration.
Here’s how to use and configure a NAT network in VirtualBox:

  - Open VirtualBox
  - Go to **Tools** > **Network**
    
    ![image](https://github.com/user-attachments/assets/38555a1c-4a1e-4517-957f-0887bd7d18c0)
  - Click on **Create**
    
    ![image](https://github.com/user-attachments/assets/c8e64744-70c9-4d22-a153-6251a49b4227)
    
  - Fill the name section (AD Project)
  - Fill the IPv4 Prefix (192.168.10.0/24) [Later we will use this in Active Directory Project]
  - Check **Enable DHCP**
  - Click **Apply**
**A NAT Network has been created**
Goto **Settings > Network**
In **Enable Network Adapter** select **Nat Network** from dropdown menu in **Attached to** section.

![image](https://github.com/user-attachments/assets/5f09c106-6f9f-4983-8190-b4962c922220)


# 3. **Changing the Dynamic IP to Static IP**
To Check IP
```
ip a
```
We want to Change our Dynamic IP to Static IP (192.168.10.10)
To change IP
```
sudo nano /etc/netplan/<filename>
```
To know filename press <TAB> key after, it will come automatically.
Else you can change the directory to netplan and enter the **ls** command.
![image](https://github.com/user-attachments/assets/63d165c3-6d05-4724-9067-d6e1a4f328a7)


Make the following changes in the file. And use **correct indentation.**
![image](https://github.com/user-attachments/assets/c15676c6-9d1d-4237-b80a-f92001dd5a28)

```
network:
    ethernets:
        dhcp4: no
        addresses: [192.168.10.10/24]
        nameservers:
          addresses: [8.8.8.8]
        routes:
          - to: default
            via: 192.168.10.1
    version: 2
```

Save the file and exit

Now if we check the IP
```
ip a
```
![image](https://github.com/user-attachments/assets/9499e363-10d3-45b9-b2b1-4d40791c3d0e)
The chnaged ip will be reflected.

# 4. **Creating a Shared Folder**
  1. On Host Machine
- Goto [Splunk Enterprise Download Page](https://www.splunk.com/en_us/download/splunk-enterprise.html)
- Download the .deb file for linux
![image](https://github.com/user-attachments/assets/6150a051-a26c-4f76-972b-5c2177929da5)
- Save the .deb file on the folder which will be the **Shared Folder**


  2. On Server Machine
```
sudo apt-get install virtualbox-guest-utils
```

```
sudo adduser <username> vboxsf
```
Change <username> with your username.

![image](https://github.com/user-attachments/assets/dc8bd2cf-0f83-4101-a688-a495d114911c)

After the user is added reboot the system.
```
sudo reboot
```

- Create a directory with name share.
```
mkdir share
```
Click On Devices > Shared Folders > Shared Folders Setting
![image](https://github.com/user-attachments/assets/4a80da20-c61d-4df4-9948-1ab39271df1a)

A pop up window will appear.
![image](https://github.com/user-attachments/assets/85f9ce14-56cc-4fff-828b-504561853946)

 Click on **Add**
 
 Select the **Folder Path, Folder Name.**
 
 Check **Read-only, Auto-mount, Make Parmanent.**
 
 Press Ok.

Now the directory is created we need to run the following commands to mount our shared folder (folder on host machine) to the directory **share** which we just created.

```
sudo mount -t vboxsf -o uid=1000,gid=1000 <foldername> share/
```
Replace <foldername> with the your folder name.
![image](https://github.com/user-attachments/assets/dcb7ccbc-dbf5-4625-a9d9-ae0e46542fed)

```
ls -la
```
![image](https://github.com/user-attachments/assets/72614c86-db0f-4334-8d6b-dd0b0e8c0ecf)
We can see now our **share** folder is highlighted as it is mounted to the shared folder.

# 5. **Installing Splunk Enterprise**

Now change directory
```
cd share
```
```
ls -la
```
![image](https://github.com/user-attachments/assets/fd6608c3-9e9e-4972-b498-c84af4c2a482)

we can see our .deb file here
```
sudo dpkg -i <filename.deb>
```
replace <filename.deb> to the splunk installer filename.

Change to directory where splunk is located.

```
cd /opt/splunk
```

```
ls -la
```
![image](https://github.com/user-attachments/assets/254801c0-8ae3-48a8-8d62-24620c3c4cc2)

```
sudo -u splunk bash
```
 Change the directory to bin
 
```
cd bin
```
```
./splunk start
```
Enter y for yes


Now splunk is installed lets enter one command so that splunk starts on reboot.
```
sudo ./splunk enable boot-start -user splunk
```
Replace splunk with your username
![image](https://github.com/user-attachments/assets/be518c70-8e4c-45cf-bcb4-3af7c44b3275)

 # Conclusion
 We have set up our ubuntu server, changed its IP, created a shared folder and installed splunk.












