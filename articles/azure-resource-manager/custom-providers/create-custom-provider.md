---
title: Resourceprovider maken
description: Hierin wordt beschreven hoe u een resourceprovider maakt en hoe u de aangepaste resourcetypen implementeert.
author: MSEvanhi
ms.topic: tutorial
ms.date: 06/19/2020
ms.author: evanhi
ms.openlocfilehash: ce547c010d3cc814d4e6f6182c19572248228fc3
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/21/2020
ms.locfileid: "85125001"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Quickstart: Aangepaste provider maken en aangepaste resources implementeren

In deze quickstart maakt u uw eigen resourceprovider en implementeert u aangepaste resourcetypen voor die resourceprovider. Zie het [overzicht van aangepaste Azure-providers (preview)](overview.md) voor meer informatie over aangepaste providers.

## <a name="prerequisites"></a>Vereisten

U moet `REST`-bewerkingen aanroepen om de stappen in deze quickstart uit te voeren. Er zijn [verschillende manieren om REST-aanvragen te verzenden](/rest/api/azure/).

Gebruik [Bash in Azure Cloud Shell](/azure/cloud-shell/quickstart) om Azure CLI-opdrachten uit te voeren. Voor de [custom-providers](/cli/azure/ext/custom-providers/custom-providers/resource-provider)-opdrachten is een uitbreiding vereist. Zie [Extensies gebruiken met Azure CLI](/cli/azure/azure-cli-extensions-overview) voor meer informatie.

