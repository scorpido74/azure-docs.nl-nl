---
title: Gebeurtenisaggregatie met Linux Azure Diagnostics
description: Meer informatie over het aggregeren en verzamelen van gebeurtenissen met LAD voor monitoring en diagnostiek van Azure Service Fabric-clusters.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: fdb78498d33416ef21b2e2b0f498e7afa6a58d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609958"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Aggregatie en verzameling van gebeurtenissen met Linux Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Wanneer u een Azure Service Fabric-cluster uitvoert, is het een goed idee om de logboeken van alle knooppunten op een centrale locatie te verzamelen. Als u de logboeken op een centrale locatie hebt, u problemen in uw cluster of problemen in de toepassingen en services die in dat cluster worden uitgevoerd, analyseren en oplossen.

Een manier om logboeken te uploaden en te verzamelen, is door de LAD-extensie (Linux Azure Diagnostics) te gebruiken, die logboeken uploadt naar Azure Storage, en ook de mogelijkheid heeft om logboeken naar Azure Application Insights of Event Hubs te verzenden. U ook een extern proces gebruiken om de gebeurtenissen uit de opslag te lezen en deze in een analyseplatformproduct te plaatsen, zoals [Azure Monitor-logboeken](../log-analytics/log-analytics-service-fabric.md) of een andere log-parsing-oplossing.

## <a name="log-and-event-sources"></a>Log- en gebeurtenisbronnen

### <a name="service-fabric-platform-events"></a>Service Fabric-platformevenementen
Service Fabric zendt een paar out-of-the-box logs uit via [LTTng,](https://lttng.org)inclusief operationele gebeurtenissen of runtime-gebeurtenissen. Deze logboeken worden opgeslagen op de locatie die de resourcebeheersjabloon van het cluster opgeeft. Als u de opslagaccountgegevens wilt opvragen of instellen, zoekt u naar de tag **AzureTableWinFabETWQueryable** en zoekt u **StoreConnectionString**.

### <a name="application-events"></a>Toepassingsgebeurtenissen
 Gebeurtenissen die worden uitgezonden uit de code van uw toepassingen en services zoals opgegeven door u bij het instrumenteren van uw software. U elke logboekoplossing gebruiken die tekstgebaseerde logboekbestanden schrijft, bijvoorbeeld LTTng. Zie voor meer informatie de LTTng-documentatie over het traceren van uw toepassing.

[Services controleren en diagnosticeren in een lokale installatie voor machineontwikkeling.](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## <a name="deploy-the-diagnostics-extension"></a>De extensie Diagnostische gegevens implementeren
De eerste stap bij het verzamelen van logboeken is het implementeren van de extensie Diagnostische gegevens op elk van de VM's in het cluster ServiceFabric. De extensie Diagnostische gegevens verzamelt logboeken op elke virtuele machine en uploadt deze naar het opslagaccount dat u opgeeft. 

Als u de extensie Diagnostische gegevens wilt implementeren in de VM's in het cluster als onderdeel van het maken van het cluster, stelt u **Diagnostics** in **op Aan**. Nadat u het cluster hebt gemaakt, u deze instelling niet wijzigen met behulp van de portal, dus u moet de juiste wijzigingen aanbrengen in de sjabloon Resourcemanager.

Hiermee configureert u de LAD-agent om opgegeven logboekbestanden te controleren. Wanneer een nieuwe regel aan het bestand wordt toegevoegd, wordt een syslog-vermelding gemaakt die wordt verzonden naar de opslag (tabel) die u hebt opgegeven.


## <a name="next-steps"></a>Volgende stappen

1. Zie [LTTng-documentatie](https://lttng.org/docs) en [Lad gebruiken](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux)om meer in detail te begrijpen welke gebeurtenissen u moet onderzoeken tijdens het oplossen van problemen.
2. [Stel de loganalytics-agent in](service-fabric-diagnostics-event-analysis-oms.md) om statistieken te verzamelen, containers te controleren die op uw cluster zijn geïmplementeerd en uw logboeken te visualiseren 
