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
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: f5be0b43de8265b2b337c024117ae8f424e4a3bc
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403483"
---
# <a name="feature-coverage-for-machines"></a>Functie dekking voor machines

De twee tabbladen hieronder tonen de functies van Azure Security Center die beschikbaar zijn voor virtuele Windows-en Linux-machines en-servers.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Ondersteunde functies voor virtuele machines en servers <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows-machines**](#tab/features-windows)

|**Functie**|**Azure Virtual Machines**|**Virtuele Azure-machineschaalsets**|**Niet-Azure-machines**|**Azure Defender vereist**
|----|:----:|:----:|:----:|:----:|
|[Micro soft Defender ATP-integratie](security-center-wdatp.md)|✔</br>(op ondersteunde versies)|✔</br>(op ondersteunde versies)|✔|Ja|
|[Gedrags analyse van virtuele machine (en beveiligings waarschuwingen)](alerts-reference.md)|✔|✔|✔|Ja|
|[Beveiligings waarschuwingen met een bestand](alerts-reference.md#alerts-windows)|✔|✔|✔|Ja|
|[Beveiligings waarschuwingen op basis van het netwerk](other-threat-protections.md#network-layer)|✔|✔|-|Ja|
|[Just-in-time-toegang voor virtuele machines](security-center-just-in-time.md)|✔|-|-|Ja|
|[Beoordeling van systeem eigen beveiligings problemen](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Ja|
|[Bestandsintegriteit controleren](security-center-file-integrity-monitoring.md)|✔|✔|✔|Ja|
|[Adaptieve toepassingsbesturingselementen](security-center-adaptive-application.md)|✔|-|✔|Ja|
|[Netwerkoverzicht](security-center-network-recommendations.md#network-map)|✔|✔|-|Ja|
|[Adaptieve netwerkbeveiliging](security-center-adaptive-network-hardening.md)|✔|-|-|Ja|
|[Dash board naleving van regelgeving & rapporten](security-center-compliance-dashboard.md)|✔|✔|✔|Ja|
|Aanbevelingen en beveiliging tegen bedreigingen op docker-gehoste IaaS-containers|-|-|-|Ja|
|Evaluatie van besturingssysteem patches ontbreekt|✔|✔|✔|Azure: Nee<br><br>Niet-Azure: Ja|
|Evaluatie van onjuiste beveiligings configuratie|✔|✔|✔|Azure: Nee<br><br>Niet-Azure: Ja|
|[Endpoint Protection-evaluatie](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: Nee<br><br>Niet-Azure: Ja|
|Analyse van schijf versleuteling|✔</br>(voor [ondersteunde scenario's](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Nee|
|Beoordeling van beveiligings lekken van derden|✔|-|-|Nee|
|[Evaluatie van netwerk beveiliging](security-center-network-recommendations.md)|✔|✔|-|Nee|


### <a name="linux-machines"></a>[**Linux-machines**](#tab/features-linux)

|**Functie**|**Azure Virtual Machines**|**Virtuele Azure-machineschaalsets**|**Niet-Azure-machines**|**Azure Defender vereist**
|----|:----:|:----:|:----:|:----:|
|[Micro soft Defender ATP-integratie](security-center-wdatp.md)|-|-|-|Ja|
|[Gedrags analyse van virtuele machine (en beveiligings waarschuwingen)](security-center-alerts-iaas.md)|✔</br>(op ondersteunde versies)|✔</br>(op ondersteunde versies)|✔|Ja|
|[Beveiligings waarschuwingen met een bestand](alerts-reference.md#alerts-windows)|-|-|-|Ja|
|[Beveiligings waarschuwingen op basis van het netwerk](other-threat-protections.md#network-layer)|✔|✔|-|Ja|
|[Just-in-time-toegang voor virtuele machines](security-center-just-in-time.md)|✔|-|-|Ja|
|[Beoordeling van systeem eigen beveiligings problemen](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Ja|
|[Bestandsintegriteit controleren](security-center-file-integrity-monitoring.md)|✔|✔|✔|Ja|
|[Adaptieve toepassingsbesturingselementen](security-center-adaptive-application.md)|✔|-|✔|Ja|
|[Netwerkoverzicht](security-center-network-recommendations.md#network-map)|✔|✔|-|Ja|
|[Adaptieve netwerkbeveiliging](security-center-adaptive-network-hardening.md)|✔|-|-|Ja|
|[Dash board naleving van regelgeving & rapporten](security-center-compliance-dashboard.md)|✔|✔|✔|Ja|
|Aanbevelingen en beveiliging tegen bedreigingen op docker-gehoste IaaS-containers|✔|✔|✔|Ja|
|Evaluatie van besturingssysteem patches ontbreekt|✔|✔|✔|Azure: Nee<br><br>Niet-Azure: Ja|
|Evaluatie van onjuiste beveiligings configuratie|✔|✔|✔|Azure: Nee<br><br>Niet-Azure: Ja|
|[Endpoint Protection-evaluatie](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Nee|
|Analyse van schijf versleuteling|✔</br>(voor [ondersteunde scenario's](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Nee|
|Beoordeling van beveiligings lekken van derden|✔|-|-|Nee|
|[Evaluatie van netwerk beveiliging](security-center-network-recommendations.md)|✔|✔|-|Nee|

--- 


> [!TIP]
>Als u wilt experimenteren met functies die alleen beschikbaar zijn met Azure Defender, kunt u zich inschrijven voor een proef versie van 30 dagen. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/)voor meer informatie.


## <a name="supported-endpoint-protection-solutions"></a>Ondersteunde Endpoint Protection-oplossingen <a name="endpoint-supported"></a>

De volgende tabel bevat een matrix met:

 - Hiermee wordt aangegeven of u Azure Security Center kunt gebruiken om elke oplossing voor u te installeren.
 - Welke Endpoint Protection-oplossingen Security Center kunnen detecteren. Als er een Endpoint Protection-oplossing in deze lijst wordt gedetecteerd, wordt Security Center niet aanbevolen een installatie uit te voeren.

Zie [Endpoint Protection-evaluatie en aanbevelingen](security-center-endpoint-protection.md)voor meer informatie over wanneer er aanbevelingen worden gegenereerd voor elk van deze beveiligings maatregelen.

| Endpoint Protection| Platformen | Security Center-installatie | Security Center Discovery |
|------|------|-----|-----|
| Microsoft Defender Antivirus| Windows Server 2016 of hoger| Nee, ingebouwd in besturingssysteem| Ja |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (zie opmerking hieronder) | Via extensie | Ja |
| Trend Micro – diepe beveiliging | Windows Server-familie  | Nee | Ja |
| Symantec v12.1.1100+| Windows Server-familie  | Nee | Ja |
| McAfee v10+ | Windows Server-familie  | Nee | Ja |
| McAfee v10+ | Linux-Server familie  | Nee | Klikt **\*** |
| Sophos v9 +| Linux-Server familie  | Nee | Klikt  **\***  |

 **\*** De status van de dekking en de ondersteunende gegevens zijn momenteel alleen beschikbaar in de werk ruimte Log Analytics die aan uw beveiligde abonnementen is gekoppeld. Het wordt niet weer gegeven in de Azure Security Center Portal.

> [!NOTE]
> Voor de detectie van System Center Endpoint Protection (SCEP) op een virtuele machine met Windows Server 2008 R2 moet SCEP worden geïnstalleerd na Power shell (v 3.0 of nieuwer).


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [Security Center verzamelen van gegevens en de log Analytics-agent](security-center-enable-data-collection.md).
- Meer informatie over het [Security Center beheren en beveiligen van gegevens](security-center-data-security.md).
- Bekijk de [platforms die Security Center ondersteunen](security-center-os-coverage.md).