---
title: Werk ruimten maken en beheren
titleSuffix: Azure Machine Learning service
description: Informatie over het maken, weergeven en verwijderen van Azure Machine Learning-service toegang tot werkruimten in Azure portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: f103bde99eb5b21d06988f11ac6f17fcf50ceb31
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278280"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Maken en beheren van Azure Machine Learning-service werkruimten

In dit artikel, zult u maken, weergeven en verwijderen [ **Azure Machine Learning-service werkruimten** ](concept-workspace.md) in Azure portal voor [Azure Machine Learning-service](overview-what-is-azure-ml.md).  De portal is de eenvoudigste manier om aan de slag te gaan met werk ruimten, maar als uw behoeften wijzigen of vereisten voor automatisering verhogen, kunt u ook werk ruimten maken en verwijderen [met behulp van de CLI](reference-azure-machine-learning-cli.md), [met Python-code](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) of [via de VS code-extensie](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

## <a name="create-a-workspace"></a>Een werkruimte maken

Een werkruimte te maken, moet u een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer nog vandaag de [gratis of betaalde versie van de Azure Machine Learning Service](https://aka.ms/AMLFree).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="download-a-configuration-file"></a>Een configuratie bestand downloaden

1. Als u een VM van een [notebook](tutorial-1st-experiment-sdk-setup.md#azure)wilt maken, slaat u deze stap over.

1. Als u van plan bent code te gebruiken in uw lokale omgeving die verwijst naar deze werk ruimte, selecteert u **config. json downloaden** in het gedeelte **overzicht** van de werk ruimte.  

   ![Config. json downloaden](./media/how-to-manage-workspace/configure.png)
   
   Plaats het bestand in de mapstructuur met uw python-scripts of Jupyter-notebooks. Deze kan zich in dezelfde map bevindt, in een submap met de naam *. azureml*of in een bovenliggende map. Wanneer u een VM van een notebook maakt, wordt dit bestand voor u toegevoegd aan de juiste map op de virtuele machine.


## <a name="view"></a>Een werk ruimte weer geven

1. Selecteer in de linkerbovenhoek van de portal, **alle services**.

1. Typ **machine learning service**in het veld **alle services** filter.  

1. Selecteer **machine learning service-werk ruimten**.

   ![Zoeken naar Azure Machine Learning service-werk ruimte](media/how-to-manage-workspace/all-services.png)

1. Bekijk de lijst met werkruimten die zijn gevonden. U kunt filteren op basis van abonnement, resourcegroepen en locaties.  

1. Selecteer een werk ruimte om de eigenschappen ervan weer te geven.
   ![Eigenschappen van werk ruimte](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Een werkruimte verwijderen

Gebruik de knop verwijderen aan de bovenkant van de werkruimte die u wilt verwijderen.

  ![Knop verwijderen](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

Voer de volledige zelfstudie voor informatie over het gebruik van een werkruimte te bouwen, te trainen en implementeer modellen met Azure Machine Learning-service.

> [!div class="nextstepaction"]
> [Zelfstudie: Modellen trainen](tutorial-train-models-with-aml.md)
