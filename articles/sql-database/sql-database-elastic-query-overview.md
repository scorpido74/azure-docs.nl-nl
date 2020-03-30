---
title: Overzicht elastische query's
description: Met elastische query u een Transact-SQL-query uitvoeren die meerdere databases omvat.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 12/05/2019
ms.openlocfilehash: 827fab0661a58bfa7d28452960ea6df64d18bf84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873740"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Overzicht van elastische query's in Azure SQL Database (voorbeeld)

Met de elastische queryfunctie (in preview) u een Transact-SQL-query uitvoeren die meerdere databases in Azure SQL Database omvat. Hiermee u cross-database query's uitvoeren om toegang te krijgen tot externe tabellen en microsoft- en hulpprogramma's van derden (Excel, Power BI, Tableau, enz.) te verbinden om gegevenslagen met meerdere databases te bevragen. Met deze functie u query's uitschalen naar grote gegevenslagen in SQL Database en de resultaten in bi-rapporten (business intelligence) visualiseren.

## <a name="why-use-elastic-queries"></a>Waarom elastische query's gebruiken

### <a name="azure-sql-database"></a>Azure SQL Database

Query's in Azure SQL-databases volledig in T-SQL. Dit maakt alleen-lezen query's van externe databases mogelijk en biedt een optie voor huidige on-premises SQL Server-klanten om toepassingen te migreren met behulp van drie- en vierdelige namen of gekoppelde server naar SQL DB.

### <a name="available-on-standard-tier"></a>Beschikbaar op standaardniveau

Elastische query wordt ondersteund op zowel de standaard- als de Premium-servicelagen. Zie de sectie over Voorbeeldbeperkingen hieronder over prestatiebeperkingen voor lagere servicelagen.

### <a name="push-parameters-to-remote-databases"></a>Parameters naar externe databases pushen

Elastische query's kunnen nu SQL-parameters naar de externe databases pushen voor uitvoering.

### <a name="stored-procedure-execution"></a>Uitvoering van de opgeslagen procedure

