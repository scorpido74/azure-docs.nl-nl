---
title: 'Multiclass Neural Network: Module Referentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de Multiclass Neural Network-module in Azure Machine Learning om een neuraal netwerkmodel te maken dat kan worden gebruikt om een doel met meerdere waarden te voorspellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 653b12ddd54c5ec9e4e7dd23a323f34460daa962
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920413"
---
# <a name="multiclass-neural-network-module"></a>Multiclass Neural Network-module

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een neuraal netwerkmodel te maken dat kan worden gebruikt om een doel met meerdere waarden te voorspellen. 

Dergelijke neurale netwerken kunnen bijvoorbeeld worden gebruikt in complexe computervisiontaken, zoals cijfer- of letterherkenning, documentclassificatie en patroonherkenning.

Classificatie met neurale netwerken is een begeleide leermethode en vereist daarom een *gelabelde gegevensset* met een labelkolom.

U het model trainen door het model en de gelabelde gegevensset op te geven als input voor [Train Model.](./train-model.md) Het getrainde model kan vervolgens worden gebruikt om waarden voor de nieuwe invoervoorbeelden te voorspellen.  

## <a name="about-neural-networks"></a>Over neurale netwerken

Een neuraal netwerk is een set van onderling verbonden lagen. De ingangen zijn de eerste laag en zijn verbonden met een uitvoerlaag door een acyclische grafiek bestaande uit gewogen randen en knooppunten.

Tussen de invoer- en uitvoerlagen u meerdere verborgen lagen invoegen. De meeste voorspellende taken kunnen eenvoudig worden uitgevoerd met slechts één of enkele verborgen lagen. Echter, recent onderzoek heeft aangetoond dat diepe neurale netwerken (DNN) met veel lagen effectief kunnen zijn in complexe taken zoals beeld- of spraakherkenning. De opeenvolgende lagen worden gebruikt om toenemende niveaus van semantische diepte te modelleren.

De relatie tussen ingangen en uitgangen wordt geleerd van het trainen van het neurale netwerk op de invoergegevens. De richting van de grafiek gaat van de ingangen door de verborgen laag en aan de outputlaag te werk. Alle knooppunten in een laag zijn verbonden door de gewogen randen met knooppunten in de volgende laag.

Om de uitvoer van het netwerk voor een bepaalde invoer te berekenen, wordt een waarde berekend op elk knooppunt in de verborgen lagen en in de uitvoerlaag. De waarde wordt ingesteld door de gewogen som van de waarden van de knooppunten uit de vorige laag te berekenen. Vervolgens wordt een activeringsfunctie toegepast op die gewogen som.

## <a name="configure-multiclass-neural-network"></a>Multiclass Neural Network configureren

1. Voeg de **MultiClass Neural Network-module** toe aan uw pijplijn in de ontwerper. U deze module vinden onder **Machine Learning**, **Initialiseren**, in de categorie **Classificatie.**

2. **Trainer-modus maken:** gebruik deze optie om op te geven hoe u het model wilt trainen:

    - **Eén parameter:** kies deze optie als u al weet hoe u het model wilt configureren.

    - **Parameterbereik:** Selecteer deze optie als u niet zeker bent van de beste parameters en een parametersweep wilt uitvoeren. Selecteer een reeks waarden om over te herhalen en de [Tune Model Hyperparameters](tune-model-hyperparameters.md) herhalen over alle mogelijke combinaties van de instellingen die u hebt opgegeven om de hyperparameters te bepalen die de optimale resultaten opleveren.  

3. Specificatie van **verborgen lagen:** selecteer het type netwerkarchitectuur dat u wilt maken.

    - **Volledig verbonden aanvraag**: Selecteer deze optie om een model te maken met behulp van de standaard neurale netwerkarchitectuur. Voor multiclass neurale netwerkmodellen zijn de standaardinstellingen als volgt:

        - Eén verborgen laag
        - De uitvoerlaag is volledig verbonden met de verborgen laag.
        - De verborgen laag is volledig verbonden met de invoerlaag.
        - Het aantal knooppunten in de invoerlaag wordt bepaald door het aantal functies in de trainingsgegevens.
        - Het aantal knooppunten in de verborgen laag kan door de gebruiker worden ingesteld. De standaardwaarde is 100.
        - Het aantal knooppunten in de uitvoerlaag is afhankelijk van het aantal klassen.
  
   

5. **Aantal verborgen knooppunten**: met deze optie u het aantal verborgen knooppunten in de standaardarchitectuur aanpassen. Typ het aantal verborgen knooppunten. De standaardinstelling is een verborgen laag met 100 knooppunten.

6. **Het leerpercentage**: Definieer de grootte van de stap die bij elke iteratie wordt genomen, vóór correctie. Een grotere waarde voor leersnelheid kan ertoe leiden dat het model sneller convergeert, maar het kan lokale minima overlopen.

7. **Aantal leeriteraties**: Geef het maximum aantal keren op dat het algoritme de trainingscases moet verwerken.

8. **De initiële leergewichten diameter**: Geef de knooppuntgewichten aan het begin van het leerproces.

9. **Het momentum**: Geef een gewicht op dat moet worden toegepast tijdens het leren op knooppunten van eerdere iteraties.
  
11. **Shuffle voorbeelden:** Selecteer deze optie om cases tussen iteraties te schuiven.

    Als u deze optie deselecteert, worden aanvragen telkens in exact dezelfde volgorde verwerkt wanneer u de pijplijn uitvoert.

12. **Willekeurig getalzaad:** Typ een waarde die u als zaad wilt gebruiken, als u herhaalbaarheid wilt garanderen in verschillende runs van dezelfde pijplijn.

14. Sluit een trainingsdataset en een van de [trainingsmodules](module-reference.md)aan: 

    - Als u **De trainermodus maken** instelt op Eén **parameter,** gebruikt u [Treinmodel](train-model.md).  
  

## <a name="results"></a>Resultaten

Na de training is voltooid:

- Als u een momentopname van het getrainde model wilt opslaan, selecteert u het tabblad **Uitvoer** in het rechterdeelvenster van de **module Treinmodel.** Selecteer het pictogram **Gegevensset registreren** om het model op te slaan als een herbruikbare module.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 