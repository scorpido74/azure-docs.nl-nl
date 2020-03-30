---
title: Gezondheidsmonitoring in Service Fabric
description: Een inleiding tot het Azure Service Fabric-statusbewakingsmodel, dat monitoring van het cluster en de toepassingen en services biedt.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 473aa2b9a74193a857390cd3e29b2b559b6084d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282417"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Inleiding tot de statuscontrole Service Fabric
Azure Service Fabric introduceert een gezondheidsmodel dat uitgebreide, flexibele en uitbreidbare statusevaluatie en -rapportage biedt. Het model maakt near-real-time monitoring van de status van het cluster en de services die erin worden uitgevoerd. U eenvoudig gezondheidsinformatie verkrijgen en potentiële problemen corrigeren voordat ze trapsgewijs werken en enorme uitval veroorzaken. In het typische model verzenden services rapporten op basis van hun lokale weergaven en wordt die informatie samengevoegd om een algemene weergave op clusterniveau te bieden.

Service Fabric-componenten gebruiken dit rijke gezondheidsmodel om hun huidige status te rapporteren. U hetzelfde mechanisme gebruiken om de status van uw toepassingen te rapporteren. Als u investeert in hoogwaardige gezondheidsrapportage die uw aangepaste omstandigheden vastlegt, u problemen voor uw lopende toepassing veel gemakkelijker detecteren en oplossen.

> [!NOTE]
> We zijn begonnen met de gezondheid subsysteem om een behoefte aan bewaakte upgrades aan te pakken. Service Fabric biedt bewaakte applicatie- en clusterupgrades die volledige beschikbaarheid, geen downtime en minimale tot geen tussenkomst van de gebruiker garanderen. Om deze doelen te bereiken, controleert de upgrade de status op basis van geconfigureerd upgradebeleid. Een upgrade kan alleen doorgaan wanneer de gezondheid de gewenste drempels respecteert. Anders wordt de upgrade automatisch teruggedraaid of onderbroken om beheerders een kans te geven om de problemen op te lossen. Zie [dit artikel](service-fabric-application-upgrade.md)voor meer informatie over toepassingsupgrades.
> 
> 

## <a name="health-store"></a>Gezondheidswinkel
Het gezondheidsarchief houdt gezondheidsgerelateerde informatie over entiteiten in het cluster bij voor eenvoudig ophalen en evalueren. Het wordt geïmplementeerd als een Service Fabric aanhoudende stateful service om een hoge beschikbaarheid en schaalbaarheid te garanderen. De statusopslag maakt deel uit van de **fabric:/System-toepassing** en is beschikbaar wanneer het cluster actief is.

## <a name="health-entities-and-hierarchy"></a>Gezondheidsentiteiten en hiërarchie
De statusentiteiten zijn ingedeeld in een logische hiërarchie die interacties en afhankelijkheden tussen verschillende entiteiten vastlegt. De statusopslag bouwt automatisch statusentiteiten en hiërarchie op basis van rapporten die zijn ontvangen van onderdelen van Service Fabric.

De statusentiteiten weerspiegelen de entiteiten Service Fabric. (De **entiteit statustoepassing** komt bijvoorbeeld overeen met een toepassingsinstantie die in het cluster is geïmplementeerd, terwijl **de entiteit van het statusknooppunt** overeenkomt met een clusterknooppunt servicestructuur.) De statushiërarchie legt de interacties van de systeementiteiten vast en is de basis voor geavanceerde gezondheidsevaluatie. U meer te weten komen over de belangrijkste Service Fabric-concepten in [het technische overzicht van Service Fabric.](service-fabric-technical-overview.md) Zie [Service Fabric-toepassingsmodel](service-fabric-application-model.md)voor meer informatie over toepassingen.

Met de statusentiteiten en hiërarchie kunnen het cluster en de toepassingen effectief worden gerapporteerd, gedebugged en gecontroleerd. Het gezondheidsmodel biedt een nauwkeurige, *gedetailleerde* weergave van de status van de vele bewegende stukken in het cluster.

![Gezondheidsentiteiten.][1]
De statusentiteiten, georganiseerd in een hiërarchie op basis van bovenliggende-onderliggende relaties.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

De gezondheidsinstanties zijn:

