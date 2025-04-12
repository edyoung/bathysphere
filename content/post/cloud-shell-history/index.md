+++
author = "Edwin Young"
date = "2020-09-07T18:11:06-07:00"
description = "Describes history storage for Cloud Shell, PowerShell and Bash"
slug="cloud-shell-history"
image="image.png"
title = "Does Azure Cloud Shell store my command history?"
type = "post"
+++

A few people have asked _"Hey, uh, hypothetically, if I had typed a password or a secret at the Cloud Shell prompt, did you store it, and how would I delete it? Obviously I would never do this, asking for a friend."_

Relax, we've all done it. And the answer is "Sort of".

In more detail, Cloud Shell does not store your history itself. But the shell you are running inside it does, unless you have configured something specially. The details depend on the shell. Since the shell doesn't know what is a password and what is not, it *will* store secrets if you type them directly at the command-line. 

# Bash
Bash stores the commands you type in `~/.bash_history`. This gets saved when you exit the shell. 

# PowerShell
PowerShell uses a package called PSReadLine to do the command-line interaction (such as tab completion). That module, rather than PowerShell itself, stores the commands you type in `~/.local/share/powershell/PSReadLine/ConsoleHost_history.txt` by default. 
You can adjust the location and behavior with [`Set-PSReadLineOption`](https://docs.microsoft.com/en-us/powershell/module/psreadline/set-psreadlineoption?view=powershell-7).

# Cleaning Up

Whichever shell you use, you can clear either file by deleting it. Remember that the file is usually updated at the end of a session, so you may need to exit Cloud Shell, reopen it, then delete the file to be sure you caught it.

Your entire home directory (including these files) is stored in a file called `.cloudconsole/acc_{username}.img` which is kept in your Cloud Drive, which is in your own Azure subscription. You can see this by clicking "Manage File Share" in the Cloud Shell toolbar and opening the .cloudconsole folder in Storage Explorer. If you are feeling super paranoid you can delete the whole file, but you will of course lose everything in your home directory.