Uitvoeren van externe opgeslagen procedure oproepen of externe functies met behulp van [sp\_uit te \_voeren op afstand](https://msdn.microsoft.com/library/mt703714).

### <a name="flexibility"></a>Flexibiliteit

Externe tabellen met elastische query kunnen verwijzen naar externe tabellen met een ander schema of tabelnaam.

## <a name="elastic-query-scenarios"></a>Elastische queryscenario's

Het doel is om queryscenario's te vergemakkelijken waarbij meerdere databases rijen bijdragen aan één algemeen resultaat. De query kan worden samengesteld door de gebruiker of toepassing direct, of indirect via tools die zijn aangesloten op de database. Dit is vooral handig bij het maken van rapporten, het gebruik van commerciële BI- of gegevensintegratietools of een toepassing die niet kan worden gewijzigd. Met een elastische query u in verschillende databases een query uitvoeren met behulp van de vertrouwde SQL Server-connectiviteitservaring in hulpprogramma's zoals Excel, Power BI, Tableau of Cognos.
Een elastische query biedt eenvoudige toegang tot een hele verzameling databases via query's die zijn uitgegeven door SQL Server Management Studio of Visual Studio, en vergemakkelijkt cross-database query's vanuit Entity Framework of andere ORM-omgevingen. Figuur 1 toont een scenario waarin een bestaande cloudtoepassing (die gebruikmaakt van de [elastische databaseclientbibliotheek)](sql-database-elastic-database-client-library.md)voortbouwt op een geschaalde gegevenslaag en een elastische query wordt gebruikt voor cross-databaserapportage.

**Figuur 1** Elastische query die wordt gebruikt op de categorie geschaalde gegevens

![Elastische query die wordt gebruikt op de categorie geschaalde gegevens][1]

Klantscenario's voor elastische query's worden gekenmerkt door de volgende topologieën:

* **Verticale partitionering - Cross-database query's** (Topologie 1): De gegevens worden verticaal verdeeld tussen een aantal databases in een gegevenslaag. Normaal gesproken bevinden verschillende sets tabellen zich in verschillende databases. Dat betekent dat het schema anders is in verschillende databases. Alle tabellen voor voorraad bevinden zich bijvoorbeeld in één database, terwijl alle boekhoudgerelateerde tabellen zich in een tweede database bevinden. Voor veelvoorkomende use cases met deze topologie moet men rapporten in verschillende databases doorzoeken of rapporten compileren.
* **Horizontale partitionering - Sharding** (Topologie 2): Gegevens worden horizontaal verdeeld om rijen te verdelen over een geschaalde gegevenslaag. Met deze aanpak is het schema identiek op alle deelnemende databases. Deze aanpak wordt ook wel "sharding" genoemd. Sharding kan worden uitgevoerd en beheerd met behulp van (1) de elastische database tools bibliotheken of (2) zelf-sharding. Een elastische query wordt gebruikt om rapporten op te vragen of samen te stellen over veel shards. Shards zijn meestal databases binnen een elastische pool. U elastische query's zien als een efficiënte manier voor het tegelijk opvragen van alle databases met elastische groep, zolang databases het algemene schema delen.

> [!NOTE]
> Elastische query werkt het beste voor het rapporteren van scenario's waarbij het grootste deel van de verwerking (filteren, aggregatie) kan worden uitgevoerd aan de externe bronzijde. Het is niet geschikt voor ETL-bewerkingen waarbij een grote hoeveelheid gegevens wordt overgedragen vanuit externe database(s). Voor zware rapportageworkloads of scenario's voor gegevensopslag met complexere query's u ook Azure [Synapse Analytics gebruiken.](https://azure.microsoft.com/services/synapse-analytics)
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Verticale partitionering - cross-database query's

Zie [Aan de slag met cross-database query (verticale partitionering)](sql-database-elastic-query-getting-started-vertical.md)om te beginnen met coderen.

Een elastische query kan worden gebruikt om gegevens in een SQL-database beschikbaar te maken voor andere SQL-databases. Hierdoor kunnen query's uit de ene database verwijzen naar tabellen in een andere externe SQL-database. De eerste stap is het definiëren van een externe gegevensbron voor elke externe database. De externe gegevensbron wordt gedefinieerd in de lokale database van waaruit u toegang wilt krijgen tot tabellen in de externe database. Er zijn geen wijzigingen nodig in de externe database. Voor typische verticale partitioneringsscenario's waarbij verschillende databases verschillende schema's hebben, kunnen elastische query's worden gebruikt om veelgebruikte use cases te implementeren, zoals toegang tot referentiegegevens en cross-database query's.

> [!IMPORTANT]
> U moet beschikken over ALTER ELKE EXTERNE GEGEVENS BRON toestemming. Deze toestemming is opgenomen in de toestemming van ALTER DATABASE. WIJZIG ALLE externe gegevensbronmachtigingen zijn nodig om naar de onderliggende gegevensbron te verwijzen.
>

**Referentiegegevens**: De topologie wordt gebruikt voor referentiegegevensbeheer. In de onderstaande afbeelding worden twee tabellen (T1 en T2) met referentiegegevens bewaard in een speciale database. Met behulp van een elastische query u nu tabellen T1 en T2 op afstand openen vanuit andere databases, zoals in de figuur wordt weergegeven. Gebruik topologie 1 als referentietabellen kleine of externe query's zijn in referentietabel met selectieve predicaten.

**Figuur 2** Verticale partitionering - Elastische query gebruiken om referentiegegevens op te vragen

![Verticale partitionering - Elastische query gebruiken om referentiegegevens op te vragen][3]

**Query's met meerdere databases:** elastische query's maken gebruiksaanvragen mogelijk waarvoor query's in verschillende SQL-databases moeten worden opgevraagd. Figuur 3 toont vier verschillende databases: CRM, Inventory, HR en Products. Query's die in een van de databases worden uitgevoerd, hebben ook toegang nodig tot een of alle andere databases. Met behulp van een elastische query u uw database voor deze aanvraag configureren door een paar eenvoudige DDL-instructies uit te voeren op elk van de vier databases. Na deze eenmalige configuratie is de toegang tot een externe tabel net zo eenvoudig als verwijzen naar een lokale tabel vanuit uw T-SQL-query's of vanuit uw BI-hulpprogramma's. Deze benadering wordt aanbevolen als de externe query's geen grote resultaten opleveren.

**Figuur 3** Verticale partitionering - Elastische query gebruiken om in verschillende databases te worden opgevraagd

![Verticale partitionering - Elastische query gebruiken om in verschillende databases te worden opgevraagd][4]

In de volgende stappen worden elastische databasequery's geconfigureerd voor verticale partitiescenario's waarvoor toegang vereist is tot een tabel in externe SQL-databases met hetzelfde schema:

* [MYMASTER-mymastersleutel MAKEN MASTER-sleutel](https://msdn.microsoft.com/library/ms174382.aspx)
* [MyCredential van DATABASESCOPED-referenties](https://msdn.microsoft.com/library/mt270260.aspx) maken
* [MAKEN/DROP EXTERNE DATABRON](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource van type **RDBMS**
* Externe tabeltabel tabeltabel [MAKEN/NEERZETTEN](https://msdn.microsoft.com/library/dn935021.aspx)

Na het uitvoeren van de DDL-instructies, u toegang krijgen tot de externe tabel mytable alsof het een lokale tabel. Azure SQL Database opent automatisch een verbinding met de externe database, verwerkt uw aanvraag op de externe database en retourneert de resultaten.

## <a name="horizontal-partitioning---sharding"></a>Horizontale partitionering - sharding

Met behulp van elastische query om rapportagetaken uit te voeren over een geshard, dat wil zeggen horizontaal verdeeld, gegevenslaag vereist een [elastische database shard kaart](sql-database-elastic-scale-shard-map-management.md) om de databases van de gegevenslaag te vertegenwoordigen. In dit scenario wordt doorgaans slechts één shardkaart gebruikt en dient een speciale database met elastische querymogelijkheden (hoofdknooppunt) als toegangspunt voor het rapporteren van query's. Alleen deze speciale database heeft toegang nodig tot de shardkaart. Figuur 4 illustreert deze topologie en de configuratie ervan met de elastische querydatabase en shardkaart. De databases in de gegevenslaag kunnen van elke Azure SQL Database-versie of -editie zijn. Zie [Shard mapmanagement](sql-database-elastic-scale-shard-map-management.md)voor meer informatie over de clientbibliotheek van de elastische database en het maken van shardkaarten.

**Figuur 4** Horizontale partitionering - Elastische query gebruiken voor rapportage over verharde gegevenslagen

![Horizontale partitionering - Elastische query gebruiken voor rapportage over verharde gegevenslagen][5]

> [!NOTE]
> Elastic Query Database (hoofdknooppunt) kan een aparte database zijn, of het kan dezelfde database zijn die de shardmap host.
> Welke configuratie u ook kiest, zorg ervoor dat de servicelaag en de rekengrootte van die database hoog genoeg zijn om de verwachte hoeveelheid aanmeldings-/queryaanvragen te verwerken.

In de volgende stappen worden elastische databasequery's geconfigureerd voor horizontale partitiescenario's waarvoor toegang moet worden nodig tot een reeks tabellen die zich bevinden in (meestal) verschillende externe SQL-databases:

* [MYMASTER-mymastersleutel MAKEN MASTER-sleutel](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)
* [MyCredential van DATABASESCOPED-referenties](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) maken
* Maak een [shardkaart](sql-database-elastic-scale-shard-map-management.md) die uw gegevenslaag weergeeft met behulp van de elastische databaseclientbibliotheek.
* [MAKEN/DROP EXTERNE GEGEVENSBRON](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource van type **SHARD_MAP_MANAGER**
* Externe tabeltabel tabeltabel [MAKEN/NEERZETTEN](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)

Zodra u deze stappen hebt uitgevoerd, u toegang krijgen tot de horizontaal verdeelde tabel mytable alsof het een lokale tabel is. Azure SQL Database opent automatisch meerdere parallelle verbindingen met de externe databases waar de tabellen fysiek zijn opgeslagen, verwerkt de aanvragen op de externe databases en retourneert de resultaten.
Meer informatie over de stappen die nodig zijn voor het horizontale partitioneringscenario is te vinden in [de elastische query voor horizontale partitionering.](sql-database-elastic-query-horizontal-partitioning.md)

Zie [Aan de slag met elastische query voor horizontale partitionering (sharding)](sql-database-elastic-query-getting-started.md)om te beginnen met coderen.

> [!IMPORTANT]
> Succesvolle uitvoering van elastische query's over een grote set databases is sterk afhankelijk van de beschikbaarheid van elk van de databases tijdens de queryuitvoering. Als een van de databases niet beschikbaar is, mislukt de volledige query. Als u van plan bent honderden of duizenden databases tegelijk op te vragen, controleert u of uw clienttoepassing de logica opnieuw heeft geprobeerd of overweegt u gebruik te maken van [Elastic Database Jobs](https://docs.microsoft.com/azure/sql-database/sql-database-job-automation-overview#elastic-database-jobs-preview) (preview) en kleinere subsets van databases op te vragen, waarbij de resultaten van elke query worden geconsolideerd tot één bestemming.

## <a name="t-sql-querying"></a>T-SQL-query's

Zodra u uw externe gegevensbronnen en uw externe tabellen hebt gedefinieerd, u normale SQL Server-verbindingstekenreeksen gebruiken om verbinding te maken met de databases waarin u uw externe tabellen hebt gedefinieerd. U vervolgens T-SQL-instructies uitvoeren over uw externe tabellen op die verbinding met de onderstaande beperkingen. U vindt meer informatie en voorbeelden van T-SQL-query's in de documentatieonderwerpen voor [horizontale partitionering](sql-database-elastic-query-horizontal-partitioning.md) en [verticale partitionering.](sql-database-elastic-query-vertical-partitioning.md)

## <a name="connectivity-for-tools"></a>Connectiviteit voor gereedschappen

U reguliere SQL Server-verbindingstekenreeksen gebruiken om uw toepassingen en BI- of gegevensintegratietools te verbinden met databases met externe tabellen. Zorg ervoor dat SQL Server wordt ondersteund als gegevensbron voor uw hulpprogramma. Raadpleeg, eenmaal verbonden, naar de elastische querydatabase en de externe tabellen in die database, net zoals u zou doen met elke andere SQL Server-database waarmee u verbinding maakt met uw tool.

> [!IMPORTANT]
> Verificatie met Azure Active Directory met elastische query's wordt momenteel niet ondersteund.

## <a name="cost"></a>Kosten

Elastische query is opgenomen in de kosten van Azure SQL Database-databases. Houd er rekening mee dat topologieën waarin uw externe databases zich in een ander datacenter bevinden dan het elastische queryeindpunt worden ondersteund, maar dat gegevensuitgangen uit externe databases regelmatig [Azure-tarieven in](https://azure.microsoft.com/pricing/details/data-transfers/)rekening worden gebracht.

## <a name="preview-limitations"></a>Preview-beperkingen

* Het uitvoeren van uw eerste elastische query kan enkele minuten duren op de standaardservicelaag. Deze tijd is nodig om de elastische queryfunctionaliteit te laden; laadprestaties verbeteren met hogere servicelagen en rekengroottes.
* Scripting van externe gegevensbronnen of externe tabellen van SSMS of SSDT wordt nog niet ondersteund.
* Import/export voor SQL DB biedt nog geen ondersteuning voor externe gegevensbronnen en externe tabellen. Als u Importeren/exporteren wilt gebruiken, geeft u deze objecten neer voordat u ze exporteert en maakt u ze opnieuw na het importeren.
* Elastische query ondersteunt momenteel alleen-lezen toegang tot externe tabellen. U echter wel volledige T-SQL-functionaliteit gebruiken in de database waarin de externe tabel is gedefinieerd. Dit kan handig zijn om bijvoorbeeld tijdelijke resultaten te blijven gebruiken, bijvoorbeeld SELECT <column_list>> INTO <local_table>, of om opgeslagen procedures te definiëren in de elastische querydatabase die verwijzen naar externe tabellen.
* Met uitzondering van nvarchar(max) worden LOB-typen (inclusief ruimtelijke typen) niet ondersteund in externe tabeldefinities. Als tijdelijke oplossing u een weergave maken op de externe database die het LOB-type in nvarchar(max) werpt, uw externe tabel over de weergave definiëren in plaats van de basistabel en deze vervolgens terugwerpen naar het oorspronkelijke LOB-type in uw query's.
* Kolommen met nvarchar(max)gegevenstype in resultaatset uitschakelen geavanceerde batching technieken die worden gebruikt in Elastic Query implementatie en kan invloed hebben op de uitvoering van query's voor een orde van grootte, of zelfs twee ordes van grootte in niet-canonieke use cases waar grote hoeveelheid van de query niet-geaggregeerde gegevens worden overgedragen als gevolg van query.
* Kolomstatistieken over externe tabellen worden momenteel niet ondersteund. Tabelstatistieken worden ondersteund, maar moeten handmatig worden gemaakt.
* Elastische query werkt alleen met Azure SQL Database. U het niet gebruiken voor het opvragen van on-premises SQL Server of SQL Server in een VM.

## <a name="feedback"></a>Feedback

Deel feedback over uw ervaring met elastische query's met ons hieronder, op de MSDN forums, of op Stack Overflow. We zijn geïnteresseerd in allerlei feedback over de service (defecten, ruwe randen, functie hiaten).

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag [met cross-databasequery (verticale partitionering)](sql-database-elastic-query-getting-started-vertical.md)voor een verticale zelfstudie voor het partitioneren.
* Zie [Verticaal verdeelde gegevens opvragen voor](sql-database-elastic-query-vertical-partitioning.md) syntaxis- en voorbeeldquery's voor verticaal verdeelde gegevens)
* Zie [Aan de slag met elastische query voor horizontale partitionering (sharding) voor](sql-database-elastic-query-getting-started.md)een horizontale zelfstudie voor het partitioneren (sharden).
* Zie [Horizontaal gepartitioneerde gegevens opvragen voor](sql-database-elastic-query-horizontal-partitioning.md) syntaxis- en voorbeeldquery's voor horizontaal verdeelde gegevens)
* Zie [\_sp \_remote uitvoeren](https://msdn.microsoft.com/library/mt703714) voor een opgeslagen procedure die een Transact-SQL-instructie uitvoert op één externe Azure SQL-database of set databases die als shards dienen in een horizontaal partitioneringsschema.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
