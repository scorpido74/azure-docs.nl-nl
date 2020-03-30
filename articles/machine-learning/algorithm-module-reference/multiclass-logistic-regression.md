---
title: 'Multiclass Logistieke Regressie: Module Referentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de multiclass logistic regression-module in Azure Machine Learning om een logistiek regressiemodel te maken dat kan worden gebruikt om meerdere waarden te voorspellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: ca446b0ab67a8a202c1f4d505262660ac55f42db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456162"
---
# <a name="multiclass-logistic-regression-module"></a>Multiclass Logistieke Regressiemodule

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een logistiek regressiemodel te maken dat kan worden gebruikt om meerdere waarden te voorspellen.

Classificatie met behulp van logistieke regressie is een begeleide leermethode en vereist daarom een gelabelde dataset. U traint het model door het model en de gelabelde gegevensset te leveren als input voor een module zoals [Train Model](./train-model.md). Het getrainde model kan vervolgens worden gebruikt om waarden te voorspellen voor nieuwe invoervoorbeelden.

Azure Machine Learning biedt ook een [twee-klasse logistieke regressiemodule,](./two-class-logistic-regression.md) die geschikt is voor het classificeren van binaire of dichotomeuze variabelen.

## <a name="about-multiclass-logistic-regression"></a>Over multiclass logistieke regressie

Logistieke regressie is een bekende methode in statistieken die wordt gebruikt om de waarschijnlijkheid van een resultaat te voorspellen en is populair voor classificatietaken. Het algoritme voorspelt de waarschijnlijkheid van het optreden van een gebeurtenis door gegevens aan te passen aan een logistieke functie. 

In multiclass logistieke regressie kan de classificatie worden gebruikt om meerdere uitkomsten te voorspellen.

## <a name="configure-a-multiclass-logistic-regression"></a>Een multiclass logistieke regressie configureren

1. Voeg de **Multiclass Logistic Regression-module** toe aan de pijplijn.

2. Geef op hoe u het model wilt trainen door de optie **Trainer-modus maken in** te stellen.

    + **Eén parameter:** gebruik deze optie als u weet hoe u het model wilt configureren en een specifieke set waarden als argumenten wilt opgeven.

    + **Parameterbereik:** Selecteer deze optie als u niet zeker bent van de beste parameters en een parametersweep wilt uitvoeren. Selecteer een reeks waarden om over te herhalen en de [Tune Model Hyperparameters](tune-model-hyperparameters.md) herhalen over alle mogelijke combinaties van de instellingen die u hebt opgegeven om de hyperparameters te bepalen die de optimale resultaten opleveren.  

3. **Optimalisatietolerantie**, geef de drempelwaarde op voor optimizerconvergentie. Als de verbetering tussen iteraties lager is dan de drempelwaarde, stopt het algoritme en retourneert het huidige model.

4. **L1 regularisatiegewicht**, **L2-regularisatiegewicht**: Typ een te gebruiken waarde voor de regularisatieparameters L1 en L2. Voor beide wordt een niet-nulwaarde aanbevolen.

    Regularisatie is een methode om overfitting te voorkomen door modellen met extreme coëfficiënten te bestraffen. Regularisatie werkt door het toevoegen van de straf die wordt geassocieerd met coëfficiënten waarden aan de fout van de hypothese. Een nauwkeurig model met extreme coëfficiënten waarden zou meer worden bestraft, maar een minder nauwkeurig model met meer conservatieve waarden zou minder worden bestraft.

     L1 en L2 regularisatie hebben verschillende effecten en toepassingen. L1 kan worden toegepast op schaarse modellen, wat handig is bij het werken met hoogdimensionale gegevens. L2-regularisatie heeft daarentegen de voorkeur voor gegevens die niet schaars zijn.  Dit algoritme ondersteunt een lineaire combinatie van L1- en `x = L1` L2-regularisatiewaarden: dat wil zeggen, als en `y = L2`, `ax + by = c` de lineaire overspanning van de regularisatietermen definieert.

     Voor logistieke regressiemodellen, zoals [elastische nettoregularisatie,](https://wikipedia.org/wiki/Elastic_net_regularization)zijn verschillende lineaire combinaties van L1- en L2-termen bedacht.

6. **Willekeurig getalzaad**: Typ een gehele waarde die u wilt gebruiken als zaad voor het algoritme als u wilt dat de resultaten herhaalbaar zijn ten opzichte van runs. Anders wordt een systeemklokwaarde gebruikt als het zaad, dat iets andere resultaten kan opleveren in runs van dezelfde pijplijn.

8. Sluit een gelabelde gegevensset en een van de treinmodules aan:

    + Als u **De trainermodus maken** instelt op Eén **parameter,** gebruikt u de module [Treinmodel.](./train-model.md)

9. Verzend de pijplijn.



## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 