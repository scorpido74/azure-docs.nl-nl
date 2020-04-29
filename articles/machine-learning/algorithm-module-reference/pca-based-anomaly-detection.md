---
title: 'Anomalie detectie op basis van PCA: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de op PCA gebaseerde anomalie detectie module voor het maken van een anomalie detectie model op basis van Principal component Analysis (PCA).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 0672b9769feae65c73a6f752a268968a7bad9e4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502982"
---
# <a name="pca-based-anomaly-detection"></a>Anomaliedetectie op basis van PCA

In dit artikel wordt beschreven hoe u de module voor **anomalie detectie op basis van PCA** in azure machine learning Designer (preview) gebruikt om een anomalie detectie model te maken op basis van Principal component ANALYSIS (PCA).

Deze module helpt u bij het bouwen van een model in scenario's waarin het eenvoudig is om trainings gegevens uit één klasse te verkrijgen, zoals geldige trans acties, maar moeilijk voldoende voor beelden van de doel afwijkingen te verkrijgen. 

Als u bijvoorbeeld frauduleuze trans acties wilt detecteren, hebt u vaak niet voldoende voor beelden van fraude voor het trainen op, maar u hebt veel voor beelden van goede trans acties. De module voor **anomalie detectie op basis van PCA** lost het probleem op door de beschik bare functies te analyseren om te bepalen wat een ' normale ' klasse vormt, en het Toep assen van afstands waarden om cases te identificeren die afwijkingen vertegenwoordigen. Zo kunt u een model trainen met bestaande, niet-sluitende gegevens.

## <a name="more-about-principal-component-analysis"></a>Meer informatie over het analyseren van hoofd onderdelen

*Analyse van Principal-onderdelen*, die regel matig wordt AFGEKORT tot PCA, is een gevestigde techniek in machine learning. PCA wordt vaak gebruikt in verkennende gegevens analyse, omdat het de interne structuur van de gegevens onthult en de variantie in de gegevens verklaart.

PCA werkt met het analyseren van gegevens die meerdere variabelen bevatten. Er wordt gezocht naar correlaties tussen de variabelen en bepaalt de combi natie van waarden die de beste verschillen in resultaten vastleggen. Deze gecombineerde functie waarden worden gebruikt voor het maken van een compactere functie ruimte die de *belangrijkste onderdelen*wordt genoemd.

Voor afwijkings detectie wordt elke nieuwe invoer geanalyseerd en de afwijkings detectie algoritme berekent de projectie op de eigenvectors, samen met een genormaliseerde reconstructie fout. De genormaliseerde fout wordt gebruikt als afwijkings Score. Hoe hoger de fout, hoe meer afwijkend het exemplaar is.

Raadpleeg de volgende artikelen voor meer informatie over hoe PCA werkt en over de implementatie van anomalie detectie:

- [Een wille keurig algoritme voor het analyseren van Principal-onderdelen](https://arxiv.org/abs/0809.2274). Rokhlin, Szlan en Tygert

- [Structuur zoeken met wille keurigheid: Probabilistic-algoritmen voor het samen stellen van geschatte matrix ontledingen](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (PDF-down load). Halko, Martinsson en Tropp.

## <a name="how-to-configure-pca-anomaly-detection"></a>De anomalie detectie van de PCA configureren

1. Voeg de module voor **afwijkings detectie op basis van PCA** toe aan uw pijp lijn in de ontwerp functie. U kunt deze module vinden in de categorie **anomalie detectie** .

2. Klik in het rechterdeel venster van de module voor **anomalie detectie op basis van PCA** op de optie **trainings modus** en geef aan of u het model wilt trainen met een specifieke set para meters, of gebruik een parameter opruiming om de beste para meters te vinden.

    - **Eén para meter**: Selecteer deze optie als u weet hoe u het model wilt configureren en geef een specifieke set waarden als argumenten op.

3. **Aantal onderdelen dat moet worden gebruikt in PCA**: Geef het aantal uitvoer functies of onderdelen op dat u wilt uitvoeren.

    Het besluit hoeveel onderdelen u moet opnemen is een belang rijk onderdeel van het ontwerp van het experiment met behulp van PCA. Algemene richt lijnen zijn dat u niet hetzelfde aantal PCA-onderdelen moet gebruiken als er variabelen zijn. In plaats daarvan moet u beginnen met een kleiner aantal onderdelen en deze verg Roten totdat aan sommige criteria wordt voldaan.

    De beste resultaten worden verkregen wanneer het aantal uitvoer onderdelen kleiner is **dan** het aantal functie kolommen dat beschikbaar is in de gegevensset.

4. Geef op hoeveel oversteek proeven moeten worden uitgevoerd tijdens wille keurige PCA-trainingen. In het geval van anomalie detectie problemen is het moeilijk om standaard PCA-technieken toe te passen. Als u een aantal oversteek proeven opgeeft, kunt u het aantal doel instanties verhogen.

    Als u 1 opgeeft, wordt er geen oversteek proef uitgevoerd. Als u een waarde opgeeft die hoger is dan 1, worden extra voor beelden gegenereerd voor het maken van het model.

    Er zijn twee opties, afhankelijk van of u een para meter-sweep gebruikt of niet:

    - **Oversampling para meter voor wille keurige PCA**: Typ een enkel geheel getal dat de verhouding van de oversteek proef van de minderheids klasse op de normale klasse vertegenwoordigt. (Beschikbaar wanneer u de **Single para meter** -Trainings methode gebruikt.)

    > [!NOTE]
    > U kunt de oversteeke gegevensset niet weer geven. Zie [technische opmerkingen](#technical-notes)voor meer informatie over het gebruik van oversteek proeven met PCA.

5. **Invoer functie gemiddelde normalisatie inschakelen**: Selecteer deze optie om alle invoer functies te normaliseren tot een gemiddelde van nul. Normalisatie of schalen naar nul wordt meestal aanbevolen voor PCA, omdat het doel van de PCA is om de variantie van variabelen te maximaliseren.

     Deze optie is standaard ingeschakeld. Schakel deze optie uit als er al waarden zijn genormaliseerd met een andere methode of schaal.

6. Verbind een gelabelde trainings gegevensset en een van de trainings modules:

    - Als u de optie **trainers modus maken** instelt op **één para meter**, gebruikt u de module [anomalie detectie model](train-anomaly-detection-model.md) .

7. Verzend de pijp lijn.

## <a name="results"></a>Resultaten

Wanneer de training is voltooid, kunt u het getrainde model opslaan of verbinding maken met de module [score model](score-model.md) om afwijkende scores te voors pellen.

Voor het evalueren van de resultaten van een afwijkings detectie model zijn enkele extra stappen vereist:

1. Zorg ervoor dat er een score kolom beschikbaar is in beide gegevens sets

    Als u probeert een anomalie detectie model te evalueren en de fout op te halen, is er geen score kolom in de Score van de gescoorde gegevensset om te vergelijken. Dit betekent dat u een typische evaluatie gegevensset gebruikt die een kolom Label bevat, maar geen waarschijnlijkheids scores. U moet een gegevensset kiezen die overeenkomt met de schema-uitvoer voor afwijkings detectie modellen, waaronder een kolom met **gescoorde labels** en **gescoorde kansen** .

2. Zorg ervoor dat label kolommen zijn gemarkeerd

    Soms worden de meta gegevens die zijn gekoppeld aan de kolom Label, verwijderd in de pipeline-grafiek. Als dit het geval is, kunt u, wanneer u de module [model evalueren](evaluate-model.md) gebruikt om de resultaten van twee anomalie detectie modellen te vergelijken, de fout melding ' er is geen label kolom in de gescoorde gegevensset ', of ' er is geen label kolom in de Score van de gescoorde gegevensset om te vergelijken ' weer geven.

    U kunt deze fout voor komen door de module [meta gegevens bewerken](edit-metadata.md) voor de module [Evaluate model](evaluate-model.md) toe te voegen. Gebruik de kolom kiezer om de kolom klasse te kiezen en selecteer in de vervolg keuzelijst **velden** de optie **Label**.

3. Gebruik [python script uitvoeren](execute-python-script.md) om de categorieën van de label kolom als 1 (positief, normaal) en 0 (negatief, abnormaal) aan te passen.

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Technische opmerkingen

Dit algoritme maakt gebruik van PCA om de subruimte met de normale klasse te benaderen. De subruimte wordt omspannen door eigenvectors die zijn gekoppeld aan de bovenste eigenvalues van de matrix voor gegevens COVARIANTIE. Voor elke nieuwe invoer berekent de afwijkende detector eerst de projectie op de eigenvectors en berekent de genormaliseerde reconstructie fout. Deze fout is de afwijkende Score. Hoe hoger de fout, hoe afwijkende het exemplaar. Zie voor meer informatie over hoe de normale ruimte wordt berekend Wikipedia: [Principal component Analysis](https://wikipedia.org/wiki/Principal_component_analysis) 


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

Zie [uitzonde ringen en fout codes voor de ontwerp functie (preview)](designer-error-codes.md) voor een lijst met fouten die specifiek zijn voor de ontwerp modules.