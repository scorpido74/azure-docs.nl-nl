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
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 9d3fa1e0b62ea6f4762c3df6ac7da310d5703807
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245237"
---
# <a name="feature-coverage-for-machines"></a>Functie dekking voor machines

De onderstaande tabellen bevatten Azure Security Center functies die beschikbaar zijn voor virtuele machines en servers.

## Ondersteunde functies voor virtuele machines en servers<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Windows-computers](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Functie**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Niet-Azure-machines**|**Prijzen**
|[Micro soft Defender ATP-integratie](security-center-wdatp.md)|✔</br>(op ondersteunde versies)|✔</br>(op ondersteunde versies)|✔|Standard|
|[Gedrags analyse van virtuele machine (en beveiligings waarschuwingen)](threat-protection.md)|✔|✔|✔|Aanbevelingen (gratis) </br></br> Beveiligings waarschuwingen (standaard)|
|[Beveiligings waarschuwingen met een bestand](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Beveiligings waarschuwingen op basis van het netwerk](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Just-in-time-VM-toegang](security-center-just-in-time.md)|✔|-|-|Standard|
|[Beoordeling van systeem eigen beveiligings problemen](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Bestands integriteit controleren](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Adaptieve toepassingsbesturingselementen](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Netwerk toewijzing](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptieve netwerk beveiliging](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Adaptieve netwerk besturings elementen|✔|✔|-|Standard|
|[Dash board naleving van regelgeving & rapporten](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Aanbevelingen en beveiliging tegen bedreigingen op docker-gehoste IaaS-containers|-|-|-|Standard|
|Evaluatie van besturingssysteem patches ontbreekt|✔|✔|✔|Gratis|
|Evaluatie van onjuiste beveiligings configuratie|✔|✔|✔|Gratis|
|[Endpoint Protection-evaluatie](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Gratis|
|Analyse van schijf versleuteling|✔|✔|-|Gratis|
|Beoordeling van beveiligings lekken van derden|✔|-|-|Gratis|
|[Evaluatie van netwerk beveiliging](security-center-network-recommendations.md)|✔|✔|-|Gratis|


### <a name="linux-machines"></a>[Linux-machines](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Functie**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Niet-Azure-machines**|**Prijzen**
|[Micro soft Defender ATP-integratie](security-center-wdatp.md)|-|-|-|Standard|
|[Gedrags analyse van virtuele machine (en beveiligings waarschuwingen)](security-center-alerts-iaas.md)|✔</br>(op ondersteunde versies)|✔</br>(op ondersteunde versies)|✔|Aanbevelingen (gratis) </br></br> Beveiligings waarschuwingen (standaard)|
|[Beveiligings waarschuwingen met een bestand](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Beveiligings waarschuwingen op basis van het netwerk](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Just-in-time-VM-toegang](security-center-just-in-time.md)|✔|-|-|Standard|
|[Beoordeling van systeem eigen beveiligings problemen](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Bestands integriteit controleren](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Adaptieve toepassingsbesturingselementen](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Netwerk toewijzing](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptieve netwerk beveiliging](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Adaptieve netwerk besturings elementen|✔|✔|-|Standard|
|[Dash board naleving van regelgeving & rapporten](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Aanbevelingen en beveiliging tegen bedreigingen op docker-gehoste IaaS-containers|✔|✔|✔|Standard|
|Evaluatie van besturingssysteem patches ontbreekt|✔|✔|✔|Gratis|
|Evaluatie van onjuiste beveiligings configuratie|✔|✔|✔|Gratis|
|[Endpoint Protection-evaluatie](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Gratis|
|Analyse van schijf versleuteling|✔|✔|-|Gratis|
|Beoordeling van beveiligings lekken van derden|✔|-|-|Gratis|
|[Evaluatie van netwerk beveiliging](security-center-network-recommendations.md)|✔|✔|-|Gratis|

--- 


> [!TIP]
>Als u wilt experimenteren met functies die alleen beschikbaar zijn in de prijs categorie Standard, kunnen gebruikers met een gratis laag een proef versie van 30 dagen registreren. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.


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


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [Security Center verzamelen van gegevens en de log Analytics-agent](security-center-enable-data-collection.md).
- Meer informatie over het [Security Center beheren en beveiligen van gegevens](security-center-data-security.md).
- Meer informatie over [het plannen en begrijpen van de ontwerp overwegingen bij het aannemen van Azure Security Center](security-center-planning-and-operations-guide.md).
- Bekijk de [platforms die Security Center ondersteunen](security-center-os-coverage.md).
- Meer informatie over [beveiliging tegen bedreigingen voor Windows-en Linux-machines in azure Security Center](threat-protection.md#windows-machines).
- Vind [Veelgestelde vragen over Azure Security Center](faq-general.md).