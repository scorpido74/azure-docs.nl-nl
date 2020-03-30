---
title: Wat is een werkruimte
titleSuffix: Azure Machine Learning
description: De werkruimte is de bron op het hoogste niveau voor Azure Machine Learning. Het houdt een geschiedenis bij van alle trainingsuitvoeringen, inclusief logboeken, statistieken, uitvoer en een momentopname van uw scripts. U gebruikt deze informatie om te bepalen welke trainingsrun het beste model oplevert
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.openlocfilehash: 03bc49c24e3c2d32e97f3e5e03bd39da63428a6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505572"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Wat is een Azure Machine Learning-werkruimte?

De werkruimte is de bron op het hoogste niveau voor Azure Machine Learning, die een centrale plek biedt om te werken met alle artefacten die u maakt wanneer u Azure Machine Learning gebruikt.  De werkruimte houdt een geschiedenis bij van alle trainingsuitvoeringen, inclusief logboeken, statistieken, uitvoer en een momentopname van uw scripts. U gebruikt deze informatie om te bepalen welke trainingsrun het beste model oplevert.  

Zodra u een model hebt dat u leuk vindt, registreert u het bij de werkruimte. Vervolgens gebruikt u het geregistreerde model en de scoringsscripts om te implementeren in Azure Container Instances, Azure Kubernetes Service of naar een field-programmeerbare gate array (FPGA) als een OP RUST gebaseerd HTTP-eindpunt. U het model ook implementeren op een Azure IoT Edge-apparaat als module.

