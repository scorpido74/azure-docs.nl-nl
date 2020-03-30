---
title: Over Azure Site Recovery-configuratie/proces/hoofddoelservers
description: In dit artikel vindt u een overzicht van de configuratie-, proces- en hoofddoelservers die worden gebruikt bij het instellen van noodherstel van on-premises Vm's vMware naar Azure met Azure Site Recovery
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062094"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Informatie over siteherstelcomponenten (configuratie, proces, hoofddoel)

In dit artikel worden de configuratie-, proces- en hoofddoelservers beschreven die door de [Site Recovery-service](site-recovery-overview.md) worden gebruikt om Vm's en fysieke servers te repliceren naar Azure.

## <a name="configuration-server"></a>Configuratieserver

Implementeer een on-premises Site Recovery-configuratieserver voor noodherstel van on-premises Vm's en fysieke servers.

**Instelling** | **Details** | **Links**
--- | --- | ---
**Onderdelen**  | De configuratieservermachine voert alle on-premises siteherstelcomponenten uit, waaronder de configuratieserver, de processerver en de hoofddoelserver.<br/><br/> Wanneer u de configuratieserver instelt, worden alle onderdelen automatisch geïnstalleerd. | [Lees](vmware-azure-common-questions.md#configuration-server) de veelgestelde vragen over de configuratieserver.
**Rol** | De configuratieserver coördineert de communicatie tussen on-premises en Azure, en beheert de gegevensreplicatie. | Meer informatie over de architectuur voor [VMware](vmware-azure-architecture.md) en herstel van [fysieke servers](physical-azure-architecture.md) naar Azure.
**VMware-vereisten** | Voor noodherstel van on-premises Vm's vMware moet u de configuratieserver installeren en uitvoeren als een on-premises, zeer beschikbare Vm vmware. | [Meer informatie over](vmware-azure-deploy-configuration-server.md#prerequisites) de vereisten.
**VMware-implementatie** | We raden u aan de configuratieserver te implementeren met behulp van een gedownloade OVA-sjabloon. Deze methode biedt een eenvoudig mogelijke manier om een configuratieserver in te stellen die voldoet aan alle vereisten en vereisten.<br/><br/> Als u om de een of andere reden een Vm vmware niet implementeren met behulp van een OVA-sjabloon, u de configuratieservermachines handmatig instellen, zoals hieronder beschreven voor herstel van fysieke machinerampen. | [Implementeren](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) met een OVA-sjabloon.
**Vereisten voor fysieke servers** | Voor noodherstel op on-premises fysieke servers implementeert u de configuratieserver handmatig. | [Meer informatie over](physical-azure-set-up-source.md#prerequisites) de vereisten.
**Fysieke serverimplementatie** | Als het niet kan worden geïnstalleerd als een VMware VM, u deze installeren op een fysieke server. | [Implementeer](physical-azure-set-up-source.md#set-up-the-source-environment) de configuratieserver handmatig.

## <a name="process-server"></a>Processerver

Een processerver verwerkt replicatiegegevens tijdens failover en failback en installeert de Mobiliteitsservice voor on-premises Vm's en fysieke servers.

**Instelling** | **Details** | **Links**
--- | --- | ---
**Implementatie**  | Wanneer de configuratieserver is geïmplementeerd, is de processerver standaard geïnstalleerd. <br/><br/> Een on-premises processerver is nodig voor noodherstel en replicatie van on-premises Vm's vmware en fysieke servers. | [Meer informatie](vmware-azure-architecture.md#architectural-components).
**Rol (on-premises)** | Hiermee ontvangt u replicatiegegevens van machines die zijn ingeschakeld voor replicatie. <br/><br/> Optimaliseert replicatiegegevens met caching, compressie en versleuteling en stuurt deze naar Azure Storage. <br/><br/> Hiermee voert u een push-installatie uit van de Site Recovery Mobility-service op on-premises Vm's en fysieke servers die u wilt repliceren. <br/><br/> Voert automatische detectie van on-premises machines uit. | [Meer informatie](vmware-azure-enable-replication.md).
**Rol (failback van Azure)** | Na failover van uw on-premises site, stelt u een processerver in Azure in, als een Azure VM, om failback naar uw on-premises locatie te verwerken.<br/><br/> De processerver in Azure is tijdelijk. De Azure VM kan worden verwijderd nadat failback is gedaan. | [Meer informatie](vmware-azure-set-up-process-server-azure.md).
**Schalen** | Voor grotere implementaties u on-premises aanvullende, scale-out processervers instellen. Extra servers schalen de capaciteit uit door grotere aantallen replicerende machines en grotere hoeveelheden replicatieverkeer te verwerken.<br/><br/> U machines tussen twee processervers verplaatsen om replicatieverkeer in balans te laden. | [Meer informatie](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>Hoofddoelserver

Op de hoofddoelserver worden de replicatiegegevens tijdens de failback vanuit Azure afgehandeld.

- Standaard is de hoofddoelserver geïnstalleerd op de configuratieserver.
- Voor grote implementaties u een extra, afzonderlijke hoofddoelserver toevoegen voor failback.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [architectuur](vmware-azure-architecture.md) voor noodherstel van Vm's en fysieke servers.
- Bekijk de [vereisten en vereisten](vmware-physical-azure-support-matrix.md) voor noodherstel van VMware VM's en fysieke servers naar Azure.
