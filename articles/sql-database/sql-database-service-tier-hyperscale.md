---
title: Overzicht van Hyperscale van Azure SQL Database | Microsoft Documenten
description: In dit artikel wordt de hyperscale-servicelaag beschreven in het op vCore gebaseerde inkoopmodel in Azure SQL Database en wordt uitgelegd hoe deze verschilt van de serviceniveaus Algemeen doel en Bedrijfskritieke.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: 074a28af8c80c109dbe97306900e8f00618e435a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411686"
---
# <a name="hyperscale-service-tier"></a>Hyperscale-servicelaag

Azure SQL Database is gebaseerd op SQL Server Database Engine-architectuur die is aangepast voor de cloudomgeving om 99,99% beschikbaarheid te garanderen, zelfs in het geval van infrastructuurfouten. Er zijn drie architectuurmodellen die worden gebruikt in Azure SQL Database:
- Algemeen doel/standaard 
-  Hyperscale
-  Bedrijfskritisch/Premium

De hyperscale-servicelaag in Azure SQL Database is de nieuwste servicelaag in het op vCore gebaseerde inkoopmodel. Deze servicelaag is een zeer schaalbare opslag- en rekenprestatieslaag die gebruikmaakt van de Azure-architectuur om de opslag- en rekenresources voor een Azure SQL-database aanzienlijk verder te schalen dan de limieten die beschikbaar zijn voor de servicelagen Algemeen Doel en Bedrijfskritieke.

> 
> [!NOTE]
> Zie [General Purpose](sql-database-service-tier-general-purpose.md) en [Business Critical](sql-database-service-tier-business-critical.md) servicelagen voor meer informatie over de servicelagen Algemeen Doel en Bedrijfskritieke in het op vCore gebaseerde inkoopmodel. Zie [Azure SQL Database-inkoopmodellen en -bronnen](sql-database-service-tiers.md)voor een vergelijking van het op vCore gebaseerde inkoopmodel met het op DTU gebaseerde inkoopmodel.


## <a name="what-are-the-hyperscale-capabilities"></a>Wat zijn de Hyperscale-mogelijkheden

De hyperscale-servicelaag in Azure SQL Database biedt de volgende extra mogelijkheden:

- Ondersteuning voor maximaal 100 TB databasegrootte
- Bijna ogenblikkelijke databaseback-ups (op basis van bestandsmomentopnamen die zijn opgeslagen in Azure Blob-opslag) ongeacht de grootte zonder IO-impact op compute resources  
- Snelle database herstelt (op basis van bestandsmomentopnamen) in minuten in plaats van uren of dagen (geen grootte van de gegevensbewerking)
- Hogere algemene prestaties door hogere logdoorvoer en snellere transactiecommittijden, ongeacht de gegevensvolumes
- Snelle uitschaing - u een of meer alleen-lezen knooppunten inrichten voor het ontladen van uw leeswerkbelasting en voor gebruik als hot-standbys
- Snel opschalen - u in constante tijd uw rekenresources opschalen om zware workloads mogelijk te maken wanneer dat nodig is, en vervolgens de rekenresources weer naar beneden schalen wanneer dit niet nodig is.

De Hyperscale-servicelaag verwijdert veel van de praktische limieten die traditioneel worden gezien in clouddatabases. Wanneer de meeste andere databases worden beperkt door de resources die beschikbaar zijn in één knooppunt, hebben databases in de hyperscale-servicelaag geen dergelijke limieten. Met zijn flexibele opslagarchitectuur groeit de opslag naar behoefte. Hyperscale-databases worden namelijk niet gemaakt met een gedefinieerde maximale grootte. Een Hyperscale-database groeit naar behoefte en u wordt alleen gefactureerd voor de capaciteit die u gebruikt. Voor leesintensieve workloads biedt de hyperscale-servicelaag een snelle scale-out door extra leesreplica's in te richten als dat nodig is voor het ontladen van leesbelasting.

