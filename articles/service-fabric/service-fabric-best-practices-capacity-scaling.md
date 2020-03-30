---
title: Capaciteitsplanning en -schaling voor Azure Service Fabric
description: Aanbevolen procedures voor het plannen en schalen van Service Fabric-clusters en -toepassingen.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: bf228e17ca24df9833f96f0c6fd3ef232cdf7ae6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258991"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Capaciteitsplanning en -schaling voor Azure Service Fabric

Voordat u een Azure Service Fabric-cluster of schaalcomputeresources maakt die uw cluster hosten, is het belangrijk om de capaciteit te plannen. Zie [De clustercapaciteit van de servicestructuur plannen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)voor meer informatie over de planning voor capaciteit. Zie overwegingen voor schaalbaarheid van [servicefabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)voor verdere best-practicerichtlijnen voor clusterschaalbaarheid.

Naast het overwegen van knooppunttype en clusterkenmerken, moet u verwachten dat schaalbewerkingen langer dan een uur in beslag nemen voor een productieomgeving. Deze overweging geldt ongeacht het aantal VM's dat u toevoegt.

## <a name="autoscaling"></a>Automatisch schalen
U moet schaalbewerkingen uitvoeren via Azure Resource Manager-sjablonen, omdat dit de beste manier is om [resourceconfiguraties als code]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)te behandelen. 

Als u automatisch schalen door virtuele machineschaalsets gebruikt, bepaalt u uw versie-resourcebeheersjabloon onjuist voor het nauwkeurig definiëren van uw instantietellingen voor virtuele machineschaalsets. Onnauwkeurige definitie verhoogt het risico dat toekomstige implementaties onbedoelde schaalbewerkingen veroorzaken. In het algemeen moet u automatisch schalen gebruiken als:

* Het implementeren van uw Resource Manager-sjablonen met de juiste opgegeven capaciteit biedt geen ondersteuning voor uw use case.
     
   Naast handmatigschalen u een [pijplijn voor continue integratie en levering configureren in Azure DevOps Services met behulp van Azure-implementatieprojecten voor resources.](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts) Deze pijplijn wordt vaak geactiveerd door een logische app die gebruikmaakt van virtuele machine prestatiestatistieken die zijn opgevraagd uit de [Azure Monitor REST API.](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough) De pijplijn wordt effectief automatisch geschaald op basis van de gewenste statistieken, terwijl u optimaliseert voor Resource Manager-sjablonen.
* U hoeft slechts één knooppunt voor een virtuele machineschaal horizontaal te schalen.
   
   Als u drie of meer knooppunten tegelijk wilt uitschalen, moet u [een cluster servicestructuur schalen door een virtuele machineschaalset toe te voegen.](virtual-machine-scale-set-scale-node-type-scale-out.md) Het is het veiligst om virtuele machineschaalsets horizontaal in te schalen en uit te schalen, één knooppunt tegelijk.
* U hebt silver-betrouwbaarheid of hoger voor uw Service Fabric-cluster en Silver-duurzaamheid of hoger op elke schaal waarop u regels voor automatisch schalen configureert.
  
   De minimale capaciteit voor regels voor automatisch schalen moet gelijk zijn aan of groter zijn dan vijf virtuele machine-exemplaren. Het moet ook gelijk zijn aan of groter zijn dan het minimum van uw betrouwbaarheidslaag voor uw primaire knooppunttype.

> [!NOTE]
> De servicefabric is een stateful servicefabric:/System/InfastructureService/<NODE_TYPE_NAME> draait op elk knooppunttype met zilver of een hogere duurzaamheid. Het is de enige systeemservice die wordt ondersteund om in Azure uit te voeren op een van uw typen clustersknooppunt.

## <a name="vertical-scaling-considerations"></a>Overwegingen voor verticale schaling

[Voor verticaal schalen](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) van een knooppunttype in Azure Service Fabric zijn een aantal stappen en overwegingen vereist. Bijvoorbeeld:

* Het cluster moet in orde zijn voordat het wordt geschaald. Anders destabiliseer je het cluster verder.
* Het silver-duurzaamheidsniveau of hoger is vereist voor alle clusterknooppunttypen van Service Fabric die stateful services hosten.

> [!NOTE]
> Het primaire knooppunttype dat stateful Service Fabric-systeemservices host, moet het silver-duurzaamheidsniveau of hoger zijn. Nadat u Silver-duurzaamheid hebt ingeschakeld, zullen clusterbewerkingen zoals upgrades, het toevoegen of verwijderen van knooppunten trager zijn, omdat het systeem optimaliseert voor gegevensveiligheid boven de snelheid van bewerkingen.

Verticaal schalen van een virtuele machineschaalset is een destructieve bewerking. Schaal in plaats daarvan het cluster horizontaal door een nieuwe schaalset toe te voegen met de gewenste SKU. Migreer vervolgens uw services naar de gewenste SKU om een veilige verticale schalingbewerking te voltooien. Het wijzigen van een virtuele machine schaal set resource SKU is een destructieve bewerking, omdat het opnieuw afbeeldingen van uw hosts, die alle lokaal aanhoudende status verwijdert.

