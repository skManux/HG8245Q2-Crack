
# HG8245Q2-Crack
How to crack the HG8245Q2 router and gain "UserLevel=0" web access!

<img src="https://github.com/user-attachments/assets/e383589d-e2b3-4351-ac34-5570566954ff" alt="image" width="250">

# Requirements
To follow this guide, you need to have:
- A **Huawei HG8245Q2** Router
- A **Linux Machine**
- **Telnet** access to the Router
- 1024 Liters of **Coffee**

# Guide
## Step 1: Reset the router
First of all, you need to reset your router to factory settings.
In the photo you will find the position of the "RESET" button. Press it and wait for the router to restart.

<img src="https://github.com/user-attachments/assets/87f8ecb7-1b4e-4f5d-a154-58aaac979ae8" alt="image" width="350">

## Step 2: Get access to a root shell
Next, connect via Ethernet and use any SSH/Telnet client to connect **WITH TELNET PROTOCOL ONLY** to `192.168.1.1`/`192.168.100.1`, on port `23`.

![image](https://github.com/user-attachments/assets/c156fd30-bfe6-467b-809a-fb4a9d3394a3)

Then login with:
| Login | Password |
|:-----:|:--------:|
| root  |  admin   |

![image](https://github.com/user-attachments/assets/c7c8bf3a-6d1c-4aed-b425-fec4337c5fba)

Next, run these commands:

```
su
shell
su root
```
It should ask you for a password. Input the same one that you typed in before.

You're in!

![image](https://github.com/user-attachments/assets/46e80040-49bd-4ce1-9126-4ddb4fbe04df)

## Step 3: Download the unencrypted configuration
Run these commands to decrypt the configuration file:

```
cd /mnt/jffs2
aescrypt2 1 hw_ctree.xml hw_ctree.decrypted.xml
```

Then, on a linux machine, run this command to start a netcat server and download the file:

```
nc -l -p 1234 > hw_ctree.decrypted.xml
```

Then, return to the router telnet and run this command to send the file to the linux machine:

```
cat hw_ctree.decrypted.xml | busybox nc <Linux Machine's IP Here> 1234
```

![image](https://github.com/user-attachments/assets/9a280eea-386f-42c6-bcab-a154c7022662)

## Step 4: Edit the configuration, upload it and re-encrypt it
Open the .xml file you downloaded on the linux machine with an editor (Not VS Code), and search for a line starting with `<X_HW_WebUserInfoInstance`, and add this line under the one you found:

```
<X_HW_WebUserInfoInstance InstanceID="69" UserName="fakeadmin" Password="465c194afb65670f38322df087f0a9bb225cc257e43eb4ac5a0c98ef5b3173ac" UserLevel="0" Enable="1" ModifyPasswordFlag="1" PassMode="2" Salt="54589f5b2cfc1cbc86ae77a1"/>
```

![image](https://github.com/user-attachments/assets/8a8f6d5c-ce37-45f0-8e98-a6c69672a218)

Then, save the file and return to the shell.
You have to re-upload this file to the router. To do this, you have to rename it from `hw_ctree.decrypted.xml` to `hw_ctree.modified.xml`.

Then, return to the router telnet and run this command to start a netcat server and download the file:

```
nc -l -p 1234 > hw_ctree.modified.xml
```

Then, on a linux machine, run this command to send the file to the router:

```
cat hw_ctree.modified.xml | nc <Router's IP Here> 1234
```

Finally, you have to re-encrypt the file, running this on the router:

```
aescrypt2 0 hw_ctree.modified.xml hw_ctree.encrypted.xml
```

![image](https://github.com/user-attachments/assets/700c877b-b22c-48f5-9f36-ec3220f7d267)

## Step 5: Switch the configuration
To actually use this new configuration, we have to switch the `hw_ctree.xml` with the `hw_ctree.encrypted.xml` file we uploaded.
To do that, i recommend you to backup the current config:

```
mv hw_ctree.xml hw_ctree.xml.bak
```

Then, you can run these commands:

```
mv hw_ctree.encrypted.xml hw_ctree.xml
chown srv_ssmp:service hw_ctree.xml
chmod 644 hw_ctree.xml
```

You're all set! Now, you can **reboot** the router.
### DO NOT RESTART THE ROUTER VIA HARDWARE!
To reboot the router, run the following command in the router's telnet:

```
reboot
```

You will see 2 errors, but don't worry: You simply have to wait: nothing more, nothing less.

## Step 6: Log-in to the router
Now that you have switched configuration, if you did everything correctly and the configuration isn't corrupted, you should be able to log into the webpage of the router and use the following credentials:

|   Login    | Password |
|:----------:|:--------:|
| fakeadmin  |  admin   |

# Credits and Useful Links
Yes, i did my own tests to get to this conclusion :)

Credits: https://github.com/skManux
