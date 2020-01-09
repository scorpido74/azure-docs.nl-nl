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
ms.sourcegitcommit: 801e9118fae92f8eef8d846da009dddbd217a187
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/27/2019
ms.locfileid: "75500002"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Inleiding tot Azure Cosmos DB: Gremlin API

[Azure Cosmos DB](introduction.md) is de wereld wijd gedistribueerde, multi-model database service van micro soft voor essentiële toepassingen. Het is een Data Base met meerdere modellen die ondersteuning biedt voor document-, sleutel waarde-, grafiek-en kolom gegevens modellen. De Azure Cosmos DB Gremlin-API wordt gebruikt om grafiek gegevens op te slaan en te gebruiken in een volledig beheerde database service die is ontworpen voor elke schaal.  

![Azure Cosmos DB-grafiekarchitectuur](./media/graph-introduction/cosmosdb-graph-architecture.png)

Dit artikel biedt een overzicht van de Azure Cosmos DB Gremlin API en legt uit hoe u deze kunt gebruiken om zeer grote grafieken met miljarden hoekpunten en randen op te slaan. U kunt een query uitvoeren op de grafieken met milliseconde latentie en de grafiek structuur eenvoudig ontwikkelen. De Gremlin-API van Azure Cosmos DB is gebaseerd op de [Apache TinkerPop](https://tinkerpop.apache.org) Graph data base Standard en maakt gebruik van de Gremlin-query taal. 

De Gremlin-API van Azure Cosmos DB combineert de kracht van Graph-database algoritmen met een zeer schaal bare, beheerde infra structuur om een unieke, flexibele oplossing te bieden voor de meest voorkomende gegevens problemen die betrekking hebben op het ontbreken van flexibiliteit en relationele benaderingen. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Functies van Azure Cosmos DB-graafdatabase
 
Azure Cosmos DB is een volledig beheerde grafiekdatabase met wereldwijde distributie, elastisch schalen van opslag en doorvoer, automatisch indexeren en doorzoeken, instelbare consistentieniveaus, en ondersteuning voor de standaardversie van TinkerPop. 

Hieronder vindt u de onderscheidende functies die Azure Cosmos DB Gremlin API-aanbiedingen:

* **Elastisch schaal bare door Voer en opslag**

  Grafieken moeten in de praktijk hoger worden geschaald dan de capaciteit van een enkele server. Azure Cosmos DB ondersteunt horizon taal schaal bare grafische data bases die een vrijwel onbeperkte grootte kunnen hebben met betrekking tot opslag en ingerichte door voer. Naarmate de schaal van de grafiek database groeit, worden de gegevens automatisch gedistribueerd met behulp van [Graph-partitionering](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

* **Replicatie met meerdere regio's**

  Azure Cosmos DB kunt uw grafiek gegevens automatisch repliceren naar elke Azure-regio. Globale Replicatie vereenvoudigt de ontwikkeling van toepassingen waarvoor globale toegang tot gegevens is vereist. Naast het minimaliseren van de latentie van lees-en schrijf bewerkingen overal ter wereld, biedt Azure Cosmos DB automatische regionale failover-mechanisme waarmee de continuïteit van uw toepassing in zeldzame gevallen van een onderbreking van de service in een regio kan worden gegarandeerd. 

* **Snelle query's en door lopen met de meestgebruikte grafiek query standaard**

  Sla heterogene hoek punten en randen op en vraag ze op met een vertrouwde Gremlin-syntaxis. Gremlin is een belang rijke, functionele query taal die een uitgebreide interface biedt voor het implementeren van algemene grafiek algoritmen. 
  
  Azure Cosmos DB biedt uitgebreide realtime query's en interacties zonder dat u schema hints, secundaire indexen of weer gaven hoeft op te geven. Meer informatie vindt u in [Querygrafieken met behulp van Gremlin](gremlin-support.md).

* **Volledig beheerde Graph-data base**

  Dankzij Azure Cosmos DB is het niet meer nodig om database- en machineresources te beheren. De meeste bestaande grafieken van de grafiek database zijn gebonden aan de beperkingen van hun infra structuur en vereisen vaak een hoge mate van onderhoud om de werking ervan te garanderen. 
  
  Als volledig beheerde service verwijdert Cosmos DB de nood zaak voor het beheren van virtuele machines, het bijwerken van runtime-software, het beheren van sharding of replicatie of het afhandelen van complexe gegevenslaag upgrades. Er wordt automatisch een back-up van elke grafiek gemaakt en de databases worden automatisch beveiligd tegen regionale fouten. Hierdoor kunnen ontwikkel aars zich richten op het leveren van toepassings waarde in plaats van het besturings systeem te gebruiken en hun grafiek databases te beheren. 

* **Automatisch indexeren**

  In Azure Cosmos DB worden standaard alle eigenschappen in de knooppunten en randen van de grafiek geïndexeerd. Er wordt geen schema verwacht of vereist en er hoeven geen secundaire indexen te worden gemaakt. Meer informatie over [indexeren in azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview). 

* **Compatibiliteit met Apache TinkerPop**

  Azure Cosmos DB ondersteunt de [Open Source Apache TinkerPop-standaard](https://tinkerpop.apache.org/). De Tinkerpop-standaard heeft een ruim ecosysteem van toepassingen en bibliotheken die eenvoudig kunnen worden geïntegreerd met de Gremlin-API van Azure Cosmos DB. 

* **Instel bare-consistentie niveaus**

  Azure Cosmos DB biedt vijf goed gedefinieerde consistentie niveaus om de juiste balans tussen consistentie en prestaties van uw toepassing te krijgen. Voor query's en leesbewerkingen biedt Azure Cosmos DB vijf verschillende consistentieniveaus: sterk, gebonden-verouderd, sessie, consistent voorvoegsel en mogelijk. Op basis van deze gedetailleerde, goed gedefinieerde consistentieniveaus kunt u een goede balans vinden tussen de consistentie, beschikbaarheid en latentie. Meer informatie over [Instelbare gegevensconsistentieniveaus in Azure Cosmos DB](consistency-levels.md).

## <a name="scenarios-that-can-use-gremlin-api"></a>Scenario's waarin de Gremlin-API kan worden gebruikt
Hier volgen enkele scenario's waarin de ondersteuning van de grafiek van Azure Cosmos DB nuttig kan zijn:

* **Sociale netwerken/klant 365**

  Door gegevens over uw klanten en hun interacties met andere personen te combineren kunt u een persoonlijke ervaring ontwikkelen, het gedrag van klanten voorspellen of personen in contact brengen met anderen die gelijksoortige interesses hebben. Azure Cosmos DB kan worden gebruikt om sociale netwerken te beheren en klantvoorkeuren en -gegevens bij te houden.

* **Aanbevolen engines**

  Dit scenario wordt doorgaans gebruikt in de detailhandel. Door informatie over producten, gebruikers en interactie tussen gebruikers, zoals het kopen, zoeken of beoordelen van een item, te combineren, kunt u aangepaste aanbevelingen genereren. De ondersteuning voor de lage latentie, elastische schaal en de systeem eigen grafiek van Azure Cosmos DB is ideaal voor deze scenario's.

* **Georuimtelijke**

  In veel toepassingen voor telecommunicatie, logistiek en reisplanning moet vaak worden gezocht naar belangrijke locaties binnen een gebied, of moet de kortste/snelste route tussen twee locaties worden berekend. Azure Cosmos DB is zeer geschikt voor dit soort taken.

* **Internet of Things**

  Omdat het netwerk en de verbindingen tussen IoT-apparaten zijn gemodelleerd als een grafiek krijgt u een beter beeld van de status van uw apparaten en activa. U krijgt ook inzicht in hoe wijzigingen in één deel van het netwerk van invloed kunnen zijn op een ander deel.

## <a name="introduction-to-graph-databases"></a>Inleiding tot Graph-data bases
Gegevens zoals deze in werkelijkheid voorkomen, zijn op natuurlijke wijze verbonden. Traditionele gegevens modellering is gericht op het afzonderlijk definiëren van entiteiten en het berekenen van hun relaties tijdens runtime. Hoewel dit model over de voor delen beschikt, kunnen zeer verbonden gegevens lastig te beheren zijn onder de beperkingen.  

Een grafiek database aanpak is afhankelijk van het persistent maken van relaties in de opslaglaag, wat leidt tot zeer efficiënte bewerkingen voor het ophalen van grafieken. De Gremlin-API van Azure Cosmos DB ondersteunt het [Eigenschappen grafiek model](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Eigenschappen grafiek objecten

Een eigenschaps [grafiek](http://mathworld.wolfram.com/Graph.html) is een structuur die bestaat uit [hoek punten](http://mathworld.wolfram.com/GraphVertex.html) en [randen](http://mathworld.wolfram.com/GraphEdge.html). Beide objecten kunnen een wille keurig aantal sleutel-waardeparen als eigenschappen hebben. 

* **Vertices** : vertices duiden discrete entiteiten aan, zoals een persoon, een plaats of een gebeurtenis.

* **Kanten**: Kanten duiden relaties tussen hoekpunten aan. Bijvoorbeeld, een persoon kent mogelijk een andere persoon, is betrokken bij een gebeurtenis, en is onlangs op een bepaalde locatie geweest. 

* **Eigenschappen**: Eigenschappen geven informatie over de hoekpunten en kanten. Er kan een wille keurig aantal eigenschappen zijn in hoek punten of randen, en ze kunnen worden gebruikt om de objecten in een query te beschrijven en te filteren. Voor beelden van eigenschappen zijn een hoek punt met naam en leeftijd, of een rand, die een tijds tempel en/of gewicht kan hebben. 

Graph-data bases worden vaak opgenomen in de categorie NoSQL of niet-relationele data base, omdat er geen afhankelijkheid is van een schema of beperkt gegevens model. Dit gebrek aan schema maakt het mogelijk om op natuurlijke en efficiënte wijze gekoppelde structuren te model leren en op te slaan. 

### <a name="gremlin-by-example"></a>Gremlin via een voorbeeld
Laten we een voorbeeldgrafiek gebruiken om te zien hoe query’s kunnen worden uitgedrukt in Gremlin. In de volgende afbeelding ziet u een zakelijke toepassing waarmee gegevens over gebruikers, interesses en apparaten worden beheerd, in de vorm van een grafiek.  

![Voorbeelddatabase met personen, apparaten en interesses](./media/gremlin-support/sample-graph.png) 

Deze grafiek heeft de volgende *vertex* typen (' label ') in Gremlin):

- **Personen**: de grafiek heeft drie personen, Robin, Thomas en ben
- **Interesses**: in dit voor beeld is het spelletje van voetbal
- **Apparaten**: de apparaten die mensen gebruiken
- **Besturings systemen**: de besturings systemen waarop de apparaten worden uitgevoerd

We vertegenwoordigen de relaties tussen deze entiteiten met behulp van de volgende *rand* typen/Labels:

- **Weet**: bijvoorbeeld ' Thomas weet '
- **Geïnteresseerd**: voor de weer gave van de interesses van de mensen in onze grafiek, bijvoorbeeld ' ben is geïnteresseerd in voetbal '
- **RunsOS**: laptop voert het Windows-besturings systeem uit
- **Gebruikt**: om aan te geven welk apparaat een persoon gebruikt. Robin gebruikt bijvoorbeeld een Motorola-telefoon met serienummer 77

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

Waar grafieken in uitblinken is in het beantwoorden van vragen zoals: Welke besturingssystemen gebruiken de vrienden van Thomas? U kunt deze Gremlin-passage uitvoeren om die informatie op te halen uit de grafiek:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>Volgende stappen
Voor meer informatie over ondersteuning voor grafieken in Azure Cosmos DB raadpleegt u:

* Aan de slag met de [zelfstudie voor grafieken in Azure Cosmos DB](create-graph-dotnet.md).
* Meer informatie over het [doorzoeken van grafieken in Azure Cosmos DB met behulp van Gremlin](gremlin-support.md).
