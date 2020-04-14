---
title: Azure Machine Learning-werkruimten maken in de portal
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken, weergeven en verwijderen van Azure Machine Learning-werkruimten in de Azure-portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 4b4b64bcca57e1dc98cdba10626597532ae1461c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269722"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Azure Machine Learning-werkruimten maken en beheren in de Azure-portal
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel maakt, bekijkt en verwijdert u [**Azure Machine Learning-werkruimten**](concept-workspace.md) in de Azure-portal voor [Azure Machine Learning.](overview-what-is-azure-ml.md)  De portal is de eenvoudigste manier om aan de slag te gaan met werkruimten, maar als uw behoeften veranderen of de vereisten voor automatisering te verhogen u ook maken en verwijderen van werkruimten [met behulp van de CLI,](reference-azure-machine-learning-cli.md)met [Python-code](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) of [via de VS Code extensie](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Een werkruimte maken

Als u een werkruimte wilt maken, hebt u een Azure-abonnement nodig. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met behulp van de referenties voor uw Azure-abonnement. 

1. Selecteer in de linkerbovenhoek van de Azure-portal de optie **+ Een resource maken**.

      ![Een nieuwe resource maken](./media/how-to-manage-workspace/create-workspace.gif)

1. Gebruik de zoekbalk om **Machine Learning**te vinden.

1. Selecteer **Machine learning**.

1. Selecteer Maken om te beginnen **in** het deelvenster **Machine learning.**

1. Geef de volgende informatie op om uw nieuwe werkruimte te configureren:

   Veld|Beschrijving 
   ---|---
   Naam van de werkruimte |Voer een unieke naam in die uw werkruimte identificeert. In dit voorbeeld gebruiken we **docs-ws**. Namen moeten uniek zijn in de resourcegroep. Gebruik een naam die gemakkelijk te herinneren is en om te onderscheiden van werkruimten die door anderen zijn gemaakt. De naam van de werkruimte is hoofdletters ongevoelig.
   Abonnement |Selecteer het Azure-abonnement dat u wilt gebruiken.
   Resourcegroep | Gebruik een bestaande resourcegroep in uw abonnement of voer een naam in om een nieuwe resourcegroep te maken. Een resourcegroep bevat gerelateerde resources voor een Azure-oplossing. In dit voorbeeld gebruiken we **docs-aml**. 
   Locatie | Selecteer de locatie die het dichtst bij uw gebruikers staat en de gegevensbronnen om uw werkruimte te maken.
   Werkruimte-editie | Selecteer **Basic** of **Enterprise**.  Deze werkruimteeditie bepaalt de functies waartoe u toegang en prijzen hebt. Meer informatie over [het aanbod van Basic- en Enterprise-edities](overview-what-is-azure-ml.md#sku). 

    ![Uw werkruimte configureren](./media/how-to-manage-workspace/select-edition.png)

1. Wanneer u klaar bent met het configureren van de werkruimte, selecteert u **Controleren + Maken**.
2. Bekijk de instellingen en breng eventuele aanvullende wijzigingen of correcties aan. Als u tevreden bent met de instellingen, selecteert u **Maken**.

   > [!Warning] 
   > Het kan enkele minuten duren voordat uw werkruimte in de cloud is gemaakt.

   Wanneer het proces is voltooid, wordt een bericht met een implementatiesucces weergegeven. 
 
 1. Als u de nieuwe werkruimte wilt weergeven, selecteert u **Ga naar resource**.

### <a name="download-a-configuration-file"></a>Een configuratiebestand downloaden

1. Als u een [rekeninstantie](tutorial-1st-experiment-sdk-setup.md#azure)maakt, slaat u deze stap over.

1. Als u van plan bent code te gebruiken in uw lokale omgeving die verwijst naar deze werkruimte, selecteert u **Config.json downloaden** in de sectie **Overzicht** van de werkruimte.  

   ![Download config.json](./media/how-to-manage-workspace/configure.png)
   
   Plaats het bestand in de directorystructuur met uw Python-scripts of Jupyter-notitieblokken. Het kan zich in dezelfde map bevinden, een submap met de naam *.azureml,* of in een bovenliggende map. Wanneer u een rekeninstantie maakt, wordt dit bestand voor u toegevoegd aan de juiste map op de vm.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Upgraden naar Enterprise-editie

U uw werkruimte upgraden van Basic edition naar Enterprise-editie om te profiteren van de verbeterde functies, zoals low-code-ervaringen en verbeterde beveiligingsfuncties.

1. Meld u aan bij [Azure Machine Learning studio](https://ml.azure.com).

1. Selecteer de werkruimte die u wilt upgraden.

1. Selecteer **Meer informatie** rechtsboven op de pagina.

   [![Een werkruimte](./media/how-to-manage-workspace/upgrade.png) bijwerken](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. Selecteer **Upgrade** in het venster dat wordt weergegeven.


> [!IMPORTANT]
> U een werkruimte voor een Enterprise-editie niet downgraden naar een werkruimte voor een Basiseditie. 

## <a name="find-a-workspace"></a><a name="view"></a>Een werkruimte zoeken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Typ **Machine learning**in het bovenste zoekveld .  

1. Selecteer **Machine learning**.

   ![Zoeken naar Azure Machine Learning-werkruimte](./media/how-to-manage-workspace/find-workspaces.png)

1. Bekijk de lijst met gevonden werkruimten. U filteren op basis van abonnement, brongroepen en locaties.  

1. Selecteer een werkruimte om de eigenschappen weer te geven.

## <a name="delete-a-workspace"></a>Een werkruimte verwijderen

Selecteer **Verwijderen** boven aan de werkruimte die u wilt verwijderen in de [Azure-portal.](https://portal.azure.com/)

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Werkruimte verwijderen":::

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="resource-provider-errors"></a>Fouten in resourceprovider

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>De werkruimte verplaatsen

> [!WARNING]
> Het verplaatsen van uw Azure Machine Learning-werkruimte naar een ander abonnement of het verplaatsen van het eigen abonnement naar een nieuwe tenant wordt niet ondersteund. Dit kan fouten veroorzaken.

### <a name="deleting-the-azure-container-registry"></a>Het Azure-containerregister verwijderen

De Azure Machine Learning-werkruimte gebruikt Azure Container Registry (ACR) voor sommige bewerkingen. Het zal automatisch een ACR-exemplaar maken wanneer het er voor het eerst een nodig heeft.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Volgende stappen

Volg de volledige zelfstudie voor meer informatie over het gebruik van een werkruimte om modellen te bouwen, te trainen en te implementeren met Azure Machine Learning.

> [!div class="nextstepaction"]
> [Zelfstudie: Modellen trainen](tutorial-train-models-with-aml.md)
