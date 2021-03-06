This was a Level 1 problem. The prompt was as follows: 

> While webshells are nice, it'd be nice to be able to login directly. To do so, please add your own public key to ~/.ssh/authorized_keys, using the webshell. Make sure to copy it correctly! The key is in the ssh banner, displayed when you login remotely with ssh, to shell2017.picoctf.com
>
> Hint:
> There are plenty of tutorials out there. This one worked for me: https://www.digitalocean.com/ community/tutorials/how-to-set-up-ssh-keys--2

So I had used ssh before through my computer science coursework at OSU, and I had a beginner’s understanding of public/ private key pairs through CSE 4471, but admittedly I was not sure how the two fit together. 

First, I read through the linked tutorial. It gives a fairly comprehensive walkthrough of how to generate ssh keys, but it didn’t explain how they were used for authentication. Through some googling, I found the following link: https://www.ssh.com/ssh/keygen/. This site filled in the blanks on how all the pieces fit together, and gave me a clearer understanding of what I need to do moving forward. 

Based on my research, I surmised that I needed to create an ssh key pair on my personal system, and then copy the public key over to the remote system, which in this case was the Pico web shell. To generate the keys on my own computer I used the following command:

```
	$ ssh-keygen -t rsa
```

It then asked me which file I wanted to save the key in.

```
	$ Enter file in which to save the key (/home/ssg/.ssh/id_rsa):
```

I left it blank to keep the default. I didn’t have an existing .ssh directory, but the process created one anyway by default. Moving on, I kept the other options blank as well to keep the default options.

```
	$ Enter passphrase (empty for no passphrase): 
	$ Enter same passphrase again:
```

At this point the key has been created and it’s located in /home/ssg/.ssh. Within this directory there are 2 files, id_rsa and id_rsa.pub. In order to complete the last leg of the challenge, I needed to copy the public key, id_rsa.pub, into a directory on the remote host called .ssh/authorized_keys. 

This problem stumped me at first. I just wasn’t sure how to copy the the id_rsa.pub file from one system to another. I started trying to use tools that I already knew, like the scp command and FTP servers. Using an FTP server seemed way too complicated for something as minor as this, but using scp seemed plausible. However, I soon learned that the Pico webshell was not going to cooperate. So what I finally decided to do was simply copy the public key over using my laptop’s shared clipboard, as follows:

```	
	[on home system]
	$ cat id_rsa.pub
	ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDRPvWsRP4Pt+eYP1EHMNL1UZAMTWjqXZg74AKFOn/	Dj7HRRa7YxuBgoUKtHpF1lJDTt3BxoNQeqaAWf/eznqq9PyaD9dYgJalgIDLwdU8jCtsM7aDX/G3qmNboM08ZG3W0ZEC9uQyuPXhjbPpR2v5jJNuLN8KIBCnrhIFb/NnXqxK64qI9rwafyMLvKu3t0RyAojCtNPEt1xvSWKor9+8+978iiPwWYQ/P3bLKg8dezgMxh3JpAp8WwWlQ4g3IiEbwYfhOZlmf6R7SKFPFXcW2t1EMCn793FMHiZ0Rn5Jlz4CSr/fpltvobUaCMtkLVvFdE1o5eincZHW0uY2UwkeX ssg@DESKTOP-8J9L2GB (copy this string)
```

```
	[on Pico webshell]
	$ mkdir .ssh
	$ cd .ssh
	$ mkdir authorized_keys
	$ vim id_rsa.pub (copy the string into here)
```

The key (no pun intended) feature about public/private key encryption is that you can post your public key anywhere, but your credentials can’t be compromised without your private key. Now all that’s left is log into the webshell with the new key. 

```
	$ ssh sgrew@shell2017.picoctf.com
	Congratulations on setting up SSH key authentication!
	Here is your flag: who_needs_pwords_anyways
```

And there we have it. I ended up learning at a later time that you could make everything a whole lot easier by using the ssh-copy-id command. But where would be the fun in that? Going forward, I’m definitely glad I learned about creating and using ssh key pairs, because I have no doubt they’ll reoccur in future jobs and projects. 

