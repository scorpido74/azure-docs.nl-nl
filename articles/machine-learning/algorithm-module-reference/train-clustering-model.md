---
title: 'Train clustering model: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module clustering model leren in Azure Machine Learning voor het trainen van cluster modellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 7053fab28b4a231c92f31e344cf09ffef3a6b146
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152105"
---
# <a name="train-clustering-model"></a>Clustermodel trainen

In dit artikel wordt een module in Azure Machine Learning Designer (preview) beschreven.

Gebruik deze module om een cluster model te trainen.

De module heeft een niet-getraind cluster model dat u al hebt geconfigureerd met de [K-betekent clustering](k-means-clustering.md) module en traint het model met behulp van een gelabelde of niet-gelabelde gegevensset. De module maakt zowel een getraind model dat u voor de voor spelling kunt gebruiken als voor elk geval een set cluster toewijzingen in de trainings gegevens.

> [!NOTE]
> Een cluster model kan niet worden getraind met behulp van de [Train model](train-model.md) module, de algemene module voor het trainen van machine learning modellen. Dat komt omdat [Train model](train-model.md) alleen werkt met Super visie-algoritmen. Met K-betekent dat en andere cluster algoritmen onbewaakt leren toestaan, wat inhoudt dat het algoritme kan leren van niet-gelabelde gegevens.  
  
## <a name="how-to-use-train-clustering-model"></a>Het gebruik van Train clustering model  

1.  Voeg de module **clustering model trainen** toe aan uw pijp lijn in de ontwerp functie. U kunt de module vinden onder **machine learning-modules**in de categorie **trein** .  
  
2. Voeg de [cluster module K-betekent](k-means-clustering.md) toe, of een andere aangepaste module die een compatibel cluster model maakt en stel de para meters van het cluster model in.  
    
3.  Een trainings gegevensset koppelen aan de rechter invoer van het **cluster model voor Train**.
  
5.  Selecteer in **kolom set**de kolommen uit de gegevensset die u wilt gebruiken voor het maken van clusters. Zorg ervoor dat u kolommen selecteert die goede functies maken: Vermijd het gebruik van Id's of andere kolommen met unieke waarden of kolommen met dezelfde waarden.

    Als er een label beschikbaar is, kunt u het gebruiken als een functie of dit laten.  
  
6. Selecteer de optie, **Schakel het selectie vakje toevoegen of alleen uitschakelen voor resultaat**in als u de trainings gegevens samen met het nieuwe cluster label wilt uitvoeren.

    Als u deze optie uitschakelt, worden alleen de cluster toewijzingen uitgevoerd. 

7. Voer de pijp lijn uit of klik op de module **clustering model trainen** en selecteer **geselecteerde uitvoeren**.  
  
### <a name="results"></a>Resultaten

Nadat de training is voltooid:

+ Als u een moment opname van het getrainde model wilt opslaan, selecteert u het tabblad **uitvoer** in het rechterdeel venster van de module **Train model** . Selecteer het pictogram **gegevensset registreren** om het model als een herbruikbare module op te slaan.

+ Als u scores wilt genereren op basis van het model, gebruikt u [gegevens toewijzen aan clusters](assign-data-to-clusters.md).

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 