Gebruik PowerShell 7 of een recentere versie en de Azure PowerShell-modules om PowerShell-opdrachten lokaal uit te voeren. Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie. Als u nog geen hulpprogramma voor `REST`-bewerkingen hebt, moet u de [ARMClient](https://github.com/projectkudu/ARMClient) installeren. Dit is een opensource-opdrachtregelprogramma waarmee u de Azure Resource Manager-API kunt aanroepen.

## <a name="deploy-custom-provider"></a>Aangepaste provider implementeren

Implementeer een [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) voor uw Azure-abonnement als u de aangepaste provider wilt instellen.

Na de implementatie van de sjabloon heeft uw abonnement de volgende resources:

* Functie-app met de bewerkingen voor de resources en acties.
* Opslagaccount voor het opslaan van gebruikers die zijn gemaakt via de aangepaste provider.
* Aangepaste provider die de aangepaste resourcetypen en acties definieert. Er wordt gebruikgemaakt van het eindpunt voor de functie-app voor het verzenden van aanvragen.
* Aangepaste resource van de aangepaste provider.

Gebruik Azure CLI of PowerShell als u de aangepaste provider wilt implementeren:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a resource group name:" rgName &&
read -p "Enter the location (i.e. eastus):" location &&
read -p "Enter the provider's function app name:" funcName &&
templateUri="https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json" &&
az group create --name $rgName --location "$location" &&
az deployment group create --resource-group $rgName --template-uri $templateUri --parameters funcName=$funcName &&
echo "Press [ENTER] to continue ..." &&
read
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$rgName = Read-Host -Prompt "Enter a resource group name"
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$funcName = Read-Host -Prompt "Enter the provider's function app name"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json"
New-AzResourceGroup -Name $rgName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $rgName -TemplateUri $templateUri -funcName $funcName
Read-Host -Prompt "Press [ENTER] to continue ..."
```

---

U kunt ook de oplossing van Azure Portal implementeren met de volgende knop:

[![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json)

## <a name="view-custom-provider-and-resource"></a>Aangepaste provider en resource weergeven

De aangepaste provider is in de portal een verborgen resourcetype. Ga naar de resourcegroep om te controleren of de resourceprovider is geïmplementeerd. Selecteer de optie **Verborgen typen weergeven**.

![Verborgen resourcetypen weergeven](./media/create-custom-provider/show-hidden.png)

Gebruik de `GET`-bewerking voor uw resourcetype om het aangepaste resourcetype weer te geven dat u hebt geïmplementeerd.

```http
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
subID=$(az account show --query id --output tsv)
requestURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"
az rest --method get --uri $requestURI
```

U ontvangt het antwoord:

```json
{
  "value": [
    {
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole",
        "provisioningState": "Succeeded"
      },
      "resourceGroup": "<rg-name>",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

U ontvangt het antwoord:

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

---

## <a name="call-action"></a>Aanroepactie

Uw aangepaste provider heeft ook een actie met de naam `ping`. De code waarmee de aanvraag wordt verwerkt, is geïmplementeerd in de functie-app. De `ping`-actie beantwoordt met een begroeting.

Gebruik de bewerking `POST` van uw aangepaste provider als u een `ping`-aanvraag wilt verzenden.

```http
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
pingURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"
az rest --method post --uri $pingURI
```

U ontvangt het antwoord:

```json
{
  "message": "hello <function-name>.azurewebsites.net",
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  }
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

U ontvangt het antwoord:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

---

## <a name="create-a-resource-type"></a>Een resourcetype maken

Als u het aangepaste resourcetype wilt maken, kunt u de resource implementeren in een sjabloon. Deze benadering wordt weergegeven in de sjabloon die u in deze quickstart hebt geïmplementeerd. U kunt ook een `PUT`-aanvraag verzenden voor het resourcetype.

```http
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
addURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
az rest --method put --uri $addURI --body "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"
```

U ontvangt het antwoord:

```json
{
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "properties": {
    "FullName": "Test User",
    "Location": "Earth",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "<rg-name>",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

U ontvangt het antwoord:

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

---

## <a name="custom-resource-provider-commands"></a>Opdrachten voor de aangepaste resourceprovider

Gebruik de [custom-providers](/cli/azure/ext/custom-providers/custom-providers/resource-provider)-opdrachten om samen te werken met uw aangepaste resourceprovider.

### <a name="list-custom-resource-providers"></a>Aangepaste resourceproviders weergeven

Maakt een lijst met alle aangepaste resourceproviders in een abonnement. De standaardlijst bevat aangepaste resourceproviders voor het huidige abonnement, maar u kunt ook de `--subscription`-parameter opgeven. Gebruik de parameter `--resource-group` om een resourcegroep weer te geven.

```azurecli-interactive
az custom-providers resource-provider list --subscription $subID
```

```json
[
  {
    "actions": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "ping",
        "routingType": "Proxy"
      }
    ],
    "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceproviders/<provider-name>",
    "location": "eastus",
    "name": "<provider-name>",
    "provisioningState": "Succeeded",
    "resourceGroup": "<rg-name>",
    "resourceTypes": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "users",
        "routingType": "Proxy, Cache"
      }
    ],
    "tags": {},
    "type": "Microsoft.CustomProviders/resourceproviders",
    "validations": null
  }
]
```

### <a name="show-the-properties"></a>De eigenschappen weergeven

Geef de eigenschappen van een aangepaste resourceprovider weer. De uitvoerindeling lijkt op de `list`-uitvoer.

```azurecli-interactive
az custom-providers resource-provider show --resource-group $rgName --name $funcName
```

### <a name="create-a-new-resource"></a>Een nieuwe resource maken

Gebruik de opdracht `create` om een aangepaste resourceprovider te maken of bij te werken. In dit voorbeeld worden de `actions` en `resourceTypes` bijgewerkt.

```azurecli-interactive
az custom-providers resource-provider create --resource-group $rgName --name $funcName \
--action name=ping endpoint=https://myTestSite.azurewebsites.net/api/{requestPath} routing_type=Proxy \
--resource-type name=users endpoint=https://myTestSite.azurewebsites.net/api{requestPath} routing_type="Proxy, Cache"
```

```json
"actions": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api/{requestPath}",
    "name": "ping",
    "routingType": "Proxy"
  }
],

"resourceTypes": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api{requestPath}",
    "name": "users",
    "routingType": "Proxy, Cache"
  }
],
```

### <a name="update-the-providers-tags"></a>De labels van de provider bijwerken

Met de opdracht `update` worden alleen labels voor een aangepaste resourceprovider bijgewerkt. In Azure Portal geeft de app-service van de aangepaste resourceprovider het label weer.

```azurecli-interactive
az custom-providers resource-provider update --resource-group $rgName --name $funcName --tags new=tag
```

```json
"tags": {
  "new": "tag"
},
```

### <a name="delete-a-custom-resource-provider"></a>Een aangepaste resourceprovider verwijderen

Met de opdracht `delete` wordt u gevraagd om alleen de aangepaste resourceprovider te verwijderen. Het opslagaccount, de app-service en het app-serviceabonnement worden niet verwijderd. Nadat de provider is verwijderd, wordt u teruggeleid naar een opdrachtprompt.

```azurecli-interactive
az custom-providers resource-provider delete --resource-group $rgName --name $funcName
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het volgende artikel voor een inleiding tot aangepaste providers:

> [!div class="nextstepaction"]
> [Overzicht van Azure Custom-providers (preview-versie)](overview.md)
