---
title: Functies en platformen die worden ondersteund door Azure Security Center | Microsoft Docs
description: Dit document bevat een lijst met functies en platformen die door Azure Security Center worden ondersteund.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/03/2019
ms.author: v-mohabe
ms.openlocfilehash: 1f5a144728614ee13bf4f08bb594f0546d446880
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640790"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Platforms en functies die door Azure Security Center worden ondersteund

Bewaking van beveiligings status en aanbevelingen zijn beschikbaar voor virtuele machines (Vm's) die zijn gemaakt met behulp van de klassieke en Resource Manager-implementatie modellen en computers.

> [!NOTE]
> Meer informatie over het [klassieke en Resource Manager-implementatie model](../azure-classic-rm.md) voor Azure-resources.
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Platforms die ondersteuning bieden voor de gegevensverzamelings agent 
In deze sectie vindt u de platforms waarop de Log Analytics-agent, die door Azure Security Center wordt gebruikt, kan worden uitgevoerd.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Ondersteunde platforms voor Windows-computers en Vm's
De volgende Windows-besturings systemen worden ondersteund:

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> Integratie met Windows Defender ATP ondersteunt alleen Windows Server 2012 R2 en Windows Server 2016.

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Ondersteunde platforms voor Linux-computers en Vm's

De volgende Linux-besturings systemen worden ondersteund:

> [!NOTE]
> Omdat de lijst met ondersteunde Linux-besturings systemen voortdurend wordt gewijzigd, klikt u [hier](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) om de meest recente lijst met ondersteunde versies weer te geven, in het geval er wijzigingen zijn sinds dit onderwerp voor het laatst is gepubliceerd.

64-bits
* CentOS 6 en 7
* Amazon Linux 2017,09
* Oracle Linux 6 en 7
* Red Hat Enterprise Linux Server 6 en 7
* Debian GNU/Linux 8 en 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS en 18,04 LTS
* SUSE Linux Enterprise Server 12

32-bits
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 en 9
* Ubuntu Linux 14,04 LTS en 16,04 LTS

## <a name="vms-and-cloud-services"></a>Vm's en Cloud Services
Virtuele machines die worden uitgevoerd in een Cloud service worden ook ondersteund. Alleen Cloud Services-Web-en-werk rollen die worden uitgevoerd in productie-sleuven worden bewaakt. Zie [overzicht van Azure Cloud Services](../cloud-services/cloud-services-choose-me.md)voor meer informatie over Cloud Services.


## <a name="supported-iaas-features"></a>Ondersteunde IaaS-functies

> [!div class="mx-tableFixed"]
> 

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

### <a name="supported-endpoint-protection-solutions"></a>Ondersteunde Endpoint Protection-oplossingen

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
> - Voor de detectie van System Center Endpoint Protection (SCEP) op een virtuele machine met Windows Server 2008 R2 moet SCEP worden geïnstalleerd na Power Shell 3,0 (of een hogere versie).
> - Detectie van Trend Micro bescherming wordt ondersteund voor diepe beveiligings agenten.  OfficeScan-agents worden niet ondersteund.

## <a name="supported-paas-features"></a>Ondersteunde PaaS-functies


|Service|Aanbevelingen (gratis)|Detectie van bedreigingen (standaard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL|✔| ✔|
|MySQL|✔| ✔|
|Azure Blob-opslag accounts|✔| ✔|
|App-services|✔| ✔|
|Cloud Services|✔| X|
|VNets|✔| N.v.t.|
|Subnetten|✔| N.v.t.|
|NIC’s|✔| N.v.t.|
|NSG's|✔| N.v.t.|
|Subscription|✔ **| ✔|
|Batch|✔| N.v.t.|
|Service Fabric|✔| N.v.t.|
|Automation-account|✔| N.v.t.|
|Load balancer|✔| N.v.t.|
|Search|✔| N.v.t.|
|Service Bus|✔| N.v.t.|
|Stream Analytics|✔| N.v.t.|
|Event Hub|✔| N.v.t.|
|Logic Apps|✔| N.v.t.|
|Storage-account|✔| N.v.t.|
|Redis|✔| N.v.t.|
|Data Lake Analytics|✔| N.v.t.|
|Sleutelkluis|✔| N.v.t.|




\*Deze functies worden momenteel ondersteund in de open bare preview-versie.

\*\*AAD-aanbevelingen zijn alleen beschikbaar voor standaard abonnementen



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het plannen en begrijpen van de ontwerp overwegingen bij het aannemen van Azure Security Center](security-center-planning-and-operations-guide.md).
- Meer informatie over het [detecteren van bedreigingen voor vm's & servers in azure Security Center](security-center-alerts-iaas.md).
- Vind [Veelgestelde vragen over het gebruik van Azure Security Center](security-center-faq.md).
- Vind [blog berichten over de beveiliging en naleving van Azure](https://blogs.msdn.com/b/azuresecurity/).
