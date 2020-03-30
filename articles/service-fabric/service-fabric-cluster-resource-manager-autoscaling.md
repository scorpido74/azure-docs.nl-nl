---
title: Azure Service Fabric Automatisch schalen services en containers
description: Met Azure Service Fabric u automatisch schaalbeleid instellen voor services en containers.
author: radicmilos
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: miradic
ms.openlocfilehash: 3660ece7add8f279292340aae9ab445b682fe045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452088"
---
# <a name="introduction-to-auto-scaling"></a>Inleiding tot automatisch schalen
Automatisch schalen is een extra mogelijkheid van Service Fabric om uw services dynamisch te schalen op basis van de belasting die services rapporteren of op basis van hun gebruik van resources. Automatisch schalen geeft een grote elasticiteit en maakt het mogelijk om extra exemplaren of partities van uw service op aanvraag in te richten. Het volledige automatische schalingproces is geautomatiseerd en transparant en zodra u uw beleid voor een service hebt ingesteld, is er geen noodzaak voor handmatige schalingsbewerkingen op serviceniveau. Automatisch schalen kan worden ingeschakeld op het moment van het maken van service of op elk gewenst moment door de service bij te werken.

Een veelvoorkomend scenario waarbij automatisch schalen nuttig is, is wanneer de belasting van een bepaalde service in de loop van de tijd varieert. Een service zoals een gateway kan bijvoorbeeld schalen op basis van de hoeveelheid resources die nodig zijn om binnenkomende aanvragen af te handelen. Laten we eens kijken naar een voorbeeld van hoe die schaalregels eruit kunnen zien:
* Als alle exemplaren van mijn gateway gemiddeld meer dan twee cores gebruiken, schaal dan de gatewayservice door nog een exemplaar toe te voegen. Doe dit elk uur, maar nooit meer dan zeven exemplaren in totaal.
* Als alle exemplaren van mijn gateway gemiddeld minder dan 0,5 cores gebruiken, schaal dan de service in door één exemplaar te verwijderen. Doe dit elk uur, maar nooit minder dan drie exemplaren in totaal.

Automatisch schalen wordt ondersteund voor zowel containers als reguliere Service Fabric-services. Als u automatisch schalen wilt gebruiken, moet u worden uitgevoerd op versie 6.2 of hoger van de runtime van Service Fabric. 

In de rest van dit artikel worden de schalingsbeleidsregels beschreven, manieren om automatisch schalen in- of uit te schakelen en worden voorbeelden gegeven over het gebruik van deze functie.

## <a name="describing-auto-scaling"></a>Automatische schaling beschrijven
Beleid voor automatisch schalen kan worden gedefinieerd voor elke service in een cluster van Servicefabric. Elk schaalbeleid bestaat uit twee delen:
* **Scaling trigger** beschrijft wanneer het schalen van de service zal worden uitgevoerd. Voorwaarden die in de trigger worden gedefinieerd, worden periodiek gecontroleerd om te bepalen of een service al dan niet moet worden geschaald.

* **Het schalingmechanisme** beschrijft hoe schalen wordt uitgevoerd wanneer het wordt geactiveerd. Mechanisme wordt alleen toegepast wanneer aan de voorwaarden van de trigger is voldaan.

Alle triggers die momenteel worden ondersteund, werken met [logische belastingsstatistieken](service-fabric-cluster-resource-manager-metrics.md)of met fysieke statistieken zoals CPU of geheugengebruik. Hoe dan ook, Service Fabric controleert de gerapporteerde belasting voor de statistiek en evalueert de trigger periodiek om te bepalen of schaling nodig is.

Er zijn twee mechanismen die momenteel worden ondersteund voor automatisch schalen. De eerste is bedoeld voor stateless services of voor containers waarbij automatisch schalen wordt uitgevoerd door [instanties](service-fabric-concepts-replica-lifecycle.md)toe te voegen of te verwijderen. Voor zowel stateful als stateless services kan automatisch schalen ook worden uitgevoerd door benoemde [partities](service-fabric-concepts-partitioning.md) van de service toe te voegen of te verwijderen.

> [!NOTE]
> Momenteel is er ondersteuning voor slechts één schalingsbeleid per service en slechts één schaaltrigger per schaalbeleid.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Gemiddelde trigger voor partitiebelasting met op instantie gebaseerdschalen
Het eerste type trigger is gebaseerd op de belasting van instanties in een stateless servicepartitie. Metrische belastingen worden eerst gladgestreken om de belasting voor elk exemplaar van een partitie te verkrijgen, en vervolgens worden deze waarden gemiddeld over alle exemplaren van de partitie. Er zijn drie factoren die bepalen wanneer de service wordt geschaald:

