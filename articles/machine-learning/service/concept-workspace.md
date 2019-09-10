---
title: Wat is een werk ruimte?
titleSuffix: Azure Machine Learning service
description: De werk ruimte is de resource op het hoogste niveau voor Azure Machine Learning service. Het houdt een geschiedenis bij van alle trainings runs, waaronder Logboeken, metrische gegevens, uitvoer en een moment opname van uw scripts. U gebruikt deze informatie om te bepalen welke trainings uitvoering het beste model produceert
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/06/2019
ms.openlocfilehash: ef060c083109a7096b62ebb35f2b49bb37a371b8
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860513"
---
# <a name="what-is-an-azure-machine-learning-service-workspace"></a>Wat is een Azure Machine Learning service-werk ruimte?

De werk ruimte is de resource op het hoogste niveau voor Azure Machine Learning service, waardoor u een centrale locatie kunt gebruiken met alle artefacten die u maakt wanneer u Azure Machine Learning-service gebruikt.  De werk ruimte houdt een geschiedenis bij van alle uitvoeringen van de training, met inbegrip van Logboeken, metrische gegevens, uitvoer en een moment opname van uw scripts. U gebruikt deze informatie om te bepalen welke trainings uitvoering het beste model produceert.  

Wanneer u een model hebt dat u wilt, registreert u het bij de werk ruimte. Vervolgens gebruikt u de geregistreerde model-en Score scripts om te implementeren op Azure Container Instances, Azure Kubernetes-service of een veld-Programmeer bare poort matrix (FPGA) als een HTTP-eind punt op basis van de REST. U kunt het model ook implementeren op een Azure IoT Edge-apparaat als een module.

## <a name="taxonomy"></a>Taxonomie 

Een taxonomie van de werk ruimte wordt geïllustreerd in het volgende diagram:

