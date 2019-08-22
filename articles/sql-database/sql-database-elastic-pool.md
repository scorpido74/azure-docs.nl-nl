---
title: Meerdere SQL-data bases beheren met elastische Pools-Azure | Microsoft Docs
description: 'Meerdere SQL-data bases beheren en schalen: honderden en duizenden-gebruik elastische Pools. Eén prijs voor resources die u kunt distribueren waar nodig.'
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
ms.openlocfilehash: 0b0a6bec7916c056c187ed9e588dd3ac8fea8d84
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876407"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Elastische Pools helpen u bij het beheren en schalen van meerdere Azure SQL-data bases

Elastische pools in SQL Database zijn eenvoudige, kosteneffectieve oplossingen voor het beheren en schalen van meerdere databases die een verschillend en onvoorspelbaar verbruik hebben. De data bases in een elastische pool bevinden zich op één Azure SQL Database Server en delen een bepaald aantal resources tegen een ingestelde prijs. Met elastische groepen in Azure SQL Database kunnen SaaS-ontwikkelaars de prijsprestaties voor een groep databases binnen een voorgeschreven budget optimaliseren en flexibele prestaties voor elke database leveren.

## <a name="what-are-sql-elastic-pools"></a>Wat zijn elastische SQL-Pools

SaaS-ontwikkelaars ontwikkelen toepassingen boven op grootschalige gegevenslagen die uit meerdere databases bestaan. Een algemeen patroon is de inrichting van een individuele database voor elke klant. Maar verschillende klanten hebben vaak verschillende en onvoorspelbare gebruikspatronen, en de resourcevereisten van elke gebruiker van een individuele database zijn moeilijk te voorspellen. Normaal gesp roken hebt u twee opties:

- Resources van meer dan inrichten op basis van piek gebruik en meer dan betalen, of
- Onder-inrichting voor het besparen van kosten, tegen kosten van prestaties en klant tevredenheid tijdens pieken.

Elastische Pools kunnen dit probleem oplossen door ervoor te zorgen dat data bases de benodigde prestatie bronnen krijgen wanneer ze deze nodig hebben. Ze bieden een eenvoudig mechanisme voor het toewijzen van resources binnen een voorspelbaar budget. Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) voor meer informatie over ontwerppatronen voor SaaS-toepassingen met elastische pools.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> Er worden geen kosten per Data Base voor elastische Pools. Er worden kosten in rekening gebracht voor elk uur dat een pool bestaat op het hoogste eDTU-of vCores, ongeacht het gebruik of het feit dat de groep korter dan een uur actief was.

