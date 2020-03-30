---
title: Azure VMware Solution by CloudSimple - Workload DNS en DHCP instellen voor Private Cloud
description: Beschrijft hoe u DNS en DHCP instelt voor toepassingen en workloads die worden uitgevoerd in uw CloudSimple Private Cloud-omgeving
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ef1266b783034cf18dc2b3ea4be5ebc01bc12c70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024684"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>DNS- en DHCP-toepassingen en -workloads instellen in uw CloudSimple Private Cloud

Toepassingen en workloads die worden uitgevoerd in een Private Cloud-omgeving vereisen naamomzetting en DHCP-services voor opzoeking en IP-adrestoewijzing.  Een goede DHCP- en DNS-infrastructuur is vereist om deze services te kunnen leveren.  U een virtuele machine configureren om deze services te leveren in uw Private Cloud-omgeving.  

## <a name="prerequisites"></a>Vereisten

* Een gedistribueerde poortgroep met VLAN geconfigureerd
* Route-instelling naar on-premises of op internet gebaseerde DNS-servers
* Sjabloon voor virtuele machines of ISO om een virtuele machine te maken

## <a name="linux-based-dns-server-setup"></a>Dns-serversetup op Linux

Linux biedt verschillende pakketten voor het opzetten van DNS-servers.  Hier volgt een [voorbeeld van DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) met instructies voor het instellen van een open-source BIND DNS-server.

## <a name="windows-based-setup"></a>Windows-installatie

In deze Microsoft-onderwerpen wordt beschreven hoe u een Windows-server instelt als DNS-server en als DHCP-server.

* [Windows Server als DNS-server](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server als DHCP-server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
