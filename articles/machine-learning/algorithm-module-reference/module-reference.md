---
title: Naslaginformatie over algoritmen en modules
titleSuffix: Azure Machine Learning service
description: Meer informatie over de beschik bare modules in de Azure Machine Learning Visual Interface
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 2d81c407c17c50c34afd15a99d2f8ac2f8c5361e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692839"
---
# <a name="algorithm--module-reference-overview"></a>Overzicht van de referentie algoritme-& module

Deze referentie-inhoud biedt de technische achtergrond informatie over elk van de machine learning-algoritmen en-modules die beschikbaar zijn in de visuele interface (preview) van Azure Machine Learning service.

Elke module vertegenwoordigt een set code die onafhankelijk kan worden uitgevoerd en een machine learning taak kan uitvoeren, op basis van de vereiste invoer. Een module kan een bepaald algoritme bevatten of een taak uitvoeren die belang rijk is in machine learning, zoals ontbrekende vervanging van waarden of statistische analyses.

> [!TIP]
> In een pijp lijn in de visuele interface kunt u informatie over een specifieke module ophalen. Selecteer de module en selecteer vervolgens de koppeling **meer Help** in het deel venster **snelle Help** .

## <a name="modules"></a>Modules

Modules zijn ingedeeld op functionaliteit:

| Functionaliteit | Beschrijving | Module |
| --- |--- | ---- |
| Conversies gegevensindeling | Gegevens converteren tussen de verschillende bestands indelingen die worden gebruikt in machine learning, | [Converteren naar CSV](convert-to-csv.md) |
| Gegevens invoer en-uitvoer | Verplaats gegevens van Cloud bronnen naar uw experiment. Schrijf uw resultaten of tussenliggende gegevens naar Azure Storage, een SQL database of Hive tijdens het uitvoeren van een experiment, of gebruik Cloud opslag voor het uitwisselen van gegevens tussen experimenten.  | [Gegevens importeren](import-data.md)<br/>[Gegevens exporteren](export-data.md)<br/>[Gegevens hand matig invoeren](enter-data-manually.md) |
| Gegevenstransformatie | Bewerkingen op gegevens die uniek zijn voor machine learning, zoals het normaliseren of binning van gegevens, het selecteren van functies en het verminderen van de dimensionaliteit.| [Kolommen selecteren in gegevensset](select-columns-in-dataset.md) <br/> [Meta gegevens bewerken](edit-metadata.md) <br/> [Ontbrekende gegevens opschonen](clean-missing-data.md) <br/>  [Functie-hashing](feature-hashing.md) <br/>  [N-gram functies uit tekst extra heren](extract-n-gram-features-from-text.md) <br/> [Kolommen toevoegen](add-columns.md) <br/> [Rijen toevoegen](add-rows.md) <br/> [Dubbele rijen verwijderen](remove-duplicate-rows.md) <br/> [Tekst voorverwerken](preprocess-text.md) <br/> [Gegevens samen voegen](join-data.md) <br/> [Gegevens splitsen](split-data.md) <br/> [Gegevens normaliseren](normalize-data.md) <br/> [Partitie en voor beeld](partition-and-sample.md) <br/> [Clip waarden](clip-values.md) <br/> [SQL-trans formatie Toep assen](apply-sql-transformation.md)|
| Python-en R-modules | Schrijf code en sluit deze in een module in om python en R te integreren met uw experiment. | [Python-script uitvoeren](execute-python-script.md)   <br/> [Python-model maken](create-python-model.md) <br/> [R-script uitvoeren](execute-r-script.md)
|  | **Machine learning-algoritmen**: | |
| Classificatie | Voors pellen van een klasse.  U kunt kiezen uit binaire (twee klasse) of meerdere klasse-algoritmen.| [Multi Class-besluitvormings forest](multiclass-decision-forest.md) <br/> [Beslissings structuur met een geboostte klasse met klassen](multiclass-boosted-decision-tree.md) <br/> [Multiklasse-logistieke regressie](multiclass-logistic-regression.md)  <br/> [Multi Class Neural-netwerk](multiclass-neural-network.md)  <br/>  [Logistiek regressie met twee klassen](two-class-logistic-regression.md)  <br/>[Gemiddelde Perceptron voor twee klassen](two-class-averaged-perceptron.md) <br/> [@No__t_1Boosted &nbsp;Decision met twee klassen &nbsp;Tree](two-class-boosted-decision-tree.md)  <br/> [Besluitvormings forest met twee klassen](two-class-decision-forest.md)  <br/> [Neural-netwerk met twee klassen](two-class-neural-network.md)  <br/> [@No__t_4Machine&#8209;van klasse &nbsp;Support &nbsp;Vector](two-class-support-vector-machine.md) 
| Clustering | Groepeert gegevens tegelijk.| [K: Clustering](k-means-clustering.md)
| Regressie | Een waarde voors pellen. | [Lineaire regressie](linear-regression.md)  <br/> [Regressie van Neural-netwerk](neural-network-regression.md)  <br/> [Regressie voor beslissings structuur](decision-forest-regression.md)  <br/> [Gestimuleerde &nbsp;Decision &nbsp;Tree &nbsp;Regression](boosted-decision-tree-regression.md)
|  | **Modellen bouwen en evalueren**: | |
| Trainen   | Voer gegevens uit via de algoritme. | [Model trainen](train-model.md)  <br/> [Cluster model trainen](train-clustering-model.md)    |
| Model evalueren | Meet de nauw keurigheid van het getrainde model. |  [Model evalueren](evaluate-model.md)
| Score | Profiteer van voor spellingen van het model dat u zojuist hebt getraind. | [Trans formatie Toep assen](apply-transformation.md)<br/>[@No__t_1Data &nbsp;to toewijzen &nbsp;Clusters](assign-data-to-clusters.md) <br/>[Score model](score-model.md)
| Statistische functies | Geef een groot aantal verschillende numerieke methoden op die betrekking hebben op Data Science. | [Wiskundige bewerking Toep assen](apply-math-operation.md) <br/> [Gegevens samenvatten](summarize-data.md)|

## <a name="error-messages"></a>Foutberichten

Meer informatie over de [fout berichten en uitzonderings codes](machine-learning-module-error-codes.md) die u kunt tegen komen met behulp van modules in de Visual-interface van Azure machine learning service.
