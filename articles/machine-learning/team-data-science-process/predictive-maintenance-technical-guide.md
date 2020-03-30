---
title: Gids voor voorspellend onderhoud voor de lucht- en ruimtevaart - Team Data Science Process
description: Een technische handleiding voor de oplossingssjabloon voor voorspellend onderhoud in de lucht- en ruimtevaart, nutsbedrijven en transport.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 0542106f70e96b6c2f63e8ca03d2532de191d365
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477167"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Technische handleiding voor de oplossingssjabloon voor voorspellend onderhoud in de lucht- en ruimtevaart

> [!Important]
> Dit artikel is afgeschaft. De discussie over Predictive Maintenance in Aerospace is nog steeds relevant, maar voor actuele informatie verwijzen we naar [Solution Overview for Business Audiences](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Oplossingssjablonen zijn ontworpen om het proces van het bouwen van een E2E-demo te versnellen. Een geïmplementeerde sjabloon voorziet uw abonnement met de benodigde componenten en bouwt vervolgens de relaties tussen deze onderdelen op. Het zaden ook de gegevenspijplijn met voorbeeldgegevens van een gegevensgeneratortoepassing, die u downloadt en installeert op uw lokale machine nadat u de oplossingssjabloon hebt geïmplementeerd. De gegevens van de generator hydrateren de gegevenspijplijn en beginnen met het genereren van machine learning-voorspellingen, die vervolgens kunnen worden gevisualiseerd op het Power BI-dashboard.

Het implementatieproces leidt u door verschillende stappen om uw oplossingsreferenties in te stellen. Zorg ervoor dat u de referenties registreert, zoals de naam van de oplossing, de gebruikersnaam en het wachtwoord dat u tijdens de implementatie opgeeft. 


De doelstellingen van dit artikel zijn:
- Beschrijf de referentiearchitectuur en onderdelen die in uw abonnement zijn ingericht.
- Laat zien hoe u de voorbeeldgegevens vervangen door uw eigen gegevens. 
- Laat zien hoe u de oplossingssjabloon wijzigt.  

## <a name="overview"></a>Overzicht
![Voorspellende onderhoudsarchitectuur](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

Wanneer u de oplossing implementeert, activeert deze Azure-services, waaronder Event Hub, Stream Analytics, HDInsight, Data Factory en Machine Learning. Het architectuurdiagram laat zien hoe de Predictive Maintenance for Aerospace Solution Template is opgebouwd. U deze services in de Azure-portal onderzoeken door erop te klikken in het oplossingssjabloondiagram dat is gemaakt met de implementatie van de oplossing (met uitzondering van HDInsight, dat op aanvraag is ingericht wanneer de gerelateerde pijplijnactiviteiten moeten worden uitgevoerd en daarna verwijderd).
Download een [volledige versie van het diagram.](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png)

In de volgende secties worden de oplossingsonderdelen beschreven.

## <a name="data-source-and-ingestion"></a>Gegevensbron en inname
### <a name="synthetic-data-source"></a>Synthetische gegevensbron
Voor deze sjabloon wordt de gebruikte gegevensbron gegenereerd uit een gedownloade bureaubladtoepassing die u lokaal uitvoert na een succesvolle implementatie.

Als u de instructies wilt vinden om deze toepassing te downloaden en te installeren, selecteert u het eerste knooppunt, Predictive Maintenance Data Generator, op het sjabloondiagram voor de oplossing. De instructies zijn te vinden in de balk Eigenschappen. Deze toepassing voedt de [Azure Event Hub-service](#azure-event-hub) met gegevenspunten of gebeurtenissen die worden gebruikt in de rest van de oplossingsstroom. Deze gegevensbron is afgeleid van openbaar beschikbare gegevens uit de [NASA-gegevensrepository](https://c3.nasa.gov/dashlink/resources/139/) met behulp van de [Turbofan Engine Degradation Simulation Data Set](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

De toepassing voor het genereren van gebeurtenissen wordt alleen gevuld met de Azure Event Hub terwijl deze wordt uitgevoerd op uw computer.  

### <a name="azure-event-hub"></a>Azure Event Hub  
De [Azure Event Hub-service](https://azure.microsoft.com/services/event-hubs/) is de ontvanger van de invoer die wordt geleverd door de synthetische gegevensbron.

## <a name="data-preparation-and-analysis"></a>Voorbereiding en analyse van gegevens  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Gebruik [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) om bijna realtime analyses te leveren op de invoerstream van de Azure Event [Hub-service.](#azure-event-hub) Vervolgens publiceert u resultaten op een [Power BI-dashboard](https://powerbi.microsoft.com) en archiveert u alle onbewerkte binnenkomende gebeurtenissen naar de [Azure Storage-service](https://azure.microsoft.com/services/storage/) voor latere verwerking door de [Azure Data Factory-service.](https://azure.microsoft.com/documentation/services/data-factory/)

### <a name="hdinsight-custom-aggregation"></a>HdInsight aangepaste aggregatie
Voer [Hive-scripts](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) uit (georkestreerd door Azure Data Factory) met HDInsight om aggregaties te verstrekken over de ruwe gebeurtenissen die zijn gearchiveerd met behulp van de Azure Stream Analytics-bron.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Maak voorspellingen over de resterende gebruiksduur (RUL) van een bepaalde vliegtuigengine met behulp van de ingangen die zijn ontvangen met [Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning/) (georkestreerd door Azure Data Factory). 

## <a name="data-publishing"></a>Het publiceren van gegevens
### <a name="azure-sql-database"></a>Azure SQL Database
Gebruik [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) om de voorspellingen op te slaan die zijn ontvangen door de Azure Machine Learning, die vervolgens worden verbruikt in het Power [BI-dashboard.](https://powerbi.microsoft.com)

## <a name="data-consumption"></a>Gegevensverbruik
### <a name="power-bi"></a>Power BI
Gebruik [Power BI](https://powerbi.microsoft.com) om een dashboard weer te geven dat aggregaties en waarschuwingen bevat die worden geleverd door Azure Stream [Analytics,](https://azure.microsoft.com/services/stream-analytics/)evenals RUL-voorspellingen die zijn opgeslagen in [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) die zijn geproduceerd met Azure [Machine Learning.](https://azure.microsoft.com/services/machine-learning/)

## <a name="how-to-bring-in-your-own-data"></a>Hoe breng je je eigen data binnen?
In dit gedeelte wordt beschreven hoe u uw eigen gegevens naar Azure brengen en welke gebieden moeten worden gewijzigd voor de gegevens die u in deze architectuur meeneemt.

Het is onwaarschijnlijk dat uw gegevensset overeenkomt met de gegevensset die wordt gebruikt door de [Turbofan Engine Degradation Simulation Data Set](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) die wordt gebruikt voor deze oplossingssjabloon. Inzicht in uw gegevens en de vereisten zijn cruciaal in de manier waarop u deze sjabloon wijzigt om met uw eigen gegevens te werken. 

In de volgende secties worden de onderdelen van de sjabloon besproken die moeten worden gewijzigd wanneer een nieuwe gegevensset wordt geïntroduceerd.

### <a name="azure-event-hub"></a>Azure Event Hub
Azure Event Hub is generiek; gegevens kunnen worden geplaatst op de hub in csv- of JSON-indeling. Er vindt geen speciale verwerking plaats in de Azure Event Hub, maar het is belangrijk dat u de gegevens begrijpt die erin worden ingevoerd.

In dit document wordt niet beschreven hoe u uw gegevens innemen, maar u eenvoudig gebeurtenissen of gegevens naar een Azure Event Hub verzenden met behulp van de Api's voor gebeurtenishubs.

### <a name="azure-stream-analytics"></a><a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Gebruik de Azure Stream Analytics-bron om bijna realtime analyses te bieden door gegevensstromen te lezen en gegevens uit te delen naar een willekeurig aantal bronnen.

Voor de sjabloon Predictive Maintenance for Aerospace Solution bestaat de Azure Stream Analytics-query uit vier subquery's, waarbij elke query gebeurtenissen uit de Azure Event Hub-service verbruikt, met uitvoer naar vier verschillende locaties. Deze uitgangen bestaan uit drie Power BI-gegevenssets en één Azure Storage-locatie.

De Azure Stream Analytics-query is te vinden op:

* Verbinding maken met de Azure-portal
* Het streamanalytics-pictogram ![](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png) Stream Analytics zoeken dat is gegenereerd toen de oplossing werd geïmplementeerd (*bijvoorbeeld* **maintenancesa02asapbi** en **maintenancesa02asablob** voor de voorspellende onderhoudsoplossing)
* Selecteren
  
  * ***INPUTS*** om de queryinvoer weer te geven
  * ***QUERY*** om de query zelf weer te geven
  * ***UITGANGEN*** om de verschillende uitgangen weer te geven

U vindt informatie over Azure Stream Analytics-queryconstructie in de [queryverwijzing streamanalytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) op MSDN.

In deze oplossing worden in de query's drie gegevenssets uitgevoerd met bijna realtime analysegegevens over de binnenkomende gegevensstroom naar een Power BI-dashboard dat als onderdeel van deze oplossingssjabloon wordt geleverd. Omdat er impliciete kennis is over de binnenkomende gegevensindeling, moeten deze query's worden gewijzigd op basis van uw gegevensindeling.

De query in de tweede Stream Analytics-taakonderhoudsbeurtena02asablob voert eenvoudig alle [Event Hub-gebeurtenissen](https://azure.microsoft.com/services/event-hubs/) uit naar [Azure Storage](https://azure.microsoft.com/services/storage/) en vereist dus geen wijziging, ongeacht uw gegevensindeling, omdat de volledige gebeurtenisgegevens naar opslag worden gestreamd. **maintenancesa02asablob**

### <a name="azure-data-factory"></a>Azure Data Factory
De [Azure Data Factory-service](https://azure.microsoft.com/documentation/services/data-factory/) orkestreert de verplaatsing en verwerking van gegevens. In de Predictive Maintenance for Aerospace Solution Template bestaat de datafabriek uit drie [pijpleidingen](../../data-factory/concepts-pipelines-activities.md) die de gegevens verplaatsen en verwerken met behulp van verschillende technologieën.  Toegang tot uw gegevensfabriek door het knooppunt Gegevensfabriek onder aan het oplossingssjabloondiagram te openen dat is gemaakt met de implementatie van de oplossing. Fouten onder uw gegevenssets zijn te wijten aan gegevensfabriek wordt geïmplementeerd voordat de gegevensgenerator werd gestart. Deze fouten kunnen worden genegeerd en verhinderen niet dat uw gegevensfabriek functioneert.

![Gegevensfabriekgegevenssetfouten](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

In deze sectie worden de benodigde [pijplijnen en activiteiten](../../data-factory/concepts-pipelines-activities.md) in de [Azure Data Factory besproken.](https://azure.microsoft.com/documentation/services/data-factory/) Hier is een diagramweergave van de oplossing.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

Twee van de pijplijnen van deze fabriek bevatten [Hive-scripts](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) die worden gebruikt om de gegevens te partitioneren en te aggregeren. Wanneer u dit opvalt, bevinden de scripts zich in het [Azure Storage-account](https://azure.microsoft.com/services/storage/) dat tijdens de installatie is gemaakt. Hun locatie is: maintenancesascript script\\\\\\\\hive\\ \\ (of https://[Your solution name].blob.core.windows.net/maintenancesascript).

Net als bij Azure Stream Analytics-query's hebben de [Hive-scripts](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) impliciete kennis over de binnenkomende gegevensindeling en moeten ze worden gewijzigd op basis van uw gegevensindeling. [Azure Stream Analytics](#azure-stream-analytics-1)

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Deze [pijplijn](../../data-factory/concepts-pipelines-activities.md) bevat één activiteit: een [HDInsightHive-activiteit](../../data-factory/transform-data-using-hadoop-hive.md) met een [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) waarmee een [Hive-script](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) wordt uitgevoerd om de gegevens in Azure Storage te [partitioneren](https://azure.microsoft.com/services/storage/) tijdens de [Azure Stream Analytics-taak.](https://azure.microsoft.com/services/stream-analytics/)

Het [Hive-script](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) voor deze partitionertaak is ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline MLScoringPipeline*
Deze [pijplijn](../../data-factory/concepts-pipelines-activities.md) bevat verschillende activiteiten waarvan het eindresultaat de gescoorde voorspellingen is van het [Azure Machine Learning-experiment](https://azure.microsoft.com/services/machine-learning/) dat is gekoppeld aan deze oplossingssjabloon.

Tot de activiteiten behoren:

* [HDInsightHive-activiteit](../../data-factory/transform-data-using-hadoop-hive.md) met behulp van een [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) die een [Hive-script](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) uitvoert om aggregaties uit te voeren en functie-engineering die nodig zijn voor het [Azure Machine Learning-experiment.](https://azure.microsoft.com/services/machine-learning/)
  Het [Hive-script](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) voor deze partitionertaak is ***PrepareMLInput.hql***.
* [Kopieer](https://msdn.microsoft.com/library/azure/dn835035.aspx) activiteit waarmee de resultaten van de [HDInsightHive-activiteit](../../data-factory/transform-data-using-hadoop-hive.md) worden verplaatst naar één [Azure Storage-blob](https://azure.microsoft.com/services/storage/) die wordt geopend door de [AzureMLBatchScoring-activiteit.](https://msdn.microsoft.com/library/azure/dn894009.aspx)
* [AzureMLBatchScoring activity](https://msdn.microsoft.com/library/azure/dn894009.aspx) calls the [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experiment, with results put in a single Azure [Storage](https://azure.microsoft.com/services/storage/) blob.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Deze [pijplijn](../../data-factory/concepts-pipelines-activities.md) bevat één activiteit: een [kopieeractiviteit](https://msdn.microsoft.com/library/azure/dn835035.aspx) waarmee de resultaten van het [Azure Machine Learning-experiment](#azure-machine-learning) worden verplaatst van de ***MLScoringPipeline*** naar de [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) die is ingericht als onderdeel van de installatie van de oplossingssjabloon.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Het [Azure Machine Learning-experiment](https://azure.microsoft.com/services/machine-learning/) dat voor deze oplossingssjabloon wordt gebruikt, biedt de resterende gebruiksduur (RUL) van een vliegtuigmotor. Het experiment is specifiek voor de gebruikte gegevensset en vereist wijziging of vervanging die specifiek is voor de ingebrachte gegevens.

## <a name="monitor-progress"></a>Voortgang controleren
Zodra de gegevensgenerator is gestart, begint de pijplijn uit te drogen en beginnen de verschillende onderdelen van uw oplossing in actie te komen na de opdrachten die door de gegevensfabriek zijn uitgegeven. Er zijn twee manieren om de pijplijn te controleren.

* Een van de Stream Analytics-taken schrijft de ruwe binnenkomende gegevens naar blob-opslag. Als u vanaf het scherm op blobopslagcomponent van uw oplossing klikt die u met succes hebt geïmplementeerd en vervolgens in het rechterdeelvenster op Openen klikt, gaat u naar de [Azure-portal.](https://portal.azure.com/) Eenmaal daar, klik op Blobs. In het volgende deelvenster ziet u een lijst met containers. Klik op **maintenancesadata**. In het volgende paneel is de **rawdata** map. In de map rawdata bevinden zich mappen met namen als hour=17 en hour=18. De aanwezigheid van deze mappen geeft aan dat er ruwe gegevens worden gegenereerd op uw computer en worden opgeslagen in blob-opslag. U ziet csv-bestanden met eindige groottes in MB in die mappen.
* De laatste stap van de pijplijn is het schrijven van gegevens (bijvoorbeeld voorspellingen van machine learning) in SQL Database. Mogelijk moet u maximaal drie uur wachten tot de gegevens in SQL Database worden weergegeven. Een manier om te controleren hoeveel gegevens beschikbaar zijn in uw SQL-database is via de [Azure-portal.](https://portal.azure.com/) Zoek in het linkerdeelvenster ![sql-databases-pictogram](./media/predictive-maintenance-technical-guide/icon-SQL-databases.png) en klik erop. Zoek dan uw database **pmaintenancedb** en klik erop. Klik op de volgende pagina onderaan op MANAGE.
   
    ![Pictogram Beheren](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    Hier u klikken op Nieuwe query en query voor het aantal rijen (bijvoorbeeld selecteer aantal(*) van PMResult). Naarmate uw database groeit, moet het aantal rijen in de tabel toenemen.

## <a name="power-bi-dashboard"></a>Power BI Dashboard

Stel een Power BI-dashboard in om uw Azure Stream Analytics-gegevens (hot path) en batchvoorspellingsresultaten van Azure machine learning (cold path) te visualiseren.

### <a name="set-up-the-cold-path-dashboard"></a>Het dashboard van het koudepad instellen
In de cold path data pipeline, het doel is om de voorspellende RUL (resterende nuttige levensduur) van elke vliegtuigmotor te krijgen zodra het klaar is met een vlucht (cyclus). Het voorspellingsresultaat wordt elke 3 uur bijgewerkt voor het voorspellen van de vliegtuigmotoren die de afgelopen 3 uur een vlucht hebben voltooid.

Power BI maakt verbinding met een Azure SQL Database als gegevensbron, waar de voorspellingsresultaten worden opgeslagen. 

Opmerking: 
1.    Bij het implementeren van uw oplossing verschijnt binnen 3 uur een voorspelling in de database. Het pbix-bestand dat bij de generatordownload is opgenomen, bevat een aantal zaadgegevens, zodat u het Power BI-dashboard meteen maken. 
2.    In deze stap is de voorwaarde om de gratis software [Power BI-desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)te downloaden en te installeren.

In de volgende stappen u het pbix-bestand verbinden met de SQL-database die is opgebouwd op het moment van de implementatie van de oplossing met gegevens (bijvoorbeeld voorspellingsresultaten) voor visualisatie.

1. Haal de databasereferenties op.
   
   U hebt **de naam, de naam van de database, de gebruikersnaam en het wachtwoord** nodig voordat u naar de volgende stappen gaat. Hier zijn de stappen om u te begeleiden hoe ze te vinden.
   
   * Zodra **'Azure SQL Database'** op uw oplossingssjabloondiagram groen wordt, klikt u erop en klikt u vervolgens op **'Openen'.**
   * U ziet een nieuw browsertabblad/venster met de Azure-portalpagina. Klik op **'Resourcegroepen'** in het linkerdeelvenster.
   * Selecteer het abonnement dat u gebruikt voor het implementeren van de oplossing en selecteer **vervolgens 'YourSolutionName\_ResourceGroup'.**
   * Klik in het nieuwe pop-outdeelvenster op het ![SQL-pictogram](./media/predictive-maintenance-technical-guide/icon-sql.png) om toegang te krijgen tot uw database. Uw databasenaam bevindt zich naast dit pictogram (bijvoorbeeld **'pmaintenancedb'**) en de naam van de **databaseserver** wordt vermeld onder de eigenschap Servernaam en moet er vergelijkbaar uitzien met **YourSolutionName.database.windows.net**.
   * Uw **gebruikersnaam** en **wachtwoord** in uw database zijn dezelfde als de gebruikersnaam en het wachtwoord die eerder zijn geregistreerd tijdens de implementatie van de oplossing.
2. Werk de gegevensbron van het cold path-rapportbestand bij met Power BI Desktop.
   
   * Dubbelklik in de map waar u het generatorbestand hebt gedownload en uitgepakt, op het **PowerBI\\PredictiveMaintenanceAerospace.pbix-bestand.** Als u waarschuwingsberichten ziet wanneer u het bestand opent, negeert u deze. Klik boven aan het bestand op **'Query's bewerken'.**
     
     ![Query's bewerken](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * U ziet twee tabellen, **RemainingUsefulLife** en **PMResult**. Selecteer de eerste ![tabel en](./media/predictive-maintenance-technical-guide/icon-query-settings.png) klik op het pictogram Query-instellingen naast **'Bron'** onder **'TOEGEPASTE STAPPEN'** in het rechter deelvenster **'Query-instellingen'.** Negeer alle waarschuwingsberichten die worden weergegeven.
   * Vervang **'Server'** en **'Database'** in het pop-outvenster door uw eigen server- en databasenamen en klik vervolgens op **'OK'.** Voor servernaam moet u de poort 1433 **(YourSolutionName.database.windows.net, 1433)** opgeven. Verlaat het veld Database als **pmaintenancedb**. Negeer de waarschuwingsberichten die op het scherm worden weergegeven.
   * In het volgende pop-outvenster ziet u twee opties in het linkerdeelvenster **(Windows** en **Database).** Klik op **'Database',** vul uw **'Gebruikersnaam'** en **'Wachtwoord'** in (de gebruikersnaam en het wachtwoord die u hebt ingevoerd toen u de oplossing voor het eerst hebt geïmplementeerd en een Azure SQL-database hebt gemaakt). Schakel in ***Selecteren op welk niveau u deze instellingen wilt toepassen***op de optie databaseniveau in. Klik vervolgens op **'Verbinden'.**
   * Klik op de tweede tabel **PMResult** ![en klik vervolgens op Navigatiepictogram](./media/predictive-maintenance-technical-guide/icon-navigation.png) naast **'Bron'** onder **'TOEGEPASTE STAPPEN'** in het rechter deelvenster **'Query-instellingen'** en werk de server- en databasenamen bij zoals in de bovenstaande stappen en klik op OK.
   * Zodra u naar de vorige pagina bent geleid, sluit u het venster. Er wordt een bericht weergegeven - klik op **Toepassen**. Klik tot slot op de knop **Opslaan** om de wijzigingen op te slaan. Uw Power BI-bestand heeft nu verbinding met de server tot stand gebracht. Als uw visualisaties leeg zijn, moet u de selecties op de visualisaties wissen om alle gegevens te visualiseren door op het gumpictogram in de rechterbovenhoek van de legenden te klikken. Gebruik de knop Vernieuwen om nieuwe gegevens over de visualisaties weer te geven. In eerste instantie ziet u alleen de seedgegevens van uw visualisaties, omdat de gegevensfabriek elke 3 uur wordt vernieuwd. Na 3 uur ziet u nieuwe voorspellingen die worden weergegeven in uw visualisaties wanneer u de gegevens vernieuwt.
3. (Optioneel) Publiceer het dashboard van het koudepad online naar [Power BI.](https://www.powerbi.com/) Voor deze stap is een Power BI-account (of Office 365-account) nodig.
   
   * Klik **op 'Publiceren'** en enkele seconden later verschijnt er een venster met 'Publiceren naar Power BI-succes!' met een groen vinkje. Klik op de link hieronder "Open PredictiveMaintenanceAerospace.pbix in Power BI". Zie Publiceren vanaf [Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop)voor meer informatie.
   * Als u een nieuw **+** dashboard wilt maken, klikt u op het bord naast de sectie **Dashboards** in het linkerdeelvenster. Voer de naam "Predictive Maintenance Demo" in voor dit nieuwe dashboard.
   * Zodra u het rapport ![opent, klikt u op het pictogram](./media/predictive-maintenance-technical-guide/icon-pin.png) PINCODE om alle visualisaties aan uw dashboard vast te maken. Zie Een tegel [vastmaken aan een Power BI-dashboard in een rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report)als u gedetailleerde instructies wilt vinden.
     Ga naar de dashboardpagina en pas de grootte en locatie van uw visualisaties aan en bewerk hun titels. Zie Een tegel bewerken, het formaat [wijzigen, verplaatsen, hernoemen, vastmaken, verwijderen, hyperlinktoevoegen](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename)als u gedetailleerde instructies wilt vinden voor het bewerken van uw tegels. Hier volgt een voorbeelddashboard met enkele koudepadvisualisaties die eraan zijn vastgemaakt.  Afhankelijk van hoe lang u uw gegevensgenerator uitvoert, kunnen uw nummers op de visualisaties verschillend zijn.
     <br/>
     ![Eindweergave](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Als u vernieuwen van de gegevens wilt plannen, houdt ![u de](./media/predictive-maintenance-technical-guide/icon-elipsis.png) muis aanwijzer boven de gegevensset **PredictiveMaintenanceAerospace,** klikt u op het pictogram Ellipsis en kiest u **Vernieuwen plannen.**
     <br/>
     > [!NOTE]
     > Als u een waarschuwingsbericht ziet, klikt u op **Referenties bewerken** en controleert u of uw databasereferenties dezelfde zijn als die welke in stap 1 zijn beschreven.
     <br/>
     ![Vernieuwen van gegevens plannen](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Vouw de sectie **Vernieuwen plannen** uit. Schakel "houd uw gegevens up-to-date".
     <br/>
   * Plan de vernieuwing op basis van uw behoeften. Zie Gegevens vernieuwen [in Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi)voor meer informatie.

### <a name="setup-hot-path-dashboard"></a>Dashboard hot path instellen
Met de volgende stappen u de gegevensuitvoer visualiseren van Stream Analytics-taken die zijn gegenereerd op het moment van implementatie van oplossingen. Een [Power BI online-account](https://www.powerbi.com/) is vereist om de volgende stappen uit te voeren. Als je geen account hebt, kun je [er een aanmaken.](https://powerbi.microsoft.com/pricing)

1. Power BI-uitvoer toevoegen in Azure Stream Analytics (ASA).
   
   * U moet de instructies in [Azure Stream Analytics & Power BI: een analysedashboard volgen voor realtime zichtbaarheid van streaminggegevens](../../stream-analytics/stream-analytics-power-bi-dashboard.md) om de uitvoer van uw Azure Stream Analytics-taak in te stellen als uw Power BI-dashboard.
   * De ASA-query heeft drie uitgangen die **aircraftmonitor**, **aircraftalert**en **flightsbyhour**zijn . U de query bekijken door op het tabblad query te klikken. Wanneer u de eerste uitvoer **(aircraftmonitor)** toevoegt, controleert u of de **uitvoeralias,** **gegevenssetnaam** en **tabelnaam** hetzelfde zijn **(aircraftmonitor).** Herhaal de stappen om uitvoer toe te voegen voor **vliegtuigenalert**en **per uur**. Zodra u alle drie de uitvoertabellen hebt toegevoegd en de ASA-taak hebt gestart, moet u een bevestigingsbericht ontvangen ("Starting Stream Analytics-taakonderhoudssa02asapbi is geslaagd").
2. Log online in op [Power BI](https://www.powerbi.com)
   
   * In de sectie Gegevenssets van het linkerdeelvenster in Mijn werkruimte worden de ***gegevenssetnamen*** **aircraftmonitor,** **aircraftalert**en **flightsbyhour** weergegeven. Dit zijn de streaminggegevens die u in de vorige stap van Azure Stream Analytics hebt gepusht. De gegevensset **vluchtenper uur** kan niet worden weergegeven op hetzelfde moment als de andere twee gegevenssets als gevolg van de aard van de SQL-query achter het. Echter, het moet verschijnen na een uur.
   * Controleer of het deelvenster ***Visualisaties*** is geopend en aan de rechterkant van het scherm wordt weergegeven.
3. Zodra u de gegevens naar Power BI hebt gestroomd, u beginnen met het visualiseren van de streaminggegevens. Hieronder vindt u een voorbeelddashboard met enkele hot path visualisaties vastgemaakt. U andere dashboardtegels maken op basis van de juiste gegevenssets. Afhankelijk van hoe lang u uw gegevensgenerator uitvoert, kunnen uw nummers op de visualisaties verschillend zijn.

    ![Dashboardweergave](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Hier volgen enkele stappen om een van de bovenstaande tegels te maken : de tegel 'Fleet View of Sensor 11 vs. Threshold 48.26':
   
   * Klik op **gegevenssetvliegtuigenmonitor** in de sectie Gegevenssets van het linkerdeelvenster.
   * Klik op het pictogram **Lijndiagram.**
   * Klik **op Verwerkt** in het deelvenster **Velden,** zodat deze wordt weergegeven onder 'As' in het deelvenster **Visualisaties.**
   * Klik op 's11' en\_'s11 alert' zodat ze beide worden weergegeven onder 'Waarden'. Klik op de kleine pijl naast **de s11-** en **s11-waarschuwing\_**, wijzig 'Som' in 'Gemiddeld'.
   * Klik op **OPSLAAN** bovenaan en geef het rapport een naam "aircraftmonitor". Het rapport met de naam "aircraftmonitor" wordt weergegeven in de sectie **Rapporten** in het deelvenster **Navigator** aan de linkerkant.
   * Klik op het pictogram **Visuele vastmaken** in de rechterbovenhoek van dit lijndiagram. Er kan een venster 'Vastmaken aan dashboard' worden weergegeven om een dashboard te kiezen. Selecteer 'Demo voorspellend onderhoud' en klik op 'Vastmaken'.
   * Plaats de muis boven deze tegel op het dashboard en klik op het pictogram 'bewerken' in de rechterbovenhoek om de titel te wijzigen in 'Fleet View of Sensor 11 vs. Threshold 48.26' en ondertitel in 'Gemiddeld in de loop van de tijd'.

## <a name="delete-your-solution"></a>Uw oplossing verwijderen
Zorg ervoor dat u de gegevensgenerator stopt wanneer u de oplossing niet actief gebruikt, omdat het uitvoeren van de gegevensgenerator hogere kosten met zich meebrengt. Verwijder de oplossing als u deze niet gebruikt. Als u uw oplossing verwijdert, worden alle onderdelen die in uw abonnement zijn ingericht, verwijderd wanneer u de oplossing hebt geïmplementeerd. Als u de oplossing wilt verwijderen, klikt u op de naam van uw oplossing in het linkerdeelvenster van de oplossingssjabloon en klikt u vervolgens op **Verwijderen**.

## <a name="cost-estimation-tools"></a>Kostenramingstools
De volgende twee tools zijn beschikbaar om u te helpen beter inzicht te krijgen in de totale kosten die gemoeid zijn met het uitvoeren van de Predictive Maintenance for Aerospace Solution Template in uw abonnement:

* [Microsoft Azure Cost Estimator Tool (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure Cost Estimator Tool (desktop)](https://www.microsoft.com/download/details.aspx?id=43376)

