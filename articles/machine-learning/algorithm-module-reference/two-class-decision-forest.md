---
title: 'Beslissingsbos van twee klassen: naslaginformatie over modules'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de Tweeklassen-Beslissingsforestmodule in Azure Machine Learning om een machine learning-model te maken op basis van het algoritme voor beslissingsforests.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: c9388da449e75dee00fd43af9a4e0407c46f597a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916707"
---
# <a name="two-class-decision-forest-module"></a>Module Decision Forest met twee klassen

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een machine learning-model te maken op basis van het algoritme voor beslissingsbossen.  

De bossen van het besluit zijn snelle, gecontroleerde ensemblemodellen. Deze module is een goede keuze als u een doel met een maximum van twee uitkomsten wilt voorspellen. 

## <a name="understanding-decision-forests"></a>Inzicht in beslissing bossen

Dit algoritme voor beslissingsbos is een ensembleleermethode die is bedoeld voor classificatietaken. Ensemble methoden zijn gebaseerd op het algemene principe dat in plaats van te vertrouwen op een enkel model, u betere resultaten en een meer gegeneraliseerd model door het creëren van meerdere gerelateerde modellen en ze te combineren op een bepaalde manier. Over het algemeen bieden ensemblemodellen een betere dekking en nauwkeurigheid dan enkele beslissingsbomen. 

Er zijn vele manieren om individuele modellen te maken en te combineren in een ensemble. Deze bijzondere uitvoering van een beslissing bos werkt door het bouwen van meerdere beslissingbomen en vervolgens **stemmen** op de meest populaire output klasse. Stemmen is een van de bekendere methoden voor het genereren van resultaten in een ensemble model. 

+ Veel individuele classificatiebomen worden gemaakt met behulp van de gehele gegevensset, maar verschillende (meestal gerandomiseerde) uitgangspunten. Dit verschilt van de willekeurige bosbenadering, waarbij de individuele beslissingsbomen slechts een willekeurig deel van de gegevens of functies kunnen gebruiken.
+ Elke boom in de beslissing bosboom outputeen niet-genormaliseerde frequentie histogram van etiketten. 
+ Het aggregatieproces somt deze histogrammen op en normaliseert het resultaat om de "waarschijnlijkheden" voor elk label te krijgen. 
+ De bomen die een hoog voorspellingsvertrouwen hebben, zullen een groter gewicht hebben in de uiteindelijke beslissing van het ensemble.

Beslissingsbomen in het algemeen hebben veel voordelen voor classificatietaken:
  
- Ze kunnen niet-lineaire beslissingsgrenzen vastleggen.
- U trainen en voorspellen op veel gegevens, omdat ze efficiënt zijn in berekening en geheugengebruik.
- Functieselectie is geïntegreerd in de trainings- en classificatieprocessen.  
- Bomen zijn geschikt voor luidruchtige gegevens en vele functies.  
- Het zijn niet-parametrische modellen, wat betekent dat ze gegevens kunnen verwerken met gevarieerde distributies. 

Eenvoudige beslissingsbomen kunnen echter overfit op gegevens, en zijn minder generaleer dan boom ensembles.

