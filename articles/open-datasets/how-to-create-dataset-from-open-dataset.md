---
title: Gegevens sets maken met Azure open gegevens sets
titleSuffix: Azure Open Datasets
description: Meer informatie over het maken van een Azure Machine Learning gegevensset van Azure open gegevens sets.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: e6f3a541f1e9dbca2c9949fb0c5cde28cd43e8e5
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88183012"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Azure Machine Learning gegevens sets maken op basis van Azure open gegevens sets
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u een Azure Machine Learning gegevensset maakt op basis van Azure-gegevens [sets](https://azure.microsoft.com/services/open-datasets/) om toegang te krijgen tot gegevens voor uw lokale of externe experimenten. 

Door een [Azure machine learning-gegevensset](../machine-learning/how-to-create-register-datasets.md)te maken, maakt u een verwijzing naar de locatie van de gegevens bron, samen met een kopie van de meta gegevens ervan. Omdat de gegevens zich op de bestaande locatie blijven, ontstaan er geen extra opslag kosten en is er geen risico dat u onbedoeld uw oorspronkelijke gegevens bronnen wijzigt. Gegevens sets worden ook geëvalueerd in vertraagd, die hulp middelen in de snelheid van de werk stroom prestaties.
 
Zie het artikel over [beveiligde toegang](../machine-learning/concept-data.md#data-workflow) als u wilt weten waar gegevens sets passen in de algehele werk stroom van Azure machine learning Data Access.


Open gegevens sets bevinden zich in de Cloud op Microsoft Azure en zijn opgenomen in de [Azure machine learning PYTHON SDK](#create-datasets-with-the-sdk) en de [Azure machine learning Studio](#create-datasets-with-the-studio).

## <a name="why-use-azure-open-datasets"></a>Waarom Azure open gegevens sets gebruiken? 

Azure Open Datasets zijn samengestelde openbare gegevenssets die u kunt gebruiken om scenariospecifieke functies toe te voegen aan machine learning-oplossingen voor nauwkeurigere modellen. Gegevens sets bevatten informatie over openbaar domein voor weer, telling, feest dagen, open bare veiligheid en locatie die u helpen bij het trainen van machine learning modellen en verrijkende voorspellende oplossingen. 

Zie [Wat zijn open gegevens sets?](overview-what-are-open-datasets.md) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u gegevens sets wilt maken en gebruiken, hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

* Een [Azure machine learning-werk ruimte](../machine-learning/how-to-manage-workspace.md).

* De [Azure machine learning SDK voor python is geïnstalleerd](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), waaronder het pakket met de azureml-gegevens sets.

    * Maak een [Azure machine learning Compute-exemplaar](../machine-learning/concept-compute-instance.md#managing-a-compute-instance), een volledig geconfigureerde en beheerde ontwikkel omgeving met daarin geïntegreerde notebooks en de SDK die al is geïnstalleerd.

    **OF**

    * Werk met uw eigen Jupyter-notebook en installeer de SDK zelf met [deze instructies](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

> [!NOTE]
> Sommige dataset-klassen hebben afhankelijkheden van het pakket voor [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Dit is alleen compatibel met 64-bits python. Voor Linux-gebruikers worden deze klassen alleen ondersteund in de volgende distributies: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) en CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Gegevens sets maken met de SDK

Als u gegevens sets wilt maken via de klassen Open gegevens sets in de Azure Machine Learning python SDK, moet u ervoor zorgen dat u het pakket met hebt geïnstalleerd `pip install azureml-opendatasets` . Elke afzonderlijke gegevensset wordt vertegenwoordigd door een eigen klasse in de SDK en bepaalde klassen zijn beschikbaar als een `TabularDataset` , `FileDataset` of beide. Zie de [referentie documentatie](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) voor een volledige lijst met klassen.

U kunt bepaalde klassen ophalen als ofwel een `TabularDataset` of `FileDataset` , waarmee u de bestanden rechtstreeks kunt bewerken en/of downloaden. Andere klassen kunnen een gegevensset **alleen** ophalen met behulp van een van `get_tabular_dataset()` of- `get_file_dataset()` functies. In het volgende code voorbeeld ziet u enkele voor beelden van deze typen klassen.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Wanneer u een gegevensset registreert die is gemaakt op basis van geopende gegevens sets, worden er geen gegevens direct gedownload, maar worden de gegevens later weer gegeven wanneer u hierom wordt gevraagd (bijvoorbeeld tijdens training) vanuit een centrale opslag locatie.

## <a name="create-datasets-with-the-studio"></a>Gegevens sets maken met de Studio

U kunt ook gegevens sets maken op basis van open gegevens sets met de [Azure machine learning Studio](https://ml.azure.com).

1. Selecteer in uw werk ruimte het tabblad **gegevens sets** onder **assets**. Selecteer in de vervolg keuzelijst **gegevensset maken** de optie **uit geopende gegevens sets**.

    ![Gegevensset openen met de gebruikers interface](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. Selecteer een gegevensset door de tegel ervan te selecteren. (U hebt de mogelijkheid om te filteren met behulp van de zoek balk.) Selecteer **volgende**.

    ![Gegevensset kiezen](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. Kies een naam waaronder u de gegevensset wilt registreren en de gegevens eventueel kunt filteren met behulp van de beschik bare filters. In dit geval filtert u voor de gegevensset van de **open bare feest dagen** de periode tot één jaar en de land code naar alleen de Verenigde Staten. Selecteer **Maken**.

    ![Gegevensset-para meters instellen en gegevensset maken](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    De gegevensset is nu beschikbaar in uw werk ruimte onder **gegevens sets**. U kunt deze op dezelfde manier gebruiken als andere gegevens sets die u hebt gemaakt.


## <a name="access-datasets-for-your-experiments"></a>Toegang tot gegevens sets voor uw experimenten

Gebruik uw gegevens sets in uw machine learning experimenten voor trainings ML-modellen. Meer [informatie over het trainen van gegevens sets](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Voorbeeldnotebooks

Voor voor beelden en demonstraties van de functionaliteit van open gegevens sets raadpleegt u deze [notitie blokken](https://github.com/Azure/OpenDatasetsNotebooks/tree/master/tutorials).

## <a name="next-steps"></a>Volgende stappen

* [Train een model met gegevens sets](../machine-learning/how-to-train-with-datasets.md).

* [Een Azure machine learning-gegevensset maken](../machine-learning/how-to-create-register-datasets.md).



