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
ms.date: 12/04/2019
ms.openlocfilehash: 6106d4e0801500b0429e634651f3de342646b754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77156454"
---
**Compute-doelen kunnen worden hergebruikt van de ene trainingstaak naar de volgende**. Zodra u bijvoorbeeld een externe virtuele machine aan uw werkruimte koppelt, u deze opnieuw gebruiken voor meerdere taken.  Voor machine learning-pijplijnen gebruikt u de juiste [pijplijnstap](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) voor elk rekendoel.

|Opleidingsdoelstellingen &nbsp;|[Geautomatiseerde machine learning](../articles/machine-learning/concept-automated-ml.md) | [Machine learning-pijplijnen](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning-ontwerper](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Lokale computer](../articles/machine-learning/how-to-set-up-training-targets.md#local)| ja | &nbsp; | &nbsp; |
|[Azure Machine Learning-computecluster](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Ja & <br/>hyperparameter&nbsp;tuning | ja | ja |
|[Externe VM](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Ja & <br/>hyperparameter tuning | ja | &nbsp; |
|[Azure-gegevensstenen&nbsp;](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| ja (alleen in de sdk-modus) | ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | ja | &nbsp; |
