---
title: Automatische, geografisch redundante back-ups
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database en Azure SQL Managed instance maken om de paar minuten automatisch een lokale back-up van de data base en gebruiken geografisch redundante opslag met lees toegang voor geografische redundantie.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: sqldbrb=2
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 06/04/2020
ms.openlocfilehash: 340f4310da5131ea0d2576e7c77d8f6cd0a731b3
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/06/2020
ms.locfileid: "85983101"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Automatische back-ups-Azure SQL Database & SQL Managed instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>Wat is een back-up van de data base?

Database back-ups zijn een essentieel onderdeel van een strategie voor bedrijfs continuïteit en herstel na nood gevallen, omdat ze uw gegevens beschermen tegen beschadiging of verwijdering.

Zowel SQL Database als SQL Managed instance gebruiken SQL Server technologie om elke week [volledige back-ups](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) te maken, [differentiële back](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) -ups elke 12-24 uur en [back-ups van transactie logboeken](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) om de vijf tot tien minuten. De frequentie van back-ups van transactie Logboeken is gebaseerd op de berekenings grootte en de hoeveelheid database activiteit.

Wanneer u een Data Base herstelt, bepaalt de service welke volledige, differentiële en transactie logboek back-ups moeten worden hersteld.

Deze back-ups maken het herstellen van data bases mogelijk op een bepaald moment binnen de geconfigureerde Bewaar periode. De back-ups worden opgeslagen als [Ra-GRS-opslag-blobs](../../storage/common/storage-redundancy.md) die worden gerepliceerd naar een [gepaard gebied](../../best-practices-availability-paired-regions.md) voor beveiliging tegen storingen die invloed hebben op back-upopslag in de primaire regio. 

Als uw regels voor gegevens bescherming vereisen dat uw back-ups langere tijd beschikbaar zijn (tot tien jaar), kunt u de [lange termijn retentie](long-term-retention-overview.md) voor zowel één als gepoolde data bases configureren.

U kunt deze back-ups gebruiken om:

