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
> In dit artikel is afgeschaft. De discussie over voorspellend onderhoud in B.v. is nog steeds relevant, maar voor actuele informatie raadpleegt u het [overzicht van oplossingen voor zakelijke doel groepen](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Sjablonen voor oplossingen zijn ontworpen om het proces van het bouwen van een demo E2E boven op Cortana Intelligence Suite te versnellen. Een geïmplementeerde sjabloon bepalingen van uw abonnement met de benodigde onderdelen van Cortana Intelligence en bouwt vervolgens de relaties tussen deze. Deze ook seeding van de gegevenspijplijn met voorbeeldgegevens uit een data-generator-toepassing, die u downloadt en installeert op uw lokale computer nadat u de sjabloon van de oplossing hebt geïmplementeerd. De gegevens van de generator hydrates de gegevenspijplijn en beginnen met het genereren van voorspellingen van machine learning, die vervolgens kunnen worden gevisualiseerd in Power BI-dashboard.

Het implementatieproces leidt u door de verschillende stappen voor het instellen van de referenties van uw oplossing. Zorg ervoor dat u de referenties, zoals de naam van oplossing, gebruikersnaam en wachtwoord die u tijdens de implementatie opgeeft vastleggen. 


De doelstellingen van dit artikel zijn:
- Beschrijf de referentie-architectuur en onderdelen die zijn ingericht in uw abonnement.
- Ziet u hoe u de voorbeeldgegevens vervangen door uw eigen gegevens. 
- Laten zien hoe de oplossingssjabloon wijzigen.  

> [!TIP]
> U kunt een [PDF-versie van dit artikel](https://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf)downloaden en afdrukken.
> 
> 

## <a name="overview"></a>Overzicht
![Predictief onderhoud-architectuur](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

Wanneer u de oplossing implementeert, wordt Azure-services binnen de Cortana Analytics Suite (met inbegrip van de Event Hub, Stream Analytics, HDInsight, Machine Learning en Data Factory) geactiveerd. Het architectuurdiagram ziet hoe de Voorspellend onderhoud voor luchtvaart oplossingssjabloon is samengesteld. U kunt deze services in Azure portal door erop te klikken in het oplossingsdiagram van de sjabloon die zijn gemaakt met de implementatie van de oplossing (met uitzondering van HDInsight, dat is ingericht op aanvraag wanneer de gerelateerde pipeline-activiteiten zijn vereist om uit te voeren en onderzoeken verwijderd daarna).
Down load een [volledige versie van het diagram](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

De volgende secties beschrijven de onderdelen van de oplossing.

## <a name="data-source-and-ingestion"></a>De gegevensbron en opnamesnelheid
### <a name="synthetic-data-source"></a>Synthetische gegevensbron
Voor deze sjabloon wordt de gebruikte gegevens bron gegenereerd op basis van een gedownloade bureaublad toepassing die u lokaal uitvoert na een geslaagde implementatie.

Als u de instructies voor het downloaden en installeren van deze toepassing zoekt, selecteert u het eerste knooppunt Gegevensgenerator Voorspellend onderhoud, op het oplossingsdiagram van de sjabloon. De instructies hiervoor vindt u in de balk eigenschappen. Met deze toepassing wordt de [Azure Event hub](#azure-event-hub) -service gevoed door gegevens punten of gebeurtenissen die worden gebruikt in de rest van de oplossings stroom. Deze gegevens bron is afgeleid van openbaar beschik bare gegevens uit de [NASA-gegevensopslag plaats](https://c3.nasa.gov/dashlink/resources/139/) met behulp van de [simulatie gegevensset voor het degraderen](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan)van de turbofan-engine.

De toepassing voor het genereren van gebeurtenis vult de Azure Event Hub alleen terwijl deze wordt uitgevoerd op uw computer.  

### <a name="azure-event-hub"></a>Azure Event Hub  
De [Azure Event hub](https://azure.microsoft.com/services/event-hubs/) -service is de ontvanger van de invoer die is opgegeven door de synthetische gegevens bron.

## <a name="data-preparation-and-analysis"></a>Voorbereiden van gegevens en analyse  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Gebruik [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/) om bijna realtime analyses te bieden op de invoer stroom vanuit de [Azure Event hub](#azure-event-hub) -service. U publiceert vervolgens resultaten naar een [Power bi](https://powerbi.microsoft.com) dash board en archiveert alle onbewerkte binnenkomende gebeurtenissen naar de [Azure Storage](https://azure.microsoft.com/services/storage/) -service voor latere verwerking door de [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) service.

### <a name="hdinsight-custom-aggregation"></a>Aangepaste HDInsight-aggregatie
Voer [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -scripts (in azure Data Factory) uit met HDInsight om aggregaties te bieden voor de onbewerkte gebeurtenissen die zijn gearchiveerd met de Azure stream Analytics-resource.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Maak voor spellingen voor de resterende levens duur (resterende levens duur) van een bepaalde vliegtuig motor met behulp van de invoer ontvangen met [Azure machine learning service](https://azure.microsoft.com/services/machine-learning/) (georganiseerd door Azure Data Factory). 

## <a name="data-publishing"></a>Gegevens publiceren
### <a name="azure-sql-database"></a>Azure SQL Database
Gebruik [Azure SQL database](https://azure.microsoft.com/services/sql-database/) om de voor spellingen op te slaan die worden ontvangen door de Azure machine learning, die vervolgens worden gebruikt in het [Power bi](https://powerbi.microsoft.com) dash board.

## <a name="data-consumption"></a>Gegevensverbruik
### <a name="power-bi"></a>Power BI
Gebruik [Power bi](https://powerbi.microsoft.com) om een dash board weer te geven dat aggregaties en waarschuwingen bevat die door [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/)worden geleverd, evenals resterende levens duur-voor spellingen die zijn opgeslagen in [Azure SQL database](https://azure.microsoft.com/services/sql-database/) die zijn gemaakt met behulp van [Azure machine learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Hoe u uw eigen gegevens
In deze sectie wordt beschreven hoe u uw eigen gegevens overbrengen naar Azure en welke gebieden wijzigingen vereist voor de gegevens die u in deze architectuur wilt overbrengen.

Het is niet waarschijnlijk dat uw gegevensset overeenkomt met de gegevensset die wordt gebruikt door de [simulatie gegevensset](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) van de turbofan-engine die wordt gebruikt voor deze oplossings sjabloon. Inzicht krijgen in uw gegevens en de vereisten zijn essentieel in hoe u deze sjabloon wilt werken met uw eigen gegevens wijzigen. 

De volgende secties worden de onderdelen van de sjabloon die moeten worden aangepast wanneer een nieuwe gegevensset wordt geïntroduceerd.

### <a name="azure-event-hub"></a>Azure Event Hub
Azure Event Hub is algemeen; gegevens kan op de hub in CSV of JSON-indeling worden geplaatst. Er is geen speciale verwerking vindt plaats in de Azure Event Hub, maar het is belangrijk dat u bekend bent met de gegevens die worden opgenomen in het.

Dit document wordt niet beschreven hoe u uw gegevens opnemen, maar u kunt eenvoudig verzenden gebeurtenissen of gegevens naar een Azure Event Hub met behulp van de Event Hub-API's.

### <a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Gebruik de Azure Stream Analytics resource om bijna realtime analyses te bieden door gegevens stromen te lezen en gegevens uit te voeren naar een wille keurig aantal bronnen.

Voor de Voorspellend onderhoud voor luchtvaart oplossingssjabloon bestaat de Azure Stream Analytics-query uit vier sub-query's, elke query gebeurtenissen van de Azure Event Hub-service, met de uitvoer naar vier verschillende locaties. Deze uitvoer bestaat uit drie Power BI-gegevenssets en een Azure-opslaglocatie.

De Azure Stream Analytics-query kan worden gevonden door:

* Verbinding maken met de Azure-portal
* Het vinden van de Stream Analytics-taken ![Stream Analytics pictogram](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) die zijn gegenereerd toen de oplossing werdgeïmplementeerd (bijvoorbeeld **maintenancesa02asapbi** en **maintenancesa02asablob** voor de oplossing voor voor speld onderhoud)
* Selecteren
  
  * ***Invoer voor het*** weer geven van de query-invoer
  * ***Query*** voor het weer geven van de query zelf
  * ***Uitvoer*** voor het weer geven van de verschillende uitvoer

Informatie over het bouwen van Azure Stream Analytics query's vindt u in de naslag informatie over het [Stream Analytics query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) op MSDN.

De query's uitvoeren in deze oplossing, drie gegevenssets met bijna realtime analyses informatie over de gegevensstroom inkomende aan een Power BI-dashboard geleverd als onderdeel van deze oplossingssjabloon. Omdat er impliciete kennis op over de indeling van de binnenkomende, moeten deze query's op basis van de gegevensindeling van uw worden gewijzigd.

Met de query in de tweede Stream Analytics-taak **maintenancesa02asablob** worden alleen alle [Event hub](https://azure.microsoft.com/services/event-hubs/) -gebeurtenissen naar [Azure Storage](https://azure.microsoft.com/services/storage/) uitgevoerd en is er dus geen wijziging nodig, ongeacht uw gegevens indeling, omdat de volledige gebeurtenis gegevens naar de opslag worden gestreamd.

### <a name="azure-data-factory"></a>Azure Data Factory
De [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) -service organiseert de verplaatsing en verwerking van gegevens. In de oplossings sjabloon voor voor speld onderhoud voor sjablonen is de data factory bestaat uit drie [pijp lijnen](../../data-factory/concepts-pipelines-activities.md) die de gegevens met behulp van verschillende technologieën verplaatsen en verwerken.  Toegang tot uw data factory door het openen van de Data Factory-knooppunt aan de onderkant van het oplossingsdiagram van de sjabloon die zijn gemaakt met de implementatie van de oplossing. Fouten in uw gegevenssets worden vanwege data factory geïmplementeerd voordat u de gegevensgenerator is gestart. Deze fouten kunnen worden genegeerd en niet voorkomen dat uw data factory werkt

![Data Factory-gegevensset-fouten](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

In deze sectie worden de benodigde [pijp lijnen en activiteiten](../../data-factory/concepts-pipelines-activities.md) in de [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)besproken. Hier volgt een diagramweergave van de oplossing.

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Twee van de pijp lijnen van deze Factory bevatten [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -scripts die worden gebruikt om de gegevens te partitioneren en samen te voegen. De scripts bevinden zich in het [Azure Storage](https://azure.microsoft.com/services/storage/) -account dat tijdens de installatie is gemaakt. De locatie is: maintenancesascript\\\\script\\\\Hive\\\\ (of https://[uw oplossings naam]. blob. core. Windows. net/maintenancesascript).

Net als bij [Azure stream Analytics](#azure-stream-analytics-1) query's hebben de [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -scripts impliciete kennis over de binnenkomende gegevens indeling en moeten ze worden gewijzigd op basis van uw gegevens indeling.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Deze [pijp lijn](../../data-factory/concepts-pipelines-activities.md) bevat één activiteit: een [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) -activiteit met een [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) die een [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -script uitvoert voor het partitioneren van de gegevens die in [Azure Storage](https://azure.microsoft.com/services/storage/) worden geplaatst tijdens de [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/) taak.

Het [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -script voor deze partitie taak is ***AggregateFlightInfo. HQL***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Deze [pijp lijn](../../data-factory/concepts-pipelines-activities.md) bevat verschillende activiteiten waarvan het eind resultaat de gescoorde voor spelling is van het [Azure machine learning](https://azure.microsoft.com/services/machine-learning/) experiment dat is gekoppeld aan deze oplossings sjabloon.

Activiteiten die zijn:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) -activiteit met een [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) die een [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -script uitvoert om aggregaties en functie-engineering uit te voeren die nodig zijn voor het [Azure machine learning](https://azure.microsoft.com/services/machine-learning/) -experiment.
  Het [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) -script voor deze partitie taak is ***PrepareMLInput. HQL***.
* [Kopieer](https://msdn.microsoft.com/library/azure/dn835035.aspx) activiteit waarmee de resultaten van de [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) -activiteit worden verplaatst naar een enkele [Azure Storage](https://azure.microsoft.com/services/storage/) blob die wordt geopend door de [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) -activiteit.
* Met [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) -activiteit wordt het [Azure machine learning](https://azure.microsoft.com/services/machine-learning/) experiment aangeroepen, waarbij de resultaten in één [Azure Storage](https://azure.microsoft.com/services/storage/) BLOB worden geplaatst.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Deze [pijp lijn](../../data-factory/concepts-pipelines-activities.md) bevat één activiteit: een [Kopieer](https://msdn.microsoft.com/library/azure/dn835035.aspx) activiteit waarmee de resultaten van het [Azure machine learning](#azure-machine-learning) experiment worden verplaatst van de ***MLScoringPipeline*** naar de [Azure SQL database](https://azure.microsoft.com/services/sql-database/) ingericht als onderdeel van de installatie van de oplossings sjabloon.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Het [Azure machine learning](https://azure.microsoft.com/services/machine-learning/) experiment dat wordt gebruikt voor deze oplossings sjabloon biedt de resterende levens duur (resterende levens duur) van een vliegtuig motor. Het experiment is specifiek voor de gegevensset verbruikt en wijziging vereist of vervanging die specifiek zijn voor de gegevens op.

Voor informatie over hoe het Azure Machine Learning experiment is gemaakt, raadpleegt u voor [speld onderhoud: stap 1 van 3, gegevens voorbereiding en functie techniek](https://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>De voortgang van de
Nadat u de Gegevensgenerator wordt gestart, de pijplijn begint met het dehydrate en de verschillende onderdelen van uw oplossing start starten in actie volgende de opdrachten die zijn uitgegeven door de data factory. Er zijn twee manieren voor het bewaken van de pijplijn.

* Een van de Stream Analytics-taken schrijft de onbewerkte binnenkomende gegevens naar blob-opslag. Als u in het scherm op Blob Storage onderdeel van uw oplossing klikt en vervolgens in het rechterdeel venster op openen klikt, gaat u naar de [Azure Portal](https://portal.azure.com/). Klik op Blobs. In het volgende deelvenster ziet u een lijst met Containers. Klik op **maintenancesadata**. In het volgende deel venster vindt u de map **rawdata** . In de map rawdata zijn mappen met de namen bijvoorbeeld uur = 17 en uur = 18. De aanwezigheid van deze mappen geeft onbewerkte gegevens worden gegenereerd op uw computer en die zijn opgeslagen in blob-opslag. U ziet de csv-bestanden met beperkte grootte in MB in deze mappen.
* De laatste stap van de pijplijn is het schrijven van gegevens (bijvoorbeeld voorspellingen op basis van machine learning) in SQL-Database. Mogelijk moet wachten maximaal drie uur voor de gegevens worden weergegeven in de SQL-Database. Een manier om te controleren hoeveel gegevens er in uw SQL Database beschikbaar zijn, is via de [Azure Portal](https://portal.azure.com/). Zoek in het linkerdeel venster naar SQL-data BASEs ![SQL-pictogram](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) en klik erop. Zoek vervolgens de **pmaintenancedb** van de data base en klik erop. Klik in de volgende pagina aan de onderkant op beheren
   
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
   
   U hebt de **naam van de database server, de database naam, de gebruikers naam en het wacht woord** nodig voordat u verdergaat met de volgende stappen. Hier volgen de stappen om u te begeleiden u deze kunt vinden.
   
   * Zodra de **Azure SQL database** in het diagram van de oplossings sjabloon groen wordt, klikt u erop en klikt u vervolgens op **openen**.
   * Er wordt een nieuw browser tabblad/-venster weer gegeven met de Azure Portal-pagina. Klik op **resource groepen** in het linkerdeel venster.
   * Selecteer het abonnement dat u gebruikt voor het implementeren van de oplossing en selecteer vervolgens **' YourSolutionName\_ResourceGroup '** .
   * Klik in het nieuwe deel venster pop-out op het pictogram ![SQL-pictogram](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) om toegang te krijgen tot uw data base. De naam van uw Data Base bevindt zich naast dit pictogram (bijvoorbeeld **' pmaintenancedb '** ) en de naam van de **database server** wordt vermeld onder de eigenschap Server naam en moet er ongeveer uitzien als **YourSolutionName.database.Windows.net**.
   * De **gebruikers** naam en het **wacht woord** voor de Data Base zijn hetzelfde als de gebruikers naam en het wacht woord die eerder zijn vastgelegd tijdens de implementatie van de oplossing.
2. De gegevensbron van het koude pad-rapportbestand bijwerken met Power BI Desktop.
   
   * Dubbel klik in de map waar u het generator bestand hebt gedownload en uitgepakt op het bestand **PowerBI\\PredictiveMaintenanceAerospace. pbix** . Als u eventuele waarschuwingsberichten ziet wanneer u het bestand opent, kunt u ze negeren. Klik boven aan het bestand op **Query's bewerken**.
     
     ![Query's bewerken](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * U ziet twee tabellen, **RemainingUsefulLife** en **PMResult**. Selecteer de eerste tabel en klik op ![pictogram query instellingen](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) naast **' Bron '** onder **toegepaste stappen** in het rechter paneel **' query instellingen** '. Negeer eventuele waarschuwingsberichten die worden weergegeven.
   * Vervang in het venster pop-out **' server '** en **' data base '** door uw eigen server-en database namen en klik vervolgens op **OK**. Zorg ervoor dat u voor de server naam de poort 1433 (**YourSolutionName.database.Windows.net, 1433**) opgeeft. Verlaat het veld Data Base als **pmaintenancedb**. Negeer de waarschuwingsberichten die worden weergegeven op het scherm.
   * In het volgende pop-out venster ziet u twee opties in het linkerdeel venster (**Windows** en **Data Base**). Klik op **Data Base**, vul uw **gebruikers naam** en **wacht** woord in (de gebruikers naam en het wacht woord die u hebt ingevoerd toen u de oplossing voor het eerst implementeerde en een Azure SQL database hebt gemaakt). Controleer de optie database niveau in ***Selecteer welk niveau u deze instellingen wilt Toep assen op***. Klik vervolgens op **verbinding maken**.
   * Klik op de tweede tabel **PMResult** klik op ![navigatie pictogram](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) naast **' Bron '** onder **toegepaste stappen** in het rechterdeel venster ' **query instellingen** ' en werk de namen van de server en de data base bij, zoals in de bovenstaande stappen. Klik vervolgens op OK.
   * Nadat u terug naar de vorige pagina Begeleide bent, sluit u het venster. Er wordt een bericht weer gegeven: Klik op **Toep assen**. Klik ten slotte op de knop **Opslaan** om de wijzigingen op te slaan. Uw Power BI-bestand is nu ingesteld voor verbinding met de server. Als uw visualisaties leeg zijn, zorg er dan voor dat u de gewenste opties op de visualisaties aan alle gegevens visualiseren door te klikken op het gumpictogram in de rechterbovenhoek van de legenda's uitschakelen. Gebruik de vernieuwknop om nieuwe gegevens voor de visualisaties weer te geven. In eerste instantie alleen ziet u de seedgegevens op uw visualisaties als de data factory is gepland om te vernieuwen elke drie uur. U ziet na 3 uur nieuwe voorspellingen doorgevoerd in uw visualisaties bij het vernieuwen van de gegevens.
3. Beschrijving Publiceer het dash board koud pad om [online te Power bi](https://www.powerbi.com/). Voor deze stap hebt u een Power BI account (of een Office 365-account) nodig.
   
   * Klik op **publiceren** en een paar seconden later verschijnt een venster met de weer gave ' publiceren naar Power bi slagen! '. met een groen vinkje. Klik op de koppeling hieronder 'Open PredictiveMaintenanceAerospace.pbix in Power BI'. Zie [publiceren vanuit Power bi Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop)voor gedetailleerde instructies.
   * Als u een nieuw dash board wilt maken, klikt u op het **+** -teken naast het gedeelte **Dash boards** in het linkerdeel venster. Voer de naam 'Voorspellend onderhoud Demo' voor dit nieuwe dashboard.
   * Zodra u het rapport hebt geopend, klikt u op ![SPELd pictogram](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png) om alle visualisaties aan uw dash board vast te maken. Zie [een tegel vastmaken aan een Power bi-dash board vanuit een rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report)om gedetailleerde instructies te vinden.
     Ga naar de dashboardpagina en de grootte en locatie van uw visualisaties aanpassen en bewerken van hun titels. Zie [een tegel bewerken--formaat wijzigen, verplaatsen, naam wijzigen, vastmaken, verwijderen, Hyper Link toevoegen](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename)voor gedetailleerde instructies voor het bewerken van uw tegels. Hier volgt een voorbeeld van dashboard met sommige koude pad visualisaties zijn vastgemaakt aan het.  Afhankelijk van hoe lang u de gegevensgenerator uitvoert, kan de nummers van de visualisaties afwijken.
     <br/>
     ![laatste weer gave](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * Als u de gegevens wilt vernieuwen, houdt u de muis aanwijzer over de **PredictiveMaintenanceAerospace** -gegevensset, klikt u op ![pictogram met het weglatings teken](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png) en kiest u vervolgens **vernieuwen plannen**.
     <br/>
     **Opmerking:** Als er een waarschuwings massage wordt weer gegeven, klikt u op **referenties bewerken** en controleert u of de database referenties hetzelfde zijn als de gegevens die u in stap 1 hebt beschreven.
     <br/>
     ![planning vernieuwen](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * Vouw de sectie **planning vernieuwen** uit. Schakel 'uw gegevens actueel houden'.
     <br/>
   * Geen vernieuwing worden gepland op basis van uw behoeften. Zie [gegevens vernieuwen in Power bi](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi)voor meer informatie.

### <a name="setup-hot-path-dashboard"></a>Snelpad dashboard instellen
De volgende stappen helpen u over het visualiseren van de gegevensuitvoer van Stream Analytics-taken die zijn gegenereerd op het moment van implementatie van de oplossing. U hebt een [Power bi online](https://www.powerbi.com/) account nodig om de volgende stappen uit te voeren. Als u geen account hebt, kunt u er [een maken](https://powerbi.microsoft.com/pricing).

1. Power BI-uitvoer in Azure Stream Analytics (ASA) toevoegen.
   
   * U moet de instructies in [Azure Stream Analytics & Power BI: een dash board van Analytics voor realtime zicht baarheid van streaminggegevens](../../stream-analytics/stream-analytics-power-bi-dashboard.md) om de uitvoer van uw Azure stream Analytics-taak in te stellen als uw Power bi dash board.
   * De ASA-query heeft drie uitvoer, **aircraftmonitor**, **aircraftalert**en **flightsbyhour**. U kunt de query weer geven door te klikken op het tabblad query. overeenkomt met elk van deze tabellen, moet u een uitvoer toevoegen aan ASA. Wanneer u de eerste uitvoer (**aircraftmonitor**) toevoegt, moet u ervoor zorgen dat de **uitvoer alias**, de naam van de **gegevensset** en de **tabel naam** hetzelfde zijn (**aircraftmonitor**). Herhaal de stappen voor het toevoegen van uitvoer voor **aircraftalert**en **flightsbyhour**. Nadat u alle drie uitvoertabellen en aan de slag de ASA-taak hebt toegevoegd, moet u een bevestigingsbericht wordt weergegeven ('vanaf Stream Analytics-taak maintenancesa02asapbi is voltooid').
2. Meld u online aan bij [Power bi](https://www.powerbi.com)
   
   * In de sectie gegevens sets van het linkerdeel venster in mijn werk ruimte moeten de ***gegevensset*** namen **aircraftmonitor**, **aircraftalert**en **flightsbyhour** worden weer gegeven. Dit is de streaminggegevens die u van Azure Stream Analytics in de vorige stap hebt gepusht. De **flightsbyhour** van de gegevensset wordt mogelijk niet tegelijk met de andere twee gegevens sets weer gegeven vanwege de aard van de SQL-query achter deze. Echter, moet het weergegeven na een uur.
   * Zorg ervoor dat het deel venster ***Visualisaties*** is geopend en wordt weer gegeven aan de rechter kant van het scherm.
3. Zodra u de gegevens die binnenkomen in Power BI hebt, kun u de streaming-gegevens te visualiseren. Hieronder is een voorbeeld van dashboard met sommige visualisaties snelpad vastgemaakt aan het. U kunt andere dashboardtegels op basis van de juiste gegevenssets maken. Afhankelijk van hoe lang u de gegevensgenerator uitvoert, kan de nummers van de visualisaties afwijken.

    ![Dashboardweergave](media/cortana-analytics-technical-guide-predictive-maintenance/dashboard-view.png)

1. Hier volgen enkele stappen voor het maken van een van de bovenstaande tegels: de tegel ' vloot weergave van sensor 11 versus drempel waarde 48,26 '.
   
   * Klik op gegevensset **aircraftmonitor** in het gedeelte gegevens sets van het linkerdeel venster.
   * Klik op het pictogram **lijn diagram** .
   * Klik op **verwerkt** in het deel venster **velden** , zodat deze onder as wordt weer gegeven in het deel venster **Visualisaties** .
   * Klik op "S11" en "S11\_waarschuwing" zodat deze beide onder "waarden" worden weer gegeven. Klik op de kleine pijl naast **S11** en **S11\_waarschuwing**, Wijzig ' sum ' in ' Average '.
   * Klik bovenaan op **Opslaan** en noem het rapport ' aircraftmonitor '. Het rapport met de naam ' aircraftmonitor ' wordt weer gegeven in de sectie **rapporten** in het **Navigatie** deel venster aan de linkerkant.
   * Klik op het pictogram voor het **visuele element** in de rechter bovenhoek van dit lijn diagram. Een 'Vastmaken aan Dashboard'-venster kan weergegeven waarin u kiest een dashboard. Selecteer 'Voorspellend onderhoud Demo', en klik vervolgens op 'Pincode'.
   * Houd de muis aanwijzer boven deze tegel op het dash board en klik op het pictogram bewerken in de rechter bovenhoek om de titel te wijzigen in ' vloot weergave van sensor 11 versus drempel waarde 48,26 ' en ondertitel op ' gemiddeld over de hele periode '.

## <a name="delete-your-solution"></a>Uw oplossing verwijderen
Zorg ervoor dat u de gegevensgenerator stopt wanneer u de oplossing niet actief gebruikt als de gegevensgenerator worden hogere kosten. De oplossing te verwijderen als u deze niet gebruikt. Uw oplossing verwijdert, worden de onderdelen die zijn ingericht in uw abonnement tijdens de implementatie van de oplossing. Als u de oplossing wilt verwijderen, klikt u op de naam van de oplossing in het linkerdeel venster van de oplossings sjabloon en klikt u vervolgens op **verwijderen**.

## <a name="cost-estimation-tools"></a>Kostenraming-hulpprogramma 's
De volgende twee hulpprogramma's zijn beschikbaar waarmee u meer informatie over de totale kosten die betrokken zijn bij het uitvoeren van de Voorspellend onderhoud voor luchtvaart oplossingssjabloon in uw abonnement:

* [Hulp programma voor Microsoft Azure kosten Estimator (online)](https://azure.microsoft.com/pricing/calculator/)
* [Hulp programma voor Microsoft Azure kosten Estimator (Desktop)](https://www.microsoft.com/download/details.aspx?id=43376)

