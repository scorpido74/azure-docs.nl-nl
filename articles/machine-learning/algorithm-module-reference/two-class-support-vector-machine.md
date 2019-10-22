---
title: '2-klasse support vector machine: module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de computer module voor **ondersteuning van twee klassen** in azure machine learning service om een model te maken dat is gebaseerd op het algoritme van de ondersteunings vector machine.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: c58e66070f137f9c5d713a45682afac5f30bbd1e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692608"
---
# <a name="two-class-support-vector-machine-module"></a>Machine module voor de ondersteuning van twee klassen

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om een model te maken dat is gebaseerd op de computer algoritme van de ondersteunings vector. 

Support Vector machines (SVMs) zijn een goed te doorzoeken klasse van leer methoden met toezicht. Deze specifieke implementatie is geschikt voor het voors pellen van twee mogelijke resultaten, op basis van doorlopende of Categorische variabelen.

Nadat u de para meters van het model hebt gedefinieerd, traint u het model met behulp van de trainings modules en geeft u een *gecodeerde gegevensset* op die een kolom Label of resultaat bevat.

## <a name="about-support-vector-machines"></a>Vector machines ondersteunen

Ondersteuning voor vector machines is een van de eerste machine learning-algoritmen en SVM modellen zijn gebruikt in veel toepassingen, van gegevens die worden opgehaald tot tekst-en afbeeldings classificatie. SVMs kan worden gebruikt voor zowel classificatie-als regressie taken.

Dit SVM-model is een trainings model met Super visie waarvoor gelabelde gegevens zijn vereist. In het trainings proces analyseert het algoritme invoer gegevens en herkent patronen in een multi-dimensionale functie ruimte die de *hyperplane*wordt genoemd.  Alle invoer voorbeelden worden weer gegeven als punten in deze ruimte en worden toegewezen aan uitvoer Categorieën op een zodanige manier dat categorieën worden gedeeld door de brede en duidelijke onderbreking.

Voor de voor spelling wijst het SVM-algoritme nieuwe voor beelden toe aan één categorie of aan de andere, en wijs deze toe aan dezelfde ruimte. 

## <a name="how-to-configure"></a>Configureren 

Voor dit model type wordt aanbevolen dat u de gegevensset normaliseert voordat u deze gebruikt om de classificatie te trainen.
  
1.  Voeg de **machine module met twee klasse-ondersteunings vectoren** toe aan uw pijp lijn.  
  
2.  Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.  
  
    -   **Eén para meter**: als u weet hoe u het model wilt configureren, kunt u een specifieke set waarden als argumenten opgeven.  

3.  Voor **aantal herhalingen**typt u een getal dat het aantal iteraties aanduidt dat wordt gebruikt bij het bouwen van het model.  
  
     Deze para meter kan worden gebruikt om de verhouding tussen de snelheid en nauw keurigheid van de training te bepalen.  
  
4.  Typ voor **Lambda**een waarde die moet worden gebruikt als gewicht voor de N1-regularisatie.  
  
     Deze regularisatie-coëfficiënt kan worden gebruikt om het model af te stemmen. Grotere waarden bestraffen complexere modellen.  
  
5.  Selecteer de optie voor het **normaliseren van functies**als u functies vóór de training wilt normaliseren.
  
     Als u normalisatie toepast voordat de training wordt toegepast, worden gegevens punten gecentreerd op het gemiddelde en geschaald om één eenheid van standaard afwijking te hebben.
  
6.  Selecteer de optie **project naar de eenheids bol**om coëfficiënten te normaliseren.
  
     Projecteren van waarden naar eenheids ruimte betekent dat voordat een training wordt gegeven, gegevens punten worden gecentreerd op 0 en geschaald om één eenheid van standaard afwijking te hebben.
  
7.  Typ bij **wille keurig aantal Seed**een geheel getal dat moet worden gebruikt als seeding als u de reproduceer baarheid van alle uitvoeringen wilt garanderen.  Anders wordt een waarde van een systeem klok gebruikt als seed, wat kan leiden tot enigszins verschillende resultaten voor uitvoeringen.
  
9. Verbind een gegevensset met een label en een van de [trainings modules](module-reference.md):
  
    -   Als u de **modus trainer maken** instelt op **één para meter**, gebruikt u de module [Train model](train-model.md) .
  

10. Voer de pijplijn uit.

## <a name="results"></a>Resultaten

Nadat de training is voltooid:

+ Als u een samen vatting van de para meters van het model wilt weer geven, samen met de functie gewichten die zijn geleerd van training, klikt u met de rechter muisknop op de uitvoer van [Train model](./train-model.md)en selecteert u **visualiseren**.

+ Als u de getrainde modellen wilt gebruiken om voor spellingen te doen, verbindt u het getrainde model met de module [score model](score-model.md) .


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 