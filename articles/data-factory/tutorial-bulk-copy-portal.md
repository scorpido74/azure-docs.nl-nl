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
ms.date: 02/27/2020
ms.openlocfilehash: 04469fa1bd0473710d9fa0bf0190c6459f1f8a07
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81418776"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Meerdere tabellen bulksgewijs kopiëren met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In deze zelf studie wordt gedemonstreerd hoe **u een aantal tabellen kopieert van Azure SQL database naar Azure Synapse Analytics (voorheen SQL DW)**. U kunt hetzelfde patroon toepassen in andere kopieerscenario's. Bijvoorbeeld: tabellen kopiëren van SQL Server/Oracle naar Azure SQL Database/Azure Synapse Analytics (voorheen SQL DW)/Azure Blob, waarbij verschillende paden van de BLOB naar Azure SQL Database tabellen worden gekopieerd.

> [!NOTE]
> - Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

Op hoog niveau bevat deze zelfstudie de volgende stappen:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Maak Azure SQL Database, Azure Synapse Analytics (voorheen SQL DW) en Azure Storage gekoppelde services.
> * Azure SQL Database-en Azure Synapse Analytics-gegevens sets (voorheen SQL DW) maken.
> * Een pijplijn maken om de te kopiëren tabellen op te zoeken en een andere pijplijn om de kopieerbewerking daadwerkelijk uit te voeren. 
> * Een pijplijnuitvoering starten.
> * De uitvoering van de pijplijn en van de activiteit controleren

In deze zelfstudie wordt Azure Portal gebruikt. Zie [Quickstarts](quickstart-create-data-factory-dot-net.md) (Snelstartgidsen) voor meer informatie over het gebruik van andere hulpprogramma's/SDK's voor het maken van een gegevensfactory. 

## <a name="end-to-end-workflow"></a>End-to-end werkstroom
In dit scenario hebt u een aantal tabellen in Azure SQL Database die u wilt kopiëren naar Azure Synapse Analytics (voorheen SQL DW). Dit is de logische volgorde van de stappen in de werkstroom die in pijplijnen plaatsvindt:

