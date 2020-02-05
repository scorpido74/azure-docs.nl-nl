---
title: 'Azure VMware-oplossingen (AVS): Stel de werk belasting DNS en DHCP in voor de Privécloud van de AVS'
description: Hierin wordt beschreven hoe u DNS en DHCP instelt voor toepassingen en werk belastingen die worden uitgevoerd in de cloud omgeving van uw AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2a5cae868f2d8f3689f05dd9d466715ab2008a3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024684"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-avs-private-cloud"></a>DNS-en DHCP-toepassingen en werk belastingen instellen in uw AVS-Privécloud

Voor toepassingen en werk belastingen die worden uitgevoerd in een cloud omgeving in de AVS, zijn naam omzetting en DHCP-services vereist voor de toewijzing van lookup-en IP-adressen. U hebt de juiste DHCP-en DNS-infra structuur nodig om deze services te kunnen leveren. U kunt een virtuele machine configureren om deze services te bieden in de Privécloud van uw cloud omgeving. 

## <a name="prerequisites"></a>Vereisten

* Een gedistribueerde poort groep waarvoor VLAN is geconfigureerd
* De installatie naar on-premises of op internet gebaseerde DNS-servers routeren
* Virtuele-machine sjabloon of ISO voor het maken van een virtuele machine

## <a name="linux-based-dns-server-setup"></a>Installatie van DNS-server op basis van Linux

Linux biedt verschillende pakketten voor het instellen van DNS-servers. Hier volgt een [voor beeld van een installatie van DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) met instructies voor het instellen van een open-source BIND-DNS-server.

## <a name="windows-based-setup"></a>Installatie op basis van Windows

In deze micro soft-onderwerpen wordt beschreven hoe u een Windows-Server als een DNS-server en als een DHCP-server instelt.

* [Windows Server als DNS-server](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server als DHCP-server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
