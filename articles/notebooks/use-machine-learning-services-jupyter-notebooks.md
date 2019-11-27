---
title: Azure Machine Learning gebruiken in Azure Notebooks
description: Een overzicht van de voorbeeld notitieblokken voor Azure Machine Learning die u met Azure Notebooks kunt gebruiken.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: e3c4fbdf35bf7ea1f4dddbceb9d7235c67ed13a4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277463"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>Azure Machine Learning in een notitie blok gebruiken

Azure Notebooks is vooraf geconfigureerd met de benodigde omgeving om te werken met [Azure machine learning](/azure/machine-learning/service/). U kunt eenvoudig een voorbeeldproject klonen bij uw account laptops om te verkennen tal van scenario's voor Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Het voorbeeld klonen bij uw account

1. Meld u aan bij [Azure notebooks](https://notebooks.azure.com/).
1. Selecteer **Mijn projecten** om naar het dash board projecten te gaan.
1. Selecteer de knop voor het **uploaden van github opslag plaats** (de pijl omhoog) om de pop-up van de **github-opslag plaats uploaden** te openen.
1. Voer in het pop-upvenster `Azure/MachineLearningNotebooks` in de **github-opslag plaats**een naam in voor het project in **project naam** zoals ' Azure machine learning ', geef een ID op in **project-id**, **Open openbaar** indien gewenst en selecteer vervolgens **importeren**.

    ![Voorbeeld van Azure Machine Learning-Notebook in uw account notitieblokken importeren](media/azureml-import-project.png)

1. Na een minuut of twee gaat Azure-notitieblokken automatisch u naar het nieuwe project-dashboard.

## <a name="run-a-sample-notebook"></a>Uitvoeren op een voorbeeld-notebook

1. Selecteer **00-configuratie. ipynb** om de configuratie sectie van het notitie blok te starten en volg de instructies voor het maken van een Azure machine learning-werkruimte.

    - Omdat Azure notitieblokken al de vereiste pakketten voor Python bevat, kunt u alleen het codefragment in stap 2 van de vereisten om te controleren of de Azure ML-SDK-versie uitvoeren.

1. Zodra de configuratie is voltooid, selecteert u **01. aan** de slag met het openen van de map met dertien verschillende voorbeeld notitieblokken, die elk een uitleg hebben.

## <a name="next-steps"></a>Volgende stappen

De Azure Machine Learning documentatie bevat diverse andere bronnen die u helpen bij het werken met Machine Learning in notitie blokken:

- [Snelstartgids: gebruik python om aan de slag te gaan met Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Zelf studie #1: een classificatie model voor een installatie kopie trainen met Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Zelf studie #2: een classificatie model voor een installatie kopie implementeren in azure container instance (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Zelf studie: een classificatie model trainen met geautomatiseerde machine learning in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Zie ook de documentatie voor de [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