Prijzen en beschikbare functies zijn afhankelijk van of [Basic- of Enterprise-editie](overview-what-is-azure-ml.md#sku) is geselecteerd voor de werkruimte. U selecteert de editie wanneer u [de werkruimte maakt.](#create-workspace)  U ook [upgraden](#upgrade) van Basic naar Enterprise-editie.

## <a name="taxonomy"></a>Taxonomie 

Een taxonomie van de werkruimte wordt geïllustreerd in het volgende diagram:

[![Taxonomie voor werkruimte](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Het diagram toont de volgende onderdelen van een werkruimte:

+ Een werkruimte kan [Azure Machine Learning compute instances](concept-compute-instance.md)bevatten, cloudresources die zijn geconfigureerd met de Python-omgeving die nodig is om Azure Machine Learning uit te voeren.

+ [Met gebruikersrollen](how-to-assign-roles.md) u uw werkruimte delen met andere gebruikers, teams of projecten.
+ [Compute-doelen](concept-azure-machine-learning-architecture.md#compute-targets) worden gebruikt om uw experimenten uit te voeren.
+ Wanneer u de werkruimte maakt, worden er ook [bijbehorende resources](#resources) voor u gemaakt.
+ [Experimenten](concept-azure-machine-learning-architecture.md#experiments) zijn trainingsruns die u gebruikt om uw modellen te bouwen.  
+ [Pijplijnen](concept-azure-machine-learning-architecture.md#ml-pipelines) zijn herbruikbare workflows voor het trainen en omscholen van uw model.
+ [Gegevenssets](concept-azure-machine-learning-architecture.md#datasets-and-datastores) helpen bij het beheer van de gegevens die u gebruikt voor modeltraining en het maken van pijplijnen.
+ Zodra u een model hebt dat u wilt implementeren, maakt u een geregistreerd model.
+ Gebruik het geregistreerde model en een scorescript om een [implementatieeindpunt te](concept-azure-machine-learning-architecture.md#endpoints)maken.

## <a name="tools-for-workspace-interaction"></a>Hulpmiddelen voor interactie met werkruimtes

U op de volgende manieren met uw werkruimte communiceren:

+ Op het web:
    + [Azure Machine Learning-studio](https://ml.azure.com) 
    + [Azure Machine Learning designer (preview)](concept-designer.md) - Alleen beschikbaar in [werkruimten voor Enterprise-edities.](overview-what-is-azure-ml.md#sku)
+ In elke Python-omgeving met de [Azure Machine Learning SDK voor Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ In elke R-omgeving met de [Azure Machine Learning SDK voor R.](https://azure.github.io/azureml-sdk-for-r/reference/index.html)
+ Op de opdrachtregel met de Azure Machine Learning [CLI-extensie](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>Machine learning met een werkruimte

Machine learning-taken lezen en/of schrijven artefacten naar uw werkruimte.

+ Voer een experiment uit om een model te trainen - schrijft de resultaten van het experiment naar de werkruimte.
+ Gebruik geautomatiseerde ML om een model te trainen - schrijft trainingsresultaten naar de werkruimte.
+ Registreer een model in de werkruimte.
+ Een model implementeren - gebruikt het geregistreerde model om een implementatie te maken.
+ Rebruikbare werkstromen maken en uitvoeren.
+ Bekijk machine learning-artefacten zoals experimenten, pijplijnen, modellen, implementaties.
+ Modellen volgen en monitoren.

## <a name="workspace-management"></a>Werkruimtebeheer

U ook de volgende taaktaken voor werkruimtebeheer uitvoeren:

| Taak voor werkruimtebeheer   | Portal              | Studio | Python SDK / R SDK       | CLI        |
|---------------------------|---------|---------|------------|------------|
| Een werkruimte maken        | **&check;**     | | **&check;** | **&check;** |
| Werkruimtetoegang beheren    | **&check;**   || |  **&check;**    |
| Upgraden naar Enterprise-editie    | **&check;** | **&check;**  | |     |
| Compute resources maken en beheren    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| Een notebook-vm maken |   | **&check;** | |     |

> [!WARNING]
> Het verplaatsen van uw Azure Machine Learning-werkruimte naar een ander abonnement of het verplaatsen van het eigen abonnement naar een nieuwe tenant wordt niet ondersteund. Dit kan fouten veroorzaken.

## <a name="create-a-workspace"></a><a name='create-workspace'></a>Een werkruimte maken

Wanneer u een werkruimte maakt, bepaalt u of u deze wilt maken met [basic- of Enterprise-editie.](overview-what-is-azure-ml.md#sku) De editie bepaalt de functies die beschikbaar zijn in de werkruimte. Enterprise Edition biedt u onder andere toegang tot [Azure Machine Learning-ontwerper](concept-designer.md) en de studioversie van het bouwen van [geautomatiseerde machine learning-experimenten.](tutorial-first-experiment-automated-ml.md)  Zie [Azure Machine Learning-prijzen](https://azure.microsoft.com/pricing/details/machine-learning/)voor meer informatie en prijsinformatie.

Er zijn meerdere manieren om een werkruimte te maken:  

* Gebruik de [Azure-portal](how-to-manage-workspace.md) voor een point-and-click-interface om u door elke stap te laten lopen.
* Gebruik de [Azure Machine Learning SDK voor Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) om een werkruimte on the fly te maken vanuit Python-scripts of Jupiter-notitieblokken
* Gebruik een [Azure Resource Manager-sjabloon](how-to-create-workspace-template.md) of de [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) wanneer u de creatie moet automatiseren of aanpassen met bedrijfsbeveiligingsstandaarden.
* Als u in Visual Studio Code werkt, gebruikt u de [VS-code-extensie](tutorial-setup-vscode-extension.md).

> [!NOTE]
> De naam van de werkruimte is hoofdletters ongevoelig.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Upgraden naar Enterprise-editie

U [uw werkruimte upgraden van Basic naar Enterprise-editie](how-to-manage-workspace.md#upgrade) met Azure-portal. U een werkruimte voor een Enterprise-editie niet downgraden naar een werkruimte voor een Basiseditie. 

## <a name="associated-resources"></a><a name="resources"></a>Gekoppelde resources

Wanneer u een nieuwe werkruimte maakt, worden automatisch verschillende Azure-resources gemaakt die door de werkruimte worden gebruikt:

+ [Azure Container Registry:](https://azure.microsoft.com/services/container-registry/)registreert dockercontainers die u gebruikt tijdens de training en wanneer u een model implementeert. Om de kosten te minimaliseren, is ACR **lui geladen** totdat implementatieafbeeldingen zijn gemaakt.
+ [Azure Storage-account](https://azure.microsoft.com/services/storage/): wordt gebruikt als het standaardgegevensarchief voor de werkruimte.  Jupyter-notitieblokken die worden gebruikt met uw Azure Machine Learning-compute-exemplaren worden hier ook opgeslagen.
+ [Azure Application Insights:](https://azure.microsoft.com/services/application-insights/)slaat bewakingsinformatie over uw modellen op.
+ [Azure Key Vault:](https://azure.microsoft.com/services/key-vault/)slaat geheimen op die worden gebruikt door rekendoelen en andere gevoelige informatie die nodig is voor de werkruimte.

> [!NOTE]
> Naast het maken van nieuwe versies, u ook bestaande Azure-services gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie het als nodig zijn om aan de slag te gaan met Azure Machine Learning:

+ [Overzicht van Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Een werkruimte maken](how-to-manage-workspace.md)
+ [Een werkruimte beheren](how-to-manage-workspace.md)
+ [Zelfstudie: Aan de slag met het maken van uw eerste ML-experiment met de Python SDK](tutorial-1st-experiment-sdk-setup.md)
+ [Zelfstudie: Aan de slag met Azure Machine Learning met de R SDK](tutorial-1st-r-experiment.md)
+ [Zelfstudie: Uw eerste classificatiemodel maken met geautomatiseerde machine learning](tutorial-first-experiment-automated-ml.md) (alleen beschikbaar in [werkruimten voor enterprise-edities)](overview-what-is-azure-ml.md#sku)
+ [Zelfstudie: Voorspel de autoprijs met de ontwerper](tutorial-designer-automobile-price-train-score.md) (alleen beschikbaar in [werkruimten voor enterprise-edities)](overview-what-is-azure-ml.md#sku)
