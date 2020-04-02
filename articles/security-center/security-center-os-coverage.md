---
title: Platforms die worden ondersteund door Azure Security Center | Microsoft Documenten
description: Dit document bevat een lijst met platforms die worden ondersteund door Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 3c8bf69b745f5dba8c08556908df4d4ae5b5769f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521922"
---
# <a name="supported-platforms"></a>Ondersteunde platforms 

Op deze pagina worden de platforms en omgevingen weergegeven die worden ondersteund door Azure Security Center.

## <a name="combinations-of-environments"></a>Combinaties van omgevingen<a name="vm-server"></a>

Azure Security Center ondersteunt virtuele machines en servers op verschillende typen hybride omgevingen:

* Alleen Azure
* Azure en on-premises
* Azure en andere clouds
* Azure, andere clouds en on-premises

Voor een Azure-omgeving die is geactiveerd op een Azure-abonnement, detecteert Azure Security Center automatisch IaaS-resources die binnen het abonnement zijn geïmplementeerd.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Security Center is afhankelijk van de [Log Analytics Agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent). Zorg ervoor dat uw machines een van de ondersteunde besturingssystemen voor deze agent draaien, zoals beschreven op de volgende pagina's:

* [Log Analytics-agent voor door Windows ondersteunde besturingssystemen](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)
* [Log Analytics-agent voor door Linux ondersteunde besturingssystemen](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

Zorg er ook voor dat uw Log Analytics-agent goed is [geconfigureerd om gegevens naar security center te verzenden](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> Zie [Functiedekking voor machines voor](security-center-services.md)meer informatie over de specifieke functies van het Beveiligingscentrum die beschikbaar zijn op Windows en Linux.

## <a name="managed-virtual-machine-services"></a>Beheerde virtuele machineservices<a name="virtual-machine"></a>

Virtuele machines worden ook gemaakt in een klantabonnement als onderdeel van sommige azure-beheerde services, zoals Azure Kubernetes (AKS), Azure Databricks en meer. Security Center detecteert deze virtuele machines ook en de Log Analytics-agent kan worden geïnstalleerd en geconfigureerd als er een ondersteund besturingssysteem beschikbaar is.

## <a name="cloud-services"></a>Cloudservices<a name="cloud-services"></a>

Virtuele machines die in een cloudservice worden uitgevoerd, worden ook ondersteund. Alleen web- en werknemersrollen voor cloudservices die in productieslots worden uitgevoerd, worden gecontroleerd. Zie Overzicht van Azure [Cloud Services](../cloud-services/cloud-services-choose-me.md)voor meer informatie over cloudservices.

Beveiliging van VM's die in Azure Stack wonen, wordt ook ondersteund. Zie Uw [virtuele Azure Stack-apparaten ingebruikmaken voor](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)meer informatie over de integratie van Security Center met Azure Stack.

## <a name="next-steps"></a>Volgende stappen

- Ontdek hoe [Security Center gegevens verzamelt met behulp van de Log Analytics Agent](security-center-enable-data-collection.md).
- Ontdek hoe [Security Center gegevens beheert en beschermt.](security-center-data-security.md)
- Meer informatie over het [plannen en begrijpen van de ontwerpoverwegingen voor de goedkeuring van Azure Security Center.](security-center-planning-and-operations-guide.md)