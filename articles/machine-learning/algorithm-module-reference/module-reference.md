---
title: Naslaginformatie over algoritmen en modules
description: Meer informatie over de modules die beschikbaar zijn in Azure Machine Learning designer (preview)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: c46c21b5662b924552f850f226a12c7350b4625a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458195"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Algoritme & moduleverwijzing voor Azure Machine Learning-ontwerper (voorbeeld)

Deze referentie-inhoud biedt de technische achtergrond van elk van de machine learning-algoritmen en -modules die beschikbaar zijn in Azure Machine Learning-ontwerper (preview).

Elke module vertegenwoordigt een set code die onafhankelijk kan worden uitgevoerd en een machine learning-taak kan uitvoeren, gezien de vereiste ingangen. Een module kan een bepaald algoritme bevatten of een taak uitvoeren die belangrijk is voor machine learning, zoals vervanging van ontbrekende waarden of statistische analyse.

Zie voor hulp bij het kiezen van algoritmen 
* [Algoritmen selecteren](../how-to-select-algorithms.md)
* [Cheat Sheet azure machine learning-algoritme](../algorithm-cheat-sheet.md)

> [!TIP]
> In elke pijplijn in de ontwerper u informatie krijgen over een specifieke module. Selecteer de module en selecteer de **koppeling Meer help** in het deelvenster Snelle **Help.**

## <a name="data-preparation-modules"></a>Modules voor gegevensvoorbereiding


