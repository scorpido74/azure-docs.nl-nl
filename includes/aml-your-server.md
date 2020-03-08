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
ms.date: 03/05/2020
ms.openlocfilehash: 4bce52ba3320506b85949493407dded1d52415a3
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673608"
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