* _Lagere belastingsdrempel_ is een waarde die bepaalt wanneer de service wordt **geschaald in**. Als de gemiddelde belasting van alle exemplaren van de partities lager is dan deze waarde, wordt de service ingeschaald.
* _Bovenste belastingsdrempel_ is een waarde die bepaalt wanneer de service wordt **uitgeschaald.** Als de gemiddelde belasting van alle exemplaren van de partitie hoger is dan deze waarde, wordt de service uitgeschaald.
* _Het schaalinterval_ bepaalt hoe vaak de trigger wordt gecontroleerd. Zodra de trigger is gecontroleerd, als schalen nodig is, wordt het mechanisme toegepast. Als schalen niet nodig is, wordt er geen actie ondernomen. In beide gevallen wordt trigger niet opnieuw gecontroleerd voordat het schaalinterval opnieuw verloopt.

Deze trigger kan alleen worden gebruikt voor stateless services (stateless containers of Service Fabric services). In het geval dat een service meerdere partities heeft, wordt de trigger voor elke partitie afzonderlijk geëvalueerd en wordt het opgegeven mechanisme onafhankelijk van elkaar op de partitie toegepast. Vandaar, in dit geval, is het mogelijk dat sommige van de partities van de dienst zal worden geschaald, zullen sommige worden ingeschaald in, en sommige zullen niet worden geschaald op alle op hetzelfde moment, op basis van hun belasting.

Het enige mechanisme dat kan worden gebruikt met deze trigger is PartitionInstanceCountScaleMechanism. Er zijn drie factoren die bepalen hoe dit mechanisme wordt toegepast:
* _Schaaltoename_ bepaalt hoeveel exemplaren worden toegevoegd of verwijderd wanneer het mechanisme wordt geactiveerd.
* _Het maximumaantal instance_ definieert de bovengrens voor schalen. Als het aantal exemplaren van de partitie deze limiet bereikt, wordt de service niet geschaald, ongeacht de belasting. Het is mogelijk om deze limiet weg te laten door de waarde van -1 op te geven, en in dat geval wordt de service zoveel mogelijk uitgeschaald (de limiet is het aantal knooppunten dat beschikbaar is in het cluster).
* _Minimumaantal instantie_ definieert de ondergrens voor schalen. Als het aantal exemplaren van de partitie deze limiet bereikt, wordt de service niet geschaald, ongeacht de belasting.

## <a name="setting-auto-scaling-policy"></a>Beleid voor automatisch schalen instellen

### <a name="using-application-manifest"></a>Toepassingsmanifest gebruiken
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>C# API's gebruiken
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>Powershell gebruiken
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Gemiddelde trigger voor servicebelasting met op partitiegebaseerde schalen
De tweede trigger is gebaseerd op de belasting van alle partities van één service. Metrische belastingen worden eerst gladgestreken om de belasting voor elke replica of instantie van een partitie te verkrijgen. Voor stateful services wordt de belasting van de partitie beschouwd als de belasting van de primaire replica, terwijl voor stateloze services de belasting van de partitie de gemiddelde belasting is van alle exemplaren van de partitie. Deze waarden worden gemiddeld over alle partities van de service en deze waarde wordt gebruikt om de automatische schaling te activeren. Hetzelfde als in het vorige mechanisme, zijn er drie factoren die bepalen wanneer de service zal worden geschaald:

* _Lagere belastingsdrempel_ is een waarde die bepaalt wanneer de service wordt **geschaald in**. Als de gemiddelde belasting van alle partities van de service lager is dan deze waarde, wordt de service ingeschaald.
* _Bovenste belastingsdrempel_ is een waarde die bepaalt wanneer de service wordt **uitgeschaald.** Als de gemiddelde belasting van alle partities van de service hoger is dan deze waarde, wordt de service uitgeschaald.
* _Het schaalinterval_ bepaalt hoe vaak de trigger wordt gecontroleerd. Zodra de trigger is gecontroleerd, als schalen nodig is, wordt het mechanisme toegepast. Als schalen niet nodig is, wordt er geen actie ondernomen. In beide gevallen wordt trigger niet opnieuw gecontroleerd voordat het schaalinterval opnieuw verloopt.

