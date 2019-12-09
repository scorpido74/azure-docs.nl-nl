---
title: Azure ML-werk ruimten maken in de portal
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken, weer geven en verwijderen van Azure Machine Learning-werk ruimten in de Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: c7ff51c18d4ec41c706518631071e8feb3bafe59
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912440"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Azure Machine Learning-werk ruimten in de Azure Portal maken en beheren
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel maakt, bekijkt en verwijdert u [**Azure machine learning-werk ruimten**](concept-workspace.md) in de Azure Portal voor [Azure machine learning](overview-what-is-azure-ml.md).  De portal is de eenvoudigste manier om aan de slag te gaan met werk ruimten, maar als uw behoeften wijzigen of vereisten voor automatisering verhogen, kunt u ook werk ruimten maken en verwijderen [met behulp van de CLI](reference-azure-machine-learning-cli.md), [met Python-code](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) of [via de VS code-extensie](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="create-a-workspace"></a>Een werkruimte maken

Een werkruimte te maken, moet u een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met behulp van de referenties voor uw Azure-abonnement. 

1. Selecteer in de linkerbovenhoek van Azure Portal **+ een resource maken**.

      ![Een nieuwe resource maken](../../../includes/media/aml-create-in-portal/create-workspace.gif)

1. Gebruik de zoek balk om **machine learning**te vinden.

1. Selecteer **machine learning**.

1. Selecteer in het deel venster **machine learning** de optie **maken** om te beginnen.

1. Geef de volgende informatie op om uw nieuwe werk ruimte te configureren:

   Veld|Beschrijving 
   ---|---
   Naam van de werkruimte |Voer een unieke naam in die uw werk ruimte identificeert. In dit voor beeld gebruiken we **docs-WS**. De namen moeten uniek zijn in de resource groep. Gebruik een naam die gemakkelijk kan worden ingetrokken en om onderscheid te maken tussen werk ruimten die door anderen zijn gemaakt.  
   Abonnement |Selecteer het Azure-abonnement dat u wilt gebruiken.
   Resourcegroep | Gebruik een bestaande resourcegroep in uw abonnement of voer een naam in om een nieuwe resourcegroep te maken. Een resource groep bevat gerelateerde resources voor een Azure-oplossing. In dit voor beeld gebruiken we **docs-AML**. 
   Locatie | Selecteer de locatie die het dichtst bij uw gebruikers ligt en de gegevens bronnen om uw werk ruimte te maken.
   Werkruimte editie | Selecteer **Basic** of **Enter prise**.  Deze werk ruimte-editie bepaalt de functies waartoe u toegang hebt en de prijzen. Meer informatie over de [Basic-en Enter prise Edition-aanbiedingen](overview-what-is-azure-ml.md#sku). 

    ![Uw werk ruimte configureren](media/how-to-manage-workspace/select-edition.png)

1. Nadat u klaar bent met het configureren van de werk ruimte, selecteert u **maken**. 

   > [!Warning] 
   > Het kan enkele minuten duren om uw werk ruimte in de cloud te maken.

   Wanneer het proces is voltooid, wordt een bericht over een geslaagde implementatie weer gegeven. 
 
 1. Als u de nieuwe werk ruimte wilt weer geven, selecteert u **Ga naar resource**.

### <a name="download-a-configuration-file"></a>Een configuratie bestand downloaden

1. Als u een VM van een [notebook](tutorial-1st-experiment-sdk-setup.md#azure)wilt maken, slaat u deze stap over.

1. Als u van plan bent code te gebruiken in uw lokale omgeving die verwijst naar deze werk ruimte, selecteert u **config. json downloaden** in het gedeelte **overzicht** van de werk ruimte.  

   ![Config. json downloaden](./media/how-to-manage-workspace/configure.png)
   
   Plaats het bestand in de mapstructuur met uw python-scripts of Jupyter-notebooks. Deze kan zich in dezelfde map bevindt, in een submap met de naam *. azureml*of in een bovenliggende map. Wanneer u een VM van een notebook maakt, wordt dit bestand voor u toegevoegd aan de juiste map op de virtuele machine.

## <a name="upgrade"></a>Bijwerken naar Enter prise Edition

U kunt uw werk ruimte upgraden van de Basic Edition naar Enter prise Edition om te profiteren van de uitgebreide functies, zoals ervaringen met weinig code en verbeterde beveiligings functies.

1. Meld u aan bij [Azure machine learning Studio](https://ml.azure.com).

1. Selecteer de werk ruimte die u wilt bijwerken.

1. Selecteer in de rechter bovenhoek van de pagina **meer informatie** .

   [een werk ruimte ![upgraden](media/how-to-manage-workspace/upgrade.png)](media/how-to-manage-workspace/upgrade.png#lightbox)

1. Selecteer **upgrade** in het venster dat wordt weer gegeven.


> [!IMPORTANT]
> U kunt een Enter prise Edition-werk ruimte niet naar een Basic editie-werk ruimte downgradeen. 

## <a name="view"></a>Een werk ruimte zoeken

1. Typ **machine learning**in het bovenste zoek veld.  

1. Selecteer **machine learning**.

   ![Zoeken naar Azure Machine Learning-werk ruimte](media/how-to-manage-workspace/find-workspaces.png)

1. Bekijk de lijst met werkruimten die zijn gevonden. U kunt filteren op basis van abonnement, resourcegroepen en locaties.  

1. Selecteer een werk ruimte om de eigenschappen ervan weer te geven.

## <a name="delete-a-workspace"></a>Een werkruimte verwijderen

Gebruik de knop verwijderen aan de bovenkant van de werkruimte die u wilt verwijderen.

  ![De knop Verwijderen](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

Volg de zelf studie met volledige lengte voor meer informatie over het gebruik van een werk ruimte om modellen te bouwen, te trainen en te implementeren met Azure Machine Learning.

> [!div class="nextstepaction"]
> [Zelfstudie: Train modellen](tutorial-train-models-with-aml.md)
