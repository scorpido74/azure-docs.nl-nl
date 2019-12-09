---
title: Gegevens bulksgewijs kopiëren met Azure Portal
description: Informatie over het gebruik van Azure Data Factory en Activiteit kopiëren om gegevens bulksgewijs van een brongegevensopslag naar een doelgegevensopslag te kopiëren.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 06/22/2018
ms.openlocfilehash: e0e6ffc45d55dc76abdbdf839958479b2ac5d40b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926701"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Meerdere tabellen bulksgewijs kopiëren met behulp van Azure Data Factory

Deze zelfstudie demonstreert het **kopiëren van een aantal tabellen uit Azure SQL Database naar Azure SQL Data Warehouse**. U kunt hetzelfde patroon toepassen in andere kopieerscenario's. Bijvoorbeeld het kopiëren van tabellen van SQL Server/Oracle naar Azure SQL Database/Data Warehouse/Azure Blob, verschillende paden kopiëren van Blob naar Azure SQL Database-tabellen.

> [!NOTE]
> - Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

Op hoog niveau bevat deze zelfstudie de volgende stappen:

> [!div class="checklist"]
> * Een gegevensfactory maakt.
> * Gekoppelde Azure SQL Database-, Azure SQL Data Warehouse- en Azure Storage-services maken.
> * Gegevenssets voor Azure SQL Database en Azure SQL Data Warehouse maken.
> * Een pijplijn maken om de te kopiëren tabellen op te zoeken en een andere pijplijn om de kopieerbewerking daadwerkelijk uit te voeren. 
> * Een pijplijnuitvoering starten.
> * De uitvoering van de pijplijn en van de activiteit controleren.

In deze zelfstudie wordt Azure Portal gebruikt. Zie [Quickstarts](quickstart-create-data-factory-dot-net.md) (Snelstartgidsen) voor meer informatie over het gebruik van andere hulpprogramma's/SDK's voor het maken van een gegevensfactory. 

## <a name="end-to-end-workflow"></a>End-to-end werkstroom
In dit scenario gebruikt u een aantal tabellen in Azure SQL Database die u gaat kopiëren naar SQL Data Warehouse. Dit is de logische volgorde van de stappen in de werkstroom die in pijplijnen plaatsvindt:

