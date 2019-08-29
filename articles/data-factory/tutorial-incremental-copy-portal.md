---
title: Stapsgewijs een tabel kopiëren met behulp van Azure Data Factory | Microsoft Docs
description: In deze zelfstudie maakt u een Azure Data Factory-pijplijn waarmee gegevens uit een Azure SQL-database stapsgewijs worden gekopieerd naar Azure Blob Storage.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/11/2018
ms.author: yexu
ms.openlocfilehash: e9284d34d3c5bc51c7d0648b24877a051df423d9
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140708"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage"></a>Stapsgewijs gegevens uit een Azure SQL-database laden in Azure Blob Storage
In deze zelfstudie maakt u een Azure-gegevensfactory met een pijplijn waarmee deltagegevens uit een tabel in een Azure SQL-database worden geladen naar Azure Blob Storage. 

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Bereid de gegevensopslag voor om de grenswaarde in op te slaan.
> * Een data factory maken.
> * Maak gekoppelde services. 
> * Bron-, sink- en grenswaardegegevenssets maken.
> * Maak een pijplijn.
> * Voer de pijplijn uit.
> * De pijplijnuitvoering controleert. 
> * Resultaten controleren
> * Voeg meer gegevens toe aan de bron.
> * Voer de pijplijn opnieuw uit.
> * Controleer de tweede pijplijnuitvoering.
> * Bekijk de resultaten van de tweede uitvoering.


## <a name="overview"></a>Overzicht
Hier volgt de diagramoplossing op hoog niveau: 

![Stapsgewijs gegevens laden](media/tutorial-Incremental-copy-portal/incrementally-load.png)

Dit zijn de belangrijke stappen voor het maken van deze oplossing: 

1. **Selecteer de grenswaardekolom**.
    Selecteer één kolom in de brongegevensopslag die kan worden gebruikt om de nieuwe of bijgewerkte records voor elke uitvoering te segmenteren. Normaal gesproken nemen de gegevens in deze geselecteerde kolom (bijvoorbeeld, last_modify_time of id) toe wanneer de rijen worden gemaakt of bijgewerkt. De maximale waarde in deze kolom wordt gebruikt als grenswaarde.

2. **Bereid een gegevensopslag voor om de grenswaarde in op te slaan**. In deze zelfstudie slaat u de grenswaarde op in een SQL-database.
    
3. **Maak een pijplijn met de volgende werkstroom**: 
    
    De pijplijn in deze oplossing heeft de volgende activiteiten:
  
    * Maak twee opzoekactiviteiten. Gebruik de eerste opzoekactiviteit om de laatste grenswaarde op te halen. Gebruik de tweede opzoekactiviteit om de nieuwe grenswaarde op te halen. Deze grenswaarden worden doorgegeven aan de kopieeractiviteit. 
    * Maak een kopieeractiviteit waarmee rijen uit de brongegevensopslag worden gekopieerd met een waarde uit de grenswaardekolom die groter is dan de oude grenswaarde en kleiner dan de nieuwe grenswaarde. Vervolgens worden de deltagegevens uit de brongegevensopslag als een nieuw bestand gekopieerd naar een Blob-opslag. 
    * Maak een opgeslagen-procedureactiviteit waarmee de grenswaarde wordt bijgewerkt voor de pijplijn die de volgende keer wordt uitgevoerd. 


Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten
* **Azure SQL-database**. U gebruikt de database als de brongegevensopslag. Als u geen SQL-database hebt, raadpleegt u het artikel [Een Azure SQL-database maken](../sql-database/sql-database-get-started-portal.md) om een database te maken.
* **Azure Storage**. U gebruikt de Blob-opslag als de sinkgegevensopslag. Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-quickstart-create-account.md) om een account te maken. Maak een container met de naam adftutorial. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>Een gegevensbrontabel maken in uw SQL-database
1. Open SQL Server Management Studio. Klik in **Server Explorer** met de rechtermuisknop op de database en kies **Nieuwe query**.

