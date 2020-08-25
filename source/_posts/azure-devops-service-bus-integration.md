---
title: Azure DevOps and Azure Service Bus Integration
date: 2020-06-05 22:27:31
tags:
- azure-devops
- azure-service-bus
- integration
- connectionstring
---

The documentation about Azure DevOps and Azure Service Bus integration is a bit blurry, but when connecting a service bus as a service hook to Azure DevOps, copy the SAS (Shared Access Token) connection string from [Azure Portal](https://portal.azure.com) and paste it without the `EntityPath` and `;` to the connection string of Azure DevOps to make it work.

This is the connection string on Azure Portal:

`Endpoint=sb://selcuk.servicebus.windows.net/;SharedAccessKeyName=ado;SharedAccessKey=FAKE=SAS=4FjStU5nBWH4F0PF/HXjgGQMA=;EntityPath=myQueue`

This what should be pasted on Azure DevOps:

`Endpoint=sb://selcuk.servicebus.windows.net/;SharedAccessKeyName=ado;SharedAccessKey=FAKE=SAS=4FjStU5nBWH4F0PF/HXjgGQMA=`

[<- Back to all TILs](../../../05/19/til/)
