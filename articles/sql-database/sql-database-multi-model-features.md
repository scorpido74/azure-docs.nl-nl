---
title: Mogelijkheden voor meerdere modellen
description: Met Azure SQL Database u met meerdere gegevensmodellen in dezelfde database werken.
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
ms.openlocfilehash: 2e8519fa8d96b7fe016b9da4ba84ce481a57d94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73802817"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Mogelijkheden voor meerdere modellen van Azure SQL Database

Met databases met meerdere modellen u gegevens opslaan en verwerken die worden weergegeven in meerdere gegevensindelingen, zoals relationele gegevens, grafieken, JSON/XML-documenten, sleutelwaardeparen, enz.

## <a name="when-to-use-multi-model-capabilities"></a>Wanneer gebruik je multi-model mogelijkheden

Azure SQL Database is ontworpen om te werken met het relationele model dat de beste prestaties biedt in de meeste gevallen voor een verscheidenheid aan toepassingen voor algemene doeleinden. Azure SQL Database is echter niet beperkt tot alleen relationele gegevens. Azure SQL Database stelt u in staat om verschillende niet-relationele indelingen te gebruiken die nauw zijn geïntegreerd in het relationele model.
U moet overwegen om in de volgende gevallen gebruik te maken van multi-modelmogelijkheden van Azure SQL Database:
- U hebt informatie of structuren die beter passen bij NoSQL-modellen en u wilt geen aparte NoSQL-database gebruiken.
- Een meerderheid van uw gegevens is geschikt voor relationeel model en u moet sommige delen van uw gegevens in NoSQL-stijl modelleren.
- U wilt gebruikmaken van rijke Transact-SQL-taal om zowel relationele als NoSQL-gegevens op te vragen en te analyseren, en deze te integreren met een verscheidenheid aan hulpprogramma's en toepassingen die SQL-taal kunnen gebruiken.
- U wilt databasefuncties zoals [in-memorytechnologieën](sql-database-in-memory.md) toepassen om de prestaties van uw analytische of verwerking van uw NoSQL-gegevensstructuren te verbeteren, [transactionele replicatie](sql-database-managed-instance-transactional-replication.md) of [leesbare replica's](sql-database-read-scale-out.md) te gebruiken om kopieën van uw gegevens op de andere plaats te maken en sommige analytische workloads uit de primaire database te ontladen.

## <a name="overview"></a>Overzicht

Azure SQL biedt de volgende functies met meerdere modellen:
- [Met grafiekfuncties](#graph-features) u uw gegevens weergeven als set knooppunten en randen en `MATCH` standaard Transact-SQL-query's gebruiken die zijn uitgebreid met de graph-operator om de grafiekgegevens op te vragen.
- [Json-functies](#json-features) stellen u in staat om JSON-documenten in tabellen te plaatsen, relationele gegevens om te zetten in JSON-documenten en vice versa. U de standaard Transact-SQL-taal gebruiken die is uitgebreid met JSON-functies voor het ontleden van documenten en niet-geclusterde indexen, kolomarchiefindexen of geheugengeoptimaliseerde tabellen gebruiken om uw query's te optimaliseren.
- [Met ruimtelijke functies](#spatial-features) u geografische en geometrische gegevens opslaan, indexeren met behulp van de ruimtelijke indexen en de gegevens ophalen met behulp van ruimtelijke query's.
- [Met XML-functies](#xml-features) u XML-gegevens opslaan en indexeren in uw database en native XQuery/XPath-bewerkingen gebruiken om met XML-gegevens te werken. Azure SQL-database heeft een gespecialiseerde ingebouwde XML-queryengine die XML-gegevens verwerkt.
- [Sleutelparen](#key-value-pairs) worden niet expliciet ondersteund als speciale functies, omdat sleutelwaardeparen native kunnen worden gemodelleerd als tabellen met twee kolommen.

  > [!Note]
  > U JSON Path-expressies, XQuery/XPath-expressies, ruimtelijke functies en grafiekqueryexpressies in dezelfde Transact-SQL-query gebruiken om toegang te krijgen tot alle gegevens die u in de database hebt opgeslagen. Ook kan elk hulpprogramma of programmeertaal dat Transact-SQL-query's kan uitvoeren, die queryinterface ook gebruiken om toegang te krijgen tot gegevens met meerdere modellen. Dit is het belangrijkste verschil in vergelijking met de multi-model databases zoals [Azure Cosmos DB](/azure/cosmos-db/) die gespecialiseerde API biedt voor verschillende gegevensmodellen.

In de volgende secties u meer te weten komen over de belangrijkste multi-modelmogelijkheden van Azures SQL Database.

## <a name="graph-features"></a>Graph-functies

Azure SQL Database biedt grafische databasemogelijkheden om veel-op-veel relaties in de database te modelleren. Een grafiek is een verzameling knooppunten (of hoekpunten) en randen (of relaties). Een knooppunt vertegenwoordigt een entiteit (bijvoorbeeld een persoon of een organisatie) en een rand vertegenwoordigt een relatie tussen de twee knooppunten die het verbindt (bijvoorbeeld vind-ik-leuks of vrienden). Hier volgen enkele functies die een grafiekdatabase uniek maken:
- Randen of relaties zijn eersteklasentiteiten in een grafiekdatabase en kunnen kenmerken of eigenschappen hebben die eraan zijn gekoppeld.
- Eén rand kan flexibel meerdere knooppunten verbinden in een grafiekdatabase.
- U patroonmatching en multi-hop navigatiequery's eenvoudig uitdrukken.
- U overgangf sluiting en polymorfe query's gemakkelijk uitdrukken.

De grafische relaties en grafische querymogelijkheden zijn geïntegreerd in Transact-SQL en ontvangen de voordelen van het gebruik van SQL Server als het fundamentele databasebeheersysteem.
[Grafiekverwerking](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) is de belangrijkste SQL Server Database Engine-functie, zodat u daar meer informatie vinden over de Graph-verwerking.

### <a name="when-to-use-a-graph-capability"></a>Wanneer een grafiekmogelijkheid gebruiken

Er is niets wat een grafiekdatabase kan bereiken, wat niet kan worden bereikt met behulp van een relationele database. Een grafiekdatabase kan het echter gemakkelijker maken om bepaalde query's uit te drukken. Uw beslissing om de ene boven de andere te kiezen kan gebaseerd zijn op de volgende factoren:

- Hiërarchische gegevens modelleren waarbij één knooppunt meerdere ouders kan hebben, zodat HierarchyId niet kan worden gebruikt
- Model has Uw toepassing heeft complexe veel-op-veel relaties; naarmate de toepassing evolueert, worden nieuwe relaties toegevoegd.
- Je moet onderling verbonden gegevens en relaties analyseren.

## <a name="json-features"></a>JSON-functies

Met Azure SQL Database u gegevens ontleden en query's ontleden die worden weergegeven in de [Json-notatie (JavaScript](https://www.json.org/) Object Notation) en uw relationele gegevens exporteren als JSON-tekst.

JSON is een populair dataformaat dat wordt gebruikt voor het uitwisselen van gegevens in moderne web- en mobiele applicaties. JSON wordt ook gebruikt voor het opslaan van semi-gestructureerde gegevens in logbestanden of in NoSQL-databases zoals [Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/) Veel REST-webservices retourneren resultaten die zijn opgemaakt als JSON-tekst of accepteren gegevens die zijn opgemaakt als JSON. De meeste Azure-services, zoals [Azure Cognitive Search,](https://azure.microsoft.com/services/search/) [Azure Storage](https://azure.microsoft.com/services/storage/)en Azure [Cosmos DB,](https://azure.microsoft.com/services/cosmos-db/) hebben REST-eindpunten die JSON retourneren of verbruiken.

Met Azure SQL Database u eenvoudig met JSON-gegevens werken en uw database integreren met moderne services. Azure SQL Database biedt de volgende functies voor het werken met JSON-gegevens:

![JSON-functies](./media/sql-database-json-features/image_1.png)

Als u JSON-tekst hebt, u gegevens uit JSON extraheren of controleren of JSON correct is opgemaakt met behulp van de ingebouwde functies [JSON_VALUE,](https://msdn.microsoft.com/library/dn921898.aspx) [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)en [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). Met de functie [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) u de waarde in JSON-tekst bijwerken. Voor meer geavanceerde query's en analyses kan de functie [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) een array van JSON-objecten omzetten in een reeks rijen. Elke SQL-query kan worden uitgevoerd op de geretourneerde resultaatset. Ten slotte is er een [JSON-clausule waarmee](https://msdn.microsoft.com/library/dn921882.aspx) u gegevens die in uw relationele tabellen zijn opgeslagen, opmaken als JSON-tekst.

Zie [Werken met JSON-gegevens in azure SQL Database](sql-database-json-features.md)voor meer informatie.
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) is de core SQL Server Database Engine-functie, zodat u daar meer informatie vinden over de JSON-functie.

### <a name="when-to-use-a-json-capability"></a>Wanneer gebruik je een JSON-mogelijkheid?

Documentmodellen kunnen in sommige specifieke scenario's worden gebruikt in plaats van de relationele modellen:
- Een hoge normalisering van het schema brengt geen significante voordelen met zich mee omdat u toegang hebt tot alle velden van objecten tegelijk of dat u nooit genormaliseerde delen van de objecten bijwerkt. Het genormaliseerde model verhoogt echter de complexiteit van uw query's vanwege het grote aantal tabellen waaraan u moet deelnemen om de gegevens te krijgen.
- U werkt met de toepassingen die native gebruik maken van JSON-documenten zijn communicatie- of datamodellen en u wilt geen extra lagen introduceren die relationele gegevens transformeren naar JSON en vice versa.
- U moet uw gegevensmodel vereenvoudigen door onderliggende tabellen of entiteitsobjectwaardepatronen te de-normaliseren.
- U moet gegevens laden of exporteren die zijn opgeslagen in de JSON-indeling zonder een extra tool die de gegevens ontleden.

## <a name="spatial-features"></a>Ruimtelijke kenmerken

Ruimtelijke gegevens vertegenwoordigen informatie over de fysieke locatie en vorm van geometrische objecten. Deze objecten kunnen puntlocaties of complexere objecten zijn, zoals landen/regio's, wegen of meren.

Azure SQL Database ondersteunt twee ruimtelijke gegevenstypen: het gegevenstype geometrie en het gegevenstype geografie.
- Het geometrietype vertegenwoordigt gegevens in een Euclidaans (plat) coördinatensysteem.
- Het aardrijkskundetype vertegenwoordigt gegevens in een round-earth coördinaatsysteem.

Er is een aantal ruimtelijke objecten die kunnen worden gebruikt in Azure SQL-database, zoals [Point](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString,](https://docs.microsoft.com/sql/relational-databases/spatial/linestring) [Polygon,](https://docs.microsoft.com/sql/relational-databases/spatial/polygon)enz.

Azure SQL Database biedt ook gespecialiseerde [ruimtelijke indexen](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) die kunnen worden gebruikt om de prestaties van uw ruimtelijke query's te verbeteren.

[Ruimtelijke ondersteuning](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) is de core SQL Server Database Engine-functie, zodat u daar meer informatie vinden over de ruimtelijke functie.

## <a name="xml-features"></a>XML-functies

SQL Server biedt een krachtig platform voor het ontwikkelen van rijke applicaties voor semi-gestructureerd datamanagement. Ondersteuning voor XML is geïntegreerd in alle componenten in SQL Server en omvat het volgende:

- Het xml-gegevenstype. XML-waarden kunnen native worden opgeslagen in een kolom van xml-gegevenstypen die kunnen worden getypt op basis van een verzameling XML-schema's of die niet kunnen worden getypt. U de XML-kolom indexeren.
- De mogelijkheid om een XQuery-query op te geven aan de hand van XML-gegevens die zijn opgeslagen in kolommen en variabelen van het xml-type. XQuery-functionaliteiten kunnen worden gebruikt in elke Transact-SQL-query die toegang heeft tot elk gegevensmodel dat u in uw database gebruikt.
- Indexeer automatisch alle elementen in XML-documenten met behulp van de [primaire XML-index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) of geef de exacte paden op die moeten worden geïndexeerd met behulp van [de secundaire XML-index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET waarmee bulkladen van XML-gegevens mogelijk is.
- Relationele gegevens omzetten in XML-indeling.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) is de kern functie SQL Server Database Engine, zodat u daar meer informatie vinden over de XML-functie.

### <a name="when-to-use-an-xml-capability"></a>Wanneer een XML-mogelijkheid gebruiken

Documentmodellen kunnen in sommige specifieke scenario's worden gebruikt in plaats van de relationele modellen:
- Een hoge normalisering van het schema brengt geen significante voordelen met zich mee omdat u toegang hebt tot alle velden van objecten tegelijk of dat u nooit genormaliseerde delen van de objecten bijwerkt. Het genormaliseerde model verhoogt echter de complexiteit van uw query's vanwege het grote aantal tabellen waaraan u moet deelnemen om de gegevens te krijgen.
- U werkt met de toepassingen die XML-documenten native gebruiken, zijn communicatie- of gegevensmodellen en u wilt geen extra lagen introduceren die relationele gegevens omzetten in XML en vice versa.
- U moet uw gegevensmodel vereenvoudigen door onderliggende tabellen of entiteitsobjectwaardepatronen te de-normaliseren.
- U moet gegevens laden of exporteren die zijn opgeslagen in XML-indeling zonder een extra hulpmiddel dat de gegevens onteert.

## <a name="key-value-pairs"></a>Sleutel-waardeparen

Azure SQL Database heeft geen gespecialiseerde typen of structuren die sleutelwaardeparen ondersteunen, omdat sleutelwaardestructuren native kunnen worden weergegeven als standaardrelationele tabellen:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

U deze sleutelwaardestructuur zonder beperkingen aanpassen aan uw behoeften. Als voorbeeld kan de waarde XML-document zijn in plaats van `nvarchar(max)` type, `CHECK` als de waarde JSON-document is, u een beperking plaatsen die de geldigheid van JSON-inhoud verifieert. U een willekeurig aantal waarden met betrekking tot één sleutel in de extra kolommen plaatsen, berekende kolommen en indexen toevoegen om gegevenstoegang te vereenvoudigen en te optimaliseren, de tabel definiëren als tabel met geheugen/geoptimaliseerde schemaalleen-tabel om betere prestaties te krijgen, enz.

Ontdek [hoe BWin In-Memory OLTP gebruikt om ongekende prestaties en schaal te bereiken](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) voor hun ASP.NET caching-oplossing die 1.200.000 batches per seconde heeft bereikt, als voorbeeld hoe relationeel model effectief kan worden gebruikt als key-value pair-oplossing in de praktijk.

## <a name="next-steps"></a>Volgende stappen
Multi-modelmogelijkheden in Azure SQL-databases zijn ook de belangrijkste SQL Server Database Engine-functies die worden gedeeld tussen Azure SQL Database en SQL Server. Ga voor meer informatie over deze functies naar de documentatiepagina's van SQL Relational database:

* [Grafiekverwerking](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [JSON-gegevens](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Ruimtelijke ondersteuning](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [XML-gegevens](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
