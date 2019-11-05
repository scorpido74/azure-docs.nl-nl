---
title: 'Regressie van beslissings bos: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module herregressies voor het besluitvormings forest in Azure Machine Learning om een regressie model te maken op basis van een ensemble van beslissings structuren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: d930a6d856c6608e7792ce8ef3204b39aba0028a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497972"
---
# <a name="decision-forest-regression-module"></a>Regressie-module voor beslissings structuur

In dit artikel wordt een module in Azure Machine Learning Designer (preview) beschreven.

Gebruik deze module om een regressie model te maken op basis van een ensemble van beslissings structuren.

Nadat u het model hebt geconfigureerd, moet u het model trainen met een gegevensset met een label en de module [Train model](./train-model.md) . Het getrainde model kan vervolgens worden gebruikt voor het maken van voor spellingen. 

## <a name="how-it-works"></a>Het werkt als volgt

Beslissings structuren zijn niet-parametrische modellen die een reeks eenvoudige tests voor elk exemplaar uitvoeren, waarbij een gegevens structuur van een binaire structuur wordt doorzocht totdat een Leaf-knoop punt (beslissing) is bereikt.

Beslissings structuren hebben de volgende voor delen:

- Ze zijn efficiënt in berekeningen en geheugen gebruik tijdens de training en voor spellingen.

- Ze kunnen niet-lineaire beslissings grenzen vertegenwoordigen.

- Ze hebben de mogelijkheid om geïntegreerde functies te selecteren en te classificeren.

Dit regressie model bestaat uit een ensemble van beslissings structuren. Elke structuur in een regressie-beslissings forest voert een Gaussiaanse distributie uit als een voor spelling. Er wordt een aggregatie uitgevoerd over de ensemble van structuren om een Gaussiaanse distributie te vinden die het dichtst bij de gecombineerde distributie ligt voor alle structuren in het model.

Raadpleeg dit artikel voor meer informatie over het theoretische Framework voor deze algoritme en de implementatie ervan: [beslissings bossen: een uniform Framework voor classificatie, regressie, dichtheids schatting, verzamel leer en semi-Super visie leren ](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Het regressie model voor beslissings structuur configureren

1. Voeg de regressie-module van de **beslissings structuur** toe aan de pijp lijn. U kunt de module vinden in de ontwerp functie onder **machine learning**, **model initialiseren**en **regressie**.

2. Open de module-eigenschappen en kies voor **resampling methode**de methode die wordt gebruikt om de afzonderlijke structuren te maken.  U kunt **kiezen uit een** of meer van de **replicatie**.

    - **Overbaggen**: de functie voor het *verzamelen van de Boots trap* Elke structuur in een regressie-beslissings forest voert een Gaussiaanse distributie uit door middel van voor spellingen. De aggregatie is het vinden van een Gaussiaans waarvan de eerste twee seconden overeenkomen met de momenten van het combi natie van Gaussiaans distributies die worden gegeven door het combi neren van alle distributies die door afzonderlijke bomen worden geretourneerd.

         Zie de Wikipedia-vermelding voor [Boots trap aggregatie](https://wikipedia.org/wiki/Bootstrap_aggregating)voor meer informatie.

    - **Repliceren**: in replicatie wordt elke structuur getraind op exact dezelfde invoer gegevens. De bepaling waarvan het gesplitste predicaat voor elk structuur knooppunt wordt gebruikt, blijft wille keurig en de structuur is divers.

         Voor meer informatie over het trainings proces met de optie **repliceren** raadpleegt u [besluitvormings forests voor computer vision en medische afbeeldings analyse. Criminisi en J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/).

3. Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.

    - **Eén para meter**

      Als u weet hoe u het model wilt configureren, kunt u een specifieke set waarden als argumenten opgeven. U hebt deze waarden mogelijk geleerd door te experimenteren of ze als richt lijn te ontvangen.



4. Geef voor **aantal beslissings structuren**het totale aantal beslissings structuren op dat in de ensemble moet worden gemaakt. Door meer beslissings structuren te maken, kunt u een betere dekking krijgen, maar wordt de trainings tijd verhoogd.

    > [!TIP]
    > Deze waarde bepaalt ook het aantal structuren dat wordt weer gegeven wanneer het getrainde model wordt gevisualiseerd. Als u één boom structuur wilt zien of afdrukken, kunt u de waarde instellen op 1. Dit betekent echter dat er slechts één structuur wordt geproduceerd (de structuur met de initiële set para meters) en er geen verdere herhalingen worden uitgevoerd.

5. Voor **de maximale diepte van de beslissings structuren**typt u een getal om de maximale diepte van een beslissings structuur te beperken. Het verg Roten van de diepte van de structuur kan nauw keuriger zijn, met het risico van een aantal verdeelde en verhoogde opleidings tijd.

6. Voor het **aantal wille keurige splitsingen per knoop punt**typt u het aantal splitsingen dat moet worden gebruikt bij het maken van elk knoop punt van de structuur. Een *splitsing* houdt in dat onderdelen in elk niveau van de structuur (knoop punt) wille keurig worden gedeeld.

7. Geef voor het **minimum aantal steek proeven per blad knooppunt**het minimale aantal cases op dat nodig is om een Terminal knooppunt (Leaf) in een structuur te maken.

     Door deze waarde te verhogen, verhoogt u de drempel voor het maken van nieuwe regels. Met de standaard waarde 1 kan zelfs een enkele case ertoe leiden dat een nieuwe regel wordt gemaakt. Als u de waarde op 5 verhoogt, moeten de trainings gegevens ten minste vijf gevallen bevatten die voldoen aan dezelfde voor waarden.


9. Verbind een gegevensset met een label, selecteer een kolom met één label die niet meer dan twee uitkomsten bevat en verbinding maakt met het [Train-model](./train-model.md).

    - Als u de optie **trainer maken** instelt op **één para meter**, traint u het model met behulp van de module [Train model](./train-model.md) .

   

10. Voer de pijplijn uit.

### <a name="results"></a>Resultaten

Nadat de training is voltooid:

+ Als u de structuur wilt zien die op elke iteratie is gemaakt, klikt u met de rechter muisknop op de uitvoer van de trainings module en selecteert u **visualiseren**.

+ Als u de regels voor elk knoop punt wilt bekijken, klikt u op elke structuur en zoomt u in op de splitsingen.

+ Als u een moment opname van het getrainde model wilt opslaan, klikt u met de rechter muisknop op de uitvoer van de trainings module en selecteert u **Opslaan als getraind model**. Deze kopie van het model wordt niet bijgewerkt op opeenvolgende uitvoeringen van de pijp lijn. 

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 