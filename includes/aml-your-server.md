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
ms.openlocfilehash: 85d1c1cd294bfc02a2e0e327073bb6a80366548b
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841109"
---
1. Volg de instructies op [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) om de Azure machine learning SDK voor python te installeren

1. Maak een [Azure machine learning service-werk ruimte](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Een [configuratie bestand](../articles/machine-learning/service/how-to-configure-environment.md#workspace) bestand (**aml_config/config. json**) schrijven.

1. Kloon [de GitHub-opslagplaats](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Start de notebookserver vanuit de gekloonde map.

    ```shell
    jupyter notebook
    ```
