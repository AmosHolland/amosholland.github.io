+++
title = "Using SSH at UoB"
description = "A short guide on using SSH at The Univeristy of Bristol. Specifically tailored for Computer Science students."
date = 2026-07-02
authors = []

[extra]
copy_button = true
+++

While studying Computer Science at the Univeristy of Bristol it's likely that 
you will want to access a lab machine remotely at some point. 
Maybe you need to transfer a file from your personal machine onto the lab 
machines, or run some code on the lab machines' hardware while working from 
home. In all of these cases SSH(Secure SHell) is your friend. SSH is a program, 
available from the command line, that enables remote access to another computer. 
In this guide we'll go through the process for setting SSH up for use on 
Computer Science courses at the University of Bristol.

These instructions are for using SSH in a shell, which should work these days, 
even on Windows (if you are on Windows you should use PowerShell). All of this 
set up is expected to be done on the machine that you want to connect to
a lab machine from.

# Preliminaries

First, generate an ssh key, using e.g. 

```bash
ssh-keygen -t RSA -f $HOME/.ssh/lab-machines
```

This will ask you if you want to set up a passphrase, just skip through the
dialogue until the process has finished. If you are on Windows the file path
may need to look a bit different.

The rest of these instructions assume that you are on campus (as this is 
easier). If that is the case then you can just go straight to the 
[next section](#section-1-accessing-lab-machines-remotely-while-on-campus). 
If you are not on campus, you should start with the 
[second section](#section-2-accessing-a-lab-machine-while-not-on-campus) (up to 
setting up ProxyJump), and come back to the first section once you can connect 
to seis easily. When completing the first section, whenever you need to access 
a lab machine, or copy something to one, you will need to go through seis i.e. 
connect to seis, then connect to the lab machine from seis.  

# Section 1: Accessing lab machines remotely while on campus

The address of the linux lab machines is `rd-mvb-linuxlab.bristol.ac.uk`. To 
connect to a machine remotely we just need to run the command. Start by running
this.

```bash
ssh USERNAME@rd-mvb-linuxlab.bristol.ac.uk
```

Replacing `USERNAME` with your UoB username (the characters at the start of your 
email, e.g. vo21765).

This will prompt you to enter your password, which will be the same as the 
password you use to log in to the lab machines.

After logging in you should have access to a shell on the lab machines! 
Technically this is all we need, but it would be nice to not have to type our 
our password every time - we can make this possible using the key we generated 
earlier!

While we are here, we need to create a `.ssh` directory which we will use later.
Run the following command (while still connected to the lab machine):

```bash
mkdir $HOME/.ssh
```

If a `.ssh` directory already exists that is fine.

Next, leave the ssh session by running:

```bash
exit
```

Then we need to copy the public version of our key onto the lab machine, we can 
do this with the following command.

```	bash
scp $HOME/.ssh/lab-machines.pub USERNAME@rd-mvb-linuxlab.bristol.ac.uk:.ssh/
```

Now, ssh back in to the lab machine, and run the following command to add your
ssh key to the list of authorised keys.

```bash
cat $HOME/.ssh/lab-machines.pub >> $HOME/.ssh/authorized_keys
```

In order to use our key when we connect, we need to configure ssh. To do this,
disconnect from ssh (again using `exit`), enter your `.ssh` folder with:

```	bash
cd $HOME/.ssh
```

and create a file here called `config` (if there is not such a file already) 
using e.g.

```bash
touch config
```
Now open this file in a text editor (e.g. `nano`, `vscode`, etc.), and add the 
following configuration information to the bottom of the file

```
Host lab
    HostName rd-mvb-linuxlab.bristol.ac.uk
    User USERNAME
    IdentityFile ~/.ssh/lab-machines
```

This sets up a host called lab which accesses the linux lab with your username
and the ssh key in `lab-machines`. To test this out, run

```bash
ssh lab
```
If everything is working (and we are connected to campus wifi) this should
connect you to the linux lab! If this works then we are set up to ssh into
lab machines while on campus, just run `ssh lab` whenever you need. Setting
up a host like this can also make it easier to configure text editors like
vscode to run through ssh (for vscode there is a ssh extension), and
makes `scp` easier to use as well (see the [last section](#extra-scp)).

# Section 2: Accessing a lab machine while not on campus

CLOSED FOR MAINTENANCE

# Extra: scp
By doing this set up we have also configured `scp` to use our hosts, so we can 
copy files between our machine and the lab machines easily as well. 
To copy a file from your machine to the lab machines just run

```bash
scp PATH_TO_FILE lab:DESTINATION_PATH
```

Where `PATH_TO_FILE` is a path to the file on your machine, and 
`DESTINATION_PATH` is the path to the desination on the lab machine. If you 
leave `DESTINATION_PATH` empty the file will just be copied to the home 
directory of the lab machine.

To copy a file from the lab machine, we can similarly run

```bash
scp lab:PATH_TO_FILE DESTINATION_PATH
```