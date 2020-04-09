---
title: Meerdere databases beheren met elastische pools
description: Meerdere SQL-databases - honderden en duizenden - beheren en schalen met behulp van elastische pools. Eén prijs voor resources die u waar nodig distribueren.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
ms.date: 08/06/2019
ms.openlocfilehash: 8139ed8f4f4799a963a051eed96dd87c4ac38aec
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981423"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Met elastische pools kunt u meerdere Azure SQL-databases beheren en schalen

Elastische pools in SQL Database zijn eenvoudige, kosteneffectieve oplossingen voor het beheren en schalen van meerdere databases die een verschillend en onvoorspelbaar verbruik hebben. De databases in een elastische pool bevinden zich op één Azure SQL Database-server en delen een vast aantal resources tegen een vaste prijs. Met elastische groepen in Azure SQL Database kunnen SaaS-ontwikkelaars de prijsprestaties voor een groep databases binnen een voorgeschreven budget optimaliseren en flexibele prestaties voor elke database leveren.

## <a name="what-are-sql-elastic-pools"></a>Wat zijn SQL-elastische pools

SaaS-ontwikkelaars ontwikkelen toepassingen boven op grootschalige gegevenslagen die uit meerdere databases bestaan. Een algemeen patroon is de inrichting van een individuele database voor elke klant. Maar verschillende klanten hebben vaak verschillende en onvoorspelbare gebruikspatronen en het is moeilijk om de resourcevereisten van elke afzonderlijke databasegebruiker te voorspellen. Traditioneel had je twee opties:

- Overprovisionresources op basis van piekgebruik en meer dan salaris, of
- Onder-provision om kosten te besparen, ten koste van de prestaties en klanttevredenheid tijdens pieken.

Elastische pools lossen dit probleem op door ervoor te zorgen dat databases de prestatiebronnen krijgen die ze nodig hebben wanneer ze het nodig hebben. Ze bieden een eenvoudig mechanisme voor het toewijzen van resources binnen een voorspelbaar budget. Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) voor meer informatie over ontwerppatronen voor SaaS-toepassingen met elastische pools.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> Er zijn geen kosten per database voor elastische pools. Er worden kosten in rekening gebracht voor elk uur dat een pool bestaat bij de hoogste eDTU of vCores, ongeacht het gebruik of dat de groep minder dan een uur actief was.

