---
title: 'Train clustering model: module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module clustering model leren in Azure Machine Learning service om cluster modellen te trainen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: fc6d889fb9be40c98e1a2a0de6fddb29939f3b5d
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693655"
---
# <a name="train-clustering-model"></a>Clustermodel trainen

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om een cluster model te trainen.

De module heeft een niet-getraind cluster model dat u al hebt geconfigureerd met de [K-betekent clustering](k-means-clustering.md) module en traint het model met behulp van een gelabelde of niet-gelabelde gegevensset. De module maakt zowel een getraind model dat u voor de voor spelling kunt gebruiken als voor elk geval een set cluster toewijzingen in de trainings gegevens.

> [!NOTE]
> Een cluster model kan niet wordt getraind met behulp van de [Train model](train-model.md) module, de algemene module voor het trainen van machine learning modellen. Dat komt omdat [Train model](train-model.md) alleen werkt met Super visie-algoritmen. Met K-betekent dat en andere cluster algoritmen onbewaakt leren toestaan, wat inhoudt dat het algoritme kan leren van niet-gelabelde gegevens.  
  
## <a name="how-to-use-train-clustering-model"></a>Het gebruik van Train clustering model  
  
1.  Voeg de module **clustering model trainen** aan uw pijp lijn toe in Studio. U kunt de module vinden onder **machine learning-modules**in de categorie **trein** .  
  
2. Voeg de [cluster module K-betekent](k-means-clustering.md) toe, of een andere aangepaste module die een compatibel cluster model maakt en stel de para meters van het cluster model in.  
    
3.  Een trainings gegevensset koppelen aan de rechter invoer van het **cluster model voor Train**.
  
5.  Selecteer in **kolom set**de kolommen uit de gegevensset die u wilt gebruiken voor het maken van clusters. Zorg ervoor dat u kolommen selecteert die goede functies maken: Vermijd het gebruik van Id's of andere kolommen met unieke waarden of kolommen met dezelfde waarden.

    Als er een label beschikbaar is, kunt u het gebruiken als een functie of dit laten.  
  
6. Selecteer de optie, **Schakel het selectie vakje toevoegen of alleen uitschakelen voor resultaat**in als u de trainings gegevens samen met het nieuwe cluster label wilt uitvoeren.

    Als u deze optie uitschakelt, worden alleen de cluster toewijzingen uitgevoerd. 

7. Voer de pijp lijn uit of klik op de module **clustering model trainen** en selecteer **geselecteerde uitvoeren**.  
  
### <a name="results"></a>Resultaten

Nadat de training is voltooid:


+  Als u de waarden in de gegevensset wilt weer geven, klikt u met de rechter muisknop op de module, selecteert u **resultaat gegevens sets**en klikt u op **visualiseren**.

+ Als u het getrainde model wilt opslaan voor later hergebruik, klikt u met de rechter muisknop op de module, selecteert u **getraind model**en klikt u op **Opslaan als getraind model**.

+ Als u scores wilt genereren op basis van het model, gebruikt u [gegevens toewijzen aan clusters](assign-data-to-clusters.md).



## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 