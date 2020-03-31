---
title: 'Decision Forest Regression: Module Referentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de tweeklassengemiddelde Perceptron-module in Azure Machine Learning om een machine learning-model te maken op basis van het gemiddelde perceptronalgoritme.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 73e23dd7d350ea63e9fd8b933a525a9d8aad9e3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920770"
---
# <a name="two-class-averaged-perceptron-module"></a>Twee-klasse gemiddelde Perceptron module

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een machine learning-model te maken op basis van het gemiddelde perceptronalgoritme.  
  
Dit classificatiealgoritme is een begeleide leermethode en vereist een *gelabelde gegevensset,* die een labelkolom bevat. U het model trainen door het model en de gelabelde gegevensset op te geven als input voor [Train Model.](./train-model.md) Het getrainde model kan vervolgens worden gebruikt om waarden voor de nieuwe invoervoorbeelden te voorspellen.  

### <a name="about-averaged-perceptron-models"></a>Over gemiddelde perceptronmodellen

De *gemiddelde perceptronmethode* is een vroege en eenvoudige versie van een neuraal netwerk. In deze benadering worden ingangen ingedeeld in verschillende mogelijke uitgangen op basis van een lineaire functie en vervolgens gecombineerd met een set gewichten die zijn afgeleid van de functievector, vandaar de naam 'perceptron'.

De eenvoudigere perceptronmodellen zijn geschikt voor het leren van lineair scheidbare patronen, terwijl neurale netwerken (vooral diepe neurale netwerken) complexere klassengrenzen kunnen modelleren. Echter, perceptrons zijn sneller, en omdat ze cases serieel verwerken, kunnen perceptrons worden gebruikt met continue training.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Perceptron met twee klassen gemiddeld configureren

1.  Voeg de **module Averaged Perceptron met twee klassen** toe aan uw pijplijn.  

2.  Geef op hoe u het model wilt trainen door de optie **Trainer-modus maken in** te stellen.  
  
    -   **Eén parameter:** Als u weet hoe u het model wilt configureren, geeft u een specifieke set waarden op als argumenten.

    -   **Parameterbereik:** Selecteer deze optie als u niet zeker bent van de beste parameters en een parametersweep wilt uitvoeren. Selecteer een reeks waarden om over te herhalen en de [Tune Model Hyperparameters](tune-model-hyperparameters.md) herhalen over alle mogelijke combinaties van de instellingen die u hebt opgegeven om de hyperparameters te bepalen die de optimale resultaten opleveren.  
  
3.  Geef **voor leersnelheid**een waarde op voor het *leerpercentage*. De leersnelheidwaarden bepalen de grootte van de stap die wordt gebruikt bij een stochastische gradiëntdaling telkens wanneer het model wordt getest en gecorrigeerd.
  
     Door de snelheid kleiner te maken, test u het model vaker, met het risico dat u vast komt te zitten in een lokaal plateau. Door de stap groter te maken, u sneller convergeren, met het risico dat u de echte minima overtreedt.
  
4.  Typ **voor maximaal aantal iteraties**het aantal keren dat het algoritme de trainingsgegevens moet onderzoeken.  
  
     Vroeg stoppen zorgt vaak voor een betere generalisatie. Het verhogen van het aantal iteraties verbetert de montage, met het risico van overfitting.
  
5.  Typ **bij Willekeurig getalzaad**eventueel een gehele waarde die u als zaad wilt gebruiken. Het gebruik van een zaad wordt aanbevolen als u wilt reproduceerbaarheid van de pijplijn over loopt te waarborgen.  
  
1.  Sluit een trainingsgegevensset en een van de trainingsmodules aan:
  
    -   Als u **De trainermodus maken** instelt op Eén **parameter,** gebruikt u de module [Treinmodel.](train-model.md)




## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 