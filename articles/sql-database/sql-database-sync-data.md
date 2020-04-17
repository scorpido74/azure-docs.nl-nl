---
title: Gegevens synchroniseren
description: Dit overzicht introduceert Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 08/20/2019
ms.openlocfilehash: 8708c458e1064e4b9ea7dc67f1a4d4fbce1547b0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481954"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Gegevens synchroniseren in meerdere cloud- en on-premises databases met SQL Data Sync

SQL Data Sync is een service die is gebouwd op Azure SQL Database waarmee u de gegevens synchroniseren die u bidirectioneel selecteert in meerdere SQL-databases en SQL Server-exemplaren.

> [!IMPORTANT]
> Azure SQL Data Sync biedt op dit moment geen ondersteuning voor beheerde exemplaren voor Azure SQL Database.

## <a name="when-to-use-data-sync"></a>Wanneer datasync gebruiken

Gegevenssynchronisatie is handig in gevallen waarin gegevens moeten worden bijgewerkt in verschillende Azure SQL-databases of SQL Server-databases. Dit zijn de belangrijkste use cases voor Data Sync:

- **Hybride gegevenssynchronisatie:** Met Data Sync u gegevens gesynchroniseerd houden tussen uw on-premises databases en Azure SQL-databases om hybride toepassingen in te schakelen. Deze mogelijkheid kan een beroep doen op klanten die overwegen over te stappen naar de cloud en een deel van hun toepassing in Azure willen plaatsen.
- **Gedistribueerde toepassingen:** In veel gevallen is het gunstig om verschillende workloads te scheiden in verschillende databases. Als u bijvoorbeeld een grote productiedatabase hebt, maar u ook een rapportage- of analysewerkbelasting op deze gegevens moet uitvoeren, is het handig om een tweede database te hebben voor deze extra workload. Deze aanpak minimaliseert de impact op de prestaties van uw productiewerklast. U Data Sync gebruiken om deze twee databases gesynchroniseerd te houden.
- **Wereldwijd gedistribueerde toepassingen:** Veel bedrijven bestrijken verschillende regio's en zelfs verschillende landen/ regio's. Om de netwerklatentie te minimaliseren, u uw gegevens het beste in een regio bij u in de buurt hebben. Met Data Sync u eenvoudig databases in regio's over de hele wereld gesynchroniseerd houden.

Data sync is niet de voorkeursoplossing voor de volgende scenario's:

