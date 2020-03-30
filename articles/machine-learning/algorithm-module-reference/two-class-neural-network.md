---
title: 'Twee-klasse neurale netwerk: Module Referentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de neurale netwerkmodule met twee klassen in Azure Machine Learning om een neuraal netwerkmodel te maken dat kan worden gebruikt om een doel te voorspellen dat slechts twee waarden heeft.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3a5cf2b210781d43ddd1a7aa87736a53df222cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477388"
---
# <a name="two-class-neural-network-module"></a>Module Neuralnetwork met twee klassen

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een neuraal netwerkmodel te maken dat kan worden gebruikt om een doel te voorspellen dat slechts twee waarden heeft.

Classificatie met neurale netwerken is een begeleide leermethode en vereist daarom een *gelabelde gegevensset*, die een labelkolom bevat. U dit neurale netwerkmodel bijvoorbeeld gebruiken om binaire uitkomsten te voorspellen, zoals of een patiënt al dan niet een bepaalde ziekte heeft, of dat een machine waarschijnlijk binnen een bepaalde tijdsperiode zal mislukken.  

Nadat u het model hebt gedefinieerd, traint u het door een gecodeerde gegevensset en het model op te geven als invoer voor [trainmodel.](./train-model.md) Het getrainde model kan vervolgens worden gebruikt om waarden voor nieuwe ingangen te voorspellen.

### <a name="more-about-neural-networks"></a>Meer over neurale netwerken

Een neuraal netwerk is een set van onderling verbonden lagen. De ingangen zijn de eerste laag en zijn verbonden met een uitvoerlaag door een acyclische grafiek bestaande uit gewogen randen en knooppunten.

Tussen de invoer- en uitvoerlagen u meerdere verborgen lagen invoegen. De meeste voorspellende taken kunnen eenvoudig worden uitgevoerd met slechts één of enkele verborgen lagen. Echter, recent onderzoek heeft aangetoond dat diepe neurale netwerken (DNN) met veel lagen effectief kunnen zijn in complexe taken zoals beeld- of spraakherkenning. De opeenvolgende lagen worden gebruikt om toenemende niveaus van semantische diepte te modelleren.

De relatie tussen ingangen en uitgangen wordt geleerd van het trainen van het neurale netwerk op de invoergegevens. De richting van de grafiek gaat van de ingangen door de verborgen laag en aan de outputlaag te werk. Alle knooppunten in een laag zijn verbonden door de gewogen randen met knooppunten in de volgende laag.

Om de uitvoer van het netwerk voor een bepaalde invoer te berekenen, wordt een waarde berekend op elk knooppunt in de verborgen lagen en in de uitvoerlaag. De waarde wordt ingesteld door de gewogen som van de waarden van de knooppunten uit de vorige laag te berekenen. Vervolgens wordt een activeringsfunctie toegepast op die gewogen som.
  
## <a name="how-to-configure"></a>Configureren

1.  Voeg de **tweeklasse neurale netwerkmodule** toe aan uw pijplijn. U deze module vinden onder **Machine Learning**, **Initialiseren**, in de categorie **Classificatie.**  
  
2.  Geef op hoe u het model wilt trainen door de optie **Trainer-modus maken in** te stellen.  
  
    -   **Eén parameter:** kies deze optie als u al weet hoe u het model wilt configureren.

    -   **Parameterbereik:** Als u niet zeker bent van de beste parameters, u de optimale parameters vinden met behulp van de module Hyperparameters van [tunemodel.](tune-model-hyperparameters.md) U geeft een aantal waarden en de trainer wijzigt meerdere combinaties van de instellingen om de combinatie van waarden te bepalen die het beste resultaat oplevert.  

3.  Selecteer **bij Verborgen laagspecificatie**het type netwerkarchitectuur dat u wilt maken.  
  
    -   **Volledig verbonden case**: Gebruikt de standaard neurale netwerkarchitectuur, gedefinieerd voor neurale netwerken van twee klassen als volgt:
  
        -   Heeft één verborgen laag.
  
        -   De uitvoerlaag is volledig verbonden met de verborgen laag en de verborgen laag is volledig verbonden met de invoerlaag.
  
        -   Het aantal knooppunten in de invoerlaag is gelijk aan het aantal functies in de trainingsgegevens.
  
        -   Het aantal knooppunten in de verborgen laag wordt ingesteld door de gebruiker. De standaardwaarde is 100.
  
        -   Het aantal knooppunten is gelijk aan het aantal klassen. Voor een twee-klasse neuraal netwerk betekent dit dat alle ingangen moeten worden toegewezen aan een van de twee knooppunten in de uitvoerlaag.

5.  Definieer **voor leersnelheid**de grootte van de stap die bij elke iteratie wordt genomen, vóór correctie. Een grotere waarde voor leersnelheid kan ertoe leiden dat het model sneller convergeert, maar het kan lokale minima overlopen.

6.  Geef **bij Aantal leeriteraties**het maximum aantal keren op dat het algoritme de trainingsaanvragen moet verwerken.

7.  Voor **de initiële leergewichten diameter**, geef de knooppunt gewichten aan het begin van het leerproces.

8.  Voor **Het momentum**, geef een gewicht toe te passen tijdens het leren op knooppunten van eerdere iteraties  

10. Selecteer de optie **Shuffle-voorbeelden** om aanvragen tussen iteraties te schuiven. Als u deze optie deselecteert, worden aanvragen telkens in exact dezelfde volgorde verwerkt wanneer u de pijplijn uitvoert.
  
11. Typ **bij Willekeurig getalzaad**een waarde die u als zaad moet gebruiken.
  
     Het opgeven van een seedwaarde is handig wanneer u wilt zorgen voor herhaalbaarheid in runs van dezelfde pijplijn.  Anders wordt een systeemklokwaarde gebruikt als het zaad, wat elke keer dat u de pijplijn uitvoert iets andere resultaten kan veroorzaken.
  
13. Voeg een gecodeerde gegevensset toe aan de pijplijn en verbind een van de [trainingsmodules](module-reference.md).  
  
    -   Als u **De trainermodus maken** instelt op Eén **parameter,** gebruikt u de module [Treinmodel.](train-model.md)  
  
14. Verzend de pijplijn.

## <a name="results"></a>Resultaten

Na de training is voltooid:

+ Als u een momentopname van het getrainde model wilt opslaan, selecteert u het tabblad **Uitvoer** in het rechterdeelvenster van de **module Treinmodel.** Selecteer het pictogram **Gegevensset registreren** om het model op te slaan als een herbruikbare module.

+ Als u het model wilt gebruiken om te scoren, voegt u de module **Scoremodel** toe aan een pijplijn.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
