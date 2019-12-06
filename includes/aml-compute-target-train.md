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
ms.openlocfilehash: 25a865296d2ebaabcf9043341878928763d61a6f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875507"
---
**Reken doelen kunnen opnieuw worden gebruikt voor een trainings taak naar de volgende**. Als u bijvoorbeeld een externe virtuele machine aan uw werk ruimte koppelt, kunt u deze opnieuw gebruiken voor meerdere taken.  Voor machine learning pijp lijnen gebruikt u de juiste [pijplijn stap](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) voor elk Compute-doel.

|Trainings &nbsp;doelen|[Automatische ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML-pijp lijnen](../articles/machine-learning/service/concept-ml-pipelines.md) | [Azure Machine Learning Designer](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|
|[Lokale computer](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| ja | &nbsp; | &nbsp; |
|[Azure Machine Learning Compute-Cluster](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| Ja & <br/>afstemming&nbsp;afstemmen | ja | ja |
|[Externe virtuele machine](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) | Ja & <br/>afstemming afstemmen | ja | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| ja | ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla) | &nbsp; | ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch) | &nbsp; | ja | &nbsp; |
