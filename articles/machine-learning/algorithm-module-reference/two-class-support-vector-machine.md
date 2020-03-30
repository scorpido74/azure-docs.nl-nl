---
title: 'Vectormachine voor ondersteuning in twee klassen: naslaginformatie over module'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de **vectormachinemodule voor twee klassen** in Azure Machine Learning om een model te maken dat is gebaseerd op het ondersteuningsvectormachinealgoritme.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: ba788518951e72c1701d99decf46350e8665dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455805"
---
# <a name="two-class-support-vector-machine-module"></a>Vectormachinemodule voor twee klassen

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een model te maken dat is gebaseerd op het ondersteuningsvectormachinealgoritme. 

Support vector machines (SVMs) zijn een goed onderzochte klasse van onder toezicht leermethoden. Deze specifieke implementatie is geschikt voor het voorspellen van twee mogelijke uitkomsten, op basis van continue of categorische variabelen.

Nadat u de modelparameters hebt gedefinieerd, traint u het model met behulp van de trainingsmodules en biedt u een *gelabelde gegevensset* met een label- of uitkomstkolom.

## <a name="about-support-vector-machines"></a>Over ondersteuningsvectormachines

Ondersteuningsvectormachines behoren tot de vroegste machine learning-algoritmen en SVM-modellen zijn in veel toepassingen gebruikt, van het ophalen van informatie tot tekst- en beeldclassificatie. SPM's kunnen worden gebruikt voor zowel classificatie- als regressietaken.

Dit SVM-model is een begeleid leermodel dat gelabelde gegevens vereist. In het trainingsproces analyseert het algoritme invoergegevens en herkent het patronen in een multidimensionale functieruimte genaamd de *hyperplane.*  Alle invoervoorbeelden worden weergegeven als punten in deze ruimte en worden zodanig toegewezen aan uitvoercategorieën dat categorieën worden verdeeld door een zo breed en duidelijk mogelijke kloof.

Voor voorspelling wijst het SVM-algoritme nieuwe voorbeelden toe in de ene of de andere categorie en brengt u ze in kaart in dezelfde ruimte. 

## <a name="how-to-configure"></a>Configureren 

Voor dit modeltype wordt aanbevolen de gegevensset te normaliseren voordat u deze gebruikt om de classificatie te trainen.
  
1.  Voeg de **vectormachinemodule voor twee klassen** ondersteuning toe aan uw pijplijn.  
  
2.  Geef op hoe u het model wilt trainen door de optie **Trainer-modus maken in** te stellen.  
  
    -   **Eén parameter:** als u weet hoe u het model wilt configureren, u een specifieke set waarden als argumenten opgeven.  

    -   **Parameterbereik:** Als u niet zeker bent van de beste parameters, u de optimale parameters vinden met behulp van de module Hyperparameters van [tunemodel.](tune-model-hyperparameters.md) U geeft een aantal waarden en de trainer wijzigt meerdere combinaties van de instellingen om de combinatie van waarden te bepalen die het beste resultaat oplevert.

3.  Typ **voor Aantal iteraties**een getal dat het aantal iteraties aangeeft dat wordt gebruikt bij het bouwen van het model.  
  
     Deze parameter kan worden gebruikt om de afweging tussen trainingssnelheid en nauwkeurigheid te regelen.  
  
4.  Voor **Lambda,** typ een waarde te gebruiken als het gewicht voor L1 regularisatie.  
  
     Deze regularisatiecoëfficiënt kan worden gebruikt om het model af te stemmen. Grotere waarden bestraffen complexere modellen.  
  
5.  Selecteer de **optie, Functies normaliseren,** als u functies wilt normaliseren voordat u traint.
  
     Als u normalisatie toepast, vóór de training, worden gegevenspunten gecentreerd op het gemiddelde en geschaald om één eenheid standaarddeviatie te hebben.
  
6.  Selecteer de optie **Project naar de eenheidsbol**om coëfficiënten te normaliseren.
  
     Het projecteren van waarden naar eenheidsruimte betekent dat gegevenspunten vóór de training gecentreerd zijn op 0 en worden geschaald om één eenheid standaarddeviatie te hebben.
  
7.  Typ **in Willekeurig getalzaad**een gehele waarde die u als zaad wilt gebruiken als u reproduceerbaarheid wilt garanderen in verschillende runs.  Anders wordt een systeemklokwaarde gebruikt als zaad, wat kan resulteren in iets andere resultaten over runs.
  
9. Sluit een gelabelde gegevensset en een van de [trainingsmodules](module-reference.md)aan:
  
    -   Als u **De trainermodus maken** instelt op Eén **parameter,** gebruikt u de module [Treinmodel.](train-model.md)
  
10. Verzend de pijplijn.

## <a name="results"></a>Resultaten

Na de training is voltooid:

+ Als u een momentopname van het getrainde model wilt opslaan, selecteert u het tabblad **Uitvoer** in het rechterdeelvenster van de **module Treinmodel.** Selecteer het pictogram **Gegevensset registreren** om het model op te slaan als een herbruikbare module.

+ Als u het model wilt gebruiken om te scoren, voegt u de module **Scoremodel** toe aan een pijplijn.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 