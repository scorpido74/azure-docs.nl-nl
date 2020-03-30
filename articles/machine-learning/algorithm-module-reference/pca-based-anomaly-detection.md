---
title: 'PCA-gebaseerde anomaliedetectie: naslaginformatie over modules'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de pca-gebaseerde anomaliedetectiemodule om een anomaliedetectiemodel te maken op basis van de Principal Component Analysis (PCA).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 0672b9769feae65c73a6f752a268968a7bad9e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502982"
---
# <a name="pca-based-anomaly-detection"></a>PcA-gebaseerde anomaliedetectie

In dit artikel wordt beschreven hoe u de **pca-gebaseerde anomaliedetectiemodule** gebruiken in Azure Machine Learning-ontwerper (preview), om een anomaliedetectiemodel te maken op basis van hoofdcomponentanalyse (PCA).

Deze module helpt u een model te bouwen in scenario's waarin het gemakkelijk is om trainingsgegevens van één klasse te verkrijgen, zoals geldige transacties, maar moeilijk om voldoende monsters van de beoogde afwijkingen te verkrijgen. 

Bijvoorbeeld, om frauduleuze transacties op te sporen, heb je vaak niet genoeg voorbeelden van fraude om op te trainen, maar heb je veel voorbeelden van goede transacties. De **PCA-gebaseerde anomaliedetectiemodule** lost het probleem op door beschikbare functies te analyseren om te bepalen wat een "normale" klasse is en afstandsstatistieken toe te passen om gevallen te identificeren die afwijkingen vertegenwoordigen. Hiermee u een model trainen met behulp van bestaande onevenwichtige gegevens.

## <a name="more-about-principal-component-analysis"></a>Meer informatie over principal component analysis

*Principal Component Analysis*, die vaak wordt afgekort tot PCA, is een gevestigde techniek in machine learning. PCA wordt vaak gebruikt in verkennende gegevensanalyse omdat het de innerlijke structuur van de gegevens onthult en de variantie in de gegevens verklaart.

PCA werkt door het analyseren van gegevens die meerdere variabelen bevatten. Het zoekt naar correlaties tussen de variabelen en bepaalt de combinatie van waarden die het beste verschillen in resultaten vastlegt. Deze gecombineerde functiewaarden worden gebruikt om een compactere functieruimte te creëren, de *belangrijkste componenten.*

Voor anomaliedetectie wordt elke nieuwe invoer geanalyseerd en het anomaliedetectiealgoritme berekent de projectie op de eigenvectoren, samen met een genormaliseerde reconstructiefout. De genormaliseerde fout wordt gebruikt als de anomaliescore. Hoe hoger de fout, hoe afwijkender de instantie is.

Zie deze documenten voor meer informatie over hoe PCA werkt en over de implementatie voor anomaliedetectie:

- [Een gerandomiseerd algoritme voor hoofdcomponentanalyse.](https://arxiv.org/abs/0809.2274) Rokhlin, Szlan en Tygert

- [Het vinden van structuur met willekeur: Probabilistic Algoritmen voor het construeren van benaderende Matrix Decomposities](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (PDF download). Halko, Martinsson en Tropp.

## <a name="how-to-configure-pca-anomaly-detection"></a>PcA-anomaliedetectie configureren

1. Voeg de **pca-gebaseerde anomaliedetectiemodule** toe aan uw pijplijn in de ontwerper. U vindt deze module in de categorie **Anomaliedetectie.**

2. Klik in het rechterdeelvenster van de **pca-gebaseerde anomaliedetectiemodule** op de optie **Trainingsmodus** en geef aan of u het model wilt trainen met behulp van een specifieke set parameters of een parametersweep wilt gebruiken om de beste parameters te vinden.

    - **Eén parameter:** selecteer deze optie als u weet hoe u het model wilt configureren en geef een specifieke set waarden op als argumenten.

3. **Aantal onderdelen dat u wilt gebruiken in PCA:** geef het aantal uitvoerfuncties of onderdelen op dat u wilt uitvoeren.

    De beslissing van hoeveel componenten op te nemen is een belangrijk onderdeel van het experiment ontwerp met behulp van PCA. Algemene richtlijnen zijn dat u niet hetzelfde aantal PCA-componenten moet opnemen als er variabelen zijn. In plaats daarvan moet u beginnen met een kleiner aantal componenten en deze verhogen totdat aan bepaalde criteria is voldaan.

    De beste resultaten worden verkregen wanneer het aantal uitvoercomponenten kleiner is **dan** het aantal functiekolommen dat beschikbaar is in de gegevensset.

4. Geef de hoeveelheid oversampling op die moet worden uitgevoerd tijdens gerandomiseerde PCA-training. Bij anomaliedetectieproblemen maakt onevenwichtige gegevens het moeilijk om standaard PCA-technieken toe te passen. Door een bepaalde hoeveelheid oversampling op te geven, u het aantal doelinstanties verhogen.

    Als u er 1 opgeeft, wordt er geen oversampling uitgevoerd. Als u een waarde opgeeft die hoger is dan 1, worden extra voorbeelden gegenereerd om het model te trainen.

    Er zijn twee opties, afhankelijk van of u een parameter sweep gebruikt of niet:

    - **Oversamplingparameter voor gerandomiseerde PCA**: Typ één geheel getal dat de verhouding van oversampling van de minderheidsklasse ten opzichte van de normale klasse vertegenwoordigt. (Beschikbaar bij het gebruik van de **trainingsmethode voor één parameter.)**

    > [!NOTE]
    > U de oversamplede gegevensset niet weergeven. Zie [Technische notities voor](#technical-notes)meer informatie over hoe oversampling wordt gebruikt met PCA.

5. **Invoerfunctie gemiddelde normalisatie inschakelen:** Selecteer deze optie om alle invoerfuncties te normaliseren tot een gemiddelde van nul. Normalisatie of schalen naar nul wordt over het algemeen aanbevolen voor PCA, omdat het doel van PCA is om de variantie tussen variabelen te maximaliseren.

     Deze optie is standaard ingeschakeld. Schakel deze optie uit als waarden al zijn genormaliseerd met een andere methode of schaal.

6. Verbind een gecodeerde trainingsgegevensset en een van de trainingsmodules:

    - Als u de optie **De trainermodus maken** instelt op **Eén parameter,** gebruikt u de module [Treinanomaliedetectiemodel.](train-anomaly-detection-model.md)

7. Verzend de pijplijn.

## <a name="results"></a>Resultaten

Wanneer de training is voltooid, u het getrainde model opslaan of koppelen aan de module [Scoremodel](score-model.md) om anomaliescores te voorspellen.

Voor het evalueren van de resultaten van een anomaliedetectiemodel zijn enkele aanvullende stappen vereist:

1. Ervoor zorgen dat er een scorekolom beschikbaar is in beide gegevenssets

    Als u een anomaliedetectiemodel probeert te evalueren en de fout "Er is geen scorekolom in de scoregegevensset te vergelijken", betekent dit dat u een typische evaluatiegegevensset gebruikt die een labelkolom bevat, maar geen kansscores. U moet een gegevensset kiezen die overeenkomt met de schema-uitvoer voor anomaliedetectiemodellen, waaronder een kolom **Scored Labels** en **Scored Probabilities.**

2. Ervoor zorgen dat labelkolommen zijn gemarkeerd

    Soms worden de metagegevens die aan de labelkolom zijn gekoppeld, verwijderd in de pijplijngrafiek. Als dit gebeurt, wanneer u de module [Model evalueren](evaluate-model.md) gebruikt om de resultaten van twee anomaliedetectiemodellen te vergelijken, u de fout krijgen: Er is geen labelkolom in de gescoorde gegevensset of Er is geen labelkolom in de gescoorde gegevensset om te vergelijken.

    U deze fout voorkomen door de module [Metagegevens bewerken](edit-metadata.md) toe te voegen vóór de module [Model evalueren.](evaluate-model.md) Gebruik de kolomkiezer om de kolom te kiezen en selecteer in de vervolgkeuzelijst **Velden** **label**.

3. Gebruik [Python Script uitvoeren](execute-python-script.md) om labelkolomcategorieën aan te passen als 1(positief, normaal) en 0 (negatief, abnormaal).

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Technische notities

Dit algoritme gebruikt PCA om de subruimte met de normale klasse te benaderen. De subruimte wordt overspannen door eigenvectoren die zijn gekoppeld aan de bovenste waardes van de gegevenscovariantiematrix. Voor elke nieuwe invoer berekent de anomaliedetector eerst de projectie op de eigenvectoren en berekent vervolgens de genormaliseerde reconstructiefout. Deze fout is de anomalie score. Hoe hoger de fout, hoe afwijkender de instantie. Zie Wikipedia: [Principal Component Analysis](https://wikipedia.org/wiki/Principal_component_analysis) voor meer informatie over de manier waarop de normale ruimte wordt berekend 


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 

Zie [Uitzonderingen en foutcodes voor de ontwerper (voorbeeld)](designer-error-codes.md) voor een lijst met fouten die specifiek zijn voor de ontwerpmodules.''