Elastische groepen stellen de ontwikkelaar in staat om resources te kopen voor een groep die wordt gedeeld door meerdere databases om rekening te houden met onvoorspelbare perioden van gebruik door afzonderlijke databases. U resources voor de groep configureren op basis van het [op DTU gebaseerde inkoopmodel](sql-database-service-tiers-dtu.md) of het [op vCore gebaseerde inkoopmodel.](sql-database-service-tiers-vcore.md) De resourcevereiste voor een groep wordt bepaald door het totale gebruik van de databases. De hoeveelheid resources die beschikbaar zijn voor de groep wordt beheerd door het ontwikkelaarsbudget. De ontwikkelaar voegt eenvoudig gegevensbestanden aan de pool toe, stelt de minimum en maximummiddelen voor de gegevensbestanden (of minimum en maximum DTU's of minimum of maximum vCores afhankelijk van uw keus van het resourcingmodel) in en stelt de middelen van de pool op basis van hun budget in. Met groepen kan een ontwikkelaar services naadloos met een alsmaar groeiende schaal uitbreiden van een kleine startende ondernemer tot een volwassen bedrijf.

Binnen de pool hebben afzonderlijke databases de flexibiliteit om de schaal automatisch aan te passen binnen ingestelde parameters. Onder zware belasting kan een database meer resources verbruiken om aan de vraag te voldoen. Databases onder lichte belastingen verbruiken minder en databases onder geen belasting verbruiken geen resources. De inrichting van resources voor de hele pool in plaats van afzonderlijke databases vereenvoudigt uw beheertaken. Plus, je hebt een voorspelbaar budget voor het zwembad. Extra resources kunnen worden toegevoegd aan een bestaande groep zonder databasedowntime, behalve dat de databases mogelijk moeten worden verplaatst om de extra rekenbronnen voor de nieuwe eDTU-reservering te bieden. Als extra resources niet meer nodig zijn, kunnen ze op elk moment uit een bestaande groep worden verwijderd. Daarnaast kunt u databases aan de groep toevoegen of uit de groep verwijderen. Als een database naar verwachting minder resources nodig heeft, kunt u deze verwijderen.

> [!NOTE]
> Bij het verplaatsen van databases naar of uit een elastische groep, is er geen downtime, behalve voor een korte periode (in de volgorde van seconden) aan het einde van de bewerking wanneer databaseverbindingen worden verwijderd.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Wanneer moet u een SQL Database-elastische pool overwegen

Pools zijn geschikt voor een groot aantal databases met specifieke gebruikspatronen. Voor een bepaalde database wordt dit patroon gekenmerkt door een laag gemiddeld gebruik met relatief incidentele gebruikspieken.

Hoe meer databases u aan een groep kunt toevoegen, des te groter worden de besparingen. Afhankelijk van het gebruikspatroon van uw toepassing, is het mogelijk om besparingen te zien met slechts twee S3-databases.

In de volgende secties ziet u hoe u kunt bepalen of het voor uw specifieke verzameling databases voordelig is om deel uit te maken van een groep. In de voorbeelden wordt gebruik gemaakt van Standard-groepen, maar dezelfde principes gelden ook voor Basic- en Premium-groepen.

### <a name="assessing-database-utilization-patterns"></a>Databasegebruikspatronen beoordelen

De volgende afbeelding toont een voorbeeld van een database die doorgaans weinig wordt gebruikt, maar bij soms ook extreem actief is. Dit is een gebruikspatroon dat geschikt is voor een groep:

   ![een individuele database die geschikt is voor een groep](./media/sql-database-elastic-pool/one-database.png)

Gedurende de geïllustreerde periode van vijf minuten piekt DB1 tot 90 DTU's, maar het gemiddelde gebruik is nog geen vijf DTU's. Een S3-rekengrootte is vereist om deze werkbelasting in één database uit te voeren, maar dit laat de meeste resources ongebruikt tijdens perioden van lage activiteit.

Met een groep kunnen deze ongebruikte DTU’s worden gedeeld met meerdere databases, wat de benodigde DTU's en de totale kosten vermindert.

Stel dat er in het vorige voorbeeld meer databases zijn die een soortgelijk gebruikspatroon hebben als DB1. In de volgende twee cijfers hieronder wordt het gebruik van vier databases en 20 databases op dezelfde grafiek gelaagd om de niet-overlappende aard van hun gebruik in de loop van de tijd te illustreren met behulp van het op DTU gebaseerde inkoopmodel:

   ![vier databases met een gebruikspatroon dat geschikt is voor een groep](./media/sql-database-elastic-pool/four-databases.png)

   ![twintig databases met een gebruikspatroon dat geschikt is voor een groep](./media/sql-database-elastic-pool/twenty-databases.png)

Het gezamenlijke DTU-gebruik van alle 20 databases wordt aangegeven door de zwarte lijn in voorgaande afbeelding. U ziet dat het totale DTU-gebruik nooit hoger is dan honderd DTU’s en dat de twintig databases gedurende deze periode honderd eDTU's kunnen delen. Dit resulteert in een 20x vermindering van DTU's en een 13x prijsverlaging in vergelijking met het plaatsen van elk van de databases in S3 compute sizes voor enkele databases.

Dit voorbeeld is om de volgende redenen ideaal:

- Er zijn grote verschillen tussen piekgebruik en gemiddeld gebruik per database.
- Het piekgebruik voor elke database vindt plaats op verschillende momenten.
- eDTU‘s worden gedeeld tussen meerdere databases.

De prijs van een groep is een functie van de groep eDTU's. Hoewel de prijs per eDTU voor een groep 1,5 x groter is dan de prijs per DTU voor een individuele database, **kunnen eDTU's in een groep door veel databases worden gedeeld en zijn er dus minder eDTU's nodig**. Deze verschillen in prijsbepaling en het delen van eDTU's vormen de basis van de mogelijke prijsbesparing die groepen kunnen bieden.

De volgende vuistregels met betrekking tot het aantal databases en het gebruik van databases helpen ervoor te zorgen dat een groep lagere kosten oplevert in vergelijking met het gebruik van rekengroottes voor afzonderlijke databases.

### <a name="minimum-number-of-databases"></a>Minimum aantal databases

Als de totale hoeveelheid resources voor afzonderlijke databases meer dan 1,5 x de resources is die nodig zijn voor de groep, is een elastische groep kosteneffectiever.

***Voorbeeld van het Op DTU gebaseerde aankoopmodel***<br>
Ten minste twee S3-databases of ten minste 15 S0-databases zijn nodig om een 100 eDTU-groep kosteneffectiever te maken dan het gebruik van rekengroottes voor afzonderlijke databases.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Maximum aantal gelijktijdig piekende databases

Door resources te delen, kunnen niet alle databases in een groep tegelijkertijd resources gebruiken tot de limiet die beschikbaar is voor afzonderlijke databases. Hoe minder databases tegelijkertijd pieken, hoe lager de poolresources kunnen worden ingesteld en hoe kosteneffectiever de groep wordt. Over het algemeen is niet meer dan 2/3 (of 67%) van de databases in de groep moet tegelijkertijd piek tot hun middelen te beperken.

***Voorbeeld van het Op DTU gebaseerde aankoopmodel***

Om de kosten voor drie S3-databases in een groep van 200 eDTU's te verlagen, kunnen maximaal twee van deze databases tegelijkertijd pieken in hun gebruik. Of, als meer dan twee van deze vier S3-databases gelijktijdig pieken, zou de groep moeten worden uitgebreid tot meer dan 200 eDTU's. Als de groep is aangepast aan meer dan 200 eDTU's, moeten meer S3-databases aan de groep worden toegevoegd om de kosten lager te houden dan rekengroottes voor afzonderlijke databases.

Houd er rekening mee dat in dit voorbeeld geen rekening wordt gehouden met het gebruik van andere databases in de groep. Als alle databases voortdurend in enige mate gebruik maken van eDTU's, kan minder dan 2/3 (of 67%) van de databases tegelijkertijd pieken.

### <a name="resource-utilization-per-database"></a>Resourcegebruik per database

Een groot verschil tussen het piek- en gemiddelde gebruik van een database geeft langere perioden van laag gebruik en korte perioden hoog gebruik aan. Dit gebruikspatroon is ideaal voor het delen van resources met meerdere databases. Een database zou een geschikte kandidaat voor een groep kunnen zijn als het piekgebruik ongeveer 1,5 keer groter is dan het gemiddelde gebruik.

**Voorbeeld van het DTU-inkoopmodel**: Een S3-database die pieken tot 100 DTU's en gemiddeld 67 DTU's of minder gebruikt, is een goede kandidaat om eDTU's in een pool te delen. Ook een S1-database die piekt tot 20 DTU's en gemiddeld 13 DTU's of minder gebruikt, is een goede kandidaat voor een groep.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Hoe kies ik de juiste poolgrootte

De beste grootte voor een groep is afhankelijk van de totale resources die nodig zijn voor alle databases in de groep. Hierbij wordt het volgende bepaald:

- Maximale resources die worden gebruikt door alle databases in de groep (maximale DTU's of maximale vCores, afhankelijk van uw keuze van het resourcingmodel).
- De maximum opslag in bytes die door alle databases in de groep wordt gebruikt.

Zie voor beschikbare servicelagen voor elk resourcemodel het [op DTU gebaseerde inkoopmodel](sql-database-service-tiers-dtu.md) of het [op vCore gebaseerde inkoopmodel](sql-database-service-tiers-vcore.md).

Als u het hulpprogramma niet kunt gebruiken, kunnen de volgende stappen u helpen om te schatten of een groep rendabeler is dan individuele databases:

1. Schat de eDTU's of vCores die nodig zijn voor de groep als volgt:

   Voor het op DTU gebaseerde inkoopmodel: MAX(<*Totaal aantal DBs* X *gemiddelde DTU-gebruik per* DB->,<br>  
   <*aantal gelijktijdig piekende databases* X *piek-DTU-gebruik per DB*)

   Voor vCore-gebaseerd inkoopmodel: MAX(<*Totaal aantal DBs* X *gemiddelde vCore-gebruik per DB*>,<br>  
   <*Aantal gelijktijdig piek-DB's* X *Piek vCore-gebruik per DB*)

2. Schat hoeveel opslagruimte de groep nodig heeft door het aantal bytes op te tellen dat nodig is voor alle databases in de groep. Bepaal daarna hoe groot de eDTU-groep moet zijn om aan deze hoeveelheid opslag te voldoen.
3. Neem voor het Op DTU gebaseerde inkoopmodel de grotere van de eDTU-schattingen van stap 1 en stap 2. Voor het vCore-gebaseerde inkoopmodel, neem de vCore-schatting van stap 1.
4. Bekijk de [prijspagina van SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) en zoek de kleinste poolgrootte die groter is dan de schatting van stap 3.
5. Vergelijk de poolprijs van stap 5 met de prijs van het gebruik van de juiste rekengroottes voor afzonderlijke databases.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Andere SQL Database-functies gebruiken met elastische pools

### <a name="elastic-jobs-and-elastic-pools"></a>Elastische taken en elastische pools

Met een groep worden beheertaken vereenvoudigd door scripts in **[elastische taken](elastic-jobs-overview.md)** uit te voeren. Een elastische taak elimineert de meeste saaie handelingen die komen kijken bij grote aantallen databases.

Zie [Scaling out with Azure SQL Database](sql-database-elastic-scale-introduction.md) (Uitbreiden met Azure SQL Database) voor meer informatie over andere databasehulpprogramma's voor het werken met meerdere databases.

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opties voor bedrijfscontinuïteit voor databases in een elastische pool

Pooldatabases ondersteunen in het algemeen dezelfde [bedrijfscontinuïteitsfuncties](sql-database-business-continuity.md) die beschikbaar zijn voor individuele databases.

- **Terugzetten naar eerder tijdstip**

  Point-in-time restore maakt gebruik van automatische databaseback-ups om een database in een groep te herstellen tot een specifiek tijdstip. Zie [Herstel naar een bepaald tijdstip](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Geo-herstel**

  Geo-restore biedt de standaardhersteloptie wanneer een database niet beschikbaar is vanwege een incident in het gebied waar de database wordt gehost. Zie [Een Azure SQL-database of failover herstellen naar een secundaire](sql-database-disaster-recovery.md)

- **Actieve geo-replicatie**

  Voor toepassingen die agressievere herstelvereisten hebben dan geo-restore kan bieden, configureert u [Actieve georeplicatie](sql-database-active-geo-replication.md) of een [groep voor automatische failover.](sql-database-auto-failover-group.md)

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Een nieuwe elastische groep SQL Database maken met de Azure-portal

Er zijn twee manieren waarop u een elastische pool maken in de Azure-portal.

1. Ga naar de [Azure-portal](https://portal.azure.com) om een elastische pool te maken. Zoeken naar en selecteer **Azure SQL**.
2. Selecteer **+Toevoegen** om de **optiepagina SQL-implementatie selecteren** te openen. U aanvullende informatie over elastische groepen weergeven door **Details weergeven** op de tegel **Databases** te selecteren.
3. Selecteer **op** de tegel Databases de optie **Elastische groep** in de vervolgkeuzelijst **Resourcetype** en selecteer **Vervolgens Maken:**

   ![Een pool voor Elastic Database maken](./media/sql-database-elastic-pool/create-elastic-pool.png)


1. U ook een elastische groep maken door naar een bestaande Azure SQL-server te navigeren en op **+ Nieuwe groep** te klikken om een groep rechtstreeks in die server te maken.

> [!NOTE]
> U meerdere groepen op een server maken, maar u geen databases van verschillende servers aan dezelfde groep toevoegen.

De servicelaag van de groep bepaalt de functies die beschikbaar zijn voor de elastieken in de groep en de maximale hoeveelheid resources die beschikbaar zijn voor elke database. Zie Resourcelimieten voor elastische pools in het [DTU-model voor](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)meer informatie. Zie [vCore-gebaseerde resourcelimieten voor vCore-limieten - elastische pools](sql-database-vcore-resource-limits-elastic-pools.md)voor vCore-gebaseerde resourcelimieten.

Als u de resources en prijzen van de groep wilt configureren, klikt u op **Groep configureren**. Selecteer vervolgens een servicelaag, voeg databases toe aan de groep en configureer de resourcelimieten voor de groep en de databases.

Wanneer u klaar bent met het configureren van de groep, u op 'Toepassen' klikken, de groep een naam geven en op 'OK' klikken om de groep te maken.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Een elastische pool en de bijbehorende databases controleren

In de Azure-portal u het gebruik van een elastische groep en de databases binnen die groep controleren. U ook een reeks wijzigingen aanbrengen in uw elastische pool en alle wijzigingen tegelijkertijd indienen. Deze wijzigingen omvatten het toevoegen of verwijderen van databases, het wijzigen van de instellingen voor elastische poolen of het wijzigen van uw database-instellingen.

Als u wilt beginnen met het bewaken van uw elastische zwembad, moet u een elastisch zwembad in het portaal zoeken en openen. U ziet eerst een scherm dat u een overzicht geeft van de status van uw elastische pool. Dit omvat:

- Grafieken weergeven met resources die het gebruik van de elastische groep weergeven
- Recente waarschuwingen en aanbevelingen, indien beschikbaar, voor de elastische pool

In de volgende afbeelding ziet u een voorbeeld van een elastische pool:

![Poolweergave](./media/sql-database-elastic-pool-manage-portal/basic.png)

Als u meer informatie wilt over de pool, u op een van de beschikbare informatie in dit overzicht klikken. Als u op het **resourcegebruiksdiagram** klikt, gaat u naar de weergave Azure-controle, waar u de statistieken en het tijdvenster in de grafiek aanpassen. Als u op beschikbare meldingen klikt, gaat u naar een mes met de volledige details van die waarschuwing of aanbeveling.

Als u de databases in uw groep wilt controleren, u klikken op Het gebruik van **databasebronnen** in de sectie **Controle** van het resourcemenu aan de linkerkant.

![Pagina databaseresourcegebruik](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>De grafiekweergave aanpassen

U de grafiek en de metrische pagina bewerken om andere statistieken weer te geven, zoals het CPU-percentage, het IO-percentage gegevens en het gebruikte IO-percentage voor logboeken.

In het formulier **Grafiek bewerken** u een vast tijdsbereik selecteren of op **aangepast** klikken om een periode van 24 uur in de afgelopen twee weken te selecteren en vervolgens de resources selecteren die u wilt controleren.

### <a name="to-select-databases-to-monitor"></a>Databases selecteren om te controleren

Standaard wordt in de grafiek in het blade **Voor het gebruik van databaseresources** de top 5 databases per DTU of CPU weergegeven (afhankelijk van uw servicelaag). U de databases in deze grafiek overschakelen door databases uit de lijst onder de grafiek te selecteren en uit te schakelen via de selectievakjes aan de linkerkant.

U ook meer statistieken selecteren om naast elkaar te bekijken in deze databasetabel om een vollediger beeld te krijgen van de prestaties van uw databases.

Zie [SQL Database-waarschuwingen maken in Azure-portal](sql-database-insights-alerts-portal.md)voor meer informatie.

## <a name="customer-case-studies"></a>Casestudy's van klanten

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart gebruikte elastische pools met Azure SQL Database om zijn zakelijke services snel uit te breiden met een snelheid van 1.000 nieuwe Azure SQL-databases per maand.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco gebruikt elastische pools met Azure SQL Database om snel services in te richten en te schalen voor duizenden tenants in de cloud.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)    

   Daxko/CSI gebruikt elastische pools met Azure SQL Database om de ontwikkelingscyclus te versnellen en de klantenservice en prestaties ervan te verbeteren.   

## <a name="next-steps"></a>Volgende stappen

- Zie [Elastische poolprijzen](https://azure.microsoft.com/pricing/details/sql-database/elastic)voor prijsinformatie .
- Zie [Elastische pools schalen](sql-database-elastic-pool-scale.md) en [Een elastische groep](scripts/sql-database-monitor-and-scale-pool-powershell.md) schalen - voorbeeldcode
- Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) voor meer informatie over ontwerppatronen voor SaaS-toepassingen met elastische pools.
- Zie [Inleiding tot de Wingtip SaaS-toepassing](sql-database-wtp-overview.md)voor een SaaS-zelfstudie met elastische pools.
- Zie [Resourcebeheer in dichte elastische pools](sql-database-elastic-pool-resource-management.md)voor meer informatie over resourcebeheer in elastische pools.
