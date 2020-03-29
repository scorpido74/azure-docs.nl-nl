---
title: Datasets maken en verkennen met labels
titleSuffix: Azure Machine Learning
description: Meer informatie over het exporteren van gegevenslabels uit uw Azure Machine Learning-labelprojecten en deze gebruiken voor machine learning-taken.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76549487"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Azure Machine Learning-gegevensset maken en verkennen met labels

In dit artikel leert u hoe u de gegevenslabels van een Azure Machine Learning-gegevenslabelingproject exporteren en deze laden in populaire indelingen, zoals een pandas-gegevensframe voor gegevensverkenning of een Torchvision-gegevensset voor beeldtransformatie. 

## <a name="what-are-datasets-with-labels"></a>Wat zijn gegevenssets met labels 

Azure Machine Learning-gegevenssets met labels zijn [Tabelsets](how-to-create-register-datasets.md#dataset-types) met een labeleigenschap, we verwijzen ernaar om gelabelde gegevenssets te noemen. Deze specifieke typen tabulardatasets worden alleen gemaakt als een uitvoer van Azure Machine Learning-gegevenslabelingprojecten. Maak met [deze stappen](how-to-create-labeling-projects.md)een project voor het labelen van gegevens . Machine Learning ondersteunt projecten voor het labelen van gegevens voor beeldclassificatie, met meerdere labelen of meerdere klassen, en objectidentificatie samen met begrensde vakken.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://aka.ms/AMLFree) voordat u begint.
* De [Azure Machine Learning SDK voor Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)of toegang tot Azure Machine [Learning-studio](https://ml.azure.com/).
    * Het pakket [azure-contrib-dataset](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py) installeren
* Een Machine Learning-werkruimte. Zie [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md).
* Toegang tot een Azure Machine Learning-gegevenslabelingproject. Als u geen labelproject hebt, maakt u er een met [deze stappen](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Gegevenslabels exporteren 

Wanneer u een project voor het labelen van gegevens voltooit, u de labelgegevens exporteren uit een labelingproject. Hiermee u zowel de verwijzing naar de gegevens als de labels vastleggen en exporteren in [COCO-indeling](http://cocodataset.org/#format-data) of als azure machine learning-gegevensset. Gebruik de knop **Exporteren** op de pagina **Projectdetails** van uw labelproject.

### <a name="coco"></a>Coco 

 Het COCO-bestand wordt gemaakt in de standaardblobstore van de Azure Machine Learning-werkruimte in een map binnen *exporteren/coco*. 

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning-gegevensset

U hebt toegang tot de geëxporteerde Azure Machine Learning-gegevensset in de sectie **Gegevenssets** van uw Azure Machine Learning-studio. De pagina **Details van** de gegevensset bevat ook voorbeeldcode om toegang te krijgen tot uw labels van Python.

![Geëxporteerde gegevensset](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Gelabelde gegevenssets verkennen

Laad uw gelabelde gegevenssets in een pandas-gegevensframe of Torchvision-gegevensset om populaire open-sourcebibliotheken te gebruiken voor gegevensverkenning, evenals door PyTorch geleverde bibliotheken voor beeldtransformatie en -training.

### <a name="pandas-dataframe"></a>Panda's dataframe

U gelabelde gegevenssets laden in een [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) panda's-gegevensframe met de methode uit de `azureml-contrib-dataset` klasse. Installeer de klasse met de volgende shell-opdracht: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>De azureml.contrib-naamruimte verandert regelmatig, omdat we werken aan het verbeteren van de service. Als zodanig moet alles in deze naamruimte worden beschouwd als een voorbeeld en niet volledig worden ondersteund door Microsoft.

We bieden de volgende opties voor bestandsverwerking voor bestandsstromen bij het converteren naar een pandas-gegevensframe.
* Downloaden: Download uw gegevensbestanden naar een lokaal pad.
* Mount: Monteer uw gegevensbestanden op een bevestigingspunt. Mount werkt alleen voor op Linux gebaseerde compute, waaronder Azure Machine Learning notebook VM en Azure Machine Learning Compute.

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

### <a name="torchvision-datasets"></a>Torchvision datasets

U gelabelde gegevenssets laden in de gegevensset Torchvision met de [methode to_torchvision()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) ook vanuit de `azureml-contrib-dataset` klasse. Om deze methode te gebruiken, moet u [PyTorch](https://pytorch.org/) hebben geïnstalleerd. 

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

* Zie de [gegevensset met labels notitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) voor volledige training voorbeeld.
