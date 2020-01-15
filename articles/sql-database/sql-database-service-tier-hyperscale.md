---
title: Overzicht van Azure SQL Database grootschalige | Microsoft Docs
description: In dit artikel wordt de grootschalige-servicelaag in het op vCore gebaseerde aankoop model beschreven in Azure SQL Database en wordt uitgelegd hoe dit verschilt van de Algemeen en Bedrijfskritiek service lagen.
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
ms.openlocfilehash: 9cce221946a16103e706875e179c677190f32af1
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940812"
---
# <a name="hyperscale-service-tier"></a>Hyperscale-servicelaag

Azure SQL Database is gebaseerd op SQL Server data base engine-architectuur die is aangepast voor de cloud omgeving om 99,99% Beschik baarheid te garanderen, zelfs in het geval van infrastructuur fouten. Er zijn drie architectuur modellen die worden gebruikt in Azure SQL Database:
- Algemeen/standaard 
-  Hyperscale
-  Business Critical/Premium

De grootschalige van de servicelaag in Azure SQL Database is de nieuwste servicelaag in het op vCore gebaseerde aankoop model. Deze servicelaag is een zeer schaal bare laag voor opslag en reken prestaties die gebruikmaakt van de Azure-architectuur voor het uitschalen van de opslag en de reken resources voor een Azure SQL Database die groter zijn dan de limieten die beschikbaar zijn voor de Algemeen en de onderneming Kritieke service lagen.

> 
> [!NOTE]
> Zie [Algemeen](sql-database-service-tier-general-purpose.md) en [bedrijfskritiek](sql-database-service-tier-business-critical.md) service lagen voor meer informatie over de service lagen algemeen en bedrijfskritiek in het op vCore gebaseerde aankoop model. Zie [Azure SQL database-inkoop modellen en-resources](sql-database-service-tiers.md)voor een vergelijking van het op vCore gebaseerde aankoop model met het DTU-gebaseerde aankoop model.


## <a name="what-are-the-hyperscale-capabilities"></a>Wat zijn de grootschalige-mogelijkheden?

De grootschalige van de service tier in Azure SQL Database biedt de volgende aanvullende mogelijkheden:

- Ondersteuning voor Maxi maal 100 TB aan database grootte
- Bijna momentane database back-ups (op basis van moment opnamen van bestanden die zijn opgeslagen in Azure Blob Storage), ongeacht de grootte zonder IO-impact op reken bronnen  
- Snel terugzetten van de data base (op basis van moment opnamen van bestanden) in minuten in plaats van uren of dagen (geen grootte van de gegevens bewerking)
- Betere prestaties als gevolg van een betere logboek doorvoer en snellere doorvoer tijden voor trans acties, ongeacht gegevens volumes
- Snel uitschalen: u kunt een of meer alleen-lezen knoop punten inrichten voor het offloaden van uw Lees werk belasting en voor gebruik als hot stand-by
- Snel omhoog schalen: u kunt in een constant tijdstip uw reken resources zo schalen dat er zware werk belastingen worden uitgevoerd als en wanneer dat nodig is, en vervolgens de reken bronnen weer lager instellen wanneer ze niet nodig zijn.

De grootschalige-service laag verwijdert veel van de praktische limieten die traditioneel in Cloud databases worden weer gegeven. Wanneer de meeste andere data bases worden beperkt door de resources die beschikbaar zijn in één knoop punt, hebben de data bases in de grootschalige-servicelaag geen limieten. Met de flexibele opslag architectuur neemt opslag toe als dat nodig is. In werkelijkheid worden grootschalige-data bases niet gemaakt met een gedefinieerde maximale grootte. Een grootschalige-data base groeit zo nodig en u wordt alleen gefactureerd voor de capaciteit die u gebruikt. Voor lees-intensieve workloads biedt de grootschalige-servicelaag snel uitschalen door extra Lees replica's in te richten als dat nodig is voor het offloaden van Lees werkbelastingen.

Daarnaast is de tijd die nodig is voor het maken van database back-ups of om omhoog of omlaag te schalen niet meer gekoppeld aan het volume van de gegevens in de data base. U kunt vrijwel onmiddellijk back-ups maken van grootschalige-data bases. U kunt in slechts enkele minuten een Data Base schalen in tien tallen of meer. Met deze functie kunt u zich zorgen maken over de mogelijkheden van de eerste configuratie opties.

