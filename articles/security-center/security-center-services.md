---
title: Ondersteunde functies die beschikbaar zijn in Azure Security Center | Microsoft Docs
description: Dit document bevat een lijst met services die door Azure Security Center worden ondersteund.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: v-mohabe
ms.openlocfilehash: 0d2b417dd01e26f0f93722be66d82972b52aca83
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70234580"
---
# <a name="supported-features-available-in-azure-security-center"></a>Ondersteunde functies die beschikbaar zijn in Azure Security Center

> [!NOTE]
>Sommige functies zijn alleen beschikbaar in de laag standaard. Als u zich nog niet hebt aangemeld voor de Standard-laag van Security Center, is een gratis proef periode beschikbaar. Zie de [pagina met prijzen van Security Center](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

De volgende secties tonen Security Center functies die beschikbaar zijn voor hun [ondersteunde platforms](security-center-os-coverage.md).

* [Virtuele machines/servers](#vm-server-features)
* [PaaS Services](#paas-services)


## Ondersteunde functies van de virtuele machine/server<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Server|Windows|||Linux|||Prijzen|
|----|----|----|----|----|----|----|----|
|**Omgeving**|**Azure**||**Non-Azure**|**Azure**||**Non-Azure**||
||**Virtuele machine**|**Schaalset voor virtuele machines**||**Virtuele machine**|**Schaalset voor virtuele machines**|
|Waarschuwingen voor detectie van VMBA-bedreigingen|✔|✔|✔|✔ (op ondersteunde versies)|✔ (op ondersteunde versies)|✔|Detectie van bedreigingen (gratis) (standaard)|
|Waarschuwingen voor detectie van bedreigingen op basis van het netwerk|✔|✔|X|✔|✔|X|Standard|
|Windows Defender ATP-integratie|✔ (op ondersteunde versies)|✔ (op ondersteunde versies)|✔|X|X|X|Standard|
|Ontbrekende patches|✔|✔|✔|✔|✔|✔|Free|
|Beveiligingsconfiguraties|✔|✔|✔|✔|✔|✔|Free|
|Endpoint Protection-evaluatie|✔|✔|✔|X|X|X|Free|
|JIT-VM-toegang|✔|X|X|✔|X|X|Standard|
|Besturingselementen voor adaptieve toepassingen|✔|X|✔|✔|X|✔|Standard|
|FIM|✔|✔|✔|✔|✔|✔|Standard|
|Analyse van schijf versleuteling|✔|✔|X|✔|✔|X|Free|
|Implementatie van derden|✔|X|X|✔|X|X|Free|
|NSG-evaluatie|✔|✔|X|✔|✔|X|Free|
|Detectie van bedreigingen met een bestand|✔|✔|✔|X|X|X|Standard|
|Netwerk toewijzing|✔|✔|X|✔|✔|X|Standard|
|Adaptieve netwerk besturings elementen|✔|✔|X|✔|✔|X|Standard|
|Dash board naleving van regelgeving & rapporten|✔|✔|✔|✔|✔|✔|Standard|
|Aanbevelingen en detectie van bedreigingen op docker-gehoste IaaS-containers|X|X|X|✔|✔|✔|Standard|

### Ondersteunde Endpoint Protection-oplossingen<a name="endpoint-supported"></a>

De volgende tabel bevat een matrix met:

 - Hiermee wordt aangegeven of u Azure Security Center kunt gebruiken om elke oplossing voor u te installeren.
 - Welke Endpoint Protection-oplossingen Security Center kunnen detecteren. Als een van deze Endpoint Protection-oplossingen wordt gedetecteerd, wordt door Security Center niet aanbevolen een installatie te installeren.

Zie [Endpoint Protection-evaluatie en aanbevelingen](security-center-endpoint-protection.md)voor meer informatie over wanneer er aanbevelingen worden gegenereerd voor elk van deze beveiligings maatregelen.

| Endpoint Protection| Platforms | Security Center-installatie | Security Center Discovery |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Nee, ingebouwd in besturingssysteem| Ja |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (zie opmerking hieronder) | Via extensie | Ja |
| Trend Micro: alle versies * | Windows Server-familie  | Nee | Ja |
| Symantec v12.1.1100+| Windows Server-familie  | Nee | Ja |
| McAfee v10+ | Windows Server-familie  | Nee | Ja |
| McAfee v10+ | Linux-Server familie  | Nee | Klikt **\*** |
| Sophos v9 +| Linux-Server familie  | Nee | Klikt **\***  |

 **\*** De status van de dekking en de ondersteunende gegevens zijn momenteel alleen beschikbaar in de werk ruimte Log Analytics die aan uw beveiligde abonnementen is gekoppeld, en wordt niet weer gegeven in Azure Security Center Portal.

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
|Blob Storage|✔| ✔|
|Storage-account|✔| N.v.t.|
|App Service|✔| ✔|
|Function|✔| X|
|Cloudservice|✔| X|
|VNet|✔| N.v.t.|
|Subnet|✔| N.v.t.|
|NIC|✔| N.v.t.|
|NSG|✔| N.v.t.|
|Subscription|✔ **| ✔|
|Batch-account|✔| X|
|Service Fabric-account|✔| X|
|Automation-account|✔| X|
|Load balancer|✔| X|
|Search|✔| X|
|Service Bus-naamruimte|✔| X|
|Stream Analytics|✔| X|
|Event Hub-naamruimte|✔| X|
|Logic Apps|✔| X|
|Redis|✔| N.v.t.|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|Sleutelkluis|✔| X|

\*Deze functies worden momenteel ondersteund in de open bare preview-versie.

\*\*Aanbevelingen voor Azure Active Directory (Azure AD) zijn alleen beschikbaar voor standaard abonnementen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [Security Center verzamelen van gegevens en de log Analytics-agent](security-center-enable-data-collection.md).
- Meer informatie over het [Security Center beheren en beveiligen van gegevens](security-center-data-security.md).
- Meer informatie over [het plannen en begrijpen van de ontwerp overwegingen bij het aannemen van Azure Security Center](security-center-planning-and-operations-guide.md).
- Bekijk de [platforms die Security Center ondersteunen](security-center-os-coverage.md).
- Meer informatie over het [detecteren van bedreigingen voor vm's & servers in azure Security Center](security-center-alerts-iaas.md).
- Vind [Veelgestelde vragen over het gebruik van Azure Security Center](security-center-faq.md).
- Vind [blog berichten over de beveiliging en naleving van Azure](https://blogs.msdn.com/b/azuresecurity/).