- [Een bestaande data base herstellen naar een bepaald tijdstip in het verleden](recovery-using-backups.md#point-in-time-restore) binnen de retentie periode met behulp van Azure Portal, Azure PowerShell, Azure CLI of rest API. Voor afzonderlijke en gepoolde data bases maakt deze bewerking een nieuwe Data Base op dezelfde server als de oorspronkelijke Data Base, maar onder een andere naam om te voor komen dat de oorspronkelijke Data Base wordt overschreven. Nadat het herstellen is voltooid, kunt u de oorspronkelijke data base verwijderen of de [naam ervan wijzigen](https://docs.microsoft.com/sql/relational-databases/databases/rename-a-database) , en de naam van de herstelde data base wijzigen in de oorspronkelijke data base. Op een beheerd exemplaar kan deze bewerking op dezelfde manier een kopie maken van de Data Base op hetzelfde of een ander beheerd exemplaar onder hetzelfde abonnement en in dezelfde regio.
- [Een verwijderde data base herstellen naar het tijdstip van de verwijdering](recovery-using-backups.md#deleted-database-restore) of naar een bepaald tijdstip binnen de Bewaar periode. De verwijderde data base kan alleen worden teruggezet op een server of een beheerd exemplaar waarop de oorspronkelijke Data Base is gemaakt. Bij het verwijderen van een Data Base neemt de service een definitieve back-up van transactie logboeken voordat deze wordt verwijderd, om verlies van gegevens te voor komen.
- [Een Data Base herstellen naar een andere geografische regio](recovery-using-backups.md#geo-restore). Met geo-Restore kunt u een geografische nood situatie herstellen wanneer u geen toegang hebt tot uw data base of back-ups in de primaire regio. Er wordt in elke Azure-regio een nieuwe data base gemaakt op een bestaande server of een beheerd exemplaar.
- [Een Data Base herstellen op basis van een specifieke lange termijn back-up](long-term-retention-overview.md) van een enkele data base of gepoolde data base, als de data base is geconfigureerd met een lange termijn retentie beleid (LTR). Met LTR kunt u een oude versie van de data base herstellen met behulp van [de Azure Portal](long-term-backup-retention-configure.md#using-the-azure-portal) of [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) om te voldoen aan een nalevings aanvraag of een oude versie van de toepassing uit te voeren. Zie [Langetermijnretentie](long-term-retention-overview.md) voor meer informatie.

Zie [Data Base terugzetten van back-ups](recovery-using-backups.md)om een herstel bewerking uit te voeren.

> [!NOTE]
> In Azure Storage verwijst de term *replicatie* naar het kopiëren van blobs van de ene locatie naar een andere. In SQL verwijst *database replicatie* naar verschillende technologieën om te voor komen dat meerdere secundaire data bases worden gesynchroniseerd met een primaire data base.

U kunt back-ups van de configuratie-en herstel bewerkingen proberen met de volgende voor beelden:

| | Azure Portal | Azure PowerShell |
|---|---|---|
| **Retentie van back-ups wijzigen** | [Eén data base](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Beheerd exemplaar](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Eén data base](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Beheerd exemplaar](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **Lange termijn retentie van back-ups wijzigen** | [Eén data base](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Beheerd exemplaar-N.v.t.  | [Eén data base](long-term-backup-retention-configure.md)<br/>Beheerd exemplaar-N.v.t.  |
| **Een Data Base vanaf een bepaald moment herstellen** | [Eén data base](recovery-using-backups.md#point-in-time-restore) | [Eén data base](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Beheerd exemplaar](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **Een verwijderde database herstellen** | [Eén data base](recovery-using-backups.md) | [Eén data base](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Beheerd exemplaar](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **Een Data Base herstellen vanuit Azure Blob-opslag** | Eén data base-N.v.t. <br/>Beheerd exemplaar-N.v.t.  | Eén data base-N.v.t. <br/>[Beheerd exemplaar](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-scheduling"></a>Back-upplanning

De eerste volledige back-up wordt direct na het maken of herstellen van een nieuwe data base gepland. Deze back-up wordt gewoonlijk binnen 30 minuten voltooid, maar kan langer duren als de data base groot is. De eerste back-up kan bijvoorbeeld langer duren op een herstelde data base of een kopie van een Data Base die normaal gesp roken groter is dan een nieuwe data base. Na de eerste volledige back-up worden alle verdere back-ups automatisch gepland en beheerd. De exacte timing van alle database back-ups wordt bepaald door de SQL Database of de SQL Managed instance service, omdat deze de algehele systeem werk belasting evenwichtig benadert. U kunt het schema van back-uptaken niet wijzigen of uitschakelen.

> [!IMPORTANT]
> Voor een nieuwe, herstelde of gekopieerde data base wordt herstel naar een bepaald tijdstip beschikbaar vanaf het moment dat de eerste back-up van het transactie logboek dat volgt, de eerste volledige back-up wordt gemaakt.

## <a name="backup-storage-consumption"></a>Opslag gebruik back-ups

Met SQL Server-technologie voor back-up en herstel is het herstellen van een Data Base naar een bepaald tijdstip een niet-onderbroken back-upketen die bestaat uit één volledige back-up, eventueel een differentiële back-up en een of meer back-ups van transactie Logboeken. Het back-upschema van SQL Database en SQL Managed instance bevat een volledige back-up elke week. Daarom moet het systeem, om PITR binnen de gehele Bewaar periode in te scha kelen, extra volledige, differentiële en transactie logboek back-ups opslaan gedurende een periode die langer is dan de geconfigureerde Bewaar periode. 

Met andere woorden: voor elk tijdstip tijdens de Bewaar periode moet er een volledige back-up worden gemaakt die ouder is dan de oudste tijd van de retentie periode, evenals een ononderbroken keten van differentiële en transactie logboek back-ups van die volledige back-up tot de volgende volledige back-up.

> [!NOTE]
> Om PITR in te scha kelen, worden extra back-ups gedurende een week langer opgeslagen dan de geconfigureerde Bewaar periode. Back-upopslag wordt in rekening gebracht tegen hetzelfde tarief voor alle back-ups. 

Back-ups die niet meer nodig zijn om de PITR-functionaliteit te bieden, worden automatisch verwijderd. Omdat voor differentiële back-ups en logboek back-ups een eerdere volledige back-up kan worden herstorable, worden alle drie de back-uptypen samen in wekelijkse sets opgeschoond.

Voor alle data bases, inclusief [TDe versleutelde](transparent-data-encryption-tde-overview.md) data bases, worden back-ups gecomprimeerd om compressie van back-upopslag en kosten te verminderen. De gemiddelde compressie ratio van back-ups is 3-4 keer, maar dit kan aanzienlijk lager of hoger zijn, afhankelijk van de aard van de gegevens en of gegevens compressie wordt gebruikt in de-data base.

SQL Database en SQL Managed instance berekenen de totale gebruikte back-upopslag als een cumulatieve waarde. Elk uur wordt deze waarde gerapporteerd aan de Azure-facturerings pijplijn, die verantwoordelijk is voor het samen voegen van dit uur gebruik om uw verbruik aan het einde van elke maand te berekenen. Nadat de data base is verwijderd, neemt het verbruik af naarmate back-ups worden verwijderd. Zodra alle back-ups zijn verwijderd en PITR niet meer mogelijk is, wordt de facturering gestopt.
   
> [!IMPORTANT]
> Back-ups van een Data Base worden bewaard om PITR in te scha kelen, zelfs als de data base is verwijderd. Tijdens het verwijderen en opnieuw maken van een Data Base kunnen opslag-en reken kosten worden bespaard, kunnen de kosten voor back-upopslag toenemen omdat de service back-ups voor elke verwijderde data base behoudt, telkens wanneer deze wordt verwijderd. 

### <a name="monitor-consumption"></a>Verbruik bewaken

Voor vCore-data bases wordt de opslag die wordt gebruikt door elk type back-up (volledig, differentieel en logboek) op de Blade database bewaking gerapporteerd als een afzonderlijke metriek. In het volgende diagram ziet u hoe u het verbruik van back-upopslag voor één data base bewaakt. Deze functie is momenteel niet beschikbaar voor beheerde exemplaren.

![Gebruik van database back-ups in de Azure Portal bewaken](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Het gebruik van back-upopslag nauw keuriger instellen

Voor het verbruik van back-upopslag tot de maximale gegevens grootte voor een Data Base worden geen kosten in rekening gebracht. Het verbruik van het back-upopslag is afhankelijk van de werk belasting en de maximale grootte van de afzonderlijke data bases. Bekijk een aantal van de volgende afstemmings technieken om het verbruik van back-upopslag te verminderen:

- Verminder de [Bewaar periode voor back-ups](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) naar de minimale vereisten voor uw behoeften.
- Vermijd grote schrijf bewerkingen, zoals het opnieuw opbouwen van indexen, vaker dan u nodig hebt.
- Overweeg het gebruik van [geclusterde column Store-indexen](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes) en de volgende gerelateerde [Aanbevolen procedures](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance), en/of verklein het aantal niet-geclusterde indexen voor bewerkingen voor grote gegevens belasting.
- In de servicelaag Algemeen is de ingerichte gegevens opslag minder duur dan de prijs van de back-upopslag. Als u voortdurend veel overtollige back-upopslagkosten hebt, kunt u overwegen gegevens opslag te verg Roten om op te slaan in de back-upopslag.
- Gebruik TempDB in plaats van permanente tabellen in uw toepassings logica om tijdelijke resultaten en/of tijdelijke gegevens op te slaan.

## <a name="backup-retention"></a>Retentie van back-ups

Voor alle nieuwe, herstelde en gekopieerde data bases Azure SQL Database en Azure SQL Managed instance voldoende back-ups behouden zodat PITR in de afgelopen 7 dagen standaard is toegestaan. Met uitzonde ring van grootschalige-data bases kunt u de [retentie periode voor back-ups](#change-the-pitr-backup-retention-period) per data base wijzigen in het bereik van 1-35 dagen. Zoals beschreven in [back-upopslag](#backup-storage-consumption), is het mogelijk dat back-ups die zijn opgeslagen om PITR in te scha kelen, ouder zijn dan de retentie periode.

Als u een Data Base verwijdert, houdt het systeem back-ups op dezelfde manier als voor een online-data base met de specifieke Bewaar periode. U kunt de Bewaar periode voor back-ups niet wijzigen voor een verwijderde data base.

> [!IMPORTANT]
> Als u een server of een beheerd exemplaar verwijdert, worden ook alle data bases op die server of het beheerde exemplaar verwijderd en kunnen deze niet worden hersteld. U kunt een verwijderde server of een beheerd exemplaar niet herstellen. Maar als u wel lange termijn retentie (LTR) hebt geconfigureerd voor een Data Base of een beheerd exemplaar, worden back-ups voor lange termijn retentie niet verwijderd en kunnen ze worden gebruikt voor het herstellen van data bases op een andere server of een beheerd exemplaar in hetzelfde abonnement, tot een tijdstip waarop een back-up met lange termijn retentie is gemaakt.

De retentie van back-ups voor PITR in de afgelopen 1-35 dagen wordt soms een retentie voor de korte termijn voor back-ups genoemd. Als u back-ups langer wilt bewaren dan de maximale Bewaar periode voor de korte termijn van 35 dagen, kunt u de [lange termijn retentie](long-term-retention-overview.md)inschakelen.

### <a name="long-term-retention"></a>Lange bewaartermijn

Voor enkelvoudige en gegroepeerde Data bases en beheerde exemplaren kunt u de lange termijn retentie (LTR) van volledige back-ups configureren voor Maxi maal tien jaar in Azure Blob-opslag. Als u een LTR-beleid inschakelt, worden de wekelijkse volledige back-ups automatisch gekopieerd naar een andere RA-GRS-opslag container. Om aan verschillende nalevings vereisten te voldoen, kunt u verschillende Bewaar perioden selecteren voor wekelijkse, maandelijkse en/of jaarlijkse volledige back-ups. Het opslag verbruik is afhankelijk van de geselecteerde frequentie van V.L.N.R.-back-ups en de retentie periode of-peri Oden. U kunt de [LTR-prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=sql-database) gebruiken om de kosten van v.l.n.r.-opslag te schatten.

Net als bij PITR-back-ups worden LTR-back-ups beschermd met geografisch redundante opslag. Zie [Redundantie in Azure Storage](../../storage/common/storage-redundancy.md) voor meer informatie.

Voor meer informatie over LTR, Zie [lange termijn retentie van back-ups](long-term-retention-overview.md).

## <a name="storage-costs"></a>Opslagkosten

De prijs voor opslag is afhankelijk van het feit of u het DTU-model of het vCore-model gebruikt.

### <a name="dtu-model"></a>DTU-model

In het DTU-model worden geen extra kosten in rekening gebracht voor back-upopslag voor data bases en elastische Pools. De prijs van back-upopslag is onderdeel van de prijs van de data base of groep.

### <a name="vcore-model"></a>vCore-model

Voor afzonderlijke data bases in SQL Database is een back-upopslagwaarde gelijk aan 100 procent van de maximale grootte van de gegevens opslag voor de data base, zonder extra kosten. Voor elastische Pools en beheerde instanties is een back-upopslagwaarde gelijk aan 100 procent van de maximale gegevens opslag voor de pool of de maximale grootte van het exemplaar van de opslag, respectievelijk, zonder extra kosten. 

Voor afzonderlijke data bases wordt deze vergelijking gebruikt voor het berekenen van het totale factureer bare gebruik van back-ups:

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

Voor gegroepeerde Data bases wordt de totale factureer bare opslag grootte geaggregeerd op het groeps niveau en wordt als volgt berekend:

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

Voor beheerde instanties wordt de totale factureer bare opslag grootte op het exemplaar niveau geaggregeerd en als volgt berekend:

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

De totale factureer bare back-upopslag wordt in GB per maand in rekening gebracht. Dit verbruik van back-upopslag is afhankelijk van de werk belasting en de grootte van afzonderlijke data bases, elastische Pools en beheerde exemplaren. Sterk gewijzigde data bases hebben meer differentiële en logboek back-ups, omdat de grootte van deze back-ups evenredig is met de hoeveelheid gegevens wijzigingen. Daarom zullen dergelijke data bases hogere back-upkosten hebben.

SQL Database en SQL Managed instance berekent uw totale factureer bare back-upopslag als een cumulatieve waarde voor alle back-upbestanden. Elk uur wordt deze waarde gerapporteerd aan de Azure-facturerings pijplijn, waarmee dit uur gebruik wordt geaggregeerd om het verbruik van back-upopslag aan het einde van elke maand te verkrijgen. Als een Data Base wordt verwijderd, neemt het verbruik van back-upopslag geleidelijk af naarmate oudere back-ups worden verwijderd. Omdat voor differentiële back-ups en logboek back-ups een eerdere volledige back-up kan worden herstorable, worden alle drie de back-uptypen samen in wekelijkse sets opgeschoond. Zodra alle back-ups zijn verwijderd, wordt de facturering gestopt. 

Als een vereenvoudigd voor beeld wordt ervan uitgegaan dat een Data Base 744 GB back-upopslag heeft gecumuleerd en dat deze hoeveelheid gedurende een hele maand constant blijft, omdat de data base volledig niet actief is. Als u dit cumulatieve opslag gebruik wilt converteren naar het uurverbruik, deelt u dit op 744,0 (31 dagen per maand * 24 uur per dag). SQL Database meldt zich aan de Azure-facturerings pijplijn dat de data base elk uur 1 GB PITR-back-up heeft verbruikt, tegen een constant tarief. In azure billing wordt dit verbruik geaggregeerd en wordt een gebruik van 744 GB voor de hele maand weer gegeven. De kosten worden berekend op basis van het percentage van de hoeveelheid/GB per maand in uw regio.

Nu is een complexere voor beeld. Stel dat voor dezelfde niet-actieve Data Base de Bewaar periode van 7 dagen tot 14 dagen in het midden van de maand is verhoogd. Dit verhoogt de resultaten van de totale back-upopslag, verdubbeld tot 1.488 GB. SQL Database zou 1 GB aan gebruik melden voor uren 1 tot en met 372 (de eerste helft van de maand). Hiermee wordt het gebruik gerapporteerd als 2 GB voor uren 373 tot 744 (de tweede helft van de maand). Dit gebruik wordt geaggregeerd naar een voltooide factuur van 1.116 GB/maand.

De daad werkelijke facturerings scenario's voor back-ups zijn complexer. Omdat de frequentie van de wijzigingen in de data base afhankelijk is van de werk belasting en de variabele gedurende een bepaalde periode, is de grootte van elke differentiële back-up en logboek registratie ook afhankelijk, waardoor het verbruik van de back-upopslag per uur dienovereenkomstig wordt geschommeld. Daarnaast bevat elke differentiële back-up alle wijzigingen die zijn aangebracht in de data base sinds de laatste volledige back-up, waardoor de totale grootte van alle differentiële back-ups geleidelijk in de loop van een week toeneemt en vervolgens weer wordt versneld als er een oudere set van volledige, differentiële en logboek back-ups wordt weer gegeven. Als er bijvoorbeeld een zware schrijf activiteit zoals het opnieuw opbouwen van de index is uitgevoerd nadat een volledige back-up is voltooid, worden de wijzigingen die zijn aangebracht door het opnieuw opbouwen van de index opgenomen in de back-ups van de transactie logboeken die zijn gemaakt tijdens het opnieuw opbouwen, in de volgende differentiële back-up en in elke differentiële back-up die tot de volgende volledige back-up wordt Voor het laatste scenario in grotere data bases maakt een optimalisatie in de service een volledige back-up in plaats van een differentiële back-up als een differentiële back-up uitzonderlijk groot zou zijn. Dit beperkt de grootte van alle differentiële back-ups tot de volgende volledige back-up.

U kunt het totale opslag verbruik van back-ups voor elk back-uptype (volledig, Differentieel, transactie logboek) in de loop van de tijd bewaken, zoals beschreven in het [monitor gebruik](#monitor-consumption).

### <a name="monitor-costs"></a>Kosten bewaken

Als u meer wilt weten over de kosten voor back-upopslag, gaat u naar **Cost Management + facturering** in de Azure Portal, selecteert u **Cost Management**en selecteert u vervolgens **kosten analyse**. Selecteer het gewenste abonnement als **bereik**en filter vervolgens op de periode en service waarop u bent geïnteresseerd.

Voeg een filter toe voor **service naam**en selecteer vervolgens **SQL data base** in de vervolg keuzelijst. Gebruik het filter **meter subcategorie** om de facturerings teller voor uw service te kiezen. Selecteer voor één data base of een pool voor elastische Data Base **één/elastische pool pitr back-upopslag**. Voor een beheerd exemplaar selecteert u **mi pitr Backup Storage**. De subcategorieën voor **opslag** en **berekening** kunnen u ook interesseren, maar ze zijn niet gekoppeld aan back-upopslagkosten.

![Kosten analyse back-upopslag](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

## <a name="encrypted-backups"></a>Versleutelde back-ups

Als uw data base is versleuteld met TDE, worden back-ups automatisch versleuteld op rest, waaronder LTR-back-ups. Alle nieuwe data bases in Azure SQL worden geconfigureerd met TDE standaard ingeschakeld. Zie [transparent Data Encryption met SQL Database & Managed instance](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)voor meer informatie over TDe.

## <a name="backup-integrity"></a>Back-upintegriteit

Het Azure SQL engineering-team test automatisch het herstellen van automatische database back-ups. (Deze test is momenteel niet beschikbaar in het SQL Managed instance.) Bij herstel naar een bepaald tijdstip ontvangen data bases ook DBCC CHECKDB-integriteits controles.

Problemen die tijdens de integriteits controle worden gevonden, zullen leiden tot een waarschuwing voor het technische team. Zie [gegevens integriteit in SQL database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)voor meer informatie.

Alle database back-ups worden gemaakt met de CONTROLESOM optie om extra back-upintegriteit te bieden.

## <a name="compliance"></a>Naleving

Wanneer u uw data base migreert van een service tier op basis van DTU naar een vCore service tier, blijft de retentie van de PITR bewaard om ervoor te zorgen dat het gegevens herstel beleid van uw toepassing niet wordt aangetast. Als de standaard retentie niet voldoet aan uw nalevings vereisten, kunt u de retentie periode van PITR wijzigen. Zie [de retentie periode voor PITR-back-ups wijzigen](#change-the-pitr-backup-retention-period)voor meer informatie.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>De retentie periode voor PITR-back-ups wijzigen

U kunt de standaard retentie periode voor PITR-back-ups wijzigen met behulp van de Azure Portal, Power shell of de REST API. In de volgende voor beelden ziet u hoe u de retentie van PITR wijzigt in 28 dagen.

> [!WARNING]
> Als u de huidige retentie periode verlaagt, verliest u de mogelijkheid om te herstellen naar punten die ouder zijn dan de nieuwe Bewaar periode. Back-ups die niet meer nodig zijn voor het leveren van PITR binnen de nieuwe Bewaar periode, worden verwijderd. Als u de huidige retentie periode verhoogt, hebt u niet onmiddellijk de mogelijkheid om binnen de nieuwe Bewaar periode naar oudere tijdstippen te herstellen. U krijgt deze mogelijkheid na verloop van tijd, naarmate het systeem meer back-ups gaat bewaren.

> [!NOTE]
> Deze Api's zijn alleen van invloed op de PITR-retentie periode. Als u LTR hebt geconfigureerd voor uw data base, heeft dit geen invloed op dit probleem. Zie [lange termijn retentie](long-term-retention-overview.md)voor informatie over het wijzigen van v.l.n.r.-Bewaar perioden.

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>De retentie periode voor PITR-back-ups wijzigen met behulp van de Azure Portal

Als u de retentie periode voor PITR-back-ups wilt wijzigen met behulp van de Azure Portal, gaat u naar de server of het beheerde exemplaar met de data bases waarvan u de Bewaar periode wilt wijzigen. 

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Wijzigingen in PITR voor het bewaren van back-ups voor SQL Database worden uitgevoerd op de server pagina in de portal. Als u de retentie van PITR voor data bases op een server wilt wijzigen, gaat u naar de Blade Server overzicht. Selecteer **back-ups beheren** in het linkerdeel venster, selecteer de data bases binnen het bereik van uw wijziging en selecteer vervolgens **retentie configureren** aan de bovenkant van het scherm:

![PITR-retentie, server niveau wijzigen](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

Wijzigingen in de retentie van PITR voor een SQL Managed instance worden uitgevoerd op het niveau van een afzonderlijke data base. Ga naar de Blade overzicht van de afzonderlijke Data Base om de PITR-back-up te wijzigen voor een exemplaar database van de Azure Portal. Selecteer vervolgens de optie voor het **bewaren van back-ups configureren** boven aan het scherm:

![PITR-retentie, beheerd exemplaar wijzigen](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>De retentie periode voor PITR-back-ups wijzigen met behulp van Power shell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell AzureRM-module wordt nog steeds ondersteund door SQL Database en SQL Managed instance, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor meer informatie. De argumenten voor de opdrachten in de module AZ zijn vrijwel identiek aan die in de AzureRm-modules.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>De retentie periode voor PITR-back-ups wijzigen met behulp van de REST API

#### <a name="sample-request"></a>Voorbeeld aanvraag

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Aanvraagbody

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Voorbeeldantwoord

Status code: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Zie [retentie van back-ups rest API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Database back-ups zijn een essentieel onderdeel van een strategie voor bedrijfs continuïteit en herstel na nood gevallen, omdat uw gegevens worden beschermd tegen onbedoelde beschadiging of verwijdering. Zie [overzicht van bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md)voor meer informatie over de andere SQL database oplossingen voor bedrijfs continuïteit.
- Meer informatie over [het herstellen van een Data Base naar een bepaald tijdstip met behulp van de Azure Portal](recovery-using-backups.md).
- Meer informatie over het herstellen van [een Data Base naar een bepaald tijdstip met behulp van Power shell](scripts/restore-database-powershell.md).
- Voor informatie over het configureren, beheren en herstellen van een lange termijn retentie van automatische back-ups in Azure Blob-opslag met behulp van de Azure Portal, Zie [lange termijn retentie van back-ups beheren met behulp van de Azure Portal](long-term-backup-retention-configure.md).
- Zie [lange termijn retentie van back-ups beheren met Power shell](long-term-backup-retention-configure.md)voor meer informatie over het configureren, beheren en herstellen van een lange termijn retentie van automatische back-ups in Azure Blob-opslag met behulp van Power shell.
