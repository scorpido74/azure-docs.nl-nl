---
title: Bestaande gegevens migreren naar een Table-API-account in Azure Cosmos DB
description: Lees hoe u on-premises of cloudgegevens migreert of importeert naar Azure Table API-account in Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5c828644cb03d83df38265719cd8afabc24cf739
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "66242577"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Gegevens migreren naar een Azure Cosmos DB Table-API-account

Deze zelfstudie bevat instructies voor het importeren van gegevens voor gebruik met de Azure Cosmos DB [Table API.](table-introduction.md) Als u gegevens hebt opgeslagen in Azure Table Storage, kunt u het gegevensmigratieprogramma of AzCopy gebruiken om de gegevens te importeren in Azure Cosmos DB Table-API. Als u gegevens hebt opgeslagen in een Azure Cosmos DB Table-API-account (preview), moet u het gegevensmigratieprogramma gebruiken om uw gegevens te migreren. 

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Gegevens importeren met het gegevensmigratieprogramma
> * Gegevens importeren met AzCopy
> * Migreren vanuit Table-API (preview) naar Table-API 

## <a name="prerequisites"></a>Vereisten

* **Verhoog de doorvoer:** De duur van uw gegevensmigratie is afhankelijk van de hoeveelheid doorvoer die u hebt ingesteld voor een afzonderlijke container of een set containers. Verhoog de doorvoer voor grotere gegevensmigraties. Nadat u de migratie hebt voltooid, verlaagt u de doorvoer om kosten te besparen. Zie Prestatieniveaus en prijscategorieën in Azure Cosmos DB voor meer informatie over het verhogen van de doorvoer in Azure Portal.

* **Maak Azure Cosmos DB-resources:** voordat u gegevens gaat migreren, maakt u vooraf alle tabellen vanuit de Azure Portal. Als u migreert naar een Azure Cosmos DB-account dat doorvoer op databaseniveau heeft, moet u een partitiesleutel opgeven bij het maken van de Azure Cosmos DB-tabellen.

## <a name="data-migration-tool"></a>Gegevensmigratieprogramma

Het opdrachtregelprogramma voor Azure Cosmos DB-gegevensmigratie (dt.exe) kan worden gebruikt om uw bestaande Azure Table Storage-gegevens te importeren in een account voor een algemeen beschikbare Table-API of om gegevens te migreren van een Table-API-account (preview) naar een account voor een algemeen beschikbare Table-API. Andere bronnen worden momenteel niet ondersteund. Het gegevensmigratieprogramma dat is gebaseerd op een gebruikersinterface (dtui.exe) wordt momenteel niet ondersteund voor Table-API-accounts. 

Als u tabelgegevens wilt migreren, moet u de volgende taken uitvoeren:

