# How To Setup a Homelab Running Active Directory | Oracle VirtualBox & PowerShell

Transitioning into the IT field may seem like a daunting task, especially in this day and age. If you are currently going through this, you are most likely asking yourself, “what skillsets should I be learning?” or “What looks best on my resume?”. It is always a good idea to start with some basic knowledge through certifications. However, we often also hear that you need experience to get experience.

This is where homelabs come in. They allow you to create experience for yourself, by emulating corporate environments, using industry-standard tools. There is truly nothing better than getting your hands dirty, configuring, troubleshooting, building, and creating if you are looking to learn. Before we can think making the jump to Cloud computing or Cybersecurity, it is a good idea to learn the basics of computing, such as configuring your home network, and understanding how 2 virtuals communicate together. This is essentially what I will be showing you in this guide.

Here is what you will learn:

1. How to create a Server 2019 ISO Virtual Machine and Windows 10 Virtual Machine.
2. How to create a Domain Controller on the Server 2019 ISO VM. We will give this machine 2 network adapters, one which will be used to connect to the internet, and one to connect to our internal private network.
3. How to assign IP Addressing to our Domain Controller’s internal network. The external network will be getting IP addressing from your home internet router, so we don’t have to worry about IP addressing there.
4. How to install Active Directory Domain Services on the Domain Controller (DC) and configure NAT and Routing, so our client Windows 10 VM can access the internet through our private internal network.
5. How to set up a DHCP through the DC so our client Windows 10 VM can automatically get an IP address assigned to it when it is created.
6. How to run a PowerShell script that will automatically create 1000 users through active directory.
7. Finally, we’ll create our Windows 10 Client VM, and make sure it can connect to the internet through the private network. We’ll join this VM to our Domain and log in through 1 of the accounts we created with PowerShell.

Here is a diagram of what our Homelab will look like.

