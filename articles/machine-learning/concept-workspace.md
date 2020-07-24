---
title: Wat is een werk ruimte?
titleSuffix: Azure Machine Learning
description: De werk ruimte is de resource op het hoogste niveau voor Azure Machine Learning. Het houdt een geschiedenis bij van alle trainings runs, waaronder Logboeken, metrische gegevens, uitvoer en een moment opname van uw scripts. U gebruikt deze informatie om te bepalen welke trainings uitvoering het beste model produceert
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/08/2020
ms.openlocfilehash: 118c80b27d10714703d631f0e2560540eb2d39cf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012549"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Wat is een Azure Machine Learning-werk ruimte?

De werk ruimte is de resource op het hoogste niveau voor Azure Machine Learning, waardoor u een centrale locatie kunt gebruiken met alle artefacten die u maakt wanneer u Azure Machine Learning gebruikt.  De werk ruimte houdt een geschiedenis bij van alle uitvoeringen van de training, met inbegrip van Logboeken, metrische gegevens, uitvoer en een moment opname van uw scripts. U gebruikt deze informatie om te bepalen welke trainings uitvoering het beste model produceert.  

Wanneer u een model hebt dat u wilt, registreert u het bij de werk ruimte. Vervolgens gebruikt u de geregistreerde model-en Score scripts om te implementeren op Azure Container Instances, Azure Kubernetes-service of een veld-Programmeer bare poort matrix (FPGA) als een HTTP-eind punt op basis van de REST. U kunt het model ook implementeren op een Azure IoT Edge-apparaat als een module.

