---
title: De Azure Portal gebruiken om een data factory pijp lijn te maken
description: Deze zelfstudie bevat stapsgewijze instructies voor het maken van een data factory met een pijplijn met behulp van Azure Portal. De pijplijn gebruikt de kopieeractiviteit om gegevens vanuit Azure Blob-opslag naar een SQL database te kopiëren.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: 4c6b96f4b8b3a0a90c136967578bfb56c83b7e3f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439491"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Gegevens kopiëren van Azure Blob-opslag naar een SQL database met Azure Data Factory
In deze zelfstudie maakt u een data factory met behulp van de Azure Data Factory-gebruikersinterface. Met de pijplijn in deze data factory worden gegevens gekopieerd van Azure Blob-opslag naar een SQL database. Het configuratiepatroon in deze zelfstudie geldt voor het kopiëren van een gegevensarchief op basis van bestanden naar een relationeel gegevensarchief. Zie de tabel [Ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die worden ondersteund als bron en als sink.

> [!NOTE]
> - Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Een pijplijn met kopieeractiviteit maken.
> * De uitvoering van de pijplijn testen.
> * De pijplijn handmatig activeren.
> * De pijplijn activeren volgens een schema.
> * De uitvoering van de pijplijn en van de activiteit controleren

## <a name="prerequisites"></a>Vereisten
* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslagaccount**. U gebruikt de blobopslag als *bron*-gegevensopslag. Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-quickstart-create-account.md) om een account te maken.
* **Azure SQL-database**. U gebruikt de database als *sink*-gegevensopslag. Als u geen SQL database hebt, raadpleegt u het artikel [Een SQL-database maken](../sql-database/sql-database-get-started-portal.md) om een database te maken.

### <a name="create-a-blob-and-a-sql-table"></a>Een blob en een SQL-tabel maken

Voer nu de volgende stappen uit om uw blobopslag en SQL database voor te bereiden voor gebruik in deze zelfstudie.

#### <a name="create-a-source-blob"></a>Een bron-blob maken

1. Start Kladblok. Kopieer de volgende tekst en sla deze op uw schijf op in het bestand **emp.txt**:

    ```
    John,Doe
    Jane,Doe
    ```

1. Maak in de blobopslag een container met de naam **adftutorial**. Maak een map aan met de naam **invoer** in deze container. Vervolgens kunt u het bestand **emp.txt** uploaden naar de map **invoer**. Gebruik de Azure-portal of hulpprogramma's zoals [Azure Storage Explorer](https://storageexplorer.com/) voor deze taken.

#### <a name="create-a-sink-sql-table"></a>Een SQL-sink-tabel maken

1. Gebruik het volgende SQL-script om de tabel **dbo.emp** te maken in uw SQL database:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

1. Geef Azure-services toegang tot SQL Server. Zorg ervoor dat **Toegang tot Azure-services toestaan** is **ingeschakeld** voor SQL Server, zodat Data Factory gegevens naar SQL Server kan schrijven. Om deze instelling te controleren en in te scha kelen, gaat u naar de > overzicht van Azure SQL Server > Set Server Firewall > Stel de optie **toegang tot Azure-Services toestaan** **in op aan**.

## <a name="create-a-data-factory"></a>Een data factory maken
In deze stap maakt u een data factory en start u de Data Factory-gebruikersinterface om een pijplijn te maken in de data factory. 

1. Open **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
2. Selecteer in het menu aan de linkerkant **een resource maken** > **Analytics** > **Data Factory**: 
  
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in bij **Naam**. 
 
   De naam van de Azure-gegevensfactory moet *wereldwijd uniek* zijn. Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. (bijvoorbeeld Uwnaamadftutorialdatafactory). Zie [Data Factory naming rules](naming-rules.md) (Naamgevingsregels Data Factory) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
        
     ![Nieuwe data factory](./media/doc-common-process/name-not-available-error.png)
