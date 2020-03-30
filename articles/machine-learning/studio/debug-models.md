---
title: Uw model debuggen
titleSuffix: ML Studio (classic) - Azure
description: Fouten van treinmodel- en scoremodelmodules in Azure Machine Learning Studio (klassiek) opsporen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 910e788830ec55b610a9234a8c8ac75dda1ea189
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218106"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Uw model debuggen in Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Wanneer u een model uitvoert, u de volgende fouten tegenkomen:

* de [module Treinmodel][train-model] produceert een fout 
* de module [Model score][score-model] produceert onjuiste resultaten 

In dit artikel worden mogelijke oorzaken voor deze fouten uitgelegd.


## <a name="train-model-module-produces-an-error"></a>Train Model Module produceert een fout

![image1](./media/debug-models/train_model-1.png)

De [Train Model][train-model] Module verwacht twee ingangen:

1. Het type machine learning-model uit de verzameling modellen van Azure Machine Learning Studio (klassiek).
2. De trainingsgegevens met een opgegeven kolom Label die de te voorspellen variabele opgeeft (de andere kolommen worden verondersteld Eigenschappen te zijn).

Deze module kan in de volgende gevallen een fout veroorzaken:

1. De kolom Label is onjuist opgegeven. Dit kan gebeuren als er meer dan één kolom is geselecteerd als label of als een onjuiste kolomindex is geselecteerd. Het tweede geval is bijvoorbeeld van toepassing als een kolomindex van 30 wordt gebruikt met een invoergegevensset met slechts 25 kolommen.

2. De gegevensset bevat geen functiekolommen. Als de invoergegevensset bijvoorbeeld slechts één kolom heeft, die is gemarkeerd als de kolom Label, zijn er geen functies waarmee u het model bouwen. In dit geval produceert de module [Treinmodel][train-model] een fout.

3. De invoergegevensset (Features of Label) bevat Oneindigheid als waarde.

## <a name="score-model-module-produces-incorrect-results"></a>Scoremodelmodule levert onjuiste resultaten op

![image2](./media/debug-models/train_test-2.png)

In een typisch trainings-/testexperiment voor begeleid leren verdeelt de module [Split Data][split] de oorspronkelijke gegevensset in twee delen: een onderdeel wordt gebruikt om het model te trainen en een onderdeel wordt gebruikt om te scoren hoe goed het getrainde model presteert. Het getrainde model wordt vervolgens gebruikt om de testgegevens te scoren, waarna de resultaten worden geëvalueerd om de nauwkeurigheid van het model te bepalen.

De module [Scoremodel][score-model] vereist twee ingangen:

1. Een getrainde modeloutput van de [Train Model][train-model] module.
2. Een scoregegevensset die verschilt van de gegevensset die wordt gebruikt om het model te trainen.

Het is mogelijk dat, hoewel het experiment slaagt, de module [Scoremodel][score-model] onjuiste resultaten oplevert. Verschillende scenario's kunnen dit probleem veroorzaken:

1. Als het opgegeven label categorisch is en een regressiemodel is getraind op de gegevens, wordt een onjuiste uitvoer geproduceerd door de module [Scoremodel.][score-model] Dit komt omdat regressie een continue responsvariabele vereist. In dit geval zou het beter zijn om een classificatiemodel te gebruiken. 

2. Als een classificatiemodel is getraind op een gegevensset met zwevende puntennummers in de kolom Label, kan dit ongewenste resultaten opleveren. Dit komt omdat classificatie een discrete responsvariabele vereist die alleen waarden toestaat die variëren over een eindige en kleine reeks klassen.

3. Als de scoregegevensset niet alle functies bevat die worden gebruikt om het model te trainen, produceert het [scoremodel][score-model] een fout.

4. Als een rij in de gegevensset voor het scoren een ontbrekende waarde of een oneindige waarde bevat voor een van de functies, produceert het [scoremodel][score-model] geen uitvoer die overeenkomt met die rij.

5. Het [scoremodel][score-model] kan identieke uitvoer produceren voor alle rijen in de scoregegevensset. Dit kan bijvoorbeeld gebeuren bij een poging tot classificatie met behulp van Decision Forests als het minimumaantal monsters per bladknooppunt wordt gekozen om meer te zijn dan het aantal beschikbare opleidingsvoorbeelden.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

