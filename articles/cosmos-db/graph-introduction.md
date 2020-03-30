---
title: Inleiding tot de Gremlin-API van Azure Cosmos DB
description: Leer hoe u Azure Cosmos DB kunt gebruiken om zeer grote grafieken met een lage latentie op te slaan, te doorzoeken en erin te bladeren, met behulp van de querytaal voor Gremlin-grafieken van Apache TinkerPop.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 07/18/2019
ms.author: lbosq
ms.openlocfilehash: 9151b54d7fa0b64a465aa8384cb4bfdb8e72c482
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75500002"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Inleiding tot Azure Cosmos DB: Gremlin API

[Azure Cosmos DB](introduction.md) is de wereldwijd gedistribueerde databaseservice met meerdere modellen van Microsoft voor bedrijfskritieke toepassingen. Het is een database met meerdere modellen en ondersteunt document-, sleutelwaarde-, grafiek- en kolom-familiegegevensmodellen. De Azure Cosmos DB Gremlin API wordt gebruikt om grafiekgegevens op te slaan en te bedienen op een volledig beheerde databaseservice die is ontworpen voor elke schaal.  

![Azure Cosmos DB-grafiekarchitectuur](./media/graph-introduction/cosmosdb-graph-architecture.png)

Dit artikel biedt een overzicht van de Azure Cosmos DB Gremlin API en legt uit hoe u deze kunt gebruiken om zeer grote grafieken met miljarden hoekpunten en randen op te slaan. U de grafieken met milliseconde latentie opvragen en de grafiekstructuur eenvoudig ontwikkelen. De Gremlin API van Azure Cosmos DB is gebaseerd op de [Apache TinkerPop-grafiekdatabasestandaard](https://tinkerpop.apache.org) en maakt gebruik van de Gremlin-querytaal. 

De Gremlin API van Azure Cosmos DB combineert de kracht van grafische databasealgoritmen met zeer schaalbare, beheerde infrastructuur om een unieke, flexibele oplossing te bieden voor de meest voorkomende gegevensproblemen in verband met gebrek aan flexibiliteit en relationele benaderingen. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Functies van Azure Cosmos DB-graafdatabase
 
Azure Cosmos DB is een volledig beheerde grafiekdatabase met wereldwijde distributie, elastisch schalen van opslag en doorvoer, automatisch indexeren en doorzoeken, instelbare consistentieniveaus, en ondersteuning voor de standaardversie van TinkerPop. 

Hieronder volgen de gedifferentieerde functies die Azure Cosmos DB Gremlin API biedt:

* **Elastische schaalbare doorvoer en opslag**

  Grafieken moeten in de praktijk hoger worden geschaald dan de capaciteit van een enkele server. Azure Cosmos DB ondersteunt horizontaal schaalbare grafiekdatabases die een vrijwel onbeperkte grootte kunnen hebben op het gebied van opslag en ingerichte doorvoer. Naarmate de grafiekdatabaseschaal toeneemt, worden de gegevens automatisch gedistribueerd met [behulp van grafiekpartitionering.](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning)

* **Replicatie met meerdere regio’s**

  Azure Cosmos DB kan uw grafiekgegevens automatisch repliceren naar elke Azure-regio wereldwijd. Globale replicatie vereenvoudigt de ontwikkeling van toepassingen waarvoor wereldwijde toegang tot gegevens vereist is. Azure Cosmos DB minimaliseert niet alleen lees- en schrijflatentie overal ter wereld, maar biedt een automatisch regionaal failovermechanisme dat de continuïteit van uw toepassing kan garanderen in het zeldzame geval van een onderbreking van de service in een regio. 

* **Snelle query's en traversals met de meest gebruikte grafiekquerystandaard**

  Sla heterogene vertices en randen op en query ze door middel van een bekende Gremlin syntaxis. Gremlin is een noodzakelijke, functionele querytaal die een rijke interface biedt om algemene grafiekalgoritmen te implementeren. 
  
  Azure Cosmos DB maakt uitgebreide realtime query's en traversals mogelijk zonder schemahints, secundaire indexen of weergaven op te geven. Meer informatie vindt u in [Querygrafieken met behulp van Gremlin](gremlin-support.md).

* **Volledig beheerde grafiekdatabase**

  Dankzij Azure Cosmos DB is het niet meer nodig om database- en machineresources te beheren. De meeste bestaande grafische databaseplatforms zijn gebonden aan de beperkingen van hun infrastructuur en vereisen vaak een hoge mate van onderhoud om de werking ervan te garanderen. 
  
  Als volledig beheerde service verwijdert Cosmos DB de noodzaak om virtuele machines te beheren, runtime-software bij te werken, sharding of replicatie te beheren of complexe upgrades op datalagen aan te pakken. Er wordt automatisch een back-up van elke grafiek gemaakt en de databases worden automatisch beveiligd tegen regionale fouten. Met deze garanties kunnen ontwikkelaars zich richten op het leveren van toepassingswaarde in plaats van hun grafiekdatabases te beheren en te beheren. 

* **Automatisch indexeren**

  In Azure Cosmos DB worden standaard alle eigenschappen in de knooppunten en randen van de grafiek geïndexeerd. Er wordt geen schema verwacht of vereist en er hoeven geen secundaire indexen te worden gemaakt. Meer informatie over [indexeren in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview). 

* **Compatibiliteit met Apache TinkerPop**

  Azure Cosmos DB ondersteunt de [open-source Apache TinkerPop standaard.](https://tinkerpop.apache.org/) De Tinkerpop-standaard heeft een uitgebreid ecosysteem van toepassingen en bibliotheken die eenvoudig kunnen worden geïntegreerd met de Gremlin API van Azure Cosmos DB. 

* **Instelbare consistentieniveaus**

  Azure Cosmos DB biedt vijf goed gedefinieerde consistentieniveaus om de juiste afweging te bereiken tussen consistentie en prestaties voor uw toepassing. Voor query's en leesbewerkingen biedt Azure Cosmos DB vijf verschillende consistentieniveaus: sterk, gebonden-verouderd, sessie, consistent voorvoegsel en mogelijk. Op basis van deze gedetailleerde, goed gedefinieerde consistentieniveaus kunt u een goede balans vinden tussen de consistentie, beschikbaarheid en latentie. Meer informatie over [Instelbare gegevensconsistentieniveaus in Azure Cosmos DB](consistency-levels.md).

## <a name="scenarios-that-can-use-gremlin-api"></a>Scenario's waarin de Gremlin-API kan worden gebruikt
Hier volgen enkele scenario's waarin grafische ondersteuning van Azure Cosmos DB nuttig kan zijn:

* **Sociale netwerken/Klant 365**

  Door gegevens over uw klanten en hun interacties met andere personen te combineren kunt u een persoonlijke ervaring ontwikkelen, het gedrag van klanten voorspellen of personen in contact brengen met anderen die gelijksoortige interesses hebben. Azure Cosmos DB kan worden gebruikt om sociale netwerken te beheren en klantvoorkeuren en -gegevens bij te houden.

* **Aanbevelingsengines**

  Dit scenario wordt doorgaans gebruikt in de detailhandel. Door informatie over producten, gebruikers en interactie tussen gebruikers, zoals het kopen, zoeken of beoordelen van een item, te combineren, kunt u aangepaste aanbevelingen genereren. De ondersteuning voor lage latentie, elastische schaal en native grafiek van Azure Cosmos DB is ideaal voor deze scenario's.

* **Georuimtelijk**

  In veel toepassingen voor telecommunicatie, logistiek en reisplanning moet vaak worden gezocht naar belangrijke locaties binnen een gebied, of moet de kortste/snelste route tussen twee locaties worden berekend. Azure Cosmos DB is zeer geschikt voor dit soort taken.

* **Internet of Things**

  Omdat het netwerk en de verbindingen tussen IoT-apparaten zijn gemodelleerd als een grafiek krijgt u een beter beeld van de status van uw apparaten en activa. U krijgt ook inzicht in hoe wijzigingen in één deel van het netwerk van invloed kunnen zijn op een ander deel.

## <a name="introduction-to-graph-databases"></a>Inleiding tot grafiekdatabases
Gegevens zoals deze in werkelijkheid voorkomen, zijn op natuurlijke wijze verbonden. Traditionele gegevensmodellering richt zich op het afzonderlijk definiëren van entiteiten en het berekenen van hun relaties tijdens runtime. Hoewel dit model zijn voordelen heeft, kunnen sterk verbonden gegevens een uitdaging zijn om te beheren onder zijn beperkingen.  

Een grafiekdatabasebenadering is gebaseerd op aanhoudende relaties in de opslaglaag, wat leidt tot zeer efficiënte bewerkingen voor het ophalen van grafieken. De Gremlin-API van Azure Cosmos DB ondersteunt het [eigenschappengrafiekmodel](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Objecten in de eigenschappengrafiek

Een [eigenschappengrafiek](http://mathworld.wolfram.com/Graph.html) is een structuur die bestaat uit [vertices](http://mathworld.wolfram.com/GraphVertex.html) en [randen.](http://mathworld.wolfram.com/GraphEdge.html) Beide objecten kunnen een willekeurig aantal sleutelwaardeparen als eigenschappen hebben. 

* **Vertices** - Vertices duidt afzonderlijke entiteiten aan, zoals een persoon, een plaats of een gebeurtenis.

* **Kanten**: Kanten duiden relaties tussen hoekpunten aan. Bijvoorbeeld, een persoon kent mogelijk een andere persoon, is betrokken bij een gebeurtenis, en is onlangs op een bepaalde locatie geweest. 

* **Eigenschappen**: Eigenschappen geven informatie over de hoekpunten en kanten. Er kunnen een willekeurig aantal eigenschappen in vertices of randen zijn en ze kunnen worden gebruikt om de objecten in een query te beschrijven en te filteren. Voorbeelden van eigenschappen zijn een hoekpunt met naam en leeftijd, of een rand, die een tijdstempel en/of een gewicht kan hebben. 

Grafiekdatabases worden vaak opgenomen in de categorie NoSQL of niet-relationele database, omdat er geen afhankelijkheid is van een schema of beperkt gegevensmodel. Dit gebrek aan schema maakt het mogelijk om verbonden structuren op een natuurlijke en efficiënte manier te modelleren en op te slaan. 

### <a name="gremlin-by-example"></a>Gremlin via een voorbeeld
Laten we een voorbeeldgrafiek gebruiken om te zien hoe query’s kunnen worden uitgedrukt in Gremlin. In de volgende afbeelding ziet u een zakelijke toepassing waarmee gegevens over gebruikers, interesses en apparaten worden beheerd, in de vorm van een grafiek.  

![Voorbeelddatabase met personen, apparaten en interesses](./media/gremlin-support/sample-graph.png) 

Deze grafiek heeft de volgende *vertex* types (genaamd "label" in Gremlin):

- **Mensen**: De grafiek heeft drie mensen, Robin, Thomas en Ben
- **Interesses**: Hun belangen, in dit voorbeeld, het spel van voetbal
- **Apparaten**: de apparaten die mensen gebruiken
- **Besturingssystemen**: De besturingssystemen waarop de apparaten draaien

Wij vertegenwoordigen de relaties tussen *edge* deze entiteiten via de volgende randtypen/labels:

- **Weet:** Bijvoorbeeld, "Thomas kent Robin"
- **Geïnteresseerd :** Om de belangen van de mensen in onze grafiek te vertegenwoordigen, bijvoorbeeld, "Ben is geïnteresseerd in voetbal"
- **RunsOS**: Laptop draait het Windows OS
- **Gebruikt:** Om aan te geven welk apparaat een persoon gebruikt. Robin gebruikt bijvoorbeeld een Motorola-telefoon met serienummer 77

We gaan enkele bewerkingen uitvoeren in deze grafiek met behulp van de [Gremlin-console](https://tinkerpop.apache.org/docs/3.3.2/reference/#gremlin-console). U kunt deze bewerkingen ook uitvoeren met behulp van Gremlin-stuurprogramma’s op een platform naar keuze (Java, Node.js, Python of NET).  Voordat we kijken wat wordt ondersteund in Azure Cosmos DB, bekijken we een aantal voorbeelden om vertrouwd te raken met de syntaxis.

Eerst kijken we naar CRUD. Met de volgende Gremlin-instructie wordt het hoekpunt Thomas ingevoegd in de grafiek:

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Vervolgens wordt met de volgende Gremlin-instructie de rand Kent ingevoegd tussen Thomas en Robin.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Met de volgende query worden de hoekpunten voor Persoon in aflopende volgorde van de voornamen geretourneerd:
```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Waar grafieken in uitblinken is in het beantwoorden van vragen zoals: Welke besturingssystemen gebruiken de vrienden van Thomas? U deze Gremlin traversal uitvoeren om die informatie uit de grafiek te halen:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>Volgende stappen
Voor meer informatie over ondersteuning voor grafieken in Azure Cosmos DB raadpleegt u:

* Aan de slag met de [zelfstudie voor grafieken in Azure Cosmos DB](create-graph-dotnet.md).
* Meer informatie over het [doorzoeken van grafieken in Azure Cosmos DB met behulp van Gremlin](gremlin-support.md).
