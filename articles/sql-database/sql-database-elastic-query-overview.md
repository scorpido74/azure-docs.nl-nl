---
title: Overzicht elastische query's
description: Met elastische query's kunt u een Transact-SQL-query uitvoeren die meerdere data bases omspant.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74873740"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Overzicht van elastische query's Azure SQL Database (preview-versie)

Met de functie elastische query's (in Preview) kunt u een Transact-SQL-query uitvoeren die meerdere data bases in Azure SQL Database omspant. Hiermee kunt u query's uitvoeren voor meerdere data bases voor toegang tot externe tabellen en kunt u micro soft en hulpprogram ma's van derden (Excel, Power BI, tableau, enzovoort) verbinden om een query uit te voeren voor gegevens lagen met meerdere data bases. Met deze functie kunt u query's uitschalen naar grote gegevens lagen in SQL Database en de resultaten visualiseren in business intelligence (BI)-rapporten.

## <a name="why-use-elastic-queries"></a>Waarom elastische query's gebruiken?

### <a name="azure-sql-database"></a>Azure SQL Database

Een query uitvoeren in Azure SQL data bases volledig in T-SQL. Hiermee kunnen alleen-lezen query's worden uitgevoerd voor externe data bases en wordt een optie geboden voor huidige on-premises SQL Server klanten om toepassingen te migreren met behulp van drie-en vier deel namen of een gekoppelde server naar SQL DB.

### <a name="available-on-standard-tier"></a>Beschikbaar in de laag standaard

Elastische query's worden ondersteund in de service lagen Standard en Premium. Zie de sectie over de beperkingen van de voor beelden over prestatie beperkingen voor lagere service lagen.

### <a name="push-parameters-to-remote-databases"></a>Push para meters naar externe data bases

Elastische query's kunnen nu SQL-para meters naar de externe data base pushen om uit te voeren.

### <a name="stored-procedure-execution"></a>Uitvoering van opgeslagen procedure

