---
title: Een Azure-data factory maken met behulp van de Azure Data Factory-gebruikers interface
description: Maak een gegevensfactory met een pijplijn die gegevens kopieert van de ene locatie naar de andere in een Azure Blob-opslag.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 02/25/2020
ms.author: jingwang
ms.openlocfilehash: f7a70454e395f5f9d39266e0777749e1fcbef68e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81419354"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Snelstartgids: een data factory maken met behulp van de Azure Data Factory-gebruikers interface

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Huidige versie](quickstart-create-data-factory-portal.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze quickstart wordt beschreven hoe u de Azure Data Factory-UI kunt gebruiken om een Azure-gegevensfactory te maken en te controleren. Met de pijplijn die u in deze data factory maakt, worden gegevens *gekopieerd* van één map naar een andere map in een Azure Blob-opslag. Zie [Zelfstudie: Gegevens transformeren met Spark](tutorial-transform-data-spark-portal.md) voor meer informatie over het *transformeren* van gegevens met Azure Data Factory.

> [!NOTE]
> Als u niet bekend bent met Azure Data Factory, raadpleegt u eerst de [Inleiding voor Azure Data Factory](data-factory-introduction.md) voordat u deze quickstart uitvoert. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Video 
Bekijk deze video voor een beter begrip van de Data Factory-UI: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
1. Ga naar de [Azure Portal](https://portal.azure.com). 
1. Selecteer in het menu Azure Portal de optie **een resource maken**.
   
   ![Selecteer een resource maken in het menu Azure Portal](./media/doc-common-process/create-a-resource.png)
1. Selecteer **analyse**en selecteer vervolgens **Data Factory**. 
   
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Voer op de pagina **Nieuwe data factory****ADFTutorialDataFactory** in als **naam**. 
 
   De naam van de Azure-data factory moet *wereld wijd uniek*zijn. Als u de volgende fout ziet, wijzigt u de naam van de Data Factory (bijvoorbeeld ** &lt;&gt;ADFTutorialDataFactory**) en probeert u het opnieuw. Zie het artikel [Data factory - Naamgevingsregels](naming-rules.md) voor naamgevingsregels voor Data Factory-artefacten.
  
   ![Fout als de naam niet beschikbaar is](./media/doc-common-process/name-not-available-error.png)
1. Selecteer voor **Abonnement** het Azure-abonnement waarin u de gegevensfactory wilt maken. 
1. Voer een van de volgende stappen uit voor **Resourcegroep**:
     
   - Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de lijst. 
   - Selecteer **nieuwe maken**en voer de naam van een resource groep in.   
         
   Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
1. Selecteer **V2** als **Versie**.
1. Selecteer voor **Locatie** een locatie voor de data factory.

   De lijst bevat alleen locaties die worden ondersteund door Data Factory en waar uw Azure Data Factory-metagegevens worden opgeslagen. De bijbehorende gegevens archieven (zoals Azure Storage en Azure SQL Database) en berekeningen (zoals Azure HDInsight) die Data Factory gebruikt, kunnen in andere regio's worden uitgevoerd.

1. Selecteer **Maken**. Nadat het maken is voltooid, selecteert u **naar resource gaan** om naar de pagina **Data Factory** te gaan. 

1. Selecteer de tegel **Maken en controleren** om de gebruikersinterface (UI) van Azure Data Factory te openen op een afzonderlijk tabblad.
   
   ![Startpagina van Data Factory, met de tegel Maken en controleren](./media/doc-common-process/data-factory-home-page.png)
   
   > [!NOTE]
   > Als u ziet dat de webbrowser is vastgelopen bij ' autoriseren ', wist u het selectie vakje cookies van derden **en site gegevens blok keren** . U kunt ook een uitzonde ring maken voor **login.microsoftonline.com**en vervolgens proberen om de app opnieuw te openen.
   
1. Schakel op de pagina **Aan de slag** over naar het tabblad **Auteur** in het linkerpaneel. 

    ![Pagina Aan de slag](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-a-linked-service"></a>Een gekoppelde service maken
In deze procedure maakt u een gekoppelde service om uw Azure Storage-account te koppelen aan de data factory. De gekoppelde service beschikt over de verbindingsgegevens die de Data Factory-service tijdens runtime gebruikt om er een verbinding mee tot stand te brengen.

1. Selecteer **verbindingen**en selecteer vervolgens de knop **Nieuw** op de werk balk (de knop**verbindingen** bevindt zich onderaan de linkerkolom onder **Factory-resources**). 

1. Selecteer op de pagina **Nieuwe gekoppelde service** de optie **Azure Blob-opslag** en selecteer vervolgens**Doorgaan**. 

1. Voer de volgende stappen uit op de pagina nieuwe gekoppelde service (Azure Blob Storage): 

   a. Voer bij **Naam****AzureStorageLinkedService** in.

   b. Voor de naam van het **opslag account**selecteert u de naam van uw Azure Storage-account.

   c. Selecteer **Verbinding testen** om te bevestigen dat de Data Factory-service verbinding mag maken met het opslagaccount. 

   d. Selecteer **maken** om de gekoppelde service op te slaan. 

      ![Nieuwe gekoppelde service](./media/quickstart-create-data-factory-portal/linked-service.png)


## <a name="create-datasets"></a>Gegevenssets maken
In deze procedure maakt u twee gegevenssets: **InputDataset** en **OutputDataset**. Deze gegevenssets zijn van het type **AzureBlob**. Ze verwijzen naar de gekoppelde Azure Storage-service die u in de vorige sectie hebt gemaakt. 

De invoergegevensset vertegenwoordigt de brongegevens in de invoermap. In de definitie van de invoergegevensset geeft u de blob-container (**adftutorial**) en de map (**invoer**) op, en het bestand (**emp.txt**) dat de brongegevens bevat. 

De uitvoergegevensset vertegenwoordigt de gegevens die worden gekopieerd naar de bestemming. In de definitie van de uitvoergegevensset geeft u de blob-container (**adftutorial**) en de map (**uitvoer**) op, en het bestand waarin de brongegevens zijn gekopieerd. Elke uitvoering van een pijplijn heeft een unieke id die eraan is gekoppeld. U hebt toegang tot deze id via de systeemvariabele **RunId**. De naam van het uitvoerbestand wordt dynamisch geëvalueerd op basis van de run-id van de pijplijn.   

In de instellingen voor de gekoppelde service hebt u het Azure Storage-account opgegeven dat de bron gegevens bevat. In de instellingen voor de brongegevensset geeft u de precieze locatie van de brongegevens op (blob-container, map en bestand). In de instellingen voor de sink-gegevensset geeft u de locatie op waarnaar de gegevens worden gekopieerd (blob-container, map en bestand). 
 
1. Selecteer de **+** knop (plus) en selecteer vervolgens **gegevensset**.

   ![Menu om een gegevensset te maken](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
1. Selecteer op de pagina **nieuwe gegevensset** de optie **Azure Blob Storage**en selecteer vervolgens **door gaan**. 

1. Kies op de pagina **indeling selecteren** het notatie type van uw gegevens en selecteer vervolgens **door gaan**. In dit geval selecteert u **binair** bij kopiëren van bestanden zonder de inhoud te parseren.

   ![Indeling selecteren](./media/quickstart-create-data-factory-portal/select-format.png)
   
1. Voer de volgende stappen uit op de pagina **Eigenschappen instellen** :

    a. Voer onder **naam** **input dataset**in. 

    b. Selecteer **AzureStorageLinkedService** bij **Linked service**.

    c. Selecteer de knop **Bladeren** bij **Bestandspad**.

    d. Blader in het venster **een bestand of map kiezen** naar de map **invoer** in de container **adftutorial** , selecteer het bestand **EMP. txt** en selecteer vervolgens **OK**.
    
    e. Selecteer **OK**.   

    ![Eigenschappen instellen voor input DataSet](./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png)
1. Herhaal de stappen om de uitvoergegevensset te maken:  

    a. Selecteer de **+** knop (plus) en selecteer vervolgens **gegevensset**.

    b. Selecteer op de pagina **nieuwe gegevensset** de optie **Azure Blob Storage**en selecteer vervolgens **door gaan**.

    c. Kies op de pagina **indeling selecteren** het notatie type van uw gegevens en selecteer vervolgens **door gaan**.

    d. Geef op de pagina **Eigenschappen instellen** **output dataset** op voor de naam. Selecteer **AzureStorageLinkedService** als gekoppelde service.

    e. Voer **onder bestandspad** **adftutorial/output**in. Als de **uitvoermap** niet bestaat, maakt de Kopieer activiteit tijdens runtime.

    f. Selecteer **OK**.   

    ![Eigenschappen instellen voor output DataSet](./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png)
## <a name="create-a-pipeline"></a>Een pijplijn maken 
In deze procedure maakt en valideert u een pijplijn via een kopieeractiviteit die gebruikmaakt van de invoer- en uitvoergegevenssets. Met de kopieeractiviteit worden gegevens uit het bestand dat is opgegeven bij de instellingen voor de invoergegevensset gekopieerd naar het bestand dat is opgegeven in de instellingen voor de uitvoergegevensset. Als in de invoergegevensset alleen een map is opgegeven (en geen bestandsnaam), worden met de kopieeractiviteit alle bestanden in de bronmap gekopieerd naar de bestemming. 

1. Selecteer de **+** knop (plus) en selecteer vervolgens **pijp lijn**. 

1. Geef op het tabblad **Algemeen****CopyPipeline** op als **Naam**. 

1. Open de werkset **Activiteiten** en vouw **Verplaatsen en transformeren** uit. Sleep de **gegevens kopiëren** activiteit vanuit de werkset **activiteiten** naar het ontwerp oppervlak voor pijp lijnen. U kunt ook zoeken naar activiteiten in de werkset **Activiteiten**. Geef **CopyFromBlobToBlob** op bij **Naam**.
   ![Een activiteit gegevens kopiëren maken](./media/quickstart-create-data-factory-portal/copy-activity.png)

1. Ga naar het tabblad **Bron** in de instellingen voor de kopieeractiviteit en selecteer **InputDataset** als de **brongegevensset**.

1. Ga naar het tabblad **Sink** in de instellingen voor de kopieeractiviteit en selecteer **OutputDataset** bij **Sink-gegevensset**.

1. Klik in de pijplijnwerkbalk boven het canvas op **Valideren** om de instellingen voor de pijplijn te valideren. Controleer of de pijplijn is gevalideerd. Selecteer de **>>** (pijl-rechts) om de uitvoer van de validatie te sluiten. 
   ![Een pijp lijn valideren](./media/quickstart-create-data-factory-portal/pipeline-validate.png)

## <a name="debug-the-pipeline"></a>Fouten opsporen in de pijplijn
In deze stap spoort u fouten op in de pijplijn voordat u deze implementeert in Data Factory. 

1. Klik in de pijplijnwerkbalk boven het canvas op **Fouten opsporen** om een testuitvoering te activeren. 
    
1. Controleer of de status van de pijplijnuitvoering onder aan het tabblad **Uitvoer** van de pijplijninstellingen wordt weergegeven. 
 
    ![Uitvoer pijplijn uitvoering](./media/quickstart-create-data-factory-portal/pipeline-output.png)

1. Controleer of een uitvoerbestand wordt weergegeven in de **uitvoermap** van de container **adftutorial**. Als de uitvoermap niet bestaat, wordt deze automatisch door de Data Factory-service gemaakt. 

## <a name="trigger-the-pipeline-manually"></a>De pijplijn handmatig activeren
In deze procedure implementeert u entiteiten (gekoppelde services, gegevenssets, pijplijnen) in Azure Data Factory. Vervolgens activeert u handmatig een pijplijnuitvoering. 

1. Voordat u een pijplijn activeert, moet u eerst entiteiten publiceren in Data Factory. Als u wilt publiceren, selecteert u **Alles publiceren** bovenaan. 
    ![Alles publiceren](./media/quickstart-create-data-factory-portal/publish-all.png)

1. Als u de pijp lijn hand matig wilt activeren, selecteert u **trigger toevoegen** op de werk balk van de pijp lijn en selecteert u vervolgens **nu activeren**. Selecteer op de pagina **pijplijn uitvoering** de optie **volt ooien**.

## <a name="monitor-the-pipeline"></a>De pijplijn bewaken

1. Ga naar het tabblad **Controleren** aan de linkerkant. Gebruik de knop **Vernieuwen** om de lijst te vernieuwen.

   ![Tabblad voor het bewaken van pijplijn uitvoeringen](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
1. Selecteer de koppeling **CopyPipeline** . u ziet nu de status van de uitvoering van de Kopieer activiteit op deze pagina. 

1. Als u details over de Kopieer bewerking wilt weer geven, selecteert u de koppeling **Details** (bril-afbeelding). Zie [Overzicht van kopieeractiviteit](copy-activity-overview.md) voor meer informatie over de eigenschappen. 

   ![Details van de kopieerbewerking](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
1. Controleer of een nieuw bestand wordt weergegeven in de **uitvoermap**. 
1. U kunt terugschakelen naar de weer gave **pijplijn uitvoeringen** in de weer gave **uitvoeringen van activiteit** door de koppeling **alle pijplijn uitvoeringen** te selecteren. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>De pijplijn activeren volgens een schema
Deze procedure is optioneel in deze zelfstudie. U kunt een *Scheduler-trigger* maken om in te plannen dat de pijplijn periodiek wordt uitgevoerd (elk uur, dagelijks, enzovoort). In deze procedure maakt u een trigger die elke minuut wordt uitgevoerd en stopt op een door u bepaald tijdstip. 

1. Schakel over naar het tabblad **Auteur**. 

1. Ga naar uw pijp lijn, selecteer **trigger toevoegen** op de werk balk van de pijp lijn en selecteer vervolgens **Nieuw/bewerken**. 

1. Selecteer op de pagina **Triggers toevoegen** de optie **Trigger kiezen** en selecteer vervolgens **Nieuw**. 

1. Op de pagina **nieuwe trigger** , onder **beëindigen**, selecteert u **op datum**, geeft u een eind tijd op een paar minuten na de huidige tijd op en selecteert u **OK**. 

   Aan elke pijplijnuitvoering zijn kosten verbonden. Geef daarom een eindtijd op die slechts enkele minuten later is dan de begintijd. Zorg ervoor dat de eindtijd op dezelfde dag is. Zorg echter dat er voldoende tijd is om de pijp lijn uit te voeren tussen de publicatie tijd en de eind tijd. De trigger gaat pas van kracht nadat u de oplossing hebt gepubliceerd in Data Factory, niet wanneer u de trigger opslaat in de UI. 

1. Schakel op de pagina **nieuwe trigger** het selectie vakje **geactiveerd** in en selecteer vervolgens **OK**. 

   ![Nieuwe trigger instelling](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
1. Lees het waarschuwings bericht en selecteer **OK**.

1. Selecteer **Alles publiceren** om wijzigingen naar Data Factory te publiceren. 

1. Ga naar het tabblad **Controleren** aan de linkerkant. Selecteer **Vernieuwen** om de lijst te vernieuwen. U ziet dat de pijplijn één keer per minuut wordt uitgevoerd vanaf het moment van publiceren tot de eindtijd. 

   Let op de waarden in de kolom **geactiveerd door** . De handmatige triggeruitvoering is afkomstig uit de stap (**Nu activeren**) die u eerder hebt uitgevoerd. 

1. Schakel over naar de weer gave **trigger uitvoeringen** . 

1. Controleer of er tot de opgegeven datum en tijd voor elke pijplijnuitvoering een uitvoerbestand is gemaakt in de **uitvoermap**. 

## <a name="next-steps"></a>Volgende stappen
Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene locatie naar een andere locatie in een Azure Blob-opslag. Doorloop de [zelfstudies](tutorial-copy-data-portal.md) voor meer informatie over het gebruiken van Data Factory in andere scenario's. 
