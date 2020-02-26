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
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 8700421551af227f158abaa38d5f96c8e2987ba3
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603407"
---
# <a name="supported-features-available-in-azure-security-center"></a>Ondersteunde functies die beschikbaar zijn in Azure Security Center

> [!NOTE]
>Sommige functies zijn alleen beschikbaar in de laag standaard. Als u zich nog niet hebt aangemeld voor de Standard-laag van Security Center, is een gratis proef periode beschikbaar. Zie de pagina met prijzen voor [Security Center](https://azure.microsoft.com/pricing/details/security-center/)voor meer informatie.

De volgende secties tonen Security Center functies die beschikbaar zijn voor hun [ondersteunde platforms](security-center-os-coverage.md).

* [Virtuele machines/servers](#vm-server-features)
* [PaaS Services](#paas-services)


## Ondersteunde functies van de virtuele machine/server<a name="vm-server-features"></a>

### <a name="windows"></a>[Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Niet-Azure-machines**|**Prijzen**
|[Micro soft Defender ATP-integratie](security-center-wdatp.md)|✔ (op ondersteunde versies)|✔ (op ondersteunde versies)|✔|Standard|
|[Waarschuwingen voor het detecteren van bedreigingen voor virtuele machines](security-center-alerts-iaas.md)|✔|✔|✔|Detectie van bedreigingen (gratis) (standaard)|
|[Waarschuwingen voor detectie van bedreigingen met een bestand](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Waarschuwingen voor detectie van bedreigingen op basis van het netwerk](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Standard|
|[Just-in-time-VM-toegang](security-center-just-in-time.md)|✔|-|-|Standard|
|[Beoordeling van systeem eigen beveiligings problemen](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Bestands integriteit controleren](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Adaptieve toepassingsbesturingselementen](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Netwerk toewijzing](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptieve netwerk beveiliging](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Adaptieve netwerk besturings elementen|✔|✔|-|Standard|
|[Dash board naleving van regelgeving & rapporten](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Aanbevelingen en detectie van bedreigingen op docker-gehoste IaaS-containers|-|-|-|Standard|
|Evaluatie van besturingssysteem patches ontbreekt|✔|✔|✔|Gratis|
|Evaluatie van onjuiste beveiligings configuratie|✔|✔|✔|Gratis|
|[Endpoint Protection-evaluatie](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Gratis|
|Analyse van schijf versleuteling|✔|✔|-|Gratis|
|Beoordeling van beveiligings lekken van derden|✔|-|-|Gratis|
|[Evaluatie van netwerk beveiliging](security-center-network-recommendations.md)|✔|✔|-|Gratis|


### <a name="linux"></a>[Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Niet-Azure-machines**|**Prijzen**
|[Micro soft Defender ATP-integratie](security-center-wdatp.md)|-|-|-|Standard|
|[Waarschuwingen voor het detecteren van bedreigingen voor virtuele machines](security-center-alerts-iaas.md)|✔ (op ondersteunde versies)|✔ (op ondersteunde versies)|✔|Detectie van bedreigingen (gratis) (standaard)|
|[Waarschuwingen voor detectie van bedreigingen met een bestand](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Waarschuwingen voor detectie van bedreigingen op basis van het netwerk](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Standard|
|[Just-in-time-VM-toegang](security-center-just-in-time.md)|✔|-|-|Standard|
|[Beoordeling van systeem eigen beveiligings problemen](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Bestands integriteit controleren](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Adaptieve toepassingsbesturingselementen](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Netwerk toewijzing](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptieve netwerk beveiliging](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Adaptieve netwerk besturings elementen|✔|✔|-|Standard|
|[Dash board naleving van regelgeving & rapporten](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Aanbevelingen en detectie van bedreigingen op docker-gehoste IaaS-containers|✔|✔|✔|Standard|
|Evaluatie van besturingssysteem patches ontbreekt|✔|✔|✔|Gratis|
|Evaluatie van onjuiste beveiligings configuratie|✔|✔|✔|Gratis|
|[Endpoint Protection-evaluatie](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Gratis|
|Analyse van schijf versleuteling|✔|✔|-|Gratis|
|Beoordeling van beveiligings lekken van derden|✔|-|-|Gratis|
|[Evaluatie van netwerk beveiliging](security-center-network-recommendations.md)|✔|✔|-|Gratis|

--- 

## Ondersteunde Endpoint Protection-oplossingen<a name="endpoint-supported"></a>

De volgende tabel bevat een matrix met:

 - Hiermee wordt aangegeven of u Azure Security Center kunt gebruiken om elke oplossing voor u te installeren.
 - Welke Endpoint Protection-oplossingen Security Center kunnen detecteren. Als er een Endpoint Protection-oplossing in deze lijst wordt gedetecteerd, wordt Security Center niet aanbevolen een installatie uit te voeren.

Zie [Endpoint Protection-evaluatie en aanbevelingen](security-center-endpoint-protection.md)voor meer informatie over wanneer er aanbevelingen worden gegenereerd voor elk van deze beveiligings maatregelen.

| Eindpuntbeveiliging| Platformen | Security Center-installatie | Security Center Discovery |
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
> - Voor de detectie van System Center Endpoint Protection (SCEP) op een virtuele machine met Windows Server 2008 R2 moet SCEP worden geïnstalleerd na Power Shell 3,0 (of een hogere versie).
> - Detectie van Trend Micro bescherming wordt ondersteund voor diepe beveiligings agenten.  OfficeScan-agents worden niet ondersteund.


## Ondersteunde functies <a name="paas-services"></a> van PaaS Services

De volgende PaaS-bronnen worden ondersteund door Azure Security Center:

|Service|Aanbevelingen (gratis)|Waarschuwingen voor detectie van bedreigingen (standaard)|Evaluatie van beveiligings problemen (standaard)|
|----|:----:|:----:|:----:|
|SQL Databases|✔|✔|✔|
|Azure Container Registry|-|-|✔|
|Azure Kubernetes Service|✔|✔|-|
|Azure Database for PostgreSQL *|✔|✔|-|
|Azure Database for MySQL *|✔|✔|-|
|Azure CosmosDB *|-|✔|-|
|Opslagaccounts|✔|-|-|
|Blob Storage|✔|✔|-|
|App Service|✔|✔|-|
|Function App|✔|-|-|
|Cloud Services|✔|-|-|
|Virtueel netwerk|✔|-|-|
|Subnet|✔|-|-|
|NIC|✔|-|-|
|Netwerkbeveiligingsgroepen|✔|-|-|
|Abonnement|✔ **|✔|-|
|Batch-account|✔|-|-|
|Service Fabric-account|✔|-|-|
|Automation-account|✔|-|-|
|Load balancer|✔|-|-|
|Cognitive Search|✔|-|-|
|Service Bus-naamruimte|✔|-|-|
|Stream Analytics|✔|-|-|
|Event hub-naamruimte|✔|-|-|
|Logic apps|✔|-|-|
|Cache voor redis|✔|-|-|
|Data Lake Analytics|✔|-|-|
|Azure Data Lake Storage|✔|-|-|
|Key Vault|✔|✔ *|-|

\* deze functies worden momenteel ondersteund in de preview-versie.

\*-aanbevelingen voor \* Azure Active Directory (Azure AD) zijn alleen beschikbaar voor standaard abonnementen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [Security Center verzamelen van gegevens en de log Analytics-agent](security-center-enable-data-collection.md).
- Meer informatie over het [Security Center beheren en beveiligen van gegevens](security-center-data-security.md).
- Meer informatie over [het plannen en begrijpen van de ontwerp overwegingen bij het aannemen van Azure Security Center](security-center-planning-and-operations-guide.md).
- Bekijk de [platforms die Security Center ondersteunen](security-center-os-coverage.md).
- Meer informatie over het [detecteren van bedreigingen voor vm's & servers in azure Security Center](security-center-alerts-iaas.md).
- Vind [Veelgestelde vragen over Azure Security Center](faq-general.md).
- Vind [blog berichten over de beveiliging en naleving van Azure](https://blogs.msdn.com/b/azuresecurity/).