| Functionaliteit | Beschrijving | Module |
| --- |--- | --- |
| Invoer en uitvoer van gegevens | Verplaats gegevens uit cloudbronnen naar uw pijplijn. Schrijf uw resultaten of tussenliggende gegevens naar Azure Storage, een SQL-database of Hive, terwijl u een pijplijn uitvoert of gebruik cloudopslag om gegevens uit te wisselen tussen pijplijnen.  | [Gegevens handmatig invoeren](enter-data-manually.md) <br/> [Gegevens exporteren](export-data.md) <br/> [Gegevens importeren](import-data.md) |
| Gegevenstransformatie | Bewerkingen op gegevens die uniek zijn voor machine learning, zoals het normaliseren of binning van gegevens, dimensionaliteitsreductie en het converteren van gegevens tussen verschillende bestandsindelingen.| [Kolommen toevoegen](add-columns.md) <br/> [Rijen toevoegen](add-rows.md) <br/> [Wiskundige bewerking toepassen](apply-math-operation.md) <br/> [SQL-transformatie toepassen](apply-sql-transformation.md) <br/> [Ontbrekende gegevens opschonen](clean-missing-data.md) <br/> [Waarden inperken](clip-values.md) <br/> [Converteren naar CSV](convert-to-csv.md) <br/> [Converteren naar gegevensset](convert-to-dataset.md) <br/> [Converteren naar indicatorwaarden](convert-to-indicator-values.md) <br/> [Metagegevens bewerken](edit-metadata.md) <br/> [Gegevens samenvoegen](join-data.md) <br/> [Gegevens normaliseren](normalize-data.md) <br/> [Partitie en voorbeeld](partition-and-sample.md)  <br/> [Dubbele rijen verwijderen](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Kolomtransformatie selecteren](select-columns-transform.md) <br/> [Kolommen in gegevensset selecteren](select-columns-in-dataset.md) <br/> [Gesplitste gegevens](split-data.md) |
| Functieselectie | Selecteer een subset van relevante, nuttige functies die u gebruiken bij het bouwen van een analytisch model. | [Functieselectie op basis van filters](filter-based-feature-selection.md) <br/> [Belang van permutatiefunctie](permutation-feature-importance.md) |
| Statistische functies | Bieden een breed scala aan statistische methoden met betrekking tot data science. | [Gegevens samenvatten](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Machine learning-algoritmen

| Functionaliteit | Beschrijving | Module |
| --- |--- | --- |
| Regressie | Voorspel een waarde. | [Regressie versterkte beslissingsstructuur](boosted-decision-tree-regression.md) <br/> [Regressie beslissingsforest](decision-forest-regression.md) <br/> [Lineaire regressie](linear-regression.md)  <br/> [Regressie neuraal netwerk](neural-network-regression.md)  <br/> |
| Clustering | Data groeperen.| [K-means-clustering](k-means-clustering.md)
| Classificatie | Voorspel een les.  Kies uit binaire (tweeklassen) of meerklassenalgoritmen.| [Versterkte beslissingsstructuur met meerdere klassen](multiclass-boosted-decision-tree.md) <br/> [Beslissingsforest met meerdere klassen](multiclass-decision-forest.md) <br/> [Logistieke regressie met meerdere klassen](multiclass-logistic-regression.md)  <br/> [Neuraal netwerk met meerdere klassen](multiclass-neural-network.md) <br/> [Eén vs. Alle Multiclass](one-vs-all-multiclass.md) <br/> [Gemiddeld perceptron met twee klassen](two-class-averaged-perceptron.md) <br/>  [Two-Class Boosted Decision Tree](two-class-boosted-decision-tree.md)  <br/> [Beslissingsforest met twee klassen](two-class-decision-forest.md) <br/>  [Logistieke regressie met twee klassen](two-class-logistic-regression.md) <br/> [Neuraal netwerk met twee klassen](two-class-neural-network.md) <br/> [Ondersteuning voor vectormachine met twee klassen](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Modules voor het bouwen en evalueren van modellen

| Functionaliteit | Beschrijving | Module |
| --- |--- | --- |
| Modeltraining | Voer gegevens door het algoritme. |  [Clustermodel trainen](train-clustering-model.md) <br/> [Trainingsmodel](train-model.md)  <br/> [Model Hyperparameters afstemmen](tune-model-hyperparameters.md) |
| Modelscore en -evaluatie | Meet de nauwkeurigheid van het getrainde model. | [Transformatie toepassen](apply-transformation.md) <br/> [Gegevens toewijzen aan clusters](assign-data-to-clusters.md) <br/> [Kruisvalidatie van model valideren](cross-validate-model.md) <br/> [Model evalueren](evaluate-model.md) <br/> [Score Model](score-model.md) |
| Python-taal | Schrijf code en sluit deze in een module in om Python te integreren met uw pijplijn. | [Python-model maken](create-python-model.md) <br/> [Python-script uitvoeren](execute-python-script.md) |
| R-taal | Schrijf code en sluit deze in een module in om R te integreren met uw pijplijn. | [R-Script uitvoeren](execute-r-script.md) |
| Tekstanalyse | Bied gespecialiseerde rekentools voor het werken met zowel gestructureerde als ongestructureerde tekst. | [N-Gram-functies uit tekst halen](extract-n-gram-features-from-text.md) <br/> [Functie-hashing](feature-hashing.md) <br/> [Tekst voorverwerken](preprocess-text.md) <br/> [Latente Dirichlet-toewijzing](latent-dirichlet-allocation.md) |
| Aanbeveling | Bouw aanbevelingsmodellen. | [Aanbevelingsfunctie voor evaluatie](evaluate-recommender.md) <br/> [Aanbevelingsfunctie voor SVD-score ](score-svd-recommender.md) <br/> [Aanbevelingsfunctie van SVD-training](train-SVD-recommender.md) |
| Anomaliedetectie | Bouw anomaliedetectiemodellen. | [PcA-gebaseerde anomaliedetectie](pca-based-anomaly-detection.md) <br/> [Trein anomalie detectiemodel](train-anomaly-detection-model.md) |


## <a name="web-service"></a>Webservice

Meer informatie over de [webservicemodules](web-service-input-output.md) die nodig zijn voor realtime conclusies in Azure Machine Learning-ontwerper.

## <a name="error-messages"></a>Foutberichten

Meer informatie over de [foutberichten en uitzonderingscodes](designer-error-codes.md) die u tegenkomen met behulp van modules in Azure Machine Learning designer.

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Bouw een model in designer om automatische prijzen te voorspellen](../tutorial-designer-automobile-price-train-score.md)
