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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79486098"
---
1. [Maak een Azure machine learning-werk ruimte](../articles/machine-learning/how-to-manage-workspace.md).

1. Kloon [de GitHub-opslagplaats](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Voeg een werkruimte configuratie bestand toe aan de gekloonde Directory met een van de volgende methoden:

    * Selecteer in de [Azure Portal](https://ms.portal.azure.com)de optie **down load config. json** in het gedeelte **overzicht** van uw werk ruimte. 

    ![Config. json downloaden](./media/aml-dsvm-server/download-config.png)

    * Maak een nieuwe werkruimte met behulp van code in de notebook [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) in uw gekloonde map.

1. Start de notebookserver vanuit de gekloonde map.

    ```bash
    jupyter notebook
    ```