---
title: 'Treinclusteringmodel: modulereferentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Treinclusteringmodel in Azure Machine Learning om clustermodellen te trainen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c29baf3cdda998a2ab78c84f3311b84d37086bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477422"
---
# <a name="train-clustering-model"></a>Clustermodel trainen

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een clustermodel te trainen.

De module maakt gebruik van een ongetraind clustermodel dat u al hebt geconfigureerd met de [clusteringmodule K-Means](k-means-clustering.md) en traint het model met behulp van een gelabelde of niet-gelabelde gegevensset. De module maakt zowel een getraind model dat u gebruiken voor voorspelling, als een set clustertoewijzingen voor elk geval in de trainingsgegevens.

> [!NOTE]
> Een clusteringmodel kan niet worden getraind met behulp van de [trainmodelmodule,](train-model.md) de algemene module voor het trainen van machine learning-modellen. Dat komt omdat [Train Model](train-model.md) werkt alleen met onder toezicht leren algoritmen. K-means en andere clustering algoritmen maken ongecontroleerd leren mogelijk, wat betekent dat het algoritme kan leren van niet-gelabelde gegevens.  
  
## <a name="how-to-use-train-clustering-model"></a>Treinclusteringmodel gebruiken  

1.  Voeg de module **Treinclusteringmodel** toe aan uw pijplijn in de ontwerper. U vindt de module onder **Machine Learning Modules,** in de categorie **Trein.**  
  
2. Voeg de [clustermodule K-Means](k-means-clustering.md) of een andere aangepaste module toe die een compatibel clustermodel maakt en stel de parameters van het clustermodel in.  
    
3.  Voeg een trainingsgegevensset toe aan de rechterinvoer van **Train Clustering Model**.
  
5.  Selecteer **in Kolomset**de kolommen in de gegevensset die u wilt gebruiken in het bouwen van clusters. Zorg ervoor dat u kolommen selecteert die goede functies bieden: vermijd bijvoorbeeld het gebruik van id's of andere kolommen met unieke waarden of kolommen met dezelfde waarden.

    Als er een label beschikbaar is, u het als functie gebruiken of het weglaten.  
  
6. Selecteer de **optie, Controleer alleen op toevoegen of uitvinken voor resultaat**als u de trainingsgegevens samen met het nieuwe clusterlabel wilt uitvoeren.

    Als u deze optie deselecteert, worden alleen de clustertoewijzingen uitgevoerd. 

7. Verzend de pijplijn of klik op de module **Clustering model trainen** en selecteer **Geselecteerd uitvoeren**.  
  
### <a name="results"></a>Resultaten

Na de opleiding is voltooid:

+ Als u een momentopname van het getrainde model wilt opslaan, selecteert u het tabblad **Uitvoer** in het rechterdeelvenster van de **module Treinmodel.** Selecteer het pictogram **Gegevensset registreren** om het model op te slaan als een herbruikbare module.

+ Als u scores uit het model wilt genereren, gebruikt u [Gegevens toewijzen aan clusters](assign-data-to-clusters.md).

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 