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
ms.date: 09/26/2019
ms.openlocfilehash: 1260ee32c267a2652ad02e74c3978654bf2f9669
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529780"
---
1. Volg de instructies op [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) om de Azure machine learning SDK voor python te installeren

1. Maak een [Azure machine learning-werk ruimte](../articles/machine-learning/how-to-manage-workspace.md).

1. Schrijf een bestand met een [configuratie bestand](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Kloon [de GitHub-opslagplaats](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Start de notebookserver vanuit de gekloonde map.

    ```shell
    jupyter notebook
    ```
