+++
author = "Edwin Young"
tags = ["Azure", "CloudShell", "PowerShell", "bash"]
date = "2020-09-21T18:11:06-07:00"
description = "How to install your own versions of tools in Azure Cloud Shell"
slug = "cloud-shell-installs"
title = "How to install your own versions of tools in Azure Cloud Shell"
type = "post"
image = "image.png"
+++

Cloud Shell comes with a lot of tools, and we try to keep them up to date. But sometimes you need something a little bit more cutting edge or unusual. How do you install it?

Using Cloud Shell is a little different from running your own Linux computer, but it's a lot like using a multi-user Unix system. 
The main difference is **you are not root** and we won't tell you the root password (sorry). 
So anything that requires you to run `sudo` or change files outside your home directory or /tmp won't work. This includes old favorites like `apt-get install`.

> Why? A tiny bit for security - we don't want to make it any easier for people to break out of Cloud Shell than it needs to be - but 
> mostly for consistency. In Cloud Shell the only things that persist across sessions are the contents of your home directory 
> and clouddrive. If you manage to change files and install something under /usr/bin, it would magically be gone the next time you start
> Cloud Shell, and that wouldn't be too helpful.

So the short version is, anything you can install to your home directory or below it will work just fine. Let's look at some examples.

## PowerShell modules

Just install them with `Install-Module`. By default, they will be saved to `~/.local/share/powershell/Modules/`. Those will be loaded first 
in preference to ones that are installed in the system-wide directories, which can be good and bad. Mostly good, but consider this:

1. Cloud Shell ships with AwesomeModule 1.0
2. You're like "That's old school, AwesomeModule 1.1 is way better". `Install-Module AwesomeModule -RequiredVersion 1.1` FTW!
3. Cloud Shell updates to AwesomeModule 2.0
4. You still wind up using AwesomeModule 1.1. Sadness.

This can be particularly tricky with the Azure PowerShell modules, because installing one of them brings along 10 others it depends on. You may want to periodically delete `~/.local/share/powershell/Modules/` to be on the safe side.

## Node Modules

Just install them with `npm install`. But `npm install -g` tries to update `/usr/local/lib/node_modules` which won't work, so don't do that.

## AZ CLI and extensions

You can install AZ CLI extensions with `az extension add`. This saves the extension to a location in your home dir, and just works.

However updating the whole of AZ CLI is more difficult. I don't have good instructions for doing that at the moment.

## Random Executables and tools

If you have a favorite unix tool look for the "Manual Installation" instructions on its home page, those will usually work even if `apt-get` does not. For example, we don't have the GitHub CLI tool (at the time of writing anyway😉). If you look at https://github.com/cli/cli/blob/trunk/docs/install_linux.md it starts with instructions about using apt-get which (sorry) won't work. But under 'Manual Instructions' it turns out you can just download https://github.com/cli/cli/releases/download/v1.0.0/gh_1.0.0_linux_amd64.tar.gz , unpack it and run it.

```bash
wget https://github.com/cli/cli/releases/download/v1.0.0/gh_1.0.0_linux_amd64.tar.gz 
tar xzvf gh_1.0.0_linux_amd64.tar.gz
gh_1.0.0_linux_amd64/bin/gh
```

The last bit points out one snag. You either need to run a program via its full path or add it to a directory in your $PATH. As with PowerShell modules, 
be aware that if your directory is earlier in the $PATH than the standard ones, if you shadow a tool that Cloud Shell installs you might not see future updates.

## Terraform

Terraform basically falls under the 'random executables bucket' but there are a few special considerations.

You can install it locally with

```
wget -O terraform.zip https://releases.hashicorp.com/terraform/0.13.2/terraform_0.13.2_linux_amd64.zip
unzip terraform.zip
./terraform 
```

But it won't behave quite the same as the one in Cloud Shell. That's because if you look at 

```bash
edwin@Azure:~$ more /usr/local/bin/terraform
#!/usr/bin/env bash

set -e

export ARM_SUBSCRIPTION_ID=`az account show --output=json | jq -r -M '.id'`
export ARM_TENANT_ID=`az account show --output=json | jq -r -M '.tenantId'`

export ARM_MSI_ENDPOINT=$MSI_ENDPOINT
if [ -z "$ARM_MSI_ENDPOINT" ]; then
  export ARM_USE_MSI=false
else
  export ARM_USE_MSI=true
fi
export PATH=/usr/local/terraform:$PATH

terraform "$@"
```

You can see it's really a script where we set a couple environment variables so terraform works better in Cloud Shell. To get the benefit of those too, you need to copy the script to (say) `~/terraform.sh` and 
edit it so it finds and runs your local terraform executable as well.