[![Taxonomie van werkruimte](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

In het diagram worden de volgende onderdelen van een werk ruimte weer gegeven:

+ Een werk ruimte kan VM- [vm's](tutorial-1st-experiment-sdk-setup.md)bevatten, cloud resources die zijn geconfigureerd met de python-omgeving die nodig is om Azure machine learning uit te voeren.
+ [Gebruikers rollen](how-to-assign-roles.md) bieden u de mogelijkheid om uw werk ruimte te delen met andere gebruikers, teams of projecten.
+ [Reken doelen](concept-azure-machine-learning-architecture.md#compute-targets) worden gebruikt om uw experimenten uit te voeren.
+ Wanneer u de werk ruimte maakt, worden er ook [gekoppelde resources](#resources) voor u gemaakt.
+ [Experimenten](concept-azure-machine-learning-architecture.md#experiments) zijn trainings uitvoeringen die u gebruikt om uw modellen te bouwen.  U kunt experimenten maken en uitvoeren met
    + De [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + De sectie [geautomatiseerde machine learning experimenten (preview)](how-to-create-portal-experiments.md) in de Azure portal of de landings pagina van uw werk ruimte (preview).
    + De [visuele interface (preview-versie)](ui-concept-visual-interface.md).
+ [Pijp lijnen](concept-azure-machine-learning-architecture.md#ml-pipelines) zijn herbruikbare werk stromen voor trainingen en trainingen van uw model.
+ Gegevens [sets](concept-azure-machine-learning-architecture.md#datasets-and-datastores) bieden ondersteuning bij het beheer van de data die u gebruikt voor model training en het maken van pijp lijnen.
+ Zodra u een model hebt dat u wilt implementeren, maakt u een geregistreerd model.
+ Gebruik het geregistreerde model en een score script om een [implementatie](concept-azure-machine-learning-architecture.md#deployment)te maken.

## <a name="tools-for-workspace-interaction"></a>Hulp middelen voor werkruimte interactie

U kunt op de volgende manieren met uw werk ruimte werken:

+ Op het web:
    + [Azure Portal](https://portal.azure.com)
    + De [landings pagina van uw werk ruimte (preview-versie)](https://ml.azure.com)
    + De [visuele interface (preview-versie)](ui-concept-visual-interface.md)
+ In python met Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Op de opdracht regel met behulp van de Azure Machine Learning [cli-extensie](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>Machine learning met een werk ruimte

Machine learning-taken lezen en/of schrijven artefacten naar uw werk ruimte. 

+ Voer een experiment uit om een model voor het schrijven van modellen te trainen in de werk ruimte.
+ Gebruik automatische ML om een model te trainen-schrijf de resultaten van de training naar de werk ruimte.
+ Registreer een model in de werk ruimte.
+ Een model implementeren: maakt gebruik van het geregistreerde model om een implementatie te maken.
+ Herbruikbare werk stromen maken en uitvoeren.
+ Machine learning artefacten weer geven, zoals experimenten, pijp lijnen, modellen en implementaties.
+ Modellen bijhouden en bewaken.




## <a name="workspace-management"></a>Werkruimte beheer

U kunt ook de volgende beheer taken voor werk ruimten uitvoeren:

| Werkruimte beheer taak   | Portal              | SDK        | CLI        |
|---------------------------|------------------|------------|------------|
| Een werkruimte maken        | **&check;**     | **&check;** | **&check;** |
| Reken resources maken en beheren    | **&check;**   | **&check;** |  **&check;**   |
| Toegang tot de werk ruimte beheren    | **&check;**   | |  **&check;**    |
| Een VM voor een notebook maken | **&check;**   | |     |

### <a name='create-workspace'></a>Een werk ruimte maken

Er zijn meerdere manieren om een werk ruimte te maken.

* Gebruik de [Azure Portal](how-to-manage-workspace.md) voor een punt-en-klik-interface om elke stap stapsgewijs te door lopen.
* De [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) gebruiken om een werk ruimte te maken in de vlucht vanuit Python-scripts of Jupiter-notebooks
* Gebruik een [Azure Resource Manager sjabloon](how-to-create-workspace-template.md) of de [Azure machine learning cli](reference-azure-machine-learning-cli.md) wanneer u het maken van de zakelijke beveiligings normen wilt automatiseren of aanpassen.
* Als u in Visual Studio code werkt, gebruikt u de [VS code-extensie](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

## <a name="resources"></a>Gekoppelde resources

Wanneer u een nieuwe werkruimte maakt, wordt automatisch verschillende Azure-resources die worden gebruikt door de werkruimte gemaakt:

+ [Azure container Registry](https://azure.microsoft.com/services/container-registry/): Registreert docker-containers die u tijdens de training gebruikt en wanneer u een model implementeert. Om de kosten te minimaliseren, wordt ACR **Lazy geladen** totdat implementatie installatie kopieën zijn gemaakt.
+ [Azure Storage account](https://azure.microsoft.com/services/storage/): Wordt gebruikt als de standaard gegevens opslag voor de werk ruimte.  Jupyter-notebooks die worden gebruikt met uw laptop-Vm's, worden hier ook opgeslagen.
+ [Azure-toepassing Insights](https://azure.microsoft.com/services/application-insights/): Slaat bewakings informatie over uw modellen op.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Slaat geheimen op die worden gebruikt door Compute-doelen en andere gevoelige informatie die nodig is voor de werk ruimte.

> [!NOTE]
> Naast het maken van nieuwe versies, kunt u ook bestaande Azure-Services gebruiken.

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met Azure Machine Learning-service, raadpleegt u:

+ [Overzicht van Azure Machine Learning-service](overview-what-is-azure-ml.md)
+ [Een werkruimte maken](how-to-manage-workspace.md)
+ [Een werkruimte beheren](how-to-manage-workspace.md)
+ [Zelfstudie: een model trainen](tutorial-train-models-with-aml.md)
