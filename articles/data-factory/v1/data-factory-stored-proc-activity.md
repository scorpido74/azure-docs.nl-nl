---
title: SQL Server-procedureactiviteit opgeslagen
description: Lees hoe u de SQL Server Stored Procedure Activity gebruiken om een opgeslagen procedure in een Azure SQL Database of Azure SQL Data Warehouse aan te roepen vanuit een Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: 45aa49de51f42b26c653b15e79c865e3f5647c39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931632"
---
# <a name="sql-server-stored-procedure-activity"></a>SQL Server-procedureactiviteit opgeslagen
> [!div class="op_single_selector" title1="Transformatieactiviteiten"]
> * [Hive Activiteit](data-factory-hive-activity.md)
> * [Varkensactiviteit](data-factory-pig-activity.md)
> * [Activiteit kaart verminderen](data-factory-map-reduce.md)
> * [Hadoop streaming activiteit](data-factory-hadoop-streaming-activity.md)
> * [Vonkactiviteit](data-factory-spark.md)
> * [Machine Learning-batchuitvoeringsactiviteit](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-activiteit resources bijwerken](data-factory-azure-ml-update-resource-activity.md)
> * [Opgeslagen procedureactiviteit](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-activiteit](data-factory-usql-activity.md)
> * [.NET Aangepaste activiteit](data-factory-use-custom-activities.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory. Als u de huidige versie van de datafabriekservice gebruikt, raadpleegt u [gegevens transformeren met behulp van opgeslagen procedureactiviteit in Gegevensfabriek](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Overzicht
U gebruikt gegevenstransformatieactiviteiten in een Data [Factory-pijplijn](data-factory-create-pipelines.md) om ruwe gegevens om te zetten en te verwerken in voorspellingen en inzichten. De activiteit Opgeslagen procedure is een van de transformatieactiviteiten die Data Factory ondersteunt. Dit artikel bouwt voort op het artikel [over gegevenstransformatieactiviteiten,](data-factory-data-transformation-activities.md) dat een algemeen overzicht geeft van gegevenstransformatie en de ondersteunde transformatieactiviteiten in Data Factory.

U de opgeslagen procedureactiviteit gebruiken om een opgeslagen procedure aan te roepen in een van de volgende gegevensarchieven in uw onderneming of op een virtuele Azure-machine (VM):

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server-database. Als u SQL Server gebruikt, installeert u Data Management Gateway op dezelfde machine die de database host of op een aparte machine die toegang heeft tot de database. Data Management Gateway is een onderdeel dat on-premises/on Azure VM koppelt aan cloudservices op een veilige en beheerde manier. Zie [artikel Data Management Gateway](data-factory-data-management-gateway.md) voor meer informatie.

> [!IMPORTANT]
> Wanneer u gegevens kopieert naar Azure SQL Database of SQL Server, u de **SqlSink** in kopieeractiviteit configureren om een opgeslagen procedure aan te roepen met behulp van de eigenschap **sqlWriterStoredProcedureName.** Zie [Opgeslagen procedure van kopieeractiviteit aanroepen voor](data-factory-invoke-stored-procedure-from-copy-activity.md)meer informatie . Zie volgende verbindingsartikelen: Azure SQL [Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties)voor meer informatie over de eigenschap. Een beroep doen op een opgeslagen procedure terwijl gegevens naar een Azure SQL Data Warehouse worden gekopieerd met behulp van een kopieeractiviteit, wordt niet ondersteund. U de opgeslagen procedureactiviteit echter gebruiken om een opgeslagen procedure in een SQL Data Warehouse aan te roepen.
>
> Wanneer u gegevens kopieert uit Azure SQL Database of SQL Server of Azure SQL Data Warehouse, u **SqlSource** in kopieeractiviteit configureren om een opgeslagen procedure aan te roepen om gegevens uit de brondatabase te lezen met behulp van de eigenschap **sqlReaderStoredProcedureName.** Zie voor meer informatie de volgende verbindingsartikelen: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties), Azure SQL [Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

De volgende walkthrough gebruikt de opgeslagen procedureactiviteit in een pijplijn om een opgeslagen procedure in een Azure SQL-database aan te roepen.

## <a name="walkthrough"></a>Kennismaking
### <a name="sample-table-and-stored-procedure"></a>Voorbeeldtabel en opgeslagen procedure
1. Maak de volgende **tabel** in uw Azure SQL Database met SQL Server Management Studio of een ander hulpmiddel waarmee u zich bevindt. De kolom datetimestamp is de datum en het tijdstip waarop de bijbehorende ID wordt gegenereerd.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    Id is de unieke geïdentificeerde en de datumtijdstempel kolom is de datum en het tijdstip waarop de bijbehorende ID wordt gegenereerd.
    
    ![Voorbeeldgegevens](./media/data-factory-stored-proc-activity/sample-data.png)

    In dit voorbeeld bevindt de opgeslagen procedure zich in een Azure SQL-database. Als de opgeslagen procedure zich in een Azure SQL Data Warehouse en SQL Server Database bevindt, is de aanpak vergelijkbaar. Voor een SQL Server-database moet u een [Data Management Gateway](data-factory-data-management-gateway.md)installeren.
2. Maak de volgende **opgeslagen procedure** waarmee gegevens in de **voorbeeldtabel worden ingevoegd.**

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **De naam** en **de omhulsel** van de parameter (DateTime in dit voorbeeld) moeten overeenkomen met die van de parameter die is opgegeven in de JSON voor pijplijn/activiteit. Zorg er in de **\@** definitie van de opgeslagen procedure voor dat dit wordt gebruikt als voorvoegsel voor de parameter.

### <a name="create-a-data-factory"></a>Een gegevensfactory maken
1. Log in bij [Azure portal](https://portal.azure.com/).
2. Klik op **NIEUW** in het linkermenu, klik op **Intelligence + Analytics**en klik op **Gegevensfabriek**.

    ![Nieuwe data factory](media/data-factory-stored-proc-activity/new-data-factory.png)
3. Voer in het nieuwe blad van de **gegevensfabriek** **SProcDF** in voor de naam. Azure Data Factory-namen zijn **wereldwijd uniek.** U moet de naam van de gegevensfabriek vooraf met uw naam bevestigen, om de succesvolle creatie van de fabriek mogelijk te maken.

   ![Nieuwe data factory](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Selecteer uw **Azure-abonnement**.
5. Ga **voor Resourcegroep**naar een van de volgende stappen:
   1. Klik **op Nieuw maken** en voer een naam in voor de resourcegroep.
   2. Klik **op Bestaand gebruiken** en selecteer een bestaande resourcegroep.
6. Selecteer de **locatie** voor de gegevensfactory.
7. Selecteer **Vastmaken aan dashboard,** zodat u de gegevensfabriek op het dashboard de volgende keer dat u zich aanmeldt, zien.
8. Klik op de blade **Nieuwe gegevensfactory** op **Maken**.
9. U ziet de gegevensfabriek die wordt gemaakt in het **dashboard** van de Azure-portal. Nadat de gegevensfactory is gemaakt, ziet u de gegevensfactorypagina. Hierop wordt de inhoud van de gegevensfactory weergegeven.

   ![Startpagina gegevensfabriek](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Een Azure SQL-gekoppelde service maken
Nadat u de gegevensfabriek hebt gemaakt, maakt u een Azure SQL-gekoppelde service die uw Azure SQL-database, die de voorbeeldtabel bevat en usp_sample opgeslagen procedure, koppelt aan uw gegevensfabriek.

1. Klik op **Auteur en implementeren** op het **datafabriekblad** voor **SProcDF** om de Data Factory Editor te starten.
2. Klik op **Nieuw gegevensarchief** op de opdrachtbalk en kies **Azure SQL Database**. U ziet het JSON-script voor het maken van een Azure SQL-gekoppelde service in de editor.

   ![Nieuw gegevensarchief](media/data-factory-stored-proc-activity/new-data-store.png)
3. Breng in het JSON-script de volgende wijzigingen aan:

   1. Vervang `<servername>` de naam van uw Azure SQL Database-server.
   2. Vervang `<databasename>` de database waarin u de tabel hebt gemaakt en de opgeslagen procedure.
   3. Vervang `<username@servername>` door het gebruikersaccount dat toegang heeft tot de database.
   4. Vervang `<password>` door het wachtwoord voor het gebruikersaccount.

      ![Nieuw gegevensarchief](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Als u de gekoppelde service wilt implementeren, klikt u op **Implementeren** op de opdrachtbalk. Controleer of u de AzureSqlLinkedService ziet in de structuurweergave aan de linkerkant.

    ![boomweergave met gekoppelde service](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Een uitvoergegevensset maken
U moet een uitvoergegevensset opgeven voor een opgeslagen procedureactiviteit, zelfs als de opgeslagen procedure geen gegevens oplevert. Dat komt omdat het de uitvoergegevensset is die het schema van de activiteit aanstuurt (hoe vaak de activiteit wordt uitgevoerd - per uur, dagelijks, enz.). De uitvoergegevensset moet een **gekoppelde service** gebruiken die verwijst naar een Azure SQL Database of een Azure SQL Data Warehouse of een SQL Server Database waarin u de opgeslagen procedure wilt uitvoeren. De uitvoergegevensset kan dienen als een manier om het resultaat van de opgeslagen procedure voor latere verwerking door een andere activiteit[(chaining-activiteiten](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) in de pijplijn) door te geven. Data Factory schrijft echter niet automatisch de uitvoer van een opgeslagen procedure naar deze gegevensset. Het is de opgeslagen procedure die naar een SQL-tabel schrijft waarnaar de uitvoerset verwijst. In sommige gevallen kan de uitvoergegevensset een **dummy-gegevensset** zijn (een gegevensset die verwijst naar een tabel die niet echt de uitvoer van de opgeslagen procedure vasthoudt). Deze gegevensset dummy wordt alleen gebruikt om het schema voor het uitvoeren van de opgeslagen procedureactiviteit op te geven.

1. Klik **op ... Klik** meer op de werkbalk op **Nieuwe gegevensset**en klik op **Azure SQL**. **Nieuwe gegevensset** op de opdrachtbalk en selecteer **Azure SQL**.

    ![boomweergave met gekoppelde service](media/data-factory-stored-proc-activity/new-dataset.png)
2. Kopieer/plak het volgende JSON-script in de JSON-editor.

    ```JSON
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Als u de gegevensset wilt implementeren, klikt u op **Implementeren** op de opdrachtbalk. Controleer of u de gegevensset in de structuurweergave ziet.

    ![boomweergave met gekoppelde services](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Een pijplijn maken met SqlServerStoredProcedure-activiteit
Laten we nu een pijplijn maken met een opgeslagen procedureactiviteit.

Let op de volgende eigenschappen:

- De **eigenschap type** is ingesteld op **SqlServerStoredProcedure**.
- De **eigenschappen van De opgeslagenProcedureName** in type is ingesteld op **usp_sample** (naam van de opgeslagen procedure).
- De sectie **storedProcedureParameters** bevat één parameter met de naam **DateTime**. Naam en omhulsel van de parameter in JSON moeten overeenkomen met de naam en de behuizing van de parameter in de definitie van de opgeslagen procedure. Als u null voor een parameter moet `"param1": null` doorgeven, gebruikt u de syntaxis: (alle kleine letters).

1. Klik **op ... Meer** op de opdrachtbalk en klik op **Nieuwe pijplijn**.
2. Kopieer/plak het volgende JSON-fragment:

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "usp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
            "start": "2017-04-02T00:00:00Z",
            "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. Als u de pijplijn wilt implementeren, klikt u op **Implementeren** op de werkbalk.

### <a name="monitor-the-pipeline"></a>De pijplijn bewaken
1. Klik op **X** om de blades van de Data Factory-editor te sluiten en om terug te keren naar de Data Factory-blade. Klik op **Diagram**.

    ![diagramtegel](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. In de **diagramweergave**ziet u een overzicht van de pijplijnen en gegevenssets die in deze zelfstudie worden gebruikt.

    ![diagramtegel](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. Dubbelklik in de diagramweergave op `sprocsampleout`de gegevensset . U ziet de segmenten in de status Klaar. Er moeten vijf segmenten zijn omdat er een segment wordt geproduceerd voor elk uur tussen de begintijd en de eindtijd van de JSON.

    ![diagramtegel](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Wanneer een segment in **de** `select * from sampletable` status Ready staat, voert u een query uit tegen de Azure SQL-database om te controleren of de gegevens in de tabel zijn ingevoegd volgens de opgeslagen procedure.

   ![Uitvoergegevens](./media/data-factory-stored-proc-activity/output.png)

   Zie [De pijplijn controleren](data-factory-monitor-manage-pipelines.md) voor gedetailleerde informatie over het bewaken van Azure Data Factory-pijplijnen.

## <a name="specify-an-input-dataset"></a>Een invoergegevensset opgeven
In de walkthrough heeft opgeslagen procedureactiviteit geen invoergegevenssets. Als u een invoergegevensset opgeeft, wordt de opgeslagen procedureactiviteit pas uitgevoerd nadat het segment van de invoergegevensset beschikbaar is (in de status Gereed). De gegevensset kan een externe gegevensset zijn (die niet wordt geproduceerd door een andere activiteit in dezelfde pijplijn) of een interne gegevensset die wordt geproduceerd door een upstreamactiviteit (de activiteit die vóór deze activiteit wordt uitgevoerd). U meerdere invoergegevenssets opgeven voor de opgeslagen procedureactiviteit. Als u dit doet, wordt de opgeslagen procedureactiviteit alleen uitgevoerd wanneer alle segmenten van de invoergegevensset beschikbaar zijn (in de status Gereed). De invoergegevensset kan niet worden verbruikt in de opgeslagen procedure als parameter. Het wordt alleen gebruikt om de afhankelijkheid te controleren voordat de opgeslagen procedureactiviteit wordt gestart.

## <a name="chaining-with-other-activities"></a>Ketenen met andere activiteiten
Als u een upstreamactiviteit aan deze activiteit wilt ketenen, geeft u de output van de upstreamactiviteit op als input van deze activiteit. Wanneer u dit doet, wordt de opgeslagen procedureactiviteit pas uitgevoerd nadat de upstreamactiviteit is voltooid en de uitvoergegevensset van de upstreamactiviteit beschikbaar is (in De status Voltooid). U uitvoergegevenssets van meerdere upstream-activiteiten opgeven als invoergegevenssets van de opgeslagen procedureactiviteit. Wanneer u dit doet, wordt de opgeslagen procedureactiviteit alleen uitgevoerd wanneer alle segmenten van de invoergegevensset beschikbaar zijn.

In het volgende voorbeeld is de uitvoer van de kopieeractiviteit: OutputDataset, een invoer van de opgeslagen procedureactiviteit. Daarom wordt de opgeslagen procedureactiviteit pas uitgevoerd nadat de kopieeractiviteit is voltooid en het slice OutputDataset beschikbaar is (in De status Gereed). Als u meerdere invoergegevenssets opgeeft, wordt de opgeslagen procedureactiviteit pas uitgevoerd als alle segmenten van de invoergegevensset beschikbaar zijn (in de status Gereed). De invoergegevenssets kunnen niet direct worden gebruikt als parameters voor de opgeslagen procedureactiviteit.

Zie [meerdere activiteiten in een pijplijn](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline) voor meer informatie over ketenactiviteiten

```json
{
    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }
        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

Om de winkelprocedureactiviteit te koppelen aan **downstreamactiviteiten** (de activiteiten die worden uitgevoerd nadat de opgeslagen procedureactiviteit is voltooid), moet ook de uitvoergegevensset van de opgeslagen procedureactiviteit worden opgegeven als input van de downstreamactiviteit in de pijplijn.

> [!IMPORTANT]
> Wanneer u gegevens kopieert naar Azure SQL Database of SQL Server, u de **SqlSink** in kopieeractiviteit configureren om een opgeslagen procedure aan te roepen met behulp van de eigenschap **sqlWriterStoredProcedureName.** Zie [Opgeslagen procedure van kopieeractiviteit aanroepen voor](data-factory-invoke-stored-procedure-from-copy-activity.md)meer informatie . Zie de volgende verbindingsartikelen: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties)voor meer informatie over de eigenschap.
> 
> Wanneer u gegevens kopieert uit Azure SQL Database of SQL Server of Azure SQL Data Warehouse, u **SqlSource** in kopieeractiviteit configureren om een opgeslagen procedure aan te roepen om gegevens uit de brondatabase te lezen met behulp van de eigenschap **sqlReaderStoredProcedureName.** Zie voor meer informatie de volgende verbindingsartikelen: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties), Azure SQL [Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>JSON-indeling
Hier is de JSON-indeling voor het definiëren van een activiteit opgeslagen procedure:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs": [ { "name": "inputtable" } ],
    "outputs": [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":
        {
            "param1": "param1Value"
            …
        }
    }
}
```

In de volgende tabel worden de volgende JSON-eigenschappen beschreven:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| name | Naam van de activiteit |Ja |
| description |Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt |Nee |
| type | Moet zijn ingesteld op: **SqlServerStoredProcedure** | Ja |
| Ingangen | Optioneel. Als u een invoergegevensset opgeeft, moet deze beschikbaar zijn (in de status 'Klaar') om de opgeslagen procedureactiviteit uit te voeren. De invoergegevensset kan niet worden verbruikt in de opgeslagen procedure als parameter. Het wordt alleen gebruikt om de afhankelijkheid te controleren voordat de opgeslagen procedureactiviteit wordt gestart. |Nee |
| Uitgangen | U moet een uitvoergegevensset opgeven voor een opgeslagen procedureactiviteit. Uitvoergegevensset geeft het **schema** voor de opgeslagen procedureactiviteit (per uur, per week, maandelijks, enz.) aan. <br/><br/>De uitvoergegevensset moet een **gekoppelde service** gebruiken die verwijst naar een Azure SQL Database of een Azure SQL Data Warehouse of een SQL Server Database waarin u de opgeslagen procedure wilt uitvoeren. <br/><br/>De uitvoergegevensset kan dienen als een manier om het resultaat van de opgeslagen procedure voor latere verwerking door een andere activiteit[(chaining-activiteiten](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) in de pijplijn) door te geven. Data Factory schrijft echter niet automatisch de uitvoer van een opgeslagen procedure naar deze gegevensset. Het is de opgeslagen procedure die naar een SQL-tabel schrijft waarnaar de uitvoerset verwijst. <br/><br/>In sommige gevallen kan de uitvoergegevensset een **dummy-gegevensset**zijn, die alleen wordt gebruikt om het schema voor het uitvoeren van de opgeslagen procedureactiviteit op te geven. |Ja |
| storedProcedureName |Geef de naam op van de opgeslagen procedure in de Azure SQL-database of Azure SQL Data Warehouse of SQL Server-database die wordt vertegenwoordigd door de gekoppelde service die de uitvoertabel gebruikt. |Ja |
| storedProcedureParameters |Waarden opgeven voor opgeslagen procedureparameters. Als u null voor een parameter moet doorgeven, gebruikt u de syntaxis: "param1": null (alle kleine letters). Zie het volgende voorbeeld voor meer informatie over het gebruik van deze eigenschap. |Nee |

## <a name="passing-a-static-value"></a>Een statische waarde doorgeven
Laten we nu eens overwegen om een andere kolom met de naam 'Scenario' toe te voegen aan de tabel met een statische waarde genaamd 'Documentsample'.

![Voorbeeldgegevens 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tabel:**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Opgeslagen procedure:**

```SQL
CREATE PROCEDURE usp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Geef nu de parameter **Scenario** en de waarde van de opgeslagen procedureactiviteit door. De sectie **typeEigenschappen** in het voorgaande voorbeeld ziet eruit als het volgende fragment:

```JSON
"typeProperties":
{
    "storedProcedureName": "usp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Gegevensfabriek:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline datafactory**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```
