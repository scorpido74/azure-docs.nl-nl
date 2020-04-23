---
title: Belangrijkste concepten voor het beheer van Azure VMware-oplossing door CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Beschrijft de belangrijkste concepten voor het beheer van Azure VMware-oplossingen door CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 410215550f728d6b11b02b3ef108d429481e42a0
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869033"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Belangrijkste concepten voor het beheer van Azure VMware-oplossingen door CloudSimple

Voor het beheer van Azure VMware-oplossingen door CloudSimple is inzicht nodig in de volgende concepten:

* CloudSimple-service, die wordt weergegeven als Azure VMware Solutions by CloudSimple - Service
* CloudSimple-knooppunt, dat wordt weergegeven als Azure VMware-oplossingen door CloudSimple - Knooppunt
* CloudSimple private cloud
* Servicenetwerken
* CloudSimple virtuele machine, die wordt weergegeven als Azure VMware Solutions door CloudSimple - Virtuele machine

## <a name="cloudsimple-service"></a>CloudSimple-service

Met de CloudSimple-service u alle resources die zijn gekoppeld aan VMware Solutions by CloudSimple maken en beheren vanuit de Azure-portal. Maak een servicebron in elke regio waar u de service wilt gebruiken.

Meer informatie over de [CloudSimple-service](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>CloudSimple-knooppunt

Een CloudSimple-knooppunt is een speciale, bare-metal, hyperconverged compute- en storagehost waarin de VMware ESXi hypervisor wordt ingezet. Dit knooppunt wordt vervolgens opgenomen in de VMware vSphere-, vCenter-, vSAN- en NSX-platforms. CloudSimple-netwerkservices en edge-netwerkservices zijn ook ingeschakeld. Elk knooppunt dient als een eenheid van reken- en opslagcapaciteit die u inrichten om [CloudSimple-privéclouds te](cloudsimple-private-cloud.md)maken. U indient of reserveert knooppunten in een regio waar de CloudSimple-service beschikbaar is.

Meer informatie over [CloudSimple-knooppunten](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>CloudSimple private cloud

Een CloudSimple private cloud is een geïsoleerde VMware stack omgeving beheerd door een vCenter server in zijn eigen beheerdomein. De VMware-stack bevat ESXi-hosts, vSphere, vCenter, vSAN en NSX. De stack draait op speciale knooppunten (dedicated en geïsoleerde bare-metal hardware) en wordt verbruikt door gebruikers via native VMware-tools die vCenter en NSX Manager bevatten. Speciale knooppunten worden geïmplementeerd in Azure-locaties en worden beheerd door Azure. Elke private cloud kan worden gesegmenteerd en beveiligd met behulp van netwerkservices zoals VLAN's en subnetten en firewalltabellen. Verbindingen met uw on-premises omgeving en het Azure-netwerk worden gemaakt met behulp van beveiligde, privé VPN- en Azure ExpressRoute-verbindingen.

Meer informatie over [CloudSimple private cloud](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Servicenetwerken

De CloudSimple-service biedt een netwerk per regio waar uw CloudSimple-service wordt geïmplementeerd. Het netwerk is één TCP Layer 3-adresruimte met standaard routering ingeschakeld. Alle privéclouds en subnetten die in deze regio zijn gemaakt, communiceren met elkaar zonder extra configuratie. U maakt gedistribueerde poortgroepen op het vCenter met behulp van de VLAN's. U de volgende netwerkfuncties gebruiken om uw workloadbronnen in uw privécloud te configureren en te beveiligen:

* [VLAN's en subnetten](cloudsimple-vlans-subnets.md)
* [Firewalltabellen](cloudsimple-firewall-tables.md)
* [VPN-gateways](cloudsimple-vpn-gateways.md)
* [Openbaar IP](cloudsimple-public-ip-address.md)
* [Azure-netwerkverbinding](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple virtuele machine

Met de CloudSimple-service u virtuele VMware-machines beheren vanuit de Azure-portal. Een of meer clusters of resourcepools uit uw vSphere-omgeving kunnen worden toegewezen aan het abonnement waarop de service is gemaakt.

Meer informatie over:

* [CloudSimple virtuele machines](cloudsimple-virtual-machines.md)
* [Azure-abonnementstoewijzing](https://docs.microsoft.com/azure/vmware-cloudsimple/azure-subscription-mapping/)
