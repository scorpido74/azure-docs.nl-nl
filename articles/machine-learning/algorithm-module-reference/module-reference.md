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
ms.openlocfilehash: 065931140894478caee9d4ea49dac49f2415716b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128644"
---
# <a name="algorithm--module-reference-overview"></a>Overzicht van de referentie algoritme-& module

Deze referentie-inhoud biedt de technische achtergrond informatie over elk van de machine learning-algoritmen en-modules die beschikbaar zijn in de visuele interface (preview) van Azure Machine Learning service.

Elke module vertegenwoordigt een set code die onafhankelijk kan worden uitgevoerd en een machine learning taak kan uitvoeren, op basis van de vereiste invoer. Een module kan een bepaald algoritme bevatten of een taak uitvoeren die belang rijk is in machine learning, zoals ontbrekende vervanging van waarden of statistische analyses.

> [!TIP]
> In elk experiment in de visuele interface kunt u informatie ophalen over een specifieke module. Selecteer de module en selecteer vervolgens de koppeling **meer Help** in het deel venster **snelle Help** .

## <a name="modules"></a>Modules

Modules zijn ingedeeld op functionaliteit:

| Functionaliteit | Description | Module |
| --- |--- | ---- |
| Conversies gegevensindeling | Gegevens converteren tussen de verschillende bestands indelingen die worden gebruikt in machine learning, | [Converteren naar CSV](convert-to-csv.md) |
| Gegevens invoer en-uitvoer | Verplaats gegevens van Cloud bronnen naar uw experiment. Schrijf uw resultaten of tussenliggende gegevens naar Azure Storage, een SQL database of Hive tijdens het uitvoeren van een experiment, of gebruik Cloud opslag voor het uitwisselen van gegevens tussen experimenten.  | [Gegevens importeren](import-data.md)<br/>[Gegevens exporteren](export-data.md)<br/>[Gegevens hand matig invoeren](enter-data-manually.md) |
| Gegevenstransformatie | Bewerkingen op gegevens die uniek zijn voor machine learning, zoals het normaliseren of binning van gegevens, het selecteren van functies en het verminderen van de dimensionaliteit.| [Kolommen selecteren in gegevensset](select-columns-in-dataset.md) <br/> [Meta gegevens bewerken](edit-metadata.md) <br/> [Ontbrekende gegevens opschonen](clean-missing-data.md) <br/> [Kolommen toevoegen](add-columns.md) <br/> [Rijen toevoegen](add-rows.md) <br/> [Dubbele rijen verwijderen](remove-duplicate-rows.md) <br/> [Gegevens samen voegen](join-data.md) <br/> [Gegevens splitsen](split-data.md) <br/> [Gegevens normaliseren](normalize-data.md) <br/> [Partitie en voor beeld](partition-and-sample.md) |
| Python-en R-modules | Schrijf code en sluit deze in een module in om python en R te integreren met uw experiment. | [Python-script uitvoeren](execute-python-script.md)   <br/> [Python-model maken](create-python-model.md) <br/> [R-script uitvoeren](execute-r-script.md)
|  | **Machine learning**-algoritmen: | |
| Classificatie | Voors pellen van een klasse.  U kunt kiezen uit binaire (twee klasse) of meerdere klasse-algoritmen.| [Multi Class-besluitvormings forest](multiclass-decision-forest.md) <br/> [Beslissings structuur met een geboostte klasse met klassen](multiclass-boosted-decision-tree.md) <br/> [Multiklasse-logistieke regressie](multiclass-logistic-regression.md)  <br/> [Multi Class Neural-netwerk](multiclass-neural-network.md)  <br/>  [Logistiek regressie met twee klassen](two-class-logistic-regression.md)  <br/>[Gemiddelde Perceptron voor twee klassen](two-class-averaged-perceptron.md) <br/> [Geboostte&nbsp;beslissings&nbsp;structuur met twee klassen&nbsp;](two-class-boosted-decision-tree.md)  <br/> [Besluitvormings forest met twee klassen](two-class-decision-forest.md)  <br/> [Neural-netwerk met twee klassen](two-class-neural-network.md)  <br/> [Vector&nbsp;&nbsp;&#8209;computermetondersteuningvoortwee&nbsp;klassen](two-class-support-vector-machine.md) 
| Clustering | Groepeert gegevens tegelijk.| [K: Clustering](k-means-clustering.md)
| Regressie | Een waarde voors pellen. | [Lineaire regressie](linear-regression.md)  <br/> [Regressie van Neural-netwerk](neural-network-regression.md)  <br/> [Regressie voor beslissings structuur](decision-forest-regression.md)  <br/> [Regressie verbetering van de&nbsp;&nbsp;&nbsp;beslissings structuur](boosted-decision-tree-regression.md)
|  | **Modellen bouwen en evalueren**: | |
| Trainen   | Voer gegevens uit via de algoritme. | [Model trainen](train-model.md)  <br/> [Cluster model trainen](train-clustering-model.md)    |
| Model evalueren | Meet de nauw keurigheid van het getrainde model. |  [Model evalueren](evaluate-model.md)
| Score | Profiteer van voor spellingen van het model dat u zojuist hebt getraind. | [Trans formatie Toep assen](apply-transformation.md)<br/>[Gegevens&nbsp;toewijzen&nbsp;aanclusters&nbsp;](assign-data-to-clusters.md) <br/>[Score model](score-model.md)

## <a name="error-messages"></a>Foutberichten

Meer informatie over de [fout berichten en uitzonderings codes](machine-learning-module-error-codes.md) die u kunt tegen komen met behulp van modules in de Visual-interface van Azure machine learning service.