Uw cluster gebruikt [eigenschappen en plaatsingsbeperkingen voor servicefabricen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) om te bepalen waar de services van uw toepassing worden gehost. Wanneer u het primaire knooppunttype verticaal schaalt, geeft `"nodeTypeRef"`u identieke eigenschapswaarden aan voor . U deze waarden vinden in de Service Fabric-extensie voor virtuele machineschaalsets. 

In het volgende fragment van een resourcemanagersjabloon worden de eigenschappen weergegeven die u declareert. Het heeft dezelfde waarde voor de nieuw ingerichte schaalsets waarop u schaalt en wordt alleen ondersteund als een tijdelijke service met een status voor uw cluster.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Laat uw cluster niet draaien met meerdere schaalsets die dezelfde `nodeTypeRef` eigenschapswaarde langer gebruiken dan nodig is om een succesvolle verticale schalingbewerking te voltooien.
>
> Valideer bewerkingen in testomgevingen altijd voordat u wijzigingen in de productieomgeving probeert. Standaard hebben clustersysteemservices servicestructuur een plaatsingsbeperking voor alleen het primaire knooppunttype van het doel.

Als de knooppunteigenschappen en plaatsingsbeperkingen zijn gedeclareerd, voeren we de volgende stappen één VM-instantie tegelijk uit. Hierdoor kunnen de systeemservices (en uw stateful services) op een elegante manier worden afgesloten op de VM-instantie die u verwijdert terwijl nieuwe replica's elders worden gemaakt.

1. Voer vanuit PowerShell `Disable-ServiceFabricNode` `RemoveNode` met de bedoeling uit om het knooppunt dat u verwijdert uit te schakelen. Verwijder het knooppunttype met het hoogste getal. Als u bijvoorbeeld een cluster met zes plaatsen hebt, verwijdert u de instantie 'MyNodeType_5'.
2. Uitvoeren `Get-ServiceFabricNode` om ervoor te zorgen dat het knooppunt is overgegaan naar uitgeschakeld. Zo niet, wacht dan tot het knooppunt is uitgeschakeld. Dit kan een paar uur duren voor elk knooppunt. Ga niet verder totdat het knooppunt is overgegaan naar uitgeschakeld.
3. Verlaag het aantal VM's met één in dat knooppunttype. De hoogste VM-instantie wordt nu verwijderd.
4. Herhaal stap 1 tot en met 3 indien nodig, maar schaal nooit het aantal exemplaren in de primaire knooppunttypen lager dan wat de betrouwbaarheidslaag garandeert. Zie [De clustercapaciteit van de servicestructuur plannen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) voor een lijst met aanbevolen exemplaren.
5. Zodra alle VM's zijn verdwenen (weergegeven als 'Omlaag') wordt de fabric:/System/InfrastructureService/[knooppuntnaam] een foutstatus weergegeven. Vervolgens u de clusterbron bijwerken om het knooppunttype te verwijderen. U de implementatie van de ARM-sjabloon gebruiken of de clusterbron bewerken via de [Azure-bronbeheer.](https://resources.azure.com) Hiermee wordt een clusterupgrade gestart waarmee de fabric-/System/InfrastructureService/[nodetype-service die in de foutstatus is uitgevoerd, wordt verwijderd.
 6. Daarna u de VMScaleSet optioneel verwijderen, maar ziet u de knooppunten nog steeds als 'Omlaag' uit de weergave Service Fabric Explorer. De laatste stap zou zijn `Remove-ServiceFabricNodeState` om ze schoon te maken met commando.

## <a name="horizontal-scaling"></a>Horizontaal schalen

U horizontaal schalen [handmatig](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) of [programmatisch](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)doen.

> [!NOTE]
> Als u een knooppunttype schaalt met de duurzaamheid van Zilver of Goud, wordt de schaling traag.

### <a name="scaling-out"></a>Uitschalen

