---
title: 'Meerklasse versterkte beslissingsstructuur: modulereferentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Multiclass Boosted Decision Tree in Azure Machine Learning om een classificatie te maken met gelabelde gegevens.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920056"
---
# <a name="multiclass-boosted-decision-tree"></a>Versterkte beslissingsstructuur met meerdere klassen

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een machine learning-model te maken dat is gebaseerd op het algoritme voor gebooste beslissingsbomen.

Een versterkte beslissingsboom is een ensemble leermethode waarbij de tweede boom corrigeert voor de fouten van de eerste boom, de derde boom corrigeert voor de fouten van de eerste en tweede bomen, enzovoort. Voorspellingen zijn gebaseerd op het ensemble van bomen samen.

## <a name="how-to-configure"></a>Configureren 

Deze module maakt een ongetraind classificatiemodel. Omdat classificatie een begeleide leermethode is, hebt u een *gelabelde gegevensset* nodig met een labelkolom met een waarde voor alle rijen.

U dit type model trainen met behulp van het [treinmodel.](././train-model.md) 

1.  Voeg de module **Multiclass Boosted Decision Tree** toe aan uw pijplijn.

1.  Geef op hoe u het model wilt laten trainen door de optie **Trainer-modus maken in** te stellen.

    + **Eén parameter:** als u weet hoe u het model wilt configureren, u een specifieke set waarden als argumenten opgeven.
    
    + **Parameterbereik:** Selecteer deze optie als u niet zeker bent van de beste parameters en een parametersweep wilt uitvoeren. Selecteer een reeks waarden om over te herhalen en de [Tune Model Hyperparameters](tune-model-hyperparameters.md) herhalen over alle mogelijke combinaties van de instellingen die u hebt opgegeven om de hyperparameters te bepalen die de optimale resultaten opleveren.  

1. **Het maximum aantal bladeren per boom** beperkt het maximum aantal terminalknooppunten (bladeren) dat in elke boom kan worden gemaakt.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **Het minimumaantal monsters per bladknooppunt** geeft het aantal gevallen aan dat nodig is om een eindknooppunt (blad) in een boom te maken.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. **Leersnelheid** definieert de stapgrootte tijdens het leren. Voer een getal in tussen 0 en 1.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **Aantal bomen gebouwd** geeft het totale aantal beslissingbomen te creëren in het ensemble. Door het creëren van meer beslissingbomen, u mogelijk een betere dekking te krijgen, maar de opleiding tijd zal toenemen.

1. **Willekeurig getalzaad** stelt optioneel een niet-negatief geheel in dat moet worden gebruikt als de willekeurige zaadwaarde. Het opgeven van een zaad zorgt voor reproduceerbaarheid voor runs met dezelfde gegevens en parameters.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> Als u **De trainermodus maken** instelt op Eén **parameter,** verbindt u een gelabelde gegevensset en de module [Treinmodel.](./train-model.md)

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
