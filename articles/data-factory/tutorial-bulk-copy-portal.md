---
title: Gegevens in bulk kopiëren met Azure-portal
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418776"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Meerdere tabellen bulksgewijs kopiëren met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In deze zelfstudie wordt het **kopiëren van een aantal tabellen van Azure SQL Database naar Azure Synapse Analytics (voorheen SQL DW)** weergegeven. U kunt hetzelfde patroon toepassen in andere kopieerscenario's. Bijvoorbeeld tabellen kopiëren van SQL Server/Oracle naar Azure SQL Database/Azure Synapse Analytics (voorheen SQL DW)/Azure Blob, waarbij verschillende paden worden gekopieerd van Blob naar Azure SQL Database-tabellen.

> [!NOTE]
> - Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

Op hoog niveau bevat deze zelfstudie de volgende stappen:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Azure SQL Database, Azure Synapse Analytics (voorheen SQL DW) en gekoppelde Azure Storage-services maken.
> * Azure SQL Database en Azure Synapse Analytics (voorheen SQL DW)-gegevenssets maken.
> * Een pijplijn maken om de te kopiëren tabellen op te zoeken en een andere pijplijn om de kopieerbewerking daadwerkelijk uit te voeren. 
> * Een pijplijnuitvoering starten.
> * De uitvoering van de pijplijn en van de activiteit controleren

In deze zelfstudie wordt Azure Portal gebruikt. Zie [Quickstarts](quickstart-create-data-factory-dot-net.md) (Snelstartgidsen) voor meer informatie over het gebruik van andere hulpprogramma's/SDK's voor het maken van een gegevensfactory. 

## <a name="end-to-end-workflow"></a>End-to-end werkstroom
In dit scenario hebt u een aantal tabellen in Azure SQL Database die u wilt kopiëren naar Azure Synapse Analytics (voorheen SQL DW). Dit is de logische volgorde van de stappen in de werkstroom die in pijplijnen plaatsvindt:

![Werkstroom](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* De eerste pijplijn zoekt de lijst op met tabellen die moeten worden gekopieerd naar de sinkgegevensopslag.  U kunt ook een metagegevenstabel bijhouden waarin alle tabellen worden vermeld die moeten worden gekopieerd naar de sinkgegevensopslag. De pijplijn activeert vervolgens een andere pijplijn, die elke tabel in de database langsloopt en de bewerking uitvoert waarmee de gegevens worden gekopieerd.
* De tweede pijplijn voert de daadwerkelijke kopieerbewerking uit. De lijst met tabellen wordt gebruikt als parameter. Kopieer voor elke tabel in de lijst de specifieke tabel in Azure SQL Database naar de bijbehorende tabel in Azure Synapse Analytics (voorheen SQL DW) met [gefaseerde kopie via Blob-opslag en PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) voor de beste prestaties. In dit voorbeeld wordt de lijst met tabellen in de eerste pijplijn doorgegeven als een waarde voor de parameter. 

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten
* **Azure Storage-account**. Het Azure Storage-account wordt gebruikt als faseringsblobopslag in de bulksgewijze kopieerbewerking. 
* **Azure SQL-database**. Deze database bevat de brongegevens. 
* **Azure Synapse Analytics (voorheen SQL DW)**. Dit datawarehouse bevat de uit de SQL Database gekopieerde gegevens. 

### <a name="prepare-sql-database-and-azure-synapse-analytics-formerly-sql-dw"></a>SQL-database en Azure Synapse Analytics (voorheen SQL DW) voorbereiden

**De Azure SQL Database-bron voorbereiden**:

Maak een Azure SQL Database met Adventure Works LT-testgegevens door het artikel [Create an Azure SQL database](../sql-database/sql-database-get-started-portal.md) (Een Azure SQL-database maken) te volgen. Met deze zelfstudie worden alle tabellen uit deze voorbeelddatabase overgezet naar een Azure Synapse Analytics (voorheen SQL DW).

**Bereid de gootsteen Azure Synapse Analytics (voorheen SQL DW)** voor:

1. Als u geen Azure Synapse Analytics (voorheen SQL DW) hebt, [raadpleegt](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) u het artikel Een SQL Data Warehouse maken voor stappen om er een te maken.

1. Maak overeenkomstige tabelschema's in Azure Synapse Analytics (voorheen SQL DW). U gebruikt Azure Data Factory om gegevens in een latere stap te migreren/kopiëren.

## <a name="azure-services-to-access-sql-server"></a>Azure-services voor toegang tot SQL-server

Sta Azure-services toe om toegang te krijgen tot SQL-server voor zowel SQL Database als Azure Synapse Analytics (voorheen SQL DW). Controleer of **Azure-services en -bronnen toegang hebben tot deze serverinstelling** is **ingeschakeld** voor uw Azure SQL-server. Met deze instelling kan de Service Data Factory gegevens uit uw Azure SQL-database lezen en gegevens schrijven naar uw Azure Synapse Analytics (voorheen SQL DW). 

Als u deze instelling wilt verifiëren en inschakelen, gaat u naar uw Azure SQL-server > Beveiliging > Firewalls en virtuele netwerken > de **Azure-services en -bronnen toestaan om toegang te krijgen tot deze server in** **aan**.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken
1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
1. Ga naar de [Azure-portal.](https://portal.azure.com) 
1. Selecteer links van het azure-portalmenu de optie **Een resource** > **Analytics** > **Data Factory maken**. 
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Voer **adftutorialbulkcopydf** in op de pagina **Nieuwe gegevensfabriek** voor **naam**. 
 
   De naam van de Azure-gegevensfabriek moet **wereldwijd uniek**zijn. Als u het volgende foutbericht krijgt, wijzigt u de naam van de gegevensfactory (bijvoorbeeld uwnaamADFTutorialBulkCopyDF) en probeert u het opnieuw. Zie het artikel [Data factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
  
       `Data factory name "ADFTutorialBulkCopyDF" is not available`
1. Selecteer het Azure-**abonnement** waarin u de gegevensfactory wilt maken. 
1. Voer een van de volgende stappen uit voor de **Resourcegroep**:
     
   - Selecteer **Bestaande gebruiken**en selecteer een bestaande resourcegroep in de vervolgkeuzelijst. 
   - Selecteer **Nieuw maken**en voer de naam van een resourcegroep in.   
         
     Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
1. Selecteer **V2** als de **versie**.
1. Selecteer de **locatie** voor de gegevensfactory. Voor een lijst met Azure-regio’s waarin Data Factory momenteel beschikbaar is, selecteert u op de volgende pagina de regio’s waarin u geïnteresseerd bent, vouwt u vervolgens **Analytics** uit en gaat u naar **Data Factory**: [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/). De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich in andere regio's bevinden.
1. Klik **op Maken**.
1. Nadat de creatie is voltooid, selecteert u **Ga naar resource om** naar de pagina **Gegevensfabriek** te navigeren. 
   
1. Klik op **Author & Monitor** om de gebruikersinterface (UI) van Data Factory op een afzonderlijk tabblad te openen.
1. Ga op de pagina **Laten we aan de slag** naar het tabblad **Auteur** in het linkerdeelvenster zoals in de volgende afbeelding wordt weergegeven:

     ![Pagina Aan de slag](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-linked-services"></a>Gekoppelde services maken
U maakt gekoppelde services om uw gegevensarchieven en compute-services aan een gegevensfactory te koppelen. Een gekoppelde service beschikt over de verbindingsgegevens die de Data Factory-service tijdens runtime gebruikt om een verbinding met het gegevensarchief tot stand te brengen. 

In deze zelfstudie koppelt u uw Azure SQL Database, Azure Synapse Analytics (voorheen SQL DW) en Azure Blob Storage-gegevensopslag aan uw gegevensfabriek. De Azure SQL-database fungeert als brongegevensarchief. De Azure Synapse Analytics (voorheen SQL DW) is het sink/destination data store. De Azure Blob Storage moet de gegevens fasen voordat de gegevens in Azure Synapse Analytics (voorheen SQL DW) worden geladen met PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Maak de gekoppelde Azure SQL Database-bronservice
In deze stap maakt u een gekoppelde service om uw Azure SQL-database aan de gegevensfactory te koppelen. 

1. Klik op **Verbindingen** onder aan het venster en klik op **+ Nieuw** op de werkbalk **(knop Verbindingen** bevindt zich onder aan de linkerkolom onder **Fabrieksresources**). 

1. In het venster **New Linked Service** selecteert u **Azure SQL Database** en klikt u op **Doorgaan**. 
1. Ga in het venster **Nieuwe Gekoppelde Service (Azure SQL Database)** de volgende stappen uit: 

    a. Voer **AzureSqlDatabaseLinkedService** in bij **Name**.
    
    b. Selecteer uw Azure SQL-server bij **Server name**
    
    c. Selecteer uw Azure SQL-database bij **Database name**. 
    
    d. Voer **naam van de gebruiker** in om verbinding te maken met de Azure SQL-database. 
    
    e. Voer het **wachtwoord** voor de gebruiker in. 

    f. Als u de verbinding met de Azure SQL-database wilt testen met de opgegeven informatie, klikt u op **Test connection**.
  
    g. Klik **op Maken** om de gekoppelde service op te slaan.


### <a name="create-the-sink-azure-synapse-analytics-formerly-sql-dw-linked-service"></a>De sink Azure Synapse Analytics (voorheen SQL DW) gekoppelde service maken

1. Op het tabblad **Connections** klikt u nogmaals op **+ New** op de werkbalk. 
1. Selecteer Azure **Synapse Analytics (voorheen SQL DW)** en klik in het venster **Nieuwe gekoppelde service** op **Doorgaan**. 
1. Ga in het venster **Nieuwe Gekoppelde Service (Azure Synapse Analytics (voorheen SQL DW))** de volgende stappen uit: 
   
    a. Voer **AzureStorageLinkedService** in bij **Name**.
     
    b. Selecteer uw Azure SQL-server bij **Server name**
     
    c. Selecteer uw Azure SQL-database bij **Database name**. 
     
    d. Voer **Gebruikersnaam** in om verbinding te maken met Azure SQL-database. 
     
    e. Voer **Wachtwoord** voor de gebruiker in. 
     
    f. Als u de verbinding met de Azure SQL-database wilt testen met de opgegeven informatie, klikt u op **Test connection**.
     
    g. Klik **op Maken**.

### <a name="create-the-staging-azure-storage-linked-service"></a>De gekoppelde Azure Storage-faseringsservice maken
In deze zelfstudie gebruikt u Azure Blob-opslag als een tussentijds faseringsgebied voor het inschakelen van PolyBase voor betere kopieerprestaties.

1. Op het tabblad **Connections** klikt u nogmaals op **+ New** op de werkbalk. 
1. In het venster **New Linked Service** selecteert u **Azure Blob Storage** en klikt u op **Continue**. 
1. Ga in het venster **Nieuwe Gekoppelde Service (Azure Blob Storage)** de volgende stappen uit: 

    a. Voer **AzureStorageLinkedService** in bij **Name**.                                                 
    b. Selecteer uw **Azure Storage-account** voor **de naam van het Opslagaccount**.
    
    c. Klik **op Maken**.


## <a name="create-datasets"></a>Gegevenssets maken
In deze zelfstudie maakt u bron- en sinkgegevenssets, waarmee de locatie wordt opgegeven waar de gegevens zijn opgeslagen. 

De invoergegevensset **AzureSqlDatabaseDataset** verwijst naar de **AzureSqlDatabaseLinkedService**. De gekoppelde service geeft de verbindingsreeks aan om verbinding maken met de database. De gegevensset bevat de naam van de database en de tabel met de brongegevens. 

De uitvoergegevensset **AzureSqlDWDataset** verwijst naar de **AzureSqlDWLinkedService**. De gekoppelde service geeft de verbindingstekenreeks op om verbinding te maken met de Azure Synapse Analytics (voorheen SQL DW). De gegevensset bevat de database en de tabel waarnaar de gegevens worden gekopieerd. 

In deze zelfstudie zijn de bron- en doel-SQL-tabellen niet vastgelegd in de definities van de gegevensset. In plaats daarvan geeft de ForEach-activiteit de naam van de tabel tijdens runtime door aan de Copy-activiteit. 

### <a name="create-a-dataset-for-source-sql-database"></a>Een gegevensset maken voor bron SQL-Database

1. Klik **op + (plus)** in het linkerdeelvenster en klik vervolgens op **Gegevensset**. 

    ![Nieuw gegevenssetmenu](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. Selecteer Azure **SQL Database**in het venster **Nieuwe gegevensset** en klik op **Doorgaan**. 
    
1. Voer in het venster **Eigenschappen instellen** onder **Naam** **AzureSqlDatabaseDataset**in . Selecteer **AzureSqlDatabaseLinkedService**onder **Gekoppelde service**. Klik vervolgens op **OK**.

1. Ga naar het tabblad **Verbinding** en selecteer een tabel voor **Tabel**. Dit is een tijdelijke tabel. U geeft een query voor de brongegevensset op tijdens het maken van een pijplijn. De query wordt gebruikt om gegevens te extraheren uit de Azure SQL-database. U ook op **Bewerken** klikken en **dbo.dummyName** invoeren als tabelnaam. 
 

### <a name="create-a-dataset-for-sink-azure-synapse-analytics-formerly-sql-dw"></a>Een gegevensset maken voor sink Azure Synapse Analytics (voorheen SQL DW)

1. Klik op **+ (plus)** in het linkervenster en klik op **Dataset**. 
1. Selecteer azure **Synapse Analytics (voorheen SQL DW)** in het venster **Nieuwe gegevensset** en klik vervolgens op **Doorgaan**.
1. Voer in het venster **Eigenschappen instellen** onder **Naam** **AzureSqlDWDataset**in . Selecteer **AzureSqlDWLinkedService**onder **Gekoppelde service**. Klik vervolgens op **OK**.
1. Ga naar het tabblad **Parameters**, klik op **+ Nieuw** en voer **DWTableName** in als de parameternaam. Als u deze naam van de pagina kopieert/plakt, moet u ervoor zorgen dat er geen **teken met een trailingruimte** is aan het einde van **DWTableName**.
1. Ga naar het tabblad **Verbinding**, 

    a. Schakel **voor Tabel**de optie Bewerken **in.** Voer **dbo** in het invoervak voor de eerste tabelnaam in. En selecteer vervolgens in het tweede invoervak en klik hieronder op de koppeling **Dynamische inhoud toevoegen.** 

    ![Tabelnaam voor gegevenssetverbinding](./media/tutorial-bulk-copy-portal/dataset-connection-tablename.png)

    b. Klik **op** de pagina Dynamische inhoud toevoegen op de **DWTAbleName** onder **Parameters**, die automatisch het tekstvak `@dataset().DWTableName`met de bovenste expressie invult en klik vervolgens op **Voltooien**. De **tableName**-eigenschap van de gegevensset is ingesteld op de waarde die wordt doorgegeven als argument voor de **DWTableName**-parameter. De ForEach-activiteit doorloopt een lijst met tabellen en geeft deze één voor één door aan de Copy-activiteit. 

    ![Opbouwfunctie voor gegevenssetparameters](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)
 
## <a name="create-pipelines"></a>Pijplijnen maken
In deze zelfstudie gaat u twee pijplijnen maken: **IterateAndCopySQLTables** en **GetTableListAndTriggerCopyData**. 

De **gettablelist-en triggercopygegevenspijplijn** voert twee acties uit:

* Zoekt de systeemtabel van Azure SQL Database op om de lijst met te kopiëren tabellen op te halen.
* Hiermee activeert u de **pijplijnfrequentieAndCopySQLTables** om de werkelijke gegevenskopie uit te vullen.

De **pijplijn IterateAndCopySQLTables** neemt een lijst met tabellen als parameter. Voor elke tabel in de lijst worden gegevens uit de tabel in Azure SQL Database gekopieerd naar Azure Synapse Analytics (voorheen SQL DW) met gefaseerde copy en PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>De pijplijn IterateAndCopySQLTables maken

1. Klik op **+ (plus)** in het linkervenster en klik op **Pipeline**.

    ![Menu Nieuwe pijplijn](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. Geef op het tabblad **Algemeen****IterateAndCopySQLTables** op als naam. 

1. Open het tabblad **Parameters** en voer de volgende stappen uit: 

    a. Klik op **+ New**. 
    
    b. **Tabellijst** invoeren voor de **parameternaam**.
    
    c. Selecteer **Matrix** bij **Type**.

1. Vouw in de werkset **Activities** de optie **Iteration & Conditions** uit en sleep de **ForEach**-activiteit naar het ontwerpoppervlak voor pijplijnen. U kunt ook zoeken naar activiteiten in de werkset **Activiteiten**. 

    a. Voer onderaan het tabblad **Algemeen****IterateSQLTables** bij **Naam** in. 

    b. Ga naar het tabblad **Instellingen,** klik op het invoervak voor **items**en klik hieronder op de koppeling **Dynamische inhoud toevoegen.** 

    c. Klik op de pagina **Dynamische inhoud toevoegen** de secties **Systeemvariabelen** en **-functies** op de **tabelLijst** onder `@pipeline().parameter.tableList` **Parameters**, die automatisch het tekstvak met de bovenste expressie als . Klik vervolgens op **Voltooien**. 

    ![Opbouwfunctie voor Foreach-parameters](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Ga naar het tabblad **Activiteiten** en klik op het **potloodpictogram** om een onderliggende activiteit toe te voegen aan de **Activiteit ForEach.**
    ![Voor elke activiteitsbouwer](./media/tutorial-bulk-copy-portal/for-each-activity-builder.png)

1. Vouw in de **gereedschapsset Activiteiten** **Move & Transfer**uit en **kopieergegevensactiviteit** kopiëren naar het oppervlak van de pijplijnontwerper. Let op het breadcrumb-menu bovenaan. De **IterateAndCopySQLTable** is de naam van de pijplijn en **IterateSQLTables** is de activiteitsnaam ForEach. Voor de ontwerpfunctie is het activiteitbereik actief. Als u vanuit de ForEach-editor wilt teruggaan naar de pijplijneditor, klikt u op de koppeling in het broodkruimelmenu. 

    ![Kopiëren in ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Open het tabblad **Source** en voer de volgende stappen uit:

    1. Selecteer **AzureSqlDatabaseDataset** bij **Source Dataset**. 
    1. Selecteer **Queryoptie** voor **Query gebruiken**. 
    1. Klik op het invoervak **Query** -> Selecteer de koppeling **Dynamische inhoud toevoegen** hieronder -> voer de volgende expressie in voor **Query** -> selecteer **Voltooien**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 


1. Open het tabblad **Sink** en voer de volgende stappen uit: 

    1. Selecteer **AzureSqlDWDataset** bij **Sink Dataset**.
    1. Klik op het invoervak voor de parameter WAARDE van DWTableName `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` -> hieronder de dynamische inhoud **toevoegen** selecteert, expressie als script invoert > selecteer **Voltooien**.
    1. Selecteer **PolyBase**voor de methode Kopiëren . 
    1. Schakel de **standaardoptie Type gebruiken uit.** 
    1. Klik op het invoervak **Prekopieerscript** -> Selecteer de koppeling **Dynamische inhoud toevoegen** hieronder -> voer de volgende expressie als script in -> selecteer **Voltooien**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Sink-instellingen kopiëren](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
1. Ga naar het tabblad **Instellingen** en voer de volgende stappen uit: 

    1. Schakel het selectievakje voor **Fasering inschakelen in**.
    1. Selecteer **AzureStorageLinkedService** bij **Store Account Linked Service**.

1. Klik in de bovenste pijplijnwerkbalk op **Valideren** om de instellingen voor de pijplijn te valideren. Zorg ervoor dat er geen validatiefout optreedt. Sluit het venster **Pipeline Validation Report** door op **>>** te klikken.

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>De pijplijn GetTableListAndTriggerCopyData maken

Deze pijplijn doet twee acties:

* Zoekt de systeemtabel van Azure SQL Database op om de lijst met te kopiëren tabellen op te halen.
* Activeert de pijplijn 'IterateAndCopySQLTables' om het kopiëren van de gegevens daadwerkelijk uit te voeren.

1. Klik op **+ (plus)** in het linkervenster en klik op **Pipeline**.
1. Wijzig op het tabblad **Algemeen** de naam van de pijplijn in **GetTableListAndTriggerCopyData**. 

1. Vouw in de **gereedschapsset Activiteiten** de activiteit **Algemeen**en **Opzoekactiviteit** slepen naar het oppervlak van de pijplijnontwerper uit en ga als volgt te werk:

    1. Voer **LookupTableList** in als **Name**. 
    1. Voer **De tabellijst ophalen uit de Azure SQL-database** in bij **Description**.

1. Ga naar het tabblad **Instellingen** en voer de volgende stappen uit:

    1. Selecteer **AzureSqlDatabaseDataset** bij **Source Dataset**. 
    1. Selecteer **Query** voor **gebruik query**. 
    1. Voer bij **Query** de volgende SQL-query in.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Schakel het selectievakje voor het veld **First row only** uit.

        ![Lookup-activiteit - pagina instellingen](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Activiteit **pijplijn** uitvoeren met slepen uitvoeren vanuit de gereedschapsset Activiteiten naar het oppervlak van de pijplijnontwerper en de naam instellen op **TriggerCopy**.

1. Als u de **opzoekactiviteit** wilt **verbinden met** de activiteit Pijplijn **uitvoeren,** sleept u het **groene vak** dat is gekoppeld aan de opzoekactiviteit links van pijplijnactiviteit uitvoeren.

    ![Lookup- en Execute Pipeline-activiteiten verbinden](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)

1. Ga naar het tabblad **Instellingen** van **Pijplijnactiviteit uitvoeren** en voer de volgende stappen uit: 

    1. Selecteer **IterateAndCopySQLTables** bij **Invoked pipeline**. 
    1. Vouw de sectie **Geavanceerd** uit en schakel het selectievakje **Wachten na voltooiing uit.**
    1. Klik op **+ New** in de sectie **Parameters**. 
    1. **Tabellijst** invoeren voor **parameternaam**.
    1. Klik op het invoervak VALUE -> selecteer de koppeling **Dynamische inhoud toevoegen** hieronder - voer `@activity('LookupTableList').output.value` als waarde voor de tabelnaam -> selecteer **Voltooien**. U stelt de resultatenlijst van de opzoekactiviteit in als invoer voor de tweede pijplijn. De lijst met resultaten bevat de lijst met tabellen waarvan de gegevens naar de bestemming moeten worden gekopieerd. 

        ![Execute Pipeline-activiteit - pagina instellingen](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)

1. Valideer de pijplijn door te klikken op de knop **Validate** op de werkbalk. Controleer of er geen validatiefouten zijn. Sluit het venster **Pipeline Validation Report** door op **>>** te klikken.

1. Als u entiteiten (gegevenssets, pijplijnen, enz.) wilt publiceren naar de service Gegevensfabriek, klikt u op Alles boven in het venster **publiceren.** Wacht totdat de publicatie is uitgevoerd. 

## <a name="trigger-a-pipeline-run"></a>Een pijplijnuitvoering activeren

1. Ga naar pijplijn **GetTableListAndTriggerCopyData,** klik op **Trigger toevoegen** op de bovenste pijplijngereedschapsbalk en klik **vervolgens op Nu activeren**. 

1. Bevestig de run op de pagina **Pijplijnrun** en selecteer **Voltooien**.

## <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

1. Ga naar het tabblad **Monitor.** Klik op **Vernieuwen** totdat u wordt weergegeven voor beide pijplijnen in uw oplossing. Blijf de lijst vernieuwen totdat de status **Succeeded** wordt weergegeven. 

1. Als u activiteitsruns wilt weergeven die zijn gekoppeld aan de **pijplijn GetTableListAndTriggerCopyData,** klikt u op de koppeling pijplijnnaam voor de pijplijn. Er moeten twee uitvoeringen van activiteit voor deze pijplijnuitvoering te zien zijn. 
    ![Monitorpijplijnrun](./media/tutorial-bulk-copy-portal/monitor-pipeline.png)
1. Als u de uitvoer van de **opzoekactiviteit** wilt weergeven, klikt u op de koppeling **Uitvoer** naast de activiteit onder de kolom **ACTIVITEITSNAAM.** U kunt het venster **Output** maximaliseren en herstellen. Nadat u de uitvoer hebt bekeken, klikt u op **X** om het venster **Output** te sluiten.

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
1. Als u wilt teruggaan naar de weergave **Pijplijnuitvoeringen,** klikt u boven aan het broodkruimelmenu op **Alle pijplijnuitvoeringen.** Klik op **koppeling IterateAndCopySQLTables** (onder kolom **PIPELINENAME)** om activiteitsruns van de pijplijn weer te geven. Er wordt één **kopieeractiviteit** uitgevoerd voor elke tabel in de uitvoer **van opzoekactiviteit.** 

1. Controleer of de gegevens zijn gekopieerd naar het doel Azure Synapse Analytics (voorheen SQL DW) dat u in deze zelfstudie hebt gebruikt. 

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u de volgende stappen uitgevoerd: 

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Azure SQL Database, Azure Synapse Analytics (voorheen SQL DW) en gekoppelde Azure Storage-services maken.
> * Azure SQL Database en Azure Synapse Analytics (voorheen SQL DW)-gegevenssets maken.
> * Een pijplijn maken om de te kopiëren tabellen op te zoeken en een andere pijplijn om de kopieerbewerking daadwerkelijk uit te voeren. 
> * Een pijplijnuitvoering starten.
> * De uitvoering van de pijplijn en van de activiteit controleren

Ga door naar de volgende zelfstudie voor informatie over het incrementeel kopiëren van gegevens uit een bron naar een bestemming:
> [!div class="nextstepaction"]
>[Gegevens incrementeel kopiëren](tutorial-incremental-copy-portal.md)