2. Voer de volgende SQL-opdracht uit voor de SQL-database om een tabel met de naam `data_source_table` te maken als de gegevensbronopslag: 
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    In deze zelfstudie gebruikt u LastModifytime als de grenswaardekolom. De gegevens in de brongegevensopslag worden weergegeven in de volgende tabel:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Nog een tabel in uw SQL-database maken om de bovengrenswaarde op te slaan
1. Voer de volgende SQL-opdracht uit op de SQL-database om een tabel met de naam `watermarktable` te maken om de grenswaarde op te slaan:  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Stel de standaardwaarde van de bovengrens in met de tabelnaam van de brongegevensopslag. In deze zelfstudie is de tabelnaam data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Controleer de gegevens in de tabel `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Uitvoer: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Een opgeslagen procedure maken in uw SQL-database 

Voer de volgende opdracht uit om een opgeslagen procedure te maken in uw SQL-database:

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Data factory maken

1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
1. Selecteer in het menu links de optie **een resource** > maken**gegevens en analyses** > **Data Factory**: 
   
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Voer op de pagina **Nieuwe gegevensfactory** **ADFTutorialBulkCopyDF** in als de **naam**. 
      
     ![De pagina Nieuwe data factory](./media/tutorial-incremental-copy-portal/new-azure-data-factory.png)
 
   De naam van de Azure-gegevensfactory moet **wereldwijd uniek** zijn. Als u een rood uitroepteken ziet met het volgende foutbericht, wijzigt u de naam van de gegevensfactory (bijvoorbeeld uwnaamADFIncCopyTutorialDF) en probeert u het opnieuw. Zie het artikel [Data factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
  
       `Data factory name "ADFIncCopyTutorialDF" is not available`
3. Selecteer het Azure-**abonnement** waarin u de gegevensfactory wilt maken. 
4. Voer een van de volgende stappen uit voor de **Resourcegroep**:
     
      - Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst. 
      - Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in.   
         
        Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/resource-group-overview.md) voor meer informatie.  
4. Selecteer **V2** als de **versie**.
5. Selecteer de **locatie** voor de gegevensfactory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich in andere regio's bevinden.
6. Selecteer **Vastmaken aan dashboard**.     
7. Klik op **Create**.      
8. Op het dashboard ziet u de volgende tegel met de status: **Gegevensfactory implementeren**. 

    ![tegel met de status 'gegevensfactory implementeren'](media/tutorial-incremental-copy-portal/deploying-data-factory.png)
9. Na het aanmaken ziet u de pagina **Data Factory** zoals weergegeven in de afbeelding.
   
   ![Startpagina van de gegevensfactory](./media/tutorial-incremental-copy-portal/data-factory-home-page.png)
10. Klik op de tegel **Author & Monitor** om de gebruikersinterface (UI) van Azure Data Factory te openen in een afzonderlijk tabblad.

## <a name="create-a-pipeline"></a>Een pijplijn maken
In deze zelfstudie maakt u een pijplijn met twee opzoekactiviteiten, één kopieeractiviteit en één opgeslagen procedureactiviteit als keten in één pijplijn. 

1. Klik op de pagina **Aan de slag** van de Data Factory-UI op de tegel **Pijplijn maken**. 

   ![Pagina Aan de slag van de Data Factory-UI](./media/tutorial-incremental-copy-portal/get-started-page.png)    
3. Voer op de pagina **Algemeen** in het venster **Eigenschappen** voor de pijplijn de naam **IncrementalCopyPipeline** in. 

   ![Naam pijplijn](./media/tutorial-incremental-copy-portal/pipeline-name.png)
4. We gaan de eerste opzoekactiviteit gebruiken om de oudste grenswaarde op te halen. Vouw in de **Activiteiten**-werkset de optie **Algemeen** uit. Gebruik vervolgens slepen-en-neerzetten om de **opzoekactiviteit** te verplaatsen naar het ontwerpoppervlak voor pijplijnen. Wijzig de naam van de activiteit in **LookupOldWaterMarkActivity**.

   ![Eerst opzoekactiviteit - naam](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. Ga naar het tabblad **Instellingen** en klik op **+ Nieuw** voor **Brongegevensset**. In deze stap maakt u een gegevensset die de gegevens in de **grenswaardetabel** vertegenwoordigt. Deze tabel bevat de oude grenswaarde die is gebruikt in de vorige kopieerbewerking. 

   ![Menu Nieuwe gegevensset - oude grenswaarde](./media/tutorial-incremental-copy-portal/new-dataset-old-watermark.png)
6. Selecteer in het venster **Nieuwe gegevensset** de optie **Azure SQL-database** en klik op **Voltooien**. Er wordt nu een nieuw tabblad geopend voor de gegevensset. 

   ![Azure SQL-database selecteren](./media/tutorial-incremental-copy-portal/select-azure-sql-database-old-watermark.png)
7. Voer in het venster Eigenschappen voor de gegevensset **WatermarkDataset** in als **Naam**.

   ![Grenswaardegegevensset - naam](./media/tutorial-incremental-copy-portal/watermark-dataset-name.png)
8. Ga naar het tabblad **Verbinding** en klik op **+ Nieuw** om verbinding te maken met (een gekoppelde service te maken voor) uw Azure SQL-database. 

   ![Knop Nieuwe gekoppelde service](./media/tutorial-incremental-copy-portal/watermark-dataset-new-connection-button.png)
9. Voer in het venster **Nieuwe gekoppelde service** de volgende stappen uit:

    1. Voer **AzureSqlDatabaseLinkedService** in als **Naam**. 
    2. Selecteer uw Azure SQL-server als **Servernaam**
    3. Voer de **naam van de gebruiker** in voor toegang tot de Azure SQL-server. 
    4. Voer het **wachtwoord** voor de gebruiker in. 
    5. Als u de verbinding met de Azure SQL-database wilt testen, klikt u op **Verbinding testen**.
    6. Klik op **Opslaan**.
    7. Open het tabblad **Verbinding** en controleer of **AzureSqlDatabaseLinkedService** is geselecteerd als **Gekoppelde service**.
       
        ![Venster Nieuwe gekoppelde service](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
10. Selecteer **[dbo].[watermarktable]** als **Tabel**. Klik op **Gegevens vooraf bekijken** om een voorbeeld van de gegevens in de tabel te bekijken.

    ![Grenswaardegegevensset - verbindingsinstellingen](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
11. Ga naar de pijplijneditor door op het pijplijntabblad bovenaan te klikken of door in de structuurweergave aan de linkerkant op de naam van de pijplijn te klikken. Bevestig in het venster Eigenschappen voor de **opzoekactiviteit** dat **WatermarkDataset** is geselecteerd in het veld **Brongegevensset**. 

    ![Pijplijn - oude grenswaardegegevensset](./media/tutorial-incremental-copy-portal/pipeline-old-watermark-dataset-selected.png)
12. Vouw in de **Activiteiten**-werkset de optie **Algemeen** uit. Gebruik vervolgens slepen-en-neerzetten om nog een **opzoekactiviteit** te verplaatsen naar het ontwerpoppervlak voor pijplijnen. Stel de naam op het tabblad **Algemeen** in het venster Eigenschappen in op **LookupNewWaterMarkActivity**. Met deze opzoekactiviteit wordt de nieuwe grenswaarde opgehaald uit de tabel met de brongegevens die moeten worden gekopieerd naar de bestemming. 

    ![Tweede opzoekactiviteit - naam](./media/tutorial-incremental-copy-portal/second-lookup-activity-name.png)
13. Ga in het venster Eigenschappen voor de tweede **opzoekactiviteit** naar het tabblad **Instellingen** en klik op **Nieuw**. U maakt een gegevensset om te verwijzen naar de brontabel met de nieuwe grenswaarde (maximumwaarde van LastModifyTime). 

    ![Tweede opzoekactiviteit - nieuwe gegevensset](./media/tutorial-incremental-copy-portal/second-lookup-activity-settings-new-button.png)
14. Selecteer in het venster **Nieuwe gegevensset** de optie **Azure SQL-database** en klik op **Voltooien**. Er wordt nu een nieuw tabblad geopend voor deze gegevensset. Ook ziet u de gegevensset in de structuurweergave. 
15. Voer op het tabblad **Algemeen** in het venster Eigenschappen **SourceDataset** in als **Naam**. 

    ![Brongegevensset - naam](./media/tutorial-incremental-copy-portal/source-dataset-name.png)
16. Ga naar het tabblad **Verbinding** en voer de volgende stappen uit: 

    1. Selecteer **AzureSqlDatabaseLinkedService** als **Gekoppelde service**.
    2. Selecteer **[dbo].[data_source_table]** als Tabel. Verderop in de zelfstudie geeft u een query op voor deze gegevensset. De query heeft voorrang op de tabel die u in deze stap opgeeft. 

        ![Tweede opzoekactiviteit - nieuwe gegevensset](./media/tutorial-incremental-copy-portal/source-dataset-connection.png)
17. Ga naar de pijplijneditor door op het pijplijntabblad bovenaan te klikken of door in de structuurweergave aan de linkerkant op de naam van de pijplijn te klikken. Bevestig in het venster Eigenschappen voor de **opzoekactiviteit** dat **SourceDataset** is geselecteerd in het veld **Brongegevensset**. 
18. Selecteer **Query** in het veld **Query gebruiken** en voer de volgende query in: u selecteert alleen de maximumwaarde van **LastModifytime** uit **data_source_table**. Zorg ervoor dat u ook alleen de **eerste rij**hebt ingeschakeld.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![Tweede opzoekactiviteit - query](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. Vouw in de **Activiteiten**-werkset de optie **Gegevensstroom** uit. Gebruik vervolgens slepen-en-neerzetten om de **kopieeractiviteit** uit de Activiteiten-werkset te verplaatsen, en stel de naam in op **IncrementalCopyActivity**. 

    ![Kopieeractiviteit - naam](./media/tutorial-incremental-copy-portal/copy-activity-name.png)
20. **Verbind beide opzoekactiviteiten met de kopieeractiviteit** door de **groene knop** die is gekoppeld aan de opzoekactiviteiten, naar de kopieeractiviteit te slepen. Laat de muisknop los als u ziet dat de randkleur van de kopieeractiviteit is gewijzigd in blauw. 

    ![Opzoekactiviteiten verbinden met kopieeractiviteit](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. Selecteer de **kopieeractiviteit** en controleer of de eigenschappen voor de activiteit worden weergegeven in het venster **Eigenschappen**. 

    ![Eigenschappen van de kopieeractiviteit](./media/tutorial-incremental-copy-portal/back-to-copy-activity-properties.png)
22. Ga naar het tabblad **Bron** in het venster **Eigenschappen** en voer de volgende stappen uit:

    1. Selecteer **SourceDataset** in het veld **Brongegevensset**. 
    2. Selecteer **Query** in het veld **Query gebruiken**. 
    3. Voer de volgende SQL-query in het veld **Query** in. 

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```
    
        ![Kopieeractiviteit - bron](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. Ga naar het tabblad **Sink** en klik op **+ Nieuw** voor het veld **Sink-gegevensset**. 

    ![Knop Nieuwe gegevensset](./media/tutorial-incremental-copy-portal/new-sink-dataset-button.png)
24. In deze zelfstudie is het sink-gegevensexemplaar van het type Azure Blob-opslag. Selecteer daarom **Azure Blob-opslag** en klik in het venster **Nieuwe gegevensset** op **Voltooien**. 

    ![Azure Blob-opslag selecteren](./media/tutorial-incremental-copy-portal/select-azure-blob-storage.png)
25. Voer op het tabblad **Algemeen** in het venster Eigenschappen voor de gegevensset **SinkDataset** in als **Naam**. 

    ![Sink-gegevensset - naam](./media/tutorial-incremental-copy-portal/sink-dataset-name.png)
26. Ga naar het tabblad **Verbinding** en klik op **+ Nieuw**. In deze stap maakt u een verbinding (gekoppelde service) voor uw **Azure Blob-opslag**.

    ![Sink-gegevensset - nieuwe verbinding](./media/tutorial-incremental-copy-portal/sink-dataset-new-connection.png)
26. Voer in het venster **Nieuwe gekoppelde service** de volgende stappen uit: 

    1. Voer **AzureStorageLinkedService** in als **Naam**. 
    2. Selecteer uw Azure Storage-account bij **Storage account name**.
    3. Klik op **Opslaan**. 

        ![Gekoppelde Azure Storage-service - instellingen](./media/tutorial-incremental-copy-portal/azure-storage-linked-service-settings.png)
27. Voer op het tabblad **Verbinding** de volgende stappen uit:

    1. Controleer of **AzureStorageLinkedService** is geselecteerd als **Gekoppelde service**. 
    2. Voer **adftutorial/incrementalcopy** in als het **mapgedeelte** van het **bestandspad**. **adftutorial** is de naam van de blob-container en **incrementalcopy** is de naam van de map. In dit fragment wordt ervan uitgegaan dat u een blobcontainer hebt met de naam adftutorial in uw Blob-opslag. Maak de container als deze bestaat niet of stel deze in op de naam van een bestaande container. Als de uitvoermap **incrementalcopy** niet bestaat, wordt deze automatisch gemaakt in Azure Data Factory. U kunt ook de knop **Bladeren** voor het **bestandspad** gebruiken om naar een map in een blob-container te navigeren. .RunId, '.txt')`.
    3. Voer `@CONCAT('Incremental-', pipeline().RunId, '.txt')` in voor het **bestandsnaamgedeelte** van het **bestandspad**. De bestandsnaam wordt dynamisch gegenereerd met behulp van de expressie. Elke pijplijnuitvoering heeft een unieke id. De kopieeractiviteit gebruikt de run-id om de bestandsnaam te genereren. 

        ![Sink-gegevensset - verbindingsinstellingen](./media/tutorial-incremental-copy-portal/sink-dataset-connection-settings.png)
28. Ga naar de **pijplijneditor** door op het pijplijntabblad bovenaan te klikken of door in de structuurweergave aan de linkerkant op de naam van de pijplijn te klikken. 
29. Vouw in de **Activiteiten**-werkset de optie **Algemeen** uit. Gebruik vervolgens slepen-en-neerzetten om de **opgeslagen-procedureactiviteit** uit de **Activiteiten**-werkset te verplaatsen naar het ontwerpoppervlak voor pijplijnen. **Verbind** de groene uitvoer (geslaagd) van de **kopieeractiviteit** met de **opgeslagen-procedureactiviteit**. 
    
    ![Kopieeractiviteit - bron](./media/tutorial-incremental-copy-portal/connect-copy-to-stored-procedure-activity.png)
24. Selecteer **Opgeslagen procedureactiviteit** in de pijplijnontwerper en verander de naam ervan in **StoredProceduretoWriteWatermarkActivity**. 

    ![Opgeslagen-procedureactiviteit - naam](./media/tutorial-incremental-copy-portal/stored-procedure-activity-name.png)
25. Ga naar het tabblad **SQL-account** en selecteer *AzureSqlDatabaseLinkedService*\* als **Gekoppelde service**. 

    ![Opgeslagen-procedureactiviteit - SQL-account](./media/tutorial-incremental-copy-portal/sp-activity-sql-account-settings.png)
26. Ga naar het tabblad **Opgeslagen procedure** en voer de volgende stappen uit: 

    1. Selecteer **usp_write_watermark** als naam van de **opgeslagen procedure**. 
    2. Als u waarden wilt opgeven voor de opgeslagen-procedureparameters, klikt u op **Importparameter** en voert u de volgende waarden voor de parameters in: 

        | Name | Type | Value | 
        | ---- | ---- | ----- | 
        | LastModifiedtime | DateTime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | Tekenreeks | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

    ![Opgeslagen-procedureactiviteit - instellingen voor de opgeslagen procedure](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. Klik in de werkbalk op **Valideren** om de instellingen voor de pijplijn te valideren. Controleer of er geen validatiefouten zijn. Klik om >> om het venster **Pijplijnvalidatierapport** te sluiten.   

    ![Pijplijn valideren](./media/tutorial-incremental-copy-portal/validate-pipeline.png)
28. Publiceer entiteiten (gekoppelde services, gegevenssets en pijplijnen) naar de Azure Data Factory-service door op de knop **Alles publiceren** te klikken. Wacht tot u een bericht ziet waarin staat dat het publiceren is voltooid. 

    ![De knop Publiceren](./media/tutorial-incremental-copy-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>Een pijplijnuitvoering activeren
1. Klik in de werkbalk op **Activeren** en klik op **Nu activeren**. 

    ![Knop Nu activeren](./media/tutorial-incremental-copy-portal/trigger-now.png)
2. Selecteer in het venster **Pijplijnuitvoering** de optie **Voltooien**. 

## <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

1. Ga naar het tabblad **Controleren** aan de linkerkant. U kunt de status van de pijplijnuitvoering zien die is geactiveerd met de handmatige trigger. Klik op de knop **Vernieuwen** om de lijst te vernieuwen. 
    
    ![Pijplijnuitvoeringen](./media/tutorial-incremental-copy-portal/pipeline-runs.png)
2. Klik op de eerste koppeling (**Uitvoeringen van activiteit weergeven**) in de kolom **Acties** om de uitvoeringen van activiteit weer te geven die zijn gekoppeld aan deze pijplijnuitvoering. Als u wilt terugkeren naar de vorige weergave, klikt u bovenaan op **Pijplijnen**. Klik op de knop **Vernieuwen** om de lijst te vernieuwen.

    ![Uitvoeringen van activiteit](./media/tutorial-incremental-copy-portal/activity-runs.png)

## <a name="review-the-results"></a>De resultaten bekijken
1. Maak verbinding met uw Azure Storage-account met behulp van hulpprogramma's zoals [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Controleer of er een uitvoerbestand is gemaakt in de map **incrementalcopy** in de container **adftutorial**.

    ![Eerste uitvoerbestand](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. Als u het uitvoerbestand opent, ziet u dat alle gegevens uit **data_source_table** zijn gekopieerd naar het blob-bestand. 

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. Controleer de laatste waarde van `watermarktable`. U ziet dat de grenswaarde is bijgewerkt.

    ```sql
    Select * from watermarktable
    ```
    
    Dit is de uitvoer:
 
    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 | 

## <a name="add-more-data-to-source"></a>Meer gegevens toevoegen aan de bron

Voeg nieuwe gegevens in de SQL-database (brongegevensopslag) in.

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
``` 