Zie [kenmerken](sql-database-service-tiers-vcore.md#service-tiers)van de grootschalige voor meer informatie over de berekenings grootten voor de servicelaag.

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Wie moet rekening houden met de grootschalige-servicelaag

De grootschalige is bedoeld voor de meeste bedrijfs werkbelastingen, omdat deze zeer flexibel en hoge prestaties biedt met onafhankelijk schaal bare reken-en opslag resources. Met de mogelijkheid om opslag tot 100 TB automatisch te schalen, is het een uitstekende keuze voor klanten die:

- Een grote data base on-premises hebben en hun toepassingen willen moderniseren door over te stappen op de Cloud
- Bevinden zich al in de Cloud en worden beperkt door de maximale database grootte beperkingen van andere service lagen (1-4 TB)
- Hebben kleinere data bases, maar vereisen snelle verticale en horizontale reken schalen, hoge prestaties, directe back-ups en snelle database herstel.

De grootschalige-servicelaag ondersteunt een breed scala aan SQL Server workloads, van zuivere OLTP tot zuivere analyses, maar is voornamelijk geoptimaliseerd voor OLTP-en Hybrid Trans Action-en Analytical Processing-workloads (HTAP).

> [!IMPORTANT]
> Elastische Pools bieden geen ondersteuning voor de grootschalige-servicelaag.

## <a name="hyperscale-pricing-model"></a>Grootschalige-prijs model

Grootschalige is alleen beschikbaar in het [vCore-model](sql-database-service-tiers-vcore.md). Als u wilt uitlijnen met de nieuwe architectuur, is het prijs model iets anders dan Algemeen of Bedrijfskritiek service lagen:

- **Compute**:

  De grootschalige reken eenheids prijs is per replica. De [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) prijs wordt toegepast om automatisch schaal replica's te lezen. Er wordt standaard een primaire replica en één alleen-lezen replica per grootschalige-data base gemaakt.  Gebruikers kunnen het totaal aantal replica's aanpassen, met inbegrip van de primaire van 1-5.

- **Opslag**:

  U hoeft niet de maximale gegevens grootte op te geven bij het configureren van een grootschalige-data base. In de laag Hyperscale wordt opslag voor uw database in rekening gebracht op basis van daadwerkelijk gebruik. Opslag wordt automatisch toegewezen tussen 10 GB en 100 TB, in stappen die dynamisch worden aangepast tussen 10 GB en 40 GB.  

Zie [Azure SQL database prijzen](https://azure.microsoft.com/pricing/details/sql-database/single/) voor meer informatie over prijzen voor grootschalige

## <a name="distributed-functions-architecture"></a>Architectuur van gedistribueerde functies

In tegens telling tot traditionele data base-engines die alle gegevens beheer functies op één locatie/proces hebben gecentraliseerd (ook wel gedistribueerde data bases in productie hebben, hebben meerdere exemplaren van een monolithische-gegevens engine), is een grootschalige-data base gescheiden van de engine voor de verwerking van query's, waarbij de semantiek van verschillende gegevens engines afwijkt, van de onderdelen die opslag Op deze manier kan de opslag capaciteit zo ver mogelijk naar behoefte worden geschaald (het eerste doel is 100 TB). Alleen-lezen replica's delen dezelfde opslag onderdelen, zodat er geen gegevens kopie vereist is om een nieuwe Lees bare replica te maken. 

In het volgende diagram ziet u de verschillende typen knoop punten in een grootschalige-Data Base:

![architectuur](./media/sql-database-hyperscale/hyperscale-architecture.png)

Een grootschalige-Data Base bevat de volgende verschillende soorten onderdelen:

### <a name="compute"></a>Computing

Het reken knooppunt is de locatie waar de relationele engine zich bevindt, zodat alle taal elementen, query's worden verwerkt, enzovoort. Alle gebruikers interacties met een grootschalige-data base vinden plaats via deze reken knooppunten. Reken knooppunten hebben op SSD gebaseerde caches in het voor gaande diagram om het aantal netwerk round trips dat nodig is om een pagina met gegevens op te halen, zo klein mogelijk te houden. Er is één primair reken knooppunt waar alle werk belastingen en trans acties voor lezen/schrijven worden verwerkt. Er zijn een of meer secundaire reken knooppunten die fungeren als hot standby-knoop punten voor failover-doel einden, en fungeren als alleen-lezen Compute-knoop punten voor het offloaden van Lees werkbelastingen (als deze functionaliteit gewenst is).

### <a name="page-server"></a>Pagina Server

Pagina servers zijn systemen die een scale-out opslag engine vertegenwoordigen.  Elke pagina Server is verantwoordelijk voor een subset van de pagina's in de data base.  Ten opzichte van elke pagina server bepaalt de besturings elementen van 128 GB en 1 TB aan gegevens. Er worden geen gegevens gedeeld op meer dan één pagina Server (buiten replica's die worden bewaard voor redundantie en beschik baarheid). De taak van een pagina Server is het uitvoeren van database pagina's naar de reken knooppunten op aanvraag en om te voor komen dat de pagina's worden bijgewerkt als update gegevens van trans acties. Pagina servers worden bijgewerkt door logboek records van de logboek service af te spelen. Pagina servers onderhouden ook caches op basis van SSD om de prestaties te verbeteren. Lange termijn opslag van gegevens pagina's wordt opgeslagen in Azure Storage voor extra betrouw baarheid.

### <a name="log-service"></a>Logboek service

De logboek service accepteert logboek records van de primaire Compute-replica, slaat ze op in een duurzame cache en stuurt de logboek records door naar de rest van reken replica's (zodat ze hun caches kunnen bijwerken) en de relevante pagina ('s), zodat de gegevens kunnen worden bijgewerkt kent. Op deze manier worden alle gegevens wijzigingen van de primaire Compute-replica door de logboek service door gegeven aan alle secundaire reken replica's en pagina servers. Ten slotte worden de logboek records gepusht naar lange termijn opslag in Azure Storage. Dit is een bijna oneindige opslag opslagplaats. Dit mechanisme verwijdert de nood zaak van een veelvuldige afkap ping van het logboek. De logboek service heeft ook lokale cache om de toegang tot logboek records te versnellen.

