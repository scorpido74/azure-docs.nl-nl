---
title: Azure SQL Data Sync | Microsoft Docs
description: In dit overzicht introduceert Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 08/20/2019
ms.openlocfilehash: 7ff7712130372dcfd277750e881cccce23b36465
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648348"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Gegevens synchroniseren tussen meerdere cloud en on-premises databases met SQL Data Sync

SQL Data Sync is een service die is gebouwd op Azure SQL Database waarmee u de gegevens die u bidirectioneel op meerdere SQL-databases en SQL Server-exemplaren selecteert worden gesynchroniseerd.

> [!IMPORTANT]
> Azure SQL Data Sync biedt op dit moment **geen** ondersteuning voor beheerde exemplaren voor Azure SQL Database.

## <a name="when-to-use-data-sync"></a>Wanneer u Data Sync

Gegevenssynchronisatie is handig in gevallen waarbij gegevens moeten worden up-to-date gehouden over meerdere Azure SQL-databases of SQL Server-databases. Hier volgen de belangrijkste gebruiksvoorbeelden voor Data Sync:

- **Gegevens synchronisatie hybride:** Met gegevens synchronisatie kunt u gegevens gesynchroniseerd blijven tussen uw on-premises data bases en Azure SQL-data bases om hybride toepassingen in te scha kelen. Deze mogelijkheid kan beroep instellen op klanten die van plan bent te verplaatsen naar de cloud en wil graag enkele van de toepassing in Azure te plaatsen.
- **Gedistribueerde toepassingen:** In veel gevallen is het nuttig om verschillende werk belastingen te scheiden in verschillende data bases. Bijvoorbeeld, als u een grote productiedatabase hebt, maar u moet ook een rapport of de analytics-workload wordt uitgevoerd op deze gegevens, is het handig om een tweede database voor deze extra belasting. Deze aanpak minimaliseert de prestatie-invloed op uw productie-werkbelasting. Data Sync kunt u deze twee databases gesynchroniseerd houden.
- **Wereld wijd gedistribueerde toepassingen:** Veel bedrijven omvatten verschillende regio's en zelfs verschillende landen/regio's. Als u wilt de netwerklatentie beperken, is het raadzaam om uw gegevens in een regio dicht bij u. U kunt databases eenvoudig in regio's over de hele wereld gesynchroniseerd houden met het synchroniseren van gegevens.

Gegevenssynchronisatie is niet de beste oplossing voor de volgende scenario's:

| Scenario | Sommige aanbevolen oplossingen |
|----------|----------------------------|
| Herstel na noodgevallen | [Azure geografisch redundante back-ups](sql-database-automated-backups.md) |
| Schaal lezen | [Alleen-lezen replica's gebruiken om te laden saldo alleen-lezen query workloads (preview)](sql-database-read-scale-out.md) |
| ETL (OLTP met OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) of [SQL Server integratieservices](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migratie van on-premises SQL Server naar Azure SQL Database | [Azure Database migratieservice](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Overzicht van SQL Data Sync

Gegevenssynchronisatie is gebaseerd op het concept van een Synchronisatiegroep. Een groep voor synchronisatie is een groep databases die u wilt synchroniseren.

Data Sync maakt gebruik van een hub en spoke-topologie om gegevens te synchroniseren. U definieert een van de databases in de groep voor synchronisatie als de Hubdatabase. De rest van de databases zijn lid databases. Synchronisatie plaatsvindt tussen de Hub en de afzonderlijke leden.

- De **Hubdatabase** moet een Azure SQL Database.
- De **lid databases** mag bestaan uit SQL-Databases, on-premises SQL Server-databases of SQL Server-exemplaren op Azure virtual machines.
- De **synchronisatiedatabase** bevat de metagegevens en het logboek voor Data Sync. De synchronisatiedatabase is dat een Azure SQL Database zich in dezelfde regio als de Hubdatabase. De synchronisatiedatabase is gemaakt van de klant en van klanten.

> [!NOTE]
> Als u een on-premises-database als een liddatabase gebruikt, hebt u [installeren en configureren van een lokale synchronisatieagent](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Gegevens tussen databases synchroniseren](media/sql-database-sync-data/sync-data-overview.png)

Een groep voor synchronisatie heeft de volgende eigenschappen:

- De **synchronisatieschema** wordt beschreven welke gegevens worden gesynchroniseerd.
- De **synchronisatierichting** kan bidirectionele of kan stromen slechts in één richting. Dat wil zeggen, de richting van de synchronisatie kan worden *Hub aan een lid van*, of *lid aan de Hub*, of beide.
- De **synchronisatie-Interval** wordt beschreven hoe vaak synchronisatie plaatsvindt.
- De **Conflict resolutie beleid** is een beleid op het niveau, die kan worden *Hub wins* of *lid wins*.

## <a name="how-does-data-sync-work"></a>Hoe werkt gegevens synchronisatie?

- **Wijzigingen in de gegevens bijhouden:** Gegevens synchronisatie houdt wijzigingen bij met behulp van INSERT-, update-en delete-triggers. De wijzigingen worden vastgelegd in een tabel aan de in de database. Houd er rekening mee dat BULKSGEWIJS invoegen wordt niet geactiveerd triggers standaard. Als bulkhints niet opgegeven is, wordt er geen triggers voor invoegen uitvoeren. De optie bulkhints toevoegen zodat gegevenssynchronisatie die ingevoegd bijhouden kunt. 
- **Gegevens synchroniseren:** Gegevens synchronisatie is ontworpen in een hub-en spoke-model. De Hub synchroniseert afzonderlijk met elk lid. Wijzigingen van de Hub worden gedownload naar het lid en vervolgens de wijzigingen van het lid worden geüpload naar de Hub.
- **Conflicten oplossen:** Gegevens synchronisatie biedt twee opties voor het oplossen van conflicten, *hub WINS* of *lid van WINS*.
  - Als u selecteert *Hub wins*, wijzigingen in het lid altijd worden overschreven door de wijzigingen in de hub.
  - Als u selecteert *lid wins*, de wijzigingen in de wijzigingen overschrijven in de hub. Als er meer dan één lid, afhankelijk van de uiteindelijke waarde waarvoor de eerst synchroniseert.

## <a name="compare-data-sync-with-transactional-replication"></a>Gegevens synchronisatie met transactionele replicatie vergelijken

| | Gegevenssynchronisatie | Transactionele replicatie |
|---|---|---|
| Voordelen | -Actief-actief ondersteuning<br/>-Bi-richting tussen on-premises en Azure SQL Database | -Laagste latentie<br/>-Transactionele consistentie<br/>-Bestaande topologie na migratie opnieuw gebruiken |
| Nadelen | -5 minuten of meer latentie<br/>-Geen transactionele consistentie<br/>-Hogere gevolgen voor de prestaties | -Kan niet publiceren vanuit Azure SQL Database afzonderlijke data base of gegroepeerde Data Base<br/>-Hoge onderhouds kosten |
| | | |

## <a name="get-started-with-sql-data-sync"></a>Aan de slag met SQL Data Sync

### <a name="set-up-data-sync-in-the-azure-portal"></a>Data Sync instellen in Azure portal

- [Azure SQL Data Sync instellen](sql-database-get-started-sql-data-sync.md)
- Data Sync-agent: [Data Sync-agent voor Azure SQL Data Sync](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Data Sync met PowerShell instellen

- [PowerShell gebruiken om meerdere Azure SQL-databases te synchroniseren](scripts/sql-database-sync-data-between-sql-databases.md)
- [PowerShell gebruiken om te synchroniseren tussen een Azure SQL-database en een on-premises database](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Bekijk de aanbevolen procedures voor gegevenssynchronisatie

- [Aanbevolen procedures voor Azure SQL Data Sync](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Er is iets fout gegaan

- [Problemen oplossen met Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Consistentie en prestaties

#### <a name="eventual-consistency"></a>Uiteindelijke consistentie

Omdat Data Sync op basis van een trigger is, transactionele consistentie kan niet worden gegarandeerd. Microsoft garandeert dat alle wijzigingen uiteindelijk worden gemaakt en dat gegevenssynchronisatie niet leiden gegevensverlies tot.

#### <a name="performance-impact"></a>Prestatie-impact

Sync maakt gebruik van gegevens invoegen, bijwerken en verwijderen van triggers voor het bijhouden van wijzigingen. Side tabellen wordt gemaakt in de database voor het bijhouden. Deze wijziging bijhouden activiteiten hebben een invloed op de werkbelasting van uw database. Beoordeel uw servicelaag en indien nodig een upgrade uitvoert.

Inrichting en ongedaan maken van inrichting tijdens de synchronisatiegroep wordt gemaakt, bijwerken en verwijderen kunnen ook invloed op de prestaties van de database. 

## <a name="sync-req-lim"></a> Vereisten en beperkingen

### <a name="general-requirements"></a>Algemene vereisten

- Elke tabel moet een primaire sleutel hebben. De waarde van de primaire sleutel in elke rij niet te wijzigen. Als u de waarde van een primaire sleutel te wijzigen moet, verwijdert u de rij en maak deze opnieuw met de nieuwe waarde voor de primaire sleutel. 

> [!IMPORTANT]
> Als u de waarde van een bestaande primaire sleutel wijzigt, resulteert dit in het volgende probleem:   
>   - De gegevens tussen de hub en het lid kunnen verloren gaan, zelfs al wordt er door synchronisatie geen problemen gerapporteerd.
> - De synchronisatie kan mislukken omdat de tracerings tabel een niet-bestaande rij van de bron bevat, omdat de primaire sleutel is gewijzigd.

- Snapshot-isolatie moet zijn ingeschakeld. Zie voor meer informatie, [Snapshot-isolatie in SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Algemene beperkingen

- Een tabel kan niet een identiteitskolom die niet de primaire sleutel hebben.
- Een primaire sleutel kan niet hebben de volgende gegevenstypen: sql_variant, binary, varbinary, image, xml. 
- Wees voorzichtig wanneer u de volgende gegevenstypen als een primaire sleutel gebruiken, omdat de ondersteunde precisie alleen voor de tweede is: tijd, datum/tijd, datetime2, datetimeoffset.
- De namen van objecten (databases, tabellen en kolommen) kunnen bevatten de afdrukbare tekens punt (.), vierkante linkerhaak ([) of rechts vierkante haak (]).
- Azure Active Directory-verificatie wordt niet ondersteund.
- Tabellen met dezelfde naam maar met een ander schema (bijvoorbeeld dbo.customers en sales.customers) worden niet ondersteund.
- Kolommen met door de gebruiker gedefinieerde gegevens typen worden niet ondersteund

#### <a name="unsupported-data-types"></a>Niet-ondersteunde gegevenstypen

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection (XML ondersteund)
- Cursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Niet-ondersteunde kolommen van het type

Data Sync kan niet alleen-lezen of het systeem gegenereerde kolommen worden gesynchroniseerd. Bijvoorbeeld:

- Berekende kolommen.
- Het systeem gegenereerde kolommen voor tijdelijke tabellen.

#### <a name="limitations-on-service-and-database-dimensions"></a>Beperkingen met betrekking tot de service en de database

| **Dimensies**                                                      | **Limiet**              | **Tijdelijke oplossing**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximum aantal synchronisatiegroepen elke database kan deel uitmaken.       | 5                      |                             |
| Maximum aantal eindpunten in een enkele synchronisatiegroep              | 30                     |                             |
| Maximum aantal on-premises eindpunten in een enkele synchronisatiegroep. | 5                      | Maken van meerdere synchronisatiegroepen |
| Database-, tabel-, schema-en kolomnamen                       | 50 tekens per naam |                             |
| Tabellen in een groep voor synchronisatie                                          | 500                    | Maken van meerdere synchronisatiegroepen |
| Kolommen in een tabel in een groep voor synchronisatie                              | 1000                   |                             |
| Grootte van de rij gegevens in een tabel                                        | 24 mb                  |                             |
| Minimale synchronisatie-interval                                           | 5 minuten              |                             |
|||
> [!NOTE]
> Mogelijk zijn er maximaal 30-eindpunten in een enkele synchronisatiegroep als er slechts één synchronisatiegroep. Als er meer dan één groep voor synchronisatie, kan het totale aantal eindpunten voor alle synchronisatiegroepen niet langer zijn dan 30. Als een database tot meerdere synchronisatiegroepen behoort, telt deze ook mee als meerdere eindpunten, niet een.

## <a name="faq-about-sql-data-sync"></a>Veelgestelde vragen over SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Hoeveel kost het SQL Data Sync service kosten

Er zijn geen kosten voor de SQL Data Sync-service zelf.  Maar verrekenen u nog steeds kosten voor gegevensoverdracht voor verplaatsing van gegevens in en uit uw SQL Database-exemplaar. Zie voor meer informatie, [prijzen van SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Welke regio's ondersteunen gegevens synchronisatie

SQL Data Sync is beschikbaar in alle regio's.

### <a name="is-a-sql-database-account-required"></a>Is een SQL Database account vereist

Ja. U moet een SQL-Database-account voor het hosten van de Hubdatabase hebben.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Kan ik Data Sync gebruiken om te synchroniseren tussen SQL Server on-premises data bases

Niet rechtstreeks. U kunt synchroniseren tussen SQL Server on-premises database indirect echter door het maken van een hubdatabase in Azure en vervolgens de on-premises databases toe te voegen aan de groep voor synchronisatie.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Kan ik gegevens synchronisatie gebruiken om te synchroniseren tussen SQL-data bases die deel uitmaken van verschillende abonnementen

Ja. U kunt synchroniseren tussen SQL-Databases die deel uitmaken van resourcegroepen die eigendom zijn van verschillende abonnementen.

- Als de abonnementen tot dezelfde tenant behoren en u bent gemachtigd voor alle abonnementen, kunt u de groep voor synchronisatie configureren in Azure portal.
- Anders moet u PowerShell gebruiken voor het toevoegen van de synchronisatieleden die tot verschillende abonnementen behoren.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china"></a>Kan ik gegevens synchronisatie gebruiken om te synchroniseren tussen SQL-data bases die deel uitmaken van verschillende Clouds (zoals Azure open bare Cloud en Azure China)

Ja. U kunt synchroniseren tussen SQL-Databases die deel uitmaken van verschillende clouds, u moet PowerShell gebruiken om toe te voegen de synchronisatieleden die deel uitmaken van de verschillende abonnementen.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Kan ik gegevens synchronisatie gebruiken voor het seeden van gegevens van mijn productie database naar een lege data base en deze vervolgens synchroniseren

Ja. Het schema handmatig maken in de nieuwe database door het uitvoeren van scripts van het origineel. Nadat u het schema hebt gemaakt, moet u de tabellen toevoegen aan een synchronisatiegroep om te kopiëren van de gegevens en deze gesynchroniseerd.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Moet ik SQL Data Sync gebruiken om een back-up te maken van mijn data bases en deze te herstellen

Het verdient aanbeveling niet naar SQL Data Sync gebruiken voor het maken van een back-up van uw gegevens. U kunt geen back-up en herstellen naar een bepaald punt in tijd omdat SQL Data Sync synchronisaties niet samengesteld zijn. SQL Data Sync is bovendien andere SQL-objecten, zoals opgeslagen procedures, geen back-up en het equivalent van een herstelbewerking niet snel kan.

Zie voor een back-up techniek aanbevolen, [een Azure SQL database kopiëren](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Kan gegevens synchronisatie versleutelde tabellen en kolommen synchroniseren

- Als een database maakt gebruik van altijd versleuteld, kunt u alleen de tabellen en kolommen die zijn synchroniseren *niet* versleuteld. U kunt de versleutelde kolommen, kan niet synchroniseren omdat Data Sync kan de gegevens niet ontsleutelen.
- Als een kolom wordt gebruikt op kolomniveau versleuteling (CLE), kunt u de kolom kunt synchroniseren, zolang de rijgrootte van de kleiner dan de maximale grootte van 24 Mb is. Gegevenssynchronisatie behandelt de kolom die is versleuteld met sleutel (wissen) als normale binaire gegevens. Voor het ontsleutelen van de gegevens op andere synchronisatieleden van de, moet u hetzelfde certificaat hebben.

### <a name="is-collation-supported-in-sql-data-sync"></a>Wordt gesorteerd ondersteund in SQL Data Sync

Ja. SQL Data Sync ondersteunt sortering in de volgende scenario's:

- Als de geselecteerde synchronisatie-schema-tabellen niet al zijn in uw databases hub of een lid en wanneer u de groep voor synchronisatie implementeert, maakt de service automatisch de bijbehorende tabellen en kolommen met de sorteerinstellingen geselecteerd in de databases leeg doel.
- Als de tabellen om te worden gesynchroniseerd al in uw hub en de lid-databases bestaat, SQL Data Sync is vereist dat de primaire-sleutelkolommen dezelfde sortering tussen hub en lid databases hebben voor een succesvolle implementatie van de groep voor synchronisatie. Er zijn geen beperkingen sortering op kolommen dan de primaire-sleutelkolommen.

### <a name="is-federation-supported-in-sql-data-sync"></a>Wordt Federatie ondersteund in SQL Data Sync

Hoofddatabase voor Federatie kan worden gebruikt in SQL Data Sync Service zonder een beperking. U kunt het eindpunt van de federatieve-Database niet toevoegen aan de huidige versie van SQL Data Sync.

## <a name="next-steps"></a>Volgende stappen

### <a name="update-the-schema-of-a-synced-database"></a>Het schema van een gesynchroniseerde database bijwerken

Hebt u het schema van een database in een groep voor synchronisatie bijwerken? Wijzigingen in het schema worden niet automatisch worden gerepliceerd. Zie de volgende artikelen voor een aantal oplossingen:

- [De replicatie van schemawijzigingen in Azure SQL Data Sync automatiseren](sql-database-update-sync-schema.md)
- [PowerShell gebruiken voor het bijwerken van het synchronisatieschema in een bestaande synchronisatiegroep](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Controleren en problemen oplossen

SQL Data Sync uitvoert zoals verwacht? Als u wilt bewaken van activiteit en het oplossen van problemen, Zie de volgende artikelen:

- [Azure-SQL Data Sync controleren met Azure Monitor-logboeken](sql-database-sync-monitor-oms.md)
- [Problemen oplossen met Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Meer informatie over Azure SQL Database

Zie de volgende artikelen voor meer informatie over SQL Database:

- [Wat is de Azure SQL Database-service?](sql-database-technical-overview.md)
- [Database Lifecycle Management (DLM)](https://msdn.microsoft.com/library/jj907294.aspx)