1. Download het migratieprogramma op [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Voer `dt.exe` uit met de opdrachtregelargumenten voor uw scenario. `dt.exe` wordt uitgevoerd met een opdracht in de volgende indeling:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

Opties voor de opdracht zijn:

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All
    /EnableCosmosTableLog: Optional. Direct the log to a cosmos table account. If set, this defaults to destination account connection string unless /CosmosTableLogConnectionString is also provided. This is useful if multiple instances of DT are being run simultaneously.
    /CosmosTableLogConnectionString: Optional. ConnectionString to direct the log to a remote cosmos table account. 

### <a name="command-line-source-settings"></a>Broninstellingen voor de opdrachtregel

Gebruik de volgende bronopties bij het definiëren van Azure Table Storage of Table-API preview als de bron van de migratie.

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

Als u de tekenreeks voor bronverbinding wilt ophalen bij het importeren uit Azure Table-opslag, opent u de Azure-portal en klikt u op**Account** > **Accounttoegangssleutels** **voor opslagaccounts** > en gebruikt u de knop Kopiëren om de **verbindingstekenreeks**te kopiëren.

![Schermopname van HBase-bronopties](./media/table-import/storage-table-access-key.png)

Als u de tekenreeks voor bronverbinding wilt ophalen wanneer u importeert uit een Azure Cosmos DB Table API -account (preview), opent u de Azure-portal, > klikt u op Azure Cosmos**DB-accountverbindingstekenreeks** > **Connection String** en gebruikt u de kopieerknop om de **verbindingstekenreeks**te kopiëren. **Azure Cosmos DB**

![Schermopname van HBase-bronopties](./media/table-import/cosmos-connection-string.png)

[Voorbeeld van Azure Table Storage-opdracht](#azure-table-storage)

[Voorbeeld van opdracht voor Azure Cosmos DB Table-API (preview)](#table-api-preview)

### <a name="command-line-target-settings"></a>Doelinstellingen voor opdrachtregel

Gebruik de volgende doelopties bij het definiëren van de Azure Cosmos DB Table-API als het doel van de migratie.

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Voorbeeldopdracht: bron is Azure Table Storage

Hier ziet u een voorbeeld van een opdrachtregel voor het importeren vanuit Azure Table Storage in Table-API:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Voorbeeldopdracht: bron is Azure Cosmos DB Table-API (preview)

Hier ziet u een voorbeeld van een opdrachtregel voor het importeren vanuit Table-API preview in een algemeen beschikbare Table-API:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Gegevens migreren met AzCopy

U kunt ook met het AzCopy-opdrachtregelprogramma gegevens vanuit Azure Table Storage migreren naar de Azure Cosmos DB Table-API. Als u AzCopy wilt gebruiken, exporteert u eerst de gegevens zoals wordt beschreven in [Gegevens exporteren vanuit Azure Table Storage](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage) en importeert u de gegevens vervolgens in Azure Cosmos DB zoals wordt beschreven in [Azure Cosmos DB Table-API](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Bij het importeren in Azure Cosmos DB kunt u de volgende opdracht als voorbeeld nemen. Houd er rekening mee dat de waarde voor /Dest cosmosdb is en niet core.

Voorbeeld van opdracht voor importeren:

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migreren vanuit Table-API (preview) naar Table-API

> [!WARNING]
> Als u direct voordeel wilt hebben van de algemeen beschikbare tabellen, migreert u uw bestaande previewtabellen zoals in deze sectie is beschreven. Anders worden de tabellen de komende weken automatisch gemigreerd voor bestaande previewklanten. Houd er echter rekening mee dat er voor automatisch gemigreerde previewtabellen bepaalde beperkingen gelden die niet van toepassing zijn op onlangs gemaakte tabellen.
> 

De Table-API is nu algemeen beschikbaar. Er zijn verschillen tussen de previewversie en de algemeen beschikbare versie voor tabellen in de code die wordt uitgevoerd in de cloud en in de code die wordt uitgevoerd op de client. Daarom wordt het niet aangeraden om een preview SDK-client te combineren met een account voor een algemeen beschikbare Table-API. Als klanten van de Table-API preview de bestaande tabellen willen blijven gebruiken in een productieomgeving, moeten ze de tabellen vanuit de previewomgeving migreren naar de omgeving met een algemeen beschikbare Table-API of wachten totdat de tabellen automatisch worden gemigreerd. Als u wacht op de automatische migratie, krijgt u een melding over de beperkingen die voor de gemigreerde tabellen gelden. Na de migratie kunt u voor uw bestaande account nieuwe tabellen zonder beperkingen maken (alleen gemigreerde tabellen hebben beperkingen).

U migreert als volgt van Table-API (preview) naar de algemeen beschikbare Table-API:

1. Maak een nieuw Azure Cosmos DB-account en stel hiervoor het API-type in op Azure Table, zoals wordt beschreven in [Een databaseaccount maken](create-table-dotnet.md#create-a-database-account).

2. Zorg ervoor dat de clients gebruikmaken van een algemeen beschikbare release van de [Table-API-SDK's](table-sdk-dotnet.md).

3. Migreer de clientgegevens van de previewtabellen naar de algemeen beschikbare tabellen met behulp van het gegevensmigratieprogramma. Instructies voor het gebruik van het gegevensmigratieprogramma voor dit doeleinde worden beschreven in [Gegevensmigratieprogramma](#data-migration-tool). 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Gegevens importeren met het gegevensmigratieprogramma
> * Gegevens importeren met AzCopy
> * Migreren vanuit Table-API (preview) naar Table-API

U kunt nu verdergaan met de volgende zelfstudie om te leren hoe u query's uitvoert op gegevens met de Azure Cosmos DB Table-API. 

> [!div class="nextstepaction"]
>[Hoe kan ik query’s uitvoeren op gegevens?](../cosmos-db/tutorial-query-table.md)
