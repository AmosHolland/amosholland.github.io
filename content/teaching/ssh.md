+++
title = "Using SSH at UoB"
description = "A short guide on using SSH at The Univeristy of Bristol. Specifically tailored for Computer Science Students."
date = 2026-07-02
authors = []
+++

While studying at the Univeristy of Bristol it's likely that you will want to
access a lab machine remotely at some point. Maybe you need to transfer a file
from your personal machine onto the lab machines, or run some code on the 
lab machines hardware while working from home. In all of these cases, SSH
(Secure SHell) is your friend. SSH is a program, available from the command line
, that enables remote access to another computer. In this guide we'll go through
the process for setting SSH up for use on Computer Science courses at the
University of Bristol.

These instructions are for using SSH in a shell, which should work these days, 
even on Windows (if you are on Windows you should use PowerShell). All of this 
set up is also expected to be done on the machine that you want to connect to
a lab machine from.

# Preliminaries

First, generate an ssh key, using e.g.

```bash
ssh-keygen -t RSA -f $HOME/.ssh/lab-machines
```

**The file-path may need to be different on Windows**

This will ask you if you want to set up a passphrase, just skip through the
dialogue until the process has finished.

### Note: Setting up away from campus
The rest of these instructions assume that you are on campus (as this is easier)
if that is the case then you can just skip ahead to the next section. If you are 
not on campus, you should start with the second section (up to setting up 
ProxyJump), and come back to the first section once you can connect to seis
easily. When completing the first section, whenever you need to access a lab 
machine, or copy something to one, you will need to go through seis e.g. 
connect to seis, then connect to the lab machine from seis.  

# Section 1: Accessing lab machines remotely while on campus

The address of the linux lab machines is rd-mvb-linuxlab.bristol.ac.uk. To 
connect to a machine remotely we just need to run the command.

```bash
ssh USERNAME@rd-mvb-linuxlab.bristol.ac.uk
```

Replacing USERNAME with your UoB username (the characters at the start of your 
email, e.g. ab12345).

This will prompt you to enter your password, which will be the same as the 
password you use to log in to the lab machines.

After logging in you should have access to a shell on the lab machines! 
Technically this is all we need, but it would be nice to not have to type our 
our password every time - we can make this possible using the key we generated 
earlier!

First, leave the ssh session by running 

```bash
exit
```

Then we need to copy the public version of our key onto the lab machine, we can 
do this with the following command.

```	bash
scp $HOME/.ssh/lab-machines.pub USERNAME@rd-mvb-linuxlab.bristol.ac.uk:.ssh/
```

Now, ssh back in to the lab machine, and run the following command to add your
ssh key to the list of known hosts.

```bash
cat $HOME/.ssh/lab-machines.pub >> $HOME/.ssh/authorized_keys
```

In order to use our key when we connect, we need to configure ssh. To do this, 
enter your .ssh folder with:

```	bash
cd $HOME/.ssh
```

and create a file here called config (if there is not such a file already) using 
e.g.

```bash
touch config
```
Now open this file in a text editor, and add the following config information to 
the bottom of the file

```name=config
Host lab
    HostName rd-mvb-linuxlab.bristol.ac.uk
    User USERNAME
    IdentityFile ~/.ssh/lab-machines
```

This sets up a host called lab which accesses the linux lab with your username
and the ssh key in lab-machines. To test this out, run

```bash
ssh lab
```
If everything is working (and we are connected to campus wifi) this should
connect you to the linux lab! If this works then we are set up to ssh into
lab machines while on campus, just run ssh lab whenever you need. Setting
up a host like this can also make it easier to configure text editors like
vscode to run through ssh (for vscode there is a ssh extension).