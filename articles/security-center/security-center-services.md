---
title: Ondersteunde functies beschikbaar in Azure Security Center | Microsoft Documenten
description: Dit document bevat een lijst met services die worden ondersteund door Azure Security Center.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245237"
---
# <a name="feature-coverage-for-machines"></a>Functiedekking voor machines

In de onderstaande tabellen worden azure security center-functies weergegeven die beschikbaar zijn voor virtuele machines en servers.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Ondersteunde functies voor virtuele machines en servers<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Windows-machines](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Functie**|**Azure Virtuele machines**|**Azure Virtual Machine Scale Sets**|**Niet-Azure-machines**|**Prijzen**
|[Microsoft Defender ATP-integratie](security-center-wdatp.md)|✔</br>(op ondersteunde versies)|✔</br>(op ondersteunde versies)|✔|Standard|
|[Virtual Machine Behavioral Analytics (en beveiligingswaarschuwingen)](threat-protection.md)|✔|✔|✔|Aanbevelingen (gratis) </br></br> Beveiligingswaarschuwingen (standaard)|
|[Fileless beveiligingswaarschuwingen](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Netwerkgebaseerde beveiligingswaarschuwingen](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Just-In-Time VM-toegang](security-center-just-in-time.md)|✔|-|-|Standard|
|[Native kwetsbaarheidsbeoordeling](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Controle op bestandsintegriteit](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Adaptieve toepassingsbesturingselementen](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Netwerkkaart](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptieve netwerkverharding](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Adaptieve netwerkbesturingselementen|✔|✔|-|Standard|
|[Dashboard voor naleving van regelgeving & rapporten](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Aanbevelingen en bescherming tegen bedreigingen op door Docker gehoste IaaS-containers|-|-|-|Standard|
|Beoordeling ontbrekende OS-patches|✔|✔|✔|Gratis|
|Beoordeling van beveiligingsverkeerde configuraties|✔|✔|✔|Gratis|
|[Beoordeling van de bescherming van eindpunten](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Gratis|
|Beoordeling van schijfversleuteling|✔|✔|-|Gratis|
|Kwetsbaarheidsbeoordeling door derden|✔|-|-|Gratis|
|[Beoordeling van de netwerkbeveiliging](security-center-network-recommendations.md)|✔|✔|-|Gratis|


### <a name="linux-machines"></a>[Linux machines](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Functie**|**Azure Virtuele machines**|**Azure Virtual Machine Scale Sets**|**Niet-Azure-machines**|**Prijzen**
|[Microsoft Defender ATP-integratie](security-center-wdatp.md)|-|-|-|Standard|
|[Virtual Machine Behavioral Analytics (en beveiligingswaarschuwingen)](security-center-alerts-iaas.md)|✔</br>(op ondersteunde versies)|✔</br>(op ondersteunde versies)|✔|Aanbevelingen (gratis) </br></br> Beveiligingswaarschuwingen (standaard)|
|[Fileless beveiligingswaarschuwingen](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Netwerkgebaseerde beveiligingswaarschuwingen](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Just-In-Time VM-toegang](security-center-just-in-time.md)|✔|-|-|Standard|
|[Native kwetsbaarheidsbeoordeling](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Controle op bestandsintegriteit](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Adaptieve toepassingsbesturingselementen](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Netwerkkaart](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptieve netwerkverharding](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Adaptieve netwerkbesturingselementen|✔|✔|-|Standard|
|[Dashboard voor naleving van regelgeving & rapporten](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Aanbevelingen en bescherming tegen bedreigingen op door Docker gehoste IaaS-containers|✔|✔|✔|Standard|
|Beoordeling ontbrekende OS-patches|✔|✔|✔|Gratis|
|Beoordeling van beveiligingsverkeerde configuraties|✔|✔|✔|Gratis|
|[Beoordeling van de bescherming van eindpunten](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Gratis|
|Beoordeling van schijfversleuteling|✔|✔|-|Gratis|
|Kwetsbaarheidsbeoordeling door derden|✔|-|-|Gratis|
|[Beoordeling van de netwerkbeveiliging](security-center-network-recommendations.md)|✔|✔|-|Gratis|

--- 


> [!TIP]
>Om te experimenteren met functies die alleen beschikbaar zijn op de standaardprijslaag, kunnen gebruikers van gratis lagen zich inschrijven voor een proefperiode van 30 dagen. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.


## <a name="supported-endpoint-protection-solutions"></a>Ondersteunde oplossingen voor endpointbescherming<a name="endpoint-supported"></a>

De volgende tabel bevat een matrix van:

 - Of u Azure Security Center gebruiken om elke oplossing voor u te installeren.
 - Welke endpoint protection oplossingen Security Center kan ontdekken. Als een oplossing voor endpointbeveiliging uit deze lijst wordt ontdekt, raadt Security Center het installeren ervan af.

Zie [Beoordeling van endpointbeveiliging en aanbevelingen](security-center-endpoint-protection.md)voor informatie over wanneer aanbevelingen voor elk van deze beveiligingen worden gegenereerd.

| Eindpuntbeveiliging| Platformen | Security Center-installatie | Security Center Discovery |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Nee, ingebouwd in besturingssysteem| Ja |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (zie noot hieronder) | Via extensie | Ja |
| Trend Micro – Alle versies* | Windows Server-familie  | Nee | Ja |
| Symantec v12.1.1100+| Windows Server-familie  | Nee | Ja |
| McAfee v10+ | Windows Server-familie  | Nee | Ja |
| McAfee v10+ | Linux Server-familie  | Nee | Ja**\*** |
| Sophos V9+| Linux Server-familie  | Nee | Ja**\***  |

 **\*** De dekkingsstatus en ondersteunende gegevens zijn momenteel alleen beschikbaar in de werkruimte Log Analytics die is gekoppeld aan uw beveiligde abonnementen. Dit wordt niet weergegeven in de Azure Security Center-portal.

> [!NOTE]
> - Detectie van System Center Endpoint Protection (SCEP) op een virtuele windows server 2008 R2 vereist dat SCEP wordt geïnstalleerd na PowerShell 3.0 (of een bovenste versie).
> - Detectie van Trend Micro-beveiliging wordt ondersteund voor Deep Security-agents.  OfficeScan-agents worden niet ondersteund.


## <a name="next-steps"></a>Volgende stappen

- Ontdek hoe [Security Center gegevens verzamelt en de Log Analytics Agent](security-center-enable-data-collection.md).
- Ontdek hoe [Security Center gegevens beheert en beschermt.](security-center-data-security.md)
- Meer informatie over het [plannen en begrijpen van de ontwerpoverwegingen voor de goedkeuring van Azure Security Center.](security-center-planning-and-operations-guide.md)
- Bekijk de [platforms die beveiligingscentrum ondersteunen.](security-center-os-coverage.md)
- Meer informatie over [bedreigingsbeveiliging voor Windows- en Linux-machines in Azure Security Center](threat-protection.md#windows-machines).
- Veelgestelde [vragen over Azure Security Center vinden.](faq-general.md)