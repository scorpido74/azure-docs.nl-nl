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
ms.date: 05/30/2019
ms.openlocfilehash: aa1120db5451583153928bd2cbc383a46781a267
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489567"
---
**Reken doelen kunnen opnieuw worden gebruikt voor een trainings taak naar de volgende**. Als u bijvoorbeeld een externe virtuele machine aan uw werk ruimte koppelt, kunt u deze opnieuw gebruiken voor meerdere taken.

|Trainings &nbsp;doelen| GPU-ondersteuning |[Automatische ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML-pijp lijnen](../articles/machine-learning/service/concept-ml-pipelines.md) | [Azure Machine Learning Designer](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[Lokale computer](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| wel | ja | &nbsp; | &nbsp; |
|[Reken instantie Azure Machine Learning](../articles/machine-learning/service/concept-compute-instance.md)| ja | | ja |  |
|[Azure Machine Learning Compute-Cluster](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| ja | Ja & <br/>afstemming&nbsp;afstemmen | ja | ja |
|[Externe VM](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |ja | Ja & <br/>afstemming afstemmen | ja | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | ja | ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | ja | &nbsp; |
