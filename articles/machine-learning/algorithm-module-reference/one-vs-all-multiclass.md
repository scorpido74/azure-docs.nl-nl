---
title: One-vs-All Multiclass
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de One-vs-All Multiclass-module in Azure Machine Learning om een classificatiemodel met meerdere klassen te maken op basis van een ensemble van binaire classificatiemodellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 29934758ab729e0fb888c10b7f834da3d0bf7fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456077"
---
# <a name="one-vs-all-multiclass"></a>One-vs-All Multiclass

In dit artikel wordt beschreven hoe u de One-vs-All Multiclass-module gebruiken in Azure Machine Learning-ontwerper (voorbeeld). Het doel is om een classificatiemodel te maken dat meerdere klassen kan voorspellen, met behulp van de *one-versus-all* benadering.

Deze module is handig voor het maken van modellen die drie of meer mogelijke uitkomsten voorspellen, wanneer de uitkomst afhankelijk is van continue of categorische voorspellervariabelen. Met deze methode u ook binaire classificatiemethoden gebruiken voor problemen waarvoor meerdere uitvoerklassen nodig zijn.

### <a name="more-about-one-versus-all-models"></a>Meer over een-tegen-alle modellen

Sommige classificatiealgoritmen maken het gebruik van meer dan twee klassen door ontwerp toe. Anderen beperken de mogelijke uitkomsten tot een van de twee waarden (een binair of tweeklassenmodel). Maar zelfs binaire classificatiealgoritmen kunnen worden aangepast voor classificatietaken van meerdere klassen door middel van verschillende strategieën. 

Deze module implementeert de één-versus-alle methode, waarbij een binair model wordt gemaakt voor elk van de meerdere uitvoerklassen. De module beoordeelt elk van deze binaire modellen voor de individuele klassen aan de hand van de aanvulling (alle andere klassen in het model) alsof het een binaire classificatie probleem. De module voert vervolgens voorspelling uit door deze binaire classificaties uit te voeren en de voorspelling te kiezen met de hoogste betrouwbaarheidsscore.  

In wezen creëert de module een ensemble van individuele modellen en voegt vervolgens de resultaten samen, om één model te maken dat alle klassen voorspelt. Elke binaire classificatie kan worden gebruikt als basis voor een een-versus-alle model.  

Stel dat u een [Vectormachine voor ondersteuning in twee klassen](two-class-support-vector-machine.md) configureert en dit als input levert voor de One-vs-All Multiclass-module. De module zou twee-klasse ondersteuning vector machine modellen voor alle leden van de output klasse. Het zou dan de een-versus-alle methode toepassen om de resultaten voor alle klassen te combineren.  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>De classificatie One-vs-All Multiclass configureren  

Deze module maakt een ensemble van binaire classificatiemodellen om meerdere klassen te analyseren. Als u deze module wilt gebruiken, moet u eerst een *binair classificatiemodel* configureren en trainen. 

U verbindt het binaire model met de One-vs-All Multiclass-module. Vervolgens train je het ensemble van modellen met behulp van [Train Model](train-model.md) met een gelabelde trainingsdataset.

Wanneer u de modellen combineert, maakt One-vs-All Multiclass meerdere binaire classificatiemodellen, optimaliseert het algoritme voor elke klasse en voegt u de modellen vervolgens samen. De module doet deze taken, ook al kan de trainingsgegevensset meerdere klassewaarden hebben.

1. Voeg de One-vs-All Multiclass-module toe aan uw pijplijn in de ontwerper. U vindt deze module onder **Machine Learning - Initialiseren**, in de categorie **Classificatie.**

   De One-vs-All Multiclass classifier heeft geen eigen configureerbare parameters. Eventuele aanpassingen moeten worden gedaan in het binaire classificatiemodel dat wordt geleverd als invoer.

2. Voeg een binair classificatiemodel toe aan de pijplijn en configureer dat model. U bijvoorbeeld [tweeklasseondersteuningvectormachine](two-class-support-vector-machine.md) of [tweeklassenversterkte beslissingsstructuur](two-class-boosted-decision-tree.md)gebruiken.

3. Voeg de [module Treinmodel](train-model.md) toe aan uw pijplijn. Sluit de ongetrainde classificatie aan die de uitvoer van One-vs-All Multiclass is.

4. Op de andere invoer van [Train Model,](train-model.md)sluit een gelabelde trainingsgegevensset die meerdere klassewaarden heeft.

5. Verzend de pijplijn.

## <a name="results"></a>Resultaten

Nadat de training is voltooid, u het model gebruiken om voorspellingen van meerdere klassen te doen.

U de ongetrainde classificatie ook doorgeven aan [cross-validate model](cross-validate-model.md) voor cross-validatie tegen een gelabelde validatiegegevensset.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
