---
title: One-vs-All Multiclass
titleSuffix: Azure Machine Learning service
description: Informatie over het gebruik van de module One-vs-All Multiclass in Azure Machine Learning-service om een classificatie model met een klasse te maken op basis van een ensemble van binaire classificatie modellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 9ded83dc5b8b8b98af66c8858214e8f82a4aa166
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518162"
---
# <a name="one-vs-all-multiclass"></a>One-vs-All Multiclass

In dit artikel wordt beschreven hoe u de **One-vs-all Multiclass** module inazure machine learning Designer (preview) gebruikt om een classificatie model te maken dat meerdere klassen kan voors pellen met behulp van de methode ' één vs. alle '.

Deze module is handig voor het maken van modellen die drie of meer mogelijke resultaten voors pellen, wanneer het resultaat afhankelijk is van continue of categorische prediction-variabelen. Met deze methode kunt u ook binaire classificatie methoden gebruiken voor problemen waarvoor meerdere uitvoer klassen zijn vereist.

### <a name="more-about-one-vs-all-models"></a>Meer informatie over een-VS-alle modellen

Hoewel sommige classificatie algoritmen het gebruik van meer dan twee klassen per ontwerp toestaan, beperken anderen de mogelijke resultaten voor een van de twee waarden (een binair of een model met twee klassen). Zelfs binaire classificatie algoritmen kunnen echter worden aangepast voor classificatie taken met meerdere klassen, met behulp van verschillende strategieën. 

Deze module implementeert de *methode vs. all*, waarin een binair model voor elk van de meerdere uitvoer klassen wordt gemaakt. Elk van deze binaire modellen voor de afzonderlijke klassen wordt beoordeeld aan de hand van een aanvulling (alle andere klassen in het model) alsof het een probleem met een binaire classificatie betreft. De voor spelling wordt vervolgens uitgevoerd door deze binaire classificaties uit te voeren en de voor spelling te kiezen met de hoogste betrouwbaarheids Score.  

In wezen wordt een ensemble van afzonderlijke modellen gemaakt en worden de resultaten vervolgens samengevoegd om één model te maken waarmee alle klassen worden voor speld. Een binaire classificatie kan daarom worden gebruikt als basis voor een model met één-VS-alles.  

 Stel bijvoorbeeld dat u een [twee klasse ondersteunings vector computer](two-class-support-vector-machine.md) model configureert en als invoer voor de module **One-vs-all Multiclass** levert. De module maakt vector-machine modellen met twee klassen ondersteuning voor alle leden van de klasse output en past vervolgens de methode één-vs-all toe om de resultaten voor alle klassen te combi neren.  

## <a name="how-to-configure-the-one-vs-all-classifier"></a>De classificatie van één VS-alles configureren  

Deze module maakt een ensemble van binaire classificatie modellen om meerdere klassen te analyseren. Daarom moet u eerst een **binair classificatie** model configureren en trainen om deze module te kunnen gebruiken. 

Vervolgens verbindt u het binaire model met **One-vs-all Multiclass** module en traint u de ensemble van modellen met behulp van [Train model](train-model.md) met een gelabelde trainings gegevensset.

Wanneer u de modellen combineert, zelfs als de gegevensset van de training meerdere klassen waarden bevat, maakt de **One-vs-all Multiclass** meerdere binaire classificatie modellen, optimaliseert de algoritme voor elke klasse en voegt de modellen vervolgens samen.

1. Voeg de **One-vs-all Multiclass** toe aan uw pijp lijn in de ontwerp functie. U kunt deze module vinden onder Machine Learning-Initialize, in de categorie **classificatie** .

    De classificatie van de **One-vs-all Multiclass** heeft geen Configureer bare para meters. Aanpassingen moeten worden uitgevoerd in het binaire classificatie model dat is opgegeven als invoer.

2. Voeg een binair classificatie model toe aan de pijp lijn en configureer dat model. U kunt bijvoorbeeld een [2-klasse support vector machine](two-class-support-vector-machine.md) of een [Geboostte beslissings structuur van twee](two-class-boosted-decision-tree.md)klassen gebruiken.

3. Voeg de module [Train model](train-model.md) toe aan uw pijp lijn en verbind de niet-getrainde classificatie die de uitvoer van **One-vs-all Multiclass**is.

4. Verbind op de andere invoer van het [trein model](train-model.md)een gelabelde trainings gegevensset met meerdere klassen waarden.

5. Voer de pijplijn uit.

## <a name="results"></a>Resultaten

Nadat de training is voltooid, kunt u het model gebruiken om voor spellingen voor multi klassen te maken.

U kunt ook de niet-getrainde classificatie door geven aan [kruislings valideren model](cross-validate-model.md) voor kruis validatie op basis van een gelabelde validatie gegevensset.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 
