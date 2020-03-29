---
title: Capaciteitsplanning voor Service Fabric-apps
description: Beschrijft hoe u het aantal rekenknooppunten identificeren dat nodig is voor een Service Fabric-toepassing
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cd5a5c55ff873e4891ac63361d0c4a0b56d70109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377205"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Capaciteitsplanning voor Service Fabric-toepassingen
In dit document leert u hoe u de hoeveelheid resources (CPU's, RAM, schijfopslag) schatten die u nodig hebt om uw Azure Service Fabric-toepassingen uit te voeren. Het is gebruikelijk dat uw resourcevereisten in de loop van de tijd veranderen. U vereist meestal weinig resources als u uw service ontwikkelt/test en hebt vervolgens meer resources nodig als u in productie gaat en uw toepassing in populariteit groeit. Wanneer u uw toepassing ontwerpt, denkt u na over de langetermijnvereisten en maakt u keuzes waarmee uw service kan worden geschaald om aan de hoge vraag van de klant te voldoen.

 Wanneer u een cluster servicestructuur maakt, bepaalt u welke soorten virtuele machines (VM's) het cluster vormen. Elke VM wordt geleverd met een beperkte hoeveelheid resources in de vorm van CPU's (cores en snelheid), netwerkbandbreedte, RAM en schijfopslag. Naarmate uw service in de loop van de tijd groeit, u upgraden naar VM's die meer resources bieden en/of meer VM's aan uw cluster toevoegen. Om dit laatste te doen, moet u uw service in eerste instantie ontwerpen, zodat deze kan profiteren van nieuwe VM's die dynamisch aan het cluster worden toegevoegd.

Sommige diensten beheren weinig tot geen gegevens over de VM's zelf. Daarom moet de capaciteitsplanning voor deze services zich in de eerste plaats richten op prestaties, wat betekent dat de juiste CPU's (kernen en snelheid) van de VM's moeten worden geselecteerd. Daarnaast moet u rekening houden met netwerkbandbreedte, inclusief hoe vaak netwerkoverdrachten plaatsvinden en hoeveel gegevens worden overgedragen. Als uw service goed moet presteren naarmate het servicegebruik toeneemt, u meer VM's aan het cluster toevoegen en de netwerkaanvragen in alle VM's in balans brengen.

Voor services die grote hoeveelheden gegevens over de VM's beheren, moet capaciteitsplanning zich voornamelijk richten op grootte. U moet dus zorgvuldig rekening houden met de capaciteit van het RAM- en schijfopslag van de VM. Het virtuele geheugenbeheersysteem in Windows zorgt ervoor dat schijfruimte eruit ziet als RAM-code voor toepassingscode. Bovendien biedt de Service Fabric-runtime slimme paging die alleen hete gegevens in het geheugen bewaart en de koude gegevens naar de schijf verplaatst. Toepassingen kunnen dus meer geheugen gebruiken dan fysiek beschikbaar is op de VM. Meer RAM verhoogt eenvoudig de prestaties, omdat de VM meer schijfopslag in RAM kan houden. De virtuele machine moet een schijf hebben die groot genoeg is om de gewenste gegevens op de virtuele machine op te slaan. Op dezelfde manier moet de VM genoeg RAM hebben om u te voorzien van de prestaties die u wenst. Als de gegevens van uw service in de loop van de tijd groeien, u meer VM's aan het cluster toevoegen en de gegevens over alle VM's verdelen.

## <a name="determine-how-many-nodes-you-need"></a>Bepalen hoeveel knooppunten u nodig hebt
Door uw service te partitioneren, u de gegevens van uw service uitschalen. Zie [Partitionerende servicefabric](service-fabric-concepts-partitioning.md)voor meer informatie over partitionering. Elke partitie moet binnen één VM passen, maar meerdere (kleine) partities kunnen op één vm worden geplaatst. Dus, met meer kleine partities geeft u meer flexibiliteit dan met een paar grotere partities. De trade-off is dat het hebben van veel partities de overhead van Service Fabric verhoogt en u geen transacties uitvoeren op partities. Er is ook meer potentieel netwerkverkeer als uw servicecode vaak toegang moet krijgen tot gegevens die in verschillende partities leven. Bij het ontwerpen van uw service, moet u zorgvuldig overwegen deze voors en tegens om te komen tot een effectieve partitionering strategie.

Laten we aannemen dat uw toepassing heeft een stateful service die een winkel grootte die u verwacht te groeien tot DB_Size GB in een jaar heeft. U bent bereid om meer toepassingen (en partities) toe te voegen als u groei ervaart na dat jaar.  De replicatiefactor (RF) die het aantal replica's voor uw service bepaalt, heeft invloed op het totale DB_Size. Het totale DB_Size voor alle replica's is de replicatiefactor vermenigvuldigd met DB_Size.  Node_Size staat voor de schijfruimte/RAM per knooppunt die u voor uw service wilt gebruiken. Voor de beste prestaties moet de DB_Size in het hele cluster in het geheugen passen en moet een Node_Size dat zich rond het RAM-geheugen van de VM bevindt, worden gekozen. Door een Node_Size toe te wijzen die groter is dan de RAM-capaciteit, vertrouwt u op de paging die wordt geleverd door de Runtime van De Service Fabric. Uw prestaties zijn dus mogelijk niet optimaal als uw volledige gegevens als hot worden beschouwd (sindsdien worden de gegevens in/uit gepiept). Echter, voor veel diensten waar slechts een fractie van de gegevens heet is, is het kosteneffectiever.

Het aantal knooppunten dat nodig is voor maximale prestaties kan als volgt worden berekend:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Rekening houden met groei
U het aantal knooppunten berekenen op basis van de DB_Size waarvan u verwacht dat uw service zal groeien, naast de DB_Size waarmee u bent begonnen. Vergroot vervolgens het aantal knooppunten naarmate uw service groeit, zodat u het aantal knooppunten niet over-inricht. Maar het aantal partities moet worden gebaseerd op het aantal knooppunten dat nodig is wanneer u uw service uitvoert op maximale groei.

Het is goed om op elk moment wat extra machines beschikbaar te hebben, zodat u onverwachte pieken of storingen aankunt (bijvoorbeeld als een paar VM's naar beneden gaan).  Terwijl de extra capaciteit moet worden bepaald door het gebruik van uw verwachte spikes, een uitgangspunt is het reserveren van een paar extra VM's (5-10 procent extra).

De voorgaande gaat uit van één stateful service. Als u meer dan één stateful service hebt, moet u de DB_Size die aan de andere services zijn gekoppeld, aan de vergelijking toevoegen. U ook het aantal knooppunten afzonderlijk berekenen voor elke stateful service.  Uw service kan replica's of partities hebben die niet in balans zijn. Houd er rekening mee dat partities ook meer gegevens kunnen bevatten dan andere. Zie artikel over best practices voor meer informatie over [partitionering](service-fabric-concepts-partitioning.md). De voorgaande vergelijking is echter partitie- en replicaagnos, omdat Service Fabric ervoor zorgt dat de replica's op een geoptimaliseerde manier over de knooppunten worden verspreid.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Een spreadsheet gebruiken voor kostenberekening
Laten we nu wat echte getallen in de formule zetten. In [een voorbeeldspreadsheet](https://github.com/Azure/service-fabric/raw/master/docs_resources/SF_VM_Cost_calculator-NEW.xlsx) ziet u hoe u de capaciteit plant voor een toepassing die drie typen gegevensobjecten bevat. Voor elk object benaderen we de grootte en het aantal objecten dat we verwachten te hebben. We selecteren ook hoeveel replica's we van elk objecttype willen. De spreadsheet berekent de totale hoeveelheid geheugen die in het cluster moet worden opgeslagen.

Vervolgens voeren we een VM-grootte en maandelijkse kosten in. Op basis van de VM-grootte geeft de spreadsheet aan hoe minimaal het aantal partities is dat u moet gebruiken om uw gegevens te splitsen om fysiek op de knooppunten te passen. U een groter aantal partities wensen om tegemoet te komen aan de specifieke berekenings- en netwerkverkeersbehoeften van uw toepassing. De spreadsheet toont het aantal partities dat de gebruikersprofielobjecten beheert, is gestegen van één naar zes.

Nu, op basis van al deze informatie, de spreadsheet laat zien dat je fysiek alle gegevens met de gewenste partities en replica's op een cluster met 26-node kon krijgen. Dit cluster zou echter dicht op elkaar zijn ingeslagen, dus u wilt mogelijk een aantal extra knooppunten om knooppuntfouten en upgrades mogelijk op te vangen. De spreadsheet toont ook aan dat het hebben van meer dan 57 knooppunten geen extra waarde biedt omdat u lege knooppunten zou hebben. Nogmaals, wilt u misschien toch boven de 57 knooppunten gaan om knooppuntfouten en upgrades mogelijk te verwerken. U de spreadsheet aanpassen aan de specifieke behoeften van uw toepassing.   

![Spreadsheet voor kostenberekening][Image1]

## <a name="next-steps"></a>Volgende stappen
Bekijk [partitioneringservicefabric-services][10] voor meer informatie over het partitioneren van uw service.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
