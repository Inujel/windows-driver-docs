---
title: Installing the Windows Sockets Switch
description: Installing the Windows Sockets Switch
ms.assetid: 69cdec9f-8ed7-48d7-ae6d-a9a9916e3c58
keywords:
- Windows Sockets Direct WDK , installing components
- layered service providers WDK SANs
ms.author: windowsdriverdev
ms.date: 04/20/2017
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-devices
---

# Installing the Windows Sockets Switch


## <a href="" id="ddk-installing-the-windows-sockets-switch-ng"></a>


Microsoft Windows installs the Windows Sockets switch as a *layered service provider*, with the Windows Sockets service provider interface (SPI) as both the top and bottom interface. The switch exports the protocol characteristics of TCP/IP just like the TCP/IP service provider. The switch is the first visible TCP/IP provider, which makes the switch the default choice for applications that open sockets for the [WSK address families](https://msdn.microsoft.com/library/windows/hardware/ff571151).

 

 





