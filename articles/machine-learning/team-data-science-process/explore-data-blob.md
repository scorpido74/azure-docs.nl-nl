---
title: Gegevens verkennen in Azure blob-opslag met panda's - Team Data Science Process
description: Gegevens verkennen die zijn opgeslagen in Azure blob-container met behulp van het Python-pakket panda's.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e429dce497411305964cb1ec5298228dc4093b1f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685956"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Gegevens verkennen in Azure blob-opslag met panda's

In dit artikel wordt ingaan op het verkennen van gegevens die zijn opgeslagen in Azure blob-container met behulp van [panda's](https://pandas.pydata.org/) Python-pakket.

Deze taak is een stap in het [Team Data Science Process.](overview.md)

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u:

* Een Azure-opslagaccount maken. Als u instructies nodig hebt, [raadpleegt u Een Azure Storage-account maken](../../storage/common/storage-account-create.md)
* Uw gegevens zijn opgeslagen in een Azure blob-opslagaccount. Zie Gegevens verplaatsen [van en naar Azure Storage als](../../storage/common/storage-moving-data.md) u instructies nodig hebt

## <a name="load-the-data-into-a-pandas-dataframe"></a>De gegevens in een panda's DataFrame laden
Om een gegevensset te verkennen en te manipuleren, moet deze eerst worden gedownload van de blobbron naar een lokaal bestand, dat vervolgens kan worden geladen in een pandas DataFrame. Hier volgen de volgende stappen voor deze procedure:

1. Download de gegevens van Azure blob met het volgende Voorbeeld van Python-code met blobservice. Vervang de variabele in de volgende code door uw specifieke waarden:

    ```python
    from azure.storage.blob import BlockBlobService
    import pandas as pd
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlockBlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```

1. Lees de gegevens in een pandas DataFrame uit het gedownloade bestand.

    ```python
    # LOCALFILE is the file path
    dataframe_blobdata = pd.read_csv(LOCALFILENAME)
    ```

Nu bent u klaar om de gegevens te verkennen en functies op deze gegevensset te genereren.

## <a name="examples-of-data-exploration-using-pandas"></a><a name="blob-dataexploration"></a>Voorbeelden van data-exploratie met panda's
Hier zijn een paar voorbeelden van manieren om gegevens te verkennen met behulp van panda's:

1. Het **aantal rijen en kolommen controleren**

    ```python
    print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
    ```

1. **Controleer** de eerste of laatste **paar rijen** in de volgende gegevensset:

    ```python
    dataframe_blobdata.head(10)

    dataframe_blobdata.tail(10)
    ```

1. Controleer het **gegevenstype** dat elke kolom is geïmporteerd met behulp van de volgende voorbeeldcode

    ```python
    for col in dataframe_blobdata.columns:
        print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
    ```

1. Controleer de **basisstatistieken** voor de kolommen in de gegevensset als volgt

    ```python
    dataframe_blobdata.describe()
    ```

1. Bekijk het aantal vermeldingen voor elke kolomwaarde als volgt

    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```

1. **Ontbrekende waarden tellen** ten opzichte van het werkelijke aantal vermeldingen in elke kolom met behulp van de volgende voorbeeldcode

    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print miss_num
    ```

1. Als u **ontbrekende waarden** hebt voor een specifieke kolom in de gegevens, u deze als volgt neerzetten:

    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```

    Een andere manier om ontbrekende waarden te vervangen is met de modusfunctie:

    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna(
        {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
    ```

1. Een **histogramplot** maken met behulp van een variabel aantal opslaglocaties om de verdeling van een variabele in kaart te brengen

    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```

1. Bekijk **correlaties** tussen variabelen met behulp van een scatterplot of met behulp van de ingebouwde correlatiefunctie

    ```python
    # relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

    # correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```
