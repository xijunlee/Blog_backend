---
title: 'GitMemo: Working with Git on Windows'
date: 2017-02-21 21:41:38
tags:
	git
---

## Preamble

Setting up Git can be tricky on Windows compared to Linux or Mac, but if you follow the steps in this guide, you should have no problems using Git on Windows. This guide will take you through the steps to install and configure Git and connect it to remote repositories to clone, push, and pull. 

<!-- more -->

## Choosing a Git distribution

There are two competing Git packages for Windows: a [Cygwin-based](http://www.cygwin.com) Git and a version called [msysGit](https://git-for-windows.github.io). I will describe how to install the msysGit package. I recommend installing msysGit because I’ve found it’s easier to work with than the Cygwin based installation.

## Installing Git

Once you have downloaded the msysGit executable, double click on it to start the installation wizard. Leave the default directory options. When you get to the “Adjusting your Path environment” setting, select the “Run Git from the Windows Command Prompt” option. Choosing this option will make it easy for you to run Git commands from the Windows Command Prompt (command line) if you choose. Command Prompt is a simple tool, where you can run commands, switch through folders, manage files and it can be ran by selecting RUN… in START menu, and executing `cmd`command.

** Note ** : I recommend that you better choose the default options when installing `msysGit` for saving your time and mind.

You will notice that for the rest of this article we will use Git Bash for running Git commands. The Git Bash tool works in the same way as the default Windows’ Command Prompt, but has some special features. With Git Bash you’ll be able to use a number of UNIX command line tools along with access to Git, and we recommend it since it’s often simpler to use than the Windows Command Prompt.

You can run it by right clicking your mouse on the desktop, and selecting Git Bash from pop up window.

** Important note **: The most common problems when setting up Git on Windows are related to SSH keys. Git uses SSH keys to securely access your repositories, and in Windows SSH keys are often searched on the wrong path when you try to use Git.

If you use an older version of msysGit, you may encounter a step called “Choosing the SSH executables”. If you encounter that dialog, we recommend that you choose the “Use OpenSSH” option.

After you have successfully installed Git on Windows, you’ll need to provide secure communication with your Git repositories by creating and installing SSH keys.

## Installing SSH keys on Windows

To access your Git repositories you will need to create and install SSH keys. You can do this using OpenSSH and generate SSH keys with `ssh-keygen`

### Using OpenSSH and generating SSH keys with ssh-keygen

To communicate with the remote Git repository from your Windows computer, you will need to generate an SSH key pair for that computer. This process requires only a few steps, but you do first need to install `msysGit` using the full installer as described above.

#### Generating a key pair

To do this you need to run Git Bash, which can be found in your START menu. Run the command:

```bash
ssh-keygen -t rsa -C youremail@xxx.xxx
```
Next, there will be several messages that asks you to set pass phrase. You could keep entering `return` key to skip these askings.

Now that the keys are generated, open the file id_rsa.pub (found in the default location from the previous step) with a text editor. The contents of this file is your new public key. If you copy it to your clipboard, you can add it to your git SSH keys.

Your SSH public key should look something like this:

```bash
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAyyA8wePstPC69PeuHFtOwyTecByonsHFAjHbVnZ+h0dpomvLZxUtbknNj3+
c7MPYKqKBOx9gUKV/diR/mIDqsb405MlrI1kmNR9zbFGYAAwIH/Gxt0Lv5ffwaqsz7cECHBbMojQGEz3IH3twEvDfF6cu5p
00QfP0MSmEi/eB+W+h30NGdqLJCziLDlp409jAfXbQm/4Yx7apLvEmkaYSrb5f/pfvYv1FEV1tS8/J7DgdHUAWo6gyGUUSZ
JgsyHcuJT7v9Tf0xwiFWOWL9WsWXa9fCKqTeYnYJhHlqfinZRnT/+jkz0OZ7YmXo6j4Hyms3RCOqenIX1W6gnIn+eQIkw==
Mac Pro
```

### Adding your SSH public key to your git SSH keys

1.Log your git account in, and click your avatar. Then, click `Setting` and find `SSH and GPG keys`.

2.Click `New SSH key`, copy your SSH public key in `Key` input area, and give this SSH key name in `Title` input area.

### Checking your connection

Enter the following code to check whether you access to github successfully:

```bash
ssh git@github.com
```
You will see:

```bash
The authenticity of host 'github.com (207.97.227.239)' can't be established.
    RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
    Are you sure you want to continue connecting (yes/no)?
```

Enter `Yes`, you will see:

```bash
Hi your name! You've successfully authenticated, but GitHub does not provide shell access.
```

If you see above code, that indicates you have accessed github successfully!