![Werkstroom](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* De eerste pijplijn zoekt de lijst op met tabellen die moeten worden gekopieerd naar de sinkgegevensopslag.  U kunt ook een metagegevenstabel bijhouden waarin alle tabellen worden vermeld die moeten worden gekopieerd naar de sinkgegevensopslag. De pijplijn activeert vervolgens een andere pijplijn, die elke tabel in de database langsloopt en de bewerking uitvoert waarmee de gegevens worden gekopieerd.
* De tweede pijplijn voert de daadwerkelijke kopieerbewerking uit. De lijst met tabellen wordt gebruikt als parameter. Voor elke tabel in de lijst kopieert u de specifieke tabel in Azure SQL Database naar de overeenkomstige tabel in azure Synapse Analytics (voorheen SQL DW) met behulp van [gefaseerde kopie via Blob Storage en poly base](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) voor de beste prestaties. In dit voorbeeld wordt de lijst met tabellen in de eerste pijplijn doorgegeven als een waarde voor de parameter. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten
* **Azure Storage-account**. Het Azure Storage-account wordt gebruikt als faseringsblobopslag in de bulksgewijze kopieerbewerking. 
* **Azure SQL database**. Deze database bevat de brongegevens. 
* **Azure Synapse Analytics (voorheen SQL DW)**. Dit datawarehouse bevat de uit de SQL Database gekopieerde gegevens. 

### <a name="prepare-sql-database-and-azure-synapse-analytics-formerly-sql-dw"></a>SQL Database en Azure Synapse Analytics voorbereiden (voorheen SQL DW)

**De Azure SQL Database-bron voorbereiden**:

Maak een Azure SQL Database met Adventure Works LT-testgegevens door het artikel [Create an Azure SQL database](../sql-database/sql-database-get-started-portal.md) (Een Azure SQL-database maken) te volgen. In deze zelf studie worden alle tabellen uit deze voorbeeld database naar een Azure Synapse-analyse (voorheen SQL DW) gekopieerd.

**De Sink voorbereiden Azure Synapse Analytics (voorheen SQL DW)**:

1. Als u geen Azure Synapse Analytics (voorheen SQL DW) hebt, raadpleegt u het artikel [een SQL Data Warehouse maken](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) om er een te maken.

1. U maakt corresponderende tabel schema's in azure Synapse Analytics (voorheen SQL DW). U gebruikt Azure Data Factory om gegevens in een latere stap te migreren/kopiëren.

## <a name="azure-services-to-access-sql-server"></a>Azure-services voor toegang tot SQL-server

Voor zowel SQL Database als Azure Synapse Analytics (voorheen SQL DW) verleent Azure-Services toegang tot SQL Server. Zorg ervoor dat **Azure-Services en-bronnen toegang hebben tot deze server** instelling **is ingeschakeld voor** uw Azure SQL-Server. Met deze instelling kan de Data Factory-service gegevens uit uw Azure SQL Database lezen en gegevens schrijven naar uw Azure Synapse Analytics (voorheen SQL DW). 

Om deze instelling te controleren en in te scha kelen, gaat u naar uw Azure SQL Server > Security > firewalls en virtuele netwerken > Stel de **Azure-Services en-resources in staat om toegang te krijgen tot deze server** in **op**aan.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken
1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
1. Ga naar de [Azure Portal](https://portal.azure.com). 
1. Selecteer aan de linkerkant van het menu Azure Portal **een resource** > **Analytics** > -**Data Factory**maken. 
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Voer op de pagina **nieuw Data Factory** **ADFTutorialBulkCopyDF** in als **naam**. 
 
   De naam van de Azure-data factory moet **wereld wijd uniek**zijn. Als u het volgende foutbericht krijgt, wijzigt u de naam van de gegevensfactory (bijvoorbeeld uwnaamADFTutorialBulkCopyDF) en probeert u het opnieuw. Zie het artikel [Data factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
  
       `Data factory name "ADFTutorialBulkCopyDF" is not available`
1. Selecteer het Azure-**abonnement** waarin u de gegevensfactory wilt maken. 
1. Voer een van de volgende stappen uit voor de **Resourcegroep**:
     
   - Selecteer **bestaande gebruiken**en selecteer een bestaande resource groep in de vervolg keuzelijst. 
   - Selecteer **nieuwe maken**en voer de naam van een resource groep in.   
         
     Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
1. Selecteer **V2** als de **versie**.
1. Selecteer de **locatie** voor de gegevensfactory. Voor een lijst met Azure-regio’s waarin Data Factory momenteel beschikbaar is, selecteert u op de volgende pagina de regio’s waarin u geïnteresseerd bent, vouwt u vervolgens **Analytics** uit en gaat u naar **Data Factory**: [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/). De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich in andere regio's bevinden.
1. Klik op **maken**.
1. Nadat het maken is voltooid, selecteert u **naar resource gaan** om naar de pagina **Data Factory** te gaan. 
   
1. Klik op **Author & Monitor** om de gebruikersinterface (UI) van Data Factory op een afzonderlijk tabblad te openen.
1. Ga op de pagina **aan de slag** naar het tabblad **Auteur** in het linkerdeel venster, zoals wordt weer gegeven in de volgende afbeelding:

     ![Pagina Aan de slag](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-linked-services"></a>Gekoppelde services maken
U maakt gekoppelde services om uw gegevensarchieven en compute-services aan een gegevensfactory te koppelen. Een gekoppelde service beschikt over de verbindingsgegevens die de Data Factory-service tijdens runtime gebruikt om een verbinding met het gegevensarchief tot stand te brengen. 

In deze zelf studie koppelt u uw Azure SQL Database, Azure Synapse Analytics (voorheen SQL DW) en Azure Blob Storage gegevens archieven aan uw data factory. De Azure SQL-database fungeert als brongegevensarchief. De Azure Synapse Analytics (voorheen SQL DW) is het sink/Destination-gegevens archief. De Azure-Blob Storage is om de gegevens te faseren voordat de gegevens in azure Synapse Analytics (voorheen SQL DW) worden geladen met poly base. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Maak de gekoppelde Azure SQL Database-bronservice
In deze stap maakt u een gekoppelde service om uw Azure SQL-database aan de gegevensfactory te koppelen. 

1. Klik op **verbindingen** aan de onderkant van het venster en klik op **+ Nieuw** op de werk balk (de knop**verbindingen** bevindt zich onderaan de linkerkolom onder **Factory-resources**). 

1. In het venster **New Linked Service** selecteert u **Azure SQL Database** en klikt u op **Doorgaan**. 
1. Voer in het venster **nieuwe gekoppelde service (Azure SQL database)** de volgende stappen uit: 

    a. Voer **AzureSqlDatabaseLinkedService** in bij **Name**.
    
    b. Selecteer uw Azure SQL-server bij **Server name**
    
    c. Selecteer uw Azure SQL-database bij **Database name**. 
    
    d. Voer **naam van de gebruiker** in om verbinding te maken met de Azure SQL-database. 
    
    e. Voer het **wachtwoord** voor de gebruiker in. 

    f. Als u de verbinding met de Azure SQL-database wilt testen met de opgegeven informatie, klikt u op **Test connection**.
  
    g. Klik op **maken** om de gekoppelde service op te slaan.


### <a name="create-the-sink-azure-synapse-analytics-formerly-sql-dw-linked-service"></a>De Sink Azure Synapse Analytics (voorheen SQL DW) gekoppelde service maken

1. Op het tabblad **Connections** klikt u nogmaals op **+ New** op de werkbalk. 
1. Selecteer in het venster **nieuwe gekoppelde service** de optie **Azure Synapse Analytics (voorheen SQL DW)** en klik op **door gaan**. 
1. Voer in het venster **nieuwe gekoppelde service (Azure Synapse Analytics (voorheen SQL DW))** de volgende stappen uit: 
   
    a. Voer **AzureStorageLinkedService** in bij **Name**.
     
    b. Selecteer uw Azure SQL-server bij **Server name**
     
    c. Selecteer uw Azure SQL-database bij **Database name**. 
     
    d. Voer de **gebruikers naam** in om verbinding te maken met Azure SQL database. 
     
    e. Voer het **wacht woord** voor de gebruiker in. 
     
    f. Als u de verbinding met de Azure SQL-database wilt testen met de opgegeven informatie, klikt u op **Test connection**.
     
    g. Klik op **maken**.

### <a name="create-the-staging-azure-storage-linked-service"></a>De gekoppelde Azure Storage-faseringsservice maken
In deze zelfstudie gebruikt u Azure Blob-opslag als een tussentijds faseringsgebied voor het inschakelen van PolyBase voor betere kopieerprestaties.

1. Op het tabblad **Connections** klikt u nogmaals op **+ New** op de werkbalk. 
1. In het venster **New Linked Service** selecteert u **Azure Blob Storage** en klikt u op **Continue**. 
1. Voer de volgende stappen uit in het venster **nieuwe gekoppelde service (Azure Blob Storage)** : 

    a. Voer **AzureStorageLinkedService** in bij **Name**.                                                 
    b. Selecteer uw **Azure Storage-account** voor de naam van het **opslag account**.
    
    c. Klik op **maken**.


## <a name="create-datasets"></a>Gegevenssets maken
In deze zelfstudie maakt u bron- en sinkgegevenssets, waarmee de locatie wordt opgegeven waar de gegevens zijn opgeslagen. 

De invoergegevensset **AzureSqlDatabaseDataset** verwijst naar de **AzureSqlDatabaseLinkedService**. De gekoppelde service geeft de verbindingsreeks aan om verbinding maken met de database. De gegevensset bevat de naam van de database en de tabel met de brongegevens. 

De uitvoergegevensset **AzureSqlDWDataset** verwijst naar de **AzureSqlDWLinkedService**. De gekoppelde service specificeert de connection string om verbinding te maken met de Azure Synapse Analytics (voorheen SQL DW). De gegevensset bevat de database en de tabel waarnaar de gegevens worden gekopieerd. 

In deze zelfstudie zijn de bron- en doel-SQL-tabellen niet vastgelegd in de definities van de gegevensset. In plaats daarvan geeft de ForEach-activiteit de naam van de tabel tijdens runtime door aan de Copy-activiteit. 

### <a name="create-a-dataset-for-source-sql-database"></a>Een gegevensset maken voor bron SQL-Database

1. Klik op **+ (plus)** in het linkerdeel venster en klik vervolgens op **gegevensset**. 

    ![Nieuw gegevenssetmenu](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. Selecteer in het venster **nieuwe gegevensset** de optie **Azure SQL database**en klik vervolgens op **door gaan**. 
    
1. Voer in het venster **set Properties** onder **naam** **AzureSqlDatabaseDataset**in. Onder **gekoppelde service**selecteert u **AzureSqlDatabaseLinkedService**. Klik vervolgens op **OK**.

1. Ga naar het tabblad **verbinding** en selecteer een tabel voor de **tabel**. Dit is een tijdelijke tabel. U geeft een query voor de brongegevensset op tijdens het maken van een pijplijn. De query wordt gebruikt om gegevens te extraheren uit de Azure SQL-database. U kunt ook op het selectie vakje **bewerken** klikken en **dbo. dummy** als tabel naam invoeren. 
 

### <a name="create-a-dataset-for-sink-azure-synapse-analytics-formerly-sql-dw"></a>Een gegevensset maken voor de Sink Azure Synapse Analytics (voorheen SQL DW)

1. Klik op **+ (plus)** in het linkervenster en klik op **Dataset**. 
1. Selecteer in het venster **nieuwe gegevensset** de optie **Azure Synapse Analytics (voorheen SQL DW)** en klik vervolgens op **door gaan**.
1. Voer in het venster **set Properties** onder **naam** **AzureSqlDWDataset**in. Onder **gekoppelde service**selecteert u **AzureSqlDWLinkedService**. Klik vervolgens op **OK**.
1. Ga naar het tabblad **Parameters**, klik op **+ Nieuw** en voer **DWTableName** in als de parameternaam. Als u deze naam van de pagina kopieert/plakt, moet u ervoor zorgen dat er geen **spatie** aan het einde van **DWTableName**.
1. Ga naar het tabblad **Verbinding**, 

    a. Controleer bij **tabel**de optie **bewerken** . Voer **dbo** in het invoervak eerste tabel naam in. En selecteer vervolgens in het tweede invoervak en klik hieronder op de koppeling **dynamische inhoud toevoegen** . 

    ![Naam gegevensset-verbinding](./media/tutorial-bulk-copy-portal/dataset-connection-tablename.png)

    b. Klik op de pagina **dynamische inhoud toevoegen** op de **DWTAbleName** onder **para meters**, waarmee automatisch het tekstvak met de bovenste `@dataset().DWTableName`expressie wordt ingevuld en klik vervolgens op **volt ooien**. De **tableName**-eigenschap van de gegevensset is ingesteld op de waarde die wordt doorgegeven als argument voor de **DWTableName**-parameter. De ForEach-activiteit doorloopt een lijst met tabellen en geeft deze één voor één door aan de Copy-activiteit. 

    ![Opbouwfunctie voor gegevenssetparameters](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)
 
## <a name="create-pipelines"></a>Pijplijnen maken
In deze zelfstudie gaat u twee pijplijnen maken: **IterateAndCopySQLTables** en **GetTableListAndTriggerCopyData**. 

De **GetTableListAndTriggerCopyData** -pijp lijn voert twee acties uit:

* Zoekt de systeemtabel van Azure SQL Database op om de lijst met te kopiëren tabellen op te halen.
* Activeert de pijp lijn **IterateAndCopySQLTables** om de daad werkelijke gegevens kopie uit te voeren.

De **IterateAndCopySQLTables** -pijp lijn gebruikt een lijst met tabellen als een para meter. Voor elke tabel in de lijst worden gegevens uit de tabel in Azure SQL Database gekopieerd naar Azure Synapse Analytics (voorheen SQL DW) met behulp van gefaseerde kopie en poly base.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>De pijplijn IterateAndCopySQLTables maken

1. Klik op **+ (plus)** in het linkervenster en klik op **Pipeline**.

    ![Menu Nieuwe pijplijn](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. Geef op het tabblad **Algemeen****IterateAndCopySQLTables** op als naam. 

1. Open het tabblad **Parameters** en voer de volgende stappen uit: 

    a. Klik op **+ New**. 
    
    b. Voer **tableList** in als parameter **naam**.
    
    c. Selecteer **Matrix** bij **Type**.

1. Vouw in de werkset **Activities** de optie **Iteration & Conditions** uit en sleep de **ForEach**-activiteit naar het ontwerpoppervlak voor pijplijnen. U kunt ook zoeken naar activiteiten in de werkset **Activiteiten**. 

    a. Voer onderaan het tabblad **Algemeen****IterateSQLTables** bij **Naam** in. 

    b. Ga naar het tabblad **instellingen** , klik op het invoervak voor **items**en klik vervolgens op de koppeling **dynamische inhoud toevoegen** hieronder. 

    c. Op de pagina **dynamische inhoud toevoegen** vouwt u de secties **systeem variabelen** en **functies** uit, klikt u op de **tableList** onder **para meters**, waarmee het tekstvak met `@pipeline().parameter.tableList`de bovenste expressie automatisch wordt gevuld als. Klik vervolgens op **Voltooien**. 

    ![Opbouwfunctie voor Foreach-parameters](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Ga naar het tabblad **activiteiten** en klik op het **Potlood pictogram** om een onderliggende activiteit toe te voegen aan de **foreach** -activiteit.
    ![Opbouw functie voor foreach-activiteit](./media/tutorial-bulk-copy-portal/for-each-activity-builder.png)

1. Vouw in de werkset **activiteiten** **verplaatsen & overdracht**uit en sleep de activiteit **gegevens kopiëren** naar het ontwerp oppervlak voor pijp lijnen. Let op het breadcrumb-menu bovenaan. De **IterateAndCopySQLTable** is de naam van de pijp lijn en **IterateSQLTables** is de naam van de foreach-activiteit. Voor de ontwerpfunctie is het activiteitbereik actief. Als u wilt terugkeren naar de pijplijn editor vanuit de ForEach-editor, kunt u klikken op de koppeling in het breadcrumb-menu. 

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
    1. Klik op het invoervak voor de waarde van de para meter DWTableName-> Selecteer de onderstaande **dynamische inhoud toevoegen** , Voer `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` de expressie in als script,-> Selecteer **volt ooien**.
    1. Selecteer bij Kopieer methode **poly base**. 
    1. Schakel de optie **type standaard gebruiken** uit. 
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
    1. Selecteer **query** om **query te gebruiken**. 
    1. Voer bij **Query** de volgende SQL-query in.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Schakel het selectievakje voor het veld **First row only** uit.

        ![Lookup-activiteit - pagina instellingen](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Sleep de activiteit **pijp lijn uitvoeren** vanuit de werkset activiteiten naar het ontwerp oppervlak voor pijp lijnen en stel de naam in op **TriggerCopy**.

1. Als u de **opzoek** activiteit wilt **verbinden** met de activiteit voor het uitvoeren van de **pijp lijn** , sleept u het **groene vak** dat is gekoppeld aan de opzoek activiteit links van de activiteit pijp lijn uitvoeren.

    ![Lookup- en Execute Pipeline-activiteiten verbinden](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)

1. Ga naar het tabblad **instellingen** van de activiteit voor het uitvoeren van de **pijp lijn** en voer de volgende stappen uit: 

    1. Selecteer **IterateAndCopySQLTables** bij **Invoked pipeline**. 
    1. Vouw de sectie **Geavanceerd** uit en schakel het selectie vakje voor **wachten op voltooiing**uit.
    1. Klik op **+ New** in de sectie **Parameters**. 
    1. Voer **tableList** in als parameter **naam**.
    1. Klik op het invoervak VALUE -> selecteer de koppeling **Dynamische inhoud toevoegen** hieronder - voer `@activity('LookupTableList').output.value` als waarde voor de tabelnaam -> selecteer **Voltooien**. U stelt de resultaten lijst van de opzoek activiteit in als een invoer voor de tweede pijp lijn. De lijst met resultaten bevat de lijst met tabellen waarvan de gegevens naar de bestemming moeten worden gekopieerd. 

        ![Execute Pipeline-activiteit - pagina instellingen](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)

1. Valideer de pijplijn door te klikken op de knop **Validate** op de werkbalk. Controleer of er geen validatiefouten zijn. Sluit het venster **Pipeline Validation Report** door op **>>** te klikken.

1. Als u entiteiten (gegevens sets, pijp lijnen, enzovoort) wilt publiceren naar de Data Factory-Service, klikt u op **Alles publiceren** boven op het venster. Wacht totdat de publicatie is uitgevoerd. 

## <a name="trigger-a-pipeline-run"></a>Een pijplijnuitvoering activeren

1. Ga naar pijplijn **GetTableListAndTriggerCopyData**, klik op **trigger toevoegen** op de werk balk van de bovenste pijp lijn en klik vervolgens op **nu activeren**. 

1. Bevestig de uitvoering op de **pipeline-uitvoer** pagina en selecteer vervolgens **volt ooien**.

## <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

1. Schakel over naar het tabblad **monitor** . Klik op **vernieuwen** totdat u voor zowel de pijp lijnen in uw oplossing uitvoeringen ziet. Blijf de lijst vernieuwen totdat de status **Succeeded** wordt weergegeven. 

1. Als u de uitvoeringen van activiteiten die zijn gekoppeld aan de **GetTableListAndTriggerCopyData** -pijp lijn wilt weer geven, klikt u op de pijplijn naam koppeling voor de pijp lijn. Er moeten twee uitvoeringen van activiteit voor deze pijplijnuitvoering te zien zijn. 
    ![Pijplijn uitvoering bewaken](./media/tutorial-bulk-copy-portal/monitor-pipeline.png)
1. Als u de uitvoer van de **opzoek** activiteit wilt weer geven, klikt u op de **uitvoer** koppeling naast de activiteit in de kolom **activiteit naam** . U kunt het venster **Output** maximaliseren en herstellen. Nadat u de uitvoer hebt bekeken, klikt u op **X** om het venster **Output** te sluiten.

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
1. Als u wilt terugkeren naar de weer gave **pijplijn uitvoeringen** , klikt u op de koppeling **alle pijplijn uitvoeringen** boven aan het breadcrumb-menu. Klik op de koppeling **IterateAndCopySQLTables** (onder **pijplijn naam** kolom) om de uitvoering van de activiteit van de pijp lijn weer te geven. U ziet dat er één **Kopieer** activiteit wordt uitgevoerd voor elke tabel in de uitvoer van de **opzoek** activiteit. 

1. Controleer of de gegevens zijn gekopieerd naar het doel-Azure Synapse Analytics (voorheen SQL DW) die u in deze zelf studie hebt gebruikt. 

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u de volgende stappen uitgevoerd: 

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Maak Azure SQL Database, Azure Synapse Analytics (voorheen SQL DW) en Azure Storage gekoppelde services.
> * Azure SQL Database-en Azure Synapse Analytics-gegevens sets (voorheen SQL DW) maken.
> * Een pijplijn maken om de te kopiëren tabellen op te zoeken en een andere pijplijn om de kopieerbewerking daadwerkelijk uit te voeren. 
> * Een pijplijnuitvoering starten.
> * De uitvoering van de pijplijn en van de activiteit controleren

Ga door naar de volgende zelfstudie voor informatie over het incrementeel kopiëren van gegevens uit een bron naar een bestemming:
> [!div class="nextstepaction"]
>[Gegevens incrementeel kopiëren](tutorial-incremental-copy-portal.md)
