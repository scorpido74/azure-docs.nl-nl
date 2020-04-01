---
title: Platforms die worden ondersteund door Azure Security Center | Microsoft Documenten
description: Dit document bevat een lijst met platforms die worden ondersteund door Azure Security Center.
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
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: f9b948714f72ba02a100d9941721f073953bf22a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473235"
---
# <a name="supported-platforms"></a>Ondersteunde platforms 

## <a name="virtual-machines--servers"></a>Virtuele machines / servers<a name="vm-server"></a>

Security Center ondersteunt virtuele machines / servers op verschillende soorten hybride omgevingen:

* Alleen Azure
* Azure en on-premises
* Azure en andere clouds
* Azure, andere clouds en on-premises

Voor een Azure-omgeving die is geactiveerd op een Azure-abonnement, detecteert Azure Security Center automatisch IaaS-resources die binnen het abonnement zijn geïmplementeerd.

> [!NOTE]
> Als u de volledige set beveiligingsfuncties wilt ontvangen, moet u beschikken over de [Log Analytics-agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent), die wordt gebruikt door Azure Security Center, die is geïnstalleerd en [correct geconfigureerd om gegevens naar Azure Security Center te verzenden.](security-center-enable-data-collection.md#manual-agent)

In de volgende secties worden de ondersteunde serverbesturingssystemen vermeld waarop de [Log Analytics Agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent), die wordt gebruikt door Azure Security Center, kan worden uitgevoerd.

### <a name="windows-server-operating-systems"></a>Windows-serverbesturingssystemen<a name="os-windows"></a>

|OS|Ondersteund door Azure Security Center|Ondersteuning voor integratie met Microsoft Defender ATP|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

Zie Functies voor ondersteunde functies voor virtuele [machines en servers](security-center-services.md#vm-server-features)voor meer informatie over de ondersteunde functies voor de Windows-besturingssystemen.

### <a name="windows-operating-systems"></a>Windows-besturingssystemen<a name="os-windows (non-server)"></a>

Azure Security Center integreert met Azure-services om uw virtuele windows-apparaten te bewaken en te beschermen.

### <a name="linux-operating-systems"></a>Linux-besturingssystemen<a name="os-linux"></a>

64-bits

* CentOS 6 en 7
* Amazon Linux 2017.09
* Oracle Linux 6 en Oracle Linux 7
* Red Hat Enterprise Linux Server 6 en 7
* Debian GNU/Linux 8 en 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS en 18.04 LTS
* SUSE Linux Enterprise Server 12

32-bits
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 en 9
* Ubuntu Linux 14.04 LTS en 16.04 LTS

> [!NOTE]
> Aangezien de lijst met ondersteunde Linux-besturingssystemen voortdurend verandert, klik dan [hier](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) om de meest actuele lijst met ondersteunde versies te bekijken, voor het geval er wijzigingen zijn geweest sinds dit onderwerp voor het laatst werd gepubliceerd.

Zie Functies voor ondersteunde functies voor virtuele [machines en servers](security-center-services.md#vm-server-features)voor meer informatie over de ondersteunde functies voor de Linux-besturingssystemen.

### <a name="managed-virtual-machine-services"></a>Beheerde virtuele machineservices<a name="virtual-machine"></a>

Virtuele machines worden ook gemaakt in een klantabonnement als onderdeel van sommige Azure managed services, zoals Azure Kubernetes (AKS), Azure Databricks en meer. Deze virtuele machines worden ook ontdekt door Azure Security Center, en de Log analytics agent kan worden geïnstalleerd en geconfigureerd volgens de ondersteunde [Windows / Linux besturingssystemen](#os-windows), hierboven vermeld.

### <a name="cloud-services"></a>Cloudservices<a name="cloud-services"></a>

Virtuele machines die in een cloudservice worden uitgevoerd, worden ook ondersteund. Alleen web- en werknemersrollen voor cloudservices die in productieslots worden uitgevoerd, worden gecontroleerd. Zie Overzicht van Azure [Cloud Services](../cloud-services/cloud-services-choose-me.md)voor meer informatie over cloudservices.

Beveiliging voor virtuele machines die in Azure Stack wonen, wordt ook ondersteund. Zie Uw [virtuele Azure Stack-apparaten ingebruikmaken voor](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)meer informatie over de integratie van Security Center met Azure Stack.

## <a name="next-steps"></a>Volgende stappen

- Ontdek hoe [Security Center gegevens verzamelt en de Log Analytics Agent](security-center-enable-data-collection.md).
- Ontdek hoe [Security Center gegevens beheert en beschermt.](security-center-data-security.md)
- Meer informatie over het [plannen en begrijpen van de ontwerpoverwegingen voor de goedkeuring van Azure Security Center.](security-center-planning-and-operations-guide.md)
- Meer informatie over [functies die beschikbaar zijn voor de verschillende cloudomgevingen.](security-center-services.md)
- Meer informatie over [bedreigingsbeveiliging voor Windows- en Linux-machines in Azure Security Center](threat-protection.md#windows-machines).
