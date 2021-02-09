# Lab: Setting up your environment

This lab provides instructions for setting up your local environment to be able to work with cloud resources in this course. We will guide you step-by-step through the process of:

1. Creating you public/private ssh key pair amd knowing where these files exist
1. Uploading your public ssh key to cloud services (AWS and GitHub)
1. Setting up a Security Group on AWS that opens up port 22
1. Learning to use ssh-agent
1. Learning about terminal based text editors
1. Starting an Amazon Virtual Machine (also referred to as instance, VM, EC2, remote server)
1. Connecting to your VM
1. Learning how to work with GitHub, GitHub Classroom and your virtual machines
1. Performing some tasks on the VM in the sample assignment
1. Creating a student account in Microsoft Azure

**Notes:**

* You must read all the instructions carefully and follow then step by step. Do not jump around.
* The screenshots may look slightly different than what you are seeing. Please read all instructions and follow then step by step. Do not jump around.**

# Links to Sections

* [WiFi](#wifi)
* [Terminal Setup](#terminal-setup)
* [SSH Keypair Setup](#ssh-keypair-setup)
* [AWS Setup](#aws-setup)
* [GitHub Setup](#github-setup)
* [SSH Agent Setup](#ssh-agent-setup)
* [GitHub Classroom Sample Assignment](#github-classroom-sample-assignment)
* [Microsoft Azure Student Account Setup](#microsoft-azure-student-account-setup)


# WiFi

**Georgetown students must be connected to _Saxanet_ wifi when on-campus. Otherwise you will not be able to connect to your cloud resources.**


# Terminal Setup

We will be using the Terminal in almost every task in this course. By "Terminal", we mean a command-line terminal, where you will use it to type instructions and connect to your remote resources.

When we say "terminal" in this course, it means that you need to open up the terminal application for your respective operating system. The terminal is a "local" application, meaning it is running on your "local" machine (your laptop.)

## Windows Users

Windows users will be using the [Windows Powershell](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/powershell). Windows Powershell is most likely installed if you have Windows 10. If you don't have Powershell, take a look at [this article](https://www.howtogeek.com/336775/how-to-enable-and-use-windows-10s-built-in-ssh-commands/) that explains how to install it.

You can find Powershell by typing "Powershell" into the search bar:

<img src='images/powershell1.png'>

Once Powershell is running, this is your terminal:

<img src='images/powershell2.png'>

### Additional Powershell Configuration (you must do this)

You need to perform this step **only once** to be able to use agent forwarding which is explained further in the lab. 

* Exit Powershell if running
* Start a new Powershell session using **run as Administrator**
* Enter the following commands, one line at a time (you can cut/paste from here):

```
Install-Module -Force OpenSSHUtils -Scope AllUsers
Get-Service -Name ssh-agent | Set-Service -StartupType Manual
```

* Exit Powershell. You should not need to run as administrator going forward.


## Mac and Linux Users

For Mac and Linux users, you will open up the Terminal. Macs and Linux have a built in Terminal. I prefer using [iTerm](https://www.iterm2.com/) app as another Terminal application for your Mac. I’ve been using it for years and I love it. This is not required, but truly recommended. 

In Linux, most distributions will open the terminal by using `Ctrl-Alt-T`.


# SSH Keypair Setup

**NOTE: You only need to create your ssh public/private keypair one time only. If you already have a public/private keypair on your laptop let us know.**

* Open a terminal (on your laptop) if not already open. By default, every time you open a terminal it will open in your home directory.
* At the command prompt, type `ssh-keygen -t rsa -b 2048`, press enter
* You will see this prompt, **just press enter**

```  
Generating public/private rsa key pair.
Enter file in which to save the key (/home/User/.ssh/id_rsa):
```

* You will see this prompt, **just press enter**

```
Created directory '/home/User/.ssh'.
Enter passphrase (empty for no passphrase):
```

* You will see this prompt, **just press enter**

```
Enter same passphrase again:
```

* You will see these messages (your randomart will look different) and your keypair has been created.

```
Your identification has been saved in /home/User/.ssh/id_rsa.
Your public key has been saved in /home/User/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:xPJtMLmJSO73x/NQo3qMpqF6r6St4ONmshS8QZqfmHA User@WinDev1802Eval
The key's randomart image is:
+---[RSA 2048]----+
|                 |
|       . .       |
| .  . . *        |
|+. o . = *       |
|++E o . S o o    |
|.=+o     . o .   |
|+oo o o  +o      |
|+= +.o oo.*.     |
|*+=++ooooo o.    |
+----[SHA256]-----+
``` 

## See the contents of your key files

* Open a terminal if not already open 
* Change to your `.ssh` directory. This is a hidden directory so if you list your files using `ls` you won't see it. For seeing all files, use `ls -la`. To change into it type `cd .ssh`.
* Type `pwd` which prints your current working directory. If you are on a Mac, you should see something like:
 
```
$ pwd
/Users/myusername/.ssh
```

If you are using Powershell you will see something like

```
PS C:\Users\marck> pwd

Path
----
C:\Users\marck

PS C:\Users\marck>
```

If you are using Linux you'll see something like 

```
$ pwd
/home/myusername/.ssh
```

* Type `ls -la` to list the files in the directory. What is displayed is my output and yours may look different. You will not have a `config` file unless you have already created one. The `id_rsa` is your **private key** and this file will not leave your computer. The `id_rsa.pub` file is the **public key**, whose contents we will upload to cloud services so you authenticate. The **known_hosts** is a file that gets generated as you connect to different remote systems.
 
```
$ ls -la
total 32
drwxr-xr-x   6 marck  staff   192 May 29 20:39 .
drwxr-xr-x+ 75 marck  staff  2400 May 30 13:35 ..
-rw-r--r--@  1 marck  staff   181 May 29 15:50 config
-r--------@  1 marck  staff  3243 May 29 15:50 id_rsa
-rw-r--r--@  1 marck  staff   742 May 29 15:50 id_rsa.pub
-rw-r--r--   1 marck  staff   363 May 29 20:42 known_hosts
```
* View the contents of your **public_key** file: `cat id_rsa.pub` (what is shown is my public key, yours will be different.)

```
$ cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCnKuIRXwZu0JZH0/Q2XNrYYTaJT7bMtXGhGQaSSOZs6MhQ4SkSbHiygO7RauQf741buLnASzY27GKMMMml6InwfxJWrF60KhNK0r869POQkuZa9v9/cmYcEIzmAJe1xRPABEZ2yfbTG9Wq4sg9cU0mwt1Bx7wiN4QNf0Bak62EC8JWTbcKLduuzO1zabIb5xW9gfR9b4K3HwmqRLl18S8bNsfYQZfvtlwd0mCWQUeuEGbDOgqh//nLIj6DeXdyxbD5xrz79iOAuAK2nXAjNCEtKpxNGQr2Py7aWQjlH+U5laDEHVg4hzmBY7yoZ5eC3Ye45yPqpQA1y8JrbXVhPJRP User@WinDev1802Eval
```
* Open a text editor (Notepad on Windows or Textpad on Mac, **NOT MICROSOFT WORD**) and select the output of your terminal from the `ssh-rsa` beginning all the way to the end, and paste it in your text editor as-is. We will use this in the next step. You can also just leave it here and copy/paste from your terminal screen.


# AWS Setup

You will be using an AWS account created through the AWS Educate Program. To logon to the AWS Console, you must logon to AWS Educate first, go to your AWS Classroom, and then click on AWS Console.

## Select N. Virginia as the Region

The first time you logon to your AWS console, **make sure that the region selected is _N. Virgina_. You can see the region on the top right, next to _Support_. If any other value other than _N. Virginia_ is selected, please select _N. Virgina_. You only need to do this one time.

## Upload your Public key to AWS

* Logon to your AWS Console if you are not already logged in
* Go to the EC2 Dashboard. You can get here by clicking on **Services** in the top left and choosing **EC2** under the **Compute** heading
* Click on **Key Pairs** on the left hand side of the EC2 console. If you don't see it, scroll down the left side panel until you see the **NETWORK & SECURITY** heading, and you will see the following:

<img src='images/create-keypair-1.png'>

* Click on **Import Key Pair** at the top of the screen and the "Import Key Pair" dialog will show:

<img src='images/create-keypair-2.png'>

* In the **Key pair name** box, enter `mykeypair` or a name that is meaningful to you. It can be anything, just make sure you know what it is.
* In the **Public Key Contents** box, paste the contents of the **public key** that you got before.

<img src='images/create-keypair-3.png'>

* Click **Import** and you will see your key in the console.

<img src='images/create-keypair-4.png'>

You will use your key to connect to AWS resources later.


## Create a Security Group

Before we launch our first AWS EC2 instance, we will setup a _Security Group_ in the AWS Console. Each account comes with a default security group, but we will create a specific one that we will use to connect to our EC2 instance via ssh, which means it will open up port 22 on your instance.

* Log on to your [AWS Console](https://console.aws.amazon.com/) if you are not already logged in
* Go to the EC2 Dashboard. You can get here by clicking on **Services** in the top left and choosing **EC2** under the **Compute** heading
* Click on **Security Groups** on the left hand side of the EC2 console. If you don't see it, scroll down the left side panel until you see the **NETWORK & SECURITY** heading, and you will see the following:

<img src='images/create-security-group.png'>

* Click on **Create Security Group**. 
	* Enter a name for your security group (you can call it whatever you want; I called it **open-22** to make it easy.) 
	* Enter a description for your group
	* Leave the VPC value unchanged (your number will be different than the one shown below.)
	* Click on **Add Rule** towards the bottom
	* Select **SSH** from **Type**
	* Select **Anywhere** from **Source**
	* Click **Create**

	<img src='images/security-group-settings.png'>

* You have just created a security group that opens up port 22 and allows you to connect from anywhere, and you should see it in the Security Groups console (again, the group id will be different for each of you). You will use this group that you just created when you launch an instance for this assignment

<img src='images/security-group-console.png'>


## Launch your first Linux instance on AWS

This is a step-by-step guide on how to launch an instance.

* From the **EC2 Dashboard**, click on the **Launch Instance** button

<img src='images/launch-instance-01.png'>

* _Step 1: Choose an Amazon Machine Image:_ 

Click on **Quick Start** on the left hand side and select the first image **Amazon Linux 2 AMI**

<img src='images/launch-instance-02.png'>


* _Step 2: Choose an Instance Type:_ Select the **t2.micro** instance type (this one is eligible for the free tier), and click **Next: Configure Instance Details**

<img src='images/launch-instance-03.png'>

* _Step 3: Configure Instance Details:_ Nothing to do here, click **Next: Add Storage**

<img src='images/launch-instance-04.png'>

* _Step 4: Add Storage:_ Nothing to do here, click **Next: Add Tags**

<img src='images/launch-instance-05.png'>

* _Step 5: Add Tags:_ Nothing to do here, click **Next: Configure Security Group**

<img src='images/launch-instance-06.png'>

* _Step 6: Configure Security Group:_ Click on **Select an existing security group** radio button, and then select the **open-22 (or whichever name you gave it)** Security Group that you created eariler, click **Review and Launch**

<img src='images/launch-instance-07.png'>

* _Step 7: Review Instance Launch:_ You will see a warning about your security group being open to the world. You can ignore it, we know. Make sure that the AMI, the Instance Type, and the Security Group is what you wanted. Click **Launch**

<img src='images/launch-instance-08.png'>

* _Select an existing key pair or create a new key pair:_ In this screen select **choose an existing keypair** from the dropdown, and make sure you select the keypair that you uploaded to AWS, otherwise you will not be able to connect to your machine. Click **Launch Instances**

<img src='images/launch-instance-09.png'>

* _Launch Status:_ Click **View Instances** to go back to the EC2 Instances page

<img src='images/launch-instance-10.png'>

* In the EC2 Console, you will see the status of your instance. Sometimes the instances are launched immediately, but sometimes you have to wait. While waiting, you will see the Instance Status showing a yellow dot with "pending", otherwise it will be a green dot with "running"

* When the instance is in "running" state, it is ready to be used!

## Connect to the remote server using Secure Shell `ssh`

We will be using the terminal with Secure Shell to connect to our remote instance. 

* Open a terminal on your laptop. If you have one already open, type `cd ~` to navigate back to your home directory
* Go to the EC2 Instances page (click on EC2, then on Instances on the left)
* Click on your running instance such that the select box is blue
* Click in the **Description** tab in the bottom if not already selected
* Hover with your mouse over the **Public DNS** text, and you will see a "Copy to Clipboard" icon to the right. Click on that to copy your instances DNS address.

<img src='images/ec2-running.png'>

* In the terminal, type `cd ~` to make sure you are in your home directory
* Type `ssh ec2-user@` and paste your DNS address
* If everything is configured correctly, the first time you connect to a host that you've never connected to, you will see something like this:

```
The authenticity of host 'ec2-54-163-133-120.compute-1.amazonaws.com 
(54.163.133.120)'can't be established.
ECDSA key fingerprint is SHA256:TeYrgHLkYHvD/zcp23bO3wozsLMyPSiSn+edPPo88zE.
Are you sure you want to continue connecting (yes/no)?
```
Note: your values for DNS, IP address and fingerprint will be different

* Enter `yes` and press enter. You will only need to enter yes once.
* If you are successful, you will see something like this:

```
The authenticity of host 'ec2-54-163-133-120.compute-1.amazonaws.com (54.163.133.120)' 
can't be established.
ECDSA key fingerprint is SHA256:TeYrgHLkYHvD/zcp23bO3wozsLMyPSiSn+edPPo88zE.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'ec2-54-163-133-120.compute-1.amazonaws.com,54.163.133.120' (ECDSA) to the list of known hosts.
Last login: Thu Jan 25 01:41:17 2018 from pool-108-45-73-252.washdc.fios.verizon.net

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
[ec2-user@ip-172-31-59-103 ~]$
```

**Congratulations, you have successfully connected to your remote instance!**


# GitHub Setup

## Upload your Public key to GitHub

* Create a [GitHub](http://github.com/) account if you do not already have one
* Log on to your [GitHub](http://github.com/) account if you are not already logged in
* Click on your profile icon on the top-right of the screen and select **Settings** from the dropdown
* Click on **SSH and GPG keys** from the left hand menu
* Click on the **New SSH key** button on the top-right
* Give your key a name (meaningful to you)
* Paste the contents of the **public key** in the Key box, and click the **Add SSH Key** button 

## Test that the ssh key works with GitHub

* Open a terminal if not already open on your laptop 
* At the command prompt, type `ssh -T git@github.com` and press enter to test. If it works, you will see something like this, with your GitHub username:

```
The authenticity of host 'github.com (192.30.253.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.253.112' (RSA) to the list of known hosts.
Hi wahalulu! You've successfully authenticated, but GitHub does not provide shell access.
```

* **You are now ready to use ssh authentication with GitHub.**


# ssh-agent Setup

## Add your private key to memory

One thing to keep in mind is that whenever you want to use your GitHub repository on your cloud resources, you will need to connect to your remote machines and use the SSH agent functionality to pass your ssh private key and store it in the remote server's memory. **You do not need to do this every time you connect to your remote machine; only when you want to clone/push a repository between your remote instance and GitHub.** 

You can read more about ssh-agent [here.](https://en.wikipedia.org/wiki/Ssh-agent)

If you are already connected to your remote host and did not use the ssh-agent, you can exit the remote terminal by typing `exit` and that will bring you back to your "local" terminal. 

__For Mac and Linux:__ 

* Type `ssh-add` in your terminal. You will get a confirmation that looks something like this:

```
➜  ~ ssh-add 
Identity added: /Users/marck/.ssh/id_rsa (/Users/marck/.ssh/id_rsa)
```
Note: if you have issues with `ssh-add` command on Mac/Linux, you can use the instructions for Windows. 

__For Windows:__ 

* In Powershell, if need to use ssh-agent, you need to start the service manually every Powershell session before you can use ssh-agent. You must run `Start-Service ssh-agent`:

```
PS C:\Users\marck> Start-Service ssh-agent
PS C:\Users\marck> ssh-add
Identity added: C:\Users\marck/.ssh/id_rsa (C:\Users\marck/.ssh/id_rsa)
PS C:\Users\marck>
```

## Connect to remote machine with ssh-agent forwarding

To connect to the remote server with your ssh-agent, you need to use the `-A` parameter in your ssh command: `ssh -A ec2-user@...` where `...` is your instance's DNS. 

One you connect to your remote machine, you can test that the ssh-agent forwarded your private key. Test the ssh connection to GitHub as we did before: 

* Type `ssh -T git@github.com` on your **remote** terminal
* If the `ssh-agent` worked properly, you should get a success message from GitHub:

```
[ec2-user@ip-172-31-59-103 ~]$ ssh -T git@github.com
Hi wahalulu! You've successfully authenticated, but GitHub does not provide shell access.
```

# Install git on Remote Machine

The virtual machine image we chose is a bare-bones Linux image. You will need to install git on the remote machine so you can clone/push your repository.

* ssh to your remote machine
* At the remote terminal prompt, type: `sudo yum -y install git`

When git is finished installing, you will see something like this:

<img src='images/git-install.png'>


# GitHub Classroom Sample Assignment

We will be using [GitHub Classroom](https://classroom.github.com/) for assignments. Working with GitHub classroom is very easy. When an assignment is created, we will share a GitHub link with you. The link to the sample assignment will be provided in the lab session. 

When you click on the assignment link, you will be asked to "accept" the invitation to this assignment. Please accept, and when you do so, GitHub will automatically create a private copy of this repository within your own GitHub account. Course instructors and TA's have access to your repository.

The assignment instructions are contained within the README file of the repository. Please perform the assignemtn as indicated.


# Microsoft Azure Student Account Setup

You will setup a student account on [Microsoft Azure](https://azure.microsoft.com/en-us/). This account provides you with a $100 credit that is good for a year. 

1. Open an incognito browser session
1. Go to [https://azure.microsoft.com/en-us/free/students/](https://azure.microsoft.com/en-us/free/students/)
2. Click on Activate Now
3. Enter your school email address (may require further authentication)
4. Go through the rest of the process
5. After your account is validated, you will end up logged in to the [Azure Portal](http://portal.azure.com).
6. To use Azure in the future, logon with your school email. It is recommended that you use an incognito/private browser session.