4. Selecteer het Azure-**abonnement** waarin u de data factory wilt maken. 
5. Voer een van de volgende stappen uit voor **Resourcegroep**:
     
    a. Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    b. Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in. 
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie. 
6. Selecteer **V2** onder **Versie**.
7. Selecteer onder **Locatie** een locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven (bijvoorbeeld Azure Storage en SQL Database) en berekenservices (bijvoorbeeld Azure HDInsight) die door de data factory worden gebruikt, kunnen zich in andere regio's bevinden.
8. Selecteer **Maken**. 
9. Nadat het maken is voltooid, ziet u de melding in meldingen centrum. Selecteer **naar resource gaan** om naar de pagina Data Factory te gaan.
10. Selecteer de tegel **Maken en controleren** om de Data Factory-gebruikersinterface te openen op een afzonderlijk tabblad.


## <a name="create-a-pipeline"></a>Een pijplijn maken
In deze stap maakt u een pijplijn met kopieeractiviteit in de data factory. De kopieeractiviteit kopieert gegevens van de blobopslag naar SQL Database. In de [Quickstart](quickstart-create-data-factory-portal.md) hebt u een pijplijn gemaakt met de volgende stappen:

1. Maak een gekoppelde service. 
1. Maak invoer- en uitvoergegevenssets.
1. Maak een pijplijn.

In deze zelfstudie begint u met het maken van de pijplijn. Vervolgens maakt u gekoppelde services en gegevenssets wanneer u deze nodig hebt om de pijplijn te configureren. 

1. Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**. 

   ![Pijplijn maken](./media/doc-common-process/get-started-page.png)
1. Voer op het tabblad **Algemeen** voor de pijplijn **CopyPipeline** in voor de **Naam** van de pijplijn.