* **Cluster**. Vertegenwoordigt de status van een cluster van servicefabric. Clusterstatusrapporten beschrijven voorwaarden die van invloed zijn op het hele cluster. Deze voorwaarden zijn van invloed op meerdere entiteiten in het cluster of het cluster zelf. Op basis van de aandoening, kan de verslaggever niet beperken het probleem tot een of meer ongezonde kinderen. Voorbeelden hiervan zijn de hersenen van de clustersplitsing als gevolg van netwerkpartitionering of communicatieproblemen.
* **Knooppunt.** Vertegenwoordigt de status van een servicefabricknooppunt. Node health reports beschrijven voorwaarden die van invloed zijn op de node functionaliteit. Ze zijn meestal van invloed op alle geïmplementeerde entiteiten die erop worden uitgevoerd. Voorbeelden hiervan zijn knooppunt uit schijfruimte (of andere machinebrede eigenschappen, zoals geheugen, verbindingen) en wanneer een knooppunt is uitgeschakeld. De entiteit knooppunt wordt geïdentificeerd met de naam van het knooppunt (tekenreeks).
* **Toepassing**. Vertegenwoordigt de status van een toepassingsinstantie die in het cluster wordt uitgevoerd. Toepassingsstatusrapporten beschrijven voorwaarden die van invloed zijn op de algehele status van de toepassing. Ze kunnen niet worden beperkt tot individuele kinderen (services of geïmplementeerde toepassingen). Voorbeelden hiervan zijn de end-to-end interactie tussen verschillende services in de toepassing. De toepassingsentiteit wordt geïdentificeerd door de toepassingsnaam (URI).
* **Service**. Vertegenwoordigt de status van een service die in het cluster wordt uitgevoerd. Rapporten over servicestatus beschrijven voorwaarden die van invloed zijn op de algehele status van de service. De verslaggever kan het probleem niet beperken tot een ongezonde partitie of replica. Voorbeelden hiervan zijn een serviceconfiguratie (zoals poort of externe bestandsshare) die problemen veroorzaakt voor alle partities. De serviceentiteit wordt geïdentificeerd door de servicenaam (URI).
* **Partitie**. Vertegenwoordigt de status van een servicepartitie. Partitiestatusrapporten beschrijven voorwaarden die van invloed zijn op de hele replicaset. Voorbeelden hiervan zijn wanneer het aantal replica's onder het aantal objecten ligt en wanneer een partitie quorumverlies heeft. De partitieentiteit wordt geïdentificeerd door de partitie-id (GUID).
* **Replica**. Vertegenwoordigt de status van een stateful servicereplica of een serviceinstantie zonder staat. De replica is de kleinste eenheid waar waakhonden en systeemcomponenten over kunnen rapporteren voor een toepassing. Voor stateful services bevatten voorbeelden een primaire replica die bewerkingen niet kan repliceren naar secondaries en langzame replicatie. Een stateloze instantie kan ook rapporteren wanneer de resources opraken of verbindingsproblemen hebben. De replicaentiteit wordt geïdentificeerd door de partitie-id (GUID) en de replica- of instantie-id (lang).
* **Geïmplementeerde toepassing**. Hiermee geeft u de status weer van een *toepassing die op een knooppunt wordt uitgevoerd.* Geïmplementeerde toepassingsstatusrapporten beschrijven voorwaarden die specifiek zijn voor de toepassing op het knooppunt en die niet kunnen worden beperkt tot servicepakketten die op hetzelfde knooppunt zijn geïmplementeerd. Voorbeelden hiervan zijn fouten wanneer het toepassingspakket niet kan worden gedownload op dat knooppunt en problemen met het instellen van toepassingsbeveiligingsprincipals op het knooppunt. De geïmplementeerde toepassing wordt geïdentificeerd aan de andere plaats (URI) en de naam van het knooppunt (tekenreeks).
* **GeïmplementeerdServicepakket**. Vertegenwoordigt de status van een servicepakket dat wordt uitgevoerd op een knooppunt in het cluster. Het beschrijft voorwaarden die specifiek zijn voor een servicepakket die geen invloed hebben op de andere servicepakketten op hetzelfde knooppunt voor dezelfde toepassing. Voorbeelden hiervan zijn een codepakket in het servicepakket dat niet kan worden gestart en een configuratiepakket dat niet kan worden gelezen. Het geïmplementeerde servicepakket wordt geïdentificeerd aan de andere inschattingsnaam (URI), knooppuntnaam (tekenreeks), naam van het servicemanifest (tekenreeks) en activerings-ID (tekenreeks) van het servicepakket.

