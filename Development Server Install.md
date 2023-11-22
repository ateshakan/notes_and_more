# Ngrok Setup
## Step 1: Install the ngrok Agent
o download and install the ngrok agent on your remote Linux device, follow these steps:

1. Open a terminal into your Linux device.
2. Download the latest ngrok binary for your Linux distribution. You can find the correct binary on our [ngrok download page](https://ngrok.com/download): Below is an example for our case x86-64:

```sh
wget https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz
```

Unzip the downloaded file and move it to a directory in your PATH. Below is an example for `/usr/local/bin`

```sh
sudo tar xvzf ./ngrok-v3-stable-linux-amd64.tgz -C /usr/local/bin
```

```sh
ngrok authtoken NGROK_AUTHTOKEN
```

**Note**: Replace `NGROK_AUTHTOKEN` with your unique ngrok authtoken found in the [ngrok dashboard](https://dashboard.ngrok.com/get-started/your-authtoken).

## Step 2: Enable SSH access

```sh
ngrok tcp 22
```

The ngrok agent assigns you a TCP address and port. Use these values to test the SSH access via ngrok by running the following command from **another server or from a desktop**. The screen should look something like this:

```sh
ngrok (Ctrl+C to quit)  
  
Session Status online  
Account inconshreveable (Plan: Free)  
Version 3.4.0  
Region United States (us)  
Latency 78ms  
Web Interface http://127.0.0.1:4040  
Forwarding https://84c5df474.ngrok-free.dev -> http://localhost:8080  
  
Connections ttl opn rt1 rt5 p50 p90  
0 0 0.00 0.00 0.00 0.00
```

This is for the SSH connection on VSCode or VNC Viewer.
```sh
ssh -p NGROK_PORT USER@NGROK_TCP_ADDRESS
```

- **NGROK_PORT**: The port number of the ngrok agent (i.e. if the agent shows `tcp://1.tcp.ngrok.io:12345`, your port number is `12345`.
- **USER**: A valid ssh login to access your remote device's operating system. (*user, utente, michael, mustafa, baturay etc*.)
- **NGROK_TCP_ADDRESS**: The address of the ngrok agent (i.e. if the agent shows `tcp://1.tcp.ngrok.io:12345`, your TCP address is `1.tcp.ngrok.io`


# Vscode Remote Developement

[Remote Developement using SSH](https://code.visualstudio.com/docs/remote/ssh)
[Remote - SSH Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)

### Connect to the host:

To connect to a remote host for the first time, follow these steps:

Verify you can connect to the SSH host by running the following command from a terminal / PowerShell window replacing `user@hostname` as appropriate.
```sh
ssh user@hostname
# Or for Windows when using a domain / AAD account
ssh user@domain@hostname
ssh -p NGROK_PORT USER@NGROK_TCP_ADDRESS
ssh -p 14421 baturay@6.tcp.eu.ngrok.io #this is the way with ngrok

```
If VS Code cannot automatically detect the type of server you are connecting to, you will be asked to select the type manually. In our case it's Linux
![[Pasted image 20231122110427.png]]

1. Open the project folder by navigating to File > Open Folder and selecting **/home/baturay/Desktop/breathment/samurAI**. Enter your password when prompted.
2. Ensure you are using Python 3.8.10 for this repository.
3. Run both the Front End and Back End repositories on the local machine, not on the host.
4. If you encounter difficulties connecting to port 5000, consider removing any automatic forwarding ports (e.g., 5001) to resolve the issue. You can find this on VScode PORTs tab.

![[Pasted image 20231122111312.png]]


# VNC - TigerVNC Installation

### How to install TigerVNC:
```sh
sudo apt install tigervnc-standalone-server tigervnc-xorg-extension tigervnc-viewer
```

Install gnome:
```
sudo apt install ubuntu-gnome-desktop  
sudo systemctl enable gdm  
sudo systemctl start gdm
```

You need to create a file name ``~/.vnc/xstartup`` using a text editor such as vim command or nano command:

```sh
nano ~/.vnc/xstartup
```

Append the following:
```sh
#!/bin/sh
# Start Gnome 3 Desktop 
[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
vncconfig -iconic &
dbus-launch --exit-with-session gnome-session &
```
### How to start TigerVNC server
Type the following command to start the vnc server on Ubuntu:  
```sh
vncserver
```

One can setup desktop bit depth such as 8, 16, 24, 32 and Desktop geometry in {width}x{height} as follows:  
```sh
vncserver -depth 32 -geometry 1680x1050  
```

Verify it with the ss command and pgrep command/egrep command:  
```sh
pgrep Xtigervnc  
ss -tulpn | grep -E -i 'vnc|590'
```

### How to stop TigetVNC server

```sh
vncserver -kill :1
```
will result: `Killing Xtigervnc process ID 9523... success!`

### Check the port for connection
vncserver -list

```sh
vncserver -list
```

```sh
TigerVNC server sessions:

X DISPLAY #     RFB PORT #      PROCESS ID
:2              5902            7419
```
*Note down the port #5902. We are going to use that one with the ssh command.*


To make the communication secure, you must encrypt your server-client connection by using an SSH tunnel between the VNC server and the client. Type the following ssh command to setup SSH tunnel from your Linux or Unix desktop:

```sh
ssh user@remote-server -L **PORT**:127.0.0.1:**PORT**  
ssh baturay@baturay -L 5902:127.0.0.1:5902
```

### setup ngrok for the port:
```sh
ngrok tcp 5902
```

```sh
ngrok (Ctrl+C to quit)  
  
Session Status online  
Account inconshreveable (Plan: Free)  
Version 3.4.0  
Region United States (us)  
Latency 78ms  
Web Interface http://127.0.0.1:4040  
Forwarding tcp://6.tcp.eu.ngrok.io:14421 -> http://localhost:5902  
  
Connections ttl opn rt1 rt5 p50 p90  
0 0 0.00 0.00 0.00 0.00
```

We only need **6.tcp.eu.ngrok.io:14421** part.
### Download a VNC Viewer:

[Download TightVNC](https://www.tightvnc.com/download.php)

Insert the IP and Connect
![[Pasted image 20231122105355.png]]
