---
title: Inzicht in queryprestaties
description: Queryprestatiebewaking identificeert de meest CPU-verbruikende en langlopende query's voor afzonderlijke en gepoolde databases in Azure SQL-database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: f5998fde6659715de4fcb533cb0f41a8939b1c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214047"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Queryprestatieinsight voor Azure SQL-database

Query Performance Insight biedt intelligente queryanalyse voor afzonderlijke en gepoolde databases. Het helpt bij het identificeren van de beste resource verbruikende en langlopende query's in uw werkbelasting. Dit helpt u bij het vinden van de query's om te optimaliseren om de algehele werkbelastingprestaties te verbeteren en de resource waarvoor u betaalt efficiënt te gebruiken. Met Query Performance Insight u minder tijd besteden aan het oplossen van problemen met de databaseprestaties door:

* Dieper inzicht in het verbruik van uw databases resource (DTU)
* Details over topdatabasequery's per CPU, duur en uitvoeringstelling (potentiële tuningkandidaten voor prestatieverbeteringen)
* De mogelijkheid om in te zoomen op details van een query, om de querytekst en de geschiedenis van het gebruik van resources weer te geven
* Annotaties die prestatieaanbevelingen van [databaseadviseurs](sql-database-advisor.md) weergeven

![Inzicht in queryprestaties](./media/sql-database-query-performance/opening-title.png)

## <a name="prerequisites"></a>Vereisten