De granulariteit van het gezondheidsmodel maakt het gemakkelijk om problemen op te sporen en te corrigeren. Als een service bijvoorbeeld niet reageert, is het mogelijk om te melden dat de toepassingsinstantie niet in orde is. Rapportage op dat niveau is echter niet ideaal, omdat het probleem mogelijk niet van invloed is op alle services binnen die toepassing. Het rapport moet worden toegepast op de ongezonde service of op een specifieke onderliggende partitie, als meer informatie naar die partitie verwijst. De gegevens worden automatisch door de hiërarchie gebladerd en een ongezonde partitie wordt zichtbaar gemaakt op service- en toepassingsniveau. Deze aggregatie helpt om de oorzaak van het probleem sneller te lokaliseren en op te lossen.

De statushiërarchie bestaat uit bovenliggende-onderliggende relaties. Een cluster bestaat uit knooppunten en toepassingen. Toepassingen hebben services en geïmplementeerde toepassingen. Geïmplementeerde toepassingen hebben servicepakketten geïmplementeerd. Services hebben partities en elke partitie heeft een of meer replica's. Er is een speciale relatie tussen knooppunten en geïmplementeerde entiteiten. Een ongezond knooppunt zoals gerapporteerd door de component van het autoriteitssysteem, de Failover Manager-service, is van invloed op de geïmplementeerde toepassingen, servicepakketten en replica's die erop zijn geïmplementeerd.

De statushiërarchie geeft de laatste status van het systeem weer op basis van de meest recente gezondheidsrapporten, die bijna realtime informatie zijn.
Interne en externe waakhonden kunnen rapporteren over dezelfde entiteiten op basis van toepassingsspecifieke logica of aangepaste bewaakte omstandigheden. Gebruikersrapporten bestaan naast de systeemrapporten.

Plan om te investeren in het rapporteren en reageren op gezondheid tijdens het ontwerp van een grote cloudservice. Deze investering vooraf maakt de service gemakkelijker te debuggen, te controleren en te bedienen.

## <a name="health-states"></a>Gezondheidstoestanden
Service fabric gebruikt drie statussen om te beschrijven of een entiteit al dan niet in orde is: OK, waarschuwing en fout. Elk rapport dat naar het gezondheidsarchief wordt verzonden, moet een van deze statussen opgeven. Het resultaat van de gezondheidsevaluatie is een van deze staten.

De mogelijke [gezondheidstoestanden](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) zijn:

* **Ok**. De entiteit is gezond. Er zijn geen bekende problemen gemeld over het of haar kinderen (indien van toepassing).
* **Waarschuwing**. De entiteit heeft een aantal problemen, maar kan nog steeds correct functioneren. Er zijn bijvoorbeeld vertragingen, maar ze veroorzaken nog geen functionele problemen. In sommige gevallen kan de waarschuwingstoestand zichzelf herstellen zonder tussenkomst van buitenaf. In deze gevallen, gezondheidsrapporten verhogen het bewustzijn en bieden inzicht in wat er gaande is. In andere gevallen kan de waarschuwingstoestand zonder tussenkomst van de gebruiker uitlopen tot een ernstig probleem.
* **Fout**. De entiteit is ongezond. Er moet actie worden ondernomen om de status van de entiteit vast te stellen, omdat deze niet goed kan functioneren.
* **Onbekend**. De entiteit bestaat niet in de statusopslag. Dit resultaat kan worden verkregen uit de gedistribueerde query's die resultaten van meerdere componenten samenvoegen. De query lijst met knooppunten wordt bijvoorbeeld naar **FailoverManager,** **Clustermanager**en **HealthManager ;** query van de toepassingslijst worden opgevraagd naar **ClusterManager** en **HealthManager**. Deze query's voegen resultaten van meerdere systeemcomponenten samen. Als een andere systeemcomponent een entiteit retourneert die niet aanwezig is in het statusarchief, heeft het samengevoegde resultaat een onbekende status. Een entiteit is niet in de winkel omdat statusrapporten nog niet zijn verwerkt of de entiteit is opgeschoond na verwijdering.

