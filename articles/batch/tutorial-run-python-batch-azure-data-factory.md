---
title: Python-scripts uitvoeren via Data Factory - Azure Batch Python
description: Zelfstudie - Meer informatie over het uitvoeren van Python-scripts als onderdeel van een pijplijn via Azure Data Factory met Azure Batch.
services: batch
author: mammask
manager: jeconnoc
ms.service: batch
ms.devlang: python
ms.topic: tutorial
ms.date: 12/11/2019
ms.author: komammas
ms.custom: mvc
ms.openlocfilehash: 2995c5da4491f14471d9ed03022a144a02beab5a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201834"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Zelfstudie: Python-scripts uitvoeren via Azure Data Factory met Azure Batch

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Verifiëren met uw Batch- en Storage-accounts
> * Een script ontwikkelen en uitvoeren in Python
> * Een pool van rekenknooppunten maken om een toepassing uit te voeren
> * Uw Python-workloads plannen
> * Uw analysepijplijn controleren
> * Toegang tot uw logboekbestanden

In het onderstaande voorbeeld wordt een Python-script uitgevoerd dat CSV-invoer ontvangt van een blobopslagcontainer, een gegevensmanipulatieproces uitvoert en de uitvoer naar een afzonderlijke blobopslagcontainer schrijft.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een geïnstalleerde [Python-distributie](https://www.python.org/downloads/) voor lokale tests.
* Het [Azure-pakket.](https://pypi.org/project/azure/) `pip`
* Een Azure Batch-account en een gekoppeld Azure Storage-account. Zie [Een batchaccount maken](quick-create-portal.md#create-a-batch-account) voor meer informatie over het maken en koppelen van Batch-accounts aan opslagaccounts.
* Een Azure Data Factory-account. Zie [Een gegevensfabriek maken](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) voor meer informatie over het maken van een gegevensfabriek via de Azure-portal.
* [Batch Explorer](https://azure.github.io/BatchExplorer/).
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Een batchgroep maken met Batch Explorer

In deze sectie gebruikt u Batch Explorer om de batchgroep te maken die uw Azure Data-fabriekspijplijn gebruikt. 

1. Meld u aan bij Batch Explorer met uw Azure-referenties.
1. Uw Batch-account selecteren
1. Maak een pool door **Pools** aan de linkerkant te selecteren en vervolgens de knop **Toevoegen** boven het zoekformulier. 
    1. Kies een id en weergavenaam. We gebruiken `custom-activity-pool` voor dit voorbeeld.
    1. Stel het schaaltype in op **Vaste grootte**en stel het aantal speciale knooppunten in op 2.
    1. Selecteer **Dsvm Windows** onder **Gegevenswetenschap**als besturingssysteem.
    1. Kies `Standard_f2s_v2` als de virtuele machinegrootte.
    1. Schakel de starttaak in `cmd /c "pip install pandas"`en voeg de opdracht toe. De gebruikersidentiteit kan blijven als de **standaardgroepgebruiker**.
    1. Selecteer **OK**.

## <a name="create-blob-containers"></a>Blobcontainers maken

Hier maakt u blobcontainers die uw invoer- en uitvoerbestanden opslaan voor de ocrbatchtaak.

1. Meld u aan bij Storage Explorer met uw Azure-referenties.
1. Maak met het opslagaccount dat is gekoppeld aan uw Batch-account twee blobcontainers (één voor invoerbestanden, één voor uitvoerbestanden) door de stappen te volgen bij [Een blobcontainer maken.](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container)
    * In dit voorbeeld noemen we onze `input`invoercontainer en `output`onze uitvoercontainer .
1. `main.py` Uploaden `iris.csv` en naar `input` uw invoercontainer met Storage Explorer door de stappen te volgen bij [Blobs beheren in een blobcontainer](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container)


## <a name="develop-a-script-in-python"></a>Een script ontwikkelen in Python

Het volgende Python-script laadt de `iris.csv` gegevensset uit uw `input` container, voert `output` een gegevensmanipulatieproces uit en slaat de resultaten terug naar de container.

``` python
# Load libraries
from azure.storage.blob import BlockBlobService
import pandas as pd

# Define parameters
storageAccountName = "<storage-account-name>"
storageKey         = "<storage-account-key>"
containerName      = "output"

# Establish connection with the blob storage account
blobService = BlockBlobService(account_name=storageAccountName,
                               account_key=storageKey
                               )

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Subset records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv("iris_setosa.csv", index = False)

# Upload iris dataset
blobService.create_blob_from_text(containerName, "iris_setosa.csv", "iris_setosa.csv")
```

Sla het `main.py` script op en upload het naar de **Azure Storage-container.** Zorg ervoor dat u de functionaliteit lokaal test en valideert voordat u deze uploadt naar uw blobcontainer:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Een Azure Data Factory-pijplijn instellen

In deze sectie maakt en valideert u een pijplijn met behulp van uw Python-script.

1. Volg de stappen om een gegevensfabriek te maken onder het gedeelte 'Een gegevensfabriek maken' van [dit artikel.](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)
1. Selecteer in het vak **Fabrieksresources** de knop + (plus) en selecteer **Vervolgens Pijplijn**
1. Stel op het tabblad **Algemeen** de naam van de pijplijn in als 'Python uitvoeren'

    ![](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. Vouw **batchservice**uit in het vak **Activiteiten** . Sleep de aangepaste activiteit van de **gereedschapsset Activiteiten** naar het oppervlak van de pijplijnontwerper.
1. Geef op het tabblad **Algemeen** **testpijplijn** op voor naam op

    ![](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. Voeg op het tabblad **Azure Batch** het **batchaccount** toe dat in de vorige stappen is gemaakt en **Testverbinding** om ervoor te zorgen dat deze is gelukt

    ![](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. Voer **Settings** op het tabblad `python main.py`Instellingen de opdracht in .
1. Voeg voor de **Resource Linked Service**het opslagaccount toe dat in de vorige stappen is gemaakt. Test de verbinding om er zeker van te zijn dat deze succesvol is.
1. Selecteer in het **mappad**de naam van de **Azure Blob Storage-container** die het Python-script en de bijbehorende ingangen bevat. Hiermee worden de geselecteerde bestanden van de container naar de instanties van het poolknooppunt gedownload voordat het Python-script wordt uitgevoerd.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)
1. Klik in de pijplijnwerkbalk boven het canvas op **Valideren** om de instellingen voor de pijplijn te valideren. Controleer of de pijplijn is gevalideerd. Klik op de &gt;&gt; (pijl-rechts) om de uitvoergegevens van de validatie te sluiten.
1. Klik **op Foutopsporing** om de pijplijn te testen en ervoor te zorgen dat deze nauwkeurig werkt.
1. Klik **op Publiceren** om de pijplijn te publiceren.
1. Klik **op Trigger** om het Python-script uit te voeren als onderdeel van een batchproces.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>De logboekbestanden controleren

In het geval dat waarschuwingen of fouten worden geproduceerd `stdout.txt` door `stderr.txt` de uitvoering van uw script, u uitchecken of voor meer informatie over de uitvoer die is geregistreerd.

1. Selecteer **Taken** aan de linkerkant van Batch Explorer.
1. Kies de taak die is gemaakt door uw gegevensfabriek. Ervan uitgaande dat `custom-activity-pool`u `adfv2-custom-activity-pool`uw zwembad hebt benoemd, selecteert u .
1. Klik op de taak met een foutuitgangscode.
1. Uw `stdout.txt` `stderr.txt` probleem bekijken en onderzoeken en diagnosticeren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een voorbeeld verkend waarin u leerde hoe u Python-scripts uitvoeren als onderdeel van een pijplijn via Azure Data Factory met Azure Batch.

Zie voor meer informatie over Azure Data Factory:

> [!div class="nextstepaction"]
> [Azure Data](../data-factory/introduction.md)
> [Factory-pijplijnen en -activiteiten](../data-factory/concepts-pipelines-activities.md)
> Aangepaste[activiteiten](../data-factory/transform-data-using-dotnet-custom-activity.md)
