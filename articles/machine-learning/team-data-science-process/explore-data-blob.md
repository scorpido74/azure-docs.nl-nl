---
title: Gegevens verkennen in Azure blob-opslag met pandas - Team Data Science Process
description: Klik hier voor meer informatie over het verkennen van gegevens die zijn opgeslagen in Azure blob-container met behulp van het pandas Python-pakket.
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
ms.openlocfilehash: 29011760a94a05020150ceddeba4303b87c2f610
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722183"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Gegevens verkennen in Azure blob-opslag met pandas

In dit artikel wordt beschreven hoe u gegevens die zijn opgeslagen in Azure Blob-container kunt verkennen met behulp van het python-pakket van [Panda](https://pandas.pydata.org/) .

Deze taak is een stap in het [team data Science process](overview.md).

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u hebt:

* Een Azure storage-account gemaakt. Als u instructies nodig hebt, raadpleegt u [een Azure Storage account maken](../../storage/common/storage-account-create.md)
* Uw gegevens opgeslagen in een Azure blob storage-account. Zie [gegevens verplaatsen van en naar Azure Storage](../../storage/common/storage-moving-data.md) als u instructies nodig hebt.

## <a name="load-the-data-into-a-pandas-dataframe"></a>De gegevens in een pandas DataFrame laden
Om te verkennen en bewerken van een gegevensset, moet deze eerst worden gedownload van de bron-blob naar een lokaal bestand, die vervolgens kan worden geladen in een pandas DataFrame. Hier volgen de stappen voor deze procedure:

1. Down load de gegevens van Azure Blob met het volgende python-code voorbeeld met Blob service. De variabele in de volgende code vervangen door uw eigen specifieke waarden:

```python
from azure.storage.blob import BlockBlobService
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

1. De gegevens in een pandas DataFrame uit het gedownloade bestand gelezen.

```python
# LOCALFILE is the file path
dataframe_blobdata = pd.read_csv(LOCALFILE)
```

U bent nu klaar voor de gegevens verkennen en het genereren van functies voor deze gegevensset.

## <a name="blob-dataexploration"></a>Voor beelden van het verkennen van gegevens met behulp van Pandas
Hier volgen enkele voorbeelden van manieren om met pandas gegevens te verkennen:

1. Het **aantal rijen en kolommen** controleren

```python
print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
```

1. **Inspecteer** de eerste of laatste paar **rijen** in de volgende gegevensset:

```python
dataframe_blobdata.head(10)

dataframe_blobdata.tail(10)
```

1. Controleer het **gegevens type** dat elke kolom heeft geïmporteerd, zoals met behulp van de volgende voorbeeld code

```python
for col in dataframe_blobdata.columns:
    print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
```

1. Controleer als volgt de **basis statistieken** voor de kolommen in de gegevensset

```python
dataframe_blobdata.describe()
```

1. Het aantal vermeldingen voor elke waarde in de kolom als volgt bekijken

```python
dataframe_blobdata['<column_name>'].value_counts()
```

1. **Aantal ontbrekende waarden** ten opzichte van het werkelijke aantal vermeldingen in elke kolom met de volgende voorbeeld code

```python
miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
print miss_num
```

1. Als u de **waarden** voor een bepaalde kolom in de gegevens ontbreken, kunt u deze als volgt verwijderen:

```python
dataframe_blobdata_noNA = dataframe_blobdata.dropna()
dataframe_blobdata_noNA.shape
```

Een andere manier om de ontbrekende waarden vervangen is met de modusfunctie:

```python
dataframe_blobdata_mode = dataframe_blobdata.fillna(
    {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
```

1. Een **histogram** maken met behulp van een variabel aantal opslag locaties om de distributie van een variabele uit te zetten

```python
dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
```

1. Bekijk **correlaties** tussen variabelen met behulp van een scatterplot of gebruik de ingebouwde correlatie functie

```python
# relationship between column_a and column_b using scatter plot
plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

# correlation between column_a and column_b
dataframe_blobdata[['<column_a>', '<column_b>']].corr()
```
