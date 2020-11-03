---
title: Netwerken en connectiviteit met Azure VMWare Solution
description: Beschrijving van netwerken en connectiviteit met Azure VMWare Solution.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 47b5fab8430f4e462f1e4a0e8cb6ddb69ec38c02
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92925016"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution biedt een omgeving met een privécloud die toegankelijk is vanuit on-premises en op Azure gebaseerde omgevingen of resources. Services zoals Azure ExpressRoute en VPN-verbindingen verzorgen de connectiviteit. Voor het inschakelen van deze services zijn specifieke bereiken van netwerkadressen en firewallpoorten vereist.

Wanneer u een privécloud implementeert, worden er privénetwerken gemaakt voor beheer, inrichting en vMotion. Gebruik deze privénetwerken om toegang te krijgen tot vCenter en NSX-T Manager en vMotion of implementatie op virtuele machines.  ExpressRoute Global Reach wordt gebruikt om privéclouds te verbinden met on-premises omgevingen. Voor de verbinding is een virtueel netwerk met een ExpressRoute-circuit in uw abonnement vereist.

Resources, zoals webservers en virtuele machines, zijn toegankelijk voor internet via de openbare IP-functionaliteit van Azure Virtual WAN.  Internettoegang is standaard uitgeschakeld voor nieuwe privéclouds. Zie [Hoe gebruikt u de openbare IP-functionaliteit in Azure VMware Solution](../public-ip-usage.md) voor meer informatie.