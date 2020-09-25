---
title: Gegevens sets maken en verkennen met labels
titleSuffix: Azure Machine Learning
description: Meer informatie over het exporteren van gegevenslabels uit uw Azure Machine Learning het labelen van projecten en het gebruik ervan voor machine learning taken.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 9ffc134c2bded747346f3639119dde4a6f14231b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250705"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Azure Machine Learning gegevensset maken en verkennen met labels

In dit artikel leert u hoe u de gegevenslabels kunt exporteren uit een Azure Machine Learning gegevens label project en laadt u deze in populaire indelingen, zoals een Panda data frame voor het verkennen van gegevens of een Torchvision-gegevensset voor afbeeldings transformatie. 

## <a name="what-are-datasets-with-labels"></a>Wat zijn gegevens sets met labels? 

We verwijzen naar het Azure Machine Learning gegevens sets met labels als gelabelde gegevens sets. Deze specifieke typen gegevensset van gegevens sets met een label worden alleen gemaakt als uitvoer van Azure Machine Learning gegevenslabel projecten. Maak een gegevens label project met [de volgende stappen](how-to-create-labeling-projects.md). Machine Learning ondersteunt gegevens label projecten voor de classificatie van afbeeldingen, ofwel meerdere labels, meerdere klassen en object-id's samen met gebonden vakken.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://aka.ms/AMLFree) aan voordat u begint.
* De [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)of toegang tot [Azure machine learning Studio](https://ml.azure.com/).
    * Het pakket [Azure-contrib-dataset](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py&preserve-view=true) installeren
* Een Machine Learning-werkruimte. Raadpleeg [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md).
* Toegang tot een Azure Machine Learning gegevens labelen project. Als u geen label project hebt, maakt u er een met [de volgende stappen](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Gegevenslabels exporteren 

Wanneer u een gegevens label project hebt voltooid, kunt u de label gegevens uit een label project exporteren. Als u dit doet, kunt u zowel de verwijzing naar de gegevens als de bijbehorende labels vastleggen en deze exporteren in [Coco-indeling](http://cocodataset.org/#format-data) of als een Azure machine learning-gegevensset. Gebruik de knop **Exporteren** op de pagina **Projectdetails** van het labelproject.

### <a name="coco"></a>COCO 

 Het COCO-bestand wordt gemaakt in de Blob-standaardopslag van de Azure Machine Learning-werkruimte in een map binnen *export/coco*. 

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning gegevensset

U kunt de geëxporteerde Azure Machine Learning gegevensset openen in het gedeelte **gegevens sets** van uw Azure machine learning Studio. De pagina **Details** gegevensset biedt ook voorbeeld code voor toegang tot uw labels vanuit Python.

![Geëxporteerde gegevens](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Gelabelde gegevens sets verkennen

Laad uw gelabelde gegevens sets in een Panda data frame-of Torchvision-gegevensset om gebruik te maken van populaire open-source bibliotheken voor het verkennen van gegevens, evenals PyTorch meegeleverde bibliotheken voor het omzetten van afbeeldingen en trainingen.

### <a name="pandas-dataframe"></a>Panda data frame

U kunt gegevens sets met de [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true#&preserve-view=trueto-pandas-dataframe-on-error--null---out-of-range-datetime--null--) methode vanuit de klasse laden in een Panda data frame `azureml-contrib-dataset` . Installeer de klasse met de volgende shell-opdracht: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>De contrib-naam ruimte wordt regel matig gewijzigd, omdat we de service kunnen verbeteren. Als zodanig moeten alle in deze naam ruimte worden beschouwd als een preview en niet volledig worden ondersteund door micro soft.

We bieden de volgende opties voor bestands verwerking voor bestands stromen bij het converteren naar een Panda data frame.
* Downloaden: uw gegevens bestanden downloaden naar een lokaal pad.
* Koppelen: uw gegevens bestanden koppelen aan een koppel punt. Koppelen werkt alleen voor op Linux gebaseerde compute, waaronder Azure Machine Learning notebook-VM en Azure Machine Learning compute.

```Python
import azureml.contrib.dataset
from azureml.contrib.dataset import FileHandlingOption
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Torchvision gegevens sets

U kunt gegevens sets in Torchvision-gegevensset laden met de methode [to_torchvision ()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py&preserve-view=true#&preserve-view=trueto-torchvision--) , ook uit de `azureml-contrib-dataset` klasse. Als u deze methode wilt gebruiken, moet [PyTorch](https://pytorch.org/) zijn geïnstalleerd. 

```python
from torchvision.transforms import functional as F

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Volgende stappen

* Zie het [notitie blok gegevensset met labels](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) voor een volledig trainings voorbeeld.
