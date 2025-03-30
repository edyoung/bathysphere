+++
author = "Edwin Young"
categories = ["Azure", "CloudShell"]
date = "2021-03-19"
description = "Describes how authentication works inside Azure Cloud Shell"
#featured = ""
#featuredalt = ""
#featuredpath = ""
#linktitle = ""
title = "Azure Cloud Shell, az login, and Managed Identity"
type = "post"
+++

When you open Azure Cloud Shell and run `Get-AzVM` or `az vm list` for the first time, it works right away. But why?

That may seem like an odd question. Of course it should work right away! But it doesn't work quite the same way when you run PowerShell or Azure CLI locally - you need to run `Connect-AzAccount` or `az login` first. So what's the difference?

A surprising amount of things are happening behind the scenes for this to work, and at the time of writing there are some limitations you might hit. This post tries to explain what is going on. If you just want to use Cloud Shell you don't really need to know any of this.

## OAuth

Within Azure, basically everything is authenticated using **OAuth**. That's a complicated beast I can't explain here in detail. The docs [here](https://docs.microsoft.com/en-us/azure/active-directory/develop/authentication-vs-authorization) provide a reasonable starting point. For our purposes, the important thing is that you authenticate yourself first to Azure Active Directory when you open the Azure Portal. AAD checks your password/PIN+smart card/whatever and issues a **refresh token**. This gets stored outside our scope in the browser - Cloud Shell never accesses it directly. 

When you need to access a particular resource, you need an **access token**. Given the refresh token, you ask AAD 'hey, I want to access Azure Storage, can I have a token?'. AAD then sends you a short-lived token (usually lasting 1 hour) which can then be presented to Azure Storage; storage checks the signature on the token and uses that to check if you are legit.

But as I mentioned, in Cloud Shell we don't have your refresh token, so how does this work?

## Managed Identity

**Managed Identity** is a cool scheme invented so that code which needs to access a resource doesn't need to store (and worry about the security and rotation of) a password. The Azure fabric manages an identity and is configured so that any code that runs on a particular VM can ask an endpoint "Can I have an access token?". I think of this as an ambient credential for the VM - we grant that machine access to resources and allow code running on it to get tokens without any extra hassle.

This works by having a special endpoint. If you're on an Azure VM with a managed identity, you can run

`curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true`

and get a token, which is just a JSON blob looking a bit like this:

```json
{
  "accessToken": "eyJ0blah...",
  "expiresOn": "2021-03-19 23:12:01.371124",
  "subscription": "b1071e00-5da5-49c8-b902-14951e12f37a",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "tokenType": "Bearer"
}
```

## Managed Identity in Cloud Shell

In Cloud Shell you still want an 'ambient' identity - you don't want to sign on *again*. But you don't want a token that identifies you as the machine where Cloud Shell runs, you want one based on your own identity that you signed on to the portal with.

So we provide an alternative managed identity endpoint. 

```bash
curl 'http://localhost:50342/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true
```

Will give you an access token based on your own identity. Unlike Azure's regular managed identity endpoint, this is implemented as another process running inside your container. 
When it gets a request, it actually sends the request back to your browser, which forwards it to the parent Portal frame, which makes a request to AAD, and the resulting token 
flows back down the same path.

## Tool Considerations
Client tools deal with this endpoint in different ways.

Azure PowerShell supports `Connect-AzAccount -Identity` which tells it to use managed identity (whether Cloud Shell or regular); AZ CLI has `az login --identity` for the same purpose. We run both during the Cloud Shell startup so you don't have to. 

You can also login explicitly, by running `az login` or `Connect-AzAccount`. That overrides this mechanism and can be used to (for example) use a different identity than the one you logged on to Cloud Shell with.

## Limitations and Issues

This works great most of the time, but there are a couple of snags you might run into. 

1. We have an allow-list of resources that we can provide tokens for (a limitation we hope to raise in future). This means that when someone introduces a brand new resource type for a new service, we have to update the list in Cloud Shell too. If you run a command and see something like this: `"error":{"code":"AudienceNotSupported","message":"Audience https://coolnewservice.azure.com/ is not a supported MSI token audience...."}"` you have hit this issue. Please feel free to file an issue at https://github.com/Azure/CloudShell/issues and we'll endeavor to add it as soon as we can. 

1. If you login explicitly (`az login`) any Conditional Access rules your company may have in place will be evaluated based on the Cloud Shell container rather than the machine where your browser runs. The Cloud Shell container doesn't count as a managed device for these policies so rights may be limited by the policy.





