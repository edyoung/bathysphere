+++
date = 2020-09-22T17:11:06-07:00
draft = false
slug = "azurearc-cheatsheet"
author = "Edwin Young"
tags = ["AzureArc","azure","azcli","PowerShell"]
description = "Examples of how to do basic things using the az cli and Azure PowerShell modules for Azure Arc-enabled Servers"
title = "Azure Arc command-line tools cheat sheet"
type = "post"
image = "orbiter.jpg"
+++

Arc for Servers has an Azure CLI extension and PowerShell module. Because they are prerelease, they are not included (yet) in the main releases 
(ie, you don't get this just by installing az cli or the Az module). 
However they are already functional and useful. Here's how to do some simple operations with each of them. 

## Install

AZ CLI:
```bash
az extension add connectedmachine
```

Azure PowerShell:
```powershell
Install-Module -Name Az.ConnectedMachine
```

## List machines

AZ CLI
```bash 
az connectedmachine list

# only display a few properties in a convenient form
az connectedmachine list --query "[].{Name:name,ResourceGroup:resourceGroup,Location:location,Status:status}" -o table
```

Azure PowerShell:
```powershell
Get-AzConnectedMachine
```

## List extensions installed on a machine

AZ CLI
```bash
az connectedmachine extension list --resource-group edyoung --machine-name edwin-virtual-machine
```

Azure PowerShell:
```powershell
Get-AzConnectedMachineExtension -ResourceGroupName edyoung -MachineName edwin-virtual-machine
```

## Install Custom Script Extension on a windows machine and run 'dir'

Note that installing extensions currently takes several minutes. Please be patient.

AZ CLI run in bash
```bash
# Note --location needs to be the location of the machine
az connectedmachine extension create --machine-name silverbox-ne --resource-group edyoung --name myext --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings '{"commandToExecute":"dir"}' --location "North Europe"
```

AZ CLI run inside PowerShell (the escaping is different for settings param)
```powershell
az connectedmachine extension create --machine-name silverbox-ne --resource-group edyoung --name myext --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings '{\"commandToExecute\":\"dir\"}' --location "North Europe"
```

Azure PowerShell
```powershell
 New-AzConnectedMachineExtension -MachineName silverbox-ne -ResourceGroupName edyoung -Location "North Europe" -Name myext -Setting '{"commandToExecute":"dir"}' -ExtensionType CustomScriptExtension -Publisher Microsoft.Compute
```

## Delete an extension

AZ CLI
```bash
az connectedmachine extension delete --name myext -g edyoung --machine-name silverbox-ne
```

Azure PowerShell
```powershell
Remove-AzConnectedMachineExtension -MachineName silverbox-ne -ResourceGroupName edyoung -Name myext
```