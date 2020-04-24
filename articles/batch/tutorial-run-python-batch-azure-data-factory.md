---
title: Python-scripts uitvoeren via Data Factory-Azure Batch python
description: 'Zelf studie: informatie over het uitvoeren van python-scripts als onderdeel van een pijp lijn via Azure Data Factory met behulp van Azure Batch.'
author: mammask
ms.devlang: python
ms.topic: tutorial
ms.date: 12/11/2019
ms.author: komammas
ms.custom: mvc
ms.openlocfilehash: 6761896a6555c11d7957f923a5951641c1541012
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117060"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Zelf studie: python-scripts uitvoeren via Azure Data Factory met behulp van Azure Batch

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Verifiëren met uw Batch- en Storage-accounts
> * Een script ontwikkelen en uitvoeren in python
> * Een pool van rekenknooppunten maken om een toepassing uit te voeren
> * Uw python-workloads plannen
> * Uw analyse pijplijn bewaken
> * Toegang tot uw logboeken

In het volgende voor beeld wordt een python-script uitgevoerd dat CSV-invoer van een BLOB storage-container ontvangt, een gegevensbewerkings proces uitvoert en de uitvoer naar een afzonderlijke Blob Storage-container schrijft.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een geïnstalleerde [python](https://www.python.org/downloads/) -distributie voor lokale tests.
* Het [Azure](https://pypi.org/project/azure/) `pip` -pakket.
* Een Azure Batch-account en een gekoppeld Azure Storage-account. Zie [een batch-account maken](quick-create-portal.md#create-a-batch-account) voor meer informatie over het maken en koppelen van batch-accounts aan opslag accounts.
* Een Azure Data Factory-account. Zie [een Data Factory maken](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) voor meer informatie over het maken van een Data Factory via de Azure Portal.
* [Batch Explorer](https://azure.github.io/BatchExplorer/).
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Een batch-pool maken met behulp van Batch Explorer

In deze sectie gebruikt u Batch Explorer om de batch-pool te maken die wordt gebruikt door uw Azure Data Factory-pijp lijn. 

1. Meld u aan bij Batch Explorer met uw Azure-referenties.
1. Uw batch-account selecteren
1. Maak een pool door **Pools** te selecteren op de balk aan de linkerkant en klik vervolgens op de knop **toevoegen** boven het zoek formulier. 
    1. Kies een ID en weergave naam. Voor dit voor `custom-activity-pool` beeld gebruiken we.
    1. Stel het schaal type in op **vaste grootte**en stel het toegewezen aantal knoop punten in op 2.
    1. Onder **Data Science**selecteert u **Dsvm Windows** als het besturings systeem.
    1. Kies `Standard_f2s_v2` als grootte van de virtuele machine.
    1. Schakel de start taak in en voeg de `cmd /c "pip install pandas"`opdracht toe. De gebruikers-id kan blijven als de standaard **groeps gebruiker**.
    1. Selecteer **OK**.

## <a name="create-blob-containers"></a>BLOB-containers maken

Hier maakt u BLOB-containers waarmee de invoer-en uitvoer bestanden voor de OCR batch-taak worden opgeslagen.

1. Meld u aan bij Storage Explorer met uw Azure-referenties.
1. Met het opslag account dat is gekoppeld aan uw batch-account, maakt u twee BLOB-containers (één voor invoer bestanden, één voor uitvoer bestanden) door de stappen te volgen in [een BLOB-container maken](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container).
    * In dit voor beeld noemen we onze invoer container `input`en onze uitvoer container. `output`
1. Upload `main.py` en `iris.csv` naar uw invoer container `input` met behulp van Storage Explorer door de stappen te volgen bij het [beheren van blobs in een BLOB-container](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container)


## <a name="develop-a-script-in-python"></a>Een script ontwikkelen in python

Met het volgende python-script `iris.csv` wordt de gegevensset `input` uit uw container geladen, wordt een gegevensverwerkings proces uitgevoerd en worden de `output` resultaten weer opgeslagen in de container.

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

Sla het script op `main.py` als en upload het naar de **Azure Storage** -container. Zorg ervoor dat u de functionaliteit lokaal test en valideert voordat u deze uploadt naar de BLOB-container:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Een Azure Data Factory pijp lijn instellen

In deze sectie maakt en valideert u een pijp lijn met behulp van uw python-script.

1. Volg de stappen voor het maken van een data factory in het gedeelte ' een data factory maken ' van [dit artikel](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).
1. Selecteer in het vak **Factory resources** de knop + (plus) en selecteer vervolgens **pijp lijn**
1. Stel op het tabblad **Algemeen** de naam van de pijp lijn in als ' python uitvoeren '

    ![](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. In het vak **activiteiten** vouwt u **batch-service**uit. Sleep de aangepaste activiteit vanuit de werkset **activiteiten** naar het ontwerp oppervlak voor pijp lijnen.
1. Geef op het tabblad **Algemeen** **testPipeline** op naam op

    ![](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. Voeg op het tabblad **Azure batch** het **batch-account** toe dat in de vorige stappen is gemaakt en **test de verbinding** om er zeker van te zijn dat het goed is

    ![](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. Voer op het tabblad **instellingen** de opdracht `python main.py`in.
1. Voor de **gekoppelde resource service**voegt u het opslag account toe dat in de vorige stappen is gemaakt. Test de verbinding om er zeker van te zijn dat deze is geslaagd.
1. Selecteer in het **mappad**de naam van de **Azure Blob Storage** -container die het python-script en de bijbehorende invoer bevat. Hiermee worden de geselecteerde bestanden van de container naar de groeps knooppunt instanties gedownload voordat het python-script wordt uitgevoerd.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)
1. Klik in de pijplijnwerkbalk boven het canvas op **Valideren** om de instellingen voor de pijplijn te valideren. Controleer of de pijplijn is gevalideerd. Klik op de &gt;&gt; (pijl-rechts) om de uitvoergegevens van de validatie te sluiten.
1. Klik op **debug** om de pijp lijn te testen en te controleren of deze correct werkt.
1. Klik op **publiceren** om de pijp lijn te publiceren.
1. Klik op **trigger** om het python-script uit te voeren als onderdeel van een batch proces.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>De logboek bestanden bewaken

Als er waarschuwingen of fouten worden gegenereerd door de uitvoering van het script, kunt u het uitchecken `stdout.txt` of `stderr.txt` voor meer informatie over de geregistreerde uitvoer.

1. Selecteer **taken** aan de linkerkant van batch Explorer.
1. Kies de taak die door uw data factory is gemaakt. Als u de naam van `custom-activity-pool`uw groep `adfv2-custom-activity-pool`hebt opgegeven, selecteert u.
1. Klik op de taak met de afsluit code voor de fout.
1. Bekijk `stdout.txt` en `stderr.txt` onderzoek uw probleem en Analyseer deze.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een voor beeld onderzocht van het gebruik van python-scripts als onderdeel van een pijp lijn via Azure Data Factory met behulp van Azure Batch.

Voor meer informatie over Azure Data Factory raadpleegt u:

> [!div class="nextstepaction"]
> [Azure Data Factory](../data-factory/introduction.md)
> [Aangepaste activiteiten](../data-factory/transform-data-using-dotnet-custom-activity.md) Azure Data Factory[pijp lijnen en activiteiten](../data-factory/concepts-pipelines-activities.md)
> 