| Scenario | Enkele aanbevolen oplossingen |
|----------|----------------------------|
| Herstel na noodgevallen | [Azure georedundante back-ups](sql-database-automated-backups.md) |
| Schaal lezen | [Alleen-lezen replica's gebruiken om alleen-lezen queryworkloads in balans te laden (voorbeeld)](sql-database-read-scale-out.md) |
| ETL (OLTP naar OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) of [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migratie van on-premises SQL Server naar Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Overzicht van SQL Data Sync

Data Sync is gebaseerd op het concept van een Synchronisatiegroep. Een synchronisatiegroep is een groep databases die u wilt synchroniseren.

Data Sync maakt gebruik van een hub en spoke topologie om gegevens te synchroniseren. U definieert een van de databases in de synchronisatiegroep als de Hub-database. De rest van de databases zijn lid databases. Synchronisatie vindt alleen plaats tussen de Hub en individuele leden.

- De **Hub-database** moet een Azure SQL-database zijn.
- De **liddatabases** kunnen SQL-databases, on-premises SQL Server-databases of SQL Server-exemplaren op virtuele Azure-machines zijn.
- De **synchronisatiedatabase** bevat de metagegevens en het logboek voor gegevenssynchronisatie. De synchronisatiedatabase moet een Azure SQL-database zijn die zich in dezelfde regio bevindt als de Hub Database. De synchronisatiedatabase is door de klant gemaakt en eigendom van de klant.

> [!NOTE]
> Als u een on-premises database gebruikt als liddatabase, moet u [een lokale synchronisatieagent installeren en configureren.](sql-database-get-started-sql-data-sync.md#add-on-prem)

![Gegevens tussen databases synchroniseren](media/sql-database-sync-data/sync-data-overview.png)

Een synchronisatiegroep heeft de volgende eigenschappen:

- Het **synchronisatieschema** beschrijft welke gegevens worden gesynchroniseerd.
- De **synchronisatierichting** kan bidirectioneel zijn of slechts in één richting stromen. Dat wil zeggen dat de synchronisatierichting *hub naar lid*of lid van *Hub*of beide kan zijn.
- Het **synchronisatieinterval** beschrijft hoe vaak synchronisatie plaatsvindt.
- Het **beleid voor conflictoplossing** is een groepsbeleid, dat *hubwint* of *lid wint.*

## <a name="how-does-data-sync-work"></a>Hoe werkt Data Sync

- **Gegevenswijzigingen bijhouden:** Data Sync houdt wijzigingen bij met het invoegen, bijwerken en verwijderen van triggers. De wijzigingen worden opgenomen in een bijtabel in de gebruikersdatabase. Houd er rekening mee dat BULK INSERT standaard geen triggers afvuurt. Als FIRE_TRIGGERS niet is opgegeven, worden er geen invoegtriggers uitgevoerd. Voeg de FIRE_TRIGGERS optie toe zodat Data Sync deze inserts kan bijhouden. 
- **Gegevens synchroniseren:** Data Sync is ontworpen in een Hub en Spoke-model. De Hub synchroniseert met elk lid afzonderlijk. Wijzigingen van de Hub worden gedownload naar het lid en vervolgens worden wijzigingen van het lid geüpload naar de Hub.
- **Conflicten oplossen:** Data Sync biedt twee opties voor conflictoplossing, *Hub wint* of *Lid wint*.
  - Als u *Hub wint,* overschrijven de wijzigingen in de hub altijd wijzigingen in het lid.
  - Als u *Lid wint,* overschrijven de wijzigingen in het lid wijzigingen in de hub. Als er meer dan één lid is, is de uiteindelijke waarde afhankelijk van welk lid het eerst synchroniseert.

## <a name="compare-data-sync-with-transactional-replication"></a>Gegevenssynchronisatie vergelijken met transactionele replicatie

| | Gegevens synchroniseren | Transactionele replicatie |
|---|---|---|
| Voordelen | - Actieve ondersteuning<br/>- Bi-directioneel tussen on-premises en Azure SQL-database | - Lagere latentie<br/>- Transactionele consistentie<br/>- Bestaande topologie hergebruiken na migratie |
| Nadelen | - 5 min of meer latentie<br/>- Geen transactionele consistentie<br/>- Hogere impact op de prestaties | - Kan niet publiceren vanuit Azure SQL Database enkele database of gepoolde database<br/>- Hoge onderhoudskosten |

## <a name="get-started-with-sql-data-sync"></a>Aan de slag met SQL Data Sync

### <a name="set-up-data-sync-in-the-azure-portal"></a>Gegevenssynchronisatie instellen in de Azure-portal

- [Azure SQL Data Sync instellen](sql-database-get-started-sql-data-sync.md)
- Data Sync-agent: [Data Sync-agent voor Azure SQL Data Sync](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Gegevenssynchronisatie instellen met PowerShell

- [PowerShell gebruiken om meerdere Azure SQL-databases te synchroniseren](scripts/sql-database-sync-data-between-sql-databases.md)
- [PowerShell gebruiken om te synchroniseren tussen een Azure SQL-database en een on-premises database](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Bekijk de aanbevolen procedures voor Gegevenssynchronisatie

- [Aanbevolen procedures voor Azure SQL Data Sync](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Is er iets misgegaan?

- [Problemen oplossen met Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Consistentie en prestaties

### <a name="eventual-consistency"></a>Uiteindelijke consistentie

Aangezien Data Sync op triggerbasis is gebaseerd, is transactionele consistentie niet gegarandeerd. Microsoft garandeert dat alle wijzigingen uiteindelijk worden doorgevoerd en dat Data Sync geen gegevensverlies veroorzaakt.

### <a name="performance-impact"></a>Impact op prestaties

Data Sync maakt gebruik van invoegen, bijwerken en verwijderen van triggers om wijzigingen bij te houden. Het maakt bijtabellen in de gebruikersdatabase voor het bijhouden van wijzigingen. Deze activiteiten voor het bijhouden van wijzigingen hebben een impact op de werkbelasting van uw database. Beoordeel uw servicelaag en upgrade indien nodig.

Het inrichten en deprovisioneren tijdens het maken, bijwerken en verwijderen van synchronisatiegroepen kan ook van invloed zijn op de databaseprestaties.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a>Eisen en beperkingen

### <a name="general-requirements"></a>Algemene vereisten

- Elke tabel moet een primaire sleutel hebben. Wijzig de waarde van de primaire sleutel in een rij niet. Als u een primaire sleutelwaarde moet wijzigen, verwijdert u de rij en maakt u deze opnieuw met de nieuwe primaire sleutelwaarde.

> [!IMPORTANT]
> Als u de waarde van een bestaande primaire sleutel wijzigt, wordt het volgende foute gedrag weergegeven:
> - Gegevens tussen hub en lid kunnen verloren gaan, ook al wordt er geen probleem gemeld voor synchronisatie.
> - Synchronisatie kan mislukken omdat de trackingtabel een niet-bestaande rij van bron heeft vanwege de primaire sleutelwijziging.

- De snapshot-isolatie moet ingeschakeld zijn. Voor meer informatie zie [Snapshot-isolatie in SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Algemene beperkingen

- Een tabel kan geen identiteitskolom hebben die niet de primaire sleutel is.
- Een primaire sleutel kan niet de volgende gegevenstypen hebben: sql_variant, binair, varbinary, image, xml.
- Wees voorzichtig wanneer u de volgende gegevenstypen als primaire sleutel gebruikt, omdat de ondersteunde precisie slechts op de tweede is: tijd, datumtijd, datetime2, datumtijdverschuiving.
- De namen van objecten (databases, tabellen en kolommen) mogen de afdrukbare tekensperiode (.), linker vierkante haakjes ([) of rechter vierkante haakjes (]) niet bevatten.
- Azure Active Directory-verificatie wordt niet ondersteund.
- Tabellen met dezelfde naam, maar verschillende schema 's (bijvoorbeeld dbo.customers en sales.customers) worden niet ondersteund.
- Kolommen met door gebruiker gedefinieerde gegevenstypen worden niet ondersteund
- Het verplaatsen van servers tussen verschillende abonnementen wordt niet ondersteund. 

#### <a name="unsupported-data-types"></a>Niet-ondersteunde gegevenstypen

- Filestream
- SQL/CLR UDT
- XMLSchemaCollection (XML ondersteund)
- Cursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Niet-ondersteunde kolomtypen

Data Sync kan alleen-lezen of door het systeem gegenereerde kolommen niet synchroniseren. Bijvoorbeeld:

- Berekende kolommen.
- Door het systeem gegenereerde kolommen voor tijdelijke tabellen.

#### <a name="limitations-on-service-and-database-dimensions"></a>Beperkingen voor service- en databasedimensies

| **Dimensies**                                                  | **Limiet**              | **Oplossing**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximaal aantal synchronisatiegroepen waartoe elke database kan behoren.       | 5                      |                             |
| Maximum aantal eindpunten in één synchronisatiegroep              | 30                     |                             |
| Maximaal aantal on-premises eindpunten in één synchronisatiegroep. | 5                      | Meerdere synchronisatiegroepen maken |
| Database-, tabel-, schema- en kolomnamen                       | 50 tekens per naam |                             |
| Tabellen in een synchronisatiegroep                                          | 500                    | Meerdere synchronisatiegroepen maken |
| Kolommen in een tabel in een synchronisatiegroep                              | 1000                   |                             |
| Grootte van de gegevensrij op een tabel                                        | 24 mb                  |                             |
| Minimaal synchronisatie-interval                                           | 5 minuten              |                             |

> [!NOTE]
> Er kunnen maximaal 30 eindpunten in één synchronisatiegroep zijn als er slechts één synchronisatiegroep is. Als er meer dan één synchronisatiegroep is, mag het totale aantal eindpunten in alle synchronisatiegroepen niet hoger zijn dan 30. Als een database tot meerdere synchronisatiegroepen behoort, wordt deze geteld als meerdere eindpunten, niet als één.

## <a name="faq-about-sql-data-sync"></a>Veelgestelde vragen over SQL-gegevenssynchronisatie

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Hoeveel kost de SQL Data Sync-service

Er zijn geen kosten verbonden aan de SQL Data Sync-service zelf. U verzamelt echter nog steeds kosten voor gegevensoverdracht voor gegevensverplaatsing in en uit uw SQL Database-instantie. Zie [SQL Database-prijzen](https://azure.microsoft.com/pricing/details/sql-database/)voor meer informatie .

### <a name="what-regions-support-data-sync"></a>Welke regio's ondersteunen Data Sync

SQL Data Sync is beschikbaar in alle regio's.

### <a name="is-a-sql-database-account-required"></a>Is een SQL Database-account vereist

Ja. U moet een SQL Database-account hebben om de Hub Database te hosten.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Kan ik Data Sync gebruiken om alleen te synchroniseren tussen sql server on-premises databases

Niet direct. U echter indirect synchroniseren tussen sql server-on-premises databases door een Hub-database in Azure te maken en vervolgens de on-premises databases toe te voegen aan de synchronisatiegroep.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Kan ik Data Sync gebruiken om te synchroniseren tussen SQL-databases die tot verschillende abonnementen behoren

Ja. U synchroniseren tussen SQL-databases die behoren tot resourcegroepen die eigendom zijn van verschillende abonnementen.

- Als de abonnementen tot dezelfde tenant behoren en u toestemming hebt voor alle abonnementen, u de synchronisatiegroep configureren in de Azure-portal.
- Anders moet u PowerShell gebruiken om de synchronisatieleden toe te voegen die tot verschillende abonnementen behoren.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Kan ik Data Sync gebruiken om te synchroniseren tussen SQL-databases die tot verschillende clouds behoren (zoals Azure Public Cloud en Azure China 21Vianet)

Ja. U synchroniseren tussen SQL-databases die tot verschillende clouds behoren, u moet PowerShell gebruiken om de synchronisatieleden toe te voegen die tot de verschillende abonnementen behoren.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Kan ik Data Sync gebruiken om gegevens uit mijn productiedatabase te zaaien naar een lege database en deze vervolgens te synchroniseren

Ja. Maak het schema handmatig in de nieuwe database door het te scripten vanuit het origineel. Nadat u het schema hebt gemaakt, voegt u de tabellen toe aan een synchronisatiegroep om de gegevens te kopiëren en gesynchroniseerd te houden.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Moet ik SQL Data Sync gebruiken om een back-up te maken en mijn databases te herstellen

Het wordt afgeraden om SQL Data Sync te gebruiken om een back-up van uw gegevens te maken. U geen back-upmaken en herstellen naar een specifiek tijdstip omdat SQL Data Sync-synchronisaties niet zijn uitgevoerd. Bovendien maakt SQL Data Sync geen back-up van andere SQL-objecten, zoals opgeslagen procedures, en doet het niet snel het equivalent van een herstelbewerking.

Zie [Een Azure SQL-database kopiëren](sql-database-copy.md)voor één aanbevolen back-uptechniek.

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Kan versleutelde tabellen en kolommen voor gegevenssynchronisatie synchroniseren

- Als een database Always Encrypted gebruikt, u alleen de tabellen en kolommen synchroniseren die *niet* zijn versleuteld. U de versleutelde kolommen niet synchroniseren, omdat Data Sync de gegevens niet kan decoderen.
- Als een kolom cle (Column-Level Encryption) gebruikt, u de kolom synchroniseren, zolang de rijgrootte kleiner is dan de maximale grootte van 24 Mb. Data Sync behandelt de kolom versleuteld met sleutel (CLE) als normale binaire gegevens. Als u de gegevens voor andere synchronisatieleden wilt decoderen, moet u hetzelfde certificaat hebben.

### <a name="is-collation-supported-in-sql-data-sync"></a>Wordt collatie ondersteund in SQL Data Sync

Ja. SQL Data Sync ondersteunt collatie in de volgende scenario's:

- Als de geselecteerde synchronisatieschematabellen zich nog niet in uw hub- of liddatabases bevinden, maakt de service, wanneer u de synchronisatiegroep implementeert, automatisch de bijbehorende tabellen en kolommen met de collatie-instellingen die zijn geselecteerd in de lege doeldatabases.
- Als de tabellen die moeten worden gesynchroniseerd al bestaan in zowel uw hub- als liddatabases, vereist SQL Data Sync dat de primaire sleutelkolommen dezelfde verzameling hebben tussen hub- en liddatabases om de synchronisatiegroep succesvol te implementeren. Er zijn geen collatiebeperkingen voor andere kolommen dan de primaire sleutelkolommen.

### <a name="is-federation-supported-in-sql-data-sync"></a>Wordt federatie ondersteund in SQL Data Sync

Federation Root Database kan zonder enige beperking worden gebruikt in de SQL Data Sync Service. U het eindpunt federated database niet toevoegen aan de huidige versie van SQL Data Sync.

## <a name="next-steps"></a>Volgende stappen

### <a name="update-the-schema-of-a-synced-database"></a>Het schema van een gesynchroniseerde database bijwerken

Moet u het schema van een database bijwerken in een synchronisatiegroep? Schemawijzigingen worden niet automatisch gerepliceerd. Zie voor sommige oplossingen de volgende artikelen:

- [De replicatie van schemawijzigingen in Azure SQL Data Sync automatiseren](sql-database-update-sync-schema.md)
- [PowerShell gebruiken voor het bijwerken van het synchronisatieschema in een bestaande synchronisatiegroep](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Controleren en problemen oplossen

Doet SQL Data Sync het zoals verwacht? Zie de volgende artikelen om activiteiten te controleren en problemen op te lossen:

- [Azure SQL Data Sync met Azure Monitor-logboeken bewaken](sql-database-sync-monitor-oms.md)
- [Problemen oplossen met Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Meer informatie over Azure SQL Database

Zie de volgende artikelen voor meer informatie over SQL Database:

- [Wat is de Azure SQL Database-service?](sql-database-technical-overview.md)
- [Database Lifecycle Management (DLM)](https://msdn.microsoft.com/library/jj907294.aspx)