Voer externe opgeslagen procedure aanroepen of externe functies uit met behulp van [\_extern uitvoeren \_op afstand](https://msdn.microsoft.com/library/mt703714).

### <a name="flexibility"></a>Flexibiliteit

Externe tabellen met elastische query's kunnen verwijzen naar externe tabellen met een andere schema-of tabel naam.

## <a name="elastic-query-scenarios"></a>Elastische query scenario's

Het doel is het vergemakkelijken van het uitvoeren van query's waarbij meerdere data bases rijen in een enkel geheel resultaat bijdragen. De query kan rechtstreeks worden samengesteld door de gebruiker of toepassing, of indirect via hulpprogram ma's die zijn verbonden met de data base. Dit is vooral handig bij het maken van rapporten, het gebruik van hulpprogram ma's voor commerciële BI of gegevens integratie, of een toepassing die niet kan worden gewijzigd. Met een elastische query kunt u een query uitvoeren op verschillende data bases met behulp van de vertrouwde SQL Server connectiviteits ervaring in hulpprogram ma's zoals Excel, Power BI, tableau of Cognos.
Een elastische query biedt eenvoudige toegang tot een volledige verzameling data bases via query's die zijn uitgegeven door SQL Server Management Studio of Visual Studio, en vereenvoudigt query's voor meerdere data bases vanuit Entity Framework of andere ORM-omgevingen. In afbeelding 1 ziet u een scenario waarin een bestaande Cloud toepassing (die gebruikmaakt van de [client bibliotheek voor Elastic data base](sql-database-elastic-database-client-library.md)), is gebaseerd op een uitgeschaalde gegevenslaag en een elastische query wordt gebruikt voor rapportage over meerdere data bases.

**Afbeelding 1** Elastische query gebruikt op uitgeschaalde gegevenslaag

![Elastische query gebruikt op uitgeschaalde gegevenslaag][1]

Klant scenario's voor elastische query's worden gekenmerkt door de volgende topologieën:

* **Verticaal partitioneren-query's voor meerdere data bases** (topologie 1): de gegevens worden verticaal gepartitioneerd tussen een aantal data bases in een gegevenslaag. Doorgaans bevinden verschillende sets tabellen zich op verschillende data bases. Dit betekent dat het schema afwijkt van verschillende data bases. Zo bevinden alle tabellen voor de inventarisatie zich op één data base, terwijl alle aan de administratie gerelateerde tabellen zich in een tweede data base bevinden. Voor veelvoorkomende use-cases met deze topologie moet er een worden doorzocht op of voor het compileren van rapporten tussen tabellen in verschillende data bases.
* **Horizontale partitionering-sharding** (topologie 2): gegevens worden horizon taal gepartitioneerd om rijen te verdelen over een uitgeschaalde gegevenslaag. Met deze methode is het schema identiek voor alle deelnemende data bases. Deze methode wordt ook wel ' sharding ' genoemd. Sharding kan worden uitgevoerd en beheerd met behulp van (1) de Elastic data base tools libraries of (2) Self-sharding. Een elastische query wordt gebruikt voor het opvragen of compileren van rapporten in veel Shards. Shards zijn doorgaans data bases binnen een elastische pool. U kunt een elastische query beschouwen als een efficiënte manier voor het opvragen van alle data bases van elastische pool tegelijk, zolang data bases het gemeen schappelijke schema delen.

> [!NOTE]
> Elastische query's werken het beste voor rapportage scenario's waarbij de meeste verwerking (filteren, aggregatie) op de externe bron zijde kan worden uitgevoerd. Het is niet geschikt voor ETL-bewerkingen waarbij een grote hoeveelheid gegevens worden overgebracht van externe data base (s). Overweeg het gebruik van [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics)voor zware rapporten van werk belastingen of scenario's voor gegevens opslag met complexere query's.
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Verticale partitionering-query's voor meerdere data bases

Zie aan de slag [met query's tussen data bases (verticaal partitioneren)](sql-database-elastic-query-getting-started-vertical.md)om te beginnen met de code ring.

Een elastische query kan worden gebruikt om gegevens te maken die zich bevinden in een SQL database die beschikbaar zijn voor andere SQL-data bases. Hierdoor kunnen query's van de ene data base verwijzen naar tabellen in andere externe SQL database. De eerste stap is het definiëren van een externe gegevens bron voor elke externe data base. De externe gegevens bron wordt gedefinieerd in de lokale data base van waaruit u toegang wilt krijgen tot de tabellen die zich op de externe data base bevinden. Er zijn geen wijzigingen vereist voor de externe data base. Voor typische scenario's voor verticale partitionering waarbij verschillende data bases verschillende schema's hebben, kunnen elastische query's worden gebruikt voor het implementeren van algemene gebruiks voorbeelden, zoals toegang tot referentie gegevens en query's tussen data bases.

> [!IMPORTANT]
> U moet een machtiging hebben om een externe gegevens bron te wijzigen. Deze machtiging is opgenomen in de machtiging ALTER data base. Machtigingen voor externe gegevens bronnen wijzigen is nodig om te verwijzen naar de onderliggende gegevens bron.
>

**Referentie gegevens**: de topologie wordt gebruikt voor het beheer van referentie gegevens. In de onderstaande afbeelding worden twee tabellen (T1 en T2) met referentie gegevens bewaard op een speciale data base. Met behulp van een elastische query hebt u nu toegang tot de tabellen T1 en T2 op afstand van andere data bases, zoals wordt weer gegeven in de afbeelding. Topologie 1 gebruiken als verwijzings tabellen kleine of externe query's in verwijzings tabel zijn, zijn selectieve predikaten.

**Afbeelding 2** Verticaal partitioneren-een elastische query gebruiken om referentie gegevens op te vragen

![Verticaal partitioneren-een elastische query gebruiken om referentie gegevens op te vragen][3]

**Query's voor meerdere data bases**: elastische query's maken gebruik van aanvragen waarvoor query's moeten worden uitgevoerd in meerdere SQL-data bases. In afbeelding 3 ziet u vier verschillende data bases: CRM, inventaris, HR en Products. Query's die in een van de data bases worden uitgevoerd, hebben ook toegang nodig tot een of alle andere data bases. Met behulp van een elastische query kunt u uw Data Base voor deze case configureren door een paar eenvoudige DDL-instructies uit te voeren op elk van de vier data bases. Na deze eenmalige configuratie is toegang tot een externe tabel net zo eenvoudig als verwijzingen naar een lokale tabel van uw T-SQL-query's of vanuit uw BI-hulpprogram ma's. Deze methode wordt aanbevolen als de externe query's geen grote resultaten retour neren.

**Afbeelding 3** Verticaal partitioneren-elastische query's gebruiken om query's uit te zoeken in verschillende data bases

![Verticaal partitioneren-elastische query's gebruiken om query's uit te zoeken in verschillende data bases][4]

Met de volgende stappen worden Elastic data base-query's voor verticale partitionatie scenario's geconfigureerd die toegang nodig hebben tot een tabel op externe SQL-data bases met hetzelfde schema:

* [Hoofd sleutel Mymasterkey maken](https://msdn.microsoft.com/library/ms174382.aspx)
* [Data Base scoped CREDENTIAL Mycredential maken](https://msdn.microsoft.com/library/mt270260.aspx)
* [Externe gegevens bron](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource van het type **RDBMS** maken/verwijderen
* [Externe tabel mytable maken/verwijderen](https://msdn.microsoft.com/library/dn935021.aspx)

Na het uitvoeren van de DDL-instructies, hebt u toegang tot de externe tabel ' mytable ', alsof het een lokale tabel is. Azure SQL Database opent automatisch een verbinding met de externe data base, verwerkt uw aanvraag op de externe data base en retourneert de resultaten.

## <a name="horizontal-partitioning---sharding"></a>Horizontale partitionering-sharding

Door gebruik te maken van elastische query's voor het uitvoeren van rapportage taken via een Shard, dat wil zeggen horizon taal gepartitioneerd, is voor een gegevenslaag een [elastische-database Shard-toewijzing](sql-database-elastic-scale-shard-map-management.md) vereist voor de data bases van de gegevenslaag. Normaal gesp roken wordt slechts één Shard-kaart gebruikt in dit scenario en een speciale data base met elastische query mogelijkheden (hoofd knooppunt) fungeert als het toegangs punt voor rapportage query's. Alleen deze speciale data base moet toegang hebben tot de Shard-kaart. Afbeelding 4 illustreert deze topologie en de configuratie ervan met de elastische query database en de Shard-kaart. De data bases in de gegevenslaag kunnen van een wille keurige Azure SQL Database versie of editie zijn. Zie [Shard map Management](sql-database-elastic-scale-shard-map-management.md)(Engelstalig) voor meer informatie over de client bibliotheek voor Elastic data base en het maken van Shard Maps.

**Afbeelding 4** Horizon taal partitioneren-een elastische query gebruiken voor rapportage over Shard-gegevens lagen

![Horizon taal partitioneren-een elastische query gebruiken voor rapportage over Shard-gegevens lagen][5]

> [!NOTE]
> Elastic query-data base (hoofd knooppunt) kan afzonderlijke data bases zijn, of kan dezelfde data base zijn die de Shard-toewijzing host.
> Welke configuratie u kiest, zorg ervoor dat de servicelaag en de reken grootte van die data base hoog genoeg zijn om de verwachte hoeveelheid aanmeldings-en query aanvragen af te handelen.

Met de volgende stappen worden Elastic data base-query's geconfigureerd voor horizontale partitionatie scenario's die toegang nodig hebben tot een set tabellen op (meestal) verschillende externe SQL-data bases:

* [Hoofd sleutel Mymasterkey maken](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)
* [Data Base scoped CREDENTIAL Mycredential maken](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
* Maak een [Shard-toewijzing](sql-database-elastic-scale-shard-map-management.md) die uw gegevenslaag weergeeft met behulp van de client bibliotheek voor Elastic data base.
* [Externe gegevens bron](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource van het type **SHARD_MAP_MANAGER** maken/verwijderen
* [Externe tabel mytable maken/verwijderen](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)

Wanneer u deze stappen hebt uitgevoerd, hebt u toegang tot de horizon taal gepartitioneerde tabel ' mytable ', alsof het een lokale tabel is. Azure SQL Database opent automatisch meerdere parallelle verbindingen met de externe data bases waarin de tabellen fysiek zijn opgeslagen, verwerkt de aanvragen op de externe data bases en retourneert de resultaten.
Meer informatie over de stappen die vereist zijn voor het horizontale partitie scenario kunt u vinden in [elastische query's voor horizontale partitionering](sql-database-elastic-query-horizontal-partitioning.md).

Zie aan de slag [met elastische query's voor horizontale partitionering (sharding)](sql-database-elastic-query-getting-started.md)om te beginnen met de code ring.

> [!IMPORTANT]
> Geslaagde uitvoering van elastische query's over een grote set data bases is sterk afhankelijk van de beschik baarheid van elk van de data bases tijdens de uitvoering van de query. Als een van de data bases niet beschikbaar is, mislukt de volledige query. Als u van plan bent om op honderden of duizenden data bases tegelijk een query uit te voeren, moet u ervoor zorgen dat uw client toepassing de logica voor opnieuw proberen insluit of u kunt [Elastic database taken](https://docs.microsoft.com/azure/sql-database/sql-database-job-automation-overview#elastic-database-jobs-preview) (preview) gebruiken en een query uitvoeren op kleinere subsets van data bases, waarbij u de resultaten van elke query samenvoegt in één bestemming.

## <a name="t-sql-querying"></a>T-SQL-query's uitvoeren

Wanneer u uw externe gegevens bronnen en uw externe tabellen hebt gedefinieerd, kunt u reguliere SQL Server verbindings reeksen gebruiken om verbinding te maken met de data bases waarin u de externe tabellen hebt gedefinieerd. U kunt vervolgens T-SQL-instructies uitvoeren voor uw externe tabellen op die verbinding met de beperkingen die hieronder worden beschreven. Meer informatie en voor beelden van T-SQL-query's vindt u in de documentatie onderwerpen voor [horizontale partitionering](sql-database-elastic-query-horizontal-partitioning.md) en [verticale partitionering](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Connectiviteit voor hulpprogram ma's

U kunt gewone SQL Server verbindings reeksen gebruiken om uw toepassingen en BI-of gegevens integratie hulpprogramma's te koppelen aan data bases die externe tabellen hebben. Zorg ervoor dat SQL Server wordt ondersteund als gegevens bron voor uw hulp programma. Als de verbinding tot stand is gebracht, raadpleegt u de elastische query database en de externe tabellen in die data base net zoals u zou doen met andere SQL Server Data Base waarmee u verbinding maakt met uw hulp programma.

> [!IMPORTANT]
> Verificatie met behulp van Azure Active Directory met elastische query's wordt momenteel niet ondersteund.

## <a name="cost"></a>Kosten

Elastische query's zijn opgenomen in de kosten van Azure SQL Database-data bases. Houd er rekening mee dat topologieën waarbij uw externe data bases zich in een ander Data Center bevinden dan het elastische query-eind punt worden ondersteund, maar dat de gegevens die worden uitgeschreven door externe data bases [regel matig](https://azure.microsoft.com/pricing/details/data-transfers/)worden berekend

## <a name="preview-limitations"></a>Preview-beperkingen

* Het uitvoeren van uw eerste elastische query kan tot een paar minuten duren op de Standard-servicelaag. Deze tijd is nodig om de functionaliteit voor elastische query's te laden. het laden van prestaties verbetert met hogere service lagen en reken grootten.
* Het uitvoeren van scripts voor externe gegevens bronnen of externe tabellen vanuit SSMS of SSDT wordt nog niet ondersteund.
* Import/export voor SQL DB biedt nog geen ondersteuning voor externe gegevens bronnen en externe tabellen. Als u importeren/exporteren moet gebruiken, verwijdert u deze objecten voordat u deze exporteert en maakt u ze na het importeren opnieuw.
* Elastische query's bieden momenteel alleen ondersteuning voor alleen-lezen toegang tot externe tabellen. U kunt echter volledige T-SQL-functionaliteit gebruiken voor de Data Base waarin de externe tabel is gedefinieerd. Dit kan handig zijn om bijvoorbeeld tijdelijke resultaten te blijven gebruiken, bijvoorbeeld <column_list> te selecteren in <local_table>, of om opgeslagen procedures te definiëren voor de elastische query database die naar externe tabellen verwijzen.
* Met uitzonde ring van nvarchar (max) worden LOB-typen (met inbegrip van ruimtelijke typen) niet ondersteund in externe tabel definities. Als tijdelijke oplossing kunt u een weer gave maken voor de externe data base waarmee het LOB-type wordt omgezet in nvarchar (max), uw externe tabel definiëren via de weer gave in plaats van de basis tabel en deze vervolgens weer omzetten in het oorspronkelijke LOB-type in uw query's.
* Kolommen van het gegevens type nvarchar (max) in de resultatenset geavanceerde batch verwerking uitschakelen die wordt gebruikt in de implementatie van elastische Query's en kan invloed hebben op de prestaties van de query voor een orde van omvang, of zelfs twee bestellingen van grootte in niet-canonieke situaties waarbij een grote hoeveelheid niet-geaggregeerde gegevens worden overgedragen als resultaat van de query.
* Kolom statistieken over externe tabellen worden momenteel niet ondersteund. Tabel statistieken worden ondersteund, maar moeten hand matig worden gemaakt.
* Elastische query's werken alleen met Azure SQL Database. U kunt deze niet gebruiken voor het opvragen van on-premises SQL Server, of SQL Server in een VM.

## <a name="feedback"></a>Feedback

Deel uw feedback over uw ervaring met elastische query's met ons hieronder, op de MSDN-Forums of op Stack Overflow. We zijn geïnteresseerd in alle soorten feedback over de service (defecten, ruwe randen, hiaten in de functie).

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met query's tussen data bases (verticaal partitioneren)](sql-database-elastic-query-getting-started-vertical.md)voor een verticaal gepartitioneerde zelf studie.
* Zie query's [uitvoeren op verticaal gepartitioneerde gegevens](sql-database-elastic-query-vertical-partitioning.md) voor syntaxis-en voorbeeld query's voor verticaal gepartitioneerde gegevens)
* Zie aan de slag [met elastische query's voor horizontale partitionering (sharding)](sql-database-elastic-query-getting-started.md)voor een zelf studie over horizontale partitionering (sharding).
* Zie query's [uitvoeren in horizon taal gepartitioneerde gegevens](sql-database-elastic-query-horizontal-partitioning.md) voor syntaxis-en voorbeeld query's voor Horizon taal gepartitioneerde gegevens)
* Zie [extern\_uitvoeren \_van SP](https://msdn.microsoft.com/library/mt703714) voor een opgeslagen procedure waarmee een Transact-SQL-instructie wordt uitgevoerd op één externe Azure SQL database of een set met data bases die fungeren als Shards in een horizon taal partitie schema.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