Prijzen en functies die beschikbaar zijn, zijn afhankelijk van het feit of [Basic of ENTER prise Edition](overview-what-is-azure-ml.md#sku) is geselecteerd voor de werk ruimte. U selecteert de editie wanneer u [de werk ruimte maakt](#create-workspace).  U kunt ook een [upgrade uitvoeren](#upgrade) van Basic naar Enter prise Edition.

## <a name="taxonomy"></a>Taxonomie 

Een taxonomie van de werk ruimte wordt geïllustreerd in het volgende diagram:

[![Werkruimte taxonomie](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

In het diagram worden de volgende onderdelen van een werk ruimte weer gegeven:

+ Een werk ruimte kan [Azure machine learning reken instanties](concept-compute-instance.md)bevatten, cloud resources die zijn geconfigureerd met de python-omgeving die nodig is om Azure machine learning uit te voeren.

+ [Gebruikers rollen](how-to-assign-roles.md) bieden u de mogelijkheid om uw werk ruimte te delen met andere gebruikers, teams of projecten.
+ [Reken doelen](concept-azure-machine-learning-architecture.md#compute-targets) worden gebruikt om uw experimenten uit te voeren.
+ Wanneer u de werk ruimte maakt, worden er ook [gekoppelde resources](#resources) voor u gemaakt.
+ [Experimenten](concept-azure-machine-learning-architecture.md#experiments) zijn trainings uitvoeringen die u gebruikt om uw modellen te bouwen.  
+ [Pijp lijnen](concept-azure-machine-learning-architecture.md#ml-pipelines) zijn herbruikbare werk stromen voor trainingen en trainingen van uw model.
+ Gegevens [sets](concept-azure-machine-learning-architecture.md#datasets-and-datastores) bieden ondersteuning bij het beheer van de data die u gebruikt voor model training en het maken van pijp lijnen.
+ Zodra u een model hebt dat u wilt implementeren, maakt u een geregistreerd model.
+ Gebruik het geregistreerde model en een score script om een [implementatie-eind punt](concept-azure-machine-learning-architecture.md#endpoints)te maken.

## <a name="tools-for-workspace-interaction"></a>Hulp middelen voor werkruimte interactie

U kunt op de volgende manieren met uw werk ruimte werken:

> [!IMPORTANT]
> De hulpprogram ma's die zijn gemarkeerd (preview) zijn momenteel beschikbaar als open bare preview.
> De preview-versie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

+ Op het web:
    + [Azure Machine Learning Studio](https://ml.azure.com) 
    + [Azure machine learning Designer (preview)](concept-designer.md) : alleen beschikbaar in [Enter prise Edition](overview-what-is-azure-ml.md#sku) -werk ruimten.
+ In een python-omgeving met de [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ In elke R-omgeving met de [Azure machine learning SDK voor R (preview)](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Op de opdracht regel met behulp van de Azure Machine Learning [cli-extensie](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)
+ [Azure Machine Learning VS code-uitbrei ding](how-to-manage-resources-vscode.md#workspaces)


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

| Werkruimte beheer taak   | Portal              | Studio | Python SDK/R SDK       | CLI        | VS-code
|---------------------------|---------|---------|------------|------------|------------|
| Een werkruimte maken        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| Toegang tot de werk ruimte beheren    | **&check;**   || |  **&check;**    ||
| Bijwerken naar Enter prise Edition    | **&check;** | **&check;**  | |     ||
| Reken resources maken en beheren    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Een VM voor een notebook maken |   | **&check;** | |     ||

> [!WARNING]
> Het verplaatsen van uw Azure Machine Learning-werk ruimte naar een ander abonnement of het verplaatsen van het abonnement dat eigenaar is naar een nieuwe Tenant, wordt niet ondersteund. Dit kan fouten veroorzaken.

## <a name="create-a-workspace"></a><a name='create-workspace'></a>Een werk ruimte maken

Wanneer u een werk ruimte maakt, bepaalt u of u deze maakt met [Basic of ENTER prise Edition](overview-what-is-azure-ml.md#sku). De editie bepaalt de functies die beschikbaar zijn in de werk ruimte. Enter prise Edition biedt onder andere functies toegang tot [Azure machine learning Designer](concept-designer.md) en de Studio-versie van het bouwen van [geautomatiseerde machine learning experimenten](tutorial-first-experiment-automated-ml.md).  Zie [Azure machine learning prijzen](https://azure.microsoft.com/pricing/details/machine-learning/)voor meer informatie en prijs informatie.

Er zijn meerdere manieren om een werk ruimte te maken:  

* Gebruik de [Azure Portal](how-to-manage-workspace.md) voor een punt-en-klik-interface om elke stap stapsgewijs te door lopen.
* De [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) gebruiken om een werk ruimte te maken in de vlucht vanuit Python-scripts of Jupiter-notebooks
* Gebruik een [Azure Resource Manager sjabloon](how-to-create-workspace-template.md) of de [Azure machine learning cli](reference-azure-machine-learning-cli.md) wanneer u het maken van de zakelijke beveiligings normen wilt automatiseren of aanpassen.
* Als u in Visual Studio code werkt, gebruikt u de [VS code-extensie](how-to-manage-resources-vscode.md#create-a-workspace).

> [!NOTE]
> De naam van de werk ruimte is niet hoofdletter gevoelig.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Bijwerken naar Enter prise Edition

U kunt [uw werk ruimte bijwerken met behulp van de Basic-naar-Enter prise-editie](how-to-manage-workspace.md#upgrade) met Azure Portal. U kunt een Enter prise Edition-werk ruimte niet naar een Basic editie-werk ruimte downgradeen. 

## <a name="associated-resources"></a><a name="resources"></a>Gekoppelde resources

Wanneer u een nieuwe werk ruimte maakt, worden er automatisch verschillende Azure-resources gemaakt die worden gebruikt door de werk ruimte:

+ [Azure container Registry](https://azure.microsoft.com/services/container-registry/): registreert docker-containers die u tijdens de training gebruikt en wanneer u een model implementeert. Om de kosten te minimaliseren, wordt ACR **Lazy geladen** totdat implementatie installatie kopieën zijn gemaakt.
+ [Azure Storage account](https://azure.microsoft.com/services/storage/): wordt gebruikt als de standaard gegevens opslag voor de werk ruimte.  Jupyter-notebooks die worden gebruikt met uw Azure Machine Learning Reken instanties worden hier ook opgeslagen.
+ [Azure-toepassing Insights](https://azure.microsoft.com/services/application-insights/): slaat bewakings informatie over uw modellen op.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): slaat geheimen op die worden gebruikt door Compute-doelen en andere gevoelige informatie die nodig is voor de werk ruimte.

> [!NOTE]
> Naast het maken van nieuwe versies, kunt u ook bestaande Azure-Services gebruiken.

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met Azure Machine Learning raadpleegt u:

+ [Overzicht van Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Een werkruimte maken](how-to-manage-workspace.md)
+ [Een werkruimte beheren](how-to-manage-workspace.md)
+ [Zelfstudie: Ga aan de slag met het maken van uw eerste ML-experiment met de Python SDK](tutorial-1st-experiment-sdk-setup.md)
+ [Zelf studie: aan de slag met Azure Machine Learning met de R SDK](tutorial-1st-r-experiment.md)
+ [Zelf studie: uw eerste classificatie model maken met geautomatiseerde machine learning](tutorial-first-experiment-automated-ml.md) (alleen beschikbaar in de [Enter prise Edition](overview-what-is-azure-ml.md#sku) -werk ruimten)
+ [Zelf studie: prijs van auto Mobile voors pellen met de ontwerp functie](tutorial-designer-automobile-price-train-score.md) (alleen beschikbaar in werk ruimten voor [Enter prise Edition](overview-what-is-azure-ml.md#sku) )
