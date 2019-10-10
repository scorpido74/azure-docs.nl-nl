---
title: Incrementeel meerdere tabellen kopiëren met behulp van Azure Data Factory | Microsoft Docs
description: In deze zelfstudie maakt u een Azure Data Factory-pijplijn waarmee wijzigingsgegevens incrementeel uit meerdere tabellen van een lokale Microsoft SQL Server worden gekopieerd naar een Azure SQL-database.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/20/2018
ms.author: yexu
ms.openlocfilehash: 44ae433040c2c9cab47567cb663d4e588311a4a1
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177417"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Incrementeel gegevens uit meerdere tabellen in SQL Server naar een Azure SQL-database kopiëren
In deze zelfstudie maakt u een Azure Data Factory met een pijplijn waarmee wijzigingsgegevens uit meerdere tabellen van een lokale SQL-server naar een Azure SWL-database worden gekopieerd.    

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Bereid de bron- en doelserver gegevensopslag voor.
> * Een gegevensfactory maakt.
> * Een zelf-hostende Integration Runtime maken.
> * De Integration Runtime installeren. 
> * Maak gekoppelde services. 
> * Maak bron-, sink- en grenswaardegegevenssets.
> * Maken, starten en controleren van een pijplijn.
> * Bekijk de resultaten.
> * Gegevens in brontabellen toevoegen of bijwerken.
> * De pijplijn opnieuw uitvoeren en controleren.
> * De eindresultaten bekijken.

## <a name="overview"></a>Overzicht
Dit zijn de belangrijke stappen voor het maken van deze oplossing: 

1. **Selecteer de grenswaardekolom**.
    
    Selecteer één kolom in elke tabel van de brongegevensopslag, die kan worden gebruikt om de nieuwe of bijgewerkte records voor elke uitvoering te segmenteren. Normaal gesproken nemen de gegevens in deze geselecteerde kolom (bijvoorbeeld, last_modify_time of id) toe wanneer de rijen worden gemaakt of bijgewerkt. De maximale waarde in deze kolom wordt gebruikt als grenswaarde.

1. **Bereid een gegevensopslag voor om de grenswaarde in op te slaan**.   
    
    In deze zelfstudie slaat u de grenswaarde op in een SQL-database.

1. **Maak een pijplijn met de volgende activiteiten**: 
    
    a. Maak een ForEach-activiteit die door een lijst met namen van gegevensbrontabellen loopt, die als parameter is doorgegeven aan de pijplijn. Voor elke brontabel roept deze de volgende activiteiten voor het laden van de deltagegevens voor deze tabel op.

    b. Maak twee opzoekactiviteiten. Gebruik de eerste opzoekactiviteit om de laatste grenswaarde op te halen. Gebruik de tweede opzoekactiviteit om de nieuwe grenswaarde op te halen. Deze grenswaarden worden doorgegeven aan de kopieeractiviteit.

    c. Maak een kopieeractiviteit waarmee rijen uit de brongegevensopslag worden gekopieerd met een waarde uit de grenswaardekolom die groter is dan de oude grenswaarde en kleiner dan de nieuwe grenswaarde. Vervolgens worden de deltagegevens uit de brongegevensopslag als een nieuw bestand gekopieerd naar een Azure Blob-opslag.

    d. Maak een opgeslagen-procedureactiviteit waarmee de grenswaarde wordt bijgewerkt voor de pijplijn die de volgende keer wordt uitgevoerd. 

    Hier volgt de diagramoplossing op hoog niveau: 

    ![Stapsgewijs gegevens laden](media/tutorial-incremental-copy-multiple-tables-portal/high-level-solution-diagram.png)


Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten
* **SQL Server**. In deze zelfstudie gebruikt u een on-premises SQL Server-database als een brongegevensopslag. 
* **Azure SQL-database**. U gebruikt een SQL database als de sink-gegevensopslag. Als u geen SQL-database hebt, raadpleegt u het artikel [Een Azure SQL-database maken](../sql-database/sql-database-get-started-portal.md) om een database te maken. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Brontabellen maken in uw SQL Server-database

1. Start SQL Server Management Studio en maak verbinding met uw on-premises SQL-database.

1. Klik in **Server Explorer** met de rechtermuisknop op de database en kies **Nieuwe query**.

1. Voer de volgende SQL-opdracht uit op uw database om tabellen te maken met de naam `customer_table` en `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Doeltabellen in uw Azure SQL-database maken
1. Open SQL Server Management Studio en maak verbinding met uw Azure SQL-database.

1. Klik in **Server Explorer** met de rechtermuisknop op de database en kies **Nieuwe query**.

1. Voer de volgende SQL-opdracht uit op uw SQL-database om tabellen te maken met de naam `customer_table` en `project_table`:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Nog een tabel in de Azure SQL-database maken om de bovengrenswaarde op te slaan
1. Voer de volgende SQL-opdracht uit op de SQL-database om een tabel met de naam `watermarktable` te maken om de grenswaarde op te slaan: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Initiële watermerkwaarden voor beide brontabellen in de watermerktabel invoegen.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Een opgeslagen procedure maken in de Azure SQL-database 

Voer de volgende opdracht uit om een opgeslagen procedure te maken in de SQL-database. Deze opgeslagen procedure werkt de bovengrenswaarde bij elke pijplijnuitvoering bij. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Gegevenstypen en aanvullende opgeslagen procedures maken in de Azure SQL-database
Voer de volgende query uit om twee opgeslagen procedures en twee gegevenstypen te maken in de SQL-database. Deze worden gebruikt voor het samenvoegen van de gegevens uit de brontabellen in doeltabellen.

Om het traject eenvoudig te laten beginnen, gebruiken we rechtstreeks deze opgeslagen procedures waarmee de Delta gegevens worden door gegeven via een tabel variabele en deze vervolgens samen voegen in het doel archief. Wees voorzichtig dat een ' grote ' aantal Delta rijen (meer dan 100) niet wordt verwacht om te worden opgeslagen in de tabel variabele.  

Als u een groot aantal Delta rijen in het doel archief moet samen voegen, kunt u het beste de Kopieer activiteit gebruiken om alle Delta gegevens te kopiëren naar een tijdelijke tabel ' staging ' in het doel archief en vervolgens uw eigen opgeslagen procedure hebt gemaakt zonder tabel Varia te gebruiken kan ze van de tabel "staging" samen voegen met de tabel definitief. 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
1. Klik op **Nieuw** in het linkermenu en klik vervolgens op **Gegevens en analyses** en **Data Factory**. 
   
   ![Nieuw -> DataFactory](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory-menu.png)
1. Voer op de pagina **Nieuwe gegevensfactory** **ADFTutorialBulkCopyDF** in als de **naam**. 
      
     ![De pagina Nieuwe data factory](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory.png)
 
   De naam van de Azure-gegevensfactory moet **wereldwijd uniek** zijn. Als u het volgende foutbericht krijgt, wijzigt u de naam van de gegevensfactory (bijvoorbeeld uwnaamADFTutorialDataFactory) en probeert u het opnieuw. Zie het artikel [Data factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
  
       `Data factory name ADFMultiIncCopyTutorialDF is not available`
1. Selecteer het Azure-**abonnement** waarin u de gegevensfactory wilt maken. 
1. Voer een van de volgende stappen uit voor de **Resourcegroep**:
     
      - Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst. 
      - Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in.   
         
        Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/resource-group-overview.md) voor meer informatie.  
1. Selecteer **V2 (Preview)** als de **versie**.
1. Selecteer de **locatie** voor de gegevensfactory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich in andere regio's bevinden.
1. Selecteer **Vastmaken aan dashboard**.     
1. Klik op **Maken**.      
1. Op het dashboard ziet u de volgende tegel met de status: **Gegevensfactory implementeren**. 

    ![tegel met de status 'gegevensfactory implementeren'](media/tutorial-incremental-copy-multiple-tables-portal/deploying-data-factory.png)
1. Na het aanmaken ziet u de pagina **Data Factory** zoals weergegeven in de afbeelding.
   
   ![Startpagina van de gegevensfactory](./media/tutorial-incremental-copy-multiple-tables-portal/data-factory-home-page.png)
1. Klik op de tegel **Author & Monitor** om de gebruikersinterface (UI) van Azure Data Factory te openen in een afzonderlijk tabblad.
1. Op de pagina Aan de slag met Azure Data Factory-gebruikersinterface, klikt u op **Pijplijn maken** (of) schakelt u over naar het tabblad **Bewerken**. 

   ![Pagina Aan de slag](./media/tutorial-incremental-copy-multiple-tables-portal/get-started-page.png)

## <a name="create-self-hosted-integration-runtime"></a>Een zelf-hostende integratieruntime maken
Als u gegevens uit een gegevensopslag in een particulier netwerk (on-premises) naar een Azure-gegevensarchief verplaatst, installeert u een zelf-hostende integratieruntime (IR) in uw on-premises-omgeving. De zelf-hostende IR verplaatst gegevens tussen uw particuliere netwerk en Azure. 

1. Klik op **Verbindingen** onderaan in het linkerdeelvenster en schakel over naar **Integratieruntimes** in het venster **Verbindingen**. 

   ![Tabblad Verbindingen](./media/tutorial-incremental-copy-multiple-tables-portal/connections-tab.png)
1. In het tabblad **Integratieruntimes** klikt u op **+ Nieuw**. 

   ![Knop Nieuwe integratieruntime](./media/tutorial-incremental-copy-multiple-tables-portal/new-integration-runtime-button.png)
1. In het venster **Instellen van Integration Runtime** selecteert u de optie **Gegevensverkeer uitvoeren en activiteiten verzenden naar externe berekeningen** en klikt u op **Volgende**. 

   ![Type integratieruntime selecteren](./media/tutorial-incremental-copy-multiple-tables-portal/select-integration-runtime-type.png)
1. Selecteer ** Privénetwerk** en klik op **Volgende**. 

   ![Privénetwerk selecteren](./media/tutorial-incremental-copy-multiple-tables-portal/select-private-network.png)
1. Voer **MySelfHostedIR** in bij **Name** en klik op **Next**. 

   ![Zelf-hostende IR-naam](./media/tutorial-incremental-copy-multiple-tables-portal/self-hosted-ir-name.png)
1. Klik in de sectie **Optie 1: snelle installatie** op **Klik hier om de snelle installatie voor deze computer te starten**. 

   ![Klik op de koppeling Snelle installatie](./media/tutorial-incremental-copy-multiple-tables-portal/click-express-setup.png)
1. Klik in het venster **Snelle installatie van integratieruntime (zelf-hostend)** op **Sluiten**. 

   ![Installatie van integratieruntime - geslaagd](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
1. Klik in de webbrowser in het venster **Installatie van integratieruntime** op **Voltooien**. 

   ![Installatie van integratieruntime - voltooien](./media/tutorial-incremental-copy-multiple-tables-portal/click-finish-integration-runtime-setup.png)
1. Controleer of u **MySelfHostedIR** in de lijst met integratieruntimes ziet.

    ![Integratieruntimes - lijst](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtimes-list.png)

## <a name="create-linked-services"></a>Gekoppelde services maken
U maakt gekoppelde services in een gegevensfactory om uw gegevensarchieven en compute-services aan de gegevensfactory te koppelen. In deze sectie maakt u gekoppelde services in de lokale SQL Server-database en de SQL-database. 

### <a name="create-the-sql-server-linked-service"></a>De gekoppelde service voor SQL Server maken
In deze stap gaat u uw on-premises SQL Server-database aan de data factory koppelen.

1. Schakel in het venster **Verbindingen** over van het tabblad **Integratieruntimes** tab naar het tabblad **Gekoppelde Services** en klik op **+ Nieuw**.

    ![Knop Nieuwe gekoppelde service](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
1. In het venster **Nieuwe gekoppelde service** selecteert u **Microsoft SQL Server** en klikt u op **Doorgaan**. 

    ![SQL-server selecteren](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server.png)
1. Voer in het venster **Nieuwe gekoppelde service** de volgende stappen uit:

    1. Voer **SqlServerLinkedService** in als **Naam**. 
    1. Selecteer **MySelfHostedIR** bij **Connect via integration runtime**. Dit is een **belangrijke** stap. De standaard integratieruntime kan geen verbinding maken met een on-premises-gegevensarchief. Gebruik de zelf-hostende integratieruntime die u eerder hebt gemaakt. 
    1. Bij **Servernaam** voert u de naam in van de computer die de SQL Server-database bevat.
    1. Bij **Naam database** voert u de naam in van de database in uw Microsoft SQL Server met de brongegevens. U hebt een tabel gemaakt en gegevens in deze database ingevoegd als onderdeel van de vereisten. 
    1. Bij **Verificatietype** selecteert u het **type van de verificatie** dat u wilt gebruiken voor verbinding met de database. 
    1. Bij **Gebruikersnaam** voert u de naam in van de gebruiker met toegang tot de SQL Server-database. Als u een slash wilt gebruiken (`\`) in de naam van uw gebruikersaccount of server, moet u het escapeteken (`\`) gebruiken. Een voorbeeld is `mydomain\\myuser`.
    1. Voer bij **Wachtwoord** het **wachtwoord** voor de gebruiker in. 
    1. Als u wilt testen of de Data Factory verbinding kan maken met uw SQL Server-database, klikt u op **Verbinding testen**. Los alle fouten op totdat de verbinding is geslaagd. 
    1. Klik op **Opslaan** om de gekoppelde service op te slaan.

        ![Gekoppelde SQL Server-service - instellingen](./media/tutorial-incremental-copy-multiple-tables-portal/sql-server-linked-service-settings.png)

### <a name="create-the-azure-sql-database-linked-service"></a>De gekoppelde Azure SQL Database-service maken
In de laatste stap maakt u een gekoppelde service om uw Microsoft SQL Server-brondatabase aan de gegevensfactory te koppelen. In deze stap koppelt u uw doel/sink-Azure SQL-database aan uw data factory. 

1. Schakel in het venster **Verbindingen** over van het tabblad **Integratieruntimes** tab naar het tabblad **Gekoppelde Services** en klik op **+ Nieuw**.

    ![Knop Nieuwe gekoppelde service](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
1. In het venster **New Linked Service** selecteert u **Azure SQL Database** en klikt u op **Doorgaan**. 
1. Voer in het venster **Nieuwe gekoppelde service** de volgende stappen uit:

    1. Voer **AzureSqlDatabaseLinkedService** in bij **Name**. 
    1. Bij de **Servernaam** selecteert u de naam van uw Azure SQL-server in de vervolgkeuzelijst. 
    1. Bij de **Databasenaam** selecteert u de Azure SQL-database waarin u customer_table en project_table als onderdeel van de vereisten heeft gemaakt. 
    1. Bij **Gebruikersnaam** voert u de naam in van de gebruiker met toegang tot de Azure SQL-database. 
    1. Voer bij **Wachtwoord** het **wachtwoord** voor de gebruiker in. 
    1. Als u wilt testen of de Data Factory verbinding kan maken met uw SQL Server-database, klikt u op **Verbinding testen**. Los alle fouten op totdat de verbinding is geslaagd. 
    1. Klik op **Opslaan** om de gekoppelde service op te slaan.

        ![Instelling voor gekoppelde Azure SQL-service](./media/tutorial-incremental-copy-multiple-tables-portal/azure-sql-linked-service-settings.png)
1. Controleer of u twee gekoppelde services in de lijst kunt zien. 
   
    ![Twee gekoppelde services](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Gegevenssets maken
In deze stap maakt u gegevenssets die de gegevensbron, het gegevensdoel en de plaats voor het opslaan van de bovengrens aangeven.

### <a name="create-a-source-dataset"></a>Een brongegevensset maken

1. Klik op **+ (plus)** in het linkervenster en klik op **Gegevensset**.

   ![Menu Nieuwe gegevensset](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. Selecteer in het venster **Nieuwe gegevensset** **SQL Server** en klik op **Voltooien**. 

   ![SQL-server selecteren](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server-for-dataset.png)
1. Uit ziet een nieuw tabblad dat geopend wordt in de webbrowser voor het configureren van de gegevensset. U ziet ook de gegevensset in de structuurweergave. Voer in het tabblad **Algemeen** in het venster Eigenschappen onderaan **SourceDataset** in als **Naam**. 

   ![Brongegevensset - naam](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-general.png)
1. Ga in het venster Eigenschappen naar het tabblad **Verbindingen** en klik op **SqlServerLinkedService** voor **Gekoppelde service**. U selecteert hier geen tabel. De kopieeractiviteit in de pijplijn gebruikt een SQL-query voor het laden van de gegevens in plaats van de hele tabel te laden.

   ![Brongegevensset - verbinding](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Een sinkgegevensset maken
1. Klik op **+ (plus)** in het linkervenster en klik op **Gegevensset**.

   ![Menu Nieuwe gegevensset](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. Selecteer in het venster **Nieuwe gegevensset** de optie **Azure SQL-database** en klik op **Voltooien**. 

   ![Azure SQL-database selecteren](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
1. Uit ziet een nieuw tabblad dat geopend wordt in de webbrowser voor het configureren van de gegevensset. U ziet ook de gegevensset in de structuurweergave. Voer in het tabblad **Algemeen** in het venster Eigenschappen onderaan **SinkDataset** in als **Naam**.

   ![Sink-gegevensset - algemeen](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-general.png)
1. Ga naar het tabblad **Parameters** in het venster Eigenschappen en voer de volgende stappen uit: 

    1. Klik op **Nieuw** in de sectie **Parameters maken/bijwerken**. 
    1. Voer **SinkTableName** in bij de **naam** en **Tekenreeks** voor het **type**. Deze gegevensset gebruikt **SinkTableName** als parameter. De parameter SinkTableName wordt in runtime dynamisch ingesteld door de pijplijn. De ForEach-activiteit in de pijplijn doorloopt een lijst met namen van tabellen en geeft de tabelnaam door aan deze gegevensset in elke iteratie.
   
       ![Sink gegevensset - eigenschappen](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)
1. Ga in het venster Eigenschappen naar het tabblad **Verbindingen** en selecteer **AzureSqlLinkedService** voor **Gekoppelde service**. Klik voor de eigenschap **Tabel** op **Dynamische inhoud toevoegen**. 

   ![Sink-gegevensset - verbinding](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection.png)
    
    
1. Selecteer in de sectie **Parameters** de optie **SinkTableName**
   
   ![Sink-gegevensset - verbinding](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-dynamicContent.png)

   
 1. Nadat u op **volt ooien**hebt geklikt, ziet u **\@dataset (). SinkTableName** als de tabel naam.
   
   ![Sink-gegevensset - verbinding](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png)

### <a name="create-a-dataset-for-a-watermark"></a>Een gegevensset maken voor een grenswaarde
In deze stap maakt u een gegevensset voor het opslaan van een bovengrenswaarde. 

1. Klik op **+ (plus)** in het linkervenster en klik op **Gegevensset**.

   ![Menu Nieuwe gegevensset](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. Selecteer in het venster **Nieuwe gegevensset** de optie **Azure SQL-database** en klik op **Voltooien**. 

   ![Azure SQL-database selecteren](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
1. Voer in het tabblad **Algemeen** in het venster Eigenschappen onderaan **WatermarkDataset** in als **Naam**.
1. Ga naar het tabblad **Verbinding** en voer de volgende stappen uit: 

    1. Selecteer **AzureSqlDatabaseLinkedService** als **Gekoppelde service**.
    1. Selecteer **[dbo].[watermarktable]** als **Tabel**.

       ![WatermarkDataset - verbinding](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Een pijplijn maken
In deze pijplijn wordt een lijst met tabelnamen gebruikt als parameter. De ForEach-activiteit doorloopt de lijst met namen van tabellen en voert de volgende bewerkingen uit: 

1. Gebruik de opzoekactiviteit voor het ophalen van de oude bovengrenswaarde (de initiële waarde, of de waarde die is gebruikt in de laatste iteratie).

1. Gebruik de opzoekactiviteit voor het ophalen van de nieuwe bovengrenswaarde (de maximale waarde van de bovengrenskolom in de brontabel).

1. Gebruik de kopieeractiviteit voor het kopiëren van gegevens tussen deze twee bovengrenswaarden uit de brondatabase naar de doeldatabase.

1. Gebruik de opgeslagen-procedureactiviteit voor het bijwerken van de oude bovengrenswaarde die in de eerste stap van de volgende iteratie moet worden gebruikt. 

### <a name="create-the-pipeline"></a>Maak de pijplijn

1. Klik op **+ (plus)** in het linkervenster en klik op **Pipeline**.

    ![Nieuwe pijplijn - menu](./media/tutorial-incremental-copy-multiple-tables-portal/new-pipeline-menu.png)
1. Voer in het tabblad **Algemeen** in het venster **Eigenschappen** **IncrementalCopyPipeline** in als **Naam**. 

    ![Naam pijplijn](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-name.png)
1. Voer bij in het venster **Eigenschappen** de volgende stappen uit: 

    1. Klik op **+ New**. 
    1. Voer **tableList** in als **Name**-parameter. 
    1. Selecteer **Object** voor de parameter **Type**.

    ![Pijplijnparameters](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-parameters.png) 
1. Vouw in de werkset **Activiteiten** de optie **Iteratie en voorwaarden** uit en sleep de **ForEach**-activiteit naar het ontwerpoppervlak voor pijplijnen. Voer in het tabblad **Algemeen** van het venster **Eigenschappen** **IterateSQLTables** in. 

    ![ForEach-activiteitnaam](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-name.png)
1. Ga naar het tabblad **Instellingen** in het venster **Eigenschappen** en voer `@pipeline().parameters.tableList` in voor **Items**. De ForEach-activiteit doorloopt de lijst met tabellen en voert de volgende incrementele kopiebewerkingen uit. 

    ![ForEach-activiteit - instellingen](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)
1. Selecteer de activiteit **ForEach** in de pijplijn als dat nog niet is gebeurd. Klik op de knop **Bewerken (potloodpictogram)** .

    ![ForEach-activiteit - bewerken](./media/tutorial-incremental-copy-multiple-tables-portal/edit-foreach.png)
1. Vouw in de **Activiteiten**-werkset de optie **Algemeen** uit. Gebruik vervolgens slepen-en-neerzetten om de **opzoekactiviteit** te verplaatsen naar het ontwerpoppervlak voor pijplijnen. en voer **LookupOldWaterMarkActivity** in bij **Naam**.

    ![Eerste opzoekactiviteit - naam](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-name.png)
1. Ga naar het tabblad **Instellingen** in het venster **Eigenschappen** en voer de volgende stappen uit: 

    1. Selecteer **WatermarkDataset** in het veld **Brongegevensset**.
    1. Selecteer **Query** bij **Use Query**. 
    1. Voer bij **Query** de volgende SQL-query in. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![Eerste opzoekactiviteit - instellingen](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
1. Sleep de activiteit **Opzoeken** uit de **Activiteiten**-werkset en voer **LookupNewWaterMarkActivity** in als **Naam**.
        
    ![Tweede opzoekactiviteit - naam](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-name.png)
1. Schakel over naar het tabblad **Instellingen**.

    1. Selecteer **SourceDataset** in het veld **Source Dataset**. 
    1. Selecteer **Query** bij **Use Query**.
    1. Voer bij **Query** de volgende SQL-query in.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Tweede opzoekactiviteit - instellingen](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
1. Sleep de activiteit **Kopiëren** uit de **Activiteiten**-werkset en voer **IncrementalCopyActivity** in als **Naam**. 

    ![Kopieeractiviteit - naam](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-name.png)
1. Verbind **Opzoek**-activiteiten één voor één met de activiteit **Kopiëren**. Om te verbinden, start u met het slepen van het **groene** vak gekoppeld aan de **Opzoek**-activiteit en zet u dit neer op de activiteit **Kopiëren**. Laat de muisknop los als u ziet dat de randkleur van de kopieeractiviteit is gewijzigd in **blauw**.

    ![Opzoekactiviteiten verbinden met kopieeractiviteit](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
1. U ziet dat de activiteit **Kopiëren** in de pijplijn is mislukt. Ga naar het tabblad **Bron** in het venster **Eigenschappen**. 

    1. Selecteer **SourceDataset** in het veld **Source Dataset**. 
    1. Selecteer **Query** bij **Use Query**. 
    1. Voer bij **Query** de volgende SQL-query in.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Kopieeractiviteit - broninstellingen](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
1. Open het tabblad **Sink** en selecteer **SinkDataset** in het veld **Sink Dataset**. 
        
    ![Kopieeractiviteit - sinkinstellingen](./media/tutorial-incremental-copy-multiple-tables-portal/copy-sink-settings.png)
1. Voer de volgende stappen uit:

    1. Voer in de eigenschap **DataSet** voor de para meter **SinkTableName** de `@{item().TABLE_NAME}` in.
    1. Voer voor de eigenschap **opgeslagen procedure naam** `@{item().StoredProcedureNameForMergeOperation}` in.
    1. Voer `@{item().TableType}` in bij eigenschap **type van tabel** .


        ![Kopieeractiviteit - parameters](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
1. Sleep de **Stored Procedure**-activiteit vanuit de werkset **Activities** naar het ontwerpoppervlak voor pijplijnen. Verbind de **Kopieer**-activiteit met de **Opgeslagen procedure**-activiteit. 

    ![Kopieeractiviteit - parameters](./media/tutorial-incremental-copy-multiple-tables-portal/connect-copy-to-sproc.png)
1. Selecteer de **Opgeslagen procedure**-activiteit in de pijplijn en voer **StoredProceduretoWriteWatermarkActivity** in als **Naam** in het tabblad **Algemeen** van het venster **Eigenschappen**. 

    ![Opgeslagen-procedureactiviteit - naam](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-name.png)
1. Ga naar het tabblad **SQL-account** en selecteer **AzureSqlDatabaseLinkedService** als **Gekoppelde Service**.

    ![Opgeslagen-procedureactiviteit - SQL-account](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
1. Open het tabblad **Stored Procedure** en voer de volgende stappen uit:

    1. Selecteer `usp_write_watermark` als **Opgeslagen procedurenaam**. 
    1. Selecteer **Importparameter**. 
    1. Geef de volgende waarden op voor de parameters: 

        | Naam | Type | Waarde | 
        | ---- | ---- | ----- |
        | LastModifiedtime | Datum/tijd | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | Tekenreeks | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Opgeslagen-procedureactiviteit - instellingen voor de opgeslagen procedure](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
1. Klik in het linkerdeelvenster op **Publiceren**. Deze actie publiceert de entiteiten die u hebt gemaakt met de Data Factory-service. 

    ![De knop Publiceren](./media/tutorial-incremental-copy-multiple-tables-portal/publish-button.png)
1. Wacht tot u het bericht **Gepubliceerd** ziet. Om de meldingen te zien, klikt u op de link **Meldingen weergeven**. Sluit het meldingenvenster door op **X** te klikken.

    ![Meldingen weergeven](./media/tutorial-incremental-copy-multiple-tables-portal/notifications.png)

 
## <a name="run-the-pipeline"></a>De pijplijn uitvoeren

1. Klik in de werkbalk voor de pijplijn op **Activeren** en klik vervolgens op **Nu activeren**.     

    ![Nu activeren](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. Voer in het venster **Pijplijn uitvoeren** de volgende waarde in voor de parameter **tableList** en klik op **Voltooien**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

    ![Pijplijn uitvoeren-argumenten](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>De pijplijn bewaken

1. Ga naar het tabblad **Controleren** aan de linkerkant. U kunt de status van de pijplijnuitvoering zien die is geactiveerd met de **handmatige trigger**. Klik op de knop **Vernieuwen** om de lijst te vernieuwen. Met de koppelingen in de kolom **Acties** kunt u de uitvoeringen van activiteiten weergeven die zijn gekoppeld aan de pijplijnuitvoering, en de pijplijn opnieuw uitvoeren. 

    ![Pijplijnuitvoeringen](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. Klik op de koppeling **Uitvoeringen van activiteiten weergeven** in de kolom **Acties**. U ziet de uitvoering van alle activiteiten die zijn gekoppeld aan de pijplijnuitvoering. 

    ![Uitvoering van activiteiten](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png)

## <a name="review-the-results"></a>De resultaten bekijken
Voer in SQL Server Management Studio de volgende query's uit op de SQL-doeldatabase om te controleren of de gegevens van de brontabellen naar de doeltabellen zijn gekopieerd: 

**Query** 
```sql
select * from customer_table
```

**Uitvoer**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Query**

```sql
select * from project_table
```

**Uitvoer**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Query**

```sql
select * from watermarktable
```

**Uitvoer**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

U ziet dat de bovengrenswaarden voor beide tabellen zijn bijgewerkt. 

## <a name="add-more-data-to-the-source-tables"></a>Meer gegevens toevoegen aan de brontabellen

Voer de volgende query uit op de SQL Server-brondatabase om een bestaande rij bij te werken in customer_table. Voeg een nieuwe rij in project_table in. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Voer de pijplijn uit
1. Schakel in het browservenster over naar het tabblad **Bewerken** aan de linkerkant. 
1. Klik in de werkbalk voor de pijplijn op **Activeren** en klik vervolgens op **Nu activeren**.   

    ![Nu activeren](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. Voer in het venster **Pijplijn uitvoeren** de volgende waarde in voor de parameter **tableList** en klik op **Voltooien**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline-again"></a>De pijplijn opnieuw controleren

1. Ga naar het tabblad **Controleren** aan de linkerkant. U kunt de status van de pijplijnuitvoering zien die is geactiveerd met de **handmatige trigger**. Klik op de knop **Vernieuwen** om de lijst te vernieuwen. Met de koppelingen in de kolom **Acties** kunt u de uitvoeringen van activiteiten weergeven die zijn gekoppeld aan de pijplijnuitvoering, en de pijplijn opnieuw uitvoeren. 

    ![Pijplijnuitvoeringen](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. Klik op de koppeling **Uitvoeringen van activiteiten weergeven** in de kolom **Acties**. U ziet de uitvoering van alle activiteiten die zijn gekoppeld aan de pijplijnuitvoering. 

    ![Uitvoering van activiteiten](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png) 

## <a name="review-the-final-results"></a>De eindresultaten bekijken
Voer in SQL Server Management Studio de volgende query's uit op de doeldatabase om te controleren dat de bijgewerkte/nieuwe gegevens van de brontabellen naar de doeltabellen zijn gekopieerd. 

**Query** 
```sql
select * from customer_table
```

**Uitvoer**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Let op de nieuwe waarden van **Name** en **LastModifytime** voor de **PersonID** voor nummer 3. 

**Query**

```sql
select * from project_table
```

**Uitvoer**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Let erop dat de invoer van **NewProject** toegevoegd is aan project_table. 

**Query**

```sql
select * from watermarktable
```

**Uitvoer**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

U ziet dat de bovengrenswaarden voor beide tabellen zijn bijgewerkt.
     
## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u de volgende stappen uitgevoerd: 

> [!div class="checklist"]
> * Bereid de bron- en doelserver gegevensopslag voor.
> * Een gegevensfactory maakt.
> * Een zelf-hostende integration runtime (IR) maken.
> * De Integration Runtime installeren.
> * Maak gekoppelde services. 
> * Maak bron-, sink- en grenswaardegegevenssets.
> * Maken, starten en controleren van een pijplijn.
> * Bekijk de resultaten.
> * Gegevens in brontabellen toevoegen of bijwerken.
> * De pijplijn opnieuw uitvoeren en controleren.
> * De eindresultaten bekijken.

Ga naar de volgende zelfstudie voor meer informatie over het transformeren van gegevens met behulp van een Spark-cluster in Azure:

> [!div class="nextstepaction"]
>[Incrementeel gegevens kopiëren van Azure SQL Database naar Azure Blob Storage met behulp van technologie voor het bijhouden van wijzigingen](tutorial-incremental-copy-change-tracking-feature-portal.md)


