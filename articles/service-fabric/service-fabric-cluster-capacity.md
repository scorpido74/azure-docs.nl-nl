---
title: De Service Fabric cluster capaciteit plannen
description: Overwegingen bij het plannen van de cluster capaciteit Service Fabric. Nodetypes, bewerkingen, duurzaamheid en betrouwbaarheids lagen
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: pepogors
ms.openlocfilehash: 6e60fc10dd7e0eec24de4a089d09d914624dcfbc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258913"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Overwegingen bij het plannen van Service Fabric cluster capaciteit
Voor een productie-implementatie is capaciteits planning een belang rijke stap. Hier volgen enkele van de items die u moet overwegen als onderdeel van dat proces.

* Het aantal knooppunt typen dat uw cluster moet starten met
* De eigenschappen van elk knooppunt type (grootte, primair, Internet gericht, aantal Vm's, enzovoort)
* De betrouwbaarheid en duurzaamheid van de clusterkenmerken

> [!NOTE]
> U moet mini maal alle **niet-toegestane** waarden voor upgrade beleid controleren tijdens de planning. Dit is om ervoor te zorgen dat u de waarden op de juiste wijze instelt en de verbranding van het cluster later kunt verhelpen vanwege onveranderbare systeem configuratie-instellingen. 
> 

Laten we elk van deze items kort bekijken.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Het aantal knooppunt typen dat uw cluster moet starten met
Eerst moet u nagaan wat het cluster is dat u maakt, wordt gebruikt voor.  Welke soorten toepassingen wilt u in dit cluster implementeren? Als u niet wist van het doel van het cluster, bent u waarschijnlijk nog niet klaar om het capaciteits plannings proces in te voeren.

Stel het aantal knooppunt typen in dat door het cluster moet worden gestart.  Elk knooppunt type wordt toegewezen aan een virtuele-machine schaalset. Elk knooppunttype kan dan onafhankelijk omhoog of omlaag worden geschaald, verschillende open poorten bevatten en diverse capaciteitsstatistieken hebben. Daarom is de beslissing van het aantal knooppunt typen in feite van belang voor de volgende overwegingen:

* Heeft uw toepassing meerdere services en moeten ze openbaar of Internet zijn gericht? Typische toepassingen bevatten een front-end Gateway Service die invoer ontvangt van een client en een of meer back-end-services die communiceren met de front-end-services. In dit geval hebt u dus ten minste twee typen knoop punten.
* Hebben uw services (waaruit uw toepassing bestaan) verschillende vereisten voor de infra structuur, zoals meer RAM-geheugen of hogere CPU-cycli? Laten we bijvoorbeeld aannemen dat de toepassing die u wilt implementeren, een front-end-service en een back-end-service bevat. De front-end-service kan worden uitgevoerd op kleinere Vm's (VM-grootten zoals D2) die poorten hebben geopend op internet.  De back-end-service is echter reken intensief en moet worden uitgevoerd op grotere Vm's (met VM-grootten zoals D4, D6, D15) die niet op internet zijn gericht.
  
  In dit voor beeld kunt u ervoor kiezen om alle services op één knooppunt type in te stellen, maar we raden u aan deze in een cluster met twee knooppunt typen te plaatsen.  Hierdoor kan elk knooppunt type verschillende eigenschappen hebben, zoals Internet verbinding of VM-grootte. Het aantal virtuele machines kan ook afzonderlijk worden geschaald.  
* Omdat u niet de toekomst kunt voors pellen, gaat u verder met de feiten die u kent en kiest u het aantal knooppunt typen waarmee uw toepassingen moeten beginnen. U kunt altijd later typen knoop punten toevoegen of verwijderen. Een Service Fabric cluster moet ten minste één knooppunt type hebben.

## <a name="the-properties-of-each-node-type"></a>De eigenschappen van elk knooppunt type
Het **knooppunt type** kan worden gezien als gelijkwaardig aan rollen in Cloud Services. Knooppunt typen definiëren de VM-grootten, het aantal Vm's en de bijbehorende eigenschappen. Elk knooppunt type dat in een Service Fabric cluster is gedefinieerd, wordt toegewezen aan een [schaalset voor virtuele machines](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Elk knooppunt type is een afzonderlijke schaalset en kan onafhankelijk van elkaar of omlaag worden geschaald, verschillende sets poorten openen en verschillende capaciteits metrieken hebben. Zie [service Fabric cluster knooppunt typen](service-fabric-cluster-nodetypes.md)voor meer informatie over de relaties tussen knooppunt typen en virtuele-machine schaal sets, over RDP in een van de exemplaren, het openen van nieuwe poorten, enzovoort.

Een Service Fabric cluster kan bestaan uit meer dan één knooppunt type. In dat geval bestaat het cluster uit één primair knooppunt type en een of meer niet-primaire knooppunt typen.

Een type knoop punt kan niet betrouwbaar worden geschaald tot Maxi maal 100 knoop punten per virtuele-machine schaalset voor SF-toepassingen. Als u meer dan 100 knoop punten op een betrouw bare manier wilt bereiken, moet u extra schaal sets voor virtuele machines toevoegen.

### <a name="primary-node-type"></a>Type primair knoop punt

De Service Fabric-systeem services (bijvoorbeeld de Cluster beheer-of Image Store-service) worden geplaatst op het primaire knooppunt type. 

![Scherm afbeelding van een cluster met twee knooppunt typen][SystemServices]

* De **minimale grootte van vm's** voor het primaire knooppunt type wordt bepaald door de **duurzaamheids categorie** die u kiest. De standaard-duurzaamheids categorie is bronzen. Zie [de duurzaamheids kenmerken van het cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) voor meer informatie.  
* Het **minimale aantal vm's** voor het primaire knooppunt type wordt bepaald door de gekozen **betrouwbaarheids categorie** . De standaard betrouwbaarheids laag is zilver. Raadpleeg [de betrouw bare kenmerken van het cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) voor meer informatie.  

Vanuit de Azure Resource Manager sjabloon wordt het primaire knooppunt type geconfigureerd met het kenmerk `isPrimary` onder de definitie van het [knooppunt type](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Niet-primair knooppunt type

In een cluster met meerdere knooppunt typen bevindt zich één primair knooppunt type en de rest niet-primair.

* De **minimale grootte van vm's** voor niet-primaire knooppunt typen wordt bepaald door de **duurzaamheids categorie** die u kiest. De standaard-duurzaamheids categorie is bronzen. Zie [de duurzaamheids kenmerken van het cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)voor meer informatie.  
* Het **minimum aantal vm's** voor niet-primaire knooppunt typen is één. U moet dit nummer echter kiezen op basis van het aantal replica's van de toepassing/services dat u wilt uitvoeren in dit knooppunt type. Het aantal virtuele machines in een knooppunt type kan worden verhoogd nadat u het cluster hebt geïmplementeerd.

## <a name="the-durability-characteristics-of-the-cluster"></a>De duurzaamheids kenmerken van het cluster
De laag duurzaamheid wordt gebruikt om aan het systeem de bevoegdheden aan te geven die uw virtuele machines hebben met de onderliggende Azure-infra structuur. In het primaire knooppunt type kan met deze bevoegdheid Service Fabric elke infrastructuur aanvraag op VM-niveau onderbreken (zoals het opnieuw opstarten van een VM, een VM-installatie kopie of een VM-migratie) die van invloed is op de quorum vereisten voor de systeem services en uw stateful Services. In de niet-primaire knooppunt typen kan met deze bevoegdheid Service Fabric alle infrastructuur aanvragen op VM-niveau (zoals opnieuw opstarten van de VM, VM-installatie kopie en VM-migratie) onderbreken die van invloed zijn op de quorum vereisten voor uw stateful Services.

| Duurzaamheids categorie  | Vereist minimum aantal Vm's | Ondersteunde VM-Sku's                                                                  | Updates die u aanbrengt in de schaalset voor virtuele machines                               | Updates en onderhoud geïnitieerd door Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Goud             | 5                              | Sku's van volledig knoop punt toegewezen aan één klant (bijvoorbeeld L32s, GS5, G5, DS15_v2, D15_v2) | Kan worden uitgesteld tot goedgekeurd door het Service Fabric-cluster | Kan gedurende 2 uur per UD worden onderbroken om meer tijd te bieden voor het herstellen van eerdere fouten in replica's |
| Zilver           | 5                              | Vm's van één kern of hoger met ten minste 50 GB lokale SSD                      | Kan worden uitgesteld tot goedgekeurd door het Service Fabric-cluster | Kan gedurende een belang rijke periode niet worden uitgesteld                                                    |
| Bron           | 1                              | Vm's met ten minste 50 GB lokale SSD                                              | Wordt niet vertraagd door het Service Fabric cluster           | Kan gedurende een belang rijke periode niet worden uitgesteld                                                    |

> [!WARNING]
> Voor knooppunt typen met de duurzaamheid Bronze zijn _geen bevoegdheden_nodig. Dit betekent dat infrastructuur taken die van invloed zijn op uw stateful werk belastingen, niet worden gestopt of uitgesteld, wat van invloed kan zijn op uw workloads. Gebruik alleen bronzen voor knooppunt typen die alleen stateless werk belastingen uitvoeren. Voor werk belastingen met Silver of hoger wordt aanbevolen. 
> 
> Ongeacht eventuele duurzaamheids niveau, wordt het cluster vernietigd door de [toewijzings](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) bewerking voor de VM-schaalset

**Voor delen van het gebruik van Silver-of Gold-duurzaamheids niveaus**
 
- Hiermee verkleint u het aantal vereiste stappen in een inschaal bewerking (dat wil zeggen: het deactiveren van knoop punten en Remove-ServiceFabricNodeState worden automatisch aangeroepen).
- Vermindert het risico van gegevens verlies als gevolg van een door de klant geïnitieerde, gewijzigde VM SKU-bewerking of Azure-infrastructuur bewerkingen.

**Nadelen van het gebruik van Silver-of Gold-duurzaamheids niveaus**
 
- Implementaties van uw schaalset voor virtuele machines en andere verwante Azure-resources kunnen worden uitgesteld, kunnen een time-out hebben of volledig worden geblokkeerd door problemen in uw cluster of op het niveau van de infra structuur. 
- Hiermee verhoogt u het aantal gebeurtenissen voor de [levens cyclus](service-fabric-reliable-services-lifecycle.md) van de replica (bijvoorbeeld primaire swaps) als gevolg van automatische activering van knoop punten tijdens Azure-infrastructuur bewerkingen.
- Neemt knoop punten buiten dienst voor Peri Oden terwijl software-updates van Azure platform of activiteiten voor onderhoud van hardware worden uitgevoerd. U ziet mogelijk knoop punten met de status uitschakelen/uitgeschakeld tijdens deze activiteiten. Dit vermindert de capaciteit van uw cluster tijdelijk, maar heeft geen invloed op de beschik baarheid van uw cluster of toepassingen.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Aanbevelingen voor het gebruik van Silver-of Gold-duurzaamheids niveaus

Gebruik Silver of Gold-duurzaamheid voor alle knooppunt typen die stateful services hosten die u verwacht te schalen (aantal VM-instanties verlagen) en u wilt de voor keur geven aan implementatie bewerkingen die worden vertraagd en de capaciteit moet worden verminderd om deze schaal te vereenvoudigen werk. De scale-out-scenario's (het toevoegen van Vm's instanties) worden niet in uw keuze van de duurzaamheids categorie afgespeeld. Dit kan alleen worden geschaald.

### <a name="changing-durability-levels"></a>Duurzaamheids niveaus wijzigen
- Knooppunt typen met duurzaamheids niveau zilver of goud kunnen niet worden gedowngraded naar Bronze.
- Een upgrade van bronzen naar Silver of Gold kan enkele uren duren.
- Wanneer u het duurzaamheids niveau wijzigt, moet u het bijwerken in zowel de configuratie van de Service Fabric-extensie in de resource van de virtuele-machine schaalset, en in de definitie van het knooppunt type in uw Service Fabric cluster bron. Deze waarden moeten overeenkomen.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Operationele aanbevelingen voor het knooppunt type dat u hebt ingesteld op het niveau zilver of goud.

- Houd uw cluster en toepassingen te allen tijde in orde en zorg ervoor dat toepassingen reageren op alle gebeurtenissen van de [levens cyclus van de service replica](service-fabric-reliable-services-lifecycle.md) (zoals replica in Build is vastgelopen) tijdig.
- Veiliger manieren om een VM-SKU te wijzigen (omhoog/omlaag schalen): het wijzigen van de VM-SKU van een schaalset voor virtuele machines vereist een aantal stappen en overwegingen. Hier volgt het proces dat u kunt volgen om veelvoorkomende problemen te voor komen.
    - **Voor niet-primaire knooppunt typen:** Het is raadzaam om een nieuwe schaalset voor virtuele machines te maken, de service plaatsings beperking te wijzigen zodat de nieuwe virtuele-machine schaalset/knooppunt type wordt opgenomen. vervolgens vermindert u het aantal exemplaren van de oude virtuele-machine schaalset naar nul, één knoop punt tegelijk
    - **Voor het primaire knooppunt type:** Als de VM-SKU die u hebt geselecteerd, op capaciteit is en u wilt overschakelen naar een grotere VM-SKU, volgt u onze richt lijnen [voor verticaal schalen voor een primair knooppunt type](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-node-type). 

- Behoud een minimum aantal van vijf knoop punten voor een schaalset voor virtuele machines met duurzaamheids niveau goud of zilver ingeschakeld.
- Elke schaalset voor virtuele machines met duurzaamheids niveau zilver of goud moet worden toegewezen aan het eigen knooppunt type in het Service Fabric cluster. Als u meerdere virtuele-machine schaal sets aan één knooppunt type toewijst, kan de coördinatie tussen het Service Fabric cluster en de Azure-infra structuur niet goed werken.
- Verwijder geen wille keurige VM-exemplaren, gebruik altijd de functie schaalset voor schaalset van virtuele machines. Als u wille keurige VM-exemplaren verwijdert, is het mogelijk dat er onbalansen in de VM-instantie worden gemaakt over UD en FD. Dit onevenwichtige kan een nadelige invloed hebben op de systeem capaciteit van de service-exemplaren/service-replica's.
- Als u automatisch schalen gebruikt, stelt u de regels zodanig in (verwijderen van VM-exemplaren) slechts één knoop punt tegelijk. Het omlaag schalen van meer dan één exemplaar op een keer is niet veilig.
- Als u Vm's verwijdert of ongedaan maakt voor het primaire knooppunt type, moet u nooit het aantal toegewezen Vm's verlagen wat de betrouwbaarheids categorie nodig heeft. Deze bewerkingen worden voor onbepaalde tijd geblokkeerd in een schaalset met een duurzaamheids niveau van zilver of goud.

## <a name="the-reliability-characteristics-of-the-cluster"></a>De betrouwbaarheids kenmerken van het cluster
De betrouwbaarheids categorie wordt gebruikt om het aantal replica's in te stellen van de systeem services die u wilt uitvoeren in dit cluster op het primaire knooppunt type. Hoe langer het aantal replica's, hoe betrouwbaarder de systeem services zijn in uw cluster.  

De betrouwbaarheids categorie kan de volgende waarden hebben:

* Platinum: Voer de systeem services uit met het aantal negen doel replica sets
* Goud: Voer de systeem services uit met het aantal zeven van de doel replicaset
* Silver: Voer de systeem services uit met een aantal van vijf ingestelde doel replicaset 
* Bronze: Voer de systeem services uit met een aantal van drie doel replica sets

> [!NOTE]
> De betrouwbaarheids categorie die u kiest, bepaalt het minimum aantal knoop punten dat voor het primaire knooppunt type moet zijn. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Aanbevelingen voor de betrouwbaarheids categorie

Wanneer u de grootte van uw cluster verg root of verkleint (de som van VM-exemplaren in alle knooppunt typen), moet u de betrouw baarheid van het cluster bijwerken van de ene laag naar de andere. Hiermee worden de cluster upgrades geactiveerd die nodig zijn om het aantal replica sets van de systeem services te wijzigen. Wacht totdat de upgrade wordt uitgevoerd voordat u andere wijzigingen aanbrengt in het cluster, zoals het toevoegen van knoop punten.  U kunt de voortgang van de upgrade op Service Fabric Explorer controleren of door [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) uit te voeren

Hier volgt een aanbeveling bij het kiezen van de betrouwbaarheids categorie.  Het aantal Seed-knoop punten is ook ingesteld op het minimum aantal knoop punten voor een betrouwbaarheids categorie.  Bijvoorbeeld: voor een cluster met Gold betrouw baarheid zijn er 7 Seed-knoop punten.

| **Aantal cluster knooppunten** | **Betrouwbaarheids niveau** |
| --- | --- |
| 1 |Geef geen para meter voor de betrouwbaarheids categorie op, het systeem berekent deze |
| 3 |Bron |
| 5 of 6|Zilver |
| 7 of 8 |Goud |
| 9 en Maxi maal |Platina |

## <a name="primary-node-type---capacity-guidance"></a>Richt lijn voor type capaciteit van het primaire knoop punt

Dit zijn de richt lijnen voor het plannen van de capaciteit van het primaire knooppunt type:

- **Aantal VM-exemplaren om een productie werk belasting in azure uit te voeren:** U moet een minimale grootte van het primaire knooppunt type van 5 en een betrouwbaarheids laag van zilver opgeven.  
- **Aantal VM-exemplaren voor het uitvoeren van test werkbelastingen in azure** U kunt de minimale grootte van het primaire knoop punt van 1 of 3 opgeven. Het cluster met één knoop punt, wordt uitgevoerd met een speciale configuratie, waardoor het uitschalen van dat cluster niet wordt ondersteund. Het cluster met één knoop punt, heeft geen betrouw baarheid en dus in uw Resource Manager-sjabloon, moet u die configuratie verwijderen (niet instellen van de configuratie waarde is niet voldoende). Als u het cluster met één knoop punt instelt via Portal, wordt de configuratie automatisch uitgevoerd. Clusters met één en drie knoop punten worden niet ondersteund voor het uitvoeren van productie werkbelastingen. 
- **VM-SKU:** Het primaire knooppunt type is de locatie waar de systeem services worden uitgevoerd. voor de VM-SKU die u kiest, moet rekening worden gehouden met de totale piek belasting die u in het cluster wilt plaatsen. Hier volgt een voor beeld van wat ik hier bevindt: het type van het primaire knoop punt wordt beschouwd als uw ' longs ', maar het is wat zuurstof levert voor uw hersenen. als de hersenen niet genoeg zuurstof behaalt, heeft uw hoofd gemoeds belang. 

Omdat de capaciteits behoeften van een cluster worden bepaald door de werk belasting die u in het cluster wilt uitvoeren, kunnen we u niet voorzien van kwalitatieve richt lijnen voor uw specifieke werk belasting, maar dit is de brede richt lijnen om u te helpen aan de slag te gaan

Voor werk belastingen voor productie: 

- Het is raadzaam om uw clusters Primary NodeType toe te wijzen aan systeem services en plaatsings beperkingen te gebruiken om uw toepassing te implementeren op secundaire NodeTypes.
- De aanbevolen VM-SKU is standaard D2_V2 of gelijkwaardig met een minimum van 50 GB lokale SSD.
- De mini maal ondersteunde virtuele machine voor VM-gebruik is Standard_D2_V3 of standaard D1_V2 of gelijkwaardig met mini maal 50 GB lokale SSD. 
- Onze aanbeveling is mini maal 50 GB. Voor uw workloads, met name bij het uitvoeren van Windows-containers, zijn grotere schijven vereist. 
- Gedeeltelijke VM-Sku's van de kern, zoals Standard a0, worden niet ondersteund voor productie werkbelastingen.
- Een serie-VM-Sku's worden niet ondersteund voor productie workloads om prestatie redenen.
- Vm's met een lage prioriteit worden niet ondersteund.

> [!WARNING]
> Het wijzigen van de SKU-grootte van het primaire knoop punt op een actief cluster, is een schaal bewerking en beschreven in de documentatie over schaal [sets voor virtuele machines](virtual-machine-scale-set-scale-node-type-scale-out.md) .

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Richt lijnen voor type capaciteit van niet-primair knoop punt voor stateful werk belastingen

Deze richt lijnen gelden voor stateful werk belastingen met [betrouw bare service Fabric-verzamelingen of betrouw bare actors](service-fabric-choose-framework.md) die u uitvoert in het niet-primaire knooppunt type.

**Aantal VM-exemplaren:** Voor productie werkbelastingen die stateful zijn, is het raadzaam om deze uit te voeren met een minimum aantal en een doel replica van 5. Dit betekent dat u in de stationaire toestand eindigt met een replica (van een replicaset) in elk fout domein en upgrade domein. Het volledige concept van het betrouwbaarheids niveau voor het primaire knooppunt type is een manier om deze instelling voor systeem services op te geven. Daarom is dezelfde overweging ook van toepassing op uw stateful Services.

Voor werk belastingen voor productie is de minimale aanbevolen grootte van het niet-primaire knooppunt type 5 als u stateful werk belastingen uitvoert.

**VM-SKU:** Dit is het knooppunt type waar uw toepassings services worden uitgevoerd. voor de VM-SKU die u kiest, moet rekening worden gehouden met de piek belasting die u in elk knoop punt wilt plaatsen. De capaciteits behoeften van het knooppunt type zijn afhankelijk van de werk belasting die u in het cluster wilt uitvoeren. Daarom kunnen we u niet voorzien van kwalitatieve richt lijnen voor uw specifieke werk belasting, maar dit is de brede richt lijnen die u helpen om aan de slag te gaan

Voor werk belastingen voor productie 

- De aanbevolen VM-SKU is standaard D2_V2 of gelijkwaardig met een minimum van 50 GB lokale SSD.
- De mini maal ondersteunde virtuele machine voor VM-gebruik is Standard_D2_V3 of standaard D1_V2 of gelijkwaardig met mini maal 50 GB lokale SSD. 
- Gedeeltelijke VM-Sku's van de kern, zoals Standard a0, worden niet ondersteund voor productie werkbelastingen.
- Een serie-VM-Sku's worden niet ondersteund voor productie workloads om prestatie redenen.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Richt lijnen voor type capaciteit van niet-primair knoop punt voor stateless workloads

Deze richt lijnen voor stateless workloads die u uitvoert op het niet-primaire knooppunt type.

**Aantal VM-exemplaren:** Voor werk belastingen voor productie die stateless zijn, is de mini maal ondersteunde grootte van het niet-primaire knooppunt type 2. Op deze manier kunt u twee stateless instanties van uw toepassing uitvoeren, zodat uw service het verlies van een VM-exemplaar kan beslaan. 

**VM-SKU:** Dit is het knooppunt type waar uw toepassings services worden uitgevoerd. voor de VM-SKU die u kiest, moet rekening worden gehouden met de piek belasting die u in elk knoop punt wilt plaatsen. De capaciteits behoeften van het knooppunt type worden bepaald door de werk belasting die u in het cluster wilt uitvoeren. We kunnen u niet voorzien van kwalitatieve richt lijnen voor uw specifieke werk belasting.  Dit is echter de brede richt lijnen die u helpen om aan de slag te gaan.

Voor werk belastingen voor productie 

- De aanbevolen VM-SKU is standaard D2_V2 of gelijkwaardig. 
- De mini maal ondersteunde virtuele machine voor VM-gebruik is standaard D1 of standaard D1_V2 of gelijkwaardig. 
- Gedeeltelijke VM-Sku's van de kern, zoals Standard a0, worden niet ondersteund voor productie werkbelastingen.
- Een serie-VM-Sku's worden niet ondersteund voor productie workloads om prestatie redenen.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Volgende stappen
Als u klaar bent met het plannen van de capaciteit en het instellen van een cluster, lees dan het volgende:

* [Service Fabric cluster beveiliging](service-fabric-cluster-security.md)
* [Schalen van Service Fabric cluster](service-fabric-cluster-scaling.md)
* [Planning voor nood herstel](service-fabric-disaster-recovery.md)
* [Relatie tussen Nodetypes en schaalset voor virtuele machines](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
