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
ms.openlocfilehash: ff449626ce528cfe0218a95330a567303c547e5f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79486145"
---
1. Volg de instructies op [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) om de Azure machine learning SDK voor python te installeren

1. Maak een [Azure machine learning-werk ruimte](../articles/machine-learning/how-to-manage-workspace.md).

1. Schrijf een bestand met een [configuratie bestand](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Kloon [de GitHub-opslagplaats](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Start de notebookserver vanuit de gekloonde map.

    ```bash
    jupyter notebook
    ```