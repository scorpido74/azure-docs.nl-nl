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
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 61e5c60317bf872b8e27a7bd2edea7247e01a3b8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91439415"
---
# <a name="feature-coverage-for-machines"></a>Functiedekking voor machines

De twee tabbladen hieronder tonen de functies van Azure Security Center die beschikbaar zijn voor virtuele Windows- en Linux-machines en -servers.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Ondersteunde functies voor virtuele machines en servers <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows-machines**](#tab/features-windows)

|**Functie**|**Azure Virtual Machines**|**Virtuele Azure-machineschaalsets**|**Niet-Azure-machines**|**Azure Defender vereist**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP-integratie](security-center-wdatp.md)|✔</br>(in ondersteunde versies)|✔</br>(in ondersteunde versies)|✔|Ja|
|[Gedragsanalyse van virtuele machine (en beveiligingswaarschuwingen)](alerts-reference.md)|✔|✔|✔|Ja|
|[Bestandsloze beveiligingswaarschuwingen](alerts-reference.md#alerts-windows)|✔|✔|✔|Ja|
|[Op netwerk gebaseerde beveiligingswaarschuwingen](other-threat-protections.md#network-layer)|✔|✔|-|Ja|
|[Just-in-time-toegang voor virtuele machines](security-center-just-in-time.md)|✔|-|-|Ja|
|[Systeemeigen evaluatie van beveiligingsproblemen](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Ja|
|[Bestandsintegriteit controleren](security-center-file-integrity-monitoring.md)|✔|✔|✔|Ja|
|[Adaptieve toepassingsbesturingselementen](security-center-adaptive-application.md)|✔|-|✔|Ja|
|[Netwerkoverzicht](security-center-network-recommendations.md#network-map)|✔|✔|-|Ja|
|[Adaptieve netwerkbeveiliging](security-center-adaptive-network-hardening.md)|✔|-|-|Ja|
|[Dashboard en rapporten voor naleving van regelgeving](security-center-compliance-dashboard.md)|✔|✔|✔|Ja|
|Aanbevelingen en bedreigingsbeveiliging in door Docker gehoste IaaS-containers|-|-|-|Ja|
|Evaluatie van ontbrekende OS-patches|✔|✔|✔|Azure: Nee<br><br>Niet-Azure: Ja|
|Evaluatie van onjuiste beveiligingsconfiguratie|✔|✔|✔|Azure: Nee<br><br>Niet-Azure: Ja|
|[Evaluatie van eindpuntbeveiliging](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: Nee<br><br>Niet-Azure: Ja|
|Evaluatie van schijfversleuteling|✔</br>(voor [ondersteunde scenario's](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Nee|
|Evaluatie van beveiligingsproblemen van derden|✔|-|-|Nee|
|[Evaluatie van netwerkbeveiliging](security-center-network-recommendations.md)|✔|✔|-|Nee|


### <a name="linux-machines"></a>[**Linux-machines**](#tab/features-linux)

|**Functie**|**Azure Virtual Machines**|**Virtuele Azure-machineschaalsets**|**Niet-Azure-machines**|**Azure Defender vereist**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP-integratie](security-center-wdatp.md)|-|-|-|Ja|
|[Gedragsanalyse van virtuele machine (en beveiligingswaarschuwingen)](security-center-alerts-iaas.md)|✔</br>(in ondersteunde versies)|✔</br>(in ondersteunde versies)|✔|Ja|
|[Bestandsloze beveiligingswaarschuwingen](alerts-reference.md#alerts-windows)|-|-|-|Ja|
|[Op netwerk gebaseerde beveiligingswaarschuwingen](other-threat-protections.md#network-layer)|✔|✔|-|Ja|
|[Just-in-time-toegang voor virtuele machines](security-center-just-in-time.md)|✔|-|-|Ja|
|[Systeemeigen evaluatie van beveiligingsproblemen](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Ja|
|[Bestandsintegriteit controleren](security-center-file-integrity-monitoring.md)|✔|✔|✔|Ja|
|[Adaptieve toepassingsbesturingselementen](security-center-adaptive-application.md)|✔|-|✔|Ja|
|[Netwerkoverzicht](security-center-network-recommendations.md#network-map)|✔|✔|-|Ja|
|[Adaptieve netwerkbeveiliging](security-center-adaptive-network-hardening.md)|✔|-|-|Ja|
|[Dashboard en rapporten voor naleving van regelgeving](security-center-compliance-dashboard.md)|✔|✔|✔|Ja|
|Aanbevelingen en bedreigingsbeveiliging in door Docker gehoste IaaS-containers|✔|✔|✔|Ja|
|Evaluatie van ontbrekende OS-patches|✔|✔|✔|Azure: Nee<br><br>Niet-Azure: Ja|
|Evaluatie van onjuiste beveiligingsconfiguratie|✔|✔|✔|Azure: Nee<br><br>Niet-Azure: Ja|
|[Evaluatie van eindpuntbeveiliging](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Nee|
|Evaluatie van schijfversleuteling|✔</br>(voor [ondersteunde scenario's](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Nee|
|Evaluatie van beveiligingsproblemen van derden|✔|-|-|Nee|
|[Evaluatie van netwerkbeveiliging](security-center-network-recommendations.md)|✔|✔|-|Nee|

--- 


> [!TIP]
>Als u wilt experimenteren met functies die alleen beschikbaar zijn met Azure Defender, kunt u zich inschrijven voor een proefversie van 30 dagen. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.


## <a name="supported-endpoint-protection-solutions"></a>Ondersteunde oplossingen voor eindpuntbeveiliging<a name="endpoint-supported"></a>

De volgende tabel bevat een matrix van:

 - Of u Azure Security Center kunt gebruiken om elke oplossing voor u te installeren.
 - Welke oplossingen voor eindpuntbeveiliging Security Center kan detecteren. Als er een oplossing voor eindpuntbeveiliging uit deze lijst wordt gedetecteerd, raadt Security Center niet aan er een te installeren.

Zie [Evaluatie van eindpuntbeveiliging en aanbevelingen](security-center-endpoint-protection.md) voor meer informatie over wanneer er aanbevelingen worden gegenereerd voor elk van deze soorten beveiliging.

| Endpoint Protection| Platformen | Security Center-installatie | Security Center Discovery |
|------|------|-----|-----|
| Microsoft Defender Antivirus| Windows Server 2016 of hoger| Nee, ingebouwd in besturingssysteem| Ja |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (zie opmerking hieronder) | Via extensie | Ja |
| Trend Micro - Deep Security | Windows Server-familie  | Nee | Ja |
| Symantec v12.1.1100+| Windows Server-familie  | Nee | Ja |
| McAfee v10+ | Windows Server-familie  | Nee | Ja |
| McAfee v10+ | Linux Server-familie  | Nee | Ja **\*** |
| Sophos V9+| Linux Server-familie  | Nee | Ja  **\***  |

 **\*** De status van de dekking en de ondersteunende gegevens zijn momenteel alleen beschikbaar in de Log Analytics-werkruimte die aan uw beveiligde abonnementen is gekoppeld. Deze wordt niet weergegeven in de Azure Security Center-portal.

> [!NOTE]
> Voor de detectie van System Center Endpoint Protection (SCEP) op een virtuele machine met Windows Server 2008 R2 moet SCEP worden geïnstalleerd na PowerShell (v 3.0 of nieuwer).


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe [Security Center gegevens verzamelt en de Log Analytics-agent](security-center-enable-data-collection.md).
- Meer informatie over hoe [Security Center gegevens beheert en beveiligt](security-center-data-security.md).
- Bekijk de [platforms die ondersteuning bieden voor Security Center](security-center-os-coverage.md).