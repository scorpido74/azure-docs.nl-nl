---
title: Clusterresourcemanager - Beheerintegratie
description: Een overzicht van de integratiepunten tussen de Cluster Resource Manager en Service Fabric Management.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 50751c7d23797a597dc5e2d209c1e3eecf6f7a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258744"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integratie van clusterresourcebeheer met clusterbeheer van Service Fabric
De Service Fabric Cluster Resource Manager voert geen upgrades uit in Service Fabric, maar is wel betrokken. De eerste manier waarop de Cluster Resource Manager helpt bij het beheer is door het bijhouden van de gewenste status van het cluster en de services erin. De clusterbronbeheer verzendt statusrapporten wanneer het cluster niet in de gewenste configuratie kan worden geplaatst. Als er bijvoorbeeld onvoldoende capaciteit is, stuurt clusterresourcebeheer statuswaarschuwingen en fouten die het probleem aangeven. Een ander stukje integratie heeft te maken met hoe upgrades werken. De Cluster Resource Manager wijzigt zijn gedrag enigszins tijdens upgrades.  

## <a name="health-integration"></a>Gezondheidsintegratie
De Cluster Resource Manager houdt voortdurend de regels bij die u hebt gedefinieerd voor het plaatsen van uw services. Het volgt ook de resterende capaciteit voor elke statistiek op de knooppunten en in het cluster en in het cluster als geheel. Als het niet aan deze regels kan voldoen of als er onvoldoende capaciteit is, worden gezondheidswaarschuwingen en fouten uitgezonden. Als een knooppunt bijvoorbeeld meer dan capaciteit heeft en clusterresourcebeheer probeert de situatie op te lossen door services te verplaatsen. Als het niet kan corrigeren van de situatie geeft een waarschuwing voor de gezondheid aangeeft welk knooppunt is meer dan de capaciteit, en voor welke statistieken.

Een ander voorbeeld van de gezondheidswaarschuwingen van Resource Manager zijn schendingen van plaatsingsbeperkingen. Als u bijvoorbeeld een plaatsingsbeperking (zoals `“NodeColor == Blue”`) hebt gedefinieerd en Resourcemanager een schending van die beperking detecteert, wordt een statuswaarschuwing afgegeven. Dit geldt voor aangepaste beperkingen en de standaardbeperkingen (zoals de beperkingen van het foutdomein en het upgradedomein).

Hier is een voorbeeld van een dergelijk gezondheidsrapport. In dit geval is het gezondheidsrapport voor een van de partities van de systeemservice. Het statusbericht geeft aan dat de replica's van die partitie tijdelijk zijn verpakt in te weinig upgradedomeinen.