## <a name="health-policies"></a>Gezondheidsbeleid
Het gezondheidsarchief past gezondheidsbeleid toe om te bepalen of een entiteit gezond is op basis van de rapporten en de kinderen.

> [!NOTE]
> Gezondheidsbeleid kan worden gespecificeerd in het clustermanifest (voor cluster- en knooppuntstatusbeoordeling) of in het toepassingsmanifest (voor toepassingsevaluatie en een van de kinderen). Gezondheidsevaluatieverzoeken kunnen ook worden doorgegeven in aangepaste gezondheidsevaluatiebeleid, dat alleen voor die evaluatie wordt gebruikt.
> 
> 

Standaard hanteert Service Fabric strikte regels (alles moet in orde zijn) voor de hiërarchische relatie tussen bovenliggend-en onderliggende status. Als zelfs een van de kinderen heeft een ongezonde gebeurtenis, de ouder wordt beschouwd als ongezond.

### <a name="cluster-health-policy"></a>Clusterstatusbeleid
Het [clusterstatusbeleid](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) wordt gebruikt om de status van de clusterstatus en de status van knooppunt te evalueren. Het beleid kan worden gedefinieerd in het clustermanifest. Als deze niet aanwezig is, wordt het standaardbeleid (nul getolereerde fouten) gebruikt.
Het clusterstatusbeleid bevat:

