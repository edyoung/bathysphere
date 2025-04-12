---
title: "Azcli_and_azurepowershell_context"
date: 2020-09-07T17:11:06-07:00
draft: false
author: "Edwin Young"
tags: ["tip","azure","azcli","PowerShell"]
description: "Explains the difference in how context is handled between AZ CLI and Azure PowerShell"
#featured = "pic03.jpg"
#featuredalt = "Pic 3"
#featuredpath = "date"
#linktitle = ""
title: "What's the difference between az account show and Get-AzContext?"
type: "post"
image: "paris.jpg"
---

One issue which can trip you up when using command-line tools to configure Azure is a confusing *lack* of interaction between Azure CLI and Azure PowerShell. They both provide a "context" so you don't have to keep specifying the subscription over and over. But Azure CLI (the `az` command) has a separate context from Azure PowerShell. You can see this by running

```sh
az account show                                  # show azure CLI context
Get-AzContext                                    # show azure Powershell context
az account set --subscription 'mysub2'           # change Azure CLI context
az account show                                  # azure CLI has changed
Get-AzContext                                    # Azure PowerShell has not!
```

You should find that the second output from `az account show` has changed to your `mysub2` subscription, but `Get-AzContext` still has the previous value. Similarly, you can run `Set-AzContext -Subscription xxx` and it will change the output from `Get-AzContext` but not the output from `az account show`.

So if you have a script where you change Azure CLI's context and then you run an Azure PowerShell command, it looks at PowerShell's context (which hasn't changed) so it doesn't produce the answer you want.

You can fix this by either: run `Set-AzContext` as well before running the cmdlet; or running the az equivalent of your cmdlet, which will honor the az context.

You may be thinking "That's crazy! Why do it that way? You should change it so both share a context!" I don't personally know the history of why these 2 are separate. But to be honest I doubt that it can be changed at this point without causing a lot of backward-compatibility issues for people who accidentally or deliberately rely on the current behavior.

