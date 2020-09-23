---
title: Platforms die worden ondersteund door Azure Security Center | Microsoft Docs
description: Dit document bevat een lijst met platformen die door Azure Security Center worden ondersteund.
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
ms.openlocfilehash: 2092a1aa3d5157db0392397e86553c5cc9da9de2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90883790"
---
# <a name="supported-platforms"></a>Ondersteunde platforms 

Op deze pagina worden de platforms en omgevingen weer gegeven die door Azure Security Center worden ondersteund.

## <a name="combinations-of-environments"></a>Combi Naties van omgevingen <a name="vm-server"></a>

Azure Security Center biedt ondersteuning voor virtuele machines en servers in verschillende typen hybride omgevingen:

* Alleen Azure
* Azure en on-premises
* Azure en andere Clouds
* Azure, andere Clouds en on-premises

Voor een Azure-omgeving die wordt geactiveerd op een Azure-abonnement, detecteert Azure Security Center automatisch IaaS-resources die in het abonnement zijn geïmplementeerd.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Security Center is afhankelijk van de [log Analytics-agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent). Zorg ervoor dat op de computers een van de ondersteunde besturings systemen voor deze agent wordt uitgevoerd, zoals wordt beschreven op de volgende pagina's:

* [Log Analytics-agent voor door Windows ondersteunde besturings systemen](../azure-monitor/platform/agents-overview.md#supported-operating-systems)
* [Besturings systemen die worden ondersteund door Log Analytics-agent voor Linux](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

Zorg er ook voor dat uw Log Analytics-agent [correct is geconfigureerd voor het verzenden van gegevens naar Security Center](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> Zie [functie dekking voor machines voor](security-center-services.md)meer informatie over de specifieke Security Center functies die beschikbaar zijn in Windows en Linux.

## <a name="managed-virtual-machine-services"></a>Beheerde services voor virtuele machines <a name="virtual-machine"></a>

Virtuele machines worden ook in een klanten abonnement gemaakt als onderdeel van sommige door Azure beheerde services, zoals Azure Kubernetes (AKS), Azure Databricks en meer. Security Center detecteert deze virtuele machines ook en de Log Analytics agent kan worden geïnstalleerd en geconfigureerd als een ondersteund besturings systeem beschikbaar is.

## <a name="cloud-services"></a>Cloud Services <a name="cloud-services"></a>

Virtuele machines die worden uitgevoerd in een Cloud service, worden ook ondersteund. Alleen Cloud Services-Web-en-werk rollen die worden uitgevoerd in productie-sleuven worden bewaakt. Zie [overzicht van Azure Cloud Services](../cloud-services/cloud-services-choose-me.md)voor meer informatie over Cloud Services.

De beveiliging van virtuele machines in Azure Stack wordt ook ondersteund. Voor meer informatie over de integratie van Security Center met Azure Stack raadpleegt u [de Azure stack virtuele machines Onboarden om te Security Center](quickstart-onboard-machines.md). 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [Security Center verzamelen van gegevens met behulp van de log Analytics-agent](security-center-enable-data-collection.md).
- Meer informatie over het [Security Center beheren en beveiligen van gegevens](security-center-data-security.md).
- Meer informatie over [het plannen en begrijpen van de ontwerp overwegingen bij het aannemen van Azure Security Center](security-center-planning-and-operations-guide.md).