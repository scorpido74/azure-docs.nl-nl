---
title: Azure Machine Learning-Services in Azure-notitieblokken gebruiken
description: Een overzicht van de voorbeeld-notebooks voor Azure Machine Learning-Services die u met Azure-Notebooks gebruiken kunt.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: f591758fa6e51c420a090aa62d5160320fe15fe8
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973031"
---
# <a name="use-azure-machine-learning-service-in-a-notebook"></a>Azure Machine Learning-service in een notitie blok gebruiken

Azure Notebooks wordt vooraf geconfigureerd met de benodigde omgeving om te werken met [Azure machine learning-service](/azure/machine-learning/service/). U kunt eenvoudig een voorbeeldproject klonen bij uw account laptops om te verkennen tal van scenario's voor Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Het voorbeeld klonen bij uw account

1. Meld u aan bij [Azure notitieblokken](https://notebooks.azure.com/).
1. Selecteer **Mijn projecten** om naar het dash board projecten te gaan.
1. Selecteer de knop voor het **uploaden van github opslag plaats** (de pijl omhoog) om de pop-up van de **github-opslag plaats uploaden** te openen.
1. Voer in het pop-upvenster `Azure/MachineLearningNotebooks` in **github-opslag plaats**een naam in voor het project in **project naam** , zoals ' Azure machine learning-service ', geef een ID op in **project-id**, **Open openbaar** indien gewenst en selecteer vervolgens **importeren** .

    ![Voorbeeld van Azure Machine Learning-Notebook in uw account notitieblokken importeren](media/azureml-import-project.png)

1. Na een minuut of twee gaat Azure-notitieblokken automatisch u naar het nieuwe project-dashboard.

## <a name="run-a-sample-notebook"></a>Uitvoeren op een voorbeeld-notebook

1. Selecteer **00 - configuration.ipynb** naar het gedeelte over configuratie van de notebook start en volg de instructies voor het maken van een Azure Machine Learning-werkruimte.

    - Omdat Azure notitieblokken al de vereiste pakketten voor Python bevat, kunt u alleen het codefragment in stap 2 van de vereisten om te controleren of de Azure ML-SDK-versie uitvoeren.

1. Zodra de configuratie is voltooid, selecteert u **01. aan** de slag met het openen van de map met dertien verschillende voorbeeld notitieblokken, die elk een uitleg hebben.

## <a name="next-steps"></a>Volgende stappen

De documentatie van Azure Machine Learning-Services bevat tal van andere bronnen die u begeleiden bij het werken met Machine Learning-Service in notitieblokken:

- [Snelstart: Gebruik python om aan de slag te gaan met Azure Machine Learning @ no__t-0
- [Zelfstudie 1: Een afbeeldings classificatie model trainen met Azure Machine Learning service @ no__t-0
- [Zelfstudie 2: Een installatie kopie classificatie model implementeren in azure container instance (ACI) ](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Zelfstudie: Een classificatie model trainen met geautomatiseerde machine learning in Azure Machine Learning service @ no__t-0

Zie ook de documentatie voor de [Azure Machine Learning-SDK voor Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
