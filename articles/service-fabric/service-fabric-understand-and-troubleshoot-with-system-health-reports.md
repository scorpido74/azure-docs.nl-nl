---
title: Problemen met systeemstatusrapporten oplossen
description: Beschrijft de statusrapporten die zijn verzonden door Azure Service Fabric-componenten en het gebruik ervan voor het oplossen van cluster- of toepassingsproblemen
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: a76ae803b1283ce50d2f4e259943ce5ffcf0274c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282014"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Systeemstatusrapporten gebruiken om fouten op te lossen
Azure Service Fabric-componenten bieden systeemstatusrapporten over alle entiteiten in het cluster direct uit de doos. Het [statusarchief](service-fabric-health-introduction.md#health-store) maakt en verwijdert entiteiten op basis van de systeemrapporten. Het organiseert ze ook in een hiërarchie die entiteitsinteracties vastlegt.

> [!NOTE]
> Lees meer bij Service Fabric [health model](service-fabric-health-introduction.md)om gezondheidsgerelateerde concepten te begrijpen.
> 
> 

Systeemstatusrapporten bieden inzicht in de functionaliteit van het cluster en de toepassing en vlagproblemen. Voor toepassingen en services controleren systeemstatusrapporten of entiteiten zijn geïmplementeerd en correct gedragen vanuit het perspectief van Service Fabric. De rapporten bieden geen statusbewaking van de bedrijfslogica van de service of detectie van processen die niet reageren. Gebruikersservices kunnen de gezondheidsgegevens verrijken met informatie die specifiek is voor hun logica.

> [!NOTE]
> Gezondheidsrapporten die door gebruikerswaakhonden worden verzonden, zijn pas zichtbaar *nadat* de systeemonderdelen een entiteit hebben gemaakt. Wanneer een entiteit wordt verwijderd, verwijdert het statusarchief automatisch alle statusrapporten die eraan zijn gekoppeld. Hetzelfde geldt wanneer een nieuwe instantie van de entiteit wordt gemaakt. Een voorbeeld hiervan is wanneer een nieuwe stateful persisted service replica-instantie wordt gemaakt. Alle rapporten die aan de oude instantie zijn gekoppeld, worden verwijderd en opgeschoond uit de winkel.
> 
> 

De rapporten van de systeemcomponent worden geïdentificeerd door de bron, die begint met het "**Systeem"** 'com.microsoft.intune.mam'. Waakhonden kunnen niet hetzelfde voorvoegsel voor hun bronnen gebruiken, omdat rapporten met ongeldige parameters worden afgewezen.

Laten we eens kijken naar een aantal systeemrapporten om te begrijpen wat hen triggers en om te leren hoe de potentiële problemen die zij vertegenwoordigen te corrigeren.

> [!NOTE]
> Service Fabric blijft rapporten toevoegen over interessevoorwaarden die de zichtbaarheid van wat er in het cluster en de toepassingen gebeurt, verbeteren. Bestaande rapporten kunnen worden uitgebreid met meer details om het probleem sneller op te lossen.
> 
> 

## <a name="cluster-system-health-reports"></a>Statusrapporten van het clustersysteem
De clusterstatusentiteit wordt automatisch gemaakt in het statusarchief. Als alles goed werkt, heeft het geen systeemrapport.

### <a name="neighborhood-loss"></a>Verlies van de buurt
**System.Federation** meldt een fout wanneer het een buurtverlies detecteert. Het rapport is afkomstig van afzonderlijke knooppunten en de knooppunt-id is opgenomen in de eigenschapsnaam. Als één buurt verloren gaat in de hele servicefabricring, u doorgaans twee gebeurtenissen verwachten die beide zijden van het rapport over de kloof vertegenwoordigen. Als er meer buurten verloren gaan, zijn er meer evenementen.

Het rapport geeft de time-out voor globale lease op als time-to-live (TTL). Het rapport is resent elke helft van de TTL duur zolang de voorwaarde actief blijft. De gebeurtenis wordt automatisch verwijderd wanneer deze verloopt. Gedrag verwijderen wanneer het is verlopen, zorgt ervoor dat het rapport correct is opgeschoond uit het gezondheidsarchief, zelfs als het rapportageknooppunt is uitgeschakeld.

* **SourceId**: System.Federation
* **Eigenschap**: Begint met **Buurt** en bevat knooppuntinformatie.
* **Volgende stappen**: Onderzoek waarom de buurt verloren is gegaan. Controleer bijvoorbeeld de communicatie tussen clusterknooppunten.

### <a name="rebuild"></a>Opnieuw bouwen

De FM-service (Failover Manager) beheert informatie over de clusterknooppunten. Wanneer FM zijn gegevens verliest en in gegevensverlies gaat, kan het niet garanderen dat het de meest bijgewerkte informatie over de clusterknooppunten heeft. In dit geval gaat het systeem door een reconstructie en verzamelt System.FM gegevens van alle knooppunten in het cluster om de status opnieuw op te bouwen. Soms, als gevolg van netwerken of knooppunt problemen, wederopbouw kan vast komen te zitten of vastgelopen. Hetzelfde kan gebeuren met de Failover Manager Master (FMM) service. De FMM is een stateless systeemservice die bijhoudt waar alle FM's zich in het cluster bevinden. De primaire FMM is altijd het knooppunt met de ID het dichtst bij 0. Als dat knooppunt wordt gedropt, wordt een herbouw geactiveerd.
Wanneer een van de vorige voorwaarden plaatsvindt, **worden System.FM** of **System.FMM** markeert via een foutrapport. Opnieuw opbouwen kan worden geplakt in een van de twee fasen:

* **Wachten op uitzending**: FM/FMM wacht op het antwoord van het uitzendingsbericht van de andere knooppunten.

  * **Volgende stappen:** Onderzoek of er een probleem is met de netwerkverbinding tussen knooppunten.
* **Wachten op knooppunten**: FM/FMM heeft al een broadcastantwoord ontvangen van de andere knooppunten en wacht op een antwoord van specifieke knooppunten. In het gezondheidsrapport worden de knooppunten vermeld waarvoor de FM/FMM op een reactie wacht.
   * **Volgende stappen**: Onderzoek de netwerkverbinding tussen de FM/FMM en de vermelde knooppunten. Onderzoek elk opgenomen knooppunt voor andere mogelijke problemen.

* **SourceID**: System.FM of System.FMM
* **Eigenschap**: Herbouwen.
* **Volgende stappen:** Onderzoek de netwerkverbinding tussen de knooppunten en de status van specifieke knooppunten die worden vermeld in de beschrijving van het gezondheidsrapport.

### <a name="seed-node-status"></a>Status seednode
**System.FM** rapporteert een waarschuwing op clusterniveau als sommige seedknooppunten niet in orde zijn. Seed-knooppunten zijn de knooppunten die de beschikbaarheid van het onderliggende cluster behouden. Deze knooppunten helpen om ervoor te zorgen dat het cluster blijft bestaan door leases met andere knooppunten op te zetten en als tiebreakers te dienen tijdens bepaalde soorten netwerkstoringen. Als een meerderheid van de seednodes in het cluster zijn uitgeschakeld en ze niet worden teruggebracht, wordt het cluster automatisch afgesloten. 

Een seed-knooppunt is ongezond als de knooppuntstatus Omlaag, Verwijderd of Onbekend is.
In het waarschuwingsrapport voor de status van het startknooppunt worden alle ongezonde zaadknooppunten weergegeven met gedetailleerde informatie.

* **SourceID**: System.FM
* **Eigenschap**: SeedNodeStatus
* **Volgende stappen:** Als deze waarschuwing in het cluster wordt weergegeven, volgt u onderstaande instructies om het te repareren: Voor clustermet Service Fabric-versie 6.5 of hoger: Voor het cluster ServiceFabric op Azure probeert Service Fabric het automatisch te wijzigen in een niet-seed-knooppunt. Om dit mogelijk te maken, moet u ervoor zorgen dat het aantal niet-zaadknooppunten in het primaire knooppunttype groter of gelijk is aan het aantal downseedknooppunten. Voeg indien nodig meer knooppunten toe aan het primaire knooppunttype om dit te bereiken.
Afhankelijk van de clusterstatus kan het enige tijd duren voordat het probleem is opgelost. Zodra dit is gebeurd, wordt het waarschuwingsrapport automatisch gewist.

Voor het zelfstandige cluster Service Fabric moeten alle seednodes gezond worden om het waarschuwingsrapport te wissen. Afhankelijk van waarom zaadknooppunten ongezond zijn, moeten verschillende acties worden ondernomen: als het zaadknooppunt down is, moeten gebruikers dat zaadknooppunt omhoog brengen; als het zaadknooppunt wordt verwijderd of onbekend is, moet dit zaadknooppunt [uit het cluster worden verwijderd.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes)
Het waarschuwingsrapport wordt automatisch gewist wanneer alle zaadknooppunten gezond worden.

Voor clusteruitvoering Service Fabric-versie ouder dan 6.5: In dit geval moet het waarschuwingsrapport handmatig worden gewist. **Gebruikers moeten ervoor zorgen dat alle zaadknooppunten gezond worden voordat het rapport wordt gewist:** als het startknooppunt down is, moeten gebruikers dat zaadknooppunt omhoog brengen;als het zaadknooppunt wordt verwijderd of onbekend is, moet dat zaadknooppunt uit het cluster worden verwijderd.
Nadat alle zaadknooppunten gezond worden, gebruikt u de opdracht volgende opdracht van Powershell om [het waarschuwingsrapport](https://docs.microsoft.com/powershell/module/servicefabric/send-servicefabricclusterhealthreport)te wissen:

```powershell
PS C:\> Send-ServiceFabricClusterHealthReport -SourceId "System.FM" -HealthProperty "SeedNodeStatus" -HealthState OK

## Node system health reports
System.FM, which represents the Failover Manager service, is the authority that manages information about cluster nodes. Each node should have one report from System.FM showing its state. The node entities are removed when the node state is removed. For more information, see [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### Node up/down
System.FM reports as OK when the node joins the ring (it's up and running). It reports an error when the node departs the ring (it's down, either for upgrading or simply because it has failed). The health hierarchy built by the health store acts on deployed entities in correlation with System.FM node reports. It considers the node a virtual parent of all deployed entities. The deployed entities on that node are exposed through queries if the node is reported as up by System.FM, with the same instance as the instance associated with the entities. When System.FM reports that the node is down or restarted, as a new instance, the health store automatically cleans up the deployed entities that can exist only on the down node or on the previous instance of the node.

* **SourceId**: System.FM
* **Property**: State.
* **Next steps**: If the node is down for an upgrade, it should come back up after it's been upgraded. In this case, the health state should switch back to OK. If the node doesn't come back or it fails, the problem needs more investigation.

The following example shows the System.FM event with a health state of OK for node up:

```powershell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Certificaat verloop
**System.FabricNode** meldt een waarschuwing wanneer certificaten die door het knooppunt worden gebruikt, bijna verlopen. Er zijn drie certificaten per knooppunt: **Certificate_cluster,** **Certificate_server**en **Certificate_default_client**. Wanneer de vervaldatum ten minste twee weken weg is, is de status van de rapportstatus OK. Wanneer de vervaldatum binnen twee weken is, is het rapporttype een waarschuwing. TTL van deze gebeurtenissen is oneindig en ze worden verwijderd wanneer een knooppunt het cluster verlaat.

* **SourceId**: System.FabricNode
* **Eigenschap**: Begint met **certificaat** en bevat meer informatie over het certificaattype.
* **Volgende stappen**: Werk de certificaten bij als ze bijna zijn verlopen.

### <a name="load-capacity-violation"></a>Schending van de laadcapaciteit
De Service Fabric Load Balancer meldt een waarschuwing wanneer het een schending van de capaciteit van het knooppunt detecteert.

* **SourceId**: System.PLB
* **Eigenschap:** Begint met **capaciteit**.
* **Volgende stappen**: Controleer de opgegeven statistieken en bekijk de huidige capaciteit op het knooppunt.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Foutopnoginheid van knooppuntcapaciteit voor resourcegovernance-statistieken
System.Hosting rapporteert een waarschuwing als gedefinieerde knooppuntcapaciteiten in het clustermanifest groter zijn dan de werkelijke knooppuntcapaciteiten voor resourcegovernance-statistieken (geheugen- en CPU-cores). Er verschijnt een statusrapport wanneer het eerste servicepakket dat [resourcegovernanceregisters](service-fabric-resource-governance.md) gebruikt op een opgegeven knooppunt.

* **SourceId**: System.Hosting
* **Eigenschap**: **ResourceGovernance**.
* **Volgende stappen**: dit probleem kan een probleem zijn omdat het regelen van servicepakketten niet wordt afgedwongen zoals verwacht en [resourcegovernance](service-fabric-resource-governance.md) niet goed werkt. Werk het clustermanifest bij met de juiste knooppuntcapaciteiten voor deze statistieken of geef ze niet op en laat Service Fabric automatisch beschikbare bronnen detecteren.

## <a name="application-system-health-reports"></a>Statusrapporten van het toepassingssysteem
System.CM, die de clusterbeheerservice vertegenwoordigt, is de autoriteit die informatie over een toepassing beheert.

### <a name="state"></a>Status
System.CM rapporten als OK wanneer de toepassing is gemaakt of bijgewerkt. Het informeert de health store wanneer de toepassing wordt verwijderd, zodat het kan worden verwijderd uit de winkel.

* **SourceId**: System.CM
* **Eigendom**: Staat.
* **Volgende stappen:** Als de toepassing is gemaakt of bijgewerkt, moet deze het statusrapport Clusterbeheer bevatten. Controleer anders de status van de toepassing door een query uit te geven. Gebruik bijvoorbeeld de PowerShell-cmdlet **Get-ServiceFabricApplication -ApplicationName-applicatieName** *applicationName*.

In het volgende voorbeeld wordt de statusgebeurtenis op de **toepassing fabric:/WordCount** weergegeven:

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Statusrapporten van het servicesysteem
System.FM, dat de Failover Manager-service vertegenwoordigt, is de autoriteit die informatie over services beheert.

### <a name="state"></a>Status
System.FM rapporteert als OK wanneer de service is gemaakt. De entiteit wordt verwijderd uit het statusarchief wanneer de service wordt verwijderd.

* **SourceId**: System.FM
* **Eigendom**: Staat.

In het volgende voorbeeld wordt de statusgebeurtenis op de **servicefabric weergegeven:/WordCount/WordCountWebService:**

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Fout in servicecorrelatie
**System.PLB** meldt een fout wanneer wordt gedetecteerd dat het bijwerken van een service is gecorreleerd met een andere service die een affiniteitsketen maakt. Het rapport wordt gewist wanneer een succesvolle update plaatsvindt.

* **SourceId**: System.PLB
* **Eigenschap**: **ServiceBeschrijving**.
* **Volgende stappen**: Controleer de gecorreleerde servicebeschrijvingen.

## <a name="partition-system-health-reports"></a>Statusrapporten van het partitiesysteem
System.FM, dat de Failover Manager-service vertegenwoordigt, is de autoriteit die informatie over servicepartities beheert.

### <a name="state"></a>Status
System.FM rapporteert als OK wanneer de partitie is gemaakt en in orde is. De entiteit wordt verwijderd uit het statusarchief wanneer de partitie wordt verwijderd.

Als de partitie lager is dan het minimum aantal replica's, wordt een fout gemeld. Als de partitie niet onder het minimum aantal replica's ligt, maar wel onder het aantal doelreplica's ligt, wordt een waarschuwing gerapporteerd. Als de partitie quorumverlies heeft, System.FM een fout.

Andere opmerkelijke gebeurtenissen zijn een waarschuwing wanneer de herconfiguratie langer duurt dan verwacht en wanneer de build langer duurt dan verwacht. De verwachte tijden voor de build en herconfiguratie zijn configureerbaar op basis van de servicescenario's. Als een service bijvoorbeeld een terabyte aan status heeft, zoals Azure SQL Database, duurt de build langer dan voor een service met een kleine hoeveelheid status.

* **SourceId**: System.FM
* **Eigendom**: Staat.
* **Volgende stappen:** Als de status niet in orde is, is het mogelijk dat sommige replica's niet zijn gemaakt, geopend of gepromoveerd naar primair of secundair correct. 

Als de beschrijving quorumverlies beschrijft, helpt het onderzoeken van het gedetailleerde gezondheidsrapport voor replica's die zijn afgebroken en ze weer omhoog brengen, om de partitie weer online te brengen.

Als in de beschrijving een partitie wordt beschreven die vastzit in [de herconfiguratie,](service-fabric-concepts-reconfiguration.md)biedt het statusrapport over de primaire replica aanvullende informatie.

Voor andere System.FM statusrapporten, zouden er rapporten over de replica's of de partitie of de dienst van andere systeemcomponenten zijn. 

In de volgende voorbeelden worden enkele van deze rapporten beschreven. 

In het volgende voorbeeld ziet u een gezonde partitie:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

In het volgende voorbeeld ziet u de status van een partitie die onder het aantal doelreplica's ligt. De volgende stap is om de partitiebeschrijving te krijgen, die laat zien hoe deze is geconfigureerd: **MinReplicaSetSize** is drie en **TargetReplicaSetSize** is zeven. Dan krijg je het aantal knooppunten in het cluster, die in dit geval is vijf. In dit geval kunnen er dus geen twee replica's worden geplaatst, omdat het doelaantal replica's hoger is dan het aantal beschikbare knooppunten.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

In het volgende voorbeeld ziet u de status van een partitie die vastzit in een herconfiguratie omdat de gebruiker het annuleringstoken niet in de **RunAsync-methode** eert. Het onderzoeken van het gezondheidsrapport van een replica die als primair (P) is gemarkeerd, kan helpen om verder in te zoomen op het probleem.

```powershell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
In dit statusrapport wordt de status weergegeven van de replica's van de partitie die opnieuw worden geconfigureerd: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Voor elke replica bevat het gezondheidsrapport:
- Vorige configuratierol
- Huidige configuratierol
- [Replicastatus](service-fabric-concepts-replica-lifecycle.md)
- Knooppunt waarop de replica wordt uitgevoerd
- Replica-id

In een geval als het voorbeeld is nader onderzoek nodig. Onderzoek de status van elke afzonderlijke replica `Primary` te `Secondary` beginnen met de replica's gemarkeerd als en (131482789658160654 en 131482789688598467) in het vorige voorbeeld.

### <a name="replica-constraint-violation"></a>Schending van de beperking van replica's
**System.PLB** rapporteert een waarschuwing als het een schending van de beperking van de replica detecteert en niet alle partitiereplica's kan plaatsen. De rapportdetails geven aan welke beperkingen en eigenschappen de plaatsing van de replica verhinderen.

* **SourceId**: System.PLB
* **Eigenschap:** Begint met **schending van replicabeperkingen**.

## <a name="replica-system-health-reports"></a>Statusrapporten van het replicasysteem
**System.RA**, dat de component van de herconfiguratieagent vertegenwoordigt, is de bevoegdheid voor de replicastatus.

### <a name="state"></a>Status
System.RA rapporteert OK wanneer de replica is gemaakt.

* **SourceId**: System.RA
* **Eigendom**: Staat.

In het volgende voorbeeld ziet u een gezonde replica:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
Deze eigenschap wordt gebruikt om waarschuwingen of fouten aan te geven wanneer u een replica probeert te openen, een replica wilt sluiten of een replica van de ene rol naar de andere wilt overgangen. Zie [Levenscyclus van replica voor](service-fabric-concepts-replica-lifecycle.md)meer informatie. De fouten kunnen uitzonderingen zijn die gedurende deze periode uit de API-aanroepen of crashes van het servicehostproces worden gegooid. Voor fouten als gevolg van API-aanroepen van C#-code voegt Service Fabric de uitzondering en stacktrace toe aan het statusrapport.

Deze gezondheidswaarschuwingen worden verhoogd nadat u de actie een aantal keren lokaal opnieuw hebt geprobeerd (afhankelijk van het beleid). Service Fabric probeert de actie opnieuw uit tot een maximale drempelwaarde. Nadat de maximale drempel is bereikt, kan het proberen om te handelen om de situatie te corrigeren. Deze poging kan ertoe leiden dat deze waarschuwingen worden gewist als het opgeeft op de actie op dit knooppunt. Als een replica bijvoorbeeld niet op een knooppunt wordt geopend, wordt een statuswaarschuwing voor Service Fabric ingeschakeld. Als de replica nog steeds niet wordt geopend, werkt Service Fabric zelf te herstellen. Deze actie kan inhouden dat u dezelfde bewerking probeert op een ander knooppunt. Deze poging zorgt ervoor dat de waarschuwing die wordt verhoogd voor deze replica wordt gewist. 

* **SourceId**: System.RA
* **Eigenschap**: **ReplicaOpenStatus**, **ReplicaCloseStatus**en **ReplicaChangeRoleStatus**.
* **Volgende stappen:** Onderzoek de servicecode of crashdumps om te bepalen waarom de bewerking mislukt.

In het volgende voorbeeld ziet u de `TargetInvocationException` status van een replica die uit de open methode gooit. De beschrijving bevat het punt van falen, **IStatefulServiceReplica.Open**, het uitzonderingstype **TargetInvocationException**en het stacktrace.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

In het volgende voorbeeld ziet u een replica die voortdurend crasht tijdens het sluiten:

```powershell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Herconfiguratie
Deze eigenschap wordt gebruikt om aan te geven wanneer een replica die een [herconfiguratie](service-fabric-concepts-reconfiguration.md) uitvoert, detecteert dat de herconfiguratie is vastgelopen of vastloopt. Dit statusrapport bevindt zich mogelijk op de replica waarvan de huidige rol primair is, behalve in het geval van een primaire swapconfiguratie, waar deze mogelijk op de replica staat die wordt gedegradeerd van primair naar actief secundair.

De herconfiguratie kan worden geplakt om een van de volgende redenen:

- Een actie op de lokale replica, dezelfde replica als die welke de herconfiguratie uitvoert, wordt niet voltooid. In dit geval kan het onderzoeken van de gezondheidsrapporten over deze replica van andere componenten, System.RAP of System.RE, aanvullende informatie opleveren.

- Een actie wordt niet voltooid op een externe replica. Replica's waarvoor acties in behandeling zijn, worden weergegeven in het gezondheidsrapport. Verder onderzoek moet worden gedaan naar gezondheidsrapporten voor die externe replica's. Er kunnen ook communicatieproblemen zijn tussen dit knooppunt en het externe knooppunt.

In zeldzame gevallen kan de herconfiguratie vastzitten als gevolg van communicatie of andere problemen tussen dit knooppunt en de Failover Manager-service.

* **SourceId**: System.RA
* **Eigenschap**: Herconfiguratie.
* **Volgende stappen:** Onderzoek lokale of externe replica's, afhankelijk van de beschrijving van het gezondheidsrapport.

In het volgende voorbeeld ziet u een statusrapport waarin een herconfiguratie vastzit op de lokale replica. In dit voorbeeld is het te wijten aan een service die het annuleringstoken niet nakomt.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

In het volgende voorbeeld ziet u een statusrapport waarin een herconfiguratie vastzit in afwachting van een antwoord van twee externe replica's. In dit voorbeeld zijn er drie replica's in de partitie, waaronder de huidige primaire. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Dit rapport over de status laat zien dat de herconfiguratie vastzit in afwachting van een antwoord van twee replica's: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Voor elke replica wordt de volgende informatie gegeven:
- Vorige configuratierol
- Huidige configuratierol
- [Replicastatus](service-fabric-concepts-replica-lifecycle.md)
- Knooppunt-id
- Replica-id

De blokkering van de herconfiguratie deblokkeren:
- De **down** replica's moeten worden opgevoed. 
- De **ingebouwde replica's** moeten de build en overgang naar gereed voltooien.

### <a name="slow-service-api-call"></a>Slow service API-aanroepen
**System.RAP** en **System.Replicator** melden een waarschuwing als een aanroep naar de servicecode van de gebruiker langer duurt dan de geconfigureerde tijd. De waarschuwing wordt gewist wanneer het gesprek is voltooid.

* **SourceId**: System.RAP of System.Replicator
* **Eigenschap:** de naam van de langzame API. De beschrijving geeft meer details over de tijd dat de API in behandeling is.
* **Volgende stappen**: Onderzoek waarom het gesprek langer duurt dan verwacht.

In het volgende voorbeeld wordt de statusgebeurtenis van System.RAP weergegeven voor een betrouwbare service die het annuleringstoken in **RunAsync**niet nakomt:

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

De eigenschap en tekst geven aan welke API vast kwam te zitten. De volgende stappen te nemen voor verschillende vastgelopen API's zijn verschillend. Elke API op de *IStatefulServiceReplica* of *IStatelessServiceInstance* is meestal een bug in de servicecode. In de volgende sectie wordt beschreven hoe deze zich vertalen naar het [Reliable Services-model:](service-fabric-reliable-services-lifecycle.md)

- **IStatefulServiceReplica.Open:** Deze waarschuwing geeft `CreateServiceInstanceListeners`aan `ICommunicationListener.OpenAsync`dat een oproep `OnOpenAsync` tot , of indien overschreven, vast zit.

- **IStatefulServiceReplica.Close** en **IStatefulServiceReplica.Abort:** Het meest voorkomende geval is een service `RunAsync`die het annuleringstoken niet nakomt. Het kan ook `ICommunicationListener.CloseAsync`zijn dat , `OnCloseAsync` of als overschreven, vast zit.

- **IStatefulServiceReplica.ChangeRole(S)** en **IStatefulServiceReplica.ChangeRole(N)**: Het meest voorkomende geval is een `RunAsync`service die het annuleringstoken niet nakomt. In dit scenario is de beste oplossing om de replica opnieuw op te starten.

- **IStatefulServiceReplica.ChangeRole(P)**: Het meest voorkomende geval is dat `RunAsync`de service geen taak heeft geretourneerd van .

Andere API-aanroepen die vast kunnen komen te zitten, bevinden zich op de **IReplicator-interface.** Bijvoorbeeld:

- **IReplicator.CatchupReplicaSet:** Deze waarschuwing geeft een van de twee dingen. Er zijn onvoldoende up replica's. Als u wilt zien of dit het geval is, bekijkt u de replicastatus van de replica's in de partitie of het System.FM-statusrapport voor een vastgelopen herconfiguratie. Of de replica's zijn niet erkennen operaties. De PowerShell-cmdlet `Get-ServiceFabricDeployedReplicaDetail` kan worden gebruikt om de voortgang van alle replica's te bepalen. Het probleem ligt bij `LastAppliedReplicationSequenceNumber` replica's waarvan `CommittedSequenceNumber` de waarde achter de waarde van de primaire ligt.

- **IReplicator.BuildReplica(\<Remote ReplicaId>)**: Deze waarschuwing geeft een probleem in het bouwproces. Zie [Levenscyclus van replica voor](service-fabric-concepts-replica-lifecycle.md)meer informatie. Het kan te wijten zijn aan een verkeerde configuratie van het replicatoradres. Zie [Stateful Reliable Services configureren](service-fabric-reliable-services-configuration.md) en Resources opgeven in een [servicemanifest](service-fabric-service-manifest-resources.md)voor meer informatie. Het kan ook een probleem zijn op het externe knooppunt.

### <a name="replicator-system-health-reports"></a>Statusrapporten van het Replicator-systeem
**Replicatiewachtrij vol:**
**System.Replicator** meldt een waarschuwing wanneer de replicatiewachtrij vol is. Op de primaire basis wordt de replicatiewachtrij meestal vol omdat een of meer secundaire replica's de bewerkingen langzaam bevestigen. Op de secundaire, dit gebeurt meestal wanneer de service is traag toe te passen van de bewerkingen. De waarschuwing wordt gewist wanneer de wachtrij niet meer vol is.

* **SourceId**: System.Replicator
* **Eigenschap**: **PrimaryReplicationQueueStatus** of **SecondaryReplicationQueueStatus**, afhankelijk van de replicarol.
* **Volgende stappen**: Als het rapport zich op de primaire basis bevindt, controleert u de verbinding tussen de knooppunten in het cluster. Als alle verbindingen in orde zijn, kan er ten minste één langzame secundaire met een hoge schijflatentie zijn om bewerkingen toe te passen. Als het rapport zich op het secundaire bevindt, controleert u eerst het schijfgebruik en de prestaties op het knooppunt. Controleer vervolgens de uitgaande verbinding van het langzame knooppunt naar de primaire.

**RemoteReplicatorConnectionStatus:**
**System.Replicator** op de primaire replica rapporteert een waarschuwing wanneer de verbinding met een secundaire (externe) replicator niet in orde is. Het adres van de externe replicator wordt weergegeven in het bericht van het rapport, waardoor het handiger is om te detecteren of de verkeerde configuratie is doorgegeven of dat er netwerkproblemen zijn tussen de replicators.

* **SourceId**: System.Replicator
* **Eigenschap:** **RemoteReplicatorConnectionStatus**.
* **Volgende stappen**: Controleer het foutbericht en controleer of het externe replicatoradres correct is geconfigureerd. Als de externe replicator bijvoorbeeld wordt geopend met het luisteradres 'localhost', is deze niet bereikbaar vanaf de buitenkant. Als het adres er correct uitziet, controleert u de verbinding tussen het primaire knooppunt en het externe adres om mogelijke netwerkproblemen te vinden.

### <a name="replication-queue-full"></a>Replicatiewachtrij vol
**System.Replicator** meldt een waarschuwing wanneer de replicatiewachtrij vol is. Op de primaire basis wordt de replicatiewachtrij meestal vol omdat een of meer secundaire replica's de bewerkingen langzaam bevestigen. Op de secundaire, dit gebeurt meestal wanneer de service is traag toe te passen van de bewerkingen. De waarschuwing wordt gewist wanneer de wachtrij niet meer vol is.

* **SourceId**: System.Replicator
* **Eigenschap**: **PrimaryReplicationQueueStatus** of **SecondaryReplicationQueueStatus**, afhankelijk van de replicarol.

### <a name="slow-naming-operations"></a>Langzame naamgevingsbewerkingen
**System.NamingService** rapporteert de status van de primaire replica wanneer een naamgevingsbewerking langer duurt dan acceptabel. Voorbeelden van naamgevingsbewerkingen zijn [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) of [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Meer methoden zijn te vinden onder FabricClient. Ze zijn bijvoorbeeld te vinden onder [servicebeheermethoden](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) of [methoden voor vastgoedbeheer.](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient)

> [!NOTE]
> De naamgevingsservice lost servicenamen op naar een locatie in het cluster. Gebruikers kunnen het gebruiken om servicenamen en -eigenschappen te beheren. Het is een service fabric partitie-voortdurende service. Een van de partities vertegenwoordigt de *Authority Owner*, die metadata bevat over alle Service Fabric namen en services. De namen van de servicestructuur worden toegewezen aan verschillende partities, *naameigenaarpartities* genaamd, zodat de service uitbreidbaar is. Lees meer over [de naamgevingsservice](service-fabric-architecture.md).
> 
> 

Wanneer een naamgevingsbewerking langer duurt dan verwacht, wordt de bewerking gemarkeerd met een waarschuwingsrapport over de primaire replica van de naamgevingsservicepartitie die de bewerking bedient. Als de bewerking is voltooid, wordt de waarschuwing gewist. Als de bewerking is voltooid met een fout, bevat het gezondheidsrapport details over de fout.

* **SourceId**: System.NamingService
* **Eigenschap**: Begint met het voorvoegsel "**Duration_**" en identificeert de langzame werking en de naam van de servicestructuur waarop de bewerking wordt toegepast. Als bijvoorbeeld service maken bij **naamfabric:/MyApp/MyService** te lang duurt, is de eigenschap **Duration_AOCreateService.fabric:/MyApp/MyService**. "AO" wijst op de rol van de naamgevingspartitie voor deze naam en bewerking.
* **Volgende stappen:** Controleer waarom de naamgevingsbewerking mislukt. Elke bewerking kan verschillende onderliggende oorzaken hebben. De verwijderservice kan bijvoorbeeld vastzitten. De service kan vast zitten omdat de toepassingshost blijft crashen op een knooppunt als gevolg van een gebruikersbug in de servicecode.

In het volgende voorbeeld wordt een bewerking voor een service maken weergegeven. De bewerking duurde langer dan de geconfigureerde duur. "AO" probeert en stuurt werk naar "NEE." "NEE" heeft de laatste bewerking voltooid met TIMEOUT. In dit geval is dezelfde replica primair voor zowel de "AO" als "NO" rollen.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>GeïmplementeerdToepassingssysteemstatusrapporten
**System.Hosting** is de autoriteit op geïmplementeerde entiteiten.

### <a name="activation"></a>Activering
System.Hosting rapporteert als OK wanneer een toepassing is geactiveerd op het knooppunt. Anders wordt er een fout gemeld.

* **SourceId**: System.Hosting
* **Eigenschap**: **Activering**, inclusief de uitrolversie.
* **Volgende stappen**: Als de toepassing niet in orde is, onderzoekt u waarom de activering is mislukt.

In het volgende voorbeeld wordt een geslaagde activering weergegeven:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Download
System.Hosting meldt een fout als het downloaden van het toepassingspakket mislukt.

* **SourceId**: System.Hosting
* **Eigenschap**: **Download**, inclusief de uitrolversie.
* **Volgende stappen:** Onderzoek waarom de download is mislukt op het knooppunt.

## <a name="deployedservicepackage-system-health-reports"></a>GeïmplementeerdservicePakket-systeemstatusrapporten
**System.Hosting** is de autoriteit op geïmplementeerde entiteiten.

### <a name="service-package-activation"></a>Activering van servicepakket
System.Hosting rapporteert als OK als de activering van het servicepakket op het knooppunt succesvol is. Anders wordt er een fout gemeld.

* **SourceId**: System.Hosting
* **Eigenschap**: Activering.
* **Volgende stappen:** Onderzoek waarom de activering is mislukt.

### <a name="code-package-activation"></a>Activering van codepakket
System.Hosting rapporteert als OK voor elk codepakket als de activering succesvol is. Als de activering mislukt, wordt een waarschuwing gerapporteerd als geconfigureerd. Als **CodePackage** niet wordt geactiveerd of beëindigd met een fout die groter is dan de geconfigureerde **CodePackageHealthErrorThreshold,** wordt een fout gemeld. Als een servicepakket meerdere codepakketten bevat, wordt voor elk pakket een activeringsrapport gegenereerd.

* **SourceId**: System.Hosting
* **Eigenschap**: Gebruikt het voorvoegsel **CodePackageActivation** en bevat de naam van het codepakket en het invoerpunt als *CodePackageActivation:CodePackageName:SetupEntryPoint/EntryPoint*. **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Registratie van servicetype
System.Hosting rapporteert als OK als het servicetype is geregistreerd. Er wordt een fout gemeld als de registratie niet op tijd is uitgevoerd, zoals geconfigureerd met **ServiceTypeRegistrationTimeout**. Als de runtime is gesloten, wordt het servicetype niet geregistreerd vanaf het knooppunt en wordt een waarschuwing gerapporteerd.

* **SourceId**: System.Hosting
* **Eigenschap**: gebruikt het voorvoegsel **ServiceTypeRegistration** en bevat de naam van het servicetype. **ServiceTypeRegistration:FileStoreServiceType**.

In het volgende voorbeeld wordt een geïmplementeerd servicepakket met een gezonde geïmplementeerde service weergegeven:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Download
System.Hosting meldt een fout als het downloaden van het servicepakket mislukt.

* **SourceId**: System.Hosting
* **Eigenschap**: **Download**, inclusief de uitrolversie.
* **Volgende stappen:** Onderzoek waarom de download is mislukt op het knooppunt.

### <a name="upgrade-validation"></a>Upgradevalidatie
System.Hosting meldt een fout als de validatie tijdens de upgrade mislukt of als de upgrade mislukt op het knooppunt.

* **SourceId**: System.Hosting
* **Eigenschap:** Gebruikt het voorvoegsel **FabricUpgradeValidation** en bevat de upgradeversie.
* **Beschrijving**: Wijst op de ondervonden fout.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Ongedefinieerde knooppuntcapaciteit voor resourcegovernance-statistieken
System.Hosting rapporteert een waarschuwing als knooppuntcapaciteiten niet zijn gedefinieerd in het clustermanifest en de configuratie voor automatische detectie is uitgeschakeld. Service Fabric verhoogt een gezondheidswaarschuwing wanneer het servicepakket dat [resourcegovernanceregisters](service-fabric-resource-governance.md) gebruikt op een opgegeven knooppunt.

* **SourceId**: System.Hosting
* **Eigenschap**: **ResourceGovernance**.
* **Volgende stappen**: De beste manier om dit probleem op te lossen is door het clustermanifest te wijzigen om automatische detectie van beschikbare resources mogelijk te maken. Een andere manier is om het clustermanifest bij te werken met de correct opgegeven knooppuntcapaciteiten voor deze statistieken.

## <a name="next-steps"></a>Volgende stappen
* [Gezondheidsrapporten van Service Fabric weergeven](service-fabric-view-entities-aggregated-health.md)

* [Hoe de servicestatus te rapporteren en te controleren](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Services lokaal controleren en een diagnose uitvoeren](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Upgrade van servicefabric-toepassingen](service-fabric-application-upgrade.md)

