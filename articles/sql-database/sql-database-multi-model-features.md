---
title: Azure SQL Database mogelijkheden voor meerdere modellen
description: Met Azure SQL Database kunt u werken met meerdere gegevens modellen in dezelfde data base.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: 7156b9923c9cb98ae3dde143c98eb32a6eb11a9c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687725"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Mogelijkheden van meerdere modellen van Azure SQL Database

Data bases met meerdere modellen bieden de mogelijkheid om gegevens op te slaan en te gebruiken die zijn opgenomen in meerdere gegevens indelingen, zoals relationele gegevens, grafieken, JSON/XML-documenten, sleutel-waardeparen, enzovoort.

## <a name="when-to-use-multi-model-capabilities"></a>Wanneer u multi-model mogelijkheden gebruikt

Azure SQL Database is ontworpen om te werken met het relationele model dat de beste prestaties biedt in de meeste gevallen voor diverse toepassingen voor algemeen gebruik. Azure SQL Database is echter niet beperkt tot relationele gegevens. Met Azure SQL Database kunt u een groot aantal niet-relationele indelingen gebruiken die nauw geïntegreerd zijn in het relationele model.
U kunt in de volgende gevallen gebruikmaken van mogelijkheden voor het gebruik van meerdere modellen van Azure SQL Database:
- U hebt een aantal informatie of structuren die beter passen voor NoSQL-modellen en u geen afzonderlijke NoSQL-Data Base wilt gebruiken.
- Een meerderheid van uw gegevens is geschikt voor relationele modellen en u moet een deel van uw gegevens in NoSQL-stijl model leren.
- U wilt gebruikmaken van de uitgebreide Transact-SQL-taal voor het opvragen en analyseren van relationele en NoSQL-gegevens en deze te integreren met een aantal hulpprogram ma's en toepassingen die SQL-taal kunnen gebruiken.
- U wilt database functies, zoals [in-Memory technologieën](sql-database-in-memory.md) , Toep assen om de prestaties van uw analyse-of verwerking van uw NoSQL-gegevens strucutres te verbeteren, gebruik [transactionele replicatie](sql-database-managed-instance-transactional-replication.md) of [Lees bare replica's](sql-database-read-scale-out.md) om een kopie van uw gegevens te maken op de andere locatie en offload enkele analytische workloads van de primaire data base.

## <a name="overview"></a>Overzicht

Azure SQL biedt de volgende functies voor meerdere modellen:
- Met [grafische functies](#graph-features) kunt u uw gegevens weer geven als set van knoop punten en randen en kunt u standaard Transact-SQL-query's gebruiken met de Graph `MATCH`-operator om de grafiek gegevens op te vragen.
- Met de [JSON-functies](#json-features) kunt u JSON-documenten in tabellen plaatsen, relationele gegevens TRANSFORMEREN naar JSON-documenten en vice versa. U kunt de standaard Transact-SQL-taal uitgebreid met JSON-functies voor het parseren van documenten en niet-geclusterde indexen, Column Store-indexen of tabellen die zijn geoptimaliseerd voor geheugen gebruiken om uw query's te optimaliseren.
- Met [ruimtelijke functies](#spatial-features) kunt u geografische en geometrische gegevens opslaan, indexeren met behulp van ruimtelijke indexen en de gegevens ophalen met behulp van ruimtelijke query's.
- Met [XML-functies](#xml-features) kunt u XML-gegevens in uw data base opslaan en indexeren en systeem eigen XQuery/XPath-bewerkingen gebruiken om met XML-gegevens te werken. Azure SQL database heeft een speciaal ingebouwde XML-query-engine waarmee XML-gegevens worden verwerkt.
- [Sleutel-](#key-value-pairs) waardeparen worden niet expliciet ondersteund als speciale functies omdat de sleutel waarde Parijs kan worden gemodelleerd als tabellen met twee kolommen.

  > [!Note]
  > U kunt JSON Path-expressie, XQuery/XPath-expressies, ruimtelijke functies en Graph-query-expressies in dezelfde Transact-SQL-query gebruiken om toegang te krijgen tot gegevens die u in de Data Base hebt opgeslagen. Daarnaast kan elk hulp programma of elke programmeer taal waarmee Transact-SQL-query's kunnen worden uitgevoerd, deze query-interface ook gebruiken om toegang te krijgen tot gegevens van meerdere modellen. Dit is het belangrijkste verschil ten opzichte van de data bases met meerdere modellen, zoals [Azure Cosmos DB](/azure/cosmos-db/) die speciale API bieden voor verschillende gegevens modellen.

In de volgende secties vindt u meer informatie over de belangrijkste mogelijkheden voor meerdere modellen van Azure SQL Database.

## <a name="graph-features"></a>Graph-functies

Azure SQL Database biedt grafische database mogelijkheden om veel-op-veel-relaties in de data base te model leren. Een grafiek is een verzameling knoop punten (of hoek punten) en randen (of relaties). Een knoop punt vertegenwoordigt een entiteit (bijvoorbeeld een persoon of een organisatie) en een rand vertegenwoordigt een relatie tussen de twee knoop punten die er verbinding mee maakt (bijvoorbeeld leuk of vrienden). Hier volgen enkele functies die een grafiek database uniek maken:
- Randen of relaties zijn de eerste klasse-entiteiten in een grafiek database en er kunnen kenmerken of eigenschappen aan zijn gekoppeld.
- Eén rand kan flexibel meerdere knoop punten in een grafiek database verbinden.
- U kunt snel patroon overeenkomsten en navigatie query's met meerdere hops eenvoudig uitdrukken.
- U kunt transitieve sluitingen en polymorphing-query's eenvoudig uitdrukken.

De mogelijkheden van Graph-relaties en Graph-query's zijn geïntegreerd in Transact-SQL en ontvangen de voor delen van het gebruik van SQL Server als basis Database Management System.
Het [verwerken van grafieken](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) is de belangrijkste functie SQL server data base-engine, zodat u meer informatie over de grafiek verwerking kunt vinden.

### <a name="when-to-use-a-graph-capability"></a>Wanneer u een grafiek mogelijkheid wilt gebruiken

Er is niets wat een grafiek database kan bereiken. Dit kan niet worden bereikt met een relationele data base. Met een Graph-Data Base kunt u echter gemakkelijker bepaalde query's uitdrukken. Uw beslissing om een van de andere te kiezen, kan worden gebaseerd op de volgende factoren:

- Modeleer hiërarchische gegevens waarbij één knoop punt meerdere bovenliggende items kan hebben, zodat HierarchyId niet kan worden gebruikt
- Model heeft uw toepassing complexe veel-op-veel-relaties. Naarmate de toepassing wordt ontwikkeld, worden nieuwe relaties toegevoegd.
- U moet onderling verbonden gegevens en relaties analyseren.

## <a name="json-features"></a>JSON-functies

Met Azure SQL Database kunt u gegevens parseren en opvragen die worden weer gegeven in de indeling van JavaScript Object Notation [(JSON)](https://www.json.org/) en uw relationele gegevens exporteren als JSON-tekst.

JSON is een populaire gegevens indeling die wordt gebruikt voor het uitwisselen van gegevens in moderne web-en mobiele toepassingen. JSON wordt ook gebruikt voor het opslaan van semi-gestructureerde gegevens in logboek bestanden of in NoSQL-data bases zoals [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Veel REST-webservices retour neren resultaten die zijn opgemaakt als JSON-tekst of accepteren gegevens die zijn opgemaakt als JSON. De meeste Azure-Services, zoals [Azure Search](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/)en [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) hebben rest-eind punten die JSON retour neren of gebruiken.

Met Azure SQL Database kunt u eenvoudig met JSON-gegevens werken en uw data base integreren met moderne services. Azure SQL Database biedt de volgende functies voor het werken met JSON-gegevens:

![JSON-functies](./media/sql-database-json-features/image_1.png)

Als u JSON-tekst hebt, kunt u gegevens uit JSON extra heren of controleren of de JSON juist is ingedeeld met behulp van de ingebouwde functies [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)en [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). Met de functie [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) kunt u de waarde in JSON-tekst bijwerken. Voor meer geavanceerde query's en analyse kunnen met de functie [openjson](https://msdn.microsoft.com/library/dn921885.aspx) een matrix van JSON-objecten worden omgezet in een set rijen. Een SQL-query kan worden uitgevoerd op de geretourneerde resultatenset. Ten slotte is er een [voor json](https://msdn.microsoft.com/library/dn921882.aspx) -component waarmee u gegevens kunt opmaken die in uw relationele tabellen zijn opgeslagen als JSON-tekst.

Zie [werken met JSON-gegevens in azure SQL database](sql-database-json-features.md)voor meer informatie.
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) is kern SQL server data base-engine, zodat u hier meer informatie over de json-functie kunt vinden.

### <a name="when-to-use-a-json-capability"></a>Wanneer gebruikt u een JSON-mogelijkheid?

Document modellen kunnen worden gebruikt in plaats van de relationele modellen in een aantal specifieke scenario's:
- Hoge normalisatie van het schema brengt geen aanzienlijke voor delen met zich mee, omdat u alle velden van objecten tegelijk opent, of als u de genormaliseerde delen van de objecten nooit bijwerkt. Het genormaliseerde model verhoogt echter de complexiteit van uw query's vanwege het grote aantal tabellen dat u moet koppelen om de gegevens op te halen.
- U werkt met de toepassingen die systeem eigen gebruik maken van JSON-documenten zijn communicatie-of gegevens modellen en u wilt geen extra lagen introduceren waarmee relationele gegevens naar JSON worden getransformeerd en omgekeerd.
- U moet uw gegevens model vereenvoudigen door onderliggende tabellen of entiteit-object-waarde-patronen te ontsleutelen.
- U moet gegevens die zijn opgeslagen in JSON-indeling laden of exporteren zonder een extra hulp programma waarmee de gegevens worden geparseerd.

## <a name="spatial-features"></a>Ruimtelijke functies

Ruimtelijke gegevens vertegenwoordigen informatie over de fysieke locatie en vorm van geometrische objecten. Deze objecten kunnen punt locaties of complexere objecten zijn, zoals landen/regio's, wegen of meren.

Azure SQL Database ondersteunt twee ruimtelijke gegevens typen: het gegevens type geometrie en het gegevens type geografie.
- Het type geometrie vertegenwoordigt gegevens in een Euclidean (plat)-coördinaten systeem.
- Het geografie type staat voor gegevens in een coördinaten systeem met round-Earth.

Er is een aantal ruimtelijke objecten die kunnen worden gebruikt in Azure SQL database zoals [Point](https://docs.microsoft.com/sql/relational-databases/spatial/point), [Lines Tring](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [veelhoek](https://docs.microsoft.com/sql/relational-databases/spatial/polygon), enzovoort.

Azure SQL Database biedt ook gespecialiseerde [ruimtelijke indexen](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) die kunnen worden gebruikt om de prestaties van uw ruimtelijke query's te verbeteren.

[Ruimtelijke ondersteuning](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) is kern SQL Server de functie data base-engine, zodat u hier meer informatie over de ruimtelijke functie kunt vinden.

## <a name="xml-features"></a>XML-functies

SQL Server biedt een krachtig platform voor het ontwikkelen van geavanceerde toepassingen voor semi-gestructureerde gegevens beheer. Ondersteuning voor XML is geïntegreerd in alle onderdelen in SQL Server en bevat het volgende:

- Het XML-gegevens type. XML-waarden kunnen worden opgeslagen in een kolom met XML-gegevens typen die kunnen worden getypt op basis van een verzameling van XML-schema's of niet-getypte links. U kunt de XML-kolom indexeren.
- De mogelijkheid om een XQuery-query op te geven voor XML-gegevens die zijn opgeslagen in kolommen en variabelen van het XML-type. XQuery-functionaliteiten kunnen worden gebruikt in elke Transact-SQL-query die toegang heeft tot elk gegevens model dat u in uw data base gebruikt.
- Alle elementen in XML-documenten automatisch indexeren met de [primaire XML-index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) of de exacte paden opgeven die moeten worden geïndexeerd met de [secundaire XML-index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET waarmee XML-gegevens bulksgewijs kunnen worden geladen.
- Transformeer relationele gegevens in XML-indeling.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) is kern SQL server data base engine-functie, zodat u hier meer informatie over de XML-functie kunt vinden.

### <a name="when-to-use-an-xml-capability"></a>Wanneer gebruikt u een XML-mogelijkheid?

Document modellen kunnen worden gebruikt in plaats van de relationele modellen in een aantal specifieke scenario's:
- Hoge normalisatie van het schema brengt geen aanzienlijke voor delen met zich mee, omdat u alle velden van objecten tegelijk opent, of als u de genormaliseerde delen van de objecten nooit bijwerkt. Het genormaliseerde model verhoogt echter de complexiteit van uw query's vanwege het grote aantal tabellen dat u moet koppelen om de gegevens op te halen.
- U werkt met de toepassingen die systeem eigen XML-documenten gebruiken, zijn communicatie-of gegevens modellen en u wilt geen extra lagen maken waarmee relationele gegevens worden omgezet naar XML en omgekeerd.
- U moet uw gegevens model vereenvoudigen door onderliggende tabellen of entiteit-object-waarde-patronen te ontsleutelen.
- U moet gegevens die zijn opgeslagen in XML-indeling laden of exporteren zonder een extra hulp programma waarmee de gegevens worden geparseerd.

## <a name="key-value-pairs"></a>Sleutel-waardeparen

Azure SQL Database geen gespecialiseerde typen of structuren die sleutel-waardeparen ondersteunen, omdat de sleutel-waardeparen kunnen worden aangeduid als standaard relationele tabellen:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

U kunt deze structuur van de sleutel waarde aanpassen aan uw behoeften zonder beperkingen. Een voor beeld: de waarde kan een XML-document zijn in plaats van `nvarchar(max)` type, als de waarde een JSON-document is, kunt u `CHECK` beperking plaatsen die de geldigheid van JSON-inhoud verifieert. U kunt een wille keurig aantal waarden in verband met één sleutel in de extra kolommen plaatsen, berekende kolommen en indexen toevoegen om gegevens toegang te vereenvoudigen en te optimaliseren, de tabel definiëren als tabel met alleen geheugen/geoptimaliseerd schema voor betere prestaties, enzovoort.

Bekijk [hoe BWin in-Memory OLTP gebruikt om ongekende prestaties te bereiken en te schalen](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) voor de ASP.NET-cache oplossing die 1.200.000 batches per seconden heeft behaald, als voor beeld hoe relationeel model effectief kan worden gebruikt als een oplossing voor sleutel waarde-paar in proef.

## <a name="next-steps"></a>Volgende stappen
Mogelijkheden van meerdere modellen in Azure SQL-data bases zijn ook de kern functies van SQL Server data base-engine die worden gedeeld tussen Azure SQL Database en SQL Server. Ga voor meer informatie over deze functies naar de documentatie pagina's van SQL relationele data base:

* [Grafiek verwerking](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [JSON-gegevens](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Ruimtelijke ondersteuning](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [XML-gegevens](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
