---
title: Clusterschaling van Azure Service Fabric
description: Meer informatie over het schalen van Azure Service Fabric-clusters in of uit en omhoog of omlaag. Aangezien de toepassing veranderingen vereist, zo kunnen de clusters van de Stof van de Dienst.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: 9dd60a5898b648215fc8b26e49a706a7b19dfeeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258692"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Azure Service Fabric-clusters schalen
Een Service Fabric-cluster is een met het netwerk verbonden set virtuele of fysieke machines waarin uw microservices worden geïmplementeerd en beheerd. Een machine of VM die deel uitmaakt van een cluster wordt een knooppunt genoemd. Clusters kunnen mogelijk duizenden knooppunten bevatten. Nadat u een cluster servicestructuur hebt gemaakt, u het cluster horizontaal schalen (het aantal knooppunten wijzigen) of verticaal wijzigen (de bronnen van de knooppunten wijzigen).  U het cluster op elk gewenst moment schalen, zelfs wanneer workloads op het cluster worden uitgevoerd.  Naarmate het cluster schaalt, schalen uw toepassingen ook automatisch.

Waarom het cluster schalen? Toepassingseisen veranderen in de loop van de tijd.  Mogelijk moet u de clusterresources vergroten om te voldoen aan de toegenomen toepassingsworkload of het netwerkverkeer of clusterbronnen verlagen wanneer de vraag daalt.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>In- en uitschalen of horizontaal schalen
Hiermee wijzigt u het aantal knooppunten in het cluster.  Zodra de nieuwe knooppunten lid worden van het cluster, verplaatst [het Clusterresourcebeheer](service-fabric-cluster-resource-manager-introduction.md) services naar deze knooppunten, waardoor de belasting op de bestaande knooppunten wordt verminderd.  U ook het aantal knooppunten verminderen als de bronnen van het cluster niet efficiënt worden gebruikt.  Als knooppunten het cluster verlaten, gaan services van die knooppunten af en neemt de belasting op de resterende knooppunten toe.  Het verminderen van het aantal knooppunten in een cluster dat in Azure wordt uitgevoerd, kan u geld besparen, omdat u betaalt voor het aantal VM's dat u gebruikt en niet de werkbelasting op die VM's.  

- Voordelen: Oneindige schaal, in theorie.  Als uw toepassing is ontworpen voor schaalbaarheid, u onbeperkte groei mogelijk maken door meer knooppunten toe te voegen.  De tooling in cloudomgevingen maakt het eenvoudig om knooppunten toe te voegen of te verwijderen, zodat u eenvoudig de capaciteit aanpassen en u alleen betaalt voor de resources die u gebruikt.  
- Nadelen: Toepassingen moeten worden [ontworpen voor schaalbaarheid.](service-fabric-concepts-scalability.md)  Toepassingsdatabases en persistentie kunnen ook extra architectonische werkzaamheden vereisen om te schalen.  [Betrouwbare collecties](service-fabric-reliable-services-reliable-collections.md) in servicefabric-stateful services maken het echter veel gemakkelijker om uw toepassingsgegevens te schalen.

Virtuele machineschaalsets zijn een Azure-compute resource die u gebruiken om een verzameling virtuele machines als set te implementeren en te beheren. Elk knooppunttype dat is gedefinieerd in een Azure-cluster, is [ingesteld als een afzonderlijke schaalset.](service-fabric-cluster-nodetypes.md) Elk knooppunttype kan vervolgens onafhankelijk in- of uitschalen, verschillende sets poorten openen en verschillende capaciteitsstatistieken hebben. 

Houd bij het schalen van een Azure-cluster rekening met de volgende richtlijnen:
- primaire knooppunttypen met productieworkloads moeten altijd vijf of meer knooppunten hebben.
- niet-primaire knooppunttypen met stateful productieworkloads moeten altijd vijf of meer knooppunten hebben.
- niet-primaire knooppunttypen met statusloze productieworkloads moeten altijd twee of meer knooppunten hebben.
- Elk knooppunt type [duurzaamheid niveau](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) van Goud of Zilver moet altijd vijf of meer knooppunten.
- Verwijder geen willekeurige VM-exemplaren/-knooppunten uit een knooppunttype, gebruik altijd de functie voor het schalen van virtuele machines. Het verwijderen van willekeurige VM-exemplaren kan een negatieve invloed hebben op het systeemvermogen om de balans goed te laden.
- Als u regels voor automatische schaal gebruikt, stelt u de regels zo in dat het schalen in (verwijderen van VM-exemplaren) één knooppunt tegelijk wordt uitgevoerd. Het is niet veilig om meer dan één instantie tegelijk af te schalen.

