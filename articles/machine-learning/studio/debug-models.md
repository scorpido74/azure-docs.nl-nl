---
title: Fouten opsporen in uw model
titleSuffix: ML Studio (classic) - Azure
description: Fout opsporing van fouten die worden gegenereerd door de modules Train model en score model in Azure Machine Learning Studio (klassiek).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 910e788830ec55b610a9234a8c8ac75dda1ea189
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79218106"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Fouten opsporen in uw model in Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Bij het uitvoeren van een model kunt u de volgende fouten uitvoeren:

* Er treedt een fout op in de module [Train model][train-model] 
* de module [score model][score-model] levert onjuiste resultaten op 

In dit artikel worden mogelijke oorzaken voor deze fouten uitgelegd.


## <a name="train-model-module-produces-an-error"></a>Er wordt een fout gegenereerd door de Train model module

![image1](./media/debug-models/train_model-1.png)

Voor de module [Train model][train-model] worden twee invoer waarden verwacht:

1. Het type machine learning model uit de verzameling van modellen die door Azure Machine Learning Studio (klassiek) worden gegeven.
2. De opleidings gegevens met een opgegeven label kolom waarmee de variabele wordt opgegeven die moet worden voor speld (de andere kolommen worden verondersteld functies te zijn).

Deze module kan in de volgende gevallen een fout veroorzaken:

1. De label kolom is onjuist opgegeven. Dit kan gebeuren als er meer dan één kolom is geselecteerd als label of als er een onjuiste kolom index is geselecteerd. Zo zou het tweede geval van toepassing zijn als een kolom index van 30 wordt gebruikt met een invoer gegevensset die slechts 25 kolommen bevat.

2. De gegevensset bevat geen functie kolommen. Als de invoer gegevensset bijvoorbeeld slechts één kolom bevat die is gemarkeerd als de kolom Label, zijn er geen functies waarmee het model kan worden gemaakt. In dit geval treedt er een fout op in de module [Train model][train-model] .

3. De invoer gegevensset (kenmerken of label) bevat oneindig als waarde.

## <a name="score-model-module-produces-incorrect-results"></a>De module score model levert onjuiste resultaten op

![image2](./media/debug-models/train_test-2.png)

In een typisch voor beeld-en test experiment voor het leren op Super visie splitst de module [Split data][split] de oorspronkelijke gegevensset in twee delen: één deel wordt gebruikt om het model te trainen en één deel wordt gebruikt om te bepalen hoe goed het getrainde model werkt. Het getrainde model wordt vervolgens gebruikt om de test gegevens te scoren, waarna de resultaten worden geëvalueerd om de nauw keurigheid van het model te bepalen.

Voor de module [score model][score-model] zijn twee invoer waarden vereist:

1. Een getrainde model uitvoer van de module [Train model][train-model] .
2. Een score gegevensset die verschilt van de gegevensset die wordt gebruikt om het model te trainen.

Het is mogelijk dat zelfs als het experiment slaagt, de module [score model][score-model] onjuiste resultaten oplevert. Dit probleem kan worden veroorzaakt door verschillende scenario's:

1. Als het opgegeven label categorische is en een regressie model wordt getraind op de gegevens, wordt er een onjuiste uitvoer gemaakt door de module [score model][score-model] . Dit komt doordat voor regressie een continue reactie variabele is vereist. In dit geval is het beter om een classificatie model te gebruiken. 

2. Als een classificatie model wordt getraind op een gegevensset met drijvende-komma getallen in de kolom Label, kan dit ook ongewenste resultaten opleveren. Dit komt omdat de classificatie een discrete reactie variabele vereist die alleen waarden toestaat die zijn toegestaan voor een eindige, en kleine set klassen.

3. Als de Score-gegevensset niet alle functies bevat die worden gebruikt om het model te trainen, genereert het [score model][score-model] een fout.

4. Als een rij in de Score-gegevensset een ontbrekende waarde of een oneindige waarde voor een van de functies bevat, produceert het [score model][score-model] geen uitvoer die overeenkomt met die rij.

5. Het [score model][score-model] kan identieke uitvoer genereren voor alle rijen in de gegevensset van de score. Dit kan bijvoorbeeld gebeuren wanneer er wordt geclassificeerd met beslissings bossen als het minimum aantal steek proeven per blad knooppunt meer is dan het aantal beschik bare trainings voorbeelden.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

