---
title: Netwerken en connectiviteit met Azure VMWare Solution
description: Beschrijving van netwerken en connectiviteit met Azure VMWare Solution.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 1d43a8a49fc56a6a5b534a89fd1dfc2f5ae2d478
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91574450"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution biedt een omgeving met een privécloud die toegankelijk is vanuit on-premises en op Azure gebaseerde omgevingen of resources. Services zoals Azure ExpressRoute en VPN-verbindingen verzorgen de connectiviteit. Voor het inschakelen van deze services zijn specifieke bereiken van netwerkadressen en firewallpoorten vereist.

Wanneer u een privécloud implementeert, worden er privénetwerken gemaakt voor beheer, inrichting en vMotion. Gebruik deze privénetwerken om toegang te krijgen tot vCenter en NSX-T Manager en vMotion of implementatie op virtuele machines.  ExpressRoute Global Reach wordt gebruikt om privéclouds te verbinden met on-premises omgevingen. Voor de verbinding is een virtueel netwerk met een ExpressRoute-circuit in uw abonnement vereist.



>[!NOTE]
>Toegang tot internet en Azure-services wordt ingericht en aangeboden om VM's in productienetwerken te gebruiken bij het implementeren van een privécloud.  Internettoegang is standaard uitgeschakeld voor nieuwe privéclouds en kan op elk gewenst moment worden ingeschakeld of uitgeschakeld.