---
title: 'Beslissings structuur met een versterkte klasse: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module voor de beslissings structuur met de klasse met meer klassen in Azure Machine Learning om een classificatie te maken met behulp van gelabelde gegevens.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: cfe35f81526a729092edf522f693ccd18494d1ec
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137821"
---
# <a name="multiclass-boosted-decision-tree"></a>Versterkte beslissingsstructuur met meerdere klassen

In dit artikel wordt een module in Azure Machine Learning Designer (preview) beschreven.

Gebruik deze module om een machine learning model te maken dat is gebaseerd op het boosted Decision Trees-algoritme.

Een versterkte beslissings structuur is een ensemble leer methode waarbij de tweede structuur de fouten van de eerste structuur corrigeert, de derde structuur corrigeert voor de fouten van de eerste en tweede bomen, enzovoort. Voor spellingen zijn gebaseerd op de ensemble van bomen samen.

## <a name="how-to-configure"></a>Configureren 

Deze module maakt een niet-getraind classificatie model. Omdat classificatie een geclassificeerde leer methode is, hebt u een gegevensset met een *Label* nodig die een kolom Label bevat met een waarde voor alle rijen.

U kunt dit type model trainen door het Train- [model](././train-model.md)te gebruiken. 

1.  Voeg de module voor de **beslissings structuur met een hogere klasse** toe aan uw pijp lijn.

1.  Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.

    + **Eén para meter**: als u weet hoe u het model wilt configureren, kunt u een specifieke set waarden als argumenten opgeven.
    
    + **Parameter bereik**: Selecteer deze optie als u niet zeker weet wat de beste para meters zijn en u een parameter sweep wilt uitvoeren. Selecteer een bereik met waarden om over te sporen en het [model Hyper parameters](tune-model-hyperparameters.md) wordt herhaald op alle mogelijke combi Naties van de instellingen die u hebt verstrekt om de Hyper parameters te bepalen die de optimale resultaten opleveren.  

1. Het **maximum aantal Leaves per structuur** beperkt het maximum aantal Terminal knooppunten (bladeren) dat in een wille keurige structuur kan worden gemaakt.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **Minimum aantal steek proeven per blad knooppunt** geeft het aantal cases aan dat nodig is om een Terminal knooppunt (Leaf) in een structuur te maken.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. Met het **leer tempo** wordt de stap grootte gedefinieerd tijdens het leren. Voer een getal tussen 0 en 1 in.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **Aantal geconstrueerde structuren** geeft het totale aantal beslissings structuren aan dat in de ensemble moet worden gemaakt. Door meer beslissings structuren te maken, kunt u een betere dekking krijgen, maar wordt de trainings tijd verhoogd.

1. **Wille keurig getal Seed** stelt optioneel een niet-negatief geheel getal in dat moet worden gebruikt als de wille keurige Seed-waarde. Het opgeven van een Seed zorgt voor een reproduceer baarheid van alle uitvoeringen die dezelfde gegevens en para meters hebben.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

1. Het model trainen:

    + Als u de **modus trainer maken** instelt op **één para meter**, verbindt u een gecodeerde gegevensset en de module [Train model](train-model.md) .  
  
    + Als u de **modus trainer maken** instelt op het **parameter bereik**, verbindt u een gecodeerde gegevensset en traint u het model met behulp van [Hyper parameters model](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Als u een parameter bereik doorgeeft aan een [Train-model](train-model.md), wordt alleen de standaard waarde in de lijst met enkelvoudige para meters gebruikt.  
    > 
    > Als u één set parameter waarden doorgeeft aan de Hyper parameters-module van het model voor het [afstemmen](tune-model-hyperparameters.md) van een reeks instellingen voor elke para meter, worden de waarden genegeerd en worden de standaard waarden gebruikt voor de kenniser.  
    > 
    > Als u de optie voor het **parameter bereik** selecteert en één waarde voor een para meter opgeeft, wordt die enkele waarde die u hebt opgegeven overal in de sweep gebruikt, zelfs als andere para meters worden gewijzigd in een reeks waarden.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