### <a name="azure-storage"></a>Azure-opslag

Azure Storage bevat alle gegevens bestanden in een Data Base. Pagina servers houden gegevens bestanden in Azure Storage up-to-date. Deze opslag wordt gebruikt voor back-updoeleinden en voor replicatie tussen Azure-regio's. Back-ups worden geïmplementeerd met behulp van opslag momentopnamen van gegevens bestanden. Herstel bewerkingen met behulp van moment opnamen zijn snel, ongeacht de grootte van de gegevens. Gegevens kunnen worden hersteld naar elk gewenst moment binnen de Bewaar periode van de back-up van de data base.

## <a name="backup-and-restore"></a>Back-up maken en terugzetten

Back-ups zijn gebaseerd op bestands momentopnamen en daarom zijn ze bijna direct. Met opslag-en Compute-schei ding kunt u de bewerking voor back-up/herstel naar de opslaglaag beperken om de verwerkings belasting van de primaire Compute replica te verminderen. Als gevolg hiervan is het maken van een back-up van de data base geen invloed op de prestaties van het primaire reken knooppunt. op dezelfde manier worden herstel bewerkingen uitgevoerd door terug te keren naar moment opnamen van bestanden. Dit is geen grootte van de gegevens bewerking. Herstellen is een constante tijd, en zelfs meerdere terabyte-data bases kunnen binnen enkele minuten worden hersteld in plaats van uren of dagen. Bij het maken van nieuwe data bases door een bestaande back-up te herstellen, wordt ook gebruikgemaakt van deze functie: het maken van database kopieën voor ontwikkelings-of test doeleinden, zelfs op terabyte-formaat databases, is doable binnen enkele minuten.

## <a name="scale-and-performance-advantages"></a>Schaal-en prestatie voordelen

Met de mogelijkheid om snel extra alleen-lezen Compute-knoop punten in te zetten/te verlagen, biedt de grootschalige-architectuur aanzienlijke mogelijkheden voor lees schaal en kan het primaire reken knooppunt worden vrijgemaakt voor het uitvoeren van meer schrijf aanvragen. Ook kunnen de reken knooppunten snel omhoog of omlaag worden geschaald als gevolg van de architectuur van de grootschalige-architectuur voor gedeeld opslag.

## <a name="create-a-hyperscale-database"></a>Een grootschalige-data base maken

Een grootschalige-data base kan worden gemaakt met behulp van de [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [Power shell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) of [cli](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Grootschalige-data bases zijn alleen beschikbaar via het [op vCore gebaseerde aankoop model](sql-database-service-tiers-vcore.md).

Met de volgende T-SQL-opdracht maakt u een grootschalige-data base. U moet zowel de editie als de service doelstelling opgeven in de instructie `CREATE DATABASE`. Raadpleeg de [resource limieten](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen4) voor een lijst met geldige service doelstellingen.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Hiermee maakt u een grootschalige-Data Base op GEN5-hardware met 4 kern geheugens.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Een bestaande Azure SQL Database migreren naar de servicelaag grootschalige

U kunt uw bestaande Azure SQL-data bases verplaatsen naar grootschalige met behulp van de [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [Power shell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) of [cli](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). Op dit moment is dit een eenrichtings migratie. U kunt geen data bases van grootschalige naar een andere servicelaag verplaatsen, met uitzonde ring van gegevens exporteren en importeren. Voor testen van concept (POCs) wordt u aangeraden een kopie te maken van uw productie databases en de kopie te migreren naar grootschalige. Het migreren van een bestaande Azure-SQL database naar de grootschalige-laag is een grootte van de gegevens bewerking.

Met de volgende T-SQL-opdracht wordt een Data Base verplaatst naar de servicelaag grootschalige. U moet zowel de editie als de service doelstelling opgeven in de instructie `ALTER DATABASE`.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Verbinding maken met een lees bare replica van een grootschalige-data base

In grootschalige-data bases wordt met het argument `ApplicationIntent` in de connection string dat door de client wordt gegeven, bepaald of de verbinding wordt doorgestuurd naar de schrijf replica of naar een secundaire alleen-lezen replica. Als de `ApplicationIntent` ingesteld op `READONLY` en de data base heeft geen secundaire replica, wordt de verbinding doorgestuurd naar de primaire replica en wordt de standaard instelling `ReadWrite` gedrag.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Grootschalige secundaire replica's zijn allemaal identiek, met dezelfde serviceniveau doelstelling als de primaire replica. Als er meer dan één secundaire replica aanwezig is, wordt de werk belasting gedistribueerd over alle beschik bare zones. Elke secundaire replica wordt onafhankelijk bijgewerkt, waardoor verschillende replica's een andere gegevens latentie kunnen hebben ten opzichte van de primaire replica.

## <a name="database-high-availability-in-hyperscale"></a>Hoge Beschik baarheid van data base in grootschalige

Net als bij alle andere service lagen garandeert grootschalige gegevens duurzaamheid voor vastgelegde trans acties, ongeacht de beschik baarheid van reken replica's. De mate van uitval tijd, omdat de primaire replica niet beschikbaar is, is afhankelijk van het type failover (gepland versus niet-gepland) en op de aanwezigheid van ten minste één secundaire replica. In een geplande failover (d.w.z. een onderhouds gebeurtenis) maakt het systeem de nieuwe primaire replica voordat een failover wordt gestart, of wordt een bestaande secundaire replica als failover-doel gebruikt. In een niet-geplande failover (d.w.z. een hardwarestoring op de primaire replica), gebruikt het systeem een secundaire replica als failover-doel als er een bestaat, of maakt een nieuwe primaire replica van de groep beschik bare reken capaciteit. In het laatste geval is de duur van de downtime langer vanwege extra stappen die nodig zijn om de nieuwe primaire replica te maken.

Zie [Sla voor Azure SQL database](https://azure.microsoft.com/support/legal/sla/sql-database/)voor grootschalige sla.

## <a name="disaster-recovery-for-hyperscale-databases"></a>Herstel na nood geval voor grootschalige-data bases

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Een grootschalige-data base herstellen naar een andere Geografie
Als u een Azure SQL Database grootschalige-Data Base wilt herstellen naar een andere regio dan die die momenteel wordt gehost in, als onderdeel van een nood herstel bewerking of een analyse, herlocatie of een andere reden, is de primaire methode een geografisch herstel van de data base.  Dit omvat precies dezelfde stappen als wat u zou gebruiken om andere AZURE SQL-Data Base naar een andere regio te herstellen:
1. Maak een SQL Database Server in de doel regio als u daar nog geen geschikte server op hebt.  Deze server moet eigendom zijn van hetzelfde abonnement als de oorspronkelijke server (bron).
2. Volg de instructies in het onderwerp [geo-Restore](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) van de pagina voor het herstellen van Azure SQL-data bases vanuit automatische back-ups.

> [!NOTE]
> Omdat de bron en het doel zich in verschillende regio's bevinden, kan de data base geen momentopname opslag delen met de bron database als in niet-geografische herstel bewerkingen, die zeer snel zijn voltooid.  In het geval van een geo-Restore van een grootschalige-data base is het een grootte-of-Data-bewerking, zelfs als het doel zich in het gekoppelde gebied van de geo-gerepliceerde opslag bevindt.  Dit betekent dat het uitvoeren van een geo-herstel tijd evenredig is met de grootte van de data base die wordt hersteld.  Als het doel zich in het gekoppelde gebied bevindt, wordt de kopie binnen een Data Center genoteerd dat aanzienlijk sneller is dan een lange afstands kopie via internet, maar worden wel alle bits gekopieerd.

## <a name=regions></a>Beschik bare regio's

De laag Azure SQL Database grootschalige is momenteel beschikbaar in de volgende regio's:

- Australië Oost
- Australië Zuidoost
- Brazilië - Zuid
- Canada-Midden
- VS - centraal
- China - oost 2
- China - noord 2
- Azië - oost
- VS - oost
- VS-Oost 2
- Frankrijk - centraal
- Japan - Oost
- Japan - West
- Korea - centraal
- Korea (Zuid)
- VS - noord-centraal
- Europa - noord
- Zuid-Afrika - noord
- VS - zuid-centraal
- Azië - zuidoost
- VK - zuid
- VK - west
- Europa - west
- VS - west
- VS - west 2

Als u een grootschalige-Data Base wilt maken in een regio die niet wordt weer gegeven als ondersteund, kunt u een aanvraag voor onboarding verzenden via Azure Portal. U kunt de lijst met ondersteunde regio's uitbreiden. Controleer vervolgens de meest recente regio lijst.

De mogelijkheid om grootschalige-data bases te maken in niet-vermelde regio's aanvragen:

1. Ga naar de [Blade Help en ondersteuning van Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

2. Klik op [ **nieuwe ondersteunings aanvraag**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

    ![De Blade Help en ondersteuning van Azure](media/sql-database-service-tier-hyperscale/request-screen-1.png)

3. Selecteer voor **probleem type** **service-en abonnements limieten (quota's)**

4. Kies het abonnement dat u wilt gebruiken voor het maken van de data base (s)

5. Selecteer **SQL database** bij **quotum type**

6. Klik op **volgende: oplossingen**

1. Klik op **Details opgeven**

    ![Details van probleem](media/sql-database-service-tier-hyperscale/request-screen-2.png)

8. **SQL database quotum type**kiezen: **andere quotum aanvraag**

9. Vul de volgende sjabloon in:

    ![Details van quotum](media/sql-database-service-tier-hyperscale/request-screen-3.png)

    Geef de volgende informatie op in de sjabloon

    > Aanvraag voor het maken van Azure grootschalige SQL Database in een nieuwe regio<br/> Regio: [vul uw aangevraagde regio in]  <br/>
    > Berekenen van SKU/totaal aantal kern geheugens inclusief Lees bare replica's <br/>
    > Aantal TB geschat 
    >

10. Kies **Ernst C**

11. Kies de juiste contact wijze en vul de details in.

12. Klik op **Opslaan** en **door gaan**

## <a name="known-limitations"></a>Bekende beperkingen
Dit zijn de huidige beperkingen voor het grootschalige van de service tier.  We werken actief om zoveel mogelijk van deze beperkingen te verwijderen.

| Probleem | Beschrijving |
| :---- | :--------- |
| In het deel venster Back-ups beheren voor een logische server worden geen grootschalige-data bases weer gegeven die worden gefilterd van SQL Server  | Grootschalige heeft een afzonderlijke methode voor het beheren van back-ups, en omdat de Bewaar periode voor lange termijn retentie en het tijdstip voor het bewaren van back-ups niet van toepassing zijn/zijn ongeldig. Daarom worden grootschalige-data bases niet weer gegeven in het deel venster back-up beheren. |
| Herstel naar een bepaald tijdstip | Wanneer een Data Base wordt gemigreerd naar de grootschalige, wordt het herstellen naar een bepaald tijdstip vóór de migratie niet ondersteund.|
| Herstellen van niet-grootschalige DB naar grootschalige en vice versa | U kunt een grootschalige-data base niet herstellen in een niet-grootschalige-data base, en u kunt ook een niet-grootschalige-data base herstellen in een grootschalige-data base.|
| Als een Data Base een of meer gegevens bestanden heeft die groter zijn dan 1 TB, mislukt de migratie | In sommige gevallen is het mogelijk om dit probleem te omzeilen door de grote bestanden kleiner te maken dan 1 TB. Als u een Data Base migreert die tijdens het migratie proces wordt gebruikt, moet u ervoor zorgen dat er geen bestanden groter zijn dan 1 TB. Gebruik de volgende query om de grootte van database bestanden te bepalen. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Managed Instance | Azure SQL Database beheerde instantie wordt momenteel niet ondersteund met grootschalige-data bases. |
| Elastische pools |  Elastische Pools worden momenteel niet ondersteund met SQL Database grootschalige.|
| Migratie naar grootschalige is momenteel een eenrichtings bewerking | Wanneer een Data Base wordt gemigreerd naar grootschalige, kan deze niet rechtstreeks naar een andere servicelaag worden gemigreerd. Op dit moment is de enige manier om een Data Base te migreren van grootschalige naar een niet-grootschalige, het exporteren/importeren met behulp van een BACPAC-bestand of andere technologieën voor gegevens verplaatsing (Bulk Copy, Azure Data Factory, Azure Databricks, SSIS, enzovoort).|
| Migratie van data bases met persistente in-Memory objecten | Grootschalige ondersteunt alleen niet-permanente objecten in het geheugen (tabel typen, systeem eigen SPs en functies).  Permanente in-Memory tabellen en andere objecten moeten worden verwijderd en opnieuw worden gemaakt als objecten die geen deel uitmaken van het geheugen voordat een Data Base naar de service tier grootschalige wordt gemigreerd.|
| Tracering wijzigen | Wijzigingen bijhouden is momenteel beschikbaar als open bare preview en kan worden ingeschakeld voor nieuwe of bestaande grootschalige-data bases. |
| Geo-replicatie  | U kunt geo-replicatie voor Azure SQL Database grootschalige nog niet configureren. |
| Data base kopiëren | U kunt geen database kopie gebruiken om een nieuwe data base in Azure SQL grootschalige te maken. |
| TDE/Azure-integratie | Transparante database versleuteling met behulp van Azure Key Vault (ook wel uw eigen sleutel of BYOK genoemd) wordt nog niet ondersteund voor Azure SQL Database grootschalige, maar TDE met door service beheerde sleutels wordt volledig ondersteund. |
|Intelligente database functies | Met uitzonde ring van de optie ' plan forceren ' worden alle andere opties voor automatisch afstemmen nog niet ondersteund op grootschalige: mogelijk lijkt het alsof de opties zijn ingeschakeld, maar zijn er geen aanbevelingen of acties gedaan. |
|Inzicht in queryprestaties | Query performance Insights wordt momenteel niet ondersteund voor grootschalige-data bases. |
| Data base verkleinen | DBCC SHRINKDATABASE of DBCC SHRINKFILE wordt momenteel niet ondersteund voor grootschalige-data bases. |
| Integriteits controle van data base | DBCC CHECKDB wordt momenteel niet ondersteund voor grootschalige-data bases. Zie [gegevens integriteit in Azure SQL database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) voor meer informatie over het beheer van gegevens integriteit in Azure SQL database. |

## <a name="next-steps"></a>Volgende stappen

- Zie [Veelgestelde vragen over grootschalige](sql-database-service-tier-hyperscale-faq.md)voor een veelgestelde vragen over grootschalige.
- Zie [service lagen](sql-database-service-tiers.md) voor meer informatie over service lagen.
- Zie [overzicht van resource limieten op een logische server](sql-database-resource-limits-logical-server.md) voor informatie over limieten op het niveau van de server en het abonnement.
- Zie [Azure SQL database op vCore gebaseerde inkoop model limieten voor één data](sql-database-vcore-resource-limits-single-databases.md)Base voor meer informatie over het aankoop model voor één data base.
- Zie [algemene SQL-functies](sql-database-features.md)voor een lijst met functies en vergelijkingen.
