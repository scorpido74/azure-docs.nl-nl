---
title: Fouten opsporen in uw model
titleSuffix: ML Studio (classic) - Azure
description: Fout opsporing van fouten die worden gegenereerd door de modules Train model en score model in Azure Machine Learning Studio (klassiek).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 320eba16511036df77dcdbb7ddb628eaa34b2450
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153550"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Fouten opsporen in uw model in Azure Machine Learning Studio (klassiek)

Bij het uitvoeren van een model kunt u de volgende fouten uitvoeren:

* Er treedt een fout op in de module [Train model][train-model] 
* de module [score model][score-model] levert onjuiste resultaten op 

In dit artikel worden mogelijke oorzaken voor deze fouten uitgelegd.


## <a name="train-model-module-produces-an-error"></a>Module Train-Model, treedt een fout

![image1](./media/debug-models/train_model-1.png)

Voor de module [Train model][train-model] worden twee invoer waarden verwacht:

1. Het type machine learning model uit de verzameling van modellen die door Azure Machine Learning Studio (klassiek) worden gegeven.
2. De opleidings gegevens met een opgegeven label kolom waarmee de variabele wordt opgegeven die moet worden voor speld (de andere kolommen worden verondersteld functies te zijn).

Deze module kunt treedt er een fout in de volgende gevallen:

1. De kolom Label is onjuist opgegeven. Dit kan gebeuren als meer dan één kolom is geselecteerd als het Label of een onjuiste kolomindex is geselecteerd. Zo zou het tweede geval van toepassing zijn als een kolom index van 30 wordt gebruikt met een invoer gegevensset die slechts 25 kolommen bevat.

2. De gegevensset bevat geen functie-kolommen. Bijvoorbeeld, als de invoergegevensset slechts één kolom, dat is gemarkeerd als de kolom Label heeft, wordt er zijn geen functies waarmee het-model bouwt. In dit geval treedt er een fout op in de module [Train model][train-model] .

3. De invoergegevensset (functies of Label) bevat oneindig als een waarde.

## <a name="score-model-module-produces-incorrect-results"></a>Score Model-Module oplevert onjuiste resultaten

![image2](./media/debug-models/train_test-2.png)

In een typisch voor beeld-en test experiment voor het leren op Super visie splitst de module [Split data][split] de oorspronkelijke gegevensset in twee delen: één deel wordt gebruikt om het model te trainen en één deel wordt gebruikt om te bepalen hoe goed het getrainde model werkt. Het getrainde model wordt vervolgens gebruikt om de testgegevens, waarna de resultaten worden geëvalueerd om te bepalen van de nauwkeurigheid van het model te beoordelen.

Voor de module [score model][score-model] zijn twee invoer waarden vereist:

1. Een getrainde model uitvoer van de module [Train model][train-model] .
2. Een scoring gegevensset die verschilt van de gegevensset die wordt gebruikt voor het model te trainen.

Het is mogelijk dat zelfs als het experiment slaagt, de module [score model][score-model] onjuiste resultaten oplevert. Dit probleem kan worden veroorzaakt door verschillende scenario's:

1. Als het opgegeven label categorische is en een regressie model wordt getraind op de gegevens, wordt er een onjuiste uitvoer gemaakt door de module [score model][score-model] . Dit is omdat regressie een continue antwoord-variabele vereist. In dit geval is het beter geschikt om te gebruiken een model voor classificatie. 

2. Op dezelfde manier als een classificatie-model wordt getraind op een gegevensset met getallen met drijvende komma in de kolom Label, kan er ongewenste resultaten opleveren. Dit komt omdat de classificatie een discrete reactie variabele vereist die alleen waarden toestaat die zijn toegestaan voor een eindige, en kleine set klassen.

3. Als de Score-gegevensset niet alle functies bevat die worden gebruikt om het model te trainen, genereert het [score model][score-model] een fout.

4. Als een rij in de Score-gegevensset een ontbrekende waarde of een oneindige waarde voor een van de functies bevat, produceert het [score model][score-model] geen uitvoer die overeenkomt met die rij.

5. Het [score model][score-model] kan identieke uitvoer genereren voor alle rijen in de gegevensset van de score. Dit kan bijvoorbeeld gebeuren wanneer wordt geprobeerd classificatie met behulp van de beslissing Forests als het minimale aantal steekproeven per leaf-knooppunt is gekozen moet meer zijn dan het aantal voorbeelden van training beschikbaar.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

