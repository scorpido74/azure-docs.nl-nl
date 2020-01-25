---
title: Handleiding voor Voorspellend onderhoud voor lucht-en ruimtevaart - Team Data Science Process
description: Een technische handleiding voor de sjabloon met Microsoft Cortana Intelligence-oplossing voor predictief onderhoud in de lucht-en ruimtevaart, hulpprogramma's en transport.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 9871e1402336f5ad282c12f959d45fda85512a84
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721843"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace"></a>Technische handleiding aan de sjabloon Cortana Intelligence-oplossing voor predictief onderhoud in de luchtvaart

> [!Important]
> In dit artikel is afgeschaft. De discussie over Voorspellend onderhoud in de luchtvaart is nog steeds relevant zijn, maar voor actuele informatie, raadpleeg dan [overzicht van de oplossing voor zakelijke doelgroepen](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Sjablonen voor oplossingen zijn ontworpen om het proces van het bouwen van een demo E2E boven op Cortana Intelligence Suite te versnellen. Een geïmplementeerde sjabloon bepalingen van uw abonnement met de benodigde onderdelen van Cortana Intelligence en bouwt vervolgens de relaties tussen deze. Deze ook seeding van de gegevenspijplijn met voorbeeldgegevens uit een data-generator-toepassing, die u downloadt en installeert op uw lokale computer nadat u de sjabloon van de oplossing hebt geïmplementeerd. De gegevens van de generator hydrates de gegevenspijplijn en beginnen met het genereren van voorspellingen van machine learning, die vervolgens kunnen worden gevisualiseerd in Power BI-dashboard.

Het implementatieproces leidt u door de verschillende stappen voor het instellen van de referenties van uw oplossing. Zorg ervoor dat u de referenties, zoals de naam van oplossing, gebruikersnaam en wachtwoord die u tijdens de implementatie opgeeft vastleggen. 


De doelstellingen van dit artikel zijn:
- Beschrijf de referentie-architectuur en onderdelen die zijn ingericht in uw abonnement.
- Ziet u hoe u de voorbeeldgegevens vervangen door uw eigen gegevens. 
- Laten zien hoe de oplossingssjabloon wijzigen.  

> [!TIP]
> U kunt downloaden en afdrukken een [PDF-versie van dit artikel](https://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).
> 
> 

## <a name="overview"></a>Overzicht
![Predictief onderhoud-architectuur](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

Wanneer u de oplossing implementeert, wordt Azure-services binnen de Cortana Analytics Suite (met inbegrip van de Event Hub, Stream Analytics, HDInsight, Machine Learning en Data Factory) geactiveerd. Het architectuurdiagram ziet hoe de Voorspellend onderhoud voor luchtvaart oplossingssjabloon is samengesteld. U kunt deze services in Azure portal door erop te klikken in het oplossingsdiagram van de sjabloon die zijn gemaakt met de implementatie van de oplossing (met uitzondering van HDInsight, dat is ingericht op aanvraag wanneer de gerelateerde pipeline-activiteiten zijn vereist om uit te voeren en onderzoeken verwijderd daarna).
Download een [volledige versie van het diagram](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

De volgende secties beschrijven de onderdelen van de oplossing.

## <a name="data-source-and-ingestion"></a>De gegevensbron en opnamesnelheid
### <a name="synthetic-data-source"></a>Synthetische gegevensbron
Voor deze sjabloon wordt de gebruikte gegevens bron gegenereerd op basis van een gedownloade bureaublad toepassing die u lokaal uitvoert na een geslaagde implementatie.

Als u de instructies voor het downloaden en installeren van deze toepassing zoekt, selecteert u het eerste knooppunt Gegevensgenerator Voorspellend onderhoud, op het oplossingsdiagram van de sjabloon. De instructies hiervoor vindt u in de balk eigenschappen. Deze toepassing feeds de [Azure Event Hub](#azure-event-hub) service met gegevenspunten of gebeurtenissen, die worden gebruikt in de rest van de oplossingsstroom. Deze gegevensbron is afgeleid van openbaar beschikbare gegevens van de [NASA-gegevensopslagplaats](https://c3.nasa.gov/dashlink/resources/139/) met behulp van de [Turbofan Engine Degradation Simulation Data Set](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

De toepassing voor het genereren van gebeurtenis vult de Azure Event Hub alleen terwijl deze wordt uitgevoerd op uw computer.  

### <a name="azure-event-hub"></a>Azure Event Hub  
De [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) -service is de ontvanger van de invoer die is opgegeven door de synthetische gegevensbron.

## <a name="data-preparation-and-analysis"></a>Voorbereiden van gegevens en analyse  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Gebruik [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) voor bijna realtime analyses over de invoerstroom uit de [Azure Event Hub](#azure-event-hub) service. Vervolgens publiceert u de resultaten naar een [Power BI](https://powerbi.microsoft.com) dashboard en archiveren van alle ruwe inkomende gebeurtenissen naar de [Azure Storage](https://azure.microsoft.com/services/storage/) service later worden verwerkt door de [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)service.

### <a name="hdinsight-custom-aggregation"></a>Aangepaste HDInsight-aggregatie
Voer [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -scripts (in azure Data Factory) uit met HDInsight om aggregaties te bieden voor de onbewerkte gebeurtenissen die zijn gearchiveerd met de Azure stream Analytics-resource.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Voorspellingen doen op de resterende levensduur (RUL) van een specifieke vliegtuigmotoren met behulp van de ingevoerde gegevens met [Azure Machine Learning-Service](https://azure.microsoft.com/services/machine-learning/) (gecoördineerd door Azure Data Factory). 

## <a name="data-publishing"></a>Gegevens publiceren
### <a name="azure-sql-database"></a>Azure SQL Database
Gebruik [Azure SQL database](https://azure.microsoft.com/services/sql-database/) om de voor spellingen op te slaan die worden ontvangen door de Azure machine learning, die vervolgens worden gebruikt in het [Power bi](https://powerbi.microsoft.com) dash board.

## <a name="data-consumption"></a>Gegevensverbruik
### <a name="power-bi"></a>Power BI
Gebruik [Power BI](https://powerbi.microsoft.com) om weer te geven van een dashboard met aggregaties en waarschuwingen die worden geleverd door [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), evenals voorspellingen van de resterende Levensduur die zijn opgeslagen in [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) die zijn gemaakt met behulp van [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Hoe u uw eigen gegevens
In deze sectie wordt beschreven hoe u uw eigen gegevens overbrengen naar Azure en welke gebieden wijzigingen vereist voor de gegevens die u in deze architectuur wilt overbrengen.

Is het onwaarschijnlijk dat uw gegevensset overeenkomt met de gegevensset die worden gebruikt door de [Turbofan Engine Degradation Simulation Data Set](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) gebruikt voor deze oplossingssjabloon. Inzicht krijgen in uw gegevens en de vereisten zijn essentieel in hoe u deze sjabloon wilt werken met uw eigen gegevens wijzigen. 

De volgende secties worden de onderdelen van de sjabloon die moeten worden aangepast wanneer een nieuwe gegevensset wordt geïntroduceerd.

### <a name="azure-event-hub"></a>Azure Event Hub
Azure Event Hub is algemeen; gegevens kan op de hub in CSV of JSON-indeling worden geplaatst. Er is geen speciale verwerking vindt plaats in de Azure Event Hub, maar het is belangrijk dat u bekend bent met de gegevens die worden opgenomen in het.

Dit document wordt niet beschreven hoe u uw gegevens opnemen, maar u kunt eenvoudig verzenden gebeurtenissen of gegevens naar een Azure Event Hub met behulp van de Event Hub-API's.

### <a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Gebruik de Azure Stream Analytics resource om bijna realtime analyses te bieden door gegevens stromen te lezen en gegevens uit te voeren naar een wille keurig aantal bronnen.

Voor de Voorspellend onderhoud voor luchtvaart oplossingssjabloon bestaat de Azure Stream Analytics-query uit vier sub-query's, elke query gebeurtenissen van de Azure Event Hub-service, met de uitvoer naar vier verschillende locaties. Deze uitvoer bestaat uit drie Power BI-gegevenssets en een Azure-opslaglocatie.

De Azure Stream Analytics-query kan worden gevonden door:

* Verbinding maken met de Azure-portal
* Zoeken naar de Stream Analytics-taken ![pictogram Stream Analytics](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) die zijn gegenereerd wanneer de oplossing is geïmplementeerd (*bijvoorbeeld*, **maintenancesa02asapbi** en **maintenancesa02asablob** voor de oplossing voor voorspeld onderhoud)
* Selecteren
  
  * ***INVOER*** om weer te geven van de query invoeren
  * ***QUERY*** om de query zelf weer te geven
  * ***UITVOER*** om de verschillende uitvoer weer te geven

Informatie over het samenstellen van de Azure Stream Analytics-query kan worden gevonden de [Stream Analytics Query verwijzing](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) op MSDN.

De query's uitvoeren in deze oplossing, drie gegevenssets met bijna realtime analyses informatie over de gegevensstroom inkomende aan een Power BI-dashboard geleverd als onderdeel van deze oplossingssjabloon. Omdat er impliciete kennis op over de indeling van de binnenkomende, moeten deze query's op basis van de gegevensindeling van uw worden gewijzigd.

De query in de tweede Stream Analytics-taak **maintenancesa02asablob** gewoon voert alle [Event Hub](https://azure.microsoft.com/services/event-hubs/) gebeurtenissen naar [Azure Storage](https://azure.microsoft.com/services/storage/) en kan daarom geen wijziging vereist ongeacht de gegevensindeling van uw als de volledige gebeurtenis worden gegevens gestreamd naar opslag.

### <a name="azure-data-factory"></a>Azure Data Factory
De [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) coördineert service de verplaatsing en verwerking van gegevens. In de Voorspellend onderhoud voor luchtvaart oplossingssjabloon, de data factory bestaat uit drie [pijplijnen](../../data-factory/concepts-pipelines-activities.md) die verplaatsen en verwerken van de gegevens met behulp van verschillende technologieën.  Toegang tot uw data factory door het openen van de Data Factory-knooppunt aan de onderkant van het oplossingsdiagram van de sjabloon die zijn gemaakt met de implementatie van de oplossing. Fouten in uw gegevenssets worden vanwege data factory geïmplementeerd voordat u de gegevensgenerator is gestart. Deze fouten kunnen worden genegeerd en niet voorkomen dat uw data factory werkt

![Data Factory-gegevensset-fouten](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Deze sectie wordt beschreven voor de benodigde [pijplijnen en activiteiten](../../data-factory/concepts-pipelines-activities.md) die deel uitmaken van de [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Hier volgt een diagramweergave van de oplossing.

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Twee van de pijplijnen van deze factory bevat [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts die worden gebruikt voor het partitioneren en de gegevens worden geaggregeerd. Wanneer die u hebt genoteerd, de scripts bevinden zich in de [Azure Storage](https://azure.microsoft.com/services/storage/) account is gemaakt tijdens de installatie. De locatie is: maintenancesascript\\\\script\\\\hive\\ \\ (of https://[Your oplossing name].blob.core.windows.net/maintenancesascript).

Vergelijkbaar met [Azure Stream Analytics](#azure-stream-analytics-1) query's, de [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts impliciete kennis over de indeling van de binnenkomende hebben en moet worden gewijzigd op basis van de gegevensindeling van uw.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Dit [pijplijn](../../data-factory/concepts-pipelines-activities.md) bevat één activiteit: een [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) activiteit met behulp van een [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) die wordt uitgevoerd een [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script voor het partitioneren van de gegevens in plaatsen [Azure Storage](https://azure.microsoft.com/services/storage/) tijdens de [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) taak.

De [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script voor deze taak partitioneren is ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Dit [pijplijn](../../data-factory/concepts-pipelines-activities.md) bevat verschillende activiteiten waarvan eindresultaat de beoordeelde voorspellingen is van de [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experiment die zijn gekoppeld aan deze oplossingssjabloon.

Activiteiten die zijn:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) activiteit met behulp van een [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) die wordt uitgevoerd een [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script aggregatiefuncties en functie-engineering die nodig zijn voor de [Azure-Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimenteren.
  De [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script voor deze taak partitioneren is ***PrepareMLInput.hql***.
* [Kopie](https://msdn.microsoft.com/library/azure/dn835035.aspx) activiteit die wordt verplaatst van de resultaten van de [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) activiteit naar een enkele [Azure Storage](https://azure.microsoft.com/services/storage/) blob toegankelijk is voor de [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) de activiteit.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) activiteit aanroepen de [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experiment met resultaten in een enkel plaatsen [Azure Storage](https://azure.microsoft.com/services/storage/) blob.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Dit [pijplijn](../../data-factory/concepts-pipelines-activities.md) bevat één activiteit: een [kopie](https://msdn.microsoft.com/library/azure/dn835035.aspx) activiteit die wordt verplaatst van de resultaten van de [Azure Machine Learning](#azure-machine-learning) experimenteren uit de  ***MLScoringPipeline*** naar de [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) ingericht als onderdeel van de installatie van de sjabloon oplossing.

### <a name="azure-machine-learning"></a>Azure Machine Learning
De [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimenteren gebruikt voor deze oplossingssjabloon de resterende bruikbare levensduur (RUL) van een vliegtuigmotor biedt. Het experiment is specifiek voor de gegevensset verbruikt en wijziging vereist of vervanging die specifiek zijn voor de gegevens op.

Zie voor meer informatie over hoe de Azure Machine Learning-experiment is gemaakt, [Voorspellend onderhoud: stap 1 van 3, gegevens voor te bereiden en feature-engineering](https://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>De voortgang van de
Nadat u de Gegevensgenerator wordt gestart, de pijplijn begint met het dehydrate en de verschillende onderdelen van uw oplossing start starten in actie volgende de opdrachten die zijn uitgegeven door de data factory. Er zijn twee manieren voor het bewaken van de pijplijn.

* Een van de Stream Analytics-taken schrijft de onbewerkte binnenkomende gegevens naar blob-opslag. Als u op Blob-opslag-onderdeel van uw oplossing van het scherm u is klikt de oplossing wordt geïmplementeerd en klikt u op openen in het deelvenster aan de rechterkant, het gaat u naar de [Azure-portal](https://portal.azure.com/). Klik op Blobs. In het volgende deelvenster ziet u een lijst met Containers. Klik op **maintenancesadata**. In het volgende deelvenster is de **rawdata** map. In de map rawdata zijn mappen met de namen bijvoorbeeld uur = 17 en uur = 18. De aanwezigheid van deze mappen geeft onbewerkte gegevens worden gegenereerd op uw computer en die zijn opgeslagen in blob-opslag. U ziet de csv-bestanden met beperkte grootte in MB in deze mappen.
* De laatste stap van de pijplijn is het schrijven van gegevens (bijvoorbeeld voorspellingen op basis van machine learning) in SQL-Database. Mogelijk moet wachten maximaal drie uur voor de gegevens worden weergegeven in de SQL-Database. Eén manier om te controleren hoeveel gegevens zijn beschikbaar in uw SQL-Database is via de [Azure-portal](https://portal.azure.com/). Zoek in het linkerdeel venster naar SQL-data BASEs ![SQL-pictogram](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) en klik erop. Ga vervolgens naar uw database **pmaintenancedb** en klik erop. Klik in de volgende pagina aan de onderkant op beheren
   
    ![Pictogram beheren](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png)
   
    U kunt hier klikken op nieuwe Query en query's uitvoeren voor het aantal rijen (bijvoorbeeld select count(*) van PMResult). Als uw database groeit, wordt het aantal rijen in de tabel moet vergroten.

## <a name="power-bi-dashboard"></a>Power BI Dashboard

Instellen van een Power BI-dashboard voor het visualiseren van uw Azure Stream Analytics-gegevens (dynamisch pad) en de batch-voorspellingsresultaten van Azure machine learning-(koud pad).

### <a name="set-up-the-cold-path-dashboard"></a>Instellen van het koude pad-dashboard
In de pijplijn koude pad, wordt het doel is om op te halen van de voorspellende resterende bruikbare Levensduur (de resterende levensduur) van elke vliegtuigmotor zodra de App is een vlucht (cyclus). Het resultaat voorspelling wordt elke drie uur voor het voorspellen van de vliegtuigmotoren die u een vlucht in de afgelopen 3 uur hebt bijgewerkt.

Power BI maakt verbinding met een Azure SQL Database als de bijbehorende gegevens bron, waarbij de Voorspellings resultaten worden opgeslagen. 

Opmerking: 
1.    Bij het implementeren van uw oplossing wordt binnen drie uur een voor spelling in de Data Base weer gegeven. Het pbix-bestand met het downloaden van de Generator bevat enkele seedgegevens, zodat u meteen het Power BI-dashboard kunt maken. 
2.    In deze stap is het vereist dat u de gratis software [Power bi bureau blad](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)downloadt en installeert.

De volgende stappen leest u hoe u verbinding maken het pbix-bestand met de SQL-Database hebben is ingeschakeld op het moment van implementatie van de oplossing met gegevens (bijvoorbeeld voorspellingsresultaten) voor visualisatie.

1. Referenties van de database ophalen.
   
   U moet **database-servernaam, databasenaam, gebruikersnaam en wachtwoord** voordat u doorgaat naar de volgende stappen. Hier volgen de stappen om u te begeleiden u deze kunt vinden.
   
   * Eenmaal **'Azure SQL Database'** op uw oplossingssjabloon diagram wordt groen, klikt u op deze en klik vervolgens op **'Open'** .
   * Er wordt een nieuw browser tabblad/-venster weer gegeven met de Azure Portal-pagina. Klik op **'Resourcegroepen'** in het linkerdeelvenster.
   * Selecteer het abonnement dat u gebruikt voor het implementeren van de oplossing en selecteer vervolgens **' YourSolutionName\_ResourceGroup'** .
   * In de nieuwe pop-outmodus deelvenster, klikt u op de ![SQL pictogram](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) pictogram voor toegang tot uw database. De naam van uw Data Base bevindt zich naast dit pictogram (bijvoorbeeld **' pmaintenancedb '** ) en de naam van de **database server** wordt vermeld onder de eigenschap Server naam en moet er ongeveer uitzien als **YourSolutionName.database.Windows.net**.
   * Uw database **gebruikersnaam** en **wachtwoord** zijn hetzelfde als de gebruikersnaam en het wachtwoord eerder vastgelegd tijdens de implementatie van de oplossing.
2. De gegevensbron van het koude pad-rapportbestand bijwerken met Power BI Desktop.
   
   * In de map waar u hebt gedownload en de Generator-bestand zijn uitgepakt, dubbelklikt u op de **Power BI\\PredictiveMaintenanceAerospace.pbix** bestand. Als u eventuele waarschuwingsberichten ziet wanneer u het bestand opent, kunt u ze negeren. Boven aan het bestand, klikt u op **query's bewerken**.
     
     ![Query's bewerken](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * Ziet u twee tabellen **RemainingUsefulLife** en **PMResult**. Selecteer de eerste tabel en klik op ![Query Instellingenpictogram](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) naast **'Source'** onder **TOEGEPASTE stappen'** aan de rechterkant **Query-instellingen** deelvenster. Negeer eventuele waarschuwingsberichten die worden weergegeven.
   * Vervang in het pop-outmodus venster, **'Server'** en **'Database'** met uw eigen namen van de server en database, en klik vervolgens op **'OK'** . Zorg ervoor dat u voor de server naam de poort 1433 (**YourSolutionName.database.Windows.net, 1433**) opgeeft. Laat het veld Database als **pmaintenancedb**. Negeer de waarschuwingsberichten die worden weergegeven op het scherm.
   * In het volgende pop-outmodus venster, ziet u twee opties in het linkerdeelvenster (**Windows** en **Database**). Klik op **Data Base**, vul uw **gebruikers naam** en **wacht** woord in (de gebruikers naam en het wacht woord die u hebt ingevoerd toen u de oplossing voor het eerst implementeerde en een Azure SQL database hebt gemaakt). In ***Selecteer welk gegevensniveau voor deze instellingen toepassen op***, een optie voor database controleren. Klik vervolgens op **'Connect'** .
   * Klik op de tweede tabel **PMResult** klikt u vervolgens op ![pictogram Navigatie](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) naast **'Source'** onder **TOEGEPASTE stappen'** aan de rechterkant **Query-instellingen** deelvenster en bijwerken van de namen van de server en database zoals in de bovenstaande stappen en klik op OK.
   * Nadat u terug naar de vorige pagina Begeleide bent, sluit u het venster. Een bericht wordt weergegeven, klikt u **toepassen**. Klik ten slotte de **opslaan** knop de wijzigingen op te slaan. Uw Power BI-bestand is nu ingesteld voor verbinding met de server. Als uw visualisaties leeg zijn, zorg er dan voor dat u de gewenste opties op de visualisaties aan alle gegevens visualiseren door te klikken op het gumpictogram in de rechterbovenhoek van de legenda's uitschakelen. Gebruik de vernieuwknop om nieuwe gegevens voor de visualisaties weer te geven. In eerste instantie alleen ziet u de seedgegevens op uw visualisaties als de data factory is gepland om te vernieuwen elke drie uur. U ziet na 3 uur nieuwe voorspellingen doorgevoerd in uw visualisaties bij het vernieuwen van de gegevens.
3. (Optioneel) Het koude pad-dashboard om te publiceren [Power BI online](https://www.powerbi.com/). Voor deze stap hebt u een Power BI account (of een Office 365-account) nodig.
   
   * Klik op **'Publiceren'** en enkele seconden later er verschijnt een venster weergegeven "Publiceren naar Power BI succes!" met een groen vinkje. Klik op de koppeling hieronder 'Open PredictiveMaintenanceAerospace.pbix in Power BI'. Gedetailleerde instructies, Zie [publiceren vanuit Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Een nieuw dashboard maken: klik op de **+** Meld u aan bij de **Dashboards** sectie in het linkerdeelvenster. Voer de naam 'Voorspellend onderhoud Demo' voor dit nieuwe dashboard.
   * Nadat u het rapport openen, klikt u op ![speldpictogram](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png) om alle visualisaties aan uw dashboard vast te maken. Gedetailleerde instructies, Zie [een tegel vastmaken aan Power BI-dashboard vanuit een rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Ga naar de dashboardpagina en de grootte en locatie van uw visualisaties aanpassen en bewerken van hun titels. Zie voor gedetailleerde instructies over hoe u uw tegels bewerken, [bewerken een tegel--formaat wijzigen, verplaatsen, naam wijzigen, vastmaken, verwijderen, hyperlink toevoegen](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Hier volgt een voorbeeld van dashboard met sommige koude pad visualisaties zijn vastgemaakt aan het.  Afhankelijk van hoe lang u de gegevensgenerator uitvoert, kan de nummers van de visualisaties afwijken.
     <br/>
     ![Laatste weergeven](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * Als u de gegevens wilt vernieuwen, houdt u de muis aanwijzer over de **PredictiveMaintenanceAerospace** -gegevensset, klikt u op ![pictogram met het weglatings teken](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png) en kiest u vervolgens **vernieuwen plannen**.
     <br/>
     **Opmerking:** als er een waarschuwing massage, klikt u op **referenties bewerken** en controleer of de referenties van uw database zijn hetzelfde als die wordt beschreven in stap 1.
     <br/>
     ![Planning vernieuwen](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * Vouw de **vernieuwen plannen** sectie. Schakel 'uw gegevens actueel houden'.
     <br/>
   * Geen vernieuwing worden gepland op basis van uw behoeften. Meer informatie, Zie [gegevens vernieuwen in Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Snelpad dashboard instellen
De volgende stappen helpen u over het visualiseren van de gegevensuitvoer van Stream Analytics-taken die zijn gegenereerd op het moment van implementatie van de oplossing. Een [Power BI online](https://www.powerbi.com/) account is vereist om de volgende stappen uitvoeren. Als u geen account hebt, kunt u [maakt u er een](https://powerbi.microsoft.com/pricing).

1. Power BI-uitvoer in Azure Stream Analytics (ASA) toevoegen.
   
   * U moet de instructies in [Azure Stream Analytics en Power BI: een dashboard voor realtime zichtbaarheid van het streamen van gegevens voor analysedoeleinden](../../stream-analytics/stream-analytics-power-bi-dashboard.md) voor het instellen van de uitvoer van uw Azure Stream Analytics-taak als uw Power BI-dashboard.
   * De ASA-query heeft drie uitvoer, **aircraftmonitor**, **aircraftalert**en **flightsbyhour**. U kunt de query weer geven door te klikken op het tabblad query. overeenkomt met elk van deze tabellen, moet u een uitvoer toevoegen aan ASA. Wanneer u de eerste uitvoer toevoegt (**aircraftmonitor**) Zorg ervoor dat de **uitvoeralias**, **gegevenssetnaam** en **tabelnaam** zijn de dezelfde (**aircraftmonitor**). Herhaal de stappen voor het toevoegen van de uitvoer voor **aircraftalert**, en **flightsbyhour**. Nadat u alle drie uitvoertabellen en aan de slag de ASA-taak hebt toegevoegd, moet u een bevestigingsbericht wordt weergegeven ('vanaf Stream Analytics-taak maintenancesa02asapbi is voltooid').
2. Meld u aan bij [Power BI online](https://www.powerbi.com)
   
   * In de sectie van de gegevenssets in mijn werkruimte in het linkerdeelvenster de ***GEGEVENSSET*** namen **aircraftmonitor**, **aircraftalert**, en **flightsbyhour** moet worden weergegeven. Dit is de streaminggegevens die u van Azure Stream Analytics in de vorige stap hebt gepusht. De gegevensset **flightsbyhour** kan niet op hetzelfde moment als de andere twee gegevenssets vanwege de aard van de SQL-query achterliggende weergegeven. Echter, moet het weergegeven na een uur.
   * Zorg ervoor dat de ***visualisaties*** deelvenster is geopend en wordt weergegeven aan de rechterkant van het scherm.
3. Zodra u de gegevens die binnenkomen in Power BI hebt, kun u de streaming-gegevens te visualiseren. Hieronder is een voorbeeld van dashboard met sommige visualisaties snelpad vastgemaakt aan het. U kunt andere dashboardtegels op basis van de juiste gegevenssets maken. Afhankelijk van hoe lang u de gegevensgenerator uitvoert, kan de nummers van de visualisaties afwijken.

    ![Dashboardweergave](media/cortana-analytics-technical-guide-predictive-maintenance/dashboard-view.png)

1. Hier volgen enkele stappen voor het maken van een van de bovenstaande tegels: de tegel ' vloot weergave van sensor 11 versus drempel waarde 48,26 '.
   
   * Klik op de gegevensset **aircraftmonitor** in de sectie van de gegevenssets in het linkerdeelvenster.
   * Klik op de **lijndiagram** pictogram.
   * Klik op **verwerkte** in de **velden** deelvenster zodat deze worden weergegeven onder 'As' de **visualisaties** deelvenster.
   * Klik op "s11" en "s11\_waarschuwing" zodat deze beide worden weergegeven onder 'Values'. Klik op het pijltje naast **s11** en **s11\_waarschuwing**, 'Som' op 'Gemiddeld' wijzigen.
   * Klik op **opslaan** op de boven- en de naam van het rapport 'aircraftmonitor." Het rapport met de naam 'aircraftmonitor' wordt weergegeven de **rapporten** sectie de **Navigator** in het linkerdeelvenster.
   * Klik op het pictogram voor het **visuele element** in de rechter bovenhoek van dit lijn diagram. Een 'Vastmaken aan Dashboard'-venster kan weergegeven waarin u kiest een dashboard. Selecteer 'Voorspellend onderhoud Demo', en klik vervolgens op 'Pincode'.
   * Houd de muis aanwijzer boven deze tegel op het dash board en klik op het pictogram bewerken in de rechter bovenhoek om de titel te wijzigen in ' vloot weergave van sensor 11 versus drempel waarde 48,26 ' en ondertitel op ' gemiddeld over de hele periode '.

## <a name="delete-your-solution"></a>Uw oplossing verwijderen
Zorg ervoor dat u de gegevensgenerator stopt wanneer u de oplossing niet actief gebruikt als de gegevensgenerator worden hogere kosten. De oplossing te verwijderen als u deze niet gebruikt. Uw oplossing verwijdert, worden de onderdelen die zijn ingericht in uw abonnement tijdens de implementatie van de oplossing. Als u wilt verwijderen van de oplossing, klikt u op de oplossingsnaam van uw in het linkerdeelvenster van de sjabloon van de oplossing en klik vervolgens op **verwijderen**.

## <a name="cost-estimation-tools"></a>Kostenraming-hulpprogramma 's
De volgende twee hulpprogramma's zijn beschikbaar waarmee u meer informatie over de totale kosten die betrokken zijn bij het uitvoeren van de Voorspellend onderhoud voor luchtvaart oplossingssjabloon in uw abonnement:

* [Microsoft Azure hulpprogramma Cost Estimator ontwikkeld (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure-hulpprogramma voor Cost Estimator ontwikkeld (desktop)](https://www.microsoft.com/download/details.aspx?id=43376)

