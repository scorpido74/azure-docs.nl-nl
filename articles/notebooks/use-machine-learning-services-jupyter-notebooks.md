---
title: Azure Machine Learning-Services in Azure-notitieblokken gebruiken
description: Een overzicht van de voorbeeld-notebooks voor Azure Machine Learning-Services die u met Azure-Notebooks gebruiken kunt.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: c99f815bd58b03dbc43ba742577259be5638fef9
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035735"
---
# <a name="use-azure-machine-learning-service-in-a-notebook"></a>Azure Machine Learning-service in een notitie blok gebruiken

Azure Notebooks wordt vooraf geconfigureerd met de benodigde omgeving om te werken met [Azure machine learning-service](/azure/machine-learning/service/). U kunt eenvoudig een voorbeeldproject klonen bij uw account laptops om te verkennen tal van scenario's voor Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Het voorbeeld klonen bij uw account

1. Meld u aan bij [Azure notitieblokken](https://notebooks.azure.com/).
1. Selecteer **Mijn projecten** om naar het dash board projecten te gaan.
1. Selecteer de knop voor het **uploaden van github opslag plaats** (de pijl omhoog) om de pop-up van de **github-opslag plaats uploaden** te openen.
1. Voer `Azure/MachineLearningNotebooks` in het pop-upvenster in de **github-opslag plaats**een naam in voor het project in **project naam** als ' Azure machine learning-service ', geef een id op in **project-id**, **Open openbaar** indien gewenst en selecteer **vervolgens Importeren**.

    ![Voorbeeld van Azure Machine Learning-Notebook in uw account notitieblokken importeren](media/azureml-import-project.png)

1. Na een minuut of twee gaat Azure-notitieblokken automatisch u naar het nieuwe project-dashboard.

## <a name="run-a-sample-notebook"></a>Uitvoeren op een voorbeeld-notebook

1. Selecteer **00 - configuration.ipynb** naar het gedeelte over configuratie van de notebook start en volg de instructies voor het maken van een Azure Machine Learning-werkruimte.

    - Omdat Azure notitieblokken al de vereiste pakketten voor Python bevat, kunt u alleen het codefragment in stap 2 van de vereisten om te controleren of de Azure ML-SDK-versie uitvoeren.

1. Zodra de configuratie is voltooid, selecteert u **01. aan** de slag met het openen van de map met dertien verschillende voorbeeld notitieblokken, die elk een uitleg hebben.

## <a name="next-steps"></a>Volgende stappen

De documentatie van Azure Machine Learning-Services bevat tal van andere bronnen die u begeleiden bij het werken met Machine Learning-Service in notitieblokken:

- [Snelstart: Python gebruiken om aan de slag te gaan met Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Zelfstudie 1: Een classificatie model voor installatie kopieën trainen met Azure Machine Learning-service](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Zelfstudie 2: Een installatie kopie classificatie model in azure container instance (ACI) implementeren](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Zelfstudie: Een classificatie model trainen met geautomatiseerde machine learning in Azure Machine Learning-service](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Zie ook de documentatie voor de [Azure Machine Learning-SDK voor Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