* [OverweegWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Hiermee geeft u op of waarschuwingsstatusrapporten tijdens de statusbeoordeling als fouten moeten worden behandeld. Standaard: onwaar.
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Hiermee geeft u het maximale getolereerde percentage toepassingen op dat ongezond kan zijn voordat het cluster als fout wordt beschouwd.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Hiermee geeft u het maximaal getolereerde percentage knooppunten op dat ongezond kan zijn voordat het cluster als fout wordt beschouwd. In grote clusters zijn sommige knooppunten altijd down of out voor reparaties, dus dit percentage moet worden geconfigureerd om dat te tolereren.
* [ApplicationTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). De beleidskaart voor statusbeleid van het toepassingstype kan worden gebruikt tijdens de evaluatie van de clusterstatus om speciale toepassingstypen te beschrijven. Standaard worden alle toepassingen in een groep geplaatst en geëvalueerd met MaxPercentUnhealthyApplications. Als sommige toepassingstypen anders moeten worden behandeld, kunnen ze uit de globale pool worden gehaald. In plaats daarvan worden ze geëvalueerd op basis van de percentages die zijn gekoppeld aan de naam van het toepassingstype in de kaart. In een cluster zijn er bijvoorbeeld duizenden toepassingen van verschillende typen en een paar controletoepassingsinstanties van een speciaal toepassingstype. De controletoepassingen mogen nooit fout zijn. U globale MaxPercentUnhealthyApplications op20% opgeven om sommige fouten te tolereren, maar voor het toepassingstype ControlApplicationType stelt u de MaxPercentUnhealthyApplications in op 0. Op deze manier, als sommige van de vele toepassingen ongezond zijn, maar onder het globale ongezonde percentage, wordt het cluster geëvalueerd op Waarschuwing. Een waarschuwingsstatus heeft geen invloed op de clusterupgrade of andere monitoring die wordt geactiveerd door de status Fout. Maar zelfs een controle toepassing in de fout zou maken cluster ongezond, die triggers roll back of pauzeert de cluster upgrade, afhankelijk van de upgrade configuratie.
  Voor de toepassingstypen die in de kaart zijn gedefinieerd, worden alle toepassingsinstanties uit de wereldwijde groep toepassingen gehaald. Ze worden geëvalueerd op basis van het totale aantal toepassingen van het toepassingstype, met behulp van de specifieke MaxPercentUnhealthyApplications van de kaart. Alle rest van de toepassingen blijven in de wereldwijde pool en worden geëvalueerd met MaxPercentUnhealthyApplications.

Het volgende voorbeeld is een fragment uit een clustermanifest. Als u vermeldingen in de toepassingstypekaart wilt definiëren, stelt u de parameternaam voor met 'ApplicationTypeMaxPercentUnhealthyApplications', gevolgd door de naam van het toepassingstype.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>Toepassingsstatusbeleid
Het [toepassingsstatusbeleid](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) beschrijft hoe de evaluatie van gebeurtenissen en samenvoeging van kindstaten wordt gedaan voor toepassingen en hun kinderen. Het kan worden gedefinieerd in het toepassingsmanifest **ApplicationManifest.xml**in het toepassingspakket. Als er geen beleid is opgegeven, gaat Service Fabric ervan uit dat de entiteit niet in orde is als er een gezondheidsrapport of een kind is bij de waarschuwings- of foutstatus.
Het configureerbare beleid is:

* [OverweegWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Hiermee geeft u op of waarschuwingsstatusrapporten tijdens de statusbeoordeling als fouten moeten worden behandeld. Standaard: onwaar.
* [MaxPercentUnhealthyDegeïmplementeerd](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Hiermee geeft u het maximale getolereerde percentage op dat kan worden uitgevoerd als het niet in orde is voordat de toepassing als fout wordt beschouwd. Dit percentage wordt berekend door het aantal ongezonde geïmplementeerde toepassingen te delen over het aantal knooppunten waarop de toepassingen momenteel in het cluster worden geïmplementeerd. De berekening wordt afgerond om één fout op kleine aantallen knooppunten te tolereren. Standaardpercentage: nul.
* [DefaultserviceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Hiermee geeft u het standaardstatusbeleid voor servicetypen op, waarmee het standaardstatusbeleid voor alle servicetypen in de toepassing wordt vervangen.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Biedt een kaart met servicestatusbeleid per servicetype. Met deze beleidsregels worden de standaardstatusbeleid voor servicetypen voor elk opgegeven servicetype vervangen. Als een toepassing bijvoorbeeld een stateless gatewayservicetype en een stateful engine servicetype heeft, u het statusbeleid voor hun evaluatie anders configureren. Wanneer u beleid per servicetype opgeeft, u meer gedetailleerde controle krijgen over de status van de service.

### <a name="service-type-health-policy"></a>Statusbeleid van servicetype
Het [statusbeleid van het servicetype](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) bepaalt hoe de services en de kinderen van services moeten worden geëvalueerd en samengevoegd. Het beleid bevat:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Hiermee geeft u het maximaal getolereerde percentage ongezonde partities op voordat een service als ongezond wordt beschouwd. Standaardpercentage: nul.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Hiermee geeft u het maximaal getolereerde percentage ongezonde replica's op voordat een partitie als ongezond wordt beschouwd. Standaardpercentage: nul.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Hiermee geeft u het maximaal getolereerde percentage ongezonde services op voordat de toepassing als ongezond wordt beschouwd. Standaardpercentage: nul.

Het volgende voorbeeld is een fragment uit een toepassingsmanifest:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Gezondheidsevaluatie
Gebruikers en geautomatiseerde services kunnen de status voor elke entiteit op elk gewenst moment evalueren. Om de status van een entiteit te evalueren, verzamelt het statusarchief alle statusrapporten over de entiteit en evalueert het al zijn kinderen (indien van toepassing). Het algoritme voor statusaggregatie maakt gebruik van gezondheidsbeleid dat aangeeft hoe statussen van kinderen moeten worden geëvalueerd (indien van toepassing).

### <a name="health-report-aggregation"></a>Aggregatie van gezondheidsrapport
Eén entiteit kan meerdere statusrapporten laten sturen door verschillende verslaggevers (systeemcomponenten of waakhonden) over verschillende eigenschappen. De aggregatie maakt gebruik van het bijbehorende gezondheidsbeleid, met name het ConsiderWarningAsError-lid van het toepassings- of clusterstatusbeleid. ConsiderWarningAsError geeft aan hoe u waarschuwingen evalueren.

De geaggregeerde status wordt geactiveerd door de *slechtste* statusrapporten op de entiteit. Als er ten minste één foutstatusrapport is, is de geaggregeerde status een fout.

![Aggregatie van het gezondheidsrapport met foutrapport.][2]

Een statusentiteit met een of meer foutstatusrapporten wordt geëvalueerd als Fout. Hetzelfde geldt voor een verlopen gezondheidsrapport, ongeacht de gezondheidstoestand.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Als er geen foutrapporten en een of meer waarschuwingen zijn, is de geaggregeerde status een waarschuwing of fout, afhankelijk van de beleidsvlag ConsiderWarningAsError.

![Gregatie van het gezondheidsrapport met waarschuwingsrapport en ConsiderWarningAsError false.][3]

Gregatie van het rapport voor statusrapportage met waarschuwingsrapport en ConsiderWarningAsError ingesteld op false (default).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Kindergezondheidsaggregatie
De geaggregeerde status van een entiteit weerspiegelt de status van de onderliggende status (indien van toepassing). Het algoritme voor het aggregeren van onderliggende statussen gebruikt het gezondheidsbeleid dat van toepassing is op basis van het entiteitstype.

![Health aggregation van onderliggende entiteiten.][4]

Kindaggregatie op basis van gezondheidsbeleid.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Nadat de gezondheidswinkel alle kinderen heeft geëvalueerd, worden hun statussen samengevoegd op basis van het geconfigureerde maximumpercentage ongezonde kinderen. Dit percentage wordt ontleend aan het beleid op basis van de entiteit en het onderliggende type.

* Als alle kinderen OK-status hebben, is de onderliggende status in orde.
* Als kinderen zowel OK- als waarschuwingstoestanden hebben, wordt de onderliggende status gewaarschuwd.
* Als er kinderen zijn met foutstatussen die het maximaal toegestane percentage ongezonde kinderen niet respecteren, is de geaggregeerde status van de bovenliggende status een fout.
* Als de kinderen met foutstatussen het maximaal toegestane percentage ongezonde kinderen respecteren, wordt de geaggregeerde status van de bovenliggende status gewaarschuwd.

## <a name="health-reporting"></a>Gezondheidsrapportage
Systeemcomponenten, System Fabric-toepassingen en interne/externe waakhonden kunnen zich melden bij servicefabric-entiteiten. De verslaggevers maken *lokale* vaststellingen van de gezondheid van de gecontroleerde entiteiten, op basis van de omstandigheden die zij controleren. Ze hoeven niet te kijken naar globale status of geaggregeerde gegevens. Het gewenste gedrag is om eenvoudige verslaggevers, en niet complexe organismen die moeten kijken naar veel dingen om af te leiden welke informatie te sturen.

Om gezondheidsgegevens naar het gezondheidsarchief te verzenden, moet een verslaggever de getroffen entiteit identificeren en een gezondheidsrapport maken. Als u het rapport wilt verzenden, gebruikt u de [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API, report health API's die worden blootgesteld op de `Partition` of-objecten, `CodePackageActivationContext` PowerShell-cmdlets of REST.

### <a name="health-reports"></a>Gezondheidsrapporten
De [statusrapporten](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) voor elk van de entiteiten in het cluster bevatten de volgende gegevens:

* **SourceId**. Een string die de verslaggever van de gezondheidsgebeurtenis op unieke wijze identificeert.
* **Entiteits-id**. Hiermee wordt de entiteit aangegeven waar het rapport wordt toegepast. Het verschilt op basis van het [type entiteit:](service-fabric-health-introduction.md#health-entities-and-hierarchy)
  
  * Cluster. Geen.
  * Knooppunt. Naam van knooppunt (tekenreeks).
  * Toepassing. Toepassingsnaam (URI). Vertegenwoordigt de naam van de toepassingsinstantie die in het cluster is geïmplementeerd.
  * Service. Servicenaam (URI). Vertegenwoordigt de naam van de serviceinstantie die in het cluster wordt geïmplementeerd.
  * Partitie. Partitie-ID (GUID). Vertegenwoordigt de unieke partitie-id.
  * Replica. De statusvolle servicereplica-ID of de statusloze serviceinstantie-id (INT64).
  * GeïmplementeerdToepassing. Toepassingsnaam (URI) en knooppuntnaam (tekenreeks).
  * GeïmplementeerdServicepakket. Toepassingsnaam (URI), knooppuntnaam (tekenreeks) en servicemanifestnaam (tekenreeks).
* **Eigendom**. Een *tekenreeks* (geen vaste opsomming) waarmee de melder de statusgebeurtenis voor een specifieke eigenschap van de entiteit kan categoriseren. Verslaggever A kan bijvoorbeeld de status van de eigenschap 'Opslag' van Knooppunt 01 melden en verslaggever B kan de status van de eigenschap 'Connectiviteit' van Knooppunt 01 melden. In het gezondheidsarchief worden deze rapporten behandeld als afzonderlijke statusgebeurtenissen voor de entiteit Node01.
* **Beschrijving**. Een string waarmee een verslaggever gedetailleerde informatie kan geven over de gezondheidsgebeurtenis. **SourceId**, **Property**en **HealthState** moeten het rapport volledig beschrijven. De beschrijving voegt door de mens leesbare informatie over het rapport toe. De tekst maakt het eenvoudiger voor beheerders en gebruikers om het gezondheidsrapport te begrijpen.
* **HealthState**. Een [opsomming](service-fabric-health-introduction.md#health-states) die de gezondheidstoestand van het rapport beschrijft. De geaccepteerde waarden zijn OK, Waarschuwing en Fout.
* **TimeToLive**. Een tijdspanne die aangeeft hoe lang het gezondheidsrapport geldig is. In combinatie met **RemoveWhenExpired**laat het de statuswinkel weten hoe verlopen gebeurtenissen moeten worden geëvalueerd. Standaard is de waarde oneindig en is het rapport voor altijd geldig.
* **VerwijderenWanneer verlopen**. Een booleaan. Als het rapport true is ingesteld, wordt het verlopen statusrapport automatisch verwijderd uit het gezondheidsarchief en heeft het rapport geen invloed op de evaluatie van de status van de entiteit. Wordt gebruikt wanneer het rapport alleen voor een bepaalde periode geldig is en de melder hoeft het niet expliciet te wissen. Het wordt ook gebruikt om rapporten uit de gezondheidsopslag te verwijderen (bijvoorbeeld, wordt een waakhond veranderd en stopt met het verzenden van rapporten met vorige bron en eigenschap). Het kan een rapport verzenden met een korte TimeToLive samen met RemoveWhenExpired om een eerdere status uit de health store op te helderen. Als de waarde is ingesteld op false, wordt het verlopen rapport behandeld als een fout in de statusevaluatie. De valse waarde geeft aan het gezondheidsarchief aan dat de bron periodiek over deze eigenschap moet rapporteren. Zo niet, dan moet er iets mis zijn met de waakhond. De gezondheid van de waakhond wordt vastgelegd door de gebeurtenis als een fout te beschouwen.
* **SequenceNummer**. Een positief geheel getal dat steeds groter moet worden, vertegenwoordigt de volgorde van de rapporten. Het wordt gebruikt door de health store om verouderde rapporten die te laat worden ontvangen als gevolg van netwerkvertragingen of andere problemen op te sporen. Een rapport wordt afgewezen als het volgnummer kleiner is dan of gelijk is aan het meest recent toegepaste nummer voor dezelfde entiteit, bron en eigenschap. Als het niet is opgegeven, wordt het volgnummer automatisch gegenereerd. Het is noodzakelijk om het volgnummer alleen in te zetten bij rapportage over statusovergangen. In deze situatie moet de bron onthouden welke rapporten hij heeft verzonden en de informatie bewaren voor herstel bij failover.

Deze vier informatiestukken- SourceId, entiteits-id, Eigenschap en HealthState - zijn vereist voor elk gezondheidsrapport. De SourceId-tekenreeks mag niet beginnen met het voorvoegsel "**System.**", dat is gereserveerd voor systeemrapporten. Voor dezelfde entiteit is er slechts één rapport voor dezelfde bron en eigenschap. Meerdere rapporten voor dezelfde bron en eigenschap overschrijven elkaar, hetzij aan de kant van de gezondheidsclient (als ze zijn batched) of aan de kant van het gezondheidsarchief. De vervanging is gebaseerd op volgnummers; nieuwere rapporten (met hogere volgnummers) vervangen oudere rapporten.

### <a name="health-events"></a>Gezondheidsgebeurtenissen
Intern houdt het gezondheidsarchief [statusgebeurtenissen](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent)bij, die alle informatie uit de rapporten bevatten, en aanvullende metagegevens. De metagegevens bevatten de tijd dat het rapport aan de statusclient is gegeven en de tijd dat het aan de serverzijde is gewijzigd. De statusgebeurtenissen worden geretourneerd door [gezondheidsquery's](service-fabric-view-entities-aggregated-health.md#health-queries).

De toegevoegde metagegevens bevatten:

* **SourceUtcTimestamp**. De tijd dat het rapport werd gegeven aan de gezondheidscliënt (Coordinated Universal Time).
* **LaatsteModifiedutcTimestamp**. De tijd dat het rapport voor het laatst is gewijzigd aan de serverzijde (Coordinated Universal Time).
* **IsVerlopen**. Een vlag om aan te geven of het rapport is verlopen toen de query werd uitgevoerd door het statusarchief. Een gebeurtenis kan alleen worden verlopen als Verwijderen wanneer verlopen onjuist is. Anders wordt de gebeurtenis niet per query geretourneerd en wordt deze uit de winkel verwijderd.
* **Lastoktransitionat**, **LastWarningTransitionat**, **LastErrorTransitionat**. De laatste keer voor ok/waarschuwing/foutovergangen. Deze velden geven de geschiedenis van de overgangen van de status voor het evenement.

De velden statusovergang kunnen worden gebruikt voor slimmere waarschuwingen of 'historische' informatie over gezondheidsgebeurtenissen. Ze maken scenario's mogelijk zoals:

* Waarschuw wanneer een eigenschap meer dan X minuten een waarschuwing/fout heeft ontvangen. Het controleren van de toestand voor een periode van tijd vermijdt waarschuwingen over tijdelijke omstandigheden. Een waarschuwing als de status langer dan vijf minuten is gewaarschuwd, kan bijvoorbeeld worden vertaald naar (HealthState == Warning and Now - LastWarningTransitionTime > 5 minuten).
* Waarschuw alleen op voorwaarden die in de laatste X-minuten zijn gewijzigd. Als een rapport al vóór de opgegeven tijd op fout was, kan het worden genegeerd omdat het al eerder was gesignaleerd.
* Als een eigenschap tussen waarschuwing en fout toggling, bepalen hoe lang het is ongezond (dat wil zeggen, niet OK). Een waarschuwing als de eigenschap langer dan vijf minuten niet gezond is geweest, kan bijvoorbeeld worden vertaald naar (HealthState != Ok en Nu - LastOkTransitionTime > 5 minuten).

## <a name="example-report-and-evaluate-application-health"></a>Voorbeeld: de toepassingsstatus rapporteren en evalueren
In het volgende voorbeeld wordt een gezondheidsrapport via PowerShell op de **toepassingsstructuur:/WordCount** van de bron **MyWatchdog.** Het gezondheidsrapport bevat informatie over de statuseigenschap 'beschikbaarheid' in een foutstatus, met oneindige TimeToLive. Vervolgens wordt de toepassingsstatus opgevraagd, die geaggregeerde statusstatusfouten en de gerapporteerde statusgebeurtenissen in de lijst met statusgebeurtenissen retourneert.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Gebruik van het gezondheidsmodel
Met het gezondheidsmodel kunnen cloudservices en het onderliggende Service Fabric-platform worden geschaald, omdat monitoring- en gezondheidsbepalingen worden verdeeld over de verschillende monitoren binnen het cluster.
Andere systemen hebben één gecentraliseerde service op clusterniveau die alle *mogelijk* nuttige informatie die door services wordt uitgestraald, ontleden. Deze aanpak belemmert hun schaalbaarheid. Het staat hen ook niet toe om specifieke informatie te verzamelen om problemen en potentiële problemen zo dicht mogelijk bij de hoofdoorzaak te identificeren.

Het gezondheidsmodel wordt intensief gebruikt voor monitoring en diagnose, voor het evalueren van cluster- en toepassingsstatus en voor bewaakte upgrades. Andere services gebruiken gezondheidsgegevens om automatische reparaties uit te voeren, de statusgeschiedenis van het cluster op te bouwen en waarschuwingen uit te geven onder bepaalde voorwaarden.

## <a name="next-steps"></a>Volgende stappen
[Gezondheidsrapporten van Service Fabric weergeven](service-fabric-view-entities-aggregated-health.md)

[Systeemstatusrapporten gebruiken voor het oplossen van problemen](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Hoe de servicestatus te rapporteren en te controleren](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Aangepaste statusrapporten van servicefabric toevoegen](service-fabric-report-health.md)

[Services lokaal controleren en een diagnose uitvoeren](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade van servicefabric-toepassingen](service-fabric-application-upgrade.md)

