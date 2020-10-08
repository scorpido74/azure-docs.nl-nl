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
ms.openlocfilehash: bbd32f7e40e3039a69a95b4458de86ff35f26867
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91644842"
---
**Rekendoelen kunnen opnieuw worden gebruikt voor trainingstaken**. Nadat u een externe VM koppelt aan uw werkruimte, kunt u die bijvoorbeeld voor meerdere taken gebruiken.  Voor machine learning-pijplijn gebruikt u de juiste [pijplijnstap](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) voor elk rekendoel.

|Trainingsdoelen&nbsp;|[Geautomatiseerde machine learning](../articles/machine-learning/concept-automated-ml.md) | [Machine learning-pijplijnen](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning-ontwerpprogramma](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Lokale computer](../articles/machine-learning/how-to-create-attach-compute-sdk.md#local)| ja | &nbsp; | &nbsp; |
|[Azure Machine Learning-rekenclusters](../articles/machine-learning/how-to-create-attach-compute-sdk.md#amlcompute)| ja en <br/>hyperparameter&nbsp;afstemming | ja | ja |
|[Azure Machine Learning-rekeninstantie](../articles/machine-learning/how-to-create-attach-compute-sdk.md#instance) | Ja (via SDK) | ja |  |
|[Externe VM](../articles/machine-learning/how-to-create-attach-compute-sdk.md#vm) | ja en <br/>hyperparameterafstemming | ja | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-attach-compute-sdk.md#databricks)| ja (alleen de lokale SDK-modus) | ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-attach-compute-sdk.md#adla) | &nbsp; | ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-create-attach-compute-sdk.md#hdinsight) | &nbsp; | ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-create-attach-compute-sdk.md#azbatch) | &nbsp; | ja | &nbsp; |
