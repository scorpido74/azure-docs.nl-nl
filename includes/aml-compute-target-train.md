---
title: bestand opnemen
description: bestand opnemen
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/19/2020
ms.openlocfilehash: 54328c7ae4fe2112a718bd82b9f7330c9e95ec69
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146727"
---
**Rekendoelen kunnen opnieuw worden gebruikt voor trainingstaken**. Nadat u een externe VM koppelt aan uw werkruimte, kunt u die bijvoorbeeld voor meerdere taken gebruiken.  Voor machine learning-pijplijn gebruikt u de juiste [pijplijnstap](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) voor elk rekendoel.

|Trainingsdoelen&nbsp;|[Geautomatiseerde machine learning](../articles/machine-learning/concept-automated-ml.md) | [Machine learning-pijplijnen](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning-ontwerpprogramma](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Lokale computer](../articles/machine-learning/how-to-create-attach-compute-sdk.md#local)| ja | &nbsp; | &nbsp; |
|[Azure Machine Learning-rekenclusters](../articles/machine-learning/how-to-create-attach-compute-sdk.md#amlcompute)| ja en <br/>hyperparameter&nbsp;afstemming | ja | ja |
|[Azure Machine Learning-rekeninstantie](../articles/machine-learning/how-to-create-attach-compute-sdk.md#instance) | ja en <br/>hyperparameterafstemming | ja |  |
|[Externe VM](../articles/machine-learning/how-to-create-attach-compute-sdk.md#vm) | ja en <br/>hyperparameterafstemming | ja | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-attach-compute-sdk.md#databricks)| ja (alleen de lokale SDK-modus) | ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-attach-compute-sdk.md#adla) | &nbsp; | ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-create-attach-compute-sdk.md#hdinsight) | &nbsp; | ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-create-attach-compute-sdk.md#azbatch) | &nbsp; | ja | &nbsp; |