Aangezien de typen Service [Fabric-knooppuntin](service-fabric-cluster-scale-up-down.md) uw cluster bestaan uit virtuele machineschaalsets op de backend, u regels voor automatische schaal instellen of elk knooppunttype/virtuele machineschaalset handmatig schalen.

### <a name="programmatic-scaling"></a>Programmatische schaling
In veel scenario's zijn [het handmatig schalen van een cluster of met automatische schaalregels](service-fabric-cluster-scale-up-down.md) goede oplossingen. Voor meer geavanceerde scenario's, hoewel, ze kunnen niet de juiste pasvorm. Mogelijke nadelen van deze benaderingen zijn:

- Voor handmatig schalen moet u zich aanmelden en expliciet schaalbewerkingen aanvragen. Als schaalbewerkingen vaak of op onvoorspelbare tijden nodig zijn, is deze aanpak mogelijk geen goede oplossing.
- Wanneer regels voor automatische schaal een instantie verwijderen uit een virtuele machineschaalset, verwijderen ze de kennis van dat knooppunt niet automatisch uit het bijbehorende cluster Service Fabric, tenzij het knooppunttype een duurzaamheidsniveau van Zilver of Goud heeft. Omdat regels voor automatische schaal werken op schaalniveau (in plaats van op servicefabricniveau), kunnen regels voor automatische schaal servicestructuur servicestructuurknooppunten verwijderen zonder ze op een elegante manier af te sluiten. Deze onbeleefde knooppunt verwijdering zal verlaten 'ghost' Service Fabric knooppunt staat achter na scale-in operaties. Een persoon (of een service) moet de verwijderde knooppuntstatus in het cluster Servicefabric periodiek opruimen.
- Een knooppunttype met een duurzaamheidsniveau van Goud of Zilver ruimt automatisch verwijderde knooppunten op, dus er is geen extra opruiming nodig.
- Hoewel er [veel statistieken](../azure-monitor/platform/autoscale-common-metrics.md) worden ondersteund door regels voor automatische schaal, is het nog steeds een beperkte set. Als in uw scenario wordt gevraagd om schalen op basis van een statistiek die niet in die set is opgenomen, zijn regels voor automatische schaal mogelijk geen goede optie.

Hoe u Service Fabric-schaling moet benaderen, is afhankelijk van uw scenario. Als schalen ongewoon is, is de mogelijkheid om knooppunten handmatig toe te voegen of te verwijderen waarschijnlijk voldoende. Voor meer complexe scenario's, auto-scale regels en SDKs bloot de mogelijkheid om programmatisch schalen bieden krachtige alternatieven.

Azure API's bestaan waarmee toepassingen programmatisch kunnen werken met virtuele machineschaalsets en Service Fabric-clusters. Als bestaande opties voor automatische schaal niet werken voor uw scenario, maken deze API's het mogelijk om aangepaste schalingslogica te implementeren. 

Een benadering voor het implementeren van deze 'zelfgemaakte' automatische schalingfunctionaliteit is het toevoegen van een nieuwe stateless service aan de Service Fabric-toepassing om schaalbewerkingen te beheren. Het maken van uw eigen schalingservice biedt de hoogste mate van controle en aanpassingsvermogen ten opzichte van het schaalgedrag van uw toepassing. Dit kan handig zijn voor scenario's die nauwkeurige controle vereisen over wanneer of hoe een toepassing in- of uitschaalt. Echter, deze controle wordt geleverd met een afweging van code complexiteit. Met behulp van deze aanpak betekent dat je nodig hebt om eigen schaling code, die niet-triviaal. Binnen de `RunAsync` methode van de service kan een set triggers bepalen of schalen vereist is (inclusief het controleren van parameters zoals maximale clustergrootte en schaalvergroting).   

De API die wordt gebruikt voor interacties met de schade machineschaal (zowel om het huidige aantal virtuele machine-exemplaren te controleren als te wijzigen) is de [vloeiende Azure Management Compute-bibliotheek.](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/) De vloeiende compute library biedt een gebruiksvriendelijke API voor interactie met virtuele machineschaalsets.  Gebruik [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient)om te communiceren met het cluster Service Fabric zelf.

De schalingcode hoeft echter niet te worden uitgevoerd als een service in het cluster om te worden geschaald. Beide `IAzure` `FabricClient` en kunnen op afstand verbinding maken met de bijbehorende Azure-bronnen, dus de schalingservice kan eenvoudig een consoletoepassing of Windows-service zijn die van buiten de Service Fabric-toepassing wordt uitgevoerd.

