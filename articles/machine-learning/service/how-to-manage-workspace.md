---
title: Azure ML-werk ruimten maken in de portal
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken, weer geven en verwijderen van Azure Machine Learning-werk ruimten in de Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: 511c737e160c0f0753e570314c9b29346972cb04
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "71269254"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Azure Machine Learning-werk ruimten in de Azure Portal maken en beheren

In dit artikel maakt, bekijkt en verwijdert u [**Azure machine learning-werk ruimten**](concept-workspace.md) in de Azure Portal voor [Azure machine learning](overview-what-is-azure-ml.md).  De portal is de eenvoudigste manier om aan de slag te gaan met werk ruimten, maar als uw behoeften wijzigen of vereisten voor automatisering verhogen, kunt u ook werk ruimten maken en verwijderen [met behulp van de CLI](reference-azure-machine-learning-cli.md), [met Python-code](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) of [via de VS code-extensie](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="create-a-workspace"></a>Een werkruimte maken

Als u een werk ruimte wilt maken, hebt u een Azure-abonnement nodig. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="download-a-configuration-file"></a>Een configuratie bestand downloaden

1. Als u een VM van een [notebook](tutorial-1st-experiment-sdk-setup.md#azure)wilt maken, slaat u deze stap over.

1. Als u van plan bent code te gebruiken in uw lokale omgeving die verwijst naar deze werk ruimte, selecteert u **config. json downloaden** in het gedeelte **overzicht** van de werk ruimte.  

   ![Config. json downloaden](./media/how-to-manage-workspace/configure.png)
   
   Plaats het bestand in de mapstructuur met uw python-scripts of Jupyter-notebooks. Deze kan zich in dezelfde map bevindt, in een submap met de naam *. azureml*of in een bovenliggende map. Wanneer u een VM van een notebook maakt, wordt dit bestand voor u toegevoegd aan de juiste map op de virtuele machine.


## <a name="view"></a>Een werk ruimte weer geven

1. Selecteer in de linkerbovenhoek van de portal **alle services**.

1. Typ **machine learning service**in het veld **alle services** filter.  

1. Selecteer **machine learning service-werk ruimten**.

   ![Zoeken naar Azure Machine Learning-werk ruimte](media/how-to-manage-workspace/all-services.png)

1. Bekijk de lijst met werk ruimten die zijn gevonden. U kunt filteren op basis van abonnement, resource groepen en locaties.  

1. Selecteer een werk ruimte om de eigenschappen ervan weer te geven.
   ![Workspace eigenschappen ](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Een werkruimte verwijderen

Gebruik de knop verwijderen boven aan de werk ruimte die u wilt verwijderen.

  ![De knop Verwijderen](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

Volg de zelf studie met volledige lengte voor meer informatie over het gebruik van een werk ruimte om modellen te bouwen, te trainen en te implementeren met Azure Machine Learning.

> [!div class="nextstepaction"]
> [Zelf studie: modellen trainen](tutorial-train-models-with-aml.md)