Met elastische Pools kunnen ontwikkel aars bronnen kopen voor een pool die wordt gedeeld door meerdere data bases om te voorzien in onvoorspelbare Peri Oden van gebruik door afzonderlijke data bases. U kunt resources configureren voor de pool op basis van het op [DTU gebaseerde aankoop model](sql-database-service-tiers-dtu.md) of het [op vCore gebaseerde aankoop model](sql-database-service-tiers-vcore.md). De resource vereiste voor een groep wordt bepaald door het aggregatie gebruik van de data bases. De hoeveelheid beschik bare bronnen voor de groep wordt bepaald door het budget van de ontwikkelaar. De ontwikkelaar voegt alleen data bases toe aan de groep, stelt de minimale en maximale bronnen voor de data bases in (minimale en maximale Dtu's of minimum-of maximum vCores afhankelijk van uw keuze van het model voor opnieuw instellen) en stelt vervolgens de resources van de groep in op basis van hun budget. Met groepen kan een ontwikkelaar services naadloos met een alsmaar groeiende schaal uitbreiden van een kleine startende ondernemer tot een volwassen bedrijf.

Binnen de pool hebben afzonderlijke databases de flexibiliteit om de schaal automatisch aan te passen binnen ingestelde parameters. Onder zware belasting kan een Data Base meer resources gebruiken om aan de vraag te voldoen. Data bases onder lichte belasting worden minder verbruikt en data bases onder geen belasting verbruiken geen resources. De inrichting van resources voor de hele pool in plaats van afzonderlijke databases vereenvoudigt uw beheertaken. Daarnaast hebt u een voorspelbaar budget voor de pool. Extra resources kunnen worden toegevoegd aan een bestaande groep zonder uitval tijd van de data base, behalve dat de data bases mogelijk moeten worden verplaatst om de extra reken resources te bieden voor de nieuwe eDTU-reserve ring. En als extra resources niet meer nodig zijn, kunnen ze op elk moment uit een bestaande pool worden verwijderd. Daarnaast kunt u databases aan de groep toevoegen of uit de groep verwijderen. Als een database naar verwachting minder resources nodig heeft, kunt u deze verwijderen.

> [!NOTE]
> Wanneer u data bases naar of uit een elastische pool verplaatst, is er geen downtime, met uitzonde ring van een korte periode (in de volg orde van seconden) aan het einde van de bewerking wanneer de database verbindingen worden verwijderd.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Wanneer moet u een SQL Database elastische pool overwegen

Pools zijn geschikt voor een groot aantal databases met specifieke gebruikspatronen. Voor een bepaalde database wordt dit patroon gekenmerkt door een laag gemiddeld gebruik met relatief incidentele gebruikspieken.

Hoe meer databases u aan een groep kunt toevoegen, des te groter worden de besparingen. Afhankelijk van uw gebruikspatroon van toepassingen is het mogelijk om besparingen te zien met slechts twee S3-databases.

In de volgende secties ziet u hoe u kunt bepalen of het voor uw specifieke verzameling databases voordelig is om deel uit te maken van een groep. In de voorbeelden wordt gebruik gemaakt van Standard-groepen, maar dezelfde principes gelden ook voor Basic- en Premium-groepen.

### <a name="assessing-database-utilization-patterns"></a>Databasegebruikspatronen beoordelen

De volgende afbeelding toont een voorbeeld van een database die doorgaans weinig wordt gebruikt, maar bij soms ook extreem actief is. Dit is een gebruikspatroon dat geschikt is voor een groep:

   ![een individuele database die geschikt is voor een groep](./media/sql-database-elastic-pool/one-database.png)

Gedurende de geïllustreerde periode van vijf minuten piekt DB1 tot 90 DTU's, maar het gemiddelde gebruik is nog geen vijf DTU's. Een S3-reken grootte is vereist voor het uitvoeren van deze werk belasting in één data base, maar hiermee blijven de meeste resources ongebruikt tijdens peri Oden met een lage activiteit.

Met een groep kunnen deze ongebruikte DTU’s worden gedeeld met meerdere databases, wat de benodigde DTU's en de totale kosten vermindert.

Stel dat er in het vorige voorbeeld meer databases zijn die een soortgelijk gebruikspatroon hebben als DB1. In de volgende twee afbeeldingen wordt het gebruik van vier data bases en 20 data bases gelaagd in dezelfde grafiek om de niet-overlappende aard van het gebruik in de loop van de tijd te illustreren met behulp van het op DTU gebaseerde aankoop model:

   ![vier databases met een gebruikspatroon dat geschikt is voor een groep](./media/sql-database-elastic-pool/four-databases.png)

   ![twintig databases met een gebruikspatroon dat geschikt is voor een groep](./media/sql-database-elastic-pool/twenty-databases.png)

Het gezamenlijke DTU-gebruik van alle 20 databases wordt aangegeven door de zwarte lijn in voorgaande afbeelding. U ziet dat het totale DTU-gebruik nooit hoger is dan honderd DTU’s en dat de twintig databases gedurende deze periode honderd eDTU's kunnen delen. Dit resulteert in een 20x-reductie in Dtu's en een dertienvoudige-prijs verlaging in vergelijking met het plaatsen van elk van de data bases in S3-reken grootten voor afzonderlijke data bases.

Dit voorbeeld is om de volgende redenen ideaal:

- Er zijn grote verschillen tussen piekgebruik en gemiddeld gebruik per database.
- Het piekgebruik voor elke database vindt plaats op verschillende momenten.
- eDTU‘s worden gedeeld tussen meerdere databases.

De prijs van een groep is een functie van de groep eDTU's. Hoewel de prijs per eDTU voor een groep 1,5 x groter is dan de prijs per DTU voor een individuele database, **kunnen eDTU's in een groep door veel databases worden gedeeld en zijn er dus minder eDTU's nodig**. Deze verschillen in prijsbepaling en het delen van eDTU's vormen de basis van de mogelijke prijsbesparing die groepen kunnen bieden.

De volgende vuist regels met betrekking tot het aantal data bases en database gebruik helpen ervoor te zorgen dat een pool gereduceerde kosten oplevert vergeleken met het gebruik van reken grootten voor afzonderlijke data bases.

### <a name="minimum-number-of-databases"></a>Minimum aantal databases

Als de totale hoeveelheid resources voor afzonderlijke data bases meer is dan 1,5 x de resources die nodig zijn voor de groep, is een elastische pool rendabeler.

***Voor beeld van een op DTU gebaseerd inkoop model***<br>
Er zijn ten minste twee S3-data bases of ten minste 15 S0-data bases nodig voor een 100-eDTU-groep om rendabeler te zijn dan het gebruik van reken grootten voor afzonderlijke data bases.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Maximum aantal gelijktijdig piekende databases

Door resources te delen, kunnen niet alle data bases in een pool gelijktijdig bronnen gebruiken tot de limiet die beschikbaar is voor afzonderlijke data bases. Hoe minder data bases gelijktijdig pieken, hoe lager de pool bronnen kunnen worden ingesteld en hoe rendabeler de groep wordt. In het algemeen, niet groter dan 2/3 (of 67%) van de data bases in de groep moeten tegelijkertijd pieken op de limiet van de resources.

***Voor beeld van een op DTU gebaseerd inkoop model***

Om de kosten voor drie S3-databases in een groep van 200 eDTU's te verlagen, kunnen maximaal twee van deze databases tegelijkertijd pieken in hun gebruik. Of, als meer dan twee van deze vier S3-databases gelijktijdig pieken, zou de groep moeten worden uitgebreid tot meer dan 200 eDTU's. Als het formaat van de groep wordt gewijzigd naar meer dan 200 Edtu's, moeten er meer S3-data bases aan de groep worden toegevoegd om de kosten lager te blijven dan reken grootten voor afzonderlijke data bases.

In dit voorbeeld wordt geen rekening gehouden met het gebruik van andere databases in de groep. Als alle databases voortdurend in enige mate gebruik maken van eDTU's, kan minder dan 2/3 (of 67%) van de databases tegelijkertijd pieken.

### <a name="resource-utilization-per-database"></a>Resource gebruik per data base

Een groot verschil tussen het piek- en gemiddelde gebruik van een database geeft langere perioden van laag gebruik en korte perioden hoog gebruik aan. Dit gebruikspatroon is ideaal voor het delen van resources met meerdere databases. Een database zou een geschikte kandidaat voor een groep kunnen zijn als het piekgebruik ongeveer 1,5 keer groter is dan het gemiddelde gebruik.

**Voor beeld van een op DTU gebaseerd inkoop model**: Een S3-database die piekt tot 100 DTU's en gemiddeld 67 DTU's of minder gebruikt, is een goede kandidaat voor het delen van eDTU's in een groep. Ook een S1-database die piekt tot 20 DTU's en gemiddeld 13 DTU's of minder gebruikt, is een goede kandidaat voor een groep.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Hoe kan ik de juiste pool grootte kiezen

De beste grootte voor een pool is afhankelijk van de geaggregeerde resources die nodig zijn voor alle data bases in de groep. Dit omvat het bepalen van het volgende:

- Het maximum aantal resources dat wordt gebruikt door alle data bases in de pool (Maxi maal Dtu's of maximum vCores, afhankelijk van uw keuze van het model voor het hergebruik van resources).
- De maximum opslag in bytes die door alle databases in de groep wordt gebruikt.

Zie voor de beschik bare service lagen voor elk resource model het [op DTU gebaseerde aankoop model](sql-database-service-tiers-dtu.md) of het [op vCore gebaseerde aankoop model](sql-database-service-tiers-vcore.md).

Als u het hulpprogramma niet kunt gebruiken, kunnen de volgende stappen u helpen om te schatten of een groep rendabeler is dan individuele databases:

1. U moet als volgt een schatting maken van de Edtu's-of vCores die nodig zijn voor de groep:

   Voor op DTU gebaseerd inkoop model: MAX(<*Totaal aantal DB's* X *gemiddelde DTU-gebruik per DB*>,<br>  
   <*aantal gelijktijdig piekende databases* X *piek-DTU-gebruik per DB*)

   Voor op vCore gebaseerd inkoop model: MAX (<*totale aantal db's* X- *VCore gebruik per DB*->,<br>  
   <*Aantal gelijktijdig gepiekde db's* X *piek vCore gebruik per data base*)

2. Schat hoeveel opslagruimte de groep nodig heeft door het aantal bytes op te tellen dat nodig is voor alle databases in de groep. Bepaal daarna hoe groot de eDTU-groep moet zijn om aan deze hoeveelheid opslag te voldoen.
3. Neem voor het op DTU gebaseerde aankoop model meer van de eDTU-schattingen uit stap 1 en stap 2. Neem voor het op vCore gebaseerde aankoop model de vCore-schatting uit stap 1.
4. Bekijk de [pagina met prijzen voor SQL database](https://azure.microsoft.com/pricing/details/sql-database/) en zoek de kleinste groeps grootte die groter is dan de schatting van stap 3.
5. Vergelijk de prijs van de groep uit stap 5 met de prijs voor het gebruik van de juiste reken grootten voor afzonderlijke data bases.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Andere SQL Database-functies gebruiken met elastische Pools

### <a name="elastic-jobs-and-elastic-pools"></a>Elastische taken en elastische Pools

Met een groep worden beheertaken vereenvoudigd door scripts in **[elastische taken](elastic-jobs-overview.md)** uit te voeren. Een elastische taak elimineert de meeste saaie handelingen die komen kijken bij grote aantallen databases.

Zie [Scaling out with Azure SQL Database](sql-database-elastic-scale-introduction.md) (Uitbreiden met Azure SQL Database) voor meer informatie over andere databasehulpprogramma's voor het werken met meerdere databases.

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opties voor bedrijfs continuïteit voor data bases in een elastische pool

Pooldatabases ondersteunen in het algemeen dezelfde [bedrijfscontinuïteitsfuncties](sql-database-business-continuity.md) die beschikbaar zijn voor individuele databases.

- **Herstel naar een bepaald tijdstip**

  Herstel naar een bepaald tijdstip maakt gebruik van automatische database back-ups om een data base in een groep te herstellen naar een specifiek tijdstip. Zie [Herstel naar een bepaald tijdstip](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Geo-restore**

  Geo-Restore biedt de standaard herstel optie wanneer een Data Base niet beschikbaar is vanwege een incident in de regio waarin de data base wordt gehost. Zie [Restore an Azure SQL Database or failover to a secondary](sql-database-disaster-recovery.md) (Een Azure SQL Database of herstellen of een failover uitvoeren naar een secundaire server)

- **Actieve Geo-replicatie**

  Voor toepassingen met meer agressieve herstel vereisten dan geo-Restore kunnen bieden, configureert u [actieve geo-replicatie](sql-database-active-geo-replication.md) of een [groep met automatische failover](sql-database-auto-failover-group.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Een nieuwe SQL Database elastische pool maken met behulp van de Azure Portal

Er zijn twee manieren waarop u een elastische pool kunt maken in de Azure Portal.

1. Selecteer **Azure SQL** in het linkermenu van de Azure Portal. Als Azure SQL niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens *Azure SQL* in het zoekvak.
2. Selecteer **+ toevoegen** om de **optie pagina SQL-implementatie selecteren** te openen. U kunt aanvullende informatie over elastische Pools weer geven door **Details weer geven** te selecteren op de tegel **data bases** .
3. Selecteer in de tegel **data bases** de optie **elastische groep** in de vervolg keuzelijst **resource type** en selecteer vervolgens **maken**:

   ![Elastische pool maken](./media/sql-database-elastic-pool/create-elastic-pool.png)


1. U kunt ook een elastische pool maken door te navigeren naar een bestaande Azure SQL-Server en op **+ nieuwe pool** te klikken om een groep rechtstreeks op die server te maken.

> [!NOTE]
> U kunt meerdere groepen maken op een-server, maar het is niet mogelijk om data bases van verschillende servers toe te voegen aan dezelfde groep.

De servicelaag van de pool bepaalt de functies die beschikbaar zijn voor de elastische elementen in de groep en de maximale hoeveelheid beschik bare bronnen voor elke Data Base. Zie resource limieten voor elastische Pools in het DTU- [model](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)voor meer informatie. Zie voor vCore resource limieten voor elastische Pools op [vCore gebaseerde resource limieten: elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md).

Klik op **groep configureren**om de resources en prijs van de groep te configureren. Selecteer vervolgens een servicelaag, voeg data bases toe aan de groep en configureer de resource limieten voor de groep en de bijbehorende data bases.

Wanneer u klaar bent met het configureren van de groep, kunt u op Toep assen klikken, de groep een naam krijgen en op OK klikken om de groep te maken.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Een elastische pool en de bijbehorende data bases bewaken

In de Azure Portal kunt u het gebruik van een elastische pool en de data bases in die groep bewaken. U kunt ook een reeks wijzigingen aanbrengen in uw elastische pool en alle wijzigingen tegelijk verzenden. Deze wijzigingen omvatten het toevoegen of verwijderen van data bases, het wijzigen van de instellingen voor de elastische groep of het wijzigen van de data base-instellingen.

Zoek en open een elastische groep in de portal om te beginnen met het bewaken van uw elastische pool. Er wordt eerst een scherm weer gegeven waarin u een overzicht krijgt van de status van uw elastische pool. Dit omvat:

- Bewakings grafieken met het gebruik van resources van de elastische pool
- Recente waarschuwingen en aanbevelingen, indien beschikbaar, voor de elastische pool

In de volgende afbeelding ziet u een voor beeld van een elastische pool:

![Groeps weergave](./media/sql-database-elastic-pool-manage-portal/basic.png)

Als u meer informatie over de groep wilt, kunt u klikken op een van de beschik bare gegevens in dit overzicht. Als u op het **resource gebruik** -diagram klikt, gaat u naar de weer gave Azure-controle, waar u de metrische gegevens en het tijd venster kunt aanpassen dat in de grafiek wordt weer gegeven. Wanneer u op beschik bare meldingen klikt, gaat u naar een Blade met de volledige details van die waarschuwing of aanbeveling.

Als u de data bases in de pool wilt bewaken, kunt u klikken op **database resource gebruik** in de sectie **bewaking** van het menu resource aan de linkerkant.

![Pagina database resource gebruik](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>De grafiek weergave aanpassen

U kunt het diagram en de metrische pagina bewerken om andere metrische gegevens weer te geven, zoals het CPU-percentage, het IO-percentage en het gebruikte logboek-IO-percentage.

Op het formulier **grafiek bewerken** kunt u een vast tijds bereik selecteren of op **aangepast** klikken om een 24-uurs venster in de afgelopen twee weken te selecteren. vervolgens selecteert u de resources die u wilt bewaken.

### <a name="to-select-databases-to-monitor"></a>Te bewaken data bases selecteren

Standaard worden in de grafiek in de Blade **database resource gebruik** de top 5-data bases weer gegeven op DTU of CPU (afhankelijk van uw servicelaag). U kunt de data bases in deze grafiek wijzigen door data bases in de lijst onder de grafiek te selecteren en te deselecteren via de selectie vakjes aan de linkerkant.

U kunt ook meer metrische gegevens selecteren om naast elkaar in deze database tabel weer te geven om een beter beeld te krijgen van de prestaties van uw data bases.

Zie [SQL database-waarschuwingen in azure portal maken](sql-database-insights-alerts-portal.md)voor meer informatie.

## <a name="customer-case-studies"></a>Casestudy's van klanten

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart gebruikt elastische Pools met Azure SQL Database om de zakelijke services snel uit te breiden tegen een snelheid van 1.000 nieuwe Azure SQL-data bases per maand.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco maakt gebruik van elastische Pools met Azure SQL Database om services snel in te richten en te schalen voor duizenden tenants in de Cloud.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)    

   Daxko/CSI maakt gebruik van elastische Pools met Azure SQL Database om de ontwikkelings cyclus te versnellen en de klant Services en-prestaties te verbeteren.   

## <a name="next-steps"></a>Volgende stappen

- Zie [elastische](sql-database-elastic-pool-scale.md) Pools schalen en [een elastische pool schalen-voorbeeld code](scripts/sql-database-monitor-and-scale-pool-powershell.md) om elastische Pools te schalen.
- Zie voor een video [micro soft Virtual Academy-video cursus over Azure SQL database Elastic-mogelijkheden](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) voor meer informatie over ontwerppatronen voor SaaS-toepassingen met elastische pools.
- Zie [Introduction to the Wingtip SaaS Application](sql-database-wtp-overview.md)voor een SaaS-zelf studie over het gebruik van elastische Pools.