Op basis van deze beperkingen u [meer aangepaste automatische schalingsmodellen implementeren.](service-fabric-cluster-programmatic-scaling.md)

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Op- en omlaag schalen of verticaal schalen 
Hiermee wijzigt u de bronnen (CPU, geheugen of opslag) van knooppunten in het cluster.
- Voordelen: Software- en applicatiearchitectuur blijft hetzelfde.
- Nadelen: Eindige schaal, omdat er een limiet is aan hoeveel u resources op afzonderlijke knooppunten verhogen. Downtime, omdat u fysieke of virtuele machines offline moet halen om resources toe te voegen of te verwijderen.

Virtuele machineschaalsets zijn een Azure-compute resource die u gebruiken om een verzameling virtuele machines als set te implementeren en te beheren. Elk knooppunttype dat is gedefinieerd in een Azure-cluster, is [ingesteld als een afzonderlijke schaalset.](service-fabric-cluster-nodetypes.md) Elk knooppunttype kan vervolgens afzonderlijk worden beheerd.  Als u een knooppunttype omhoog of omlaag schaalt, moet u de SKU van de virtuele machine-exemplaren in de schaalset wijzigen. 

> [!WARNING]
> We raden u aan de VM SKU van een schaalset/knooppunttype niet te wijzigen, tenzij deze wordt uitgevoerd op [Silver-duurzaamheid of meer.](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Het wijzigen van VM SKU-grootte is een gegevensdestructieve infrastructuurbewerking op plaats van gegevens. Zonder enige mogelijkheid om deze wijziging uit te stellen of te controleren, is het mogelijk dat de bewerking gegevensverlies kan veroorzaken voor stateful services of andere onvoorziene operationele problemen kan veroorzaken, zelfs voor stateloze workloads. 
>

Houd bij het schalen van een Azure-cluster rekening met de volgende richtlijn:
- Als u een primair knooppunttype afschaalt, moet u het nooit meer verkleinen dan de [betrouwbaarheidslaag](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) toestaat.

Het proces van het schalen van een knooppunttype naar boven of omlaag is anders, afhankelijk van of het een niet-primair of primair knooppunttype is.

### <a name="scaling-non-primary-node-types"></a>Niet-primaire knooppunttypen schalen
Maak een nieuw knooppunttype met de resources die u nodig hebt.  Werk de plaatsingsbeperkingen van het uitvoeren van services bij om het nieuwe knooppunttype op te nemen.  Geleidelijk (één voor één) het aantal instantietypen van het oude knooppunttype tot nul verlagen, zodat de betrouwbaarheid van het cluster niet wordt beïnvloed.  Services worden geleidelijk gemigreerd naar het nieuwe knooppunttype wanneer het oude knooppunttype wordt buiten gebruik gesteld.

### <a name="scaling-the-primary-node-type"></a>Het primaire knooppunttype schalen
We raden u aan de VM SKU van het primaire knooppunttype niet te wijzigen. Als u meer clustercapaciteit nodig hebt, raden we u aan meer exemplaren toe te voegen. 

Als dat niet mogelijk is, u een nieuw cluster maken en [de toepassingsstatus](service-fabric-reliable-services-backup-restore.md) (indien van toepassing) herstellen vanuit uw oude cluster. U hoeft geen systeemservicestatus te herstellen, deze worden opnieuw gemaakt wanneer u uw toepassingen implementeert in uw nieuwe cluster. Als u alleen stateloze toepassingen op uw cluster uitvoert, u uw toepassingen alleen maar implementeren naar het nieuwe cluster, maar u hoeft niets te herstellen. Als u besluit de niet-ondersteunde route te gaan en de VM SKU wilt wijzigen, moet u de modeldefinitie van de virtuele machineschaal instellen om de nieuwe SKU weer te geven. Als uw cluster slechts één knooppunttype heeft, moet u ervoor zorgen dat al uw stateful-toepassingen tijdig reageren op alle gebeurtenissen in de [levenscyclus van servicereplica's](service-fabric-reliable-services-lifecycle.md) (zoals replica in build) en dat de duur van de wederopbouw van uw servicereplica minder dan vijf minuten is (voor het duurzaamheidsniveau van Zilver). 

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [schaalbaarheid van toepassingen](service-fabric-concepts-scalability.md).
* [Een Azure-cluster in- of uitchecken](service-fabric-tutorial-scale-cluster.md).
* [Schaal een Azure-cluster programmatisch](service-fabric-cluster-programmatic-scaling.md) met de vloeiende Azure compute SDK.
* [Een zelfstandig cluster in- of uitchecken](service-fabric-cluster-windows-server-add-remove-nodes.md).