Schaal een cluster servicestructuur uit door het aantal instanties voor een bepaalde virtuele machineschaalset te verhogen. U programmatisch schalen `AzureClient` met behulp van en de ID voor de gewenste schaalset om de capaciteit te vergroten.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Als u handmatig wilt uitschalen, werkt u de capaciteit in de eigenschap SKU van de gewenste bron voor de gewenste virtuele [machineschaalset bij.](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Schalen in

Schalen in te nemen vereist meer aandacht dan uitschalen. Bijvoorbeeld:

* Service Fabric-systeemservices worden uitgevoerd in het primaire knooppunttype in uw cluster. Sluit nooit het aantal exemplaren voor dat knooppunttype af of schaal deze niet omlaag, zodat u minder exemplaren hebt dan wat de betrouwbaarheidslaag garandeert. 
* Voor een stateful service hebt u een bepaald aantal knooppunten nodig die altijd in staat zijn om de beschikbaarheid te behouden en de status van uw service te behouden. U hebt minimaal een aantal knooppunten nodig die gelijk zijn aan het aantal doelreplica's van de partitie of service.

Voer de volgende stappen uit om handmatig in te schalen:

1. Voer vanuit PowerShell `Disable-ServiceFabricNode` `RemoveNode` met de bedoeling uit om het knooppunt dat u verwijdert uit te schakelen. Verwijder het knooppunttype met het hoogste getal. Als u bijvoorbeeld een cluster met zes plaatsen hebt, verwijdert u de instantie 'MyNodeType_5'.
2. Uitvoeren `Get-ServiceFabricNode` om ervoor te zorgen dat het knooppunt is overgegaan naar uitgeschakeld. Zo niet, wacht dan tot het knooppunt is uitgeschakeld. Dit kan een paar uur duren voor elk knooppunt. Ga niet verder totdat het knooppunt is overgegaan naar uitgeschakeld.
3. Verlaag het aantal VM's met één in dat knooppunttype. De hoogste VM-instantie wordt nu verwijderd.
4. Herhaal stap 1 tot en met 3 indien nodig totdat u de gewenste capaciteit indient. Schaal het aantal exemplaren in de primaire knooppunttypen niet af tot minder dan wat de betrouwbaarheidslaag garandeert. Zie [De clustercapaciteit van de servicestructuur plannen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) voor een lijst met aanbevolen exemplaren.

Als u handmatig wilt schalen, werkt u de capaciteit in de eigenschap SKU van de gewenste bron voor [virtuele machineschaalinstel.](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

U moet het knooppunt voorbereiden op afsluiten om programmatisch in te schalen. Zoek het knooppunt dat moet worden verwijderd (het knooppunt met de hoogste instantie). Bijvoorbeeld:

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Deactiveer en verwijder het knooppunt `FabricClient` met`client` dezelfde instantie (in dit`instanceIdString` geval) en knooppuntinstantie (in dit geval) die u in de vorige code hebt gebruikt:

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> Wanneer u een cluster schaalt, wordt de verwijderde knooppunt/VM-instantie in een ongezonde status weergegeven in Service Fabric Explorer. Zie Gedrag dat u [kunt waarnemen in Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer)voor een uitleg van dit gedrag. U kunt:
> * Bel de [opdracht Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) met de juiste naam van het knooppunt.
> * Implementeer de [helpertoepassing Voor automatische schaal servicefabric](https://github.com/Azure/service-fabric-autoscale-helper/) op uw cluster. Deze toepassing zorgt ervoor dat de verkleinde knooppunten worden gewist uit Service Fabric Explorer.

## <a name="reliability-levels"></a>Betrouwbaarheidsniveaus

Het [betrouwbaarheidsniveau](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) is een eigenschap van uw Service Fabric-clusterbron. Het kan niet anders worden geconfigureerd voor afzonderlijke knooppunttypen. Hiermee wordt de replicatiefactor van de systeemservices voor het cluster bepaald en wordt een instelling op clusterbronniveau ingesteld. 

Het betrouwbaarheidsniveau bepaalt het minimumaantal knooppunten dat uw primaire knooppunttype moet hebben. De betrouwbaarheidslaag kan de volgende waarden aannemen:

* Platinum: Voert de systeemservices uit met een aantal doelreplica's van zeven en negen zaadknooppunten.
* Goud: Hiermee wordt de systeemservices uitgevoerd met een aantal setsdien voor doelreplica's van zeven en zeven zaadknooppunten.
* Zilver: Hiermee wordt de systeemservices uitgevoerd met een aantal setsteken van vijf en vijf zaadknooppunten.
* Brons: Voert de systeemservices uit met een doelreplicasettelling van drie en drie zaadknooppunten.

Het minimale aanbevolen betrouwbaarheidsniveau is Zilver.

Het betrouwbaarheidsniveau is ingesteld in het gedeelte eigenschappen van de [bron Microsoft.ServiceFabric/clusters,](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)zoals deze:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Duurzaamheidsniveaus

> [!WARNING]
> Node types die draaien met Bronze duurzaamheid verkrijgen _geen privileges._ Infrastructuurtaken die van invloed zijn op uw stateless workloads worden niet gestopt of vertraagd, wat van invloed kan zijn op uw workloads. 
>
> Gebruik bronzeduurzaamheid alleen voor knooppunttypen die stateloze workloads uitvoeren. Voer zilver of hoger uit voor productieworkloads om de consistentie van de status te garanderen. Kies de juiste betrouwbaarheid op basis van de richtlijnen in de [capaciteitsplanningsdocumentatie.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

Het duurzaamheidsniveau moet in twee bronnen worden vastgesteld. Een daarvan is het uitbreidingsprofiel van de bron van de [virtuele machineschaalset:](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

De andere bron `nodeTypes` staat onder in de [bron Microsoft.ServiceFabric/clusters:](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Volgende stappen

* Een cluster maken op VM's of computers met Windows Server: [Service Fabric-clustercreatie voor Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Een cluster maken op VM's of computers met Linux: [een Linux-cluster maken.](service-fabric-cluster-creation-via-portal.md)
* Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
