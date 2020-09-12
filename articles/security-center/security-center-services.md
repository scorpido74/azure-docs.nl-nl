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
ms.openlocfilehash: c4f3ccce03f91b0567980d55b59bfb15d6985bb7
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299742"
---
# <a name="feature-coverage-for-machines"></a>Functie dekking voor machines

De twee tabbladen hieronder tonen de functies van Azure Security Center die beschikbaar zijn voor virtuele Windows-en Linux-machines en-servers.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Ondersteunde functies voor virtuele machines en servers <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows-machines**](#tab/features-windows)

|**Functie**|**Azure Virtual Machines**|**Virtuele Azure-machineschaalsets**|**Niet-Azure-machines**|**Prijzen**
|----|:----:|:----:|:----:|:----:|
|[Micro soft Defender ATP-integratie](security-center-wdatp.md)|✔</br>(op ondersteunde versies)|✔</br>(op ondersteunde versies)|✔|Standard|
|[Gedrags analyse van virtuele machine (en beveiligings waarschuwingen)](threat-protection.md)|✔|✔|✔|Standard|
|[Beveiligings waarschuwingen met een bestand](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Beveiligings waarschuwingen op basis van het netwerk](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Just-in-time-toegang voor virtuele machines](security-center-just-in-time.md)|✔|-|-|Standard|
|[Beoordeling van systeem eigen beveiligings problemen](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Standard|
|[Bestandsintegriteit controleren](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Adaptieve toepassingsbesturingselementen](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Netwerkoverzicht](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptieve netwerkbeveiliging](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|[Dash board naleving van regelgeving & rapporten](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Aanbevelingen en beveiliging tegen bedreigingen op docker-gehoste IaaS-containers|-|-|-|Standard|
|Evaluatie van besturingssysteem patches ontbreekt|✔|✔|✔|Azure: gratis<br><br>Niet-Azure: Standard|
|Evaluatie van onjuiste beveiligings configuratie|✔|✔|✔|Azure: gratis<br><br>Niet-Azure: Standard|
|[Endpoint Protection-evaluatie](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: gratis<br><br>Niet-Azure: Standard|
|Analyse van schijf versleuteling|✔|✔|-|Gratis|
|Beoordeling van beveiligings lekken van derden|✔|-|-|Gratis|
|[Evaluatie van netwerk beveiliging](security-center-network-recommendations.md)|✔|✔|-|Gratis|


### <a name="linux-machines"></a>[**Linux-machines**](#tab/features-linux)

|**Functie**|**Azure Virtual Machines**|**Virtuele Azure-machineschaalsets**|**Niet-Azure-machines**|**Prijzen**
|----|:----:|:----:|:----:|:----:|
|[Micro soft Defender ATP-integratie](security-center-wdatp.md)|-|-|-|Standard|
|[Gedrags analyse van virtuele machine (en beveiligings waarschuwingen)](security-center-alerts-iaas.md)|✔</br>(op ondersteunde versies)|✔</br>(op ondersteunde versies)|✔|Standard|
|[Beveiligings waarschuwingen met een bestand](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Beveiligings waarschuwingen op basis van het netwerk](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Just-in-time-toegang voor virtuele machines](security-center-just-in-time.md)|✔|-|-|Standard|
|[Beoordeling van systeem eigen beveiligings problemen](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Standard|
|[Bestandsintegriteit controleren](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Adaptieve toepassingsbesturingselementen](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Netwerkoverzicht](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptieve netwerkbeveiliging](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|[Dash board naleving van regelgeving & rapporten](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Aanbevelingen en beveiliging tegen bedreigingen op docker-gehoste IaaS-containers|✔|✔|✔|Standard|
|Evaluatie van besturingssysteem patches ontbreekt|✔|✔|✔|Azure: gratis<br><br>Niet-Azure: Standard|
|Evaluatie van onjuiste beveiligings configuratie|✔|✔|✔|Azure: gratis<br><br>Niet-Azure: Standard|
|[Endpoint Protection-evaluatie](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Gratis|
|Analyse van schijf versleuteling|✔|✔|-|Gratis|
|Beoordeling van beveiligings lekken van derden|✔|-|-|Gratis|
|[Evaluatie van netwerk beveiliging](security-center-network-recommendations.md)|✔|✔|-|Gratis|

--- 


> [!TIP]
>Als u wilt experimenteren met functies die alleen beschikbaar zijn in de prijs categorie Standard, kunnen gebruikers met een gratis laag een proef versie van 30 dagen registreren. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/)voor meer informatie.


## <a name="supported-endpoint-protection-solutions"></a>Ondersteunde Endpoint Protection-oplossingen <a name="endpoint-supported"></a>

De volgende tabel bevat een matrix met:

 - Hiermee wordt aangegeven of u Azure Security Center kunt gebruiken om elke oplossing voor u te installeren.
 - Welke Endpoint Protection-oplossingen Security Center kunnen detecteren. Als er een Endpoint Protection-oplossing in deze lijst wordt gedetecteerd, wordt Security Center niet aanbevolen een installatie uit te voeren.

Zie [Endpoint Protection-evaluatie en aanbevelingen](security-center-endpoint-protection.md)voor meer informatie over wanneer er aanbevelingen worden gegenereerd voor elk van deze beveiligings maatregelen.

| Endpoint Protection| Platformen | Security Center-installatie | Security Center Discovery |
|------|------|-----|-----|
| Microsoft Defender Antivirus| Windows Server 2016 of hoger| Nee, ingebouwd in besturingssysteem| Yes |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (zie opmerking hieronder) | Via extensie | Yes |
| Trend Micro – diepe beveiliging | Windows Server-familie  | Nee | Ja |
| Symantec v12.1.1100+| Windows Server-familie  | Nee | Ja |
| McAfee v10+ | Windows Server-familie  | Nee | Ja |
| McAfee v10+ | Linux-Server familie  | No | Klikt **\*** |
| Sophos v9 +| Linux-Server familie  | No | Klikt  **\***  |

 **\*** De status van de dekking en de ondersteunende gegevens zijn momenteel alleen beschikbaar in de werk ruimte Log Analytics die aan uw beveiligde abonnementen is gekoppeld. Het wordt niet weer gegeven in de Azure Security Center Portal.

> [!NOTE]
> Voor de detectie van System Center Endpoint Protection (SCEP) op een virtuele machine met Windows Server 2008 R2 moet SCEP worden geïnstalleerd na Power shell (v 3.0 of nieuwer).


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [Security Center verzamelen van gegevens en de log Analytics-agent](security-center-enable-data-collection.md).
- Meer informatie over het [Security Center beheren en beveiligen van gegevens](security-center-data-security.md).
- Bekijk de [platforms die Security Center ondersteunen](security-center-os-coverage.md).