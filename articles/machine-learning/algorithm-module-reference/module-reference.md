---
title: Naslaginformatie over algoritmen en modules
description: Meer informatie over de beschik bare modules in Azure Machine Learning Designer
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 12/17/2019
ms.openlocfilehash: c265c2386b31fd02a5aa635c343be5d3cb227722
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76312172"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Naslag informatie voor algoritme & module voor Azure Machine Learning Designer

Deze referentie-inhoud biedt de technische achtergrond informatie over elk van de machine learning algoritmen en modules die beschikbaar zijn in Azure Machine Learning Designer.

Elke module vertegenwoordigt een set code die onafhankelijk kan worden uitgevoerd en een machine learning taak kan uitvoeren, op basis van de vereiste invoer. Een module kan een bepaald algoritme bevatten of een taak uitvoeren die belang rijk is in machine learning, zoals ontbrekende vervanging van waarden of statistische analyses.

> [!TIP]
> In een pijp lijn in de ontwerp functie kunt u informatie ophalen over een specifieke module. Selecteer de module en selecteer vervolgens de koppeling **meer Help** in het deel venster **snelle Help** .

## <a name="modules"></a>Modules

Modules zijn ingedeeld op functionaliteit:

| Functionaliteit | Beschrijving | Module |
| --- |--- | --- |
|  | **Gegevens voorbereiding**: | |
| Invoer en uitvoer van gegevens | Verplaats gegevens van Cloud bronnen naar uw pijp lijn. Schrijf uw resultaten of tussenliggende gegevens naar Azure Storage, een SQL database of Hive, terwijl u een pijp lijn uitvoert, of gebruik Cloud opslag voor het uitwisselen van gegevens tussen pijp lijnen.  | [Gegevens hand matig invoeren](enter-data-manually.md) <br/> [Gegevens exporteren](export-data.md) <br/> [Gegevens importeren](import-data.md) |
| Gegevenstransformatie | Bewerkingen op gegevens die uniek zijn voor machine learning, zoals het normaliseren of Binningen van gegevens, het beperken van de afmetingen en het converteren van gegevens over verschillende bestands indelingen.| [Kolommen toevoegen](add-columns.md) <br/> [Rijen toevoegen](add-rows.md) <br/> [Wiskundige bewerking Toep assen](apply-math-operation.md) <br/> [SQL-trans formatie Toep assen](apply-sql-transformation.md) <br/> [Ontbrekende gegevens opschonen](clean-missing-data.md) <br/> [Clip waarden](clip-values.md) <br/> [Converteren naar CSV](convert-to-csv.md) <br/> [Converteren naar gegevensset](convert-to-dataset.md) <br/> [Meta gegevens bewerken](edit-metadata.md) <br/> [Gegevens samen voegen](join-data.md) <br/> [Gegevens normaliseren](normalize-data.md) <br/> [Partitie en voor beeld](partition-and-sample.md)  <br/> [Dubbele rijen verwijderen](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Kolom transformatie selecteren](select-columns-transform.md) <br/> [Kolommen selecteren in gegevensset](select-columns-in-dataset.md) <br/> [Gegevens splitsen](split-data.md) |
| Onderdelen selecteren | Selecteer een subset van relevante, nuttige functies om te gebruiken bij het bouwen van een analytisch model. | [Functies selecteren op basis van filter](filter-based-feature-selection.md) <br/> [Permutatie functie urgentie](permutation-feature-importance.md) |
| Statistische functies | Bieden een groot aantal statistische methoden die zijn gerelateerd aan data Science. | [Gegevens samenvatten](summarize-data.md)|
|  | **Machine learning-algoritmen**: | |
| Regressie | Een waarde voors pellen. | [Regressie verbetering van de beslissings structuur](boosted-decision-tree-regression.md) <br/> [Regressie voor beslissings structuur](decision-forest-regression.md) <br/> [Lineaire regressie](linear-regression.md)  <br/> [Regressie van Neural-netwerk](neural-network-regression.md)  <br/> |
| Clustering | Groepeert gegevens tegelijk.| [K: Clustering](k-means-clustering.md)
| Classificatie | Voors pellen van een klasse.  U kunt kiezen uit binaire (twee klasse) of meerdere klasse-algoritmen.| [Beslissings structuur met een geboostte klasse met klassen](multiclass-boosted-decision-tree.md) <br/> [Multi Class-besluitvormings forest](multiclass-decision-forest.md) <br/> [Multiklasse-logistieke regressie](multiclass-logistic-regression.md)  <br/> [Multi Class Neural-netwerk](multiclass-neural-network.md) <br/> [Eén vs. alle multi klassen](one-vs-all-multiclass.md) <br/> [Gemiddelde Perceptron voor twee klassen](two-class-averaged-perceptron.md) <br/>  [Geboostte beslissings structuur met twee klassen](two-class-boosted-decision-tree.md)  <br/> [Besluitvormings forest met twee klassen](two-class-decision-forest.md) <br/>  [Logistiek regressie met twee klassen](two-class-logistic-regression.md) <br/> [Neural-netwerk met twee klassen](two-class-neural-network.md) <br/> [Vector computer met ondersteuning voor twee klassen](two-class-support-vector-machine.md) | 
|  | **Modellen bouwen en evalueren**: | |
| Modeltraining | Voer gegevens uit via de algoritme. |  [Cluster model trainen](train-clustering-model.md) <br/> [Model trainen](train-model.md)  <br/> [Model Hyper parameters afstemmen](tune-model-hyperparameters.md) |
| Model leren en evalueren | Meet de nauw keurigheid van het getrainde model. | [Trans formatie Toep assen](apply-transformation.md) <br/> [Gegevens toewijzen aan clusters](assign-data-to-clusters.md) <br/> [Model voor kruis valideren](cross-validate-model.md) <br/> [Model evalueren](evaluate-model.md) <br/> [Score model](score-model.md) |
| Python-taal | Schrijf code en sluit deze in een module in om python met uw pijp lijn te integreren. | [Python-model maken](create-python-model.md) <br/> [Python-script uitvoeren](execute-python-script.md) |
| R-taal | Schrijf code en sluit deze in een module in om R met uw pijp lijn te integreren. | [R-script uitvoeren](execute-r-script.md) |
| Tekstanalyse | Bieden speciale reken kundige hulp middelen voor het werken met zowel gestructureerde als ongestructureerde tekst. | [N-gram functies uit tekst extra heren](extract-n-gram-features-from-text.md) <br/> [Functie-hashing](feature-hashing.md) <br/> [Tekst voorverwerken](preprocess-text.md) |
| Aanbeveling | Aanbevelings modellen bouwen. | [Aanbeveling evalueren](evaluate-recommender.md) <br/> [Aanbeveling van Score SVD](score-svd-recommender.md) <br/> [Train SVD-aanbeveling](train-SVD-recommender.md) |

## <a name="error-messages"></a>Foutberichten

Meer informatie over de [fout berichten en uitzonderings codes](designer-error-codes.md) die u kunt tegen komen met behulp van modules in azure machine learning Designer.
