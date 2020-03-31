---
title: De capaciteit van het cluster servicefabric plannen
description: Overwegingen voor de capaciteitsplanning van servicefabric-cluster. Nodetypes, Operations, Sustainability and Reliability tiers
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: pepogors
ms.openlocfilehash: 6e60fc10dd7e0eec24de4a089d09d914624dcfbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258913"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Overwegingen voor capaciteitsplanning van servicefabric-cluster
Bij elke implementatie voor productie is capaciteitsplanning van groot belang. Hier zijn enkele van de items die je moet overwegen als onderdeel van dat proces.

* Het aantal knooppunttypen waarmee uw cluster moet beginnen
* De eigenschappen van elk knooppunttype (grootte, primair, internetgericht, aantal VM's, enz.)
* De betrouwbaarheid en duurzaamheid van de clusterkenmerken

> [!NOTE]
> U moet tijdens de planning minimaal alle **niet toegestane** upgradebeleidswaarden controleren. Dit is om ervoor te zorgen dat u de waarden op de juiste manier instelt en om het afbranden van uw cluster later te beperken vanwege onveranderlijke systeemconfiguratie-instellingen. 
> 

Laten we elk van deze punten kort bekijken.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Het aantal knooppunttypen waarmee uw cluster moet beginnen
Eerst moet u uitzoeken waarvoor het cluster dat u maakt, wordt gebruikt.  Welke soorten toepassingen bent u van plan te implementeren in dit cluster? Als u niet duidelijk bent over het doel van het cluster, bent u waarschijnlijk nog niet klaar om het capaciteitsplanningsproces in te voeren.

Stel het aantal knooppunttypen in waarmee uw cluster moet beginnen.  Elk knooppunttype wordt toegewezen aan een virtuele machineschaalset. Elk knooppunttype kan dan onafhankelijk omhoog of omlaag worden geschaald, verschillende open poorten bevatten en diverse capaciteitsstatistieken hebben. Dus de beslissing van het aantal knooppunt soorten komt in wezen neer op de volgende overwegingen:

* Heeft uw applicatie meerdere diensten, en heeft een van hen nodig om openbaar of internet geconfronteerd? Typische toepassingen bevatten een front-end gatewayservice die invoer ontvangt van een client en een of meer back-endservices die communiceren met de front-endservices. Dus in dit geval heb je ten minste twee knooppunttypes.
* Hebben uw diensten (die deel uitmaken van uw toepassing) verschillende infrastructuurbehoeften zoals meer RAM of hogere CPU-cycli? Laten we er bijvoorbeeld van uitgaan dat de toepassing die u wilt implementeren een front-endservice en een back-endservice bevat. De front-end service kan worden uitgevoerd op kleinere VM's (VM-formaten zoals D2) die poorten open voor het internet hebben.  De back-end service is echter rekenintensief en moet draaien op grotere VM's (met VM-formaten zoals D4, D6, D15) die niet op internet staan.
  
  In dit voorbeeld u, hoewel u besluiten om alle services op één knooppunttype te plaatsen, u aanbevolen deze in een cluster met twee knooppunttypen te plaatsen.  Hierdoor kan elk knooppunttype verschillende eigenschappen hebben, zoals internetverbinding of VM-grootte. Het aantal VM's kan ook onafhankelijk van elkaar worden geschaald.  
* Omdat u de toekomst niet voorspellen, gaat u met feiten die u kent en kiest u het aantal knooppunttypen waarmee uw toepassingen moeten beginnen. U knooppunttypen altijd later toevoegen of verwijderen. Een cluster Servicefabric moet ten minste één knooppunttype hebben.

## <a name="the-properties-of-each-node-type"></a>De eigenschappen van elk knooppunttype
Het **knooppunttype** kan worden gezien als gelijkwaardig aan rollen in Cloud Services. Knooppunttypen definiëren de VM-formaten, het aantal VM's en hun eigenschappen. Elk knooppunttype dat is gedefinieerd in een cluster van Servicefabric, wordt toegewezen aan een [virtuele machineschaalset.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)  
Elk knooppunttype is een afzonderlijke schaalset en kan onafhankelijk worden opgeschaald of omlaag, verschillende sets poorten openen en verschillende capaciteitsstatistieken hebben. Zie Clusterknooppunttypen [servicestructuur](service-fabric-cluster-nodetypes.md)voor meer informatie over de relaties tussen knooppunttypen en virtuele machineschaalsets, hoe rdp in een van de instanties wordt gebruikt, hoe u nieuwe poorten openen, enzovoort.

Een cluster servicestructuur kan uit meer dan één knooppunttype bestaan. In dat geval bestaat het cluster uit één primair knooppunttype en een of meer niet-primaire knooppunttypen.

Een enkel knooppunttype kan niet op betrouwbare wijze verder dan 100 knooppunten per virtuele machineschaal worden geschaald voor SF-toepassingen; het bereiken van meer dan 100 knooppunten betrouwbaar, vereist u om extra virtuele machine schaal sets toe te voegen.

### <a name="primary-node-type"></a>Type primair knooppunt

De servicefabricsysteemservices (bijvoorbeeld de clustermanagerservice of Image Store-service) worden op het primaire knooppunttype geplaatst. 

![Schermafbeelding van een cluster met twee knooppunttypen][SystemServices]

* De **minimale grootte van VM's** voor het primaire knooppunttype wordt bepaald door de **duurzaamheidslaag die** u kiest. De standaardduurzaamheidslaag is Brons. Zie [De duurzaamheidskenmerken van het cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) voor meer details.  
* Het **minimumaantal VM's** voor het primaire knooppunttype wordt bepaald door de **betrouwbaarheidslaag die** u kiest. De standaardbetrouwbaarheidslaag is Zilver. Zie [De betrouwbaarheidskenmerken van het cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) voor meer details.  

Vanuit de sjabloon Azure Resource Manager wordt het primaire `isPrimary` knooppunttype geconfigureerd met het kenmerk onder de [definitie van knooppunttype](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Niet-primair knooppunttype

In een cluster met meerdere knooppunttypen is er één primair knooppunttype en de rest niet-primair.

* De **minimale grootte van VM's** voor niet-primaire knooppunttypen wordt bepaald door de **duurzaamheidslaag die** u kiest. De standaardduurzaamheidslaag is Brons. Zie [De duurzaamheidskenmerken van het cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)voor meer informatie.  
* Het **minimumaantal VM's** voor niet-primaire knooppunttypen is één. U moet dit nummer echter kiezen op basis van het aantal replica's van de toepassing/services die u in dit knooppunttype wilt uitvoeren. Het aantal VM's in een knooppunttype kan worden verhoogd nadat u het cluster hebt geïmplementeerd.

## <a name="the-durability-characteristics-of-the-cluster"></a>De duurzaamheidskenmerken van het cluster
De duurzaamheidslaag wordt gebruikt om aan het systeem de bevoegdheden aan te geven die uw VM's hebben met de onderliggende Azure-infrastructuur. Met deze bevoegdheid kan Service Fabric in het primaire knooppunttype alle infrastructuuraanvragen op VM-niveau onderbreken (zoals een vm-reboot, vm-reimage of VM-migratie) die van invloed zijn op de quorumvereisten voor de systeemservices en uw stateful services. In de niet-primaire knooppunttypen kan Service Fabric met deze bevoegdheid alle infrastructuuraanvragen op VM-niveau (zoals vm-reboot, vm-reimage en VM-migratie) pauzeren die van invloed zijn op de quorumvereisten voor uw stateful services.

| Duurzaamheidslaag  | Vereist minimumaantal VM's | Ondersteunde VM SKU's                                                                  | Updates die u maakt voor uw virtuele machineschaalset                               | Updates en onderhoud geïnitieerd door Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Goud             | 5                              | SKU's met volledige node die zijn gewijd aan één klant (bijvoorbeeld L32s, GS5, G5, DS15_v2, D15_v2) | Kan worden uitgesteld tot goedgekeurd door het cluster Service Fabric | Kan worden onderbroken voor 2 uur per UD om extra tijd voor replica's om te herstellen van eerdere storingen |
| Zilver           | 5                              | VM's van single core of hoger met ten minste 50 GB lokale SSD                      | Kan worden uitgesteld tot goedgekeurd door het cluster Service Fabric | Kan niet worden uitgesteld voor een aanzienlijke periode van tijd                                                    |
| Bronze           | 1                              | VM's met ten minste 50 GB lokale SSD                                              | Wordt niet vertraagd door het cluster Service Fabric           | Kan niet worden uitgesteld voor een aanzienlijke periode van tijd                                                    |

> [!WARNING]
> Node types die draaien met Bronze duurzaamheid verkrijgen _geen privileges._ Dit betekent dat infrastructuurtaken die van invloed zijn op uw stateful workloads niet worden gestopt of vertraagd, wat van invloed kan zijn op uw workloads. Gebruik alleen Bronze voor knooppunttypen die alleen stateloze workloads uitvoeren. Voor productieworkloads wordt het uitvoeren van Zilver of hoger aanbevolen. 
> 
> Ongeacht het duurzaamheidsniveau [vernietigt de toewijzingsbewerking](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) op VM-schaalset het cluster

**Voordelen van het gebruik van silver of gold duurzaamheidsniveaus**
 
- Vermindert het aantal vereiste stappen in een scale-in operatie (dat wil zeggen, node deactivering en Remove-ServiceFabricNodeState wordt automatisch aangeroepen).
- Vermindert het risico op gegevensverlies als gevolg van een door de klant geïnitieerde vm SKU-wijzigingsbewerking of Azure-infrastructuurbewerkingen.

**Nadelen van het gebruik van silver of gold duurzaamheidsniveaus**
 
- Implementaties naar uw virtuele machineschaalset en andere gerelateerde Azure-resources kunnen worden vertraagd, kunnen een time-out krijgen of kunnen volledig worden geblokkeerd door problemen in uw cluster of op infrastructuurniveau. 
- Hiermee wordt het aantal gebeurtenissen voor de levenscyclus van [replica's](service-fabric-reliable-services-lifecycle.md) (bijvoorbeeld primaire swaps) verhoogd als gevolg van geautomatiseerde deactiveringen van nodetijdens Azure-infrastructuurbewerkingen.
- Haalt knooppunten uit de dienst voor perioden waarin Azure-platformsoftware-updates of hardwareonderhoudsactiviteiten plaatsvinden. Mogelijk ziet u knooppunten met status Uitschakelen/uitgeschakeld tijdens deze activiteiten. Dit vermindert de capaciteit van uw cluster tijdelijk, maar heeft geen invloed op de beschikbaarheid van uw cluster of toepassingen.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Aanbevelingen voor het gebruik van silver of gold duurzaamheidsniveaus

Gebruik de duurzaamheid van Zilver of Goud voor alle knooppunttypen die stateful services hosten die u verwacht in te schalen (het aantal VM-eenheden verminderen) vaak, en u zou liever zien dat implementatiebewerkingen worden vertraagd en de capaciteit wordt verminderd ten gunste van het vereenvoudigen van deze scale-in Operaties. De scale-out scenario's (het toevoegen van VM's exemplaren) spelen niet in uw keuze van de duurzaamheidslaag, alleen scale-in doet.

### <a name="changing-durability-levels"></a>Veranderende duurzaamheidsniveaus
- Node types met duurzaamheidsniveaus van Zilver of Goud kunnen niet worden gedegradeerd tot Brons.
- Upgraden van brons naar zilver of goud kan een paar uur duren.
- Wanneer u het duurzaamheidsniveau wijzigt, moet u deze bijwerken in zowel de configuratie van de servicestructuurextensie in uw bron met een virtuele machineschaalset als in de definitie van het knooppunttype in uw clusterbron servicestructuur. Deze waarden moeten overeenkomen.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Operationele aanbevelingen voor het knooppunttype dat u hebt ingesteld op zilver- of goudduurzaamheidsniveau.

- Houd uw cluster en toepassingen te allen tijde gezond en zorg ervoor dat toepassingen tijdig reageren op alle gebeurtenissen in de [levenscyclus van servicereplica's](service-fabric-reliable-services-lifecycle.md) (zoals replica in build) die vastzitten.
- Adopteer veiligere manieren om een VM SKU-wijziging aan te brengen (Op/omlaag schalen): Het wijzigen van de VM SKU van een virtuele machineschaalset vereist een aantal stappen en overwegingen. Hier is het proces dat u volgen om veelvoorkomende problemen te voorkomen.
    - **Voor niet-primaire knooppunttypen:** Het wordt aanbevolen dat u een nieuwe virtuele machineschaalset maakt, de beperking voor serviceplaatsing wijzigt om het nieuwe type virtuele machineschaalset/knooppunt op te nemen en vervolgens het aantal oude typen van de virtuele machineschaal tot nul te reduceren, één knooppunt tegelijk (dit is om ervoor te zorgen dat verwijdering van de knooppunten geen invloed heeft op de betrouwbaarheid van het cluster).
    - **Voor het primaire knooppunttype:** Als de door u geselecteerde VM-SKU op capaciteit is en u wilt overschakelen naar een grotere VM SKU, volgt u onze richtlijnen voor [verticale schaling voor een primair knooppunttype.](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-node-type) 

- Zorg voor een minimum aantal van vijf knooppunten voor elke virtuele machineschaalset met het duurzaamheidsniveau van Goud of Zilver ingeschakeld.
- Elke virtuele machineschaalset met duurzaamheidsniveau Zilver of Goud moet worden toegewezen aan zijn eigen knooppunttype in het cluster Service Fabric. Als u meerdere virtuele machineschaalsets in kaart brengt aan één knooppunttype, wordt voorkomen dat de coördinatie tussen het cluster Servicefabric en de Azure-infrastructuur naar behoren werkt.
- Verwijder geen willekeurige VM-exemplaren, gebruik altijd de functie voor het schalen van virtuele machines. Het verwijderen van willekeurige VM-exemplaren heeft een potentieel van het creëren van onevenwichtigheden in de VM-instantie verspreid over UD en FD. Deze onbalans kan een negatieve invloed hebben op het systeemvermogen om de balans tussen de service-exemplaren/servicereplica's goed te laden.
- Als u Autoscale gebruikt, stelt u de regels in die schaal (het verwijderen van VM-exemplaren) slechts één knooppunt tegelijk. Het is niet veilig om meer dan één instantie tegelijk af te schalen.
- Als u VM's op het primaire knooppunttype verwijderde of deallocatert, mag u het aantal toegewezen VM's nooit verlagen tot wat de betrouwbaarheidslaag vereist. Deze bewerkingen zullen voor onbepaalde tijd worden geblokkeerd in een weegschaal met een duurzaamheidsniveau van Zilver of Goud.

## <a name="the-reliability-characteristics-of-the-cluster"></a>De betrouwbaarheidskenmerken van het cluster
De betrouwbaarheidslaag wordt gebruikt om het aantal replica's van de systeemservices in te stellen dat u in dit cluster wilt uitvoeren op het primaire knooppunttype. Hoe meer het aantal replica's, hoe betrouwbaarder de systeemservices in uw cluster zijn.  

De betrouwbaarheidslaag kan de volgende waarden aannemen:

* Platinum - Voer de systeemservices uit met een doelreplicasetvan negen
* Goud - Voer de systeemservices uit met een aantal doelreplica's van zeven
* Zilver - Voer de systeemservices uit met een doelreplicasetaantal van vijf 
* Brons - Voer de systeemservices uit met een doelreplicasetaantal van drie

> [!NOTE]
> De betrouwbaarheidslaag die u kiest, bepaalt het minimumaantal knooppunten dat het primaire knooppunttype moet hebben. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Aanbevelingen voor de betrouwbaarheidslaag

Wanneer u de grootte van uw cluster vergroot of kleinerwordt (de som van VM-exemplaren in alle knooppunttypen), moet u de betrouwbaarheid van uw cluster bijwerken van de ene laag naar de andere. Als u dit doet, worden de clusterupgrades geactiveerd die nodig zijn om het aantal replicasetsets voor systeemservices te wijzigen. Wacht tot de lopende upgrade is voltooid voordat u andere wijzigingen in het cluster aanbrengt, zoals het toevoegen van knooppunten.  U de voortgang van de upgrade op Service Fabric Explorer volgen of door [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) uit te voeren

Hier is de aanbeveling over het kiezen van de betrouwbaarheid laag.  Het aantal seednodes is ook ingesteld op het minimumaantal knooppunten voor een betrouwbaarheidslaag.  Voor een cluster met gold-betrouwbaarheid zijn er bijvoorbeeld 7 zaadknooppunten.

| **Aantal clusterknooppunten** | **Betrouwbaarheidslaag** |
| --- | --- |
| 1 |Geef de parameter Betrouwbaarheidslaag niet op, het systeem berekent deze |
| 3 |Bronze |
| 5 of 6|Zilver |
| 7 of 8 |Goud |
| 9 en meer |Platinum |

## <a name="primary-node-type---capacity-guidance"></a>Primair knooppunttype - capaciteitsrichtlijnen

Hier is de richtlijnen voor het plannen van de primaire node type capaciteit:

- **Aantal VM-exemplaren dat een productiewerkbelasting in Azure moet uitvoeren:** U moet een minimum primaire knooppuntgrootte van 5 en een betrouwbaarheidslaag van zilver opgeven.  
- **Aantal VM-exemplaren dat testworkloads in Azure moet uitvoeren** U een minimumgrootte van het primaire knooppunt van 1 of 3 opgeven. Het cluster met één knooppunt, wordt uitgevoerd met een speciale configuratie en dus wordt de schaal uit dat cluster niet ondersteund. Het cluster met één knooppunt, heeft geen betrouwbaarheid en dus moet u in uw Resource Manager-sjabloon verwijderen/niet opgeven dat de configuratie (het niet instellen van de configuratiewaarde is niet genoeg). Als u het cluster met één knooppunt instelt dat via een portal is ingesteld, wordt de configuratie automatisch geregeld. Een en drie knooppuntclusters worden niet ondersteund voor het uitvoeren van productieworkloads. 
- **VM SKU:** Primaire knooppunt type is waar de systeemservices worden uitgevoerd, dus de VM SKU die u kiest voor het, moet rekening houden met de totale piekbelasting die u van plan bent te plaatsen in het cluster. Hier is een analogie om te illustreren wat ik bedoel hier - Denk aan de primaire knooppunt type als je "Longen", het is wat zorgt voor zuurstof aan je hersenen, en dus als de hersenen niet genoeg zuurstof te krijgen, je lichaam lijdt. 

Aangezien de capaciteitsbehoeften van een cluster worden bepaald door de werkbelasting die u in het cluster wilt uitvoeren, kunnen we u geen kwalitatieve richtlijnen bieden voor uw specifieke werkbelasting, maar hier is de brede richtlijnen om u op weg te helpen

Voor productieworkloads: 

- Het wordt aanbevolen om uw clusters primaire NodeType te wijden aan systeemservices en plaatsingsbeperkingen te gebruiken om uw toepassing te implementeren op secundaire nodetypes.
- De aanbevolen VM SKU is standaard D2_V2 of gelijkwaardig met een minimum van 50 GB lokale SSD.
- Het minimaal ondersteunde gebruik VM SKU is Standard_D2_V3 of Standaard D1_V2 of gelijkwaardig met een minimum van 50 GB lokale SSD. 
- Onze aanbeveling is minimaal 50 GB. Voor uw workloads, vooral bij het uitvoeren van Windows-containers, zijn grotere schijven vereist. 
- Gedeeltelijke VM SKU's zoals Standard A0 worden niet ondersteund voor productieworkloads.
- Een serie VM SKU's worden om prestatieredenen niet ondersteund voor productieworkloads.
- VM's met lage prioriteit worden niet ondersteund.

> [!WARNING]
> Het wijzigen van de primaire grootte van DE VM-sKU op een lopend cluster, is een schaalbewerking en gedocumenteerd in de [schaaldocumentatie van de virtuele machineschaalset.](virtual-machine-scale-set-scale-node-type-scale-out.md)

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Niet-primair knooppunttype - capaciteitsrichtlijnen voor stateful workloads

Deze richtlijnen zijn bedoeld voor stateful Workloads met behulp van [betrouwbare collecties servicefabric of betrouwbare actoren](service-fabric-choose-framework.md) die u uitvoert in het niet-primaire knooppunttype.

**Aantal VM-exemplaren:** Voor productieworkloads die stateful zijn, wordt aanbevolen dat u ze uitvoert met een minimum aantal en doelreplica's van 5. Dit betekent dat in stabiele toestand je eindigt met een replica (van een replica set) in elk foutdomein en upgrade domein. Het hele concept van de betrouwbaarheidslaag voor het primaire knooppunttype is een manier om deze instelling voor systeemservices op te geven. Dus dezelfde overweging geldt ook voor uw statige diensten.

Voor productieworkloads is de minimale aanbevolen grootte van het type niet-primaire knooppunt 5, als u er stateful workloads in uitvoert.

**VM SKU:** Dit is het knooppunttype waarbij uw toepassingsservices worden uitgevoerd, dus de VM SKU die u ervoor kiest, moet rekening houden met de piekbelasting die u in elk knooppunt wilt plaatsen. De capaciteitsbehoeften van het knooppunttype worden bepaald door de werkbelasting die u in het cluster wilt uitvoeren, dus we kunnen u geen kwalitatieve richtlijnen bieden voor uw specifieke werklast, maar hier is de brede richtlijnen om u te helpen aan de slag te gaan

Voor productieworkloads 

- De aanbevolen VM SKU is standaard D2_V2 of gelijkwaardig met een minimum van 50 GB lokale SSD.
- Het minimaal ondersteunde gebruik VM SKU is Standard_D2_V3 of Standaard D1_V2 of gelijkwaardig met een minimum van 50 GB lokale SSD. 
- Gedeeltelijke VM SKU's zoals Standard A0 worden niet ondersteund voor productieworkloads.
- Een serie VM SKU's worden om prestatieredenen niet ondersteund voor productieworkloads.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Niet-primair knooppunttype - capaciteitsrichtlijnen voor stateloze workloads

Deze richtlijnen voor stateloze workloads die u uitvoert op het type niet-primaire knooppunt.

**Aantal VM-exemplaren:** Voor productieworkloads die stateloos zijn, is de minimaal ondersteunde grootte van het type niet-primair knooppunt 2. Hiermee u twee stateloze exemplaren van uw toepassing uitvoeren en u uw service het verlies van een VM-exemplaar laten overleven. 

**VM SKU:** Dit is het knooppunttype waarbij uw toepassingsservices worden uitgevoerd, dus de VM SKU die u ervoor kiest, moet rekening houden met de piekbelasting die u in elk knooppunt wilt plaatsen. De capaciteitsbehoeften van het knooppunttype worden bepaald door de werkbelasting die u in het cluster wilt uitvoeren. Wij kunnen u geen kwalitatieve begeleiding bieden voor uw specifieke werklast.  Echter, hier is de brede begeleiding om u te helpen aan de slag.

Voor productieworkloads 

- De aanbevolen VM SKU is standaard D2_V2 of gelijkwaardig. 
- Het minimaal ondersteunde gebruik VM SKU is Standaard D1 of Standard D1_V2 of gelijkwaardig. 
- Gedeeltelijke VM SKU's zoals Standard A0 worden niet ondersteund voor productieworkloads.
- Een serie VM SKU's worden om prestatieredenen niet ondersteund voor productieworkloads.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Volgende stappen
Lees het volgende nadat u klaar bent met uw capaciteitsplanning en het instellen van een cluster:

* [Beveiliging van het cluster servicefabric](service-fabric-cluster-security.md)
* [Clusterschaling van servicefabric](service-fabric-cluster-scaling.md)
* [Planning voor noodherstel](service-fabric-disaster-recovery.md)
* [Relatie tussen nodetypen met virtuele machineschaalset](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
