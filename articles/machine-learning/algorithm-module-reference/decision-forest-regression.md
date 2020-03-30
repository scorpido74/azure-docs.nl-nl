---
title: 'Decision Forest Regression: Module Referentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de Decision Forest Regression-module in Azure Machine Learning om een regressiemodel te maken op basis van een ensemble van beslissingsbomen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 63d90a5239e6bf350d8a6b66f35157e4c7d15aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456536"
---
# <a name="decision-forest-regression-module"></a>De module Decision Forest Regression

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een regressiemodel te maken op basis van een ensemble van beslissingsbomen.

Nadat u het model hebt geconfigureerd, moet u het model trainen met behulp van een gelabelde gegevensset en de [module Treinmodel.](./train-model.md) Het getrainde model kan vervolgens worden gebruikt om voorspellingen te doen. 

## <a name="how-it-works"></a>Hoe werkt het?

Beslissingsbomen zijn niet-parametrische modellen die voor elk exemplaar een reeks eenvoudige tests uitvoeren, waarbij een binaire structuur voor boomgegevens wordt doorgenomen totdat een bladknooppunt (beslissing) is bereikt.

Beslissingsbomen hebben deze voordelen:

- Ze zijn efficiënt in zowel berekening en geheugengebruik tijdens de training en voorspelling.

- Ze kunnen niet-lineaire beslissingsgrenzen vertegenwoordigen.

- Ze voeren geïntegreerde functieselectie en classificatie uit en zijn veerkrachtig in aanwezigheid van lawaaierige functies.

Dit regressiemodel bestaat uit een ensemble van beslisbomen. Elke boom in een regressiebeslissing forest outputs een Gaussische verdeling als een voorspelling. Een aggregatie wordt uitgevoerd over het ensemble van bomen om een Gaussische verdeling te vinden die het dichtst bij de gecombineerde verdeling voor alle bomen in het model ligt.

Zie dit artikel: [Decision Forests: A Unified Framework for Classification, Regression, Density Estimation, Manifold Learning en Semi-Supervised Learning](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#) voor meer informatie over het theoretische kader voor dit algoritme en de implementatie ervan.

## <a name="how-to-configure-decision-forest-regression-model"></a>Het regressiemodel van Decision Forest configureren

1. Voeg de **module Decision Forest Regression** toe aan de pijplijn. U de module vinden in de ontwerper onder **Machine Learning,** **Initialiseren Model**en **Regressie**.

2. Open de module-eigenschappen en kies voor **de methode Resampling**de methode die wordt gebruikt om de afzonderlijke bomen te maken.  U kiezen uit **Zakken** of **Repliceren.**

    - **Zakken**: Zakken wordt ook wel *bootstrap aggregeren*. Elke boom in een regressie beslissing bos outputs een Gaussische verdeling door middel van voorspelling. De aggregatie is het vinden van een Gaussian waarvan de eerste twee momenten overeenkomen met de momenten van de mix van Gaussische distributies gegeven door het combineren van alle distributies geretourneerd door individuele bomen.

         Zie voor meer informatie de Wikipedia-vermelding voor [Bootstrap die aggregeren.](https://wikipedia.org/wiki/Bootstrap_aggregating)

    - **Repliceren:** Bij replicatie wordt elke structuur getraind op exact dezelfde invoergegevens. De bepaling van welke split predicaat wordt gebruikt voor elke boom knooppunt blijft willekeurig en de bomen zullen divers zijn.

         Zie Decision Forests for Computer **Replicate** Vision en Medical Image Analysis voor meer informatie over het trainingsproces met de optie [Repliceren. Criminisi en J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/).

3. Geef op hoe u het model wilt trainen door de optie **Trainer-modus maken in** te stellen.

    - **Eén parameter**

      Als u weet hoe u het model wilt configureren, u een specifieke set waarden als argumenten opgeven. U hebt deze waarden misschien geleerd door te experimenteren of ze als leidraad ontvangen.

    - **Parameterbereik:** Selecteer deze optie als u niet zeker bent van de beste parameters en een parametersweep wilt uitvoeren. Selecteer een reeks waarden om over te herhalen en de [Tune Model Hyperparameters](tune-model-hyperparameters.md) herhalen over alle mogelijke combinaties van de instellingen die u hebt opgegeven om de hyperparameters te bepalen die de optimale resultaten opleveren. 



4. Voor **Aantal beslissingsbomen,** geef het totale aantal beslissingsbomen aan om in het ensemble te creëren. Door het creëren van meer beslissingbomen, u mogelijk een betere dekking te krijgen, maar de opleiding tijd zal toenemen.

    > [!TIP]
    > Deze waarde bepaalt ook het aantal bomen dat wordt weergegeven bij het visualiseren van het getrainde model. Als u één boom wilt zien of afdrukken, u de waarde instellen op 1; dit betekent echter dat er slechts één boom wordt geproduceerd (de boom met de eerste set parameters) en dat er geen verdere iteraties worden uitgevoerd.

5. Voor **maximale diepte van de beslissingsbomen,** typt u een getal om de maximale diepte van een beslissingsboom te beperken. Het verhogen van de diepte van de boom kan de precisie verhogen, met het risico van enige overfitting en verhoogde trainingstijd.

6. Typ **voor aantal willekeurige splitsingen per knooppunt**het aantal splitsingen dat moet worden gebruikt bij het bouwen van elk knooppunt van de boom. Een *splitsing* betekent dat functies in elk niveau van de boom (knooppunt) willekeurig worden verdeeld.

7. Voor **minimumaantal monsters per bladknooppunt**geeft u het minimumaantal gevallen aan dat nodig is om een eindknooppunt (blad) in een boom te maken.

     Door deze waarde te verhogen, verhoogt u de drempel voor het maken van nieuwe regels. Met de standaardwaarde van 1 kan bijvoorbeeld zelfs één aanvraag ertoe leiden dat een nieuwe regel wordt gemaakt. Als u de waarde verhoogt tot 5, moeten de trainingsgegevens ten minste vijf gevallen bevatten die aan dezelfde voorwaarden voldoen.


9. Verbind een gelabelde gegevensset, selecteer één labelkolom met niet meer dan twee uitkomsten en maak verbinding met [Train Model](./train-model.md).

    - Als u de optie **De trainermodus maken** instelt op **Enkele parameter,** traint u het model met de module [Treinmodel.](./train-model.md)

   

10. Verzend de pijplijn.

### <a name="results"></a>Resultaten

Na de training is voltooid:

+ Als u een momentopname van het getrainde model wilt opslaan, selecteert u de trainingsmodule en schakelt u vervolgens over naar het tabblad **Uitvoer** in het rechterdeelvenster. Klik op het pictogram **Register model**.  U het opgeslagen model vinden als module in de modulestructuur. 

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 