De bijgewerkte gegevens in de SQL-database zijn:

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```


## <a name="trigger-another-pipeline-run"></a>Een andere pijplijnuitvoering activeren
1. Ga naar het tabblad **Bewerken**. Klik op de pijplijn in de structuurweergave als deze niet is geopend in de ontwerper. 

    ![Knop Nu activeren](./media/tutorial-incremental-copy-portal/edit-tab.png)
2. Klik in de werkbalk op **Activeren** en klik op **Nu activeren**. 

    ![Knop Nu activeren](./media/tutorial-incremental-copy-portal/trigger-now.png)

## <a name="monitor-the-second-pipeline-run"></a>Controleer de tweede pijplijnuitvoering.

1. Ga naar het tabblad **Controleren** aan de linkerkant. U kunt de status van de pijplijnuitvoering zien die is geactiveerd met de handmatige trigger. Klik op de knop **Vernieuwen** om de lijst te vernieuwen. 
    
    ![Pijplijnuitvoeringen](./media/tutorial-incremental-copy-portal/pipeline-runs-2.png)
2. Klik op de eerste koppeling (**Uitvoeringen van activiteit weergeven**) in de kolom **Acties** om de uitvoeringen van activiteit weer te geven die zijn gekoppeld aan deze pijplijnuitvoering. Als u wilt terugkeren naar de vorige weergave, klikt u bovenaan op **Pijplijnen**. Klik op de knop **Vernieuwen** om de lijst te vernieuwen.

    ![Uitvoeringen van activiteit](./media/tutorial-incremental-copy-portal/activity-runs-2.png)

## <a name="verify-the-second-output"></a>De tweede uitvoer controleren

1. In de Blob-opslag ziet u dat een ander bestand is gemaakt. In deze zelfstudie is de nieuwe bestandsnaam `Incremental-<GUID>.txt`. Als u dit bestand opent, ziet u twee rijen met records.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. Controleer de laatste waarde van `watermarktable`. U ziet dat de grenswaarde opnieuw is bijgewerkt.

    ```sql
    Select * from watermarktable
    ```
    voorbeelduitvoer: 
    
    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |


     
## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u de volgende stappen uitgevoerd: 

> [!div class="checklist"]
> * Bereid de gegevensopslag voor om de grenswaarde in op te slaan.
> * Een data factory maken.
> * Maak gekoppelde services. 
> * Bron-, sink- en grenswaardegegevenssets maken.
> * Maak een pijplijn.
> * Voer de pijplijn uit.
> * De pijplijnuitvoering controleert. 
> * Resultaten controleren
> * Voeg meer gegevens toe aan de bron.
> * Voer de pijplijn opnieuw uit.
> * Controleer de tweede pijplijnuitvoering.
> * Bekijk de resultaten van de tweede uitvoering.

In deze zelfstudie heeft de pijplijn gegevens uit één tabel in een SQL-database naar een Blob-opslag gekopieerd. Ga door naar de volgende zelfstudie voor meer informatie over het kopiëren van gegevens uit meerdere tabellen in een on-premises SQL Server-database naar een SQL-database. 

> [!div class="nextstepaction"]
>[Incrementeel gegevens uit meerdere tabellen in SQL Server naar een Azure SQL Database kopiëren](tutorial-incremental-copy-multiple-tables-portal.md)



