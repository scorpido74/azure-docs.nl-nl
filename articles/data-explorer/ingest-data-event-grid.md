---
title: Azure-blobs opnemen in Azure Data Explorer
description: In dit artikel leert u hoe u gegevens van een opslag account kunt verzenden naar Azure Data Explorer met behulp van een Event Grid-abonnement.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 47870410741cf96e289014fab5a9c2eab26759b1
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096416"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Blobs opnemen in azure Data Explorer door zich te abonneren op Event Grid meldingen

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-sjabloon](data-connection-event-grid-resource-manager.md)

Azure Data Explorer is een snelle en schaal bare service voor gegevens exploratie voor logboek-en telemetriegegevens. Het biedt doorlopende opname (gegevens laden) van blobs die zijn geschreven naar BLOB-containers. 

In dit artikel leert u hoe u een [Azure Event grid](/azure/event-grid/overview) -abonnement kunt instellen en hoe u via een event hub gebeurtenissen naar Azure Data Explorer kunt door sturen. Als u wilt beginnen, moet u een opslag account hebben met een event grid-abonnement dat meldingen verzendt naar Azure Event Hubs. Vervolgens maakt u een Event Grid gegevens verbinding en ziet u de gegevens stroom in het hele systeem.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Maak een [gratis Azure-account](https://azure.microsoft.com/free/).
* [Een cluster en data base](create-cluster-database-portal.md).
* [Een opslag account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Een event hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Een Event Grid-abonnement maken in uw opslagaccount

1. Zoek uw opslag account in het Azure Portal.
1. Selecteer **gebeurtenissen** > **gebeurtenis abonnement**.

    ![Toepassingskoppeling voor query](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. Geef de volgende waarden op in het venster **Gebeurtenisabonnement maken** in het tabblad **Basic**:

    **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    |---|---|---|
    | Naam | *test-grid-connection* | De naam van het gebeurtenis raster dat u wilt maken.|
    | Gebeurtenisschema | *Event Grid schema* | Het schema dat moet worden gebruikt voor het event grid. |
    | Onderwerptype | *Opslagaccount* | Het type Event Grid-onderwerp. |
    | Onderwerpresource | *gridteststorage* | De naam van uw opslagaccount. |
    | Abonneren op alle gebeurtenistypen | *Maak* | Geen meldingen ontvangen voor alle gebeurtenissen. |
    | Gedefinieerde gebeurtenistypen | *BLOB gemaakt* | Voor welke specifieke gebeurtenissen u een melding ontvangt. |
    | Eindpunttype | *Event hubs* | Het type eindpunt waarnaar u de gebeurtenissen verzendt. |
    | Eindpunt | *test-hub* | De Event Hub die u hebt gemaakt. |
    | | |

1. Selecteer het tabblad **filters** als u bestanden van een bepaalde container wilt bijhouden. Stel de filters voor de meldingen als volgt in:
    * **Onderwerp begint met** veld is het *letterlijke* voor voegsel van de BLOB-container. Wanneer het toegepaste patroon *startsWith*is, kan het meerdere containers omvatten. Er zijn geen jokertekens zijn toegestaan.
     Het veld *moet* als volgt zijn ingesteld: *`/blobServices/default/containers/`* [container-voorvoegsel]
    * Het veld **Onderwerp eindigt met** veld is het *letterlijke* achtervoegsel van de blob. Er zijn geen jokertekens zijn toegestaan.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Een doeltabel maken in Azure Data Explorer

Een tabel maken in azure Data Explorer waar Event Hubs gegevens verzendt. Maak de tabel in het cluster en de data base die is voor bereid in de vereisten.

1. Selecteer in de Azure-portal, onder het cluster, de optie **Query**.

    ![Toepassingskoppeling voor query](media/ingest-data-event-grid/query-explorer-link.png)

1. Kopieer de volgende opdracht in het venster en selecteer **uitvoeren** om de tabel (TestTable) te maken waarin de opgenomen gegevens worden ontvangen.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Maken van query uitvoeren](media/ingest-data-event-grid/run-create-table.png)

1. Kopieer de volgende opdracht in het venster en selecteer **Uitvoeren** om de binnenkomende JSON-gegevens toe te wijzen aan de kolomnamen en gegevenstypen van de tabel (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Een Event Grid-gegevensverbinding maken in Azure Data Explorer

Maak nu verbinding met de Event Grid vanuit Azure Data Explorer, zodat gegevens die in de BLOB-container worden geplaatst, naar de tabel test worden gestreamd. 

1. Selecteer op de werkbalk de optie **Meldingen** om te controleren of de implementatie van de Event Hub is geslaagd.

1. Selecteer in het cluster dat u hebt gemaakt **data bases** > **TestDatabase**.

    ![Testdatabase selecteren](media/ingest-data-event-grid/select-test-database.png)

1. Selecteer **gegevens opname** > **gegevens verbinding toevoegen**.

    ![Gegevensopname](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Selecteer het verbindings type: **Blob Storage**.

1. Vul het formulier in met de volgende gegevens en selecteer **maken**.

    ![Event Hub-verbinding](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Gegevensbron:

    **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    |---|---|---|
    | Naam van gegevensverbinding | *test-hub-connection* | De naam van de verbinding die u wilt maken in azure Data Explorer.|
    | Abonnement van opslagaccount | Uw abonnements-id | Het abonnements-id waarin uw opslagaccount zich bevindt.|
    | Storage-account | *gridteststorage* | De naam van het opslag account dat u eerder hebt gemaakt.|
    | Event Grid | *test-grid-connection* | De naam van het gebeurtenis raster dat u hebt gemaakt. |
    | Event Hub-naam | *test-hub* | De Event Hub die u hebt gemaakt. Dit veld wordt automatisch ingevuld wanneer u een event grid kiest. |
    | Consumentengroep | *test-group* | De consumenten groep die is gedefinieerd in de Event Hub die u hebt gemaakt. |
    | | |

    Doeltabel:

     **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    |---|---|---|
    | Tabel | *TestTable* | De tabel die u hebt gemaakt in **TestDatabase**. |
    | Gegevensindeling | *JSON* | Ondersteunde indelingen zijn AVRO, CSV, JSON, MEERREGELIGE JSON, PSV, SOH, SCSV, TSV, RAW en TXT. Ondersteunde compressie opties: zip en GZip |
    | Toewijzen van kolommen | *TestMapping* | De toewijzing die u hebt gemaakt in **TestDatabase** en waarmee die binnenkomende JSON-gegevens worden toegewezen aan de kolomnamen en gegevenstypen van **TestTable**.|
    | | |
    
## <a name="generate-sample-data"></a>Voorbeeldgegevens genereren

Nu Azure Data Explorer en het opslag account zijn verbonden, kunt u voorbeeld gegevens maken en deze uploaden naar de Blob-opslag.

U werkt met een klein shellscript dat een paar eenvoudige Azure CLI-opdrachten opgeeft voor interactie met Azure Storage-resources. Met dit script maakt u een nieuwe container in uw opslag account, uploadt u een bestaand bestand (als een blob) naar die container en vervolgens worden de blobs in de container weer gegeven. U kunt [Azure Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview) gebruiken om het script rechtstreeks in de portal uit te voeren.

Sla de gegevens op in een bestand en upload het met dit script:

```Json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```bash
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
> Ter verkrijging van de beste opname prestaties moet *de gedecomprimeerde grootte van* de gecomprimeerde blobs die voor opname worden verzonden, worden gecommuniceerd. Omdat Event Grid meldingen alleen basis details bevatten, moet de informatie over de grootte expliciet worden gecommuniceerd. U kunt de grootte van niet-gecomprimeerde gegevens instellen door de eigenschap `rawSizeBytes` in te stellen op de BLOB-meta gegevens met de niet- *gecomprimeerde* gegevens grootte in bytes.

### <a name="ingestion-properties"></a>Opname-eigenschappen

U kunt de [opname-eigenschappen](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#ingestion-properties) van de BLOB-opname opgeven via de BLOB-meta gegevens.

Deze eigenschappen kunnen worden ingesteld:

|**Eigenschap** | **Beschrijving van eigenschap**|
|---|---|
| `rawSizeBytes` | Grootte van de onbewerkte (niet-gecomprimeerde) gegevens. Voor Avro/ORC/Parquet is dit de grootte voordat de indelings-specifieke compressie wordt toegepast.|
| `kustoTable` |  De naam van de bestaande doel tabel. Onderdrukt de `Table` die is ingesteld op de Blade `Data Connection`. |
| `kustoDataFormat` |  Gegevens indeling. Onderdrukt de `Data format` die is ingesteld op de Blade `Data Connection`. |
| `kustoIngestionMappingReference` |  De naam van de bestaande opname toewijzing die moet worden gebruikt. Onderdrukt de `Column mapping` die is ingesteld op de Blade `Data Connection`.|
| `kustoIgnoreFirstRecord` | Als deze is ingesteld op `true`, wordt in Kusto de eerste rij van de blob genegeerd. Gebruiken in tabel indeling gegevens (CSV, TSV of vergelijkbaar) om kopteksten te negeren. |
| `kustoExtentTags` | Teken reeks die de [labels](/azure/kusto/management/extents-overview#extent-tagging) vertegenwoordigt die worden toegevoegd aan de resulterende gebieden. |
| `kustoCreationTime` |  Onderdrukt [$IngestionTime](/azure/kusto/query/ingestiontimefunction?pivots=azuredataexplorer) voor de blob, opgemaakt als een ISO 8601-teken reeks. Gebruiken voor backfilling. |

> [!NOTE]
> In azure Data Explorer worden de blobs na opname niet verwijderd.
> Behoud de blobs voor thrre tot vijf dagen.
> Gebruik [Azure Blob Storage-levens cyclus](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) om het verwijderen van blobs te beheren. 

## <a name="review-the-data-flow"></a>De gegevensstroom controleren

> [!NOTE]
> Azure Data Explorer heeft een aggregatie beleid (batching) voor gegevens opname die is ontworpen om het opname proces te optimaliseren.
Standaard is het beleid geconfigureerd op vijf minuten.
Zo nodig kunt u het beleid op een later tijdstip wijzigen. In dit artikel kunt u een latentie van een paar minuten verwachten.

1. In de Azure-portal, onder uw Event Grid, ziet u de piek in activiteit terwijl de app wordt uitgevoerd.

    ![Event Grid-grafiek](media/ingest-data-event-grid/event-grid-graph.png)

1. Als u wilt controleren hoeveel berichten er op dat moment de database hebben bereikt, voert u de volgende query uit in de testdatabase.

    ```Kusto
    TestTable
    | count
    ```

1. Voer de volgende query in uw testdatabase uit om de inhoud van de berichten te bekijken.

    ```Kusto
    TestTable
    ```

    De resultatenset ziet er ongeveer als volgt uit.

    ![Berichtresultatenset](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet van plan bent de Event Grid opnieuw te gebruiken, wist u de **test-hub-rg** om te voorkomen dat er kosten in rekening worden gebracht.

1. Selecteer in Azure Portal **Resourcegroepen** aan de linkerkant en selecteer vervolgens de resourcegroep die u hebt gemaakt.  

    Als het menu links is samengevouwen, selecteert u ![Knop Uitvouwen](media/ingest-data-event-grid/expand.png) om het menu uit te vouwen.

   ![Resourcegroep selecteren die moet worden verwijderd](media/ingest-data-event-grid/delete-resources-select.png)

1. Selecteer onder **test-resource-group** de optie **Resourcegroep verwijderen**.

1. Voer in het nieuwe venster de naam in van de resource groep die u wilt verwijderen (*test-hub-RG*) en selecteer vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* [Query's uitvoeren op gegevens in azure Data Explorer](web-query-data.md)
