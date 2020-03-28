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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79486145"
---
1. Gebruik de instructies van [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) om de Azure Machine Learning SDK voor Python te installeren

1. Maak een [Azure Machine Learning-werkruimte](../articles/machine-learning/how-to-manage-workspace.md).

1. Schrijf een [configuratiebestand](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Kloon [de GitHub-opslagplaats](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Start de notebookserver vanuit de gekloonde map.

    ```bash
    jupyter notebook
    ```