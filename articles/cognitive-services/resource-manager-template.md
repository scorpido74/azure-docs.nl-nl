---
title: 'Snelstartgids: een Azure Cognitive Services-resource maken met ARM-sjablonen | Microsoft Docs'
description: Ga aan de slag met het gebruik van een Azure Resource Manager sjabloon voor het implementeren van een Cognitive Services-resource.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 06/18/2020
ms.author: aahi
ms.custom: subject-armqs
ms.openlocfilehash: 30e3f901d9feca07902e20c87f4fbf5b6979a11b
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85299480"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-an-arm-template"></a>Snelstartgids: een Cognitive Services-resource maken met een ARM-sjabloon

Gebruik dit artikel om een Cognitive Services resource te maken en te implementeren met behulp van een Azure Resource Manager-sjabloon (ARM). Met deze resource voor meerdere services kunt u:
* Toegang tot meerdere Azure-Cognitive Services met één sleutel en een eind punt.
* Consolideer de facturering van de services die u gebruikt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u vertrouwd bent met het gebruik van ARM-sjablonen, selecteert u de knop **implementeren in azure** . De sjabloon wordt in de Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, [maakt u er gratis een](https://azure.microsoft.com/free/cognitive-services).

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure Quick Start-sjablonen](https://azure.microsoft.com/resources/templates/101-cognitive-services-universalkey/).

:::code language="json" source="~/quickstart-templates/101-cognitive-services-universalkey/azuredeploy.json" highlight="27-41":::

Er is één Azure-resource gedefinieerd in de sjabloon:
* [Micro soft. CognitiveServices/accounts](https://docs.microsoft.com/azure/templates/microsoft.cognitiveservices/accounts): maakt een Cognitive Services-resource.

## <a name="deploy-the-template"></a>De sjabloon implementeren

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Klik op de knop **implementeren naar Azure** .

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

2. Voer de volgende waarden in.
    
    |Waarde  |Beschrijving  |
    |---------|---------|
    | **Abonnement** | Selecteer een Azure-abonnement. |
    | **Resourcegroep** | Selecteer **Nieuw maken**, voer een unieke naam in voor de resource groep en klik vervolgens op **OK**. |
    | **Regio** | Selecteer een regio.  Bijvoorbeeld VS- **Oost** |
    | **Naam van de cognitieve service** | Vervang door een unieke naam voor uw resource. Wanneer u de implementatie valideert, hebt u de naam in de volgende sectie nodig. |
    | **Locatie** | Vervang door de hierboven gebruikte regio. |
    | **SKU** | De [prijs categorie](https://azure.microsoft.com/pricing/details/cognitive-services/) voor uw resource. |
    
    :::image type="content" source="media/arm-template/universal-key-portal-template.png" alt-text="Scherm voor het maken van resources.":::

3. Selecteer **Controleren en maken** en vervolgens **Maken**. Nadat de implementatie van de resource is voltooid, wordt de knop **Ga naar resource** gemarkeerd.


# <a name="azure-cli"></a>[Azure-CLI](#tab/CLI)

> [!NOTE]
> `az deployment group`Create vereist Azure CLI-versie 2,6 of hoger. Om het versie type weer te geven `az --version` . Raadpleeg de [documentatie](https://docs.microsoft.com/cli/azure/deployment/group)voor meer informatie.

Voer het volgende script uit met behulp van de Azure-opdracht regel interface (CLI) [op uw lokale computer](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)of in een browser met de knop **Probeer het opnieuw** . Voer een naam en locatie (bijvoorbeeld `centralus` ) in voor een nieuwe resource groep en de arm-sjabloon wordt gebruikt voor het implementeren van een Cognitive Services bron. Onthoud de naam die u gebruikt. U kunt deze later gebruiken om de implementatie te valideren.


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


## <a name="validate-the-deployment"></a>De implementatie valideren

# <a name="portal"></a>[Portal](#tab/portal)

Wanneer de implementatie is voltooid, kunt u op de knop **naar resource gaan** klikken om uw nieuwe resource te bekijken. U kunt ook de resource groep vinden door:

1. **Resource groepen** selecteren in het navigatie menu links.
2. De naam van de resource groep selecteren.

# <a name="azure-cli"></a>[Azure-CLI](#tab/CLI)

Voer het volgende script uit met behulp van de Azure CLI en voer de naam in van de resource groep die u eerder hebt gemaakt.

```azurecli-interactive
echo "Enter the resource group where the Cognitive Services resource exists:" &&
read resourceGroupName &&
az cognitiveservices account list -g $resourceGroupName
```

---


## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle resources in de groep verwijderd.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Vouw het menu aan de linkerkant in Azure Portal uit om het menu met services te openen en kies **Resourcegroepen** om de lijst met resourcegroepen weer te geven.
2. Zoek de resource groep met de resource die u wilt verwijderen
3. Klik met de rechter muisknop op de vermelding van de resource groep. Selecteer **Resourcegroep verwijderen** en bevestig dit.

# <a name="azure-cli"></a>[Azure-CLI](#tab/CLI)

Voer het volgende script uit met behulp van de Azure CLI en voer de naam in van de resource groep die u eerder hebt gemaakt. 

```azurecli-interactive
echo "Enter the resource group name, for deletion:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Volgende stappen

* [Aanvragen verifiëren voor Azure Cognitive Services](authentication.md)
* [Wat is Azure Cognitive Services?](Welcome.md)
* [Ondersteuning voor natuurlijke taal](language-support.md)
* [Ondersteuning voor docker-container](cognitive-services-container-support.md)