Deze trigger kan zowel worden gebruikt met stateful en stateless services. Het enige mechanisme dat kan worden gebruikt met deze trigger is AddRemoveIncrementalNamedPartitionScalingMechanism. Wanneer de service wordt uitgeschaald, wordt een nieuwe partitie toegevoegd en wanneer de service wordt geschaald in een van de bestaande partities. Er zijn beperkingen die worden gecontroleerd wanneer de service wordt gemaakt of bijgewerkt en het maken/bijwerken van de service mislukt als niet aan deze voorwaarden wordt voldaan:
* Het benoemde partitieschema moet worden gebruikt voor de service.
* Partitienamen moeten opeenvolgende gehele getallen zijn, zoals "0", "1", ...
* De naam van de eerste partitie moet "0" zijn.

Als een service bijvoorbeeld in eerste instantie wordt gemaakt met drie partities, is de enige geldige mogelijkheid voor partitienamen '0', '1' en '2'.

De werkelijke automatische schalingbewerking die wordt uitgevoerd, respecteert ook dit naamgevingsschema:
* Als de huidige partities van de service de naam "0", "1" en "2" krijgen, wordt de partitie die wordt toegevoegd voor het uitschalen "3" genoemd.
* Als huidige partities van de service de naam "0", "1" en "2" krijgen, dan is de partitie die wordt verwijderd voor het inschalen partitie met de naam "2".

Hetzelfde als bij mechanisme dat schaling gebruikt door instanties toe te voegen of te verwijderen, zijn er drie parameters die bepalen hoe dit mechanisme wordt toegepast:
* _Schaaltoename_ bepaalt hoeveel partities worden toegevoegd of verwijderd wanneer het mechanisme wordt geactiveerd.
* _Het maximale aantal partities_ definieert de bovengrens voor schalen. Als het aantal partities van de service deze limiet bereikt, wordt de service niet uitgeschaald, ongeacht de belasting. Het is mogelijk om deze limiet weg te laten door de waarde van -1 op te geven, en in dat geval wordt de service zoveel mogelijk uitgeschroefd (de limiet is de werkelijke capaciteit van het cluster).
* _Minimumaantal instantie_ definieert de ondergrens voor schalen. Als het aantal partities van de service deze limiet bereikt, wordt de service niet geschaald, ongeacht de belasting.

> [!WARNING] 
> Wanneer AddRemoveIncrementalNamedPartitionScalingMechanism wordt gebruikt met stateful services, voegt of verwijdert Service Fabric partities **zonder melding of waarschuwing.** Het opnieuw verdelen van gegevens wordt niet uitgevoerd wanneer het schalingsmechanisme wordt geactiveerd. In het geval van scale-up werking, nieuwe partities zal leeg zijn, en in het geval van scale down operatie, **partitie zal worden verwijderd samen met alle gegevens die het bevat**.

## <a name="setting-auto-scaling-policy"></a>Beleid voor automatisch schalen instellen

### <a name="using-application-manifest"></a>Toepassingsmanifest gebruiken
``` xml
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>C# API's gebruiken
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedPartitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>Powershell gebruiken
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedPartitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>Automatisch schalen op basis van resources

Om de resourcemonitorservice te laten schalen op basis van werkelijke resources

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Er zijn twee statistieken die werkelijke fysieke resources vertegenwoordigen. Een van hen is servicefabric: / _CpuCores die de werkelijke cpu-gebruik vertegenwoordigen (dus 0,5 vertegenwoordigt de helft van een kern) en de andere wordt servicefabric: / _MemoryInMB die het geheugengebruik in DE's vertegenwoordigt.
ResourceMonitorService is verantwoordelijk voor het bijhouden van het cpu- en geheugengebruik van gebruikersservices. Deze dienst zal het gewogen voortschrijdend gemiddelde toepassen om rekening te houden met potentiële kortstondige pieken. Resourcemonitoring wordt ondersteund voor zowel gecontaineriseerde als niet-gecontaineriseerde toepassingen op Windows en voor containerized toepassingen op Linux. Automatisch schalen op resources is alleen ingeschakeld voor services die zijn geactiveerd in [het exclusieve procesmodel.](service-fabric-hosting-model.md#exclusive-process-model)

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [schaalbaarheid van toepassingen](service-fabric-concepts-scalability.md).
