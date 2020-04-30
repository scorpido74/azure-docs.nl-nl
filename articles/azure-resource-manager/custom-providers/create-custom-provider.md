---
title: Resource provider maken
description: Hierin wordt beschreven hoe u een resource provider maakt en hoe u de aangepaste resource typen implementeert.
author: MSEvanhi
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 393993a44c860525b9bd9a540ed7afff78e5b93c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75649867"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Snelstartgids: aangepaste provider maken en aangepaste resources implementeren

In deze Quick Start maakt u uw eigen resource provider en implementeert u aangepaste resource typen voor die resource provider. Zie [Preview-versie van Azure Custom providers](overview.md)voor meer informatie over aangepaste providers.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze Snelstartgids wilt uitvoeren, moet u REST-bewerkingen aanroepen. Er zijn [verschillende manieren om rest-aanvragen te verzenden](/rest/api/azure/). Als u nog geen hulp programma voor REST-bewerkingen hebt, installeert u [ARMClient](https://github.com/projectkudu/ARMClient). Het is een open-source opdracht regel programma waarmee u de Azure Resource Manager-API kunt aanroepen.

## <a name="deploy-custom-provider"></a>Aangepaste provider implementeren

Als u de aangepaste provider wilt instellen, implementeert u een [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) in uw Azure-abonnement.

Na de implementatie van de sjabloon heeft uw abonnement de volgende resources:

* Functie-app met de bewerkingen voor de resources en acties.
* Opslag account voor het opslaan van gebruikers die zijn gemaakt via de aangepaste provider.
* Aangepaste provider die de aangepaste resource typen en acties definieert. Er wordt gebruikgemaakt van het eind punt voor de functie-app voor het verzenden van aanvragen.
* Aangepaste resource van de aangepaste provider.

Als u de aangepaste provider wilt implementeren met Power shell, gebruikt u:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

U kunt ook de oplossing implementeren met behulp van de volgende knop:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Aangepaste provider en resource weer geven

In de portal is de aangepaste provider een verborgen resource type. Als u wilt controleren of de resource provider is geïmplementeerd, gaat u naar de resource groep. Selecteer de optie om **verborgen typen weer te geven**.

![Verborgen resource typen weer geven](./media/create-custom-provider/show-hidden.png)

Als u het aangepaste resource type wilt zien dat u hebt geïmplementeerd, gebruikt u de bewerking GET voor het bron type.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

Met ARMClient, gebruikt u:

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

## <a name="call-action"></a>Aanroep actie

Uw aangepaste provider heeft ook een actie met de naam **ping**. De code waarmee de aanvraag wordt verwerkt, wordt geïmplementeerd in de functie-app. De ping-actie beantwoordt een begroeting.

Als u een ping-aanvraag wilt verzenden, gebruikt u de POST-bewerking voor uw aangepaste provider.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

Met ARMClient, gebruikt u:

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

## <a name="create-resource-type"></a>Resource type maken

Als u het aangepaste resource type wilt maken, kunt u de resource in een sjabloon implementeren. Deze benadering wordt weer gegeven in de sjabloon die u in deze Quick Start hebt geïmplementeerd. U kunt ook een PUT-aanvraag verzenden voor het bron type.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

Met ARMClient, gebruikt u:

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

Zie [Preview-versie van Azure Custom providers](overview.md)voor een inleiding tot aangepaste providers.
