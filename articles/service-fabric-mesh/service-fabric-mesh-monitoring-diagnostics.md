---
title: Monitoring en diagnostiek in Azure Service Fabric Mesh-apps
description: Meer informatie over het bewaken en diagnosticeren van toepassingen in Service Fabric Mesh op Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 247a1de4d00668371337295616d31caf101f0cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498141"
---
# <a name="monitoring-and-diagnostics"></a>Controle en diagnose
Azure Service Fabric Mesh is een volledig beheerde service waarmee ontwikkelaars microservices-toepassingen kunnen implementeren zonder virtuele machines, opslag of netwerken hoeven te beheren. Monitoring en diagnostiek voor Service Fabric Mesh is onderverdeeld in drie hoofdtypen diagnostische gegevens:

- Toepassingslogboeken - deze worden gedefinieerd als de logboeken van uw gecontaineriseerde toepassingen, op basis van hoe u uw toepassing hebt geinstrumenteerd (bijv. dockerlogs)
- Platformgebeurtenissen - gebeurtenissen van het Mesh-platform die relevant zijn voor uw containeractiviteiten, momenteel inclusief containeractivering, deactivering en beëindiging.
- Containerstatistieken - resourcegebruik en prestatiestatistieken voor uw containers (dockerstatistieken)

In dit artikel worden de opties voor monitoring en diagnostiek voor de nieuwste preview-versie besproken.

## <a name="application-logs"></a>Toepassingslogboeken

U uw dockerlogs bekijken vanaf uw geïmplementeerde containers, per container. In het servicefabric mesh-toepassingsmodel is elke container een codepakket in uw toepassing. Als u de gekoppelde logboeken met een codepakket wilt bekijken, gebruikt u de volgende opdracht:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> U de opdracht 'az mesh-service-replica' gebruiken om de replicanaam te krijgen. Replicanamen verhogen gehele getallen van 0.

Hier is hoe dit eruit ziet voor het zien van de logs van de VotingWeb.Code container van de stemapplicatie:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Containerstatistieken 

De Mesh-omgeving onthult een handvol statistieken die aangeven hoe uw containers presteren. De volgende statistieken zijn beschikbaar via de Azure-portal en Azure-monitor CLI:

| Gegevens | Beschrijving | Eenheden|
|----|----|----|
| Cpu-gebruik | ActualCpu/Toegewezen Cpu als percentage | % |
| Geheugengebruik | ActualMem/ToegewezenMem als percentage | % |
| ToegewezenCpu | Cpu toegewezen volgens Azure Resource Manager-sjabloon | Millicores |
| ToegewezenGeheugen | Geheugen toegewezen volgens azure resource manager-sjabloon | MB |
| Werkelijke Cpu | CPU-gebruik | Millicores |
| Werkelijkememory | Geheugengebruik | MB |
| ContainerStatus | 0 - Ongeldig: de containerstatus is onbekend <br> 1 - In behandeling: De container is gepland om te starten <br> 2 - Start: De container is in het proces van het starten <br> 3 - Gestart: De container is succesvol gestart <br> 4 - Stoppen: De container wordt gestopt <br> 5 - Gestopt: de container is gestopt | N.v.t. |
| ApplicationStatus | 0 - Onbekend: de status is niet opvraagbaar <br> 1 - Gereed: de toepassing wordt uitgevoerd <br> 2 - Upgraden: Er is een upgrade aan de gang <br> 3 - Maken: De toepassing wordt gemaakt <br> 4 - Verwijderen: De toepassing wordt verwijderd <br> 5 - Mislukt: de toepassing kan niet worden geïmplementeerd | N.v.t. |
| ServiceStatus | 0 - Ongeldig: de service heeft momenteel geen status <br> 1 - Ok: De service is gezond  <br> 2 - Waarschuwing: Er kan iets mis zijn dat onderzoek vereist <br> 3 - Fout: Er is iets mis dat onderzoek nodig heeft <br> 4 - Onbekend: De status is niet opvraagbaar | N.v.t. |
| ServiceReplicaStatus | 0 - Ongeldig: de replica heeft momenteel geen status <br> 1 - Ok: De service is gezond  <br> 2 - Waarschuwing: Er kan iets mis zijn dat onderzoek vereist <br> 3 - Fout: Er is iets mis dat onderzoek nodig heeft <br> 4 - Onbekend: De status is niet opvraagbaar | N.v.t. | 
| Aantal opnieuw starten | Aantal containeropnieuw opstarten | N.v.t. |

> [!NOTE]
> De waarden ServiceStatus en ServiceReplicaStatus zijn dezelfde als de [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) in Service Fabric. 

Elke statistiek is beschikbaar op verschillende dimensies, zodat u aggregaten op verschillende niveaus zien. De huidige lijst met dimensies is als volgt:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> De codepackagename-dimensie is niet beschikbaar voor Linux-toepassingen. 

Elke dimensie komt overeen met verschillende componenten van het [Service Fabric Application-model](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

Een volledige lijst met opdrachten is beschikbaar in de [Azure Monitor CLI-documenten,](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) maar we hebben hieronder een paar handige voorbeelden toegevoegd 

In elk voorbeeld volgt de Resource-ID dit patroon

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* CPU-gebruik van de containers in een toepassing

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Geheugengebruik voor elke servicereplica
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Opnieuw start voor elke container in een venster van 1 uur 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Gemiddeld CPU-gebruik voor services met de naam "VotingWeb" in een venster van 1 uur
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Statistieken explorer

Metrics explorer is een blade in de portal waarin je alle statistieken voor je Mesh-toepassing visualiseren. Dit blad is toegankelijk op de pagina van de toepassing in de portal en het Azure-monitorblad, waarvan u het laatste gebruiken om statistieken weer te geven voor al uw Azure-resources die Azure Monitor ondersteunen. 

![Metrics Explorer](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Volgende stappen
* Lees [Wat is Service Fabric Mesh?](service-fabric-mesh-overview.md) voor meer informatie over Service Fabric Mesh.
* Ga voor meer informatie over de opdrachten voor Azure Monitor-metrische gegevens naar de [CLI-documenten van Azure Monitor](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
