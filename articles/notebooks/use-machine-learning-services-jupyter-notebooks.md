---
title: Azure Machine Learning gebruiken in Azure Notebooks
description: Een overzicht van de voorbeeld notitieblokken voor Azure Machine Learning die u met Azure Notebooks kunt gebruiken.
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
ms.openlocfilehash: 6eac5d77404c85d5481ded7e58b0cd9fab0de083
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496636"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>Azure Machine Learning in een notitie blok gebruiken

Azure Notebooks is vooraf geconfigureerd met de benodigde omgeving om te werken met [Azure machine learning](/azure/machine-learning/service/). U kunt eenvoudig een voorbeeld project klonen in uw notitieblok account om diverse Machine Learning scenario's te verkennen.

## <a name="clone-the-sample-into-your-account"></a>Het voor beeld in uw account klonen

1. Meld u aan bij [Azure notebooks](https://notebooks.azure.com/).
1. Selecteer **Mijn projecten** om naar het dash board projecten te gaan.
1. Selecteer de knop voor het **uploaden van github opslag plaats** (de pijl omhoog) om de pop-up van de **github-opslag plaats uploaden** te openen.
1. Voer in het pop-upvenster `Azure/MachineLearningNotebooks` in de **github-opslag plaats**een naam in voor het project in **project naam** zoals ' Azure machine learning ', geef een ID op in **project-id**, **Open openbaar** indien gewenst en selecteer vervolgens **importeren**.

    ![Azure Machine Learning notebook-voor beeld importeren in uw notitieblok account](media/azureml-import-project.png)

1. Na een paar minuten gaat u Azure Notebooks automatisch naar het dash board van het nieuwe project.

## <a name="run-a-sample-notebook"></a>Een voor beeld van een notebook uitvoeren

1. Selecteer **00-configuratie. ipynb** om de configuratie sectie van het notitie blok te starten en volg de instructies voor het maken van een Azure machine learning-werkruimte.

    - Omdat Azure Notebooks al de benodigde Python-pakketten bevat, kunt u gewoon het code fragment uitvoeren in stap 2 van de vereisten om de versie van de Azure ML SDK te controleren.

1. Zodra de configuratie is voltooid, selecteert u **01. aan** de slag met het openen van de map met dertien verschillende voorbeeld notitieblokken, die elk een uitleg hebben.

## <a name="next-steps"></a>Volgende stappen

De Azure Machine Learning documentatie bevat diverse andere bronnen die u helpen bij het werken met Machine Learning in notitie blokken:

- [Snelstartgids: gebruik python om aan de slag te gaan met Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Zelf studie #1: een classificatie model voor een installatie kopie trainen met Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Zelf studie #2: een classificatie model voor een installatie kopie implementeren in azure container instance (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Zelf studie: een classificatie model trainen met geautomatiseerde machine learning in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Zie ook de documentatie voor de [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
