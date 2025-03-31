+++
author = "Edwin Young"
categories = ["Azure", "Arc"]
date = "2022-03-26"
description = "Azure Arc-Enabled Servers and Managed Identity"
#featured = ""
#featuredalt = ""
#featuredpath = ""
#linktitle = ""
title = "Azure Arc-Enabled Servers and Managed Identity"
type = "post"
draft = "true"
+++

# Securing Managed Identity on Azure Arc-Enabled Servers

One of the main technical facilities in [Azure Arc-Enabled Servers]() is the ability to have a System-Assigned Managed Identity for your server. I think of this as the server's AAD account. The server itself has an identity which can be granted permissions to interact with other resources in Azure. For example, maybe your on-premise web servers need to be able to access a KeyVault or a SQL instance in the cloud

https://docs.microsoft.com/en-us/azure/azure-arc/servers/managed-identity-authentication#acquiring-an-access-token-using-rest-api
