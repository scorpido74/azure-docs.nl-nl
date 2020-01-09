---
title: Azure Machine Learning gebruiken in Azure Notebooks preview
description: Een overzicht van de voorbeeld notitieblokken voor Azure Machine Learning die u kunt gebruiken met Azure Notebooks preview.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 3c0e0e9ccea079d7e8f4e35e9af2a0e1c1ec5051
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646949"
---
# <a name="use-azure-machine-learning-in-azure-notebooks-preview"></a>Azure Machine Learning gebruiken in Azure Notebooks preview

Azure Notebooks is vooraf geconfigureerd met de benodigde omgeving om te werken met [Azure machine learning](/azure/machine-learning/service/). U kunt eenvoudig een voorbeeldproject klonen bij uw account laptops om te verkennen tal van scenario's voor Machine Learning.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="clone-the-sample-into-your-account"></a>Het voorbeeld klonen bij uw account

1. Meld u aan bij [Azure notitieblokken](https://notebooks.azure.com/).
1. Selecteer **Mijn projecten** om naar het dash board projecten te gaan.
1. Selecteer de knop voor het **uploaden van github opslag plaats** (de pijl omhoog) om de pop-up van de **github-opslag plaats uploaden** te openen.
1. Voer in het pop-upvenster `Azure/MachineLearningNotebooks` in de **github-opslag plaats**een naam in voor het project in **project naam** zoals ' Azure machine learning ', geef een ID op in **project-id**, **Open openbaar** indien gewenst en selecteer vervolgens **importeren**.

    ![Voorbeeld van Azure Machine Learning-Notebook in uw account notitieblokken importeren](media/azureml-import-project.png)

1. Na een minuut of twee gaat Azure-notitieblokken automatisch u naar het nieuwe project-dashboard.

## <a name="run-a-sample-notebook"></a>Uitvoeren op een voorbeeld-notebook

1. Selecteer **00 - configuration.ipynb** naar het gedeelte over configuratie van de notebook start en volg de instructies voor het maken van een Azure Machine Learning-werkruimte.

    - Omdat Azure notitieblokken al de vereiste pakketten voor Python bevat, kunt u alleen het codefragment in stap 2 van de vereisten om te controleren of de Azure ML-SDK-versie uitvoeren.

1. Zodra de configuratie is voltooid, selecteert u **01. aan** de slag met het openen van de map met dertien verschillende voorbeeld notitieblokken, die elk een uitleg hebben.

## <a name="next-steps"></a>Volgende stappen

De Azure Machine Learning documentatie bevat diverse andere bronnen die u helpen bij het werken met Machine Learning in notitie blokken:

- [Snelstartgids: Gebruik Python aan de slag met Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Zelf studie #1: een classificatie model voor een installatie kopie trainen met Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Zelfstudie #2: Een classificeringsmodel installatiekopie in Azure Container exemplaar (ACI) implementeren](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Zelf studie: een classificatie model trainen met geautomatiseerde machine learning in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Zie ook de documentatie voor de [Azure Machine Learning-SDK voor Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
