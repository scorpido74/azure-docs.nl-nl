---
title: Log ML experimenten & metrische gegevens
titleSuffix: Azure Machine Learning
description: Bewaak uw Azure ML experimenten en bewaak metrische uitvoerings gegevens om het proces voor het maken van het model te verbeteren. Voeg logboek registratie toe aan uw trainings script met behulp van run. log, run. start_logging of ScriptRunConfig.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 44fe71f575a32ccc1a687bc87793cb6a8b6508a9
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650631"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>Logboek registratie inschakelen in azure ML-trainings uitvoeringen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Met de Azure Machine Learning python-SDK kunt u real-time informatie vastleggen met behulp van zowel het standaard python-logboek registratie pakket als de SDK-specifieke functionaliteit. U kunt lokaal vastleggen en logboeken verzenden naar uw werk ruimte in de portal.

Logboeken kunnen u helpen bij het onderzoeken van fouten en waarschuwingen, of bij het volgen van prestatie gegevens, zoals para meters en model prestaties. In dit artikel leert u hoe u logboek registratie inschakelt in de volgende scenario's:

> [!div class="checklist"]
> * Interactieve trainings sessies
> * Trainings taken verzenden met behulp van ScriptRunConfig
> * Systeem eigen `logging` instellingen voor python
> * Logboek registratie van aanvullende bronnen


> [!TIP]
> In dit artikel leest u hoe u het model trainings proces bewaken. Als u geïnteresseerd bent in het bewaken van het resource gebruik en gebeurtenissen van Azure machine learning, zoals quota's, voltooide trainings uitvoeringen of voltooide model implementaties, raadpleegt u [bewaking Azure machine learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Gegevenstypen

U kunt meerdere gegevens typen vastleggen, waaronder scalaire waarden, lijsten, tabellen, afbeeldingen, directory's en meer. Zie de [referentie pagina](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true)voor het uitvoeren van klassen voor meer informatie en voor beelden van python-code voor verschillende gegevens typen.

## <a name="interactive-logging-session"></a>Interactieve logboek registratie sessie

Interactieve logboek registratie sessies worden doorgaans gebruikt in notebook omgevingen. De methode [experiment. start_logging ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-) start een interactieve logboek registratie sessie. Alle metrische gegevens die tijdens de sessie zijn geregistreerd, worden toegevoegd aan het uitvoerings record in het experiment. De methode [Run. complete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) beëindigt de sessies en markeert de uitvoering als voltooid.

## <a name="scriptrunconfig-logs"></a>ScriptRunConfig-logboeken

In deze sectie leert u hoe u logboek registratie code kunt toevoegen in ScriptConfig-uitvoeringen. U kunt de [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) -klasse gebruiken om scripts en omgevingen te integreren voor Herhaal bare uitvoeringen. U kunt deze optie ook gebruiken om een widget Visual Jupyter-notitie blokken weer te geven voor bewaking.

In dit voor beeld wordt een parameter opruiming uitgevoerd op basis van alfa waarden en worden de resultaten vastgelegd met de methode [Run. log ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#&preserve-view=truelog-name--value--description----) .

1. Maak een trainings script dat de logica voor logboek registratie bevat `train.py` .

   [! code-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Verzend het ```train.py``` script om uit te voeren in een door de gebruiker beheerde omgeving. De volledige script-map wordt verzonden voor training.

   [! notebook-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? name = src)] [! notebook-python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? name = run)]

    Met de `show_output` para meter wordt uitgebreide logboek registratie ingeschakeld, waarmee u de details van het trainings proces en informatie over externe bronnen of reken doelen kunt zien. Gebruik de volgende code om uitgebreide logboek registratie in te scha kelen wanneer u het experiment verzendt.

```python
run = exp.submit(src, show_output=True)
```

U kunt ook dezelfde para meter in de `wait_for_completion` functie gebruiken bij de resulterende uitvoering.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Systeem eigen python-logboek registratie

Sommige Logboeken in de SDK bevatten mogelijk een fout die aangeeft dat u het logboek registratie niveau wilt instellen op fout opsporing. Als u het logboek registratie niveau wilt instellen, voegt u de volgende code toe aan het script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Aanvullende logboek registratie bronnen

Azure Machine Learning kunnen ook gegevens van andere bronnen tijdens de training registreren, zoals geautomatiseerde machine learning uitvoeringen, of docker-containers die de taken uitvoeren. Deze logboeken worden niet gedocumenteerd, maar als u problemen ondervindt en contact opneemt met micro soft ondersteuning, kunnen ze deze logboeken gebruiken tijdens het oplossen van problemen.

Voor informatie over het vastleggen van metrische gegevens in Azure Machine Learning Designer (preview), Zie [metrische gegevens vastleggen in de ontwerp functie (preview-versie)](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Voorbeeldnotebooks

In de volgende notitie blokken worden concepten in dit artikel gedemonstreerd:
* [instructies-to-use-azureml/training/trein-on-Local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [instructies voor het gebruik van azureml/track-and-monitor-experimenten/logging-API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het gebruik van Azure Machine Learning:

* Informatie over het [vastleggen van metrische gegevens in azure machine learning Designer (preview)](how-to-track-designer-experiments.md).

* Bekijk een voor beeld van hoe u het beste model registreert en implementeert in de zelf studie, [een classificatie model voor installatie kopieën traint met Azure machine learning](tutorial-train-models-with-aml.md).