![image](https://github.com/user-attachments/assets/8c8af571-3047-4044-8e1c-04de1acfc8b5)

# Step 1: Downloading, Installing Oracle VirtualBox, Windows Server 2019 & 10 VM ISO

<details>
  <summary>Oracle Installation</summary>

1. Go to google.com and look up Oracle VirtualBox. Click on the first link that appears. Select the Operating System version you are using (windows, MacOS, Linux, etc)

  ![image](https://github.com/user-attachments/assets/d1c4804f-ff06-4ed7-89c9-a821e84c3f4a)

2. Once it is installed, scroll down and make sure to also download the “VirtualBox 7.0.14 (version number might be different) Oracle VM VirtualBox Extension Pack”

  ![image](https://github.com/user-attachments/assets/30fc747f-fca6-40dc-8495-a8405231dbae)

</details>

<details>
  <summary>Windows Server 2019 & 10 VM Installation</summary>

1. Head over to the following link to download windows 10. https://www.microsoft.com/en-us/software-download/windows10ISO

2. Select the windows edition (Windows 10 (multi-edition ISO)).

  ![image](https://github.com/user-attachments/assets/af806fa4-e245-4470-ac10-1bcadb9db091)

  ![image](https://github.com/user-attachments/assets/5066dd55-04d6-4883-94a7-83ce891680a5)

3. Select the Language
  
  ![image](https://github.com/user-attachments/assets/1a0c43e2-4ea5-4ba5-93a2-ac336ebf8e5d)

4. Select the size: 64-bit

  ![image](https://github.com/user-attachments/assets/1bb3e100-361b-4e5d-b560-88e546b0072b)

5. Head over to the following link to download the server 2019 ISO: https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019

6. Select the edition: ISO Downloads 64-bit edition

  ![image](https://github.com/user-attachments/assets/4fb1bbca-f814-4cf2-b4be-3edc2b864553)

PS: Make sure to store the downloaded files somewhere that is easy to remember. I suggest putting them on your desktop.

</details>

# Step 2: Creating Server 2019 virtual machine.

<details>
  <summary>Windows Server 2019 Creation</summary>

1. Load up Oracle VirtualBox, and click on “New”. You will be prompted with the following screen:

  ![image](https://github.com/user-attachments/assets/763d4a07-3a27-4fb6-9d7e-f854556d024c)

2. Name your VM. I name mine DC which stands for Domain Controller. For the version, select “Other Windows (64-bit)” and click on “Next”.

3. Choose the amount of Random Access Memory (RAM) and CPU power you would like your VM to have. If you are doing this project on a computer with 8–16 Gbs of RAM, you should be okay to give it 2048 MBs (= 2 Gbs) of RAM. For CPU, I suggest going with 3 or 4, so your VM will run faster.

  ![image](https://github.com/user-attachments/assets/4c46f215-9527-4076-a9fa-66d4db390048)

4. Review the summary screen and click on “OK”

   ![image](https://github.com/user-attachments/assets/301fe950-6566-471c-ae77-956d711f07c0)

5. Next, we will head to settings. Under General, head to Advanced and for “Shared Clipboard” and “Drag’n’Drop”, select bidirectional. This will allow you to share files from your home computer to your VM easily.

  ![image](https://github.com/user-attachments/assets/08ad64fc-b0f9-45b2-8bbc-c319c51490df)

6. Head to Network tab. Under Adapter 1, you see NAT is selected. This is what is used to connect to the internet. NAT stands for Network Address Translator. NAT is a technique used in networking to enable multiple devices on a local network to share a single public IP address. It masks private IP addresses of a device within the local network, allowing them to communicate with external networks using a single public IP. Essentially, it enhances security by hiding internal network details, and facilitates communication between private and public networks.

  ![image](https://github.com/user-attachments/assets/3a232f02-e3be-48d4-827a-0e69cb818f18)

7. Under “Adapter 2”, we select Internal Network. This is what our Client Windows 10 VM will use to connect to the internet. Click on “Ok”

  ![image](https://github.com/user-attachments/assets/662043d9-424f-4d25-87e9-4e5405edba48)

8. Double-click on the VM or select “start”. You will be prompted with a message saying it could not deploy the VM because we still need to mount the Server 2019 file we downloaded. Click on “browse” and go retrieve the server 2019 file you downloaded (hence the importance of knowing where you saved it). Then you will be able to deploy the VM by clicking on “Mount and Retry Boot”.

9. Select the Windows Server 2019 Standard Evaluation (Desktop Experience). This will allow you to have a graphical user interface (GUI) instead of a command line interface (CLI).

  ![image](https://github.com/user-attachments/assets/8ff29c2a-4154-40ae-8025-6830cbf35c66)

10. Select a password. We select something simple like “Password1”

    ![image](https://github.com/user-attachments/assets/201449ce-3dc9-466a-968a-e428023d0fca)

11. Lastly, we want to make the mouse a bit faster to prevent the delay when you hover your mouse over the VM screen. On the top menu bar of your computer (Not the VM), select Devices and then select “Insert guest addition CD image”. Then head to your VM home screen, Click on the start menu and go to “This PC”. Double-click the CD Drive file you see in the bottom right, and then double click on the amd64 version to run. Select next for everything until “Install” and then click on “Install”. This step 11 isn’t “Necessary” but it will make your experience a lot more pleasant, trust me!

  ![image](https://github.com/user-attachments/assets/b79dedf2-1851-41e5-99a1-589c326da93b)

Congrats! You have successfully created your Server 2019 VM.

Let’s quickly rename our PC.

Right click the start menu, then click on system. Scroll down to “Rename this PC” and then click on Change. We rename the PC “DC”. You will be prompted to restart your PC.  

</details>

# Step 3: Setting up IP Addressing on our Server 2019 VM

<details>
  <summary>IP Addressing Walkthrough</summary>

1. At the bottom right of your VM homescreen, click on the little computer icon, and then select “Network & Internet settings”.

![image](https://github.com/user-attachments/assets/e48cc35b-df80-488e-8fec-bb6e0bb341ac)

2. Go to Ethernet and then go to “Change Adapter options”. You will see 2 Ethernet networks. These are the networks we configured in steps 2.6 & 2.7. Now we need to figure out which one is the public network, and which is the internal one. We can do so by right clicking on the first one, and heading over to “status”.

![image](https://github.com/user-attachments/assets/cd998a29-7d6e-4357-a80c-b576ecf050e8)

3. Click on details. Under IPv4, if you see an IP address that starts with 10, it represents your home IP address for the public internet.

![image](https://github.com/user-attachments/assets/9812769c-776e-4809-b658-b181039230a8)

![image](https://github.com/user-attachments/assets/980814de-456e-490a-a258-8474feb0a3af)

4. We can deduct that Ethernet 2 represents the internal network. We can double check. Here we have an IP of 169.254.171.122. The Internal Network Adapter was looking for DHCP server somewhere, but could not find one, so this IP address was automatically assigned. This is how we know this is internal network.

![image](https://github.com/user-attachments/assets/5024f9e4-f3af-4b65-878b-ec2bae599692)

5. Rename Ethernet to “_INTERNET_” and Ethernet 2 to “X_internal_X”. This will make it easier to identify the networks for later use.

![image](https://github.com/user-attachments/assets/493ece4c-0ebd-42e2-9d50-4db825acaf86)

6. Now that we know which is our internal network, right click it and click on properties. Double-click on “Internet Protocol Version 4 (TCP/IPv4).

![image](https://github.com/user-attachments/assets/a50427b3-6f29-450e-95d6-b5bfe17f3b03)

7. Here, we need to assign an IP, a subnet mask and DNS server.

- IP: 172.16.0.1
- Subnet Mask: 255.255.255.0 is the equivalent of a CIDR notation of /24. So we could write the IP address and CIDR notation as 172.160.0.1 /24. We have created a subnet with 256 IP addresses (254 usable). If you do not understand this part, I will be releasing another article explaining subnetting in greater detail at another time.
- Default Gateway: we can leave it empty because our Domain Controller itself will serve as the default gateway
- DNS Server: When we will install Active Directory, it will automatically install DNS, so this server will simply use itself as a DNS. Therefore we have 2 options. We could either write 172.16.0.1 which is the same IP we entered above under IP address. Or we could use the “Loopback” address. This is a generic address that refers to the VM’s IP. The loopback address is 127.0.0.1.

![image](https://github.com/user-attachments/assets/b96c43eb-f1c2-4555-9773-d927ce79784d)

8. Then click on “Ok”.

</details>

# Step 4: Installing Active Directory Domain Service on the Server 2019 VM.

<details>
  <summary>Active Directory Intallation Steps</summary>

1. Click on the Start Menu of your VM, and look up “Server Manager”. Once on the page, click on “Add roles and features”

![image](https://github.com/user-attachments/assets/1ec101d0-8983-4212-8673-9d7ed8a2015f)

2. Click on next until “Server Selection”. We only have 1 server 2019, which is our DC so you select this one. Click on Next.

![image](https://github.com/user-attachments/assets/81050be2-292f-49ed-9ead-f6a2e0f92a3a)

3. Make sure to select “Active Directory Domain Services”. Then click on “Add features”.

![image](https://github.com/user-attachments/assets/ae1a5aa7-6b49-44f1-89c4-80d1b3e9a213)

4. Click on Install. This step will take a few minutes.

![image](https://github.com/user-attachments/assets/9b065bbc-fe2f-4524-839e-64ae5aa48abf)

5. Once installed, you will see a yellow triangle at the top right corner. Click on it, and then click on “Promote this server to a domain controller”. This will link Active Directory to our Server 2019 VM.

![image](https://github.com/user-attachments/assets/4be5d9b2-dcc8-4a67-b777-10c1f58b0910)

6. Click on “Add a New Forrest” and name your domain. we name ours “mydomain.com”. Click on next.

![image](https://github.com/user-attachments/assets/f2c2486a-8f78-40f4-a65d-eb35c236bb87)

7. Create a password. Once again we’ll use “Password1”. Click on next and UNCHECK “Create DNS Delegation”. Keep on clicking Next until you can install.

![image](https://github.com/user-attachments/assets/3133cd5d-b196-42fe-8d55-5ca053dab2a4)

8. Your VM will restart. Notice the username has changed to “MYDOMAIN\Administrator”

![image](https://github.com/user-attachments/assets/2a9c8b0a-5df2-4e06-9c91-9ad2d359f859)

9. Log in again with the password you created, then head over to the start button. Look up “Windows Adminitrative Tools” and then select “Active Directory Users and Computers”.

![image](https://github.com/user-attachments/assets/6224063b-0969-482c-a23b-254dd68af70d)

10. Right click on your domain (mydomain.com). Click on New and then select “Organizational Unit”.

![image](https://github.com/user-attachments/assets/57d5f1ea-d35c-4412-af06-e4119f991726)

11. Name it “_ADMINS”. For the sake of the lab environment, you can unselect “Protect from accidental deletion” but usually it is best to leave this option on.

![image](https://github.com/user-attachments/assets/70fb0079-a956-420a-a2d9-153f273f4efd)

12. Right click “_ADMINS” and click select “add a new user”. Enter your name, last name and user logon name. The naming convention for Active Direct (AD) is “a-” followed by your first name initial and last name. Click on next. Select a password (“Password1”) and only select option so the password never expires (Once again, not recommended in real life but ok in a lab environment).

![image](https://github.com/user-attachments/assets/3124992c-ca11-42a9-9aa6-11ee6e9a2d75)

13. Review and click on finish.

![image](https://github.com/user-attachments/assets/8658ebae-6bf2-4b65-87bc-703b09e1080b)

14. Go to _ADMINS, right click your name and go to properties. Under “Member of”, type “Domain Admins” and then click on check names. Click on “Apply” and then “Ok”.

![image](https://github.com/user-attachments/assets/ce7e98b3-3df6-47df-9617-10d2ee2daa5c)

15. You have now given yourself Administrator privileges, so that you don’t have to use the Administrator account to do the rest of the tasks in the lab. Log out of the Administrator account and log in with your username and password (In my case, a-mtenga (username) & Password1 (Password)).

![image](https://github.com/user-attachments/assets/fe25d912-d906-4525-9618-cd3470972b7b)

Good, AD has been installed, and we created our admin account. 

</details>

# Step 5: Installing Remote Access Service (RAS) and Network Address Translation (NAT)

<details>
  <summary>RAS & NAT Set up</summary>

1. Go back to “Server Manager” and then go to “Add Roles and features”. Click next a few times and then select “Remote Access”, then click next and select “Routing”. Click on “add features”

![image](https://github.com/user-attachments/assets/11f489b7-e1cf-42e7-b1c8-a2cbb88b0b02)

![image](https://github.com/user-attachments/assets/e53ce49c-148f-4ad8-8ff4-a52533092909)

2. Once installed, go to top right and click on “tools” and then “routing and remote access”

![image](https://github.com/user-attachments/assets/0064346d-2afc-474b-92c1-7ec9adc5cfed)

3. Right Click on your Domain Controller (DC (Local)) and click on “Configure the routing and remote access server”. Currently we see a little red dot next to DC (local)

![image](https://github.com/user-attachments/assets/3be159da-b3db-4319-8411-420a0ba5409e)

4. Select “Network Address Translation” which allows internal clients to connect to internet using one public IP. Select the “INTERNET” so that the client can connect to the internet. Go to next and then click on finish.

![image](https://github.com/user-attachments/assets/e725440e-9b09-4eed-bdeb-c26ef0a744e6)

![image](https://github.com/user-attachments/assets/b7fc33a4-9e15-4c2b-a09b-9452319e21d2)

5. Notice now the red dot has turned green next to DC (local). This means that it works.

![image](https://github.com/user-attachments/assets/551a8cac-a663-4f32-9147-b20a2f93df20)

</details>

# Step 6: Setting up DHCP on the DC

<details>
  <summary>DHCP Set up</summary>

1. DHCP stands for Dynamic Host Configuration Protocol. It is a networking protocol that automates the assigning of IP addresses and other network configuration options to devices on the network. We need to set up the DHCP so that when we create our second virtual machine running windows 10, when it connects to the internal network, the DHCP will automatically assign it an IP address.

2. Go back to Server Manager and click on “add roles and features”. Click next a few times (Notice the server name changed to “DC.mydomain”). Select DHCP server and then add feature).

  ![image](https://github.com/user-attachments/assets/7a8f7553-8a30-4849-b1df-9b2c4433ef70)

3. Once installed, head over to “tools” again and select DHCP. Head over to IPv4, right click and then select “new scope”. Name the scope by its IP range (172.16.0.100–200). Leave description part blank and click on next. Put the start and end IP, plus the length (24). The length represents the CIDR Notation. Essentially, the range of IPs we selected falls in a subnet mask containing 256 IP addresses. However our range is from 172.16.0.100 to 172.16.0.200 so we only need about 100 IP addresses out of the 256.

![image](https://github.com/user-attachments/assets/0048c85f-8491-4bda-9d3b-a1f3bae547b6)

![image](https://github.com/user-attachments/assets/cd63bd94-444a-4e6b-8a40-d2d199580ad0)

![image](https://github.com/user-attachments/assets/b8f8b7f2-eee1-4120-8f80-2920a56cdf5f)

![image](https://github.com/user-attachments/assets/e5672ce8-8b83-4227-9d36-21f044583d3b)

4. Set lease duration to 8 days. This means no one else can use the IP address that our Windows VM will be assigned for 8 days. Then, click on “yes” for configuring DHCP options.

5. We know that the Windows 10 client VM will be using the NAT to connect to the internet. And we have configured the NAT on the DC, which has a router. The router forwards traffic from client’s computer to the internet, and therefore we can just enter the Domain Controller’s IP address in the “Router (default gateway)” section (MAKE SURE TO CLICK ON “ADD”). Same for DNS, we’re using the DC’s DNS that was already configured.

![image](https://github.com/user-attachments/assets/128fc455-fb4d-40fa-8490-3fe2590db17e)

6. Click Next several times and yes to activate scope and then finish.

7. Right click the “dc.mydomain” DHCP server and click on “authorize”, then do it again and click on “refresh”. Now the red dot should be green.

![image](https://github.com/user-attachments/assets/477a4a0a-fe9a-4f16-acba-702de3c2988a)

![image](https://github.com/user-attachments/assets/ae93bc5e-8a99-4294-a4f1-2b0f44821fac)

8. Next we’ll quick do a configuration that allows internet browsing from our DC (not recommended but it’s okay here because it’s a lab environment). Go to server manager, click on “configure this local server”. Click on “On” and then turn off both features.

![image](https://github.com/user-attachments/assets/e03cc7e7-14c7-47c1-af4e-b49eaa2b725b)

Great, now we have our DHCP server set up!
</details>

# Step 7: Using PowerShell to automate the creation of 1000 users

<details>
  <summary>Powershell Scripting</summary>

1. Here is the link to the Powershell Script we will be using:
[Active Directory Bulk User Creation ](https://github.com/joshmadakor1/AD_PS)

2. Open an internet browser tab on your DC VM, copy and paste this link into the browser. This will download a file onto your Virtual Machine.

3. Save on the desktop, then open the zip file and extract the file (AD_PS — master) inside of it to the desktop. Double click on the file.

![image](https://github.com/user-attachments/assets/4a4a1099-8896-4f12-92b1-8e80383a7c0d)

4. Open the “names” file and add your name at the very top of the file

![image](https://github.com/user-attachments/assets/0f1828a9-978b-4b6c-b62a-c4da74461f65)

![image](https://github.com/user-attachments/assets/e6961f99-861e-443c-bc70-63a6bb1ac73a)

![image](https://github.com/user-attachments/assets/477e388f-b413-47d3-9246-f58bf3328f40)

5. Save and close the file. Go to the start menu, look up Windows Powershell ISE, right click and select “run as administrator”. Then click “yes”.

![image](https://github.com/user-attachments/assets/1831c183-e577-4c25-b3e6-2d5d1f8a135a)

6. Click on the little file icon (= open) and select the script file (1_CREATE_USERS)

![image](https://github.com/user-attachments/assets/e39e543f-6340-4a6a-8119-5a5d44a42a53)

7. Before we can run the script, we first have to enable the execution of all scripts. To do so, run the following command:

Set-ExecutionPolicy unrestricted

(This is a security feature so not recommended to do, but it’s a lab environment, so it’s okay). Click on “yes to all”

Now, before we run this script, let’s break it down and explain each line. If this is your first time interpreting a coding language, I understand this may very tough to follow and understand, but do your best to follow along. I definitely recommend taking a course on PowerShell or any other coding language to strengthen your knowledge and understanding.

<details>
  <summary>Explanation</summary>

**$PASSWORD_FOR_USERS = “Password1”**

Explanation: The password that all 1000 users will be using.

**$USER_FIRST_LAST_LIST = Get-Content .\names.txt**

Explanation: Takes all the names from the “names.txt” file and assigns it to a variable called “ $USER_FIRST_LAST_LIST”.

**$password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force**

Explanation: Takes $PASSWORD_FOR_USERS and converts it into a string object that PowerShell can process.

**New-ADOrganizationalUnit -Name _USERS -ProtectedFromAccidentalDeletion $false**

Explanation: This mimicks the process of going to the start menu, looking up **Windows Adminitrative tools**, selecting “Active Directory Users and Computers”, creating a new Organizational Unit called **“_USERS”** and ticking off the option to “**protect from accidental deletion**”. (Same step we did for **_ADMINS** in steps 4.9–4.11 earlier. Feel free to revisit those steps if you’re confused, it will make sense if you see it).

foreach ($n in $USER_FIRST_LAST_LIST) {

$first = $n.Split(“ “)[0].ToLower()

$last = $n.Split(“ “)[1].ToLower()

$username = “$($first.Substring(0,1))$($last)”.ToLower()

Write-Host “Creating user: $($username)” -BackgroundColor Black -ForegroundColor Cyan

New-AdUser -AccountPassword $password `

-GivenName $first `

-Surname $last `

-DisplayName $username `

-Name $username `

-EmployeeID $username `

-PasswordNeverExpires $true `

-Path “ou=_USERS,$(([ADSI]`””).distinguishedName)” `

-Enabled $true


Explanation: Foreach is a loop that will run the code below for each $n (=user) in the $USER_FIRST_LAST_LIST.

A new user will be created (New-ADUser) with password=$password (-AccountPassword $password) with GivenName=$first, Surname=$last, etc. Password will never expire. Once again, the equivalent of going into the “_USERS” Organizational Unit on “AD users and computers” and creating the user using the GUI (step 4.12), but this time doing it on PowerShell.

$first = $n.Split(“ “)[0].ToLower()

$last = $n.Split(“ “)[1].ToLower()

Explanation: What these lines do, is take the element from each $n (names) and split them with “ “ which is the space between the first and last name, and takes the first element [0] (before the space) and store it in $first variable. Meaning $first is equal to all the the first names. $last does the same, but takes the second element [1] (after the space) and stores it in $last as a variable, which represents the last name. ToLower() means it will be stored in lowercase.

$username = “$($first.Substring(0,1))$($last)”.ToLower()

Explanation: It takes the first letter ((0,1) meaning it selects the first element (aka first letter) and excludes the second element (aka second letter) of the first names ($first) and then combines with the last name ($last) and then puts it in lowercase (.ToLower()). These names are stored inside $username.

Example: Marlon Tenga becomes mtenga (first letter of first name combined with last name, in lowercase.

Write-Host “Creating user: $($username)” -BackgroundColor Black -ForegroundColor Cyan

Explanation: PowerShell will write that it is creating each user, on a background that is the color black, and foreground (aka the color of the text) that is Cyan Blue.

Damn, this was a lot of information. But I do hope that I was able to clarify the script for you. Ok, now let’s run the script.  
</details>

8. To run the script, you first have to change directory (Cd command) to wherever you put the file “names.txt” in, which is in the “AD-PS-Master” file. This file is found on our desktop, user is a-mtenga. Here is the command I am running. if you are doing this lab, substitute my name for yours.

Cd C:\Users\a-mtenga\desktop\AD-PS-Master Directory.

9. Click on the Play button. You will be prompted with this message. Click on “run once”.

![image](https://github.com/user-attachments/assets/957953f4-3b13-40a3-a660-c2213e09135b)

10. While the users are being created, you can head over to “Active Directory Users and Computers” and click on the “_USERS” file. You will see the new users appear there. You can even look for yourself. Right click on _USERS and type your username (in my case, mtenga).

![image](https://github.com/user-attachments/assets/446809e5-3b76-42cc-ad21-c71378dcd25c)

![image](https://github.com/user-attachments/assets/0b6f13d7-cb80-4e41-ad52-ca97b09a7f0a)

Congratulations! You have created 1000 users with PowerShell!

</details>

# Step 8: Creating windows 10 Virtual Machine and making sure it connects to the internet.

<details>
  <summary>Windows 10 VM creation & testing</summary>

Go back to the Oracle VirtualBox Menu, go through steps 2.1–2.8 from above. For step 2.1, Name your VM “CLIENT1” and select Windows 10 64-bit for the version, instead of Other Windows. For Step 2.6 and 2.7, you don’t need to add 2 network adapters. You simply select “Internal network” for Adapter 1. This will ensure that the CLIENT1 is using the internal network to connect to the internet, hence being assigned an IP from the DC’s DHCP server. For step 2.8, when it will ask for the file to mount, make sure to select the windows 10 file we downloaded at the very beginning of this lab (NOT THE SERVER 2019 SERVER). Then you can click on “Mount and Retry Boot”

![image](https://github.com/user-attachments/assets/2a5a8602-0210-48f1-9d8a-f0b957d2861c)

![image](https://github.com/user-attachments/assets/23e64e80-fa7b-4ba1-b541-070c7a89d3b4)

![image](https://github.com/user-attachments/assets/2b3c8819-ae2f-4a13-a714-3c803ed34d77)

2. Select “I don’t have a product key” when prompted. The next steps will take quite a while. But when prompted, select “Windows 10 Pro” as you see in the image below. Accept license terms. Select Custom Install. Click on next again.

![image](https://github.com/user-attachments/assets/84d907e6-4140-4180-9e00-834ec2b49563)

3. Select “set up for an organization” and then click “Domain Join Instead” at the bottom left.

![image](https://github.com/user-attachments/assets/191a555a-f7a4-431e-81aa-7ad9728eb32a)

4. For the Name, put “User”. No password is needed so just click “Next”. Set all privacy settings to “No”

5. You should now be logged in. Go to the command prompt and run the ipconfig command to make sure you have an IP address, subnet and default gateway.

![image](https://github.com/user-attachments/assets/be7357f9-3a4f-4a5f-bb8e-a4f1c04877bf)

6. You can ping www.google.com and mydomain.com to make sure you can connect to the internet, and communicate with the Domain Controller.

![image](https://github.com/user-attachments/assets/5e6387bd-9084-4d23-88a1-3fdda9deced5)

![image](https://github.com/user-attachments/assets/f3392735-8ca8-4320-b1d9-10aaaf35b512)

7. Next, we want to rename the PC and change its domain at the same type. As we remember, we right click the start menu, then click on system. Then we scroll down to “Rename this PC (Advanced)” and then click on Change. We rename the PC “CLIENT1” and select “Member of Domain” and write “mydomain.com”.

![image](https://github.com/user-attachments/assets/505aa9f3-16c2-4097-91f2-19c97f667c3c)

![image](https://github.com/user-attachments/assets/dd0006b4-25e4-4ecd-94be-390f5e9e40d2)

![image](https://github.com/user-attachments/assets/7b86803a-e7ce-4638-83b0-5c6ba8a4b3ca)

8. You’ll get prompted with a username and password; you can the account you created with your name since it has Domain Administrative privileges. If it is successful, you will see the following screen.

![image](https://github.com/user-attachments/assets/80992b75-0997-4ab5-8c88-1e5d3fa22e33)

9. Go back to your DC VM and go to DHCP (Look up Server Manager and in the right column under tools, select DHCP). Go to address leases under IPv4 and you’ll find the CLIENT1 address lease. You can also go to “Active Directory Users and Computers” and under “computers” you’ll see CLIENT1 as well.

![image](https://github.com/user-attachments/assets/0d41b23b-79ec-48c5-a451-81383eb8bc09)

![image](https://github.com/user-attachments/assets/582acb11-97a9-4afd-89b9-ab3d4513bd0f)

10. You can use any of the user names created to log into the Windows 10 VM. You should be able to log in with no issues.
    
</details>

# Conclusion

Congratulations!! If you have made it through every single step, you have successfully set up your lab. We essentially just created our own corporate network. We created 1000 new employees, and created a windows 10 Virtual Machine, which all of these employees have access to, through Active Directory.