```posh
PS C:\Users\User > Get-ServiceFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Hier is wat deze gezondheid bericht vertelt ons is:

1. Alle replica's zelf zijn gezond: Elk heeft AggregatedHealthState: Ok
2. De distributiebeperking Voor het upgradedomein wordt momenteel geschonden. Dit betekent dat een bepaald upgradedomein meer replica's van deze partitie heeft dan het zou moeten.
3. Welk knooppunt bevat de replica die de overtreding veroorzaakt. In dit geval is het het knooppunt met de naam "Node.8"
4. Of er momenteel een upgrade plaatsvindt voor deze partitie ("Momenteel upgraden -- false")
5. Het distributiebeleid voor deze dienst: "Distributiebeleid -- Verpakking". Dit wordt geregeld `RequireDomainDistribution` door het [plaatsingsbeleid](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). "Verpakking" geeft aan dat in dit geval DomainDistribution _niet_ nodig was, dus we weten dat het plaatsingsbeleid niet is opgegeven voor deze service. 
6. Toen het rapport gebeurde - 8/10/2015 19:13:02

Informatie zoals deze bevoegdheden waarschuwingen dat brand in de productie om u te laten weten dat er iets mis is gegaan en wordt ook gebruikt om te detecteren en te stoppen slechte upgrades. In dit geval willen we zien of we kunnen achterhalen waarom de Resource Manager de replica's in het upgradedomein moest inpakken. Meestal is het verpakken van voorbijgaande aard omdat de knooppunten in de andere upgradedomeinen bijvoorbeeld zijn uitgeschakeld.

Stel dat de Cluster Resource Manager probeert bepaalde services te plaatsen, maar er zijn geen oplossingen die werken. Wanneer services niet kunnen worden geplaatst, is dit meestal om een van de volgende redenen:

1. Een tijdelijke voorwaarde heeft het onmogelijk gemaakt om deze service-instantie of replica correct te plaatsen
2. De plaatsingsvereisten van de service zijn onsatiste.

In deze gevallen helpen statusrapporten van clusterbronbeheer u te bepalen waarom de service niet kan worden geplaatst. We noemen dit proces de beperking eliminatie sequentie. Tijdens het, het systeem loopt door de geconfigureerde beperkingen die van invloed zijn op de service en registreert wat ze elimineren. Op deze manier wanneer services niet kunnen worden geplaatst, u zien welke knooppunten zijn geëlimineerd en waarom.

## <a name="constraint-types"></a>Beperkingstypen
Laten we praten over elk van de verschillende beperkingen in deze gezondheidsrapporten. U ziet gezondheidsberichten met betrekking tot deze beperkingen wanneer replica's niet kunnen worden geplaatst.

* **ReplicaExclusionStatic** en **ReplicaExclusionDynamic:** deze beperkingen geven aan dat een oplossing is afgewezen omdat twee serviceobjecten van dezelfde partitie op hetzelfde knooppunt moeten worden geplaatst. Dit is niet toegestaan omdat het falen van dat knooppunt dan overdreven invloed zou hebben op die partitie. ReplicaExclusionStatic en ReplicaExclusionDynamic zijn bijna dezelfde regel en de verschillen doen er niet echt toe. Als u een beperkingseliminatiereeks ziet die de beperking ReplicaExclusionStatic of ReplicaExclusionDynamic bevat, denkt de Clusterresourcemanager dat er niet genoeg knooppunten zijn. Dit vereist de resterende oplossingen om deze ongeldige plaatsingen te gebruiken die zijn afgekeurd. De andere beperkingen in de reeks zullen ons meestal vertellen waarom knooppunten in de eerste plaats worden geëlimineerd.
* **Plaatsingsbeperking:** als u dit bericht ziet, betekent dit dat we sommige knooppunten hebben verwijderd omdat ze niet overeenkomen met de plaatsingsbeperkingen van de service. We traceren de momenteel geconfigureerde plaatsingsbeperkingen als onderdeel van dit bericht. Dit is normaal als u een plaatsingsbeperking hebt gedefinieerd. Als plaatsingsbeperking echter ten onrechte ervoor zorgt dat te veel knooppunten worden geëlimineerd, merkt u dit.
* **Knooppuntcapaciteit:** deze beperking betekent dat de clusterbronbeheer de replica's niet op de aangegeven knooppunten kan plaatsen, omdat ze daardoor over capaciteit heen kunnen worden geplaatst.
* **Affiniteit:** deze beperking geeft aan dat we de replica niet op de betreffende knooppunten kunnen plaatsen, omdat dit een schending van de affiniteitsbeperking zou veroorzaken. Meer informatie over affiniteit is in [dit artikel](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** en **UpgradeDomain:** Deze beperking elimineert knooppunten als het plaatsen van de replica op de aangegeven knooppunten zou leiden tot verpakking in een bepaalde fout of upgrade domein. Verschillende voorbeelden van deze beperking worden gepresenteerd in het onderwerp over [fout en upgrade domein beperkingen en daaruit voortvloeiende gedrag](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation:** Normaal gesproken ziet u deze beperking niet als het verwijderen van knooppunten uit de oplossing, omdat deze standaard wordt uitgevoerd als een optimalisatie. De gewenste locatiebeperking is ook aanwezig tijdens upgrades. Tijdens de upgrade wordt het gebruikt om services terug te verplaatsen naar waar ze waren toen de upgrade begon.

## <a name="blocklisting-nodes"></a>Knooppunten voor blokkering
Een ander statusbericht dat de clusterbronbeheerrapporten bevat, is wanneer knooppunten worden geblokkeerd. U blocklisting zien als een tijdelijke beperking die automatisch voor u wordt toegepast. Knooppunten worden geblokkeerd wanneer ze herhaalde fouten ondervinden bij het starten van exemplaren van dat servicetype. Knooppunten worden geblokkeerd op basis van per service-type. Een knooppunt kan worden geblokkeerd voor het ene servicetype, maar niet voor een ander. 

Je ziet blocklisting vaak intrappen tijdens de ontwikkeling: sommige bug zorgt ervoor dat je servicehost crasht bij het opstarten. Service Fabric probeert de servicehost een paar keer te maken en de storing blijft zich voordoen. Na een paar pogingen wordt het knooppunt geblokkeerd en probeert de Cluster Resource Manager de service elders te maken. Als die fout op meerdere knooppunten blijft plaatsvinden, is het mogelijk dat alle geldige knooppunten in het cluster uiteindelijk worden geblokkeerd. Blocklisting kan ook zoveel knooppunten verwijderen dat niet genoeg de service met succes kan starten om aan de gewenste schaal te voldoen. U ziet doorgaans extra fouten of waarschuwingen van clusterbronbeheer die aangeven dat de service zich onder het gewenste aantal replica's of instance bevindt, evenals statusberichten die aangeven wat de fout is die leidt tot de blokkering in de eerste Plaats.

Blocklisting is geen permanente voorwaarde. Na een paar minuten wordt het knooppunt uit de blocklist verwijderd en kan Service Fabric de services op dat knooppunt opnieuw activeren. Als services blijven mislukken, wordt het knooppunt opnieuw geblokkeerd voor dat servicetype. 

### <a name="constraint-priorities"></a>Beperkingen prioriteiten

> [!WARNING]
> Het wijzigen van de beperkingen wordt niet aanbevolen en kan aanzienlijke nadelige gevolgen hebben voor uw cluster. De onderstaande informatie wordt verstrekt ter referentie van de standaardbeperkingsprioriteiten en hun gedrag. 
>

Met al deze beperkingen, u gedacht hebben Hey - ik denk dat de beperkingen van het foutdomein het belangrijkste ding in mijn systeem zijn. Om ervoor te zorgen dat de beperking van het foutdomein niet wordt geschonden, ben ik bereid om andere beperkingen te schenden."

Beperkingen kunnen worden geconfigureerd met verschillende prioriteitsniveaus. Dit zijn:

   - "hard" (0)
   - "zacht" (1)
   - "optimalisatie" (2)
   - "uit" (-1). 
   
De meeste beperkingen zijn standaard geconfigureerd als harde beperkingen.

Het wijzigen van de prioriteit van beperkingen is ongewoon. Er zijn momenten geweest waar constraint prioriteiten die nodig zijn om te veranderen, meestal om te werken rond een andere bug of gedrag dat van invloed was op de omgeving. Over het algemeen heeft de flexibiliteit van de infrastructuur met prioriteitsprioriteit zeer goed gewerkt, maar het is niet vaak nodig. Meestal zit alles op hun standaardprioriteiten. 

De prioriteitsniveaus betekenen niet dat een bepaalde beperking _zal_ worden geschonden, noch dat het altijd zal worden voldaan. Beperkingenprioriteiten definiëren een volgorde waarin beperkingen worden afgedwongen. Prioriteiten definiëren de afwegingen wanneer het onmogelijk is om aan alle beperkingen te voldoen. Meestal kunnen alle beperkingen worden voldaan, tenzij er iets anders aan de hand in het milieu. Enkele voorbeelden van scenario's die leiden tot beperkingsschendingen zijn conflicterende beperkingen of grote aantallen gelijktijdige fouten.

In geavanceerde situaties u de beperkingsprioriteiten wijzigen. Stel dat u ervoor wilt zorgen dat affiniteit altijd wordt geschonden wanneer dat nodig is om problemen met knooppuntcapaciteit op te lossen. Om dit te bereiken, u de prioriteit van de affiniteitsbeperking instellen op 'zacht' (1) en de capaciteitsbeperking op 'hard' (0) laten staan.

De standaardprioriteitwaarden voor de verschillende beperkingen worden opgegeven in de volgende config:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

via ClusterConfig.json voor zelfstandige implementaties of Template.json voor azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Beperkingen voor foutdomeinen en upgradedomeinen
De Cluster Resource Manager wil services verspreid houden over fout- en upgradedomeinen. Het modelleert dit als een beperking in de engine van de Cluster Resource Manager. Voor meer informatie over hoe ze worden gebruikt en hun specifieke gedrag, bekijk het artikel over [clusterconfiguratie](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

Het is mogelijk dat clusterbronbeheer een paar replica's moet inpakken in een upgradedomein om upgrades, fouten of andere schendingen van beperkingen aan te pakken. Verpakking in fout of upgrade domeinen gebeurt normaal gesproken alleen wanneer er verschillende storingen of andere churn in het systeem te voorkomen dat de juiste plaatsing. Als u wilt voorkomen dat verpakking, zelfs `RequireDomainDistribution` tijdens deze situaties, u gebruik maken van de [plaatsing beleid](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Houd er rekening mee dat dit van invloed kan zijn op de beschikbaarheid en betrouwbaarheid van de service als neveneffect, dus denk er goed rekening mee.

Als de omgeving correct is geconfigureerd, worden alle beperkingen volledig gerespecteerd, zelfs tijdens upgrades. Het belangrijkste is dat de Cluster Resource Manager is oplet op uw beperkingen. Wanneer het een overtreding detecteert, meldt het onmiddellijk en probeert het probleem te verhelpen.

## <a name="the-preferred-location-constraint"></a>De gewenste locatiebeperking
De voorkeurslocatiebeperking is een beetje anders, omdat deze twee verschillende toepassingen heeft. Een gebruik van deze beperking is tijdens toepassingsupgrades. De Cluster resource Manager beheert deze beperking automatisch tijdens upgrades. Het wordt gebruikt om ervoor te zorgen dat wanneer upgrades zijn voltooid, replica's terugkeren naar hun oorspronkelijke locaties. Het andere gebruik van de beperking Voorkeurslocatie is voor het [ `PreferredPrimaryDomain` plaatsingsbeleid](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Beide zijn optimalisaties, en dus de PreferredLocation beperking is de enige beperking ingesteld op "Optimalisatie" standaard.

## <a name="upgrades"></a>Upgrades
De Cluster Resource Manager helpt ook tijdens applicatie- en clusterupgrades, waarbij het twee taken heeft:

* ervoor zorgen dat de regels van het cluster niet worden aangetast
* proberen om de upgrade soepel te laten verlopen

### <a name="keep-enforcing-the-rules"></a>Blijf de regels handhaven
Het belangrijkste om bewust te zijn van is dat de regels - de strikte beperkingen zoals plaatsing beperkingen en capaciteiten - nog steeds worden afgedwongen tijdens upgrades. Plaatsingsbeperkingen zorgen ervoor dat uw workloads alleen worden uitgevoerd waar ze mogen, zelfs tijdens upgrades. Wanneer services sterk beperkt zijn, kunnen upgrades langer duren. Wanneer de service of het knooppunt waarop het wordt uitgevoerd, wordt neergehaald voor een update, kunnen er weinig opties zijn voor waar het naartoe kan.

### <a name="smart-replacements"></a>Slimme vervangingen
Wanneer een upgrade wordt gestart, maakt Resource Manager een momentopname van de huidige indeling van het cluster. Als elk upgradedomein wordt voltooid, wordt geprobeerd de services die zich in dat upgradedomein bevonden, terug te sturen naar hun oorspronkelijke indeling. Op deze manier zijn er maximaal twee overgangen voor een service tijdens de upgrade. Er is een beweging uit het getroffen knooppunt en een beweging terug in. Als u het cluster of de service teruggeeft naar hoe het was voordat de upgrade is uitgevoerd, zorgt dit er ook voor dat de upgrade geen invloed heeft op de lay-out van het cluster. 

### <a name="reduced-churn"></a>Minder churn
Een ander ding dat gebeurt tijdens upgrades is dat de Cluster Resource Manager schakelt balanceren. Het voorkomen van balanceren voorkomt onnodige reacties op de upgrade zelf, zoals het verplaatsen van services naar knooppunten die zijn geleegd voor de upgrade. Als de upgrade in kwestie een clusterupgrade is, wordt het hele cluster niet in evenwicht gebracht tijdens de upgrade. Beperkingscontroles blijven actief, alleen beweging op basis van de proactieve afweging van statistieken is uitgeschakeld.

### <a name="buffered-capacity--upgrade"></a>Upgrade van gebufferde capaciteit &
Over het algemeen wilt u dat de upgrade wordt voltooid, zelfs als het cluster is beperkt of bijna vol is. Het beheer van de capaciteit van het cluster is nog belangrijker tijdens upgrades dan normaal. Afhankelijk van het aantal upgradedomeinen moet tussen de 5 en 20 procent van de capaciteit worden gemigreerd als de upgrade door het cluster rolt. Dat werk moet ergens heen. Dit is waar het begrip [gebufferde capaciteiten](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) nuttig is. De gebufferde capaciteit wordt gerespecteerd tijdens de normale werking. De Clusterresourcemanager kan knooppunten vullen tot hun totale capaciteit (de buffer verbruiken) indien nodig tijdens upgrades.

## <a name="next-steps"></a>Volgende stappen
* Begin vanaf het begin en [krijg een inleiding tot de Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