Queryprestatieinzicht vereist dat [Query Store](https://msdn.microsoft.com/library/dn817826.aspx) actief is in uw database. Het is standaard automatisch ingeschakeld voor alle Azure SQL-databases. Als de Query Store niet wordt uitgevoerd, wordt u in de Azure-portal gevraagd deze in te schakelen.

> [!NOTE]
> Zie De [configuratie van de Querystore optimaliseren](#optimize-the-query-store-configuration)als het bericht 'Queryarchief niet goed is geconfigureerd in deze database' in de portal wordt weergegeven.

## <a name="permissions"></a>Machtigingen

U hebt de volgende [toegangsbeheermachtigingen](../role-based-access-control/overview.md) voor rollen nodig om Query Performance Insight te gebruiken:

* **Reader,** **Owner**, **Contributor**, **SQL DB Contributor**of SQL Server **Contributor** permissions are required to view the top resource-consuming queries and charts.
* **Er**zijn machtigingen voor **eigenaar, inzender,** **SQL DB-bijdrager**of **SQL Server-inzender** vereist om querytekst weer te geven.

## <a name="use-query-performance-insight"></a>Query Performance Insight gebruiken

Query Performance Insight is eenvoudig te gebruiken:

1. Open de [Azure-portal](https://portal.azure.com/) en zoek een database die u wilt onderzoeken.
2. Open **Intelligent Performance** > **Query Performance Insight**in het menu aan de linkerkant.
  
   ![Inzicht in queryprestaties in het menu](./media/sql-database-query-performance/tile.png)

3. Bekijk op het eerste tabblad de lijst met de belangrijkste resourceverbruikende query's.
4. Selecteer een afzonderlijke query om de details weer te geven.
5. Open **aanbevelingen voor intelligente** > **prestaties** en controleer of er prestatieaanbevelingen beschikbaar zijn. Zie [SQL Database Advisor](sql-database-advisor.md)voor meer informatie over ingebouwde prestatieaanbevelingen.
6. Gebruik schuifregelaars of zoompictogrammen om het waargenomen interval te wijzigen.

   ![Prestatiedashboard](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Als SQL Database de informatie in Query Performance Insight wilt renderen, moet Query Store een paar uur aan gegevens vastleggen. Als de database geen activiteit heeft of als het queryarchief gedurende een bepaalde periode niet actief was, zijn de grafieken leeg wanneer queryprestatieinzicht dat tijdsbereik weergeeft. U Query Store op elk gewenst moment inschakelen als deze niet wordt uitgevoerd. Zie [Aanbevolen procedures met queryarchief](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store)voor meer informatie.
>

Selecteer [Aanbevelingen](sql-database-advisor.md) voor het navigatieblad queryprestatieinzicht voor aanbevelingen voor databaseprestaties.

![Het tabblad Aanbevelingen](./media/sql-database-query-performance/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>Bekijk de belangrijkste CPU-verbruikende query's

Queryperformance Insight toont standaard de vijf belangrijkste CPU-verbruikende query's wanneer u deze voor het eerst opent.

1. Schakel afzonderlijke query's in of uit om deze op te nemen of uit te sluiten van de grafiek met behulp van selectievakjes.

   De bovenste regel toont het totale DTU-percentage voor de database. De balken tonen het CPU-percentage dat de geselecteerde query's verbruikt en die tijdens het geselecteerde interval zijn verbruikt. Als afgelopen **week** is geselecteerd, vertegenwoordigt elke balk bijvoorbeeld één dag.

   ![Topquery's](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > De getoonde DTU-lijn wordt samengevoegd tot een maximale verbruikswaarde in perioden van één uur. Het is bedoeld voor een vergelijking op hoog niveau alleen met query uitvoeringsstatistieken. In sommige gevallen lijkt het Gebruik van DTU misschien te hoog in vergelijking met uitgevoerde query's, maar dit is mogelijk niet het geval.
   >
   > Als een query bijvoorbeeld een paar minuten lang maximaal 100% heeft, wordt in de DTU-regel in Query Performance Insight het hele uur verbruik weergegeven als 100% (het gevolg van de maximale geaggregeerde waarde).
   >
   > Voor een fijnere vergelijking (tot één minuut) u overwegen een aangepaste DTU-gebruiksgrafiek te maken:
   >
   > 1. Selecteer Azure SQL Database**Monitoring**in de **Azure-portal** > .
   > 2. Selecteer **Metrische gegevens**.
   > 3. Selecteer **+Grafiek toevoegen**.
   > 4. Selecteer het DTU-percentage in de grafiek.
   > 5. Selecteer Bovendien **Laatste 24 uur** in het menu linksboven en wijzig deze in één minuut.
   >
   > Gebruik de aangepaste DTU-grafiek met een fijner detailniveau om te vergelijken met het queryuitvoeringsdiagram.

   Het onderste raster toont geaggregeerde informatie voor de zichtbare query's:

   * Query-id, een unieke id voor de query in de database.
   * CPU per query tijdens een waarneembaar interval, dat afhankelijk is van de aggregatiefunctie.
   * Duur per query, die ook afhankelijk is van de aggregatiefunctie.
   * Totaal aantal uitvoeringen voor een specifieke query.

2. Als uw gegevens verouderd worden, selecteert u de knop **Vernieuwen.**

3. Gebruik schuifregelaars en zoomknoppen om het observatie-interval te wijzigen en verbruikspieken te onderzoeken:

   ![Schuifregelaars en zoomknoppen voor het wijzigen van het interval](./media/sql-database-query-performance/zoom.png)

4. Optioneel u het tabblad **Aangepast** selecteren om de weergave aan te passen voor:

   * Metric (CPU, duur, uitvoering telling).
   * Tijdsinterval (laatste 24 uur, afgelopen week of afgelopen maand).
   * Aantal query's.
   * Aggregatie, functie.
  
   ![Tabblad Aangepast](./media/sql-database-query-performance/custom-tab.png)
  
5. Selecteer de knop **Ga >** om de aangepaste weergave weer te geven.

   > [!IMPORTANT]
   > Queryperformance Insight is beperkt tot het weergeven van de top 5-20 verbruikende query's, afhankelijk van uw selectie. Uw database kan veel meer query's uitvoeren dan de bovenste query's die worden weergegeven, en deze query's worden niet opgenomen in de grafiek.
   >
   > Er kan een databasewerkbelastingtype bestaan waarin veel kleinere query's, buiten de weergegeven bovenste query's, vaak worden uitgevoerd en de meerderheid van de DTU gebruiken. Deze query's worden niet weergegeven in het prestatiediagram.
   >
   > Een query kan bijvoorbeeld een tijdje een aanzienlijke hoeveelheid DTU hebben verbruikt, hoewel het totale verbruik in de waargenomen periode lager is dan de andere topverbruikende query's. In een dergelijk geval wordt het gebruik van deze query in resources niet weergegeven in de grafiek.
   >
   > Als u inzicht wilt krijgen in de hoogste queryuitvoeringen buiten de beperkingen van Query Performance Insight, u [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) gebruiken voor geavanceerde databaseprestatiebewaking en probleemoplossing.
   >

## <a name="view-individual-query-details"></a>Afzonderlijke querydetails weergeven

Ga als u de details van de query weergeven:

1. Selecteer een query in de lijst met topquery's.

    ![Lijst met topquery's](./media/sql-database-query-performance/details.png)

   Er wordt een gedetailleerde weergave geopend. Het toont het CPU-verbruik, de duur en de uitvoering tellen in de tijd.

2. Selecteer de grafiekfuncties voor meer informatie.

   * De bovenste grafiek toont een lijn met het totale DTU-percentage van de database. De balken zijn het CPU-percentage dat de geselecteerde query heeft verbruikt.
   * In het tweede diagram wordt de totale duur van de geselecteerde query weergegeven.
   * Het onderste diagram toont het totale aantal uitvoeringen door de geselecteerde query.

   ![Querydetails](./media/sql-database-query-performance/query-details.png)

3. Gebruik desgewenst schuifregelaars, gebruik zoomknoppen of selecteer **Instellingen** om aan te passen hoe querygegevens worden weergegeven of om een ander tijdsbereik te kiezen.

   > [!IMPORTANT]
   > QueryPerformance Insight legt geen DDL-query's vast. In sommige gevallen worden mogelijk niet alle ad-hocquery's vastgelegd.
   >

## <a name="review-top-queries-per-duration"></a>Topquery's per duur controleren

Met twee statistieken in Query Performance Insight u potentiële knelpunten vinden: duur en uitvoeringtellen.

Langlopende query's hebben het grootste potentieel om resources langer te vergrendelen, andere gebruikers te blokkeren en de schaalbaarheid te beperken. Ze zijn ook de beste kandidaten voor optimalisatie.

Ga als u op de lange termijn query's identificeren:

1. Open het tabblad **Aangepast** in Queryprestatieinzicht voor de geselecteerde database.
2. Wijzig de statistieken in **duur**.
3. Selecteer het aantal query's en het observatie-interval.
4. Selecteer de aggregatiefunctie:

   * **Som** telt alle queryuitvoeringstijd op voor het hele observatie-interval.
   * **Max** vindt query's waarin de uitvoeringstijd maximaal was voor het hele observatie-interval.
   * **Avg** vindt de gemiddelde uitvoeringstijd van alle queryuitvoeringen en toont u de hoogste voor deze gemiddelden.

   ![Queryduur](./media/sql-database-query-performance/top-duration.png)

5. Selecteer de knop **Ga >** om de aangepaste weergave weer te geven.

   > [!IMPORTANT]
   > Als u de queryweergave aanpast, wordt de DTU-regel niet bijgewerkt. De DTU-lijn geeft altijd de maximale verbruikswaarde voor het interval weer.
   >
   > Als u het DTU-verbruik van de database met meer details wilt begrijpen (tot één minuut), u overwegen een aangepaste grafiek te maken in de Azure-portal:
   >
   > 1. Selecteer **Azure SQL-databasebewaking** > **Monitoring**.
   > 2. Selecteer **Metrische gegevens**.
   > 3. Selecteer **+Grafiek toevoegen**.
   > 4. Selecteer het DTU-percentage in de grafiek.
   > 5. Selecteer Bovendien **Laatste 24 uur** in het menu linksboven en wijzig deze in één minuut.
   >
   > We raden u aan de aangepaste DTU-grafiek te gebruiken om te vergelijken met het queryprestatiediagram.
   >

## <a name="review-top-queries-per-execution-count"></a>Topquery's controleren per uitvoeringsaantal

Een gebruikerstoepassing die de database gebruikt, kan traag worden, ook al heeft een groot aantal uitvoeringen mogelijk geen invloed op de database zelf en is het gebruik van resources laag.

In sommige gevallen kan een hoog aantal uitvoeringen leiden tot meer netwerkretouren. Rondritten beïnvloeden de prestaties. Ze zijn onderhevig aan netwerklatentie en downstream serverlatentie.

Veel datagestuurde websites hebben bijvoorbeeld veel toegang tot de database voor elke gebruikersaanvraag. Hoewel het bundelen van verbindingen helpt, kunnen de toegenomen netwerkverkeer- en verwerkingsbelasting op de databaseserver de prestaties vertragen. In het algemeen, houden rondreizen tot een minimum.

Ga als lid van het onderzoek naar veeluitgevoerde query's ('chatty'):

1. Open het tabblad **Aangepast** in Queryprestatieinzicht voor de geselecteerde database.
2. Wijzig de statistieken in **het aantal uitvoeringen**.
3. Selecteer het aantal query's en het observatie-interval.
4. Selecteer de knop **Ga >** om de aangepaste weergave weer te geven.

   ![Aantal queryuitvoeringen](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Inzicht in prestatieafstemmingsannotaties

Tijdens het verkennen van uw werkbelasting in Query Performance Insight ziet u mogelijk pictogrammen met een verticale lijn boven op de grafiek.

Deze pictogrammen zijn annotaties. Ze tonen prestatieaanbevelingen van [SQL Database Advisor.](sql-database-advisor.md) Door over een annotatie te zweven, u samengevatte informatie krijgen over prestatieaanbevelingen.

   ![Query-annotatie](./media/sql-database-query-performance/annotation.png)

Als u meer wilt begrijpen of de aanbeveling van de adviseur wilt toepassen, selecteert u het pictogram om details van de aanbevolen actie te openen. Als dit een actieve aanbeveling is, u deze direct vanaf de portal toepassen.

   ![Details van query-annotatie](./media/sql-database-query-performance/annotation-details.png)

In sommige gevallen, als gevolg van het zoomniveau, is het mogelijk dat annotaties dicht bij elkaar worden samengevouwen in een enkele annotatie. Query Performance Insight vertegenwoordigt dit als een groepsannotatiepictogram. Als u het groepsaantekeningspictogram selecteert, wordt een nieuw blad geopend met de aantekeningen.

Door query's en prestatieafstemmingsacties te correleren, u uw werklast beter begrijpen.

## <a name="optimize-the-query-store-configuration"></a>De configuratie van de Query Store optimaliseren

Tijdens het gebruik van QueryPerformance Insight ziet u mogelijk de volgende foutberichten voor queryopslag:

* "Query Store is niet goed geconfigureerd in deze database. Klik hier voor meer informatie."
* "Query Store is niet goed geconfigureerd in deze database. Klik hier om de instellingen te wijzigen."

Deze berichten worden meestal weergegeven wanneer Query Store geen nieuwe gegevens kan verzamelen.

Het eerste geval gebeurt wanneer Query Store is in de alleen-lezen status en parameters optimaal zijn ingesteld. U dit oplossen door de grootte van het gegevensarchief te vergroten of door queryarchief te wissen. (Als u de Query Store wist, gaan alle eerder verzamelde telemetrie verloren.)

   ![Querywinkelgegevens](./media/sql-database-query-performance/qds-off.png)

Het tweede geval gebeurt wanneer queryarchief niet is ingeschakeld of parameters niet optimaal zijn ingesteld. U het bewaar- en opnamebeleid wijzigen en ook queryarchief inschakelen door de volgende opdrachten uit te voeren die zijn uitgevoerd vanuit [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) of de Azure-portal.

### <a name="recommended-retention-and-capture-policy"></a>Aanbevolen bewaar- en vastleggensbeleid

Er zijn twee soorten bewaarbeleid:

* **Grootte gebaseerd:** Als dit beleid is ingesteld op **AUTO,** worden gegevens automatisch opschonen wanneer de bijna maximale grootte is bereikt.
* **Time based**: Standaard is dit beleid ingesteld op 30 dagen. Als de ruimte van de querywinkel op is, worden querygegevens ouder dan 30 dagen verwijderd.

U het opnamebeleid instellen op:

* **Alle:** Query Store legt alle query's vast.
* **Automatisch:** Query Store negeert onregelmatige query's en query's met een onbeduidende compilerings- en uitvoeringsduur. Drempels voor het aantal uitvoeringen, de compilerduur en de duur van de runtime worden intern bepaald. Dit is de standaardoptie.
* **Geen:** Query Store stopt met het vastleggen van nieuwe query's, maar runtime-statistieken voor reeds vastgelegde query's worden nog steeds verzameld.

We raden u aan alle beleidsregels in te stellen op **AUTO** en het reinigingsbeleid op 30 dagen door de volgende opdrachten uit te voeren van [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) of de Azure-portal. (Vervangen `YourDB` door de databasenaam.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Vergroot de grootte van de Query Store door verbinding te maken met een database via [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) of de Azure-portal en de volgende query uit te voeren. (Vervangen `YourDB` door de databasenaam.)

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Als u deze instellingen toepast, verzamelt U In query's uiteindelijk telemetrie voor nieuwe query's. Als u queryarchief meteen operationeel wilt maken, u er optioneel voor kiezen om de Query Store te wissen door de volgende query uit te voeren via SSMS of de Azure-portal. (Vervangen `YourDB` door de databasenaam.)

> [!NOTE]
> Als u de volgende query uitvoert, worden alle eerder verzamelde bewaakte telemetrie in de Query Store verwijderd.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>Volgende stappen

Overweeg [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) te gebruiken voor geavanceerde prestatiebewaking van een groot wagenpark van afzonderlijke en gepoolde databases, elastische pools, beheerde instanties en instantiedatabases.
