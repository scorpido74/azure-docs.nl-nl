---
title: 'Meerklassen Decision Forest: naslaginformatie over modules'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de Multiclass Decision Forest-module in Azure Machine Learning om een machine learning-model te maken op basis van het *algoritme voor beslissingsbos.*
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 9a0a70f94be337eedf8f8ba4cc17af896f7a03b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477541"
---
# <a name="multiclass-decision-forest-module"></a>Module Meerklassen Decision Forest

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een machine learning-model te maken op basis van het *algoritme voor beslissingsbos.* Een beslissingsbos is een ensemblemodel dat snel een reeks beslissingsbomen bouwt, terwijl ze leren van gelabelde gegevens.

## <a name="more-about-decision-forests"></a>Meer over beslissing bossen

Het algoritme voor beslissingsbos is een ensembleleermethode voor classificatie. Het algoritme werkt door het bouwen van meerdere beslissingsbomen en vervolgens *stemmen* op de meest populaire output klasse. Stemmen is een vorm van aggregatie, waarin elke boom in een classificatiebesluit een niet-genormaliseerd frequentiehistogram van etiketten produceert. Het aggregatieproces somt deze histogrammen op en normaliseert het resultaat om de "waarschijnlijkheden" voor elk label te krijgen. De bomen die een hoog voorspellingsvertrouwen hebben, hebben een groter gewicht in de uiteindelijke beslissing van het ensemble.

Beslissingsbomen in het algemeen zijn niet-parametrische modellen, wat betekent dat ze gegevens ondersteunen met gevarieerde distributies. In elke boom wordt voor elke klasse een reeks eenvoudige tests uitgevoerd, waardoor de niveaus van een boomstructuur worden verhoogd totdat een bladknooppunt (beslissing) is bereikt.

Beslissingbomen hebben vele voordelen:

+ Ze kunnen niet-lineaire beslissingsgrenzen vertegenwoordigen.
+ Ze zijn efficiënt in berekening en geheugengebruik tijdens training en voorspelling.
+ Ze voeren geïntegreerde functieselectie en classificatie uit.
+ Ze zijn veerkrachtig in de aanwezigheid van luidruchtige functies.

De beslissingforestclassificatie in Azure Machine Learning bestaat uit een ensemble van beslissingsbomen. Over het algemeen bieden ensemblemodellen een betere dekking en nauwkeurigheid dan enkele beslissingsbomen. Zie [Besluit bomen voor](https://go.microsoft.com/fwlink/?LinkId=403677)meer informatie.

## <a name="how-to-configure-multiclass-decision-forest"></a>Meerklassenbeslissingsbos configureren

1. Voeg de **multiclass Decision Forest-module** toe aan uw pijplijn in de ontwerper. U deze module vinden onder **Machine Learning,** **Initialiseren Model**en **Classificatie**.

2. Dubbelklik op de module om het deelvenster **Eigenschappen** te openen.

3. Kies **bij resamplingmethode**de methode die wordt gebruikt om de afzonderlijke bomen te maken.  U kiezen uit zakken of replicatie.

    + **Zakken**: Zakken wordt ook wel *bootstrap aggregeren*. In deze methode wordt elke structuur gekweekt op een nieuw voorbeeld, gemaakt door willekeurig de oorspronkelijke gegevensset te bemonsteren met vervanging totdat u een gegevensset hebt ter grootte van het origineel. De outputs van de modellen worden gecombineerd door *te stemmen*, wat een vorm van aggregatie is. Zie voor meer informatie de Wikipedia-vermelding voor Bootstrap die aggregeren.

    + **Repliceren:** Bij replicatie wordt elke structuur getraind op exact dezelfde invoergegevens. De bepaling van welke split predicaat wordt gebruikt voor elke boom knooppunt blijft willekeurig, het creëren van diverse bomen.

   

4. Geef op hoe u het model wilt trainen door de optie **Trainer-modus maken in** te stellen.

    + **Eén parameter:** selecteer deze optie als u weet hoe u het model wilt configureren en geef een set waarden op als argumenten.

    + **Parameterbereik:** Selecteer deze optie als u niet zeker bent van de beste parameters en een parametersweep wilt uitvoeren. Selecteer een reeks waarden om over te herhalen en de [Tune Model Hyperparameters](tune-model-hyperparameters.md) herhalen over alle mogelijke combinaties van de instellingen die u hebt opgegeven om de hyperparameters te bepalen die de optimale resultaten opleveren.   

5. **Aantal beslissingsbomen**: Typ het maximum aantal beslissingsbomen dat in het ensemble kan worden gemaakt. Door meer beslissingsbomen te maken, u mogelijk een betere dekking krijgen, maar de trainingstijd kan toenemen.

    Deze waarde bepaalt ook het aantal bomen dat in de resultaten wordt weergegeven bij het visualiseren van het getrainde model. Als u één boom wilt zien of afdrukken, u de waarde instellen op 1. dit betekent echter dat er slechts één boom kan worden geproduceerd (de boom met de oorspronkelijke set parameters) en dat er geen verdere iteraties worden uitgevoerd.

6. **Maximale diepte van de beslissingsbomen**: Typ een getal om de maximale diepte van een beslissingsboom te beperken. Het verhogen van de diepte van de boom kan de precisie verhogen, met het risico van enige overfitting en verhoogde trainingstijd.

7. **Aantal willekeurige splitsingen per knooppunt**: Typ het aantal splitsingen dat moet worden gebruikt bij het bouwen van elk knooppunt van de boom. Een *splitsing* betekent dat functies in elk niveau van de boom (knooppunt) willekeurig worden verdeeld.

8. **Minimumaantal monsters per bladknooppunt**: Geef het minimumaantal gevallen aan dat nodig is om een eindknooppunt (blad) in een boom te maken. Door deze waarde te verhogen, verhoogt u de drempel voor het maken van nieuwe regels.

    Met de standaardwaarde van 1 kan bijvoorbeeld zelfs één aanvraag ertoe leiden dat een nieuwe regel wordt gemaakt. Als u de waarde verhoogt tot 5, moeten de trainingsgegevens ten minste vijf gevallen bevatten die aan dezelfde voorwaarden voldoen.



10. Sluit een gelabelde gegevensset en een van de trainingsmodules aan:

    + Als u **De trainermodus maken** instelt op Eén **parameter,** gebruikt u de module [Treinmodel.](./train-model.md)

11. Verzend de pijplijn.



## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 