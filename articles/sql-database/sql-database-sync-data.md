---
title: Azure SQL Data Sync
description: In dit overzicht worden Azure-SQL Data Sync geïntroduceerd
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
ms.openlocfilehash: 925977edf267510399dc631f0d0efe5fc1941803
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687051"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Gegevens synchroniseren tussen meerdere Cloud-en on-premises data bases met SQL Data Sync

SQL Data Sync is een service die is gebouwd op Azure SQL Database waarmee u de gegevens die u in twee richtingen selecteert, kunt synchroniseren in meerdere SQL-data bases en SQL Server exemplaren.

> [!IMPORTANT]
> Azure SQL Data Sync biedt op dit moment **geen** ondersteuning voor beheerde exemplaren voor Azure SQL Database.

## <a name="when-to-use-data-sync"></a>Wanneer u gegevens synchronisatie wilt gebruiken

Gegevens synchronisatie is handig in gevallen waarin gegevens moeten worden bewaard in meerdere Azure SQL-data bases of SQL Server data bases. Hier volgen de belangrijkste use cases voor gegevens synchronisatie:

- **Gegevens synchronisatie hybride:** Met gegevens synchronisatie kunt u gegevens gesynchroniseerd blijven tussen uw on-premises data bases en Azure SQL-data bases om hybride toepassingen in te scha kelen. Deze mogelijkheid kan worden bezwaarder voor klanten die overwegen over te stappen naar de Cloud en graag een deel van hun toepassing in azure willen plaatsen.
- **Gedistribueerde toepassingen:** In veel gevallen is het nuttig om verschillende werk belastingen te scheiden in verschillende data bases. Als u bijvoorbeeld een grote productie database hebt, maar u ook een rapportage-of analyse werk belasting moet uitvoeren op deze gegevens, is het handig om een tweede Data Base voor deze extra workload te hebben. Deze aanpak minimaliseert de invloed op de prestaties van uw productiewerk belasting. U kunt gegevens synchronisatie gebruiken om deze twee data bases te synchroniseren.
- **Wereld wijd gedistribueerde toepassingen:** Veel bedrijven omvatten verschillende regio's en zelfs verschillende landen/regio's. Om de netwerk latentie te minimaliseren, is het het beste om uw gegevens in een regio dicht bij u af te sluiten. Met gegevens synchronisatie kunt u eenvoudig data bases in regio's over de hele wereld gesynchroniseerd blijven.

Gegevens synchronisatie is niet de aanbevolen oplossing voor de volgende scenario's:

| Scenario | Enkele aanbevolen oplossingen |
|----------|----------------------------|
| Herstel na noodgevallen | [Azure geo-redundante back-ups](sql-database-automated-backups.md) |
| Schaal lezen | [Alleen-lezen replica's gebruiken om taken te verdelen over alleen-lezen query's (preview-versie)](sql-database-read-scale-out.md) |
| ETL (OLTP to OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) of [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migratie van on-premises SQL Server naar Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Overzicht van SQL Data Sync

Gegevens synchronisatie is gebaseerd op het concept van een synchronisatie groep. Een synchronisatie groep is een groep data bases die u wilt synchroniseren.

Bij de gegevens synchronisatie wordt gebruikgemaakt van een hub-en spoke-topologie voor het synchroniseren van gegevens. U definieert een van de data bases in de synchronisatie groep als de hub-data base. De rest van de data bases zijn leden databases. Synchronisatie vindt alleen plaats tussen de hub en de afzonderlijke leden.

- De **hub-data base** moet een Azure SQL database zijn.
- De **leden databases** kunnen bestaan uit SQL-data bases, on-premises SQL server data bases of SQL Server exemplaren op virtuele machines van Azure.
- De **synchronisatie database** bevat de meta gegevens en het logboek voor de gegevens synchronisatie. De synchronisatie database moet een Azure SQL Database zijn die zich in dezelfde regio bevindt als de hub-data base. De gesynchroniseerde data base is klant gemaakt en is eigenaar van de klant.

> [!NOTE]
> Als u een on-premises Data Base als een lid-data base gebruikt, moet u [een lokale synchronisatie agent installeren en configureren](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Gegevens synchroniseren tussen data bases](media/sql-database-sync-data/sync-data-overview.png)

Een synchronisatie groep heeft de volgende eigenschappen:

- In het **synchronisatie schema** wordt beschreven welke gegevens worden gesynchroniseerd.
- De **synchronisatie richting** kan bidirectionele zijn of kan in slechts één richting stromen. Dat wil zeggen dat de synchronisatie richting van de *hub naar het lid*, of van het lid is van een *hub*of van beide kan zijn.
- Het **synchronisatie-interval** beschrijft hoe vaak synchronisatie plaatsvindt.
- Het **beleid** voor het oplossen van conflicten is een beleid op groeps niveau, dat *hub wint* of lid van een *WINS-server*kan zijn.

## <a name="how-does-data-sync-work"></a>Hoe werkt gegevens synchronisatie?

- **Wijzigingen in de gegevens bijhouden:** Gegevens synchronisatie houdt wijzigingen bij met behulp van INSERT-, update-en delete-triggers. De wijzigingen worden vastgelegd in een tabel aan de kant van de gebruikers database. Houd er rekening mee dat BULK INSERT triggers standaard niet worden geactiveerd. Als FIRE_TRIGGERS niet is opgegeven, worden er geen invoeg triggers uitgevoerd. Voeg de optie FIRE_TRIGGERS toe zodat de gegevens synchronisatie deze toevoegingen kan bijhouden. 
- **Gegevens synchroniseren:** Gegevens synchronisatie is ontworpen in een hub-en spoke-model. De hub wordt met elk lid afzonderlijk gesynchroniseerd. Wijzigingen van de hub worden gedownload naar het lid en vervolgens worden wijzigingen van het lid geüpload naar de hub.
- **Conflicten oplossen:** Gegevens synchronisatie biedt twee opties voor het oplossen van conflicten, *hub WINS* of *lid van WINS*.
  - Als u *hub WINS*selecteert, worden wijzigingen in het lid altijd overschreven door de wijzigingen in de hub.
  - Als u *lid bent van WINS*, worden wijzigingen in het lid overschreven in de hub. Als er meer dan één lid is, is de uiteindelijke waarde afhankelijk van welk lid het eerst synchroniseert.

## <a name="compare-data-sync-with-transactional-replication"></a>Gegevens synchronisatie met transactionele replicatie vergelijken

| | Gegevens synchroniseren | Transactionele replicatie |
|---|---|---|
| Voordelen | -Actief-actief ondersteuning<br/>-Bi-richting tussen on-premises en Azure SQL Database | -Laagste latentie<br/>-Transactionele consistentie<br/>-Bestaande topologie na migratie opnieuw gebruiken |
| Nadelen | -5 minuten of meer latentie<br/>-Geen transactionele consistentie<br/>-Hogere gevolgen voor de prestaties | -Kan niet publiceren vanuit Azure SQL Database afzonderlijke data base of gegroepeerde Data Base<br/>-Hoge onderhouds kosten |
| | | |

## <a name="get-started-with-sql-data-sync"></a>Aan de slag met SQL Data Sync

### <a name="set-up-data-sync-in-the-azure-portal"></a>Gegevens synchronisatie instellen in de Azure Portal

- [Azure SQL Data Sync instellen](sql-database-get-started-sql-data-sync.md)
- Data Sync-agent: [Data Sync-agent voor Azure SQL Data Sync](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Gegevens synchronisatie met Power shell instellen

- [PowerShell gebruiken om meerdere Azure SQL-databases te synchroniseren](scripts/sql-database-sync-data-between-sql-databases.md)
- [PowerShell gebruiken om te synchroniseren tussen een Azure SQL-database en een on-premises database](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Bekijk de aanbevolen procedures voor gegevens synchronisatie

- [Aanbevolen procedures voor Azure SQL Data Sync](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Er is iets fout gegaan

- [Problemen oplossen met Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Consistentie en prestaties

#### <a name="eventual-consistency"></a>Uiteindelijke consistentie

Omdat de gegevens synchronisatie op basis van een trigger is gebaseerd, is de transactionele consistentie niet gegarandeerd. Micro soft garandeert dat alle wijzigingen uiteindelijk worden doorgevoerd en dat gegevens synchronisatie geen gegevens verlies veroorzaakt.

#### <a name="performance-impact"></a>Invloed op prestaties

Bij de gegevens synchronisatie worden de triggers invoegen, bijwerken en verwijderen gebruikt om wijzigingen bij te houden. Hiermee maakt u tabellen in de gebruikers database voor het bijhouden van wijzigingen. Deze activiteiten voor het bijhouden van wijzigingen hebben gevolgen voor de werk belasting van uw data base. Evalueer uw servicelaag en voer zo nodig een upgrade uit.

Het inrichten en verwijderen van de inrichting tijdens het maken van een synchronisatie groep, update en verwijdering kan ook van invloed zijn op de prestaties van de data base. 

## <a name="sync-req-lim"></a>Vereisten en beperkingen

### <a name="general-requirements"></a>Algemene vereisten

- Elke tabel moet een primaire sleutel hebben. Wijzig de waarde van de primaire sleutel in geen enkele rij. Als u een waarde voor de primaire sleutel moet wijzigen, verwijdert u de rij en maakt u deze opnieuw met de nieuwe waarde voor de primaire sleutel. 

> [!IMPORTANT]
> Als u de waarde van een bestaande primaire sleutel wijzigt, resulteert dit in het volgende probleem:   
>   - De gegevens tussen de hub en het lid kunnen verloren gaan, zelfs al wordt er door synchronisatie geen problemen gerapporteerd.
> - De synchronisatie kan mislukken omdat de tracerings tabel een niet-bestaande rij van de bron bevat, omdat de primaire sleutel is gewijzigd.

- De snapshot-isolatie moet ingeschakeld zijn. Voor meer informatie zie [Snapshot-isolatie in SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Algemene beperkingen

- Een tabel kan geen identiteits kolom hebben die niet de primaire sleutel is.
- Een primaire sleutel kan niet de volgende gegevens typen bevatten: sql_variant, binary, varbinary, Image, XML. 
- Wees voorzichtig wanneer u de volgende gegevens typen als primaire sleutel gebruikt, omdat de ondersteunde precisie alleen geldt voor de tweede: time, datetime, DATETIME2, date time offset.
- De namen van objecten (data bases, tabellen en kolommen) mogen niet de periode van het afdruk bare teken (.), het linker vier Kante haakje ([) of de rechter rechte haak (]) bevatten.
- Azure Active Directory-verificatie wordt niet ondersteund.
- Tabellen met dezelfde naam maar een ander schema (bijvoorbeeld dbo. klanten en Sales. klanten) worden niet ondersteund.
- Kolommen met door de gebruiker gedefinieerde gegevens typen worden niet ondersteund

#### <a name="unsupported-data-types"></a>Niet-ondersteunde gegevens typen

- -
- SQL/CLR UDT
- XMLSchemaCollection (XML ondersteund)
- Cursor, RowVersion, Time Stamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Niet-ondersteunde kolom typen

Met gegevens synchronisatie kunnen alleen-lezen of door het systeem gegenereerde kolommen niet worden gesynchroniseerd. Bijvoorbeeld:

- Berekende kolommen.
- Door het systeem gegenereerde kolommen voor tijdelijke tabellen.

#### <a name="limitations-on-service-and-database-dimensions"></a>Beperkingen voor service-en database dimensies

| **Hoogte**                                                      | **Limiet**              | **Tijdelijke oplossing**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Het maximum aantal synchronisatie groepen waarvan elke Data Base deel kan uitmaken.       | 5                      |                             |
| Maximum aantal eind punten in één synchronisatie groep              | 30                     |                             |
| Het maximum aantal on-premises eind punten in één synchronisatie groep. | 5                      | Meerdere synchronisatie groepen maken |
| Data Base-, tabel-, schema-en kolom namen                       | 50 tekens per naam |                             |
| Tabellen in een synchronisatie groep                                          | 500                    | Meerdere synchronisatie groepen maken |
| Kolommen in een tabel in een synchronisatie groep                              | 1000                   |                             |
| Grootte van gegevensrij in een tabel                                        | 24 MB                  |                             |
| Mini maal synchronisatie-interval                                           | 5 minuten              |                             |
|||
> [!NOTE]
> Er kunnen Maxi maal 30 eind punten in één synchronisatie groep bestaan als er slechts één synchronisatie groep is. Als er meer dan één synchronisatie groep is, kan het totale aantal eind punten voor alle synchronisatie groepen niet meer dan 30 zijn. Als een Data Base deel uitmaakt van meerdere synchronisatie groepen, wordt deze als meerdere eind punten geteld, niet een.

## <a name="faq-about-sql-data-sync"></a>Veelgestelde vragen over SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Hoeveel kost het SQL Data Sync service kosten

Er worden geen kosten in rekening gebracht voor de SQL Data Sync-service zelf.  U boekt echter nog steeds kosten voor gegevens overdracht voor de verplaatsing van gegevens in en uit uw SQL Database-exemplaar. Zie [SQL database prijzen](https://azure.microsoft.com/pricing/details/sql-database/)voor meer informatie.

### <a name="what-regions-support-data-sync"></a>Welke regio's ondersteunen gegevens synchronisatie

SQL Data Sync is in alle regio's beschikbaar.

### <a name="is-a-sql-database-account-required"></a>Is een SQL Database account vereist

Ja. U moet een SQL Database-account hebben om de hub-data base te hosten.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Kan ik Data Sync gebruiken om te synchroniseren tussen SQL Server on-premises data bases

Niet rechtstreeks. U kunt echter indirect synchroniseren tussen SQL Server on-premises data bases, door een hub-data base in azure te maken en vervolgens de on-premises data bases aan de synchronisatie groep toe te voegen.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Kan ik gegevens synchronisatie gebruiken om te synchroniseren tussen SQL-data bases die deel uitmaken van verschillende abonnementen

Ja. U kunt synchroniseren tussen SQL-data bases die deel uitmaken van resource groepen die eigendom zijn van verschillende abonnementen.

- Als de abonnementen deel uitmaken van dezelfde Tenant en u gemachtigd bent voor alle abonnementen, kunt u de synchronisatie groep configureren in de Azure Portal.
- Anders moet u Power shell gebruiken om de synchronisatie leden toe te voegen die deel uitmaken van verschillende abonnementen.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china"></a>Kan ik gegevens synchronisatie gebruiken om te synchroniseren tussen SQL-data bases die deel uitmaken van verschillende Clouds (zoals Azure open bare Cloud en Azure China)

Ja. U kunt synchroniseren tussen SQL-data bases die deel uitmaken van verschillende Clouds. u moet Power shell gebruiken om de synchronisatie leden toe te voegen die deel uitmaken van de verschillende abonnementen.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Kan ik gegevens synchronisatie gebruiken voor het seeden van gegevens van mijn productie database naar een lege data base en deze vervolgens synchroniseren

Ja. Maak het schema hand matig in de nieuwe Data Base door het uit te schrijven op basis van het oorspronkelijke script. Nadat u het schema hebt gemaakt, voegt u de tabellen toe aan een synchronisatie groep om de gegevens te kopiëren en te synchroniseren.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Moet ik SQL Data Sync gebruiken om een back-up te maken van mijn data bases en deze te herstellen

Het is niet raadzaam om SQL Data Sync te gebruiken om een back-up van uw gegevens te maken. U kunt geen back-up-en herstel naar een specifiek tijdstip maken, omdat SQL Data Sync synchronisaties geen versie nummer hebben. Bovendien maakt SQL Data Sync geen back-up van andere SQL-objecten, zoals opgeslagen procedures, en wordt het equivalent van een herstel bewerking niet snel uitgevoerd.

Zie [een Azure-SQL database kopiëren](sql-database-copy.md)voor een aanbevolen back-uptechniek.

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Kan gegevens synchronisatie versleutelde tabellen en kolommen synchroniseren

- Als een Data Base gebruikmaakt van Always Encrypted, kunt u alleen de tabellen en kolommen synchroniseren die *niet* zijn versleuteld. U kunt de versleutelde kolommen niet synchroniseren omdat gegevens synchronisatie de gegevens niet kan ontsleutelen.
- Als een kolom wordt gebruikt op kolomniveau versleuteling (CLE), kunt u de kolom kunt synchroniseren, zolang de rijgrootte van de kleiner dan de maximale grootte van 24 Mb is. Gegevens synchronisatie behandelt de kolom die is versleuteld met de sleutel (CLE) als normale binaire gegevens. Als u de gegevens op andere synchronisatie leden wilt ontsleutelen, moet u hetzelfde certificaat hebben.

### <a name="is-collation-supported-in-sql-data-sync"></a>Wordt gesorteerd ondersteund in SQL Data Sync

Ja. SQL Data Sync sortering ondersteunt in de volgende scenario's:

- Als de geselecteerde synchronisatie schema tabellen zich nog niet in uw hub of lid databases bevinden, worden de bijbehorende tabellen en kolommen automatisch door de service gemaakt met de sorterings instellingen die zijn geselecteerd in de lege doel databases, wanneer u de synchronisatie groep implementeert.
- Als de tabellen die u wilt synchroniseren al bestaan in zowel uw hub-als lid-data bases, SQL Data Sync vereist dat de primaire-sleutel kolommen dezelfde sortering hebben tussen de data bases van de hub en het lid om de synchronisatie groep goed te implementeren. Er zijn geen sorterings beperkingen voor kolommen die geen primaire-sleutel kolommen zijn.

### <a name="is-federation-supported-in-sql-data-sync"></a>Wordt Federatie ondersteund in SQL Data Sync

De Federatie hoofd database kan worden gebruikt in de SQL Data Sync-Service zonder enige beperking. U kunt het federatieve data base-eind punt niet toevoegen aan de huidige versie van SQL Data Sync.

## <a name="next-steps"></a>Volgende stappen

### <a name="update-the-schema-of-a-synced-database"></a>Het schema van een gesynchroniseerde data base bijwerken

Moet u het schema van een data base in een synchronisatie groep bijwerken? Wijzigingen in het schema worden niet automatisch gerepliceerd. Voor sommige oplossingen raadpleegt u de volgende artikelen:

- [De replicatie van schema wijzigingen in Azure SQL Data Sync automatiseren](sql-database-update-sync-schema.md)
- [Power shell gebruiken voor het bijwerken van het synchronisatie schema in een bestaande synchronisatie groep](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Controleren en problemen oplossen

Wordt SQL Data Sync volgens verwachting uitgevoerd? Als u wilt bewaken van activiteit en het oplossen van problemen, Zie de volgende artikelen:

- [Azure-SQL Data Sync controleren met Azure Monitor-logboeken](sql-database-sync-monitor-oms.md)
- [Problemen oplossen met Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Meer informatie over Azure SQL Database

Raadpleeg de volgende artikelen voor meer informatie over SQL Database:

- [Wat is de Azure SQL Database-service?](sql-database-technical-overview.md)
- [Database Lifecycle Management (DLM)](https://msdn.microsoft.com/library/jj907294.aspx)
