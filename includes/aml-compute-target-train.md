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
ms.openlocfilehash: 2c275e1d8f54191e12454f7e97a8dd59e7c84be2
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75539383"
---
**Reken doelen kunnen opnieuw worden gebruikt voor een trainings taak naar de volgende**. Als u bijvoorbeeld een externe virtuele machine aan uw werk ruimte koppelt, kunt u deze opnieuw gebruiken voor meerdere taken.  Voor machine learning pijp lijnen gebruikt u de juiste [pijplijn stap](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) voor elk Compute-doel.

|Trainings &nbsp;doelen|[Automatische ML](../articles/machine-learning/concept-automated-ml.md) | [ML-pijp lijnen](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning Designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Lokale computer](../articles/machine-learning/how-to-set-up-training-targets.md#local)| ja | &nbsp; | &nbsp; |
|[Reken instantie Azure Machine Learning (preview-versie)](../articles/machine-learning/concept-compute-instance.md) | | ja |  |
|[Azure Machine Learning Compute-Cluster](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Ja & <br/>afstemming&nbsp;afstemmen | ja | ja |
|[Externe virtuele machine](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Ja & <br/>afstemming afstemmen | ja | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| ja | ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | ja | &nbsp; |
