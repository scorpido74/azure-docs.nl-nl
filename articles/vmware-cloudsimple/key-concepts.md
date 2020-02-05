---
title: Belangrijkste concepten voor het beheer van Azure VMware-oplossingen (AVS)
description: Hierin worden de belangrijkste concepten voor het beheer van Azure VMware-oplossingen (AVS) beschreven
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c581ffbba7fbaefc8fbd0ffc7e92350bd69802b8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025228"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-avs"></a>Belangrijkste concepten voor het beheer van Azure VMware-oplossingen (AVS)

Voor het beheren van de Azure VMware-oplossingen (AVS) is een goed idee van de volgende concepten vereist:

* AVS-service, die wordt weer gegeven als Azure VMware-oplossingen (AVS)-service
* Het AVS-knoop punt, dat wordt weer gegeven als Azure VMware-oplossingen (AVS)-knoop punt
* Privécloud van de automatische AVS
* Service netwerken
* Virtuele machine van AVS, die wordt weer gegeven als Azure VMware-oplossingen (AVS)-virtuele machine

## <a name="avs-service"></a>AVS-service

Met de AVS-service kunt u alle resources maken en beheren die zijn gekoppeld aan VMware-oplossingen (AVS) van de Azure Portal. Maak een service resource in elke regio waar u de service wilt gebruiken.

Meer informatie over de [AVS-service](cloudsimple-service.md).

## <a name="avs-node"></a>AVS-knoop punt

Een AVS-knoop punt is een toegewezen, Bare-Metal, hypergeconvergeerd Compute-en opslag host waarop de VMware ESXi Hyper Visor wordt geïmplementeerd. Dit knoop punt wordt vervolgens opgenomen in de VMware vSphere-, vCenter-, vSAN-en NSX-platforms. AVS-netwerk services en Edge-netwerk services zijn ook ingeschakeld. Elk knoop punt fungeert als een eenheid voor reken-en opslag capaciteit die u kunt inrichten voor het maken van een automatische [AVS-Cloud](cloudsimple-private-cloud.md). U kunt knoop punten inrichten of reserveren in een regio waarin de AVS-service beschikbaar is.

Meer informatie over [AVS-knoop punten](cloudsimple-node.md).

## <a name="avs-private-cloud"></a>Privécloud van de automatische AVS

Een AVS-privécloud is een geïsoleerde VMware-stack omgeving die wordt beheerd door een vCenter-Server in een eigen beheer domein. De VMware-stack bevat ESXi-hosts, vSphere, vCenter, vSAN en NSX. De stack wordt uitgevoerd op toegewezen knoop punten (toegewezen en geïsoleerde bare-metal hardware) en wordt door gebruikers gebruikt via systeem eigen VMware-hulpprogram ma's die vCenter en NSX manager bevatten. Toegewezen knoop punten worden geïmplementeerd op Azure-locaties en worden beheerd door Azure. Elke AVS Privécloud kan worden gesegmenteerd en beveiligd met behulp van netwerk services, zoals VLAN'S en subnetten en firewall tabellen. Verbindingen met uw on-premises omgeving en het Azure-netwerk worden gemaakt met behulp van beveiligde, particuliere VPN-en Azure ExpressRoute-verbindingen.

Meer informatie over de privécloud van de [AVS](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Service netwerken

De AVS-service biedt een netwerk per regio waarin uw AVS-service is geïmplementeerd. Het netwerk is één adres ruimte van de TCP-laag 3 waarvoor route ring standaard is ingeschakeld. Alle nieuwe AVS-Clouds en-subnetten die in deze regio worden gemaakt, communiceren zonder aanvullende configuratie. U maakt gedistribueerde poort groepen op de vCenter met behulp van de VLAN'S. U kunt de volgende netwerk functies gebruiken om uw werkbelasting resources te configureren en beveiligen in de Privécloud van uw AVS:

* [VLAN'S en subnetten](cloudsimple-vlans-subnets.md)
* [Firewall tabellen](cloudsimple-firewall-tables.md)
* [VPN-gateways](cloudsimple-vpn-gateways.md)
* [Openbare IP](cloudsimple-public-ip-address.md)
* [Azure-netwerk verbinding](cloudsimple-azure-network-connection.md)

## <a name="avs-virtual-machine"></a>Virtuele machine voor AVS

Met de AVS-service kunt u virtuele VMware-machines beheren vanuit de Azure Portal. Een of meer clusters of resource groepen uit uw vSphere-omgeving kunnen worden toegewezen aan het abonnement waarop de service is gemaakt.

Meer informatie over:

* [Virtuele machines van AVS](cloudsimple-virtual-machines.md)
* [Toewijzing van Azure-abonnement](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
