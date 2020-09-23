---
title: Beveiligingsautomatisering maken voor specifieke beveiligingswaarschuwingen met behulp van een ARM-sjabloon (Azure Resource Manager)
description: Leer hoe u Azure Security Center-automatisering maakt om een logische app te activeren die door middel van een ARM-sjabloon (Azure Resource Manager) wordt geactiveerd door specifieke Security Center-waarschuwingen
services: azure-resource-manager
author: memildin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: memildin
ms.date: 08/20/2020
ms.openlocfilehash: 6c8a3d6c291435a379a637707a42f9ad7fe8dc00
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906356"
---
# <a name="quickstart-create-an-automatic-response-to-a-specific-security-alert-using-an-azure-resource-manager-template-arm-template"></a>Quickstart: een automatisch antwoord op een specifieke beveiligingswaarschuwing maken met behulp van een ARM-sjabloon (Azure Resource Manager)

In deze quickstart wordt beschreven hoe u een ARM-sjabloon (Azure Resource Manager) gebruikt om de werkstroom te automatiseren zodat een logische app wordt geactiveerd zodra Azure Security Center specifieke beveiligingswaarschuwingen ontvangt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)


## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Zie [Werkstroomautomatisering](workflow-automation.md) voor een lijst met de rollen en machtigingen die u nodig hebt om met de functie Werkstroomautomatisering van Azure Security Center te werken.


## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-securitycenter-create-automation-for-alertnamecontains/).

:::code language="json" source="~/quickstart-templates/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json":::

### <a name="relevant-resources"></a>Relevante informatiebronnen

- [**Microsoft.Security/automations**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-securitycenter-create-automation-for-alertnamecontains/#microsoftsecurity-resource-provider): de automatisering waarmee de logische app wordt geactiveerd zodra Azure Security Center een melding met een specifieke tekenreeks ontvangt.
- [**Microsoft.Logic/workflows**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-securitycenter-create-automation-for-alertnamecontains/#microsoftlogic-resource-provider): een lege logische app die kan worden geactiveerd.

Zie deze [door de community bijgedragen sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Security) voor andere quickstart-sjablonen van Security Center.

## <a name="deploy-the-template"></a>De sjabloon implementeren

  - **PowerShell**:
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
    ```

  - **CLI**:
    ```azurecli-interactive
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
    az group deployment create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
    ```

  - **Portal**:

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

    Zie [Een implementatieknop gebruiken om sjablonen uit de GitHub-opslagplaats te implementeren](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-to-azure-button) voor meer informatie over deze implementatieoptie.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Gebruik Azure Portal om te controleren of de werkstroomautomatisering is geïmplementeerd. 

1. Ga naar [Azure Portal](https://portal.azure.com) en open **Security Center**.
1. Selecteer het filterpictogram op de bovenste menubalk en selecteer het specifieke abonnement waarin u de nieuwe werkstroomautomatisering hebt geïmplementeerd.
1. Open **werkstroomautomatisering** in de zijbalk van Security Center en controleer of uw nieuwe automatisering erbij staat.
    :::image type="content" source="./media/quickstart-automation-alert/validating-template-run.png" alt-text="Lijst met geconfigureerde automatiseringen" lightbox="./media/quickstart-automation-alert/validating-template-run.png":::
    >[!TIP]
    > Gebruik de optie **filteren op naam** als uw abonnement veel werkstroomautomatiseringen bevat. 

## <a name="clean-up-resources"></a>Resources opschonen

Als u de werkstroomautomatisering niet langer nodig hebt, verwijdert u deze via Azure Portal.

1. Ga naar [Azure Portal](https://portal.azure.com) en open **Security Center**.
1. Selecteer het filterpictogram op de bovenste menubalk en selecteer het specifieke abonnement waarin u de nieuwe werkstroomautomatisering hebt geïmplementeerd.
1. Open **werkstroomautomatisering** in de zijbalk van Security Center en zoek de automatisering die u wilt verwijderen.
    :::image type="content" source="./media/quickstart-automation-alert/deleting-workflow-automation.png" alt-text="Stappen voor het verwijderen van een werkstroomautomatisering" lightbox="./media/quickstart-automation-alert/deleting-workflow-automation.png":::
1. Schakel het selectievakje in voor het item dat u wilt verwijderen.
1. Selecteer **Verwijderen** op de werkbalk.


## <a name="next-steps"></a>Volgende stappen

Zie voor een stapsgewijze zelfstudie die u door het proces van het maken van een sjabloon leidt:

> [!div class="nextstepaction"]
> [Zelfstudie: Uw eerste ARM-sjabloon maken en implementeren](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)