Zie [Decision Forests voor](https://go.microsoft.com/fwlink/?LinkId=403677)meer informatie.  

## <a name="how-to-configure"></a>Configureren
  
1.  Voeg de **tweeklassen-beslissingsforestmodule** toe aan uw pijplijn in Azure Machine Learning en open het deelvenster **Eigenschappen** van de module. 

    U de module vinden onder **Machine Learning.** **Initialiseren**en vervolgens **Classificeren uitvouwen**.  
  
2.  Kies **bij resamplingmethode**de methode die wordt gebruikt om de afzonderlijke bomen te maken.  U kiezen uit **Zakken** of **Repliceren.**  
  
    -   **Zakken**: Zakken wordt ook wel *bootstrap aggregeren*. In deze methode wordt elke structuur gekweekt op een nieuw voorbeeld, gemaakt door willekeurig de oorspronkelijke gegevensset te bemonsteren met vervanging totdat u een gegevensset hebt ter grootte van het origineel.  
  
         De outputs van de modellen worden gecombineerd door *te stemmen*, wat een vorm van aggregatie is. Elke boom in een classificatiebesluit bos output een ongenormaliseerde frequentie histogram van etiketten. De aggregatie is om deze histogrammen op te tellen en te normaliseren om de "waarschijnlijkheden" voor elk label te krijgen. Op deze manier zullen de bomen met een hoog voorspellingsvertrouwen een groter gewicht hebben in de uiteindelijke beslissing van het ensemble.  
  
         Zie voor meer informatie de Wikipedia-vermelding voor Bootstrap die aggregeren.  
  
    -   **Repliceren:** Bij replicatie wordt elke structuur getraind op exact dezelfde invoergegevens. De bepaling van welke split predicaat wordt gebruikt voor elke boom knooppunt blijft willekeurig en de bomen zullen divers zijn.   
  
3.  Geef op hoe u het model wilt trainen door de optie **Trainer-modus maken in** te stellen.  
  
    -   **Eén parameter:** als u weet hoe u het model wilt configureren, u een specifieke set waarden als argumenten opgeven.

    -   **Parameterbereik:** Als u niet zeker bent van de beste parameters, u de optimale parameters vinden met behulp van de module Hyperparameters van [tunemodel.](tune-model-hyperparameters.md) U geeft een aantal waarden en de trainer wijzigt meerdere combinaties van de instellingen om de combinatie van waarden te bepalen die het beste resultaat oplevert.
  
4.  Typ **voor aantal beslissingsbomen**het maximum aantal beslissingsbomen dat in het ensemble kan worden gemaakt. Door meer beslissingsbomen te maken, u mogelijk een betere dekking krijgen, maar de trainingstijd neemt toe.  
  
    > [!NOTE]
    >  Deze waarde bepaalt ook het aantal bomen dat wordt weergegeven bij het visualiseren van het getrainde model. Als u één boom wilt zien of afdrukken, u de waarde instellen op 1. Er kan echter slechts één boom worden geproduceerd (de boom met de eerste set parameters) en er worden geen verdere iteraties uitgevoerd.
  
5.  Voor **maximale diepte van de beslissingsbomen,** typt u een getal om de maximale diepte van een beslissingsboom te beperken. Het verhogen van de diepte van de boom kan de precisie verhogen, met het risico van enige overfitting en verhoogde trainingstijd.
  
6.  Typ **voor aantal willekeurige splitsingen per knooppunt**het aantal splitsingen dat moet worden gebruikt bij het bouwen van elk knooppunt van de boom. Een *splitsing* betekent dat functies in elk niveau van de boom (knooppunt) willekeurig worden verdeeld.
  
7.  Voor **minimumaantal monsters per bladknooppunt**geeft u het minimumaantal gevallen aan dat nodig is om een eindknooppunt (blad) in een boom te maken.
  
     Door deze waarde te verhogen, verhoogt u de drempel voor het maken van nieuwe regels. Met de standaardwaarde van 1 kan bijvoorbeeld zelfs één aanvraag ertoe leiden dat een nieuwe regel wordt gemaakt. Als u de waarde verhoogt tot 5, moeten de trainingsgegevens ten minste vijf gevallen bevatten die aan dezelfde voorwaarden voldoen.  
  
8.  Selecteer de optie **Onbekende waarden toestaan voor categorische functies** om een groep voor onbekende waarden te maken in de trainings- of validatiesets. Het model is misschien minder nauwkeurig voor bekende waarden, maar het kan betere voorspellingen geven voor nieuwe (onbekende) waarden. 

     Als u deze optie deselecteert, kan het model alleen de waarden accepteren die in de trainingsgegevens zijn opgenomen.
  
9. Voeg een gelabelde gegevensset en een van de [trainingsmodules toe:](module-reference.md)  
  
    -   Als u **De trainermodus maken** instelt op Eén **parameter,** gebruikt u de module [Treinmodel.](./train-model.md)  
    
## <a name="results"></a>Resultaten

Na de training is voltooid:

+ Als u een momentopname van het getrainde model wilt opslaan, selecteert u het tabblad **Uitvoer** in het rechterdeelvenster van de **module Treinmodel.** Selecteer het pictogram **Gegevensset registreren** om het model op te slaan als een herbruikbare module.

+ Als u het model wilt gebruiken om te scoren, voegt u de module **Scoremodel** toe aan een pijplijn.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 