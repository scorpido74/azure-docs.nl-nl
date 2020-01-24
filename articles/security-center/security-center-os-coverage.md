---
title: Platforms die worden ondersteund door Azure Security Center | Microsoft Docs
description: Dit document bevat een lijst met platformen die door Azure Security Center worden ondersteund.
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
ms.openlocfilehash: 6ec818ff77664fcc038412f79fffc1e3e05b82f0
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294249"
---
# <a name="supported-platforms"></a>Ondersteunde platforms 

## Virtuele machines/servers<a name="vm-server"></a>

Security Center ondersteunt virtuele machines/servers in verschillende typen hybride omgevingen:

* Alleen Azure
* Azure en on-premises
* Azure en andere Clouds
* Azure, andere Clouds en on-premises

Voor een Azure-omgeving die wordt geactiveerd op een Azure-abonnement, detecteert Azure Security Center automatisch IaaS-resources die in het abonnement zijn geïmplementeerd.

> [!NOTE]
> Als u de volledige set beveiligings functies wilt ontvangen, moet u beschikken over de [log Analytics-agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent), die wordt gebruikt door Azure Security Center, is geïnstalleerd en [correct is geconfigureerd voor het verzenden van gegevens naar Azure Security Center](security-center-enable-data-collection.md#manual-agent).


De volgende secties bevatten een lijst met de ondersteunde besturings systemen voor servers waarop de [log Analytics-agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent), die door Azure Security Center wordt gebruikt, kan worden uitgevoerd.

### Windows Server-besturings systemen<a name="os-windows"></a>

|Besturingssysteem|Ondersteund door Azure Security Center|Ondersteuning voor integratie met micro soft Defender ATP|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

Zie [ondersteunde functies van virtual machine/server](security-center-services.md#vm-server-features)voor meer informatie over de ondersteunde functies voor de Windows-besturings systemen die hierboven worden vermeld.

### Windows-besturings systemen<a name="os-windows (non-server)"></a>

Azure Security Center integreert met Azure-Services om uw op Windows gebaseerde virtuele machines te controleren en te beveiligen.

### Linux-besturings systemen<a name="os-linux"></a>

64-bits

* CentOS 6 en 7
* Amazon Linux 2017,09
* Oracle Linux 6 en Oracle Linux 7
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

> [!NOTE]
> Omdat de lijst met ondersteunde Linux-besturings systemen voortdurend wordt gewijzigd, klikt u [hier](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) om de meest recente lijst met ondersteunde versies weer te geven, in het geval er wijzigingen zijn sinds dit onderwerp voor het laatst is gepubliceerd.

Zie voor meer informatie over de ondersteunde functies voor de Linux-besturings systemen, die hierboven worden vermeld, de [functies van virtuele machine/server](security-center-services.md#vm-server-features)die worden ondersteund.

### Beheerde services voor virtuele machines<a name="virtual-machine"></a>

Virtuele machines worden ook in een klant abonnement gemaakt als onderdeel van sommige door Azure beheerde services, zoals Azure Kubernetes (AKS), Azure Databricks en meer. Deze virtuele machines worden ook gedetecteerd door Azure Security Center en de log Analytics-agent kan worden geïnstalleerd en geconfigureerd volgens de ondersteunde [Windows/Linux-besturings systemen](#os-windows), zoals hierboven vermeld.

### Cloud Services<a name="cloud-services"></a>

Virtuele machines die worden uitgevoerd in een Cloud service, worden ook ondersteund. Alleen Cloud Services-Web-en-werk rollen die worden uitgevoerd in productie-sleuven worden bewaakt. Zie [overzicht van Azure Cloud Services](../cloud-services/cloud-services-choose-me.md)voor meer informatie over Cloud Services.

## PaaS Services<a name="paas-services"></a>

De volgende Azure PaaS-resources worden ondersteund door Azure Security Center:

* SQL
* PostGreSQL
* MySQL
* CosmosDB
* Opslagaccount
* App Service
* Functie
* Cloudservice
* VNet
* Subnet
* NIC
* NSG
* Batch-account
* Service Fabric-account
* Automation-account
* Load Balancer
* Search
* Service Bus-naamruimte
* Stream Analytics
* Event hub-naamruimte
* Logische apps
* Redis
* Data Lake Analytics
* Data Lake Store
* Key Vault

Zie [PaaS Services ondersteunde functies](security-center-services.md#paas-services)voor meer informatie over de ondersteunde functies voor de bovenstaande lijst met PaaS-resources.

De beveiliging van Virtual Machines in Azure Stack wordt ook ondersteund. Voor meer informatie over de integratie van Security Center met Azure Stack raadpleegt u [de Azure stack virtuele machines Onboarden om te Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [Security Center verzamelen van gegevens en de log Analytics-agent](security-center-enable-data-collection.md).
- Meer informatie over het [Security Center beheren en beveiligen van gegevens](security-center-data-security.md).
- Meer informatie over [het plannen en begrijpen van de ontwerp overwegingen bij het aannemen van Azure Security Center](security-center-planning-and-operations-guide.md).
- Meer informatie over [functies die beschikbaar zijn voor de verschillende Cloud omgevingen](security-center-services.md).
- Meer informatie over het [detecteren van bedreigingen voor vm's & servers in azure Security Center](security-center-alerts-iaas.md).
- Vind [Veelgestelde vragen over het gebruik van Azure Security Center](security-center-faq.md).
- Vind [blog berichten over de beveiliging en naleving van Azure](https://blogs.msdn.com/b/azuresecurity/).
