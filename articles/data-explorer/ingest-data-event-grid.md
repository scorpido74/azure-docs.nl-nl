---
title: Azure-blobs opnemen in Azure Data Explorer
description: In dit artikel vindt u informatie over het verzenden van opslagaccountgegevens naar Azure Data Explorer met behulp van een Abonnement op Event Grid.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ec218b1638183db463ff09488c988cad64d78c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370437"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Blobs opnemen in Azure Data Explorer door u te abonneren op meldingen over gebeurtenisraster

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-sjabloon](data-connection-event-grid-resource-manager.md)

Azure Data Explorer is een snelle en schaalbare gegevensverkenningsservice voor logboek- en telemetriegegevens. Het biedt continue inname (data laden) van blobs geschreven tot blob containers. 

In dit artikel leert u hoe u een [Azure Event Grid-abonnement](/azure/event-grid/overview) instelt en gebeurtenissen via een gebeurtenishub naar Azure Data Explorer routeert. Om te beginnen moet u een opslagaccount hebben met een gebeurtenisrasterabonnement dat meldingen verzendt naar Azure Event Hubs. Vervolgens maakt u een gegevensverbinding voor gebeurtenisrasters en ziet u de gegevensstroom door het hele systeem.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Maak een [gratis Azure-account](https://azure.microsoft.com/free/)aan .
* [Een cluster en database](create-cluster-database-portal.md).
* [Een opslagaccount](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Een gebeurtenishub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Een Event Grid-abonnement maken in uw opslagaccount

1. Zoek uw opslagaccount in de Azure-portal.
1. Selecteer abonnement op **evenementengebeurtenissen** > **Event Subscription**.

    ![Toepassingskoppeling voor query](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. Geef de volgende waarden op in het venster **Gebeurtenisabonnement maken** in het tabblad **Basic**:

    **Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**
    |---|---|---|
    | Name | *test-grid-connection* | De naam van het gebeurtenisraster dat u wilt maken.|
    | Gebeurtenisschema | *Event Grid-schema* | Het schema dat moet worden gebruikt voor het gebeurtenisraster. |
    | Onderwerptype | *Opslagaccount* | Het type Event Grid-onderwerp. |
    | Onderwerpresource | *gridteststorage* | De naam van uw opslagaccount. |
    | Abonneren op alle gebeurtenistypen | *Duidelijk* | Geen meldingen ontvangen voor alle gebeurtenissen. |
    | Gedefinieerde gebeurtenistypen | *BlobCreated* | Voor welke specifieke gebeurtenissen u een melding ontvangt. |
    | Eindpunttype | *Event hubs* | Het type eindpunt waarnaar u de gebeurtenissen verzendt. |
    | Eindpunt | *test-hub* | De Event Hub die u hebt gemaakt. |
    | | |

1. Selecteer het tabblad **Filters** als u bestanden uit een specifieke container wilt bijhouden. Stel de filters voor de meldingen als volgt in:
    * **Onderwerp begint met** veld is het *letterlijke* voorvoegsel van de blobcontainer. Als het toegepaste patroon *begintmet,* kan het meerdere containers overspannen. Er zijn geen jokertekens zijn toegestaan.
     Het veld *moet* als volgt zijn ingesteld: *`/blobServices/default/containers/`*[container-voorvoegsel]
    * Het veld **Onderwerp eindigt met** veld is het *letterlijke* achtervoegsel van de blob. Er zijn geen jokertekens zijn toegestaan.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Een doeltabel maken in Azure Data Explorer

Maak een tabel in Azure Data Explorer waarin gebeurtenishubs gegevens verzenden. Maak de tabel in het cluster en de database die is opgesteld in de vereiste voorwaarden.

1. Selecteer in de Azure-portal, onder het cluster, de optie **Query**.

    ![Toepassingskoppeling voor query](media/ingest-data-event-grid/query-explorer-link.png)

1. Kopieer de volgende opdracht naar het venster en selecteer **Uitvoeren** om de tabel (TestTable) te maken die de opgenomen gegevens ontvangt.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Maken van query uitvoeren](media/ingest-data-event-grid/run-create-table.png)

1. Kopieer de volgende opdracht in het venster en selecteer **Uitvoeren** om de binnenkomende JSON-gegevens toe te wijzen aan de kolomnamen en gegevenstypen van de tabel (TestTable).

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Een Event Grid-gegevensverbinding maken in Azure Data Explorer

Maak nu verbinding met het gebeurtenisraster vanuit Azure Data Explorer, zodat gegevens die naar de blobcontainer stromen, naar de testtabel worden gestreamd. 

1. Selecteer op de werkbalk de optie **Meldingen** om te controleren of de implementatie van de Event Hub is geslaagd.

1. Selecteer **Databases** > **TestDatabase**onder het cluster dat u hebt gemaakt.

    ![Testdatabase selecteren](media/ingest-data-event-grid/select-test-database.png)

1. Gegevensopname **toevoegen** > **Gegevensverbinding selecteren**.

    ![Gegevensopname](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Selecteer het verbindingstype: **Blob-opslag**.

1. Vul het formulier in met de volgende informatie en selecteer **Maken**.

    ![Event Hub-verbinding](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Gegevensbron:

    **Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**
    |---|---|---|
    | Naam van gegevensverbinding | *test-hub-connection* | De naam van de verbinding die u wilt maken in Azure Data Explorer.|
    | Abonnement van opslagaccount | Uw abonnements-id | Het abonnements-id waarin uw opslagaccount zich bevindt.|
    | Storage-account | *gridteststorage* | De naam van het opslagaccount dat u eerder hebt gemaakt.|
    | Event Grid | *test-grid-connection* | De naam van het gebeurtenisraster dat u hebt gemaakt. |
    | Event Hub-naam | *test-hub* | De gebeurtenishub die u hebt gemaakt. Dit veld wordt automatisch gevuld wanneer u een gebeurtenisraster kiest. |
    | Consumentengroep | *test-group* | De consumentengroep die is gedefinieerd in de gebeurtenishub die u hebt gemaakt. |
    | | |

    Doeltabel:

     **Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**
    |---|---|---|
    | Tabel | *TestTable* | De tabel die u hebt gemaakt in **TestDatabase**. |
    | Gegevensindeling | *Json* | Ondersteunde formaten zijn Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV, RAW en TXT. Ondersteunde compressieopties: Zip en GZip |
    | Toewijzen van kolommen | *TestMapping* | De toewijzing die u hebt gemaakt in **TestDatabase** en waarmee die binnenkomende JSON-gegevens worden toegewezen aan de kolomnamen en gegevenstypen van **TestTable**.|
    | | |
    
## <a name="generate-sample-data"></a>Voorbeeldgegevens genereren

Nu Azure Data Explorer en het opslagaccount zijn verbonden, u voorbeeldgegevens maken en uploaden naar de blob-opslag.

U werkt met een klein shellscript dat een paar eenvoudige Azure CLI-opdrachten opgeeft voor interactie met Azure Storage-resources. Met dit script wordt een nieuwe container in uw opslagaccount gemaakt, wordt een bestaand bestand (als blob) naar die container geüpload en worden de blobs in de container weergegeven. U [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) gebruiken om het script rechtstreeks in de portal uit te voeren.

Sla de gegevens op in een bestand en upload deze met dit script:

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
#!/bin/bash
### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> Om de beste innameprestaties te bereiken, moet de *niet-gecomprimeerde* grootte van de gecomprimeerde blobs die voor inname worden ingediend, worden gecommuniceerd. Omdat gebeurtenisrastermeldingen alleen basisgegevens bevatten, moet de groottegegevens expliciet worden gecommuniceerd. De informatie over niet-gecomprimeerde grootte `rawSizeBytes` kan worden verstrekt door de eigenschap op de blobmetagegevens in te stellen met de *niet-gecomprimeerde* gegevensgrootte in bytes.

### <a name="ingestion-properties"></a>Inname-eigenschappen

U de [inname-eigenschappen](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#ingestion-properties) van de blobopname opgeven via de blobmetagegevens.

Deze eigenschappen kunnen worden ingesteld:

|**Eigenschap** | **Beschrijving van de eigenschap**|
|---|---|
| `rawSizeBytes` | Grootte van de ruwe (niet gecomprimeerde) gegevens. Voor Avro/ORC/Parket is dit de grootte voordat formaatspecifieke compressie wordt toegepast.|
| `kustoTable` |  Naam van de bestaande doeltabel. Overschrijft `Table` de set `Data Connection` op het mes. |
| `kustoDataFormat` |  Gegevensindeling. Overschrijft `Data format` de set `Data Connection` op het mes. |
| `kustoIngestionMappingReference` |  Naam van de bestaande innametoewijzing die moet worden gebruikt. Overschrijft `Column mapping` de set `Data Connection` op het mes.|
| `kustoIgnoreFirstRecord` | Als kusto is ingesteld op `true`, negeert het de eerste rij van de blob. Gebruik in tabelindelinggegevens (CSV, TSV of iets dergelijks) om kopteksten te negeren. |
| `kustoExtentTags` | Tekenreeks die [tags](/azure/kusto/management/extents-overview#extent-tagging) vertegenwoordigt die in de resulterende mate worden bevestigd. |
| `kustoCreationTime` |  Overschrijft [$IngestionTime](/azure/kusto/query/ingestiontimefunction?pivots=azuredataexplorer) voor de blob, opgemaakt als iso 8601-tekenreeks. Gebruiken voor het bijvullen. |

> [!NOTE]
> Azure Data Explorer verwijdert de blobs na inname niet.
> Bewaar de blobs voor thrre tot vijf dagen.
> Gebruik [de levenscyclus van Azure Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) om blobverwijdering te beheren. 

## <a name="review-the-data-flow"></a>De gegevensstroom controleren

> [!NOTE]
> Azure Data Explorer heeft een aggregatiebeleid (batching) voor het innemen van gegevens die is ontworpen om het innameproces te optimaliseren.
Standaard is het beleid geconfigureerd op vijf minuten.
U het beleid op een later tijdstip wijzigen als dat nodig is. In dit artikel u een latentie van een paar minuten verwachten.

1. In de Azure-portal, onder uw Event Grid, ziet u de piek in activiteit terwijl de app wordt uitgevoerd.

    ![Event Grid-grafiek](media/ingest-data-event-grid/event-grid-graph.png)

1. Als u wilt controleren hoeveel berichten er op dat moment de database hebben bereikt, voert u de volgende query uit in de testdatabase.

    ```kusto
    TestTable
    | count
    ```

1. Voer de volgende query in uw testdatabase uit om de inhoud van de berichten te bekijken.

    ```kusto
    TestTable
    ```

    De resultatenset ziet er ongeveer als volgt uit.

    ![Berichtresultatenset](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet van plan bent de Event Grid opnieuw te gebruiken, wist u de **test-hub-rg** om te voorkomen dat er kosten in rekening worden gebracht.

1. Selecteer in Azure Portal **Resourcegroepen** aan de linkerkant en selecteer vervolgens de resourcegroep die u hebt gemaakt.  

    Wanneer het menu links is samengevouwen, klikt u op ![Knop Uitvouwen](media/ingest-data-event-grid/expand.png) om het menu uit te vouwen.

   ![Resourcegroep selecteren die moet worden verwijderd](media/ingest-data-event-grid/delete-resources-select.png)

1. Selecteer onder **test-resource-group** de optie **Resourcegroep verwijderen**.

1. Voer in het nieuwe venster de naam in van de brongroep om te verwijderen *(test-hub-rg)* en selecteer **Vervolgens Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* [Query's uitvoeren op gegevens in Azure Data Explorer](web-query-data.md)
