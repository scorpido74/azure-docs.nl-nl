---
title: Resourceprovider maken
description: Beschrijft hoe u een resourceprovider maakt en de aangepaste resourcetypen implementeert.
author: MSEvanhi
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 393993a44c860525b9bd9a540ed7afff78e5b93c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649867"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Snelstart: aangepaste provider maken en aangepaste resources implementeren

In deze quickstart maakt u uw eigen resourceprovider en implementeert u aangepaste resourcetypen voor die resourceprovider. Zie overzicht van Azure [Custom Providers Preview](overview.md)voor meer informatie over aangepaste providers.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze quickstart wilt uitvoeren, moet u REST-bewerkingen aanroepen. Er zijn [verschillende manieren om REST-verzoeken te verzenden.](/rest/api/azure/) Als u nog geen tool hebt voor REST-bewerkingen, installeert u [ARMClient.](https://github.com/projectkudu/ARMClient) Het is een open-source command-line tool die het inroepen van de Azure Resource Manager API vereenvoudigt.

## <a name="deploy-custom-provider"></a>Aangepaste provider implementeren

Als u de aangepaste provider wilt instellen, implementeert u een [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) in uw Azure-abonnement.

Nadat u de sjabloon hebt geïmplementeerd, heeft uw abonnement de volgende bronnen:

* Functie-app met de bewerkingen voor de resources en acties.
* Opslagaccount voor het opslaan van gebruikers die zijn gemaakt via de aangepaste provider.
* Aangepaste provider die de aangepaste resourcetypen en -acties definieert. Het maakt gebruik van de functie-app eindpunt voor het verzenden van verzoeken.
* Aangepaste resource van de aangepaste provider.

Gebruik het als u de aangepaste provider wilt implementeren met PowerShell:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

U de oplossing ook implementeren met de volgende knop:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Aangepaste provider en resource weergeven

In de portal is de aangepaste provider een verborgen resourcetype. Als u wilt controleren of de resourceprovider is geïmplementeerd, navigeert u naar de resourcegroep. Selecteer de optie om **verborgen typen weer te geven**.

![Verborgen resourcetypen weergeven](./media/create-custom-provider/show-hidden.png)

Als u het aangepaste resourcetype wilt weergeven dat u hebt geïmplementeerd, gebruikt u de GET-bewerking op uw resourcetype.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

Gebruik met ARMClient:

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

## <a name="call-action"></a>Oproepactie

Uw aangepaste provider heeft ook een actie met de naam **ping.** De code die de aanvraag verwerkt, wordt geïmplementeerd in de functie-app. De ping-actie antwoordt met een begroeting.

Als u een ping-verzoek wilt verzenden, gebruikt u de bewerking POST op uw aangepaste provider.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

Gebruik met ARMClient:

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

## <a name="create-resource-type"></a>Resourcetype maken

Als u het aangepaste resourcetype wilt maken, u de bron in een sjabloon implementeren. Deze benadering wordt weergegeven in de sjabloon die u in deze quickstart hebt geïmplementeerd. U ook een PUT-aanvraag voor het resourcetype verzenden.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

Gebruik met ARMClient:

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

## <a name="next-steps"></a>Volgende stappen

Zie Overzicht van Azure [Custom Providers Preview](overview.md)voor een inleiding tot aangepaste providers.