1. Vouw in het dialoog venster **activiteiten** de categorie **verplaatsen en transformeren** uit en sleep de **gegevens kopiëren** activiteit van het vak naar het ontwerp oppervlak voor pijp lijnen. Geef **CopyFromBlobToSql** op bij **Naam**.

    ![Kopieeractiviteit](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Bron configureren

1. Ga naar het tabblad **bron** . Selecteer **+ Nieuw** om een bron-gegevensset te maken. 

1. Selecteer in het dialoog venster **nieuwe gegevensset** de optie **Azure Blob Storage**en selecteer vervolgens **door gaan**. De brongegevens bevinden zich in een blobopslag, daarom selecteert u **Azure Blob-opslag** voor de brongegevensset. 

1. Kies in het dialoog venster **indeling selecteren** het notatie type van uw gegevens en selecteer vervolgens **door gaan**.

    ![Type gegevens indeling](./media/doc-common-process/select-data-format.png)

1. Voer in het dialoog venster **set** -eigenschappen **SourceBlobDataset** in als naam. Klik naast het tekstvak **Gekoppelde service** op **+Nieuw**. 
    
1. Voer in het dialoog venster **nieuwe gekoppelde service (Azure Blob Storage)** **AzureStorageLinkedService** als naam in, selecteer uw opslag account in de lijst naam van het **opslag account** . Test de verbinding en selecteer vervolgens **volt ooien** om de gekoppelde service te implementeren.

1. Nadat de gekoppelde service is gemaakt, wordt deze teruggestuurd naar de pagina **Eigenschappen instellen** . Selecteer naast **Bestandspad** de knop **Bladeren**.

1. Navigeer naar de map **adftutorial/input**, selecteer het bestand **emp.txt** en klik vervolgens op **Voltooien**.

1. Er wordt automatisch naar de pijplijn pagina genavigeerd. Controleer op het tabblad **bron** of **SourceBlobDataset** is geselecteerd. Selecteer **Gegevens vooraf bekijken** om een voorbeeld van de gegevens op deze pagina te bekijken. 
    
    ![Brongegevensset](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Sink configureren

1. Ga naar het tabblad **Sink** en selecteer **+Nieuw** om een sink-gegevensset te maken. 

1. In het dialoog venster **nieuwe gegevensset** voert u in het zoekvak de invoer ' SQL ' in om de connectors te filteren, selecteert u **Azure SQL database**en selecteert u vervolgens **door gaan**. In deze zelfstudie kopieert u gegevens naar een SQL database. 

1. Voer in het dialoog venster **set** -eigenschappen **OutputSqlDataset** in als naam. Klik naast het tekstvak **Gekoppelde service** op **+Nieuw**. Een gegevensset moet worden gekoppeld aan een gekoppelde service. De gekoppelde service beschikt over de verbindingsreeks die door Data Factory wordt gebruikt om tijdens de uitvoering verbinding te maken met de SQL database. De dataset geeft informatie over de container, map en het bestand (optioneel) met de brongegevens. 
      
1. Voer in het dialoog venster **nieuwe gekoppelde service (Azure SQL database)** de volgende stappen uit: 

    a. Geef **AzureSqlDatabaseLinkedService** op als **Naam**.

    b. Selecteer bij **Servernaam** uw SQL Server-exemplaar.

    c. Selecteer uw SQL-database bij **Databasenaam**.

    d. Voer bij **Gebruikersnaam** de naam van de gebruiker in.

    e. Geef bij **Wachtwoord** het wachtwoord van de gebruiker op.

    f. Als u de verbinding wilt testen, selecteert u **Verbinding testen**.

    g. Selecteer **volt ooien** om de gekoppelde service te implementeren. 
    
    ![Nieuwe gekoppelde service opslaan](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Er wordt automatisch naar het dialoog venster **Eigenschappen instellen** genavigeerd. Selecteer bij **Tabel** **[dbo].[emp]** . Selecteer vervolgens **Voltooien**.

1. Ga naar het tabblad met de pijplijn en controleer bij **Sink-gegevensset** of **OutputSqlDataset** is geselecteerd.

    ![Tabblad Pijplijn](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

U kunt eventueel het schema van de bron toewijzen aan het bijbehorende schema van bestemming door de volgende [schema toewijzing te volgen in de Kopieer activiteit](copy-activity-schema-and-type-mapping.md)
    
## <a name="validate-the-pipeline"></a>De pijplijn valideren
Selecteer in de werkbalk **Valideren** om de pijplijn te valideren.
 
U ziet de JSON-code die is gekoppeld aan de pijp lijn door te klikken op **code** in de rechter bovenhoek.

## <a name="debug-and-publish-the-pipeline"></a>Fouten opsporen in de pijplijn en de pijplijn publiceren
U kunt fouten opsporen in een pijplijn voordat u artefacten (gekoppelde services, gegevenssets en pijplijn) publiceert naar Data Factory of uw eigen Azure Repos Git-opslagplaats. 

1. Selecteer **Fouten opsporen** om fouten op te sporen in de pijplijn. De status van de pijplijnuitvoering wordt weergegeven op het tabblad **Uitvoer** onder in het venster. 

1. Zodra de pijp lijn kan worden uitgevoerd, selecteert u in de bovenste werk balk **Alles publiceren**. Met deze actie publiceert u entiteiten (gegevenssets en pijplijnen) die u hebt gemaakt met Data Factory.

1. Wacht tot u het bericht **Publiceren gelukt** ziet. Om meldingsberichten te zien, klikt u op **Meldingen weergeven** rechts bovenin (belknop). 

## <a name="trigger-the-pipeline-manually"></a>De pijplijn handmatig activeren
In deze stap moet u handmatig de pijplijn activeren, die u in de vorige stap heeft gepubliceerd. 

1. Selecteer **trigger toevoegen** op de werk balk en selecteer **nu activeren**. Klik op de pagina **Pijplijnuitvoering** op **Voltooien**.  

1. Ga naar het tabblad **Controleren** aan de linkerkant. U ziet een pijplijn die wordt geactiveerd door een handmatige trigger. U kunt via de links in de kolom **Acties** details van de activiteiten bekijken en de pijplijn opnieuw uitvoeren.

    ![Pijplijnuitvoeringen controleren](./media/tutorial-copy-data-portal/monitor-pipeline.png)

1. Selecteer de link **Uitvoeringen van activiteit weergeven** in de kolom **Acties** om de activiteituitvoeringen te zien die zijn gekoppeld aan de pijplijnuitvoering. In dit voor beeld is er slechts één activiteit, zodat er slechts één vermelding in de lijst wordt weer gegeven. Selecteer de link **Details** (pictogram van een bril) in de kolom **Acties** om details over de kopieerbewerking te zien. Selecteer de **pijp lijn wordt** aan de bovenkant uitgevoerd om terug te gaan naar de weer gave pijplijn uitvoeringen. Selecteer **Vernieuwen** om de weergave te vernieuwen.

    ![Uitvoering van activiteiten controleren](./media/tutorial-copy-data-portal/view-activity-runs.png)

1. Controleer of er twee extra rijen zijn toegevoegd aan de **emp**-tabel in de SQL database. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>De pijplijn activeren volgens een schema
In dit schema maakt u een planningstrigger voor de pijplijn. De trigger voert de pijplijn uit volgens de opgegeven planning, bijvoorbeeld elk uur of dagelijks. Hier stelt u de trigger zo in dat deze elke minuut wordt uitgevoerd tot de opgegeven eind datum. 

1. Ga naar het tabblad **Auteur** links boven op het tabblad Monitor. 

1. Ga naar uw pijp lijn, klik op **trigger toevoegen** op de werk balk en selecteer **Nieuw/bewerken**. 

1. Selecteer in het dialoog venster **triggers toevoegen** de optie **+ Nieuw** voor het **trigger gebied kiezen** .

1. Voer in het venster **Nieuwe trigger** de volgende stappen uit: 

    a. Geef **RunEveryMinute** op bij **Naam**.

    b. Selecteer bij **Einde** de optie **Op datum**.

    c. Selecteer de vervolgkeuzelijst onder **Eindigen op**.

    d. Selecteer de optie **Huidige dag**. De dag is standaard ingesteld op de volgende dag.

    e. Werk het deel van de **eind tijd** bij tot een paar minuten na de huidige datum/tijd. De trigger wordt pas geactiveerd nadat u de wijzigingen publiceert. Als u deze slechts enkele minuten uit elkaar instelt en u deze niet publiceert, ziet u geen trigger uitvoering.

    f. Selecteer **Toepassen**. 

    g. Selecteer bij **geactiveerd** de optie **Ja**. 

    h. Selecteer **Next**.

    ![Knop Geactiveerd](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Er zijn kosten verbonden aan elke pijplijnuitvoering, dus stel een geschikte einddatum in. 
1. Lees de waarschuwing op de pagina **Parameters voor pijplijnuitvoeringen** en selecteer vervolgens **Voltooien**. De pijplijn in dit voorbeeld gebruikt geen parameters. 

1. Klik op **Alles publiceren** om de wijziging te publiceren. 

1. Ga naar het tabblad **Controleren** aan de linkerkant om de geactiveerde pijplijnuitvoeringen te bekijken. 

    ![Geactiveerde pijplijnuitvoeringen](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)   
 
1. Als u wilt overschakelen van de weer gave **pijplijn uitvoeringen** naar de weer gave **trigger uitvoeringen** , selecteert u **trigger uitvoeringen** boven aan het venster.

1. U ziet de triggeruitvoeringen in een lijst. 

1. Controleer of er twee rijen per minuut (voor elke pijplijnuitvoering) tot de opgegeven eindtijd in de **emp**-tabel worden ingevoegd. 

## <a name="next-steps"></a>Volgende stappen
Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene locatie naar een andere locatie in een blobopslag. U hebt geleerd hoe u: 

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Een pijplijn met kopieeractiviteit maken.
> * De uitvoering van de pijplijn testen.
> * De pijplijn handmatig activeren.
> * De pijplijn activeren volgens een schema.
> * De uitvoering van de pijplijn en van de activiteit controleren


Ga verder met de volgende zelfstudie als u wilt weten hoe u on-premises gegevens kopieert naar de cloud: 

> [!div class="nextstepaction"]
>[On-premises gegevens kopiëren naar de cloud](tutorial-hybrid-copy-portal.md)
