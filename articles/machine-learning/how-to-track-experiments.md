---
title: Logboekregistratie van ML-experimenten en metrische gegevens
titleSuffix: Azure Machine Learning
description: Bewaak uw Azure ML experimenten en metrische uitvoeringsgegevens om het proces voor het maken van het model te verbeteren. Voeg logboekregistratie toe aan uw trainingsscript met behulp van run.log, Run.start_logging of ScriptRunConfig.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 7eaa2fbe6033f801a252f6f2c7afa5eb726bce2f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318242"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>Logboekregistratie inschakelen in Azure ML-trainingsuitvoeringen


Met de Azure Machine Learning Python-SDK kunt u in realtime informatie vastleggen met behulp van zowel het standaardpakket voor logboekregistratie van Python als de specifieke functionaliteit van de SDK. U kunt logboeken lokaal opslaan en logboeken verzenden naar uw werkruimte in de portal.

Logboeken kunnen u helpen bij het diagnosticeren van fouten en waarschuwingen, of bij het bijhouden van metrische gegevens, zoals parameters en modelprestaties. In dit artikel leert u hoe u logboekregistratie inschakelt in de volgende scenario's:

> [!div class="checklist"]
> * Interactieve trainingssessies
> * Trainingstaken verzenden met behulp van ScriptRunConfig
> * Systeemeigen Python-instellingen voor `logging`
> * Logboekregistratie van aanvullende bronnen


> [!TIP]
> In dit artikel leest u hoe u het modeltrainingsproces kunt bewaken. Zie [Azure Machine Learning bewaken](monitor-azure-machine-learning.md) als u geïnteresseerd bent in het bewaken van het resourcegebruik en gebeurtenissen van Azure Machine Learning, zoals quota's, voltooide trainingsuitvoeringen of voltooide implementaties van modellen.

## <a name="data-types"></a>Gegevenstypen

U kunt meerdere gegevenstypen vastleggen, waaronder scalaire waarden, lijsten, tabellen, afbeeldingen, directory's en meer. Zie de [referentiepagina voor Run-klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) voor meer informatie en Python-codevoorbeelden voor verschillende gegevenstypen.

## <a name="interactive-logging-session"></a>Interactieve logboekregistratiesessie

Interactieve logboekregistratiesessies worden doorgaans gebruikt in notebookomgevingen. Met de methode [Experiment.start_logging()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truestart-logging--args----kwargs-) wordt een interactieve logboekregistratiesessie gestart. Alle metrische gegevens die tijdens de sessie zijn geregistreerd, worden toegevoegd aan het uitvoeringsrecord in het experiment. Met de methode [run.complete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truecomplete--set-status-true-) worden de sessies beëindigd en de uitvoering als voltooid gemarkeerd.

## <a name="scriptrun-logs"></a>ScriptRun-logboeken

In dit gedeelte leert u hoe u logboekcode in uitvoeringen toevoegt die zijn gemaakt toen de ScriptRunConfig werd geconfigureerd. Met de klasse [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true)-kunt u scripts en omgevingen inkapselen voor herhaalbare uitvoeringen. U kunt deze optie ook gebruiken om een visuele Jupyter Notebook-widget weer te geven voor bewakingsdoeleinden.

In dit voorbeeld wordt een parameteropruiming uitgevoerd op alfawaarden en worden de resultaten vastgelegd met de methode [run.log()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truelog-name--value--description----).

1. Maak een trainingsscript dat de logica voor logboekregistratie bevat, `train.py`.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Verzend het script ```train.py``` voor uitvoering in een door de gebruiker beheerde omgeving. De volledige scriptmap wordt verzonden voor training.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    Met de parameter `show_output` wordt uitgebreide logboekregistratie ingeschakeld, zodat u zowel details van het trainingsproces als informatie over externe resources of rekendoelen kunt bekijken. Gebruik de volgende code om uitgebreide logboekregistratie in te schakelen wanneer u het experiment verzendt.

```python
run = exp.submit(src, show_output=True)
```

U kunt ook dezelfde parameter gebruiken in de functie `wait_for_completion` van de resulterende uitvoering.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Systeemeigen logboekregistratie van Python

Sommige logboeken in de SDK bevatten mogelijk een foutmelding dat u het logboekregistratieniveau moet instellen op DEBUG (fouten opsporen). Als u het logboekregistratieniveau wilt instellen, voegt u de volgende code toe aan het script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Aanvullende bronnen voor logboekregistratie

Met Azure Machine Learning kunnen tijdens de training ook gegevens van andere bronnen worden geregistreerd, zoals geautomatiseerde machine learning-uitvoeringen, of Docker-containers die de taken uitvoeren. Deze logboeken worden niet gedocumenteerd, maar als u problemen ondervindt en contact opneemt met Microsoft-ondersteuning, kunnen ze deze logboeken gebruiken tijdens het oplossen van problemen.

Raadpleeg [Hoe kan ik metrische gegevens registreren in de ontwerpfunctie](how-to-track-designer-experiments.md) voor informatie over het vastleggen van metrische gegevens in de Azure Machine Learning-ontwerpfunctie

## <a name="example-notebooks"></a>Voorbeeldnotebooks

In de volgende notebooks worden concepten in dit artikel gedemonstreerd:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

Raadpleeg deze artikelen voor meer informatie over het gebruik van Azure Machine Learning:

* Meer informatie over het [vastleggen van metrische gegevens in de Azure Machine Learning-ontwerpfunctie](how-to-track-designer-experiments.md).

* Bekijk een voorbeeld van hoe u het beste model registreert en implementeert in de zelfstudie [Een afbeeldingsclassificatiemodel trainen met Azure Machine Learning](tutorial-train-models-with-aml.md).
