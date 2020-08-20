---
title: Werk ruimten maken in de portal
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken, weer geven en verwijderen van Azure Machine Learning-werk ruimten in de Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: e1f97fddb07e56946e37c04d9b9685412782c560
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88659752"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Azure Machine Learning-werkruimten maken en beheren in de Azure-portal
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel maakt, bekijkt en verwijdert u [**Azure machine learning-werk ruimten**](concept-workspace.md) in de Azure Portal voor [Azure machine learning](overview-what-is-azure-ml.md).  De portal is de eenvoudigste manier om aan de slag te gaan met werk ruimten, maar als uw behoeften wijzigen of vereisten voor automatisering verhogen, kunt u ook werk ruimten maken en verwijderen [met behulp van de CLI](reference-azure-machine-learning-cli.md), [met Python-code](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) of [via de VS code-extensie](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Een werkruimte maken

Als u een werk ruimte wilt maken, hebt u een Azure-abonnement nodig. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met behulp van de referenties voor uw Azure-abonnement. 

1. Selecteer in de linkerbovenhoek van Azure Portal **+ een resource maken**.

      ![Een nieuwe resource maken](./media/how-to-manage-workspace/create-workspace.gif)

1. Gebruik de zoek balk om **machine learning**te vinden.

1. Selecteer **machine learning**.

1. Selecteer in het deel venster **machine learning** de optie **maken** om te beginnen.

1. Geef de volgende informatie op om uw nieuwe werk ruimte te configureren:

   Veld|Beschrijving 
   ---|---
   Werkruimtenaam |Voer een unieke naam in die uw werk ruimte identificeert. In dit voor beeld gebruiken we **docs-WS**. De namen moeten uniek zijn in de resource groep. Gebruik een naam die gemakkelijk kan worden ingetrokken en om onderscheid te maken tussen werk ruimten die door anderen zijn gemaakt. De naam van de werk ruimte is niet hoofdletter gevoelig.
   Abonnement |Selecteer het Azure-abonnement dat u wilt gebruiken.
   Resourcegroep | Gebruik een bestaande resourcegroep in uw abonnement of voer een naam in om een nieuwe resourcegroep te maken. Een resource groep bevat gerelateerde resources voor een Azure-oplossing. In dit voor beeld gebruiken we **docs-AML**. U hebt de rol *Inzender* of *eigenaar* nodig voor het gebruik van een bestaande resource groep.  Zie [toegang tot een Azure machine learning-werk ruimte beheren](how-to-assign-roles.md)voor meer informatie over toegang.
   Locatie | Selecteer de locatie die het dichtst bij uw gebruikers ligt en de gegevens bronnen om uw werk ruimte te maken.
   Locatie | Selecteer de locatie die het dichtst bij uw gebruikers ligt en de gegevens bronnen om uw werk ruimte te maken.
   Werkruimte editie | Selecteer **Basic** of **Enter prise**.  Deze werk ruimte-editie bepaalt de functies waartoe u toegang hebt en de prijzen. Meer informatie over de [Basic-en Enter prise Edition-aanbiedingen](overview-what-is-azure-ml.md#sku). 

    ![Uw werk ruimte configureren](./media/how-to-manage-workspace/select-edition.png)

1. Wanneer u klaar bent met het configureren van de werk ruimte, selecteert u **controleren + maken**.
2. Controleer de instellingen en breng eventuele aanvullende wijzigingen of correcties aan. Wanneer u tevreden bent met de instellingen, selecteert u **maken**.

   > [!Warning] 
   > Het kan enkele minuten duren om uw werk ruimte in de cloud te maken.

   Wanneer het proces is voltooid, wordt een bericht over een geslaagde implementatie weer gegeven. 
 
 1. Als u de nieuwe werk ruimte wilt weer geven, selecteert u **Ga naar resource**.

### <a name="download-a-configuration-file"></a>Een configuratiebestand downloaden

1. Als u een [reken instantie](tutorial-1st-experiment-sdk-setup.md#azure)wilt maken, slaat u deze stap over.

1. Als u van plan bent om code te gebruiken in uw lokale omgeving die verwijst naar deze werk ruimte, selecteert u  **config.jsdownloaden in** het gedeelte **overzicht** van de werk ruimte.  

   ![Down load config.jsop](./media/how-to-manage-workspace/configure.png)
   
   Plaats het bestand in de mapstructuur met uw python-scripts of Jupyter-notebooks. Deze kan zich in dezelfde map bevindt, in een submap met de naam *. azureml*of in een bovenliggende map. Wanneer u een reken instantie maakt, wordt dit bestand voor u toegevoegd aan de juiste map op de virtuele machine.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Bijwerken naar Enter prise Edition

U kunt uw werk ruimte upgraden van de Basic Edition naar Enter prise Edition om gebruik te maken van de uitgebreide functies, zoals ervaringen met weinig code en verbeterde beveiligings functies.

1. Meld u aan bij [Azure Machine Learning Studio](https://ml.azure.com).

1. Selecteer de werk ruimte die u wilt bijwerken.

1. Selecteer in de rechter bovenhoek van de pagina **meer informatie**  .

   [![Een werk ruimte ](./media/how-to-manage-workspace/upgrade.png) bijwerken](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. Selecteer **upgrade** in het venster dat wordt weer gegeven.


> [!IMPORTANT]
> U kunt een Enter prise Edition-werk ruimte niet naar een Basic editie-werk ruimte downgradeen. 

## <a name="find-a-workspace"></a><a name="view"></a>Een werk ruimte zoeken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Typ **machine learning**in het bovenste zoek veld.  

1. Selecteer **machine learning**.

   ![Zoeken naar Azure Machine Learning-werk ruimte](./media/how-to-manage-workspace/find-workspaces.png)

1. Bekijk de lijst met werk ruimten die zijn gevonden. U kunt filteren op basis van abonnement, resource groepen en locaties.  

1. Selecteer een werk ruimte om de eigenschappen ervan weer te geven.

## <a name="delete-a-workspace"></a>Een werkruimte verwijderen

Selecteer in de [Azure Portal](https://portal.azure.com/) **verwijderen**  boven aan de werk ruimte die u wilt verwijderen.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Werk ruimte verwijderen":::

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="resource-provider-errors"></a>Fouten van de resource provider

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>De werk ruimte verplaatsen

> [!WARNING]
> Het verplaatsen van uw Azure Machine Learning-werk ruimte naar een ander abonnement of het verplaatsen van het abonnement dat eigenaar is naar een nieuwe Tenant, wordt niet ondersteund. Dit kan fouten veroorzaken.

### <a name="deleting-the-azure-container-registry"></a>De Azure Container Registry verwijderen

In de Azure Machine Learning-werk ruimte wordt Azure Container Registry (ACR) gebruikt voor bepaalde bewerkingen. Er wordt automatisch een ACR-exemplaar gemaakt wanneer dit voor het eerst nodig is.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Volgende stappen

Volg de zelf studie met volledige lengte voor meer informatie over het gebruik van een werk ruimte om modellen te bouwen, te trainen en te implementeren met Azure Machine Learning.

> [!div class="nextstepaction"]
> [Zelf studie: modellen trainen](tutorial-train-models-with-aml.md)
