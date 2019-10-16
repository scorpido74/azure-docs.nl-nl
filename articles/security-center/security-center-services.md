---
title: Ondersteunde functies die beschikbaar zijn in Azure Security Center | Microsoft Docs
description: Dit document bevat een lijst met services die door Azure Security Center worden ondersteund.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: b6de3a520150a95316371c4454fd2537f2beab5d
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331149"
---
# <a name="supported-features-available-in-azure-security-center"></a>Ondersteunde functies die beschikbaar zijn in Azure Security Center

> [!NOTE]
>Sommige functies zijn alleen beschikbaar in de laag standaard. Als u zich nog niet hebt aangemeld voor de Standard-laag van Security Center, is een gratis proef periode beschikbaar. Zie de pagina met prijzen voor [Security Center](https://azure.microsoft.com/pricing/details/security-center/)voor meer informatie.

De volgende secties tonen Security Center functies die beschikbaar zijn voor hun [ondersteunde platforms](security-center-os-coverage.md).

* [Virtuele machines/servers](#vm-server-features)
* [PaaS Services](#paas-services)


## Ondersteunde functies van de virtuele machine/server<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Server|Windows|||Linux|||Prijzen|
|----|----|----|----|----|----|----|----|
|**Omgeving**|**Azure**||**Niet-Azure**|**Azure**||**Niet-Azure**||
||**Virtuele machine**|**Schaalset voor virtuele machines**||**Virtuele machine**|**Schaalset voor virtuele machines**|
|[Micro soft Defender ATP-integratie](https://docs.microsoft.com/azure/security-center/security-center-wdatp)|✔ (op ondersteunde versies)|✔ (op ondersteunde versies)|✔|X|X|X|Standard|
|[Waarschuwingen voor het detecteren van bedreigingen voor virtuele machines](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas)|✔|✔|✔|✔ (op ondersteunde versies)|✔ (op ondersteunde versies)|✔|Detectie van bedreigingen (gratis) (standaard)|
|[Waarschuwingen voor detectie van bedreigingen met een bestand](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas#fileless-attack-detection-)|✔|✔|✔|X|X|X|Standard|
|[Waarschuwingen voor detectie van bedreigingen op basis van het netwerk](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer#azure-network-layer)|✔|✔|X|✔|✔|X|Standard|
|[Just-in-time-VM-toegang](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|✔|X|X|✔|X|X|Standard|
|[Bestands integriteit controleren](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)|✔|✔|✔|✔|✔|✔|Standard|
|[Adaptieve toepassingsbesturingselementen](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)|✔|X|✔|✔|X|✔|Standard|
|[Netwerk toewijzing](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations#network-map)|✔|✔|X|✔|✔|X|Standard|
|[Adaptieve netwerk beveiliging](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)|✔|X|X|✔|X|X|Standard|
|Adaptieve netwerk besturings elementen|✔|✔|X|✔|✔|X|Standard|
|[Dash board naleving van regelgeving & rapporten](https://docs.microsoft.com/azure/security-center/security-center-compliance-dashboard)|✔|✔|✔|✔|✔|✔|Standard|
|Aanbevelingen en detectie van bedreigingen op docker-gehoste IaaS-containers|X|X|X|✔|✔|✔|Standard|
|Evaluatie van besturingssysteem patches ontbreekt|✔|✔|✔|✔|✔|✔|Gratis|
|Evaluatie van onjuiste beveiligings configuratie|✔|✔|✔|✔|✔|✔|Gratis|
|[Endpoint Protection-evaluatie](https://docs.microsoft.com/azure/security-center/security-center-services#supported-endpoint-protection-solutions-)|✔|✔|✔|X|X|X|Gratis|
|Analyse van schijf versleuteling|✔|✔|X|✔|✔|X|Gratis|
|Beoordeling van beveiligings lekken van derden|✔|X|X|✔|X|X|Gratis|
|[Evaluatie van netwerk beveiliging](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)|✔|✔|X|✔|✔|X|Gratis|

### Ondersteunde Endpoint Protection-oplossingen<a name="endpoint-supported"></a>

De volgende tabel bevat een matrix met:

 - Hiermee wordt aangegeven of u Azure Security Center kunt gebruiken om elke oplossing voor u te installeren.
 - Welke Endpoint Protection-oplossingen Security Center kunnen detecteren. Als er een Endpoint Protection-oplossing in deze lijst wordt gedetecteerd, wordt Security Center niet aanbevolen een installatie uit te voeren.

Zie [Endpoint Protection-evaluatie en aanbevelingen](security-center-endpoint-protection.md)voor meer informatie over wanneer er aanbevelingen worden gegenereerd voor elk van deze beveiligings maatregelen.

| Endpoint Protection| Platformen | Security Center-installatie | Security Center Discovery |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Nee, ingebouwd in besturingssysteem| Ja |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (zie opmerking hieronder) | Via extensie | Ja |
| Trend Micro: alle versies * | Windows Server-familie  | Nee | Ja |
| Symantec v12.1.1100+| Windows Server-familie  | Nee | Ja |
| McAfee v10+ | Windows Server-familie  | Nee | Ja |
| McAfee v10+ | Linux-Server familie  | Nee | Ja **\*** |
| Sophos v9 +| Linux-Server familie  | Nee | Ja **\***  |

 **\*** De status van de dekking en de ondersteunende gegevens zijn momenteel alleen beschikbaar in de werk ruimte Log Analytics die aan uw beveiligde abonnementen is gekoppeld. Het wordt niet weer gegeven in de Azure Security Center Portal.

> [!NOTE]
>
> - Voor de detectie van System Center Endpoint Protection (SCEP) op een virtuele machine met Windows Server 2008 R2 moet SCEP worden geïnstalleerd na Power Shell 3,0 (of een hogere versie).
> - Detectie van Trend Micro bescherming wordt ondersteund voor diepe beveiligings agenten.  OfficeScan-agents worden niet ondersteund.


## Ondersteunde functies <a name="paas-services"></a> van PaaS Services

De volgende PaaS-bronnen worden ondersteund door Azure Security Center:

|Service|Aanbevelingen (gratis)|Detectie van bedreigingen (standaard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL|✔| ✔|
|MySQL|✔| ✔|
|CosmosDB|X| ✔|
|Blob-opslag|✔| ✔|
|Opslagaccount|✔| N.V.T.|
|App service|✔| ✔|
|Functie|✔| X|
|Cloudservice|✔| X|
|VNet|✔| N.V.T.|
|Subnet|✔| N.V.T.|
|NIC|✔| N.V.T.|
|NSG|✔| N.V.T.|
|Abonnement|✔ * *| ✔|
|Batch-account|✔| X|
|Service Fabric-account|✔| X|
|Automation-account|✔| X|
|Load Balancer|✔| X|
|Search|✔| X|
|Service Bus-naam ruimte|✔| X|
|Stream Analytics|✔| X|
|Event hub-naamruimte|✔| X|
|Logische apps|✔| X|
|Redis|✔| N.V.T.|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|Key Vault|✔| X|

\* deze functies worden momenteel ondersteund in de open bare preview-versie.

de aanbevelingen voor \* @ no__t-1-Azure Active Directory (Azure AD) zijn alleen beschikbaar voor standaard abonnementen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [Security Center verzamelen van gegevens en de log Analytics-agent](security-center-enable-data-collection.md).
- Meer informatie over het [Security Center beheren en beveiligen van gegevens](security-center-data-security.md).
- Meer informatie over [het plannen en begrijpen van de ontwerp overwegingen bij het aannemen van Azure Security Center](security-center-planning-and-operations-guide.md).
- Bekijk de [platforms die Security Center ondersteunen](security-center-os-coverage.md).
- Meer informatie over het [detecteren van bedreigingen voor vm's & servers in azure Security Center](security-center-alerts-iaas.md).
- Vind [Veelgestelde vragen over het gebruik van Azure Security Center](security-center-faq.md).
- Vind [blog berichten over de beveiliging en naleving van Azure](https://blogs.msdn.com/b/azuresecurity/).
