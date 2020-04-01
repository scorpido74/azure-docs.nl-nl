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
ms.openlocfilehash: 2504245a14eee5308e046148f8073cf34ffa8143
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79486098"
---
1. [Maak een Azure Machine Learning-werkruimte](../articles/machine-learning/how-to-manage-workspace.md).

1. Kloon [de GitHub-opslagplaats](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Voeg een configuratiebestand voor werkruimtes toe aan de gekloonde map met behulp van een van de volgende methoden:

    * Selecteer **config.json downloaden** in de [Azure-portal](https://ms.portal.azure.com)in het gedeelte **Overzicht** van uw werkruimte. 

    ![Download config.json](./media/aml-dsvm-server/download-config.png)

    * Maak een nieuwe werkruimte met behulp van code in de notebook [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) in uw gekloonde map.

1. Start de notebookserver vanuit de gekloonde map.

    ```bash
    jupyter notebook
    ```