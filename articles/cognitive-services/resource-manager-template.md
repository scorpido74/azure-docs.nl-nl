---
title: 'Quickstart: Een Azure Cognitive Services-resource maken met behulp van ARM-sjablonen | Microsoft Docs'
description: Ga aan de slag en gebruik een Azure Resource Manager-sjabloon om een Cognitive Services-resource te implementeren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/27/2020
ms.author: aahi
ms.custom: subject-armqs
ms.openlocfilehash: 2aa9325fc01fff683e2604063408281e66d65804
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641922"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-an-arm-template"></a>Quickstart: Een Cognitive Services-resource maken met behulp van een ARM-sjabloon

Gebruik dit artikel om een Cognitive Services-resource te maken en te implementeren, met behulp van een ARM-sjabloon (Azure Resource Manager). Met deze resource met meerdere services, kunt u:
* Via één sleutel en eindpunt toegang krijgen tot meerdere Azure Cognitive Services.
* Facturering consolideren van de services die u gebruikt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, [kunt u er gratis een maken](https://azure.microsoft.com/free/cognitive-services).

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-cognitive-services-universalkey/).

:::code language="json" source="~/quickstart-templates/101-cognitive-services-universalkey/azuredeploy.json":::

Er is één Azure-resource gedefinieerd in de sjabloon:
* [Microsoft.CognitiveServices/accounts](https://docs.microsoft.com/azure/templates/microsoft.cognitiveservices/accounts): hiermee wordt een Cognitive Services-resource gemaakt.

## <a name="deploy-the-template"></a>De sjabloon implementeren

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Klik op de knop **Implementeren in Azure**.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

2. Voer de volgende waarden in.
    
    |Waarde  |Beschrijving  |
    |---------|---------|
    | **Abonnement** | Selecteer een Azure-abonnement. |
    | **Resourcegroep** | Selecteer **Nieuwe maken**, geef een unieke naam op voor de resourcegroep en klik vervolgens op **OK**. |
    | **Regio** | Selecteer een regio.  Bijvoorbeeld **VS - Oost** |
    | **Naam van Cognitive Service** | Vervang dit door een unieke naam voor de resource. U hebt de naam nodig in de volgende sectie, wanneer u de implementatie valideert. |
    | **Locatie** | Vervang dit door de hierboven gebruikte regio. |
    | **SKU** | De [prijscategorie](https://azure.microsoft.com/pricing/details/cognitive-services/) voor de resource. |
    
    :::image type="content" source="media/arm-template/universal-key-portal-template.png" alt-text="Scherm voor het maken van resource.":::

3. Selecteer **Controleren en maken** en vervolgens **Maken**. Nadat de implementatie van de resource is voltooid, wordt de knop **Naar resource** gemarkeerd.

# <a name="azure-cli"></a>[Azure-CLI](#tab/CLI)

> [!NOTE]
> Voor het maken van `az deployment group` is Azure CLI-versie 2.6 of hoger vereist. Typ `az --version` om de versie weer te geven. Raadpleeg de [documentatie](https://docs.microsoft.com/cli/azure/deployment/group) voor meer informatie.

Voer het volgende script uit met behulp van de Azure CLI (opdrachtregelinterface), [op de lokale computer](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) of in een browser met de knop **Uitproberen**. Voer een naam en locatie (bijvoorbeeld `centralus`) in voor een nieuwe resourcegroep. De ARM-sjabloon wordt vervolgens gebruikt om in deze groep een Cognitive Services-resource te implementeren. Onthoud de naam die u gebruikt. Deze naam hebt u later nodig om de implementatie te valideren.


```azurecli-interactive
read -p "Enter a name for your new resource group:" resourceGroupName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cognitive-services-universalkey/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]


## <a name="validate-the-deployment"></a>De implementatie valideren

# <a name="portal"></a>[Portal](#tab/portal)

Wanneer de implementatie is voltooid, kunt u op de knop **Naar resource** klikken om de nieuwe resource te bekijken. U kunt de resourcegroep ook vinden door:

1. In het linkernavigatiemenu **Resourcegroepen**  te selecteren.
2. De naam van de resourcegroep te selecteren.

# <a name="azure-cli"></a>[Azure-CLI](#tab/CLI)

Voer met behulp van de Azure CLI het volgende script uit, en voer de naam van de resourcegroep in die u eerder hebt gemaakt.

```azurecli-interactive
echo "Enter the resource group where the Cognitive Services resource exists:" &&
read resourceGroupName &&
az cognitiveservices account list -g $resourceGroupName
```

---


## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook eventuele andere bijbehorende resources in de groep verwijderd.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Vouw het menu aan de linkerkant in Azure Portal uit om het menu met services te openen en kies **Resourcegroepen** om de lijst met resourcegroepen weer te geven.
2. Zoek de resourcegroep die de resource bevat die u wilt verwijderen
3. Klik met de rechtermuisknop op de vermelding van de resourcegroep. Selecteer **Resourcegroep verwijderen** en bevestig dit.

# <a name="azure-cli"></a>[Azure-CLI](#tab/CLI)

Voer met behulp van de Azure CLI het volgende script uit, en voer de naam van de resourcegroep in die u eerder hebt gemaakt. 

```azurecli-interactive
echo "Enter the resource group name, for deletion:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Volgende stappen

* [Aanvragen verifiëren bij Azure Cognitive Services](authentication.md)
* [Wat is Azure Cognitive Services?](Welcome.md)
* [Ondersteuning voor natuurlijke taal](language-support.md)
* [Ondersteuning voor Docker-container](cognitive-services-container-support.md)
