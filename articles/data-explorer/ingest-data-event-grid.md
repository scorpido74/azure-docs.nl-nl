---
title: Azure-blobs opnemen in Azure Data Explorer
description: In dit artikel leert u hoe u gegevens van een opslag account kunt verzenden naar Azure Data Explorer met behulp van een Event Grid-abonnement.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 9557923fc2228e8508acaa7e15d1729ac3d29538
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028378"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Blobs opnemen in azure Data Explorer door zich te abonneren op Event Grid meldingen

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)

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
    | Name | *test-grid-connection* | De naam van het gebeurtenis raster dat u wilt maken.|
    | Gebeurtenisschema | *Event Grid schema* | Het schema dat moet worden gebruikt voor het event grid. |
    | Onderwerptype | *Opslagaccount* | Het type Event Grid-onderwerp. |
    | Onderwerpresource | *gridteststorage* | De naam van uw opslagaccount. |
    | Abonneren op alle gebeurtenistypen | *Maak* | Geen meldingen ontvangen voor alle gebeurtenissen. |
    | Gedefinieerde gebeurtenistypen | *BLOB gemaakt* | Voor welke specifieke gebeurtenissen u een melding ontvangt. |
    | Eindpunttype | *Event hubs* | Het type eindpunt waarnaar u de gebeurtenissen verzendt. |
    | Eindpunt | *test-hub* | De Event Hub die u hebt gemaakt. |
    | | |

1. Selecteer het tabblad **Extra functies** als u bestanden uit een specifieke container wilt bijhouden. Stel de filters voor de meldingen als volgt in:
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

1.  Selecteer het verbindings type: **BLOB Storage**.

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
    | Gegevensindeling | *JSON* | Ondersteunde indelingen zijn Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV en TXT. Ondersteunde compressie opties: Zip en GZip |
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
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

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