Bovendien is de tijd die nodig is om databaseback-ups te maken of op te schalen of op te schalen niet langer gekoppeld aan het volume van gegevens in de database. Hyperscale databases kunnen vrijwel onmiddellijk worden geback-upt. U een database ook in de tientallen terabytes in enkele minuten omhoog of omlaag schalen. Deze mogelijkheid bevrijdt u van zorgen over het feit dat boxed in door uw eerste configuratie keuzes.

Zie [Kenmerken van servicelagen](sql-database-service-tiers-vcore.md#service-tiers)voor meer informatie over de rekengroottes voor de servicelaag Hyperscale.

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Wie moet de Hyperscale-servicelaag overwegen

De Hyperscale-servicelaag is bedoeld voor de meeste zakelijke workloads, omdat deze een grote flexibiliteit en hoge prestaties biedt met onafhankelijk schaalbare reken- en opslagbronnen. Met de mogelijkheid om opslag automatisch op te schalen tot 100 TB, is het een geweldige keuze voor klanten die:

- Hebben grote databases on-premises en willen hun applicaties moderniseren door over te stappen naar de cloud
- Zijn al in de cloud en worden beperkt door de maximale database grootte beperkingen van andere service lagen (1-4 TB)
- Heb kleinere databases, maar vereisen snelle verticale en horizontale compute scaling, hoge prestaties, directe back-up en snelle database herstel.

De Hyperscale-servicelaag ondersteunt een breed scala aan SQL Server-workloads, van pure OLTP tot pure analytics, maar is voornamelijk geoptimaliseerd voor OLTP- en hybride transactie- en analytische verwerkingsworkloads (HTAP).

> [!IMPORTANT]
> Elastische pools ondersteunen de servicelaag Hyperscale niet.

## <a name="hyperscale-pricing-model"></a>Prijzensmodel hyperschaal

Hyperscale servicelaag is alleen beschikbaar in [vCore-model.](sql-database-service-tiers-vcore.md) Om af te stemmen op de nieuwe architectuur, verschilt het prijsmodel enigszins van algemene doel- of bedrijfskritieke servicelagen:

- **Berekenen**:

  De prijs voor de berekeningseenheid hyperschaal is per replica. De [azure hybrid benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) prijs wordt toegepast om schaalreplica's automatisch te lezen. We maken standaard een primaire replica en één alleen-lezen replica per Hyperscale-database.  Gebruikers kunnen het totale aantal replica's, inclusief de primaire, aanpassen van 1-5.

- **Opslag**:

  U hoeft de maximale gegevensgrootte niet op te geven bij het configureren van een Hyperscale-database. In de hyperscale-laag worden kosten in rekening gebracht voor opslag voor uw database op basis van de werkelijke toewijzing. Opslag wordt automatisch toegewezen tussen 40 GB en 100 TB, in stappen van 10 GB. Meerdere gegevensbestanden kunnen groeien op hetzelfde moment indien nodig. Een Hyperscale-database wordt gemaakt met een begingrootte van 10 GB en begint elke 10 minuten met 10 GB te groeien, totdat deze de grootte van 40 GB bereikt.

Zie [Azure SQL Database Pricing](https://azure.microsoft.com/pricing/details/sql-database/single/) voor meer informatie over hyperscale-prijzen

## <a name="distributed-functions-architecture"></a>Architectuur voor gedistribueerde functies

In tegenstelling tot traditionele databaseengines die alle gegevensbeheerfuncties in één locatie/proces hebben gecentraliseerd (zelfs zogenaamde gedistribueerde databases die vandaag in productie zijn, hebben meerdere kopieën van een monolithische gegevensengine), scheidt een Hyperscale-database de queryverwerkingsengine, waar de semantiek van verschillende gegevensengines afwijkt van de componenten die langdurige opslag en duurzaamheid voor de gegevens bieden. Op deze manier kan de opslagcapaciteit soepel worden opgeschaald voor zover nodig (initiële doelstelling is 100 TB). Alleen-lezen replica's delen dezelfde opslagcomponenten, zodat er geen gegevenskopie nodig is om een nieuwe leesbare replica te draaien. 

In het volgende diagram worden de verschillende typen knooppunten in een Hyperscale-database weergegeven:

![architectuur](./media/sql-database-hyperscale/hyperscale-architecture.png)

Een Hyperscale-database bevat de volgende verschillende typen componenten:

### <a name="compute"></a>Compute

Het compute-knooppunt is waar de relationele engine woont, zodat alle taalelementen, queryverwerking, enzovoort, optreden. Alle gebruikersinteracties met een Hyperscale-database vinden plaats via deze compute nodes. Compute-knooppunten hebben SSD-gebaseerde caches (gelabeld RBPEX - Resilient Buffer Pool Extension in het vorige diagram) om het aantal netwerkretouren te minimaliseren dat nodig is om een pagina met gegevens op te halen. Er is één primair compute-knooppunt waar alle lees-schrijfworkloads en transacties worden verwerkt. Er zijn een of meer secundaire compute nodes die fungeren als hot standby-knooppunten voor failoverdoeleinden, evenals fungeren als alleen-lezen compute-knooppunten voor het ontladen van leesworkloads (als deze functionaliteit gewenst is).

### <a name="page-server"></a>Paginaserver

Paginaservers zijn systemen die een geschaalde opslagengine vertegenwoordigen.  Elke paginaserver is verantwoordelijk voor een subset van de pagina's in de database.  Nominaal besturingselementen voor elke paginaserver tussen 128 GB en 1 TB aan gegevens. Er worden geen gegevens gedeeld op meer dan één paginaserver (buiten replica's die worden bewaard voor redundantie en beschikbaarheid). De taak van een paginaserver is om databasepagina's op aanvraag uit te serveren naar de compute nodes en om de pagina's bijgewerkt te houden als gegevens over transacties. Paginaservers worden up-to-date gehouden door logboekrecords van de logboekservice af te spelen. Paginaservers behouden ook SSD-gebaseerde caches om de prestaties te verbeteren. Langdurige opslag van gegevenspagina's wordt bewaard in Azure Storage voor extra betrouwbaarheid.

### <a name="log-service"></a>Logboekservice

De logboekservice accepteert logboekrecords van de primaire compute-replica, houdt deze voort in een duurzame cache en stuurt de logboekrecords door naar de rest van compute-replica's (zodat ze hun caches kunnen bijwerken) en de relevante paginaserver(s), zodat de gegevens daar kunnen worden bijgewerkt. Op deze manier worden alle gegevenswijzigingen van de primaire compute-replica via de logboekservice gepropageerd naar alle secundaire compute replica's en paginaservers. Ten slotte worden de logboekrecords naar langdurige opslag in Azure Storage gepusht, wat een vrijwel oneindige opslagopslagplaats is. Dit mechanisme verwijdert de noodzaak voor frequente logboekafvloeiing. De logboekservice heeft ook lokale cache om de toegang tot logboekrecords te versnellen.

### <a name="azure-storage"></a>Azure Storage

Azure Storage bevat alle gegevensbestanden in een database. Paginaservers houden gegevensbestanden in Azure Storage up-to-date. Deze opslag wordt gebruikt voor back-updoeleinden en voor replicatie tussen Azure-regio's. Back-ups worden geïmplementeerd met behulp van storage snapshots van gegevensbestanden. Herstelbewerkingen met behulp van momentopnamen zijn snel, ongeacht de grootte van de gegevens. Gegevens kunnen worden hersteld naar elk moment binnen de back-upbewaarperiode van de database.

## <a name="backup-and-restore"></a>Back-ups en herstellen

Back-ups zijn file-snapshot gebaseerd en dus zijn ze bijna ogenblikkelijk. Opslag- en compute-scheiding maakt het mogelijk om de back-up-/herstelbewerking naar de opslaglaag te verlagen om de verwerkingsbelasting voor de primaire compute-replica te verminderen. Als gevolg hiervan heeft databaseback-up geen invloed op de prestaties van het primaire compute-knooppunt; Op dezelfde manier worden herstelt gedaan door terug te keren naar bestandsmomentopnamen, en als zodanig zijn niet een grootte van de gegevensbewerking. Herstel is een constante bewerking en zelfs databases met meerdere terabytes kunnen in minuten worden hersteld in plaats van uren of dagen. Het maken van nieuwe databases door het herstellen van een bestaande back-up maakt ook gebruik van deze functie: het maken van databasekopieën voor ontwikkelings- of testdoeleinden, zelfs van terabyte-formaat databases, is binnen enkele minuten te doen.

## <a name="scale-and-performance-advantages"></a>Schaal- en prestatievoordelen

Met de mogelijkheid om snel extra alleen-lezen rekenknooppunten op te draaien/ neer te zetten, maakt de Hyperscale-architectuur aanzienlijke leesschaalmogelijkheden mogelijk en kan het primaire compute-knooppunt ook vrijmaken voor het weergeven van meer schrijfverzoeken. Ook kunnen de compute nodes snel worden opgeschaald/omlaag vanwege de shared storage-architectuur van de Hyperscale-architectuur.

## <a name="create-a-hyperscale-database"></a>Een Hyperscale-database maken

Er kan een Hyperscale-database worden gemaakt met behulp van de [Azure-portal](https://portal.azure.com), [T-SQL,](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current) [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) of [CLI.](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) Hyperscale databases zijn alleen beschikbaar met behulp van het [vCore-gebaseerde inkoopmodel.](sql-database-service-tiers-vcore.md)

Met de volgende Opdracht T-SQL wordt een Hyperscale-database gesmaakt. U moet zowel de editie als `CREATE DATABASE` de servicedoelstelling opgeven in de instructie. Raadpleeg de [resourcelimieten](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen4) voor een lijst met geldige servicedoelstellingen.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Hierdoor ontstaat een Hyperscale-database op Gen5-hardware met 4 cores.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Een bestaande Azure SQL-database migreren naar de hyperscale-servicelaag

U uw bestaande Azure SQL-databases verplaatsen naar Hyperscale via de [Azure-portal,](https://portal.azure.com) [T-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) of [CLI.](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) Op dit moment is dit een eenrichtingsmigratie. U databases niet verplaatsen van Hyperscale naar een andere servicelaag, behalve door gegevens te exporteren en te importeren. Voor proofs of concept (POC's) raden we u aan een kopie van uw productiedatabases te maken en de kopie naar Hyperscale te migreren. Het migreren van een bestaande Azure SQL-database naar de hyperscale-laag is een grootte van de gegevensbewerking.

Met de volgende Opdracht T-SQL wordt een database verplaatst naar de hyperscale-servicelaag. U moet zowel de editie als `ALTER DATABASE` de servicedoelstelling opgeven in de instructie.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Verbinding maken met een leesbare replica van een Hyperscale-database

In Hyperscale-databases `ApplicationIntent` bepaalt het argument in de verbindingstekenreeks die door de client wordt geleverd, of de verbinding wordt doorgestuurd naar de schrijfreplica of naar een alleen-lezen secundaire replica. Als `ApplicationIntent` de `READONLY` set en de database geen secundaire replica hebben, wordt de verbinding `ReadWrite` doorgestuurd naar de primaire replica en standaard naar gedrag.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Secundaire replica's met hyperschaal zijn allemaal identiek en gebruiken dezelfde doelstelling op serviceniveau als de primaire replica. Als er meer dan één secundaire replica aanwezig is, wordt de werkbelasting verdeeld over alle beschikbare secondaries. Elke secundaire replica wordt onafhankelijk bijgewerkt, dus verschillende replica's kunnen verschillende gegevenslatentie ten opzichte van de primaire replica hebben.

## <a name="database-high-availability-in-hyperscale"></a>Hoge beschikbaarheid database in Hyperscale

Net als in alle andere servicelagen garandeert Hyperscale de duurzaamheid van gegevens voor toegezegde transacties, ongeacht de beschikbaarheid van compute-replica's. De mate van downtime als gevolg van de primaire replica steeds niet beschikbaar is afhankelijk van het type failover (gepland vs. ongepland), en van de aanwezigheid van ten minste een secundaire replica. In een geplande failover (d.w.z. een onderhoudsgebeurtenis) maakt het systeem de nieuwe primaire replica voordat een failover wordt gestart of gebruikt het een bestaande secundaire replica als failoverdoel. In een ongeplande failover (d.w.z. een hardwarefout op de primaire replica) gebruikt het systeem een secundaire replica als een failoverdoel als er een bestaat, of maakt het een nieuwe primaire replica uit de pool met beschikbare rekencapaciteit. In het laatste geval is de downtimeduur langer vanwege extra stappen die nodig zijn om de nieuwe primaire replica te maken.

Zie SLA voor [Azure SQL Database voor](https://azure.microsoft.com/support/legal/sla/sql-database/)Hyperscale SLA .

## <a name="disaster-recovery-for-hyperscale-databases"></a>Disaster Recovery voor hyperscale databases

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Een Hyperscale-database herstellen naar een andere geografie
Als u een Azure SQL Database Hyperscale DB moet herstellen naar een andere regio dan de regio waarin deze momenteel wordt gehost, als onderdeel van een disaster recovery-bewerking of -oefening, verplaatsing of een andere reden, is de primaire methode om een geoherstel van de database uit te voeren.  Dit omvat precies dezelfde stappen als wat u zou gebruiken om een andere AZURE SQL DB te herstellen naar een andere regio:
1. Maak een Azure SQL Database-server in het doelgebied als u daar nog geen geschikte server hebt.  Deze server moet eigendom zijn van hetzelfde abonnement als de oorspronkelijke (bron)server.
2. Volg de instructies in het [geo-herstelonderwerp](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) van de pagina over het herstellen van Azure SQL-databases van automatische back-ups.

> [!NOTE]
> Omdat de bron en het doel zich in afzonderlijke regio's bevinden, kan de database geen momentopnameopslag delen met de brondatabase zoals in niet-geoherstel, die uiterst snel worden voltooid. In het geval van een geo-herstel van een Hyperscale-database is dit een grootte van gegevens, zelfs als het doel zich in het gekoppelde gebied van de geo-gerepliceerde opslag bevindt.  Dat betekent dat het doen van een geo-herstel tijd zal vergen evenredig aan de grootte van de database die wordt hersteld.  Als het doel zich in het gekoppelde gebied bevindt, bevindt de kopie zich binnen een regio, die aanzienlijk sneller is dan een kopie in verschillende regio's, maar het is nog steeds een grootte van gegevens.

## <a name="available-regions"></a><a name=regions></a>Beschikbare regio's

De laag Hyperscale van Azure SQL Database is momenteel beschikbaar in de volgende regio's:

- Australië - oost
- Australië - zuidoost
- Brazilië - zuid
- Canada - midden
- VS - centraal
- China Oost 2
- China Noord 2
- Azië - oost
- VS - oost
- Oost-Ons 2
- Frankrijk - centraal
- Japan - oost
- Japan - west
- Korea - centraal
- Korea - zuid
- VS - noord-centraal
- Europa - noord
- Zuid-Afrika Noord
- VS - zuid-centraal
- Azië - zuidoost
- Verenigd Koninkrijk Zuid
- Verenigd Koninkrijk West
- Europa -west
- VS - west
- VS - west 2

Als u Hyperscale-database wilt maken in een regio die niet wordt vermeld als ondersteund, u een onboarding-aanvraag verzenden via Azure-portal. Zie [Quotaverhogingen voor Azure SQL Database](quota-increase-request.md) voor instructies aanvragen voor instructies. Gebruik bij het indienen van uw aanvraag de volgende richtlijnen:

- Gebruik het [sql-databasequotumtype Andere quotumaanvraag](quota-increase-request.md#other) voor quota.
- Voeg in de tekstdetails de reken-SKU/totale kernen toe, inclusief leesbare replica's.
- Geef ook de geschatte tb op.

## <a name="known-limitations"></a>Bekende beperkingen

Dit zijn de huidige beperkingen voor de hyperscale-servicelaag vanaf GA.  We zijn actief bezig om zoveel mogelijk van deze beperkingen te verwijderen.

| Probleem | Beschrijving |
| :---- | :--------- |
| In het deelvenster Back-ups beheren voor een logische server worden geen Hyperscale-databases weergegeven, deze worden uit de weergave gefilterd  | Hyperscale heeft een aparte methode voor het beheren van back-ups, en als zodanig de lange termijn retentie en point in time back-up retentie-instellingen niet van toepassing / worden ongeldig gemaakt. Hyperscale-databases worden dus niet weergegeven in het deelvenster Back-up beheren. |
| Terugzetten naar eerder tijdstip | U een Hyperscale-database herstellen in een niet-Hyperscale-database, binnen de bewaarperiode van niet-hyperscale-database. U een niet-Hyperscale-database niet herstellen in een Hyperscale-database.|
| Als een database een of meer gegevensbestanden groter dan 1 TB heeft, mislukt migratie | In sommige gevallen kan het mogelijk zijn om te werken rond dit probleem door het krimpen van de grote bestanden te worden minder dan 1 TB. Als u een database migreert die tijdens het migratieproces wordt gebruikt, moet u ervoor zorgen dat geen enkel bestand groter wordt dan 1 TB. Gebruik de volgende query om de grootte van databasebestanden te bepalen. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Beheerd exemplaar | Azure SQL Database Managed Instance wordt momenteel niet ondersteund met Hyperscale-databases. |
| Elastische pools |  Elastic Pools worden momenteel niet ondersteund met SQL Database Hyperscale.|
| Migratie naar Hyperscale is momenteel een eenrichtingsbewerking | Zodra een database is gemigreerd naar Hyperscale, kan deze niet rechtstreeks worden gemigreerd naar een servicelaag van niet-hyperschaal. Op dit moment is de enige manier om een database te migreren van Hyperscale naar niet-Hyperscale door te exporteren/importeren met behulp van een BACPAC-bestand of andere gegevensbewegingstechnologieën (Bulk Copy, Azure Data Factory, Azure Databricks, SSIS, enz.)|
| Migratie van databases met permanente in-memory objecten | Hyperscale ondersteunt alleen niet-persistente In-Memory-objecten (tabeltypen, native SP's en functies).  Permanente in-memorytabellen en andere objecten moeten worden verwijderd en opnieuw worden gemaakt als objecten die niet in het geheugen zijn voordat u een database migreert naar de servicelaag Hyperscale.|
| Georeplicatie  | U georeplicatie nog niet configureren voor Azure SQL Database Hyperscale. |
| Databasekopiëren | U Databasekopiëren nog niet gebruiken om een nieuwe database te maken in Azure SQL Hyperscale. |
| TDE/AKV Integratie | Transparante databaseversleuteling met Azure Key Vault (ook wel Bring-Your-Own-Key of BYOK genoemd) wordt nog niet ondersteund voor Azure SQL Database Hyperscale, maar TDE met Service Managed Keys wordt volledig ondersteund. |
|Intelligente databasefuncties | Met uitzondering van de optie 'Force Plan' worden alle andere opties voor automatisch afstemmen nog niet ondersteund op Hyperscale: opties lijken mogelijk ingeschakeld, maar er worden geen aanbevelingen of acties gedaan. |
|Inzicht in queryprestaties | Query Performance Insights wordt momenteel niet ondersteund voor Hyperscale-databases. |
| Database verkleinen | DBCC SHRINKDATABASE of DBCC SHRINKFILE wordt momenteel niet ondersteund voor Hyperscale-databases. |
| Controle van de integriteit van de database | DBCC CHECKDB wordt momenteel niet ondersteund voor Hyperscale-databases. Zie [Gegevensintegriteit in Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) voor meer informatie over gegevensintegriteitsbeheer in Azure SQL Database. |

## <a name="next-steps"></a>Volgende stappen

- Zie [Veelgestelde vragen over Hyperscale](sql-database-service-tier-hyperscale-faq.md)voor een veelgestelde faq over Hyperscale.
- Zie [Servicelagen](sql-database-service-tiers.md) voor informatie over servicelagen
- Zie [Overzicht van resourcelimieten op een logische server](sql-database-resource-limits-logical-server.md) voor informatie over limieten op server- en abonnementsniveau.
- Zie [Azure SQL Database vCore-modellimieten voor één database voor](sql-database-vcore-resource-limits-single-databases.md)het aanschaffen van modellimieten voor één database.
- Zie [ALGEMENE SQL-functies voor](sql-database-features.md)een lijst met functies en vergelijkingsfuncties .
