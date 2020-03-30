---
title: Wat is gedistribueerde training?
titleSuffix: Azure Machine Learning
description: Meer informatie over gedistribueerde training en hoe Azure Machine Learning dit ondersteunt.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: a0d5bf795e4759a105b9a235770f37aa10bd6751
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385543"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Gedistribueerde training met Azure Machine Learning

In dit artikel leert u over gedistribueerde training en hoe Azure Machine Learning dit ondersteunt voor deep learning-modellen. 

In gedistribueerde training wordt de werklast om een model te trainen opgesplitst en gedeeld tussen meerdere miniprocessors, zogenaamde worker nodes. Deze werkknooppunten werken parallel om de modeltraining te versnellen. Gedistribueerde training kan worden gebruikt voor traditionele ML-modellen, maar is beter geschikt voor rekenkracht en tijdintensieve taken, zoals [deep learning](concept-deep-learning-vs-machine-learning.md) voor het trainen van diepe neurale netwerken.

## <a name="deep-learning-and-distributed-training"></a>Deep learning en gedistribueerde training 

Er zijn twee belangrijke soorten gedistribueerde opleidingen: [dataparallellisme](#data-parallelism) en [modelparallellisme.](#model-parallelism) Voor gedistribueerde training over deep learning-modellen ondersteunt de [Azure Machine Learning SDK in Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) integraties met populaire frameworks, PyTorch en TensorFlow. Beide frameworks maken gebruik van dataparallellisme voor gedistribueerde training en kunnen gebruikmaken van [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) voor het optimaliseren van rekensnelheden. 

* [Gedistribueerde training met PyTorch](how-to-train-pytorch.md#distributed-training)

* [Gedistribueerde training met TensorFlow](how-to-train-tensorflow.md#distributed-training)

Voor ML-modellen waarvoor geen gedistribueerde training nodig is, raadpleegt u [treinmodellen met Azure Machine Learning](concept-train-machine-learning-model.md#python-sdk) voor de verschillende manieren om modellen te trainen met de Python SDK.

## <a name="data-parallelism"></a>Parallellisme van gegevens

Data parallellisme is het gemakkelijkst te implementeren van de twee gedistribueerde trainingsbenaderingen, en is voldoende voor de meeste use cases.

In deze benadering worden de gegevens verdeeld in partities, waarbij het aantal partities gelijk is aan het totale aantal beschikbare knooppunten in het compute cluster. Het model wordt gekopieerd in elk van deze werkknooppunten en elke werknemer werkt op zijn eigen subset van de gegevens. Houd er rekening mee dat elk knooppunt de capaciteit moet hebben om het model dat wordt getraind te ondersteunen, dat wil zeggen dat het model volledig op elk knooppunt moet passen.

Elk knooppunt berekent onafhankelijk de fouten tussen zijn voorspellingen voor zijn trainingsmonsters en de gelabelde uitvoer. Elk knooppunt werkt op zijn beurt zijn model bij op basis van de fouten en moet alle wijzigingen aan de andere knooppunten doorgeven om hun bijbehorende modellen bij te werken. Dit betekent dat de werknemersknooppunten de modelparameters of verlopen moeten synchroniseren aan het einde van de batchberekening om ervoor te zorgen dat ze een consistent model trainen. 

## <a name="model-parallelism"></a>Modelparallelisme

In modelparallellisme, ook wel netwerkparallellisme genoemd, wordt het model gesegmenteerd in verschillende onderdelen die gelijktijdig in verschillende knooppunten kunnen worden uitgevoerd en elk model wordt uitgevoerd op dezelfde gegevens. De schaalbaarheid van deze methode is afhankelijk van de mate van taakparallelisatie van het algoritme en het is complexer te implementeren dan dataparallellisme. 

In modelparallellisme hoeven worker nodes alleen de gedeelde parameters te synchroniseren, meestal eenmaal voor elke voorwaartse of achterwaartse propagatiestap. Ook grotere modellen zijn geen probleem, omdat elk knooppunt werkt op een subsectie van het model op dezelfde trainingsgegevens.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [instellen van trainingsomgevingen](how-to-set-up-training-targets.md) met de Python SDK.
* Zie het [referentiearchitectuurscenario](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning)voor een technisch voorbeeld .
* [Train ML-modellen met TensorFlow](how-to-train-tensorflow.md).
* [Train ML modellen met PyTorch](how-to-train-pytorch.md). 