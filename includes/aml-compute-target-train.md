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
ms.openlocfilehash: 764e4c33182499b922499c798d57fd05d8859742
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673387"
---
**Reken doelen kunnen opnieuw worden gebruikt voor een trainings taak naar de volgende**. Als u bijvoorbeeld een externe virtuele machine aan uw werk ruimte koppelt, kunt u deze opnieuw gebruiken voor meerdere taken.  Voor machine learning pijp lijnen gebruikt u de juiste [pijplijn stap](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) voor elk Compute-doel.

|Trainings &nbsp; doelen|[Geautomatiseerde machine learning](../articles/machine-learning/concept-automated-ml.md) | [Machine learning-pijplijnen](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning Designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Lokale computer](../articles/machine-learning/how-to-set-up-training-targets.md#local)| ja | &nbsp; | &nbsp; |
|[Azure Machine Learning Compute-Cluster](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Ja & <br/>afstemming &nbsp; afstemmen | ja | ja |
|[Reken instantie Azure Machine Learning](../articles/machine-learning/concept-compute-instance.md) | Ja & <br/>afstemming afstemmen | ja |  |
|[Externe VM](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Ja & <br/>afstemming afstemmen | ja | &nbsp; |
|[Azure- &nbsp; Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| Ja (alleen de lokale SDK-modus) | ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | ja | &nbsp; |