![Werkstroom](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* De eerste pijplijn zoekt de lijst op met tabellen die moeten worden gekopieerd naar de sinkgegevensopslag.  U kunt ook een metagegevenstabel bijhouden waarin alle tabellen worden vermeld die moeten worden gekopieerd naar de sinkgegevensopslag. De pijplijn activeert vervolgens een andere pijplijn, die elke tabel in de database langsloopt en de bewerking uitvoert waarmee de gegevens worden gekopieerd.
* De tweede pijplijn voert de daadwerkelijke kopieerbewerking uit. De lijst met tabellen wordt gebruikt als parameter. Kopieer voor elke tabel in de lijst de specifieke tabel in Azure SQL Database naar de bijbehorende tabel in SQL Data Warehouse met behulp van [gefaseerd kopiëren via Blob storage en PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) (Engelstalig artikel) voor de beste prestaties. In dit voorbeeld wordt de lijst met tabellen in de eerste pijplijn doorgegeven als een waarde voor de parameter. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten
* **Een Azure Storage-account**. Het Azure Storage-account wordt gebruikt als faseringsblobopslag in de bulksgewijze kopieerbewerking. 
* **Azure SQL-database**. Deze database bevat de brongegevens. 
* **Azure SQL Data Warehouse**. Dit datawarehouse bevat de uit de SQL Database gekopieerde gegevens. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>SQL Database en SQL Data Warehouse voorbereiden

**De Azure SQL Database-bron voorbereiden**:

Maak een Azure SQL Database met Adventure Works LT-testgegevens door het artikel [Create an Azure SQL database](../sql-database/sql-database-get-started-portal.md) (Een Azure SQL-database maken) te volgen. In deze zelfstudie worden alle tabellen van deze voorbeelddatabase naar een SQL0datawarehouse gekopieerd.

**De Azure SQL Data Warehouse-sink voorbereiden**:

1. Als u geen Azure SQL Data Warehouse hebt, raadpleegt u het artikel [Create a SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) (Een Azure SQL-database maken) voor de stappen om er een te maken.

1. Maak bijbehorende tabelschema's in SQL Data Warehouse. U gebruikt Azure Data Factory om gegevens in een latere stap te migreren/kopiëren.

## <a name="azure-services-to-access-sql-server"></a>Azure-services voor toegang tot SQL-server

Geef Azure-services toegang tot SQL-server voor zowel SQL Database als SQL Data Warehouse. Zorg ervoor dat de instelling **Toegang tot Azure-services toestaan** voor uw Azure SQL-server op **AAN** staat. Met deze instelling kan de Data Factory-service gegevens lezen uit uw Azure SQL Database en schrijven naar uw Azure SQL Data Warehouse. 

Om deze instelling te controleren en in te scha kelen, gaat u naar uw Azure SQL Server > Security > firewalls en virtuele netwerken > Stel de **optie toegang tot Azure-Services toestaan** **in op**aan.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken
1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
1. Selecteer in het menu aan de linkerkant **een resource maken** > **Analytics** > **Data Factory**: ![Data Factory selectie in het deel venster Nieuw](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Voer op de pagina **nieuw Data Factory** **ADFTutorialBulkCopyDF** in als **naam**. 
 
   De naam van de Azure-gegevensfactory moet **wereldwijd uniek** zijn. Als u het volgende foutbericht krijgt, wijzigt u de naam van de gegevensfactory (bijvoorbeeld uwnaamADFTutorialBulkCopyDF) en probeert u het opnieuw. Zie het artikel [Data factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
1. Selecteer het Azure-**abonnement** waarin u de gegevensfactory wilt maken. 
1. Voer een van de volgende stappen uit voor de **Resourcegroep**:
     
   - Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst. 
   - Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in.   
         
     Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/resource-group-overview.md) voor meer informatie.  
1. Selecteer **V2** als de **versie**.
1. Selecteer de **locatie** voor de gegevensfactory. Voor een lijst met Azure-regio’s waarin Data Factory momenteel beschikbaar is, selecteert u op de volgende pagina de regio’s waarin u geïnteresseerd bent, vouwt u vervolgens **Analytics** uit en gaat u naar **Data Factory**: [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/). De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich in andere regio's bevinden.
1. Klik op **Maken**.
1. Wanneer het maken is voltooid, ziet u de pagina **Data Factory**.
   
1. Klik op **Author & Monitor** om de gebruikersinterface (UI) van Data Factory op een afzonderlijk tabblad te openen.
1. Ga op de pagina **aan de slag** naar het tabblad **Auteur** in het linkerdeel venster, zoals wordt weer gegeven in de volgende afbeelding:  

     ![Pagina Aan de slag](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-linked-services"></a>Gekoppelde services maken
U maakt gekoppelde services om uw gegevensarchieven en compute-services aan een gegevensfactory te koppelen. Een gekoppelde service beschikt over de verbindingsgegevens die de Data Factory-service tijdens runtime gebruikt om een verbinding met het gegevensarchief tot stand te brengen. 

In deze zelfstudie koppelt u uw Azure SQL Database-, Azure SQL Data Warehouse- en Azure Blob Storage-gegevensarchieven aan uw data factory. De Azure SQL-database fungeert als brongegevensarchief. Azure SQL Data Warehouse fungeert als het sink/doelgegevensarchief. Azure Blob Storage is bedoeld voor het vasthouden van de gegevens voordat deze worden geladen in SQL Data Warehouse met behulp van PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Maak de gekoppelde Azure SQL Database-bronservice
In deze stap maakt u een gekoppelde service om uw Azure SQL-database aan de gegevensfactory te koppelen. 

1. Klik op **Connections** onderaan het venster en klik op **+ New** op de werkbalk. 

    ![Knop Nieuwe gekoppelde service](./media/tutorial-bulk-copy-portal/new-linked-service-button.png)
1. In het venster **New Linked Service** selecteert u **Azure SQL Database** en klikt u op **Doorgaan**. 
1. Voer in het venster **nieuwe gekoppelde service (Azure SQL database)** de volgende stappen uit: 

    a. Voer **AzureSqlDatabaseLinkedService** in bij **Name**.
    
    b. Selecteer uw Azure SQL-server bij **Server name**
    
    c. Selecteer uw Azure SQL-database bij **Database name**. 
    
    d. Voer **naam van de gebruiker** in om verbinding te maken met de Azure SQL-database. 
    
    e. Voer het **wachtwoord** voor de gebruiker in. 

    f. Als u de verbinding met de Azure SQL-database wilt testen met de opgegeven informatie, klikt u op **Test connection**.
  
    g. Klik op **Doorgaan**.


### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>De gekoppelde Azure SQL Data Warehouse-sinkservice maken

1. Op het tabblad **Connections** klikt u nogmaals op **+ New** op de werkbalk. 
1. In het venster **New Linked Service** selecteert u **Azure SQL Data Warehouse** en klikt u op **Continue**. 
1. Voer in het venster **nieuwe gekoppelde service (Azure SQL Data Warehouse)** de volgende stappen uit: 
   
    a. Voer **AzureStorageLinkedService** in bij **Name**.
     
    b. Selecteer uw Azure SQL-server bij **Server name**
     
    c. Selecteer uw Azure SQL-database bij **Database name**. 
     
    d. Voer **naam van de gebruiker** in om verbinding te maken met de Azure SQL-database. 
     
    e. Voer het **wachtwoord** voor de gebruiker in. 
     
    f. Als u de verbinding met de Azure SQL-database wilt testen met de opgegeven informatie, klikt u op **Test connection**.
     
    g. Klik op **Doorgaan**.

### <a name="create-the-staging-azure-storage-linked-service"></a>De gekoppelde Azure Storage-faseringsservice maken
In deze zelfstudie gebruikt u Azure Blob-opslag als een tussentijds faseringsgebied voor het inschakelen van PolyBase voor betere kopieerprestaties.

1. Op het tabblad **Connections** klikt u nogmaals op **+ New** op de werkbalk. 
1. In het venster **New Linked Service** selecteert u **Azure Blob Storage** en klikt u op **Continue**. 
1. Voer de volgende stappen uit in het venster **nieuwe gekoppelde service (Azure Blob Storage)** : 

    a. Voer **AzureStorageLinkedService** in als **Naam**.                                                     
    
    b. Selecteer uw **Azure Storage-account** bij **Storage account name**.
    
    c. Klik op **Doorgaan**.


## <a name="create-datasets"></a>Gegevenssets maken
In deze zelfstudie maakt u bron- en sinkgegevenssets, waarmee de locatie wordt opgegeven waar de gegevens zijn opgeslagen. 

De invoergegevensset **AzureSqlDatabaseDataset** verwijst naar de **AzureSqlDatabaseLinkedService**. De gekoppelde service geeft de verbindingsreeks aan om verbinding maken met de database. De gegevensset bevat de naam van de database en de tabel met de brongegevens. 

De uitvoergegevensset **AzureSqlDWDataset** verwijst naar de **AzureSqlDWLinkedService**. De gekoppelde service geeft de verbindingsreeks aan om verbinding maken met het datawarehouse. De gegevensset bevat de database en de tabel waarnaar de gegevens worden gekopieerd. 

In deze zelfstudie zijn de bron- en doel-SQL-tabellen niet vastgelegd in de definities van de gegevensset. In plaats daarvan geeft de ForEach-activiteit de naam van de tabel tijdens runtime door aan de Copy-activiteit. 

### <a name="create-a-dataset-for-source-sql-database"></a>Een gegevensset maken voor bron SQL-Database

1. Klik op **+ (plus)** in het linkerdeel venster en klik vervolgens op **gegevensset**. 

    ![Nieuw gegevenssetmenu](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. Selecteer in het venster **nieuwe gegevensset** de optie **Azure SQL database**en klik vervolgens op **door gaan**. 
    
1. Voer in het venster **set Properties** onder **naam** **AzureSqlDatabaseDataset**in. Onder **gekoppelde service**selecteert u **AzureSqlDatabaseLinkedService**. Klik vervolgens op **Doorgaan**.
1. Ga naar het tabblad **verbinding** en selecteer een tabel voor de **tabel**. Dit is een tijdelijke tabel. U geeft een query voor de brongegevensset op tijdens het maken van een pijplijn. De query wordt gebruikt om gegevens te extraheren uit de Azure SQL-database. U kunt ook het selectievakje **Edit** inschakelen en **dummyName** invoeren als tabelnaam. 
 

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Een gegevensset maken voor de sink in SQL Data Warehouse

1. Klik op **+ (plus)** in het linkervenster en klik op **Dataset**. 
1. Selecteer in het venster **nieuwe gegevensset** de optie **Azure SQL Data Warehouse**en klik vervolgens op **door gaan**.
1. Voer in het venster **set Properties** onder **naam** **AzureSqlDWDataset**in. Onder **gekoppelde service**selecteert u **AzureSqlDatabaseLinkedService**. Klik vervolgens op **Doorgaan**.
1. Ga naar het tabblad **Parameters**, klik op **+ Nieuw** en voer **DWTableName** in als de parameternaam. Als u deze naam van de pagina kopieert/plakt, moet u ervoor zorgen dat er geen **spatie** aan het einde van **DWTableName**.
1. Ga naar het tabblad **Verbinding**, 

    a. Schakel voor **Tabel** de optie **Bewerken**in, klik op het invoervak van de tabelnaam en klik op de onderstaande koppeling **Dynamische inhoud toevoegen**. 

    b. Klik op de pagina **dynamische inhoud toevoegen** op de **DWTAbleName** onder **para meters**, waarmee automatisch het tekstvak met de bovenste expressie wordt gevuld `@dataset().DWTableName`, en klik vervolgens op **volt ooien**. De **tableName**-eigenschap van de gegevensset is ingesteld op de waarde die wordt doorgegeven als argument voor de **DWTableName**-parameter. De ForEach-activiteit doorloopt een lijst met tabellen en geeft deze één voor één door aan de Copy-activiteit. 

    ![Opbouwfunctie voor gegevenssetparameters](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)
 
## <a name="create-pipelines"></a>Pijplijnen maken
In deze zelfstudie gaat u twee pijplijnen maken: **IterateAndCopySQLTables** en **GetTableListAndTriggerCopyData**. 

De **GetTableListAndTriggerCopyData** -pijp lijn voert twee acties uit:

* Zoekt de systeemtabel van Azure SQL Database op om de lijst met te kopiëren tabellen op te halen.
* Activeert de pijplijn **IterateAndCopySQLTables** om het kopiëren van de gegevens daadwerkelijk uit te voeren.

De **IterateAndCopySQLTables** -pijp lijn gebruikt een lijst met tabellen als een para meter. Voor elke tabel in de lijst worden gegevens uit de tabel in Azure SQL Database naar Azure SQL Data Warehouse gekopieerd met behulp van gefaseerd kopiëren en PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>De pijplijn IterateAndCopySQLTables maken

1. Klik op **+ (plus)** in het linkervenster en klik op **Pipeline**.

    ![Menu Nieuwe pijplijn](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. Geef op het tabblad **Algemeen** **IterateAndCopySQLTables** op als naam. 

1. Open het tabblad **Parameters** en voer de volgende stappen uit: 

    a. Klik op **+ New**. 
    
    b. Voer **tableList** in als **Name**-parameter.
    
    c. Selecteer **Matrix** bij **Type**.

1. Vouw in de werkset **Activities** de optie **Iteration & Conditions** uit en sleep de **ForEach**-activiteit naar het ontwerpoppervlak voor pijplijnen. U kunt ook zoeken naar activiteiten in de werkset **Activiteiten**. 

    a. Voer onderaan het tabblad **Algemeen** **IterateSQLTables** bij **Naam** in. 

    b. Ga naar het tabblad **instellingen** , klik op het invoervak voor **items**en klik vervolgens op de koppeling **dynamische inhoud toevoegen** hieronder. 

    c. Op de pagina **dynamische inhoud toevoegen** vouwt u de secties **systeem variabelen** en **functies** uit, klikt u op de **tableList** onder **para meters**, waarmee het tekstvak met de bovenste expressie automatisch wordt gevuld als `@pipeline().parameter.tableList`. Klik vervolgens op **Voltooien**. 

    ![Opbouwfunctie voor Foreach-parameters](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Ga naar het tabblad **Activiteiten** en klik op **Activiteit toevoegen** om een onderliggende activiteit toe te voegen aan de activiteit **ForEach**.

1. Vouw in de werkset **activiteiten** de optie **& overdracht verplaatsen**uit en sleep **gegevens kopiëren** activiteit naar het ontwerp oppervlak voor pijp lijnen. Let op het breadcrumb-menu bovenaan. De **IterateAndCopySQLTable** is de naam van de pijp lijn en **IterateSQLTables** is de naam van de foreach-activiteit. Voor de ontwerpfunctie is het activiteitbereik actief. Als u wilt terugkeren naar de pijplijn editor vanuit de ForEach-editor, kunt u klikken op de koppeling in het breadcrumb-menu. 

    ![Kopiëren in ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Open het tabblad **Source** en voer de volgende stappen uit:

    1. Selecteer **AzureSqlDatabaseDataset** bij **Source Dataset**. 
    1. Selecteer **query** optie voor **query gebruiken**. 
    1. Klik op het invoervak **Query** -> Selecteer de koppeling **Dynamische inhoud toevoegen** hieronder -> voer de volgende expressie in voor **Query** -> selecteer **Voltooien**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 


1. Open het tabblad **Sink** en voer de volgende stappen uit: 

    1. Selecteer **AzureSqlDWDataset** bij **Sink Dataset**.
    1. Klik op het invoervak voor de waarde van de para meter DWTableName-> Selecteer de onderstaande **dynamische inhoud toevoegen** , voer `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` expressie in als script,-> Selecteer **volt ooien**.
    1. Schakel het selectie vakje voor **poly base toestaan**in. 
    1. Schakel de optie **Use Type default** uit. 
    1. Klik op het invoervak **Prekopieerscript** -> Selecteer de koppeling **Dynamische inhoud toevoegen** hieronder -> voer de volgende expressie als script in -> selecteer **Voltooien**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Sink-instellingen kopiëren](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
1. Ga naar het tabblad **Instellingen** en voer de volgende stappen uit: 

    1. Schakel het selectie vakje in om **fase ring in te scha kelen**.
    1. Selecteer **AzureStorageLinkedService** bij **Store Account Linked Service**.

1. Klik in de bovenste pijplijnwerkbalk op **Valideren** om de instellingen voor de pijplijn te valideren. Controleer of er geen validatie fout is. Sluit het venster **Pipeline Validation Report** door op **>>** te klikken.

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>De pijplijn GetTableListAndTriggerCopyData maken

Deze pijp lijn voert twee acties uit:

* Zoekt de systeemtabel van Azure SQL Database op om de lijst met te kopiëren tabellen op te halen.
* Activeert de pijplijn 'IterateAndCopySQLTables' om het kopiëren van de gegevens daadwerkelijk uit te voeren.

1. Klik op **+ (plus)** in het linkervenster en klik op **Pipeline**.
1. Wijzig op het tabblad **Algemeen** de naam van de pijp lijn in **GetTableListAndTriggerCopyData**. 

1. Vouw in de werkset **activities** de optie **Algemeen**uit en sleep de **opzoek** activiteit naar het ontwerp oppervlak voor pijp lijnen en voer de volgende stappen uit:

    1. Voer **LookupTableList** in als **Name**. 
    1. Voer **De tabellijst ophalen uit de Azure SQL-database** in bij **Description**.

1. Ga naar het tabblad **Instellingen** en voer de volgende stappen uit:

    1. Selecteer **AzureSqlDatabaseDataset** bij **Source Dataset**. 
    1. Selecteer **Query** bij **Use Query**. 
    1. Voer bij **Query** de volgende SQL-query in.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Schakel het selectievakje voor het veld **First row only** uit.

        ![Lookup-activiteit - pagina instellingen](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Sleep de activiteit **pijp lijn uitvoeren** vanuit de werkset activiteiten naar het ontwerp oppervlak voor pijp lijnen en stel de naam in op **TriggerCopy**.

1. Ga naar het tabblad **Instellingen** en voer de volgende stappen uit: 

    1. Selecteer **IterateAndCopySQLTables** bij **Invoked pipeline**. 
    1. Vouw de sectie **Advanced** uit. 
    1. Klik op **+ New** in de sectie **Parameters**. 
    1. Voer **tableList** in als **Name**-parameter.
    1. Klik op het invoervak VALUE -> selecteer de koppeling **Dynamische inhoud toevoegen** hieronder - voer `@activity('LookupTableList').output.value` als waarde voor de tabelnaam -> selecteer **Voltooien**. U stelt de resultaten lijst van de opzoek activiteit in als een invoer voor de tweede pijp lijn. De lijst met resultaten bevat de lijst met tabellen waarvan de gegevens naar de bestemming moeten worden gekopieerd. 

        ![Execute Pipeline-activiteit - pagina instellingen](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)
1. Als u de **opzoek** activiteit wilt **verbinden** met de activiteit voor het uitvoeren van de **pijp lijn** , sleept u het **groene vak** dat is gekoppeld aan de opzoek activiteit links van de activiteit pijp lijn uitvoeren.

    ![Lookup- en Execute Pipeline-activiteiten verbinden](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)
1. Valideer de pijplijn door te klikken op de knop **Validate** op de werkbalk. Controleer of er geen validatiefouten zijn. Sluit het venster **Pipeline Validation Report** door op **>>** te klikken.

1. Als u entiteiten (gegevenssets, pijplijnen, enzovoort) wilt publiceren naar de Data Factory-service, klikt u boven aan het venster op **Alles publiceren**. Wacht totdat de publicatie is uitgevoerd. 

## <a name="trigger-a-pipeline-run"></a>Een pijplijnuitvoering activeren

Ga naar pijplijn **GetTableListAndTriggerCopyData**, klik op **trigger toevoegen**en klik vervolgens op **nu activeren**. 

## <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

1. Schakel over naar het tabblad **monitor** . Klik op **vernieuwen** totdat u voor zowel de pijp lijnen in uw oplossing uitvoeringen ziet. Blijf de lijst vernieuwen totdat de status **Succeeded** wordt weergegeven. 

1. Klik op de eerste koppeling in de actie koppeling voor die pijp lijn om de uitvoering van activiteiten weer te geven die zijn gekoppeld aan de **GetTableListAndTriggerCopyData** -pijp lijn. Er moeten twee uitvoeringen van activiteit voor deze pijplijnuitvoering te zien zijn. 

1. Als u de uitvoer van de **Lookup**-activiteit wilt weergeven, klikt u op de koppeling in de kolom **Output** voor die activiteit. U kunt het venster **Output** maximaliseren en herstellen. Nadat u de uitvoer hebt bekeken, klikt u op **X** om het venster **Output** te sluiten.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
1. Als u wilt terugkeren naar de weergave **Pipeline Runs**, klikt u op de koppeling **Pipelines** bovenaan. Klik op **View Activity Runs** (de eerste koppeling in de kolom **Actions**) voor de pijplijn **IterateAndCopySQLTables**. U ziet dat er één **Kopieer** activiteit wordt uitgevoerd voor elke tabel in de uitvoer van de **opzoek** activiteit. 

1. Bevestig dat de gegevens zijn gekopieerd naar het beoogde SQL Data Warehouse dat u in deze zelfstudie hebt gebruikt. 

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u de volgende stappen uitgevoerd: 

> [!div class="checklist"]
> * Een gegevensfactory maakt.
> * Gekoppelde Azure SQL Database-, Azure SQL Data Warehouse- en Azure Storage-services maken.
> * Gegevenssets voor Azure SQL Database en Azure SQL Data Warehouse maken.
> * Een pijplijn maken om de te kopiëren tabellen op te zoeken en een andere pijplijn om de kopieerbewerking daadwerkelijk uit te voeren. 
> * Een pijplijnuitvoering starten.
> * De uitvoering van de pijplijn en van de activiteit controleren.

Ga door naar de volgende zelfstudie voor informatie over het incrementeel kopiëren van gegevens uit een bron naar een bestemming:
> [!div class="nextstepaction"]
>[Gegevens incrementeel kopiëren](tutorial-incremental-copy-portal.md)
