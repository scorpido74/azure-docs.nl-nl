---
title: Gebeurtenissen publiceren met gebeurtenisdomeinen met Azure Event Grid
description: Hier ziet u hoe u grote reeksen onderwerpen in Azure Event Grid beheert en gebeurtenissen voor deze gebeurtenissen publiceert met behulp van gebeurtenisdomeinen.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 1d07227249806b7d54523af66817a170c19354ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72786544"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Onderwerpen beheren en gebeurtenissen publiceren met gebeurtenisdomeinen

In dit artikel ziet u hoe u:

* Een gebeurtenisrasterdomein maken
* Inschrijven op onderwerpen van het evenementraster
* Lijsttoetsen
* Gebeurtenissen publiceren naar een domein

Zie [Gebeurtenisdomeinen begrijpen voor het beheren van gebeurtenisrasteronderwerpen](event-domains.md)voor meer informatie over gebeurtenisdomeinen .

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Preview-functie installeren

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Een gebeurtenisdomein maken

Als u grote reeksen onderwerpen wilt beheren, maakt u een gebeurtenisdomein.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershell"></a>[Powershell](#tab/powershell)
```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

Met succes maken retourneert de volgende waarden:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Let `endpoint` op `id` de en als ze nodig zijn om het domein te beheren en gebeurtenissen te publiceren.

## <a name="manage-access-to-topics"></a>Toegang tot onderwerpen beheren

Het beheren van toegang tot onderwerpen gebeurt via [roltoewijzing.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) Roltoewijzing maakt gebruik van op rollen gebaseerd toegangsbeheer om bewerkingen op Azure-resources te beperken tot geautoriseerde gebruikers op een bepaald bereik.

Event Grid heeft twee ingebouwde rollen, die u gebruiken om bepaalde gebruikers toegang toe te wijzen aan verschillende onderwerpen binnen een domein. Deze rollen `EventGrid EventSubscription Contributor (Preview)`zijn , die het mogelijk `EventGrid EventSubscription Reader (Preview)`maakt voor het maken en verwijderen van abonnementen, en , die alleen voor het aanbieden van gebeurtenisabonnementen.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)
De volgende Azure `alice@contoso.com` CLI-opdracht beperkt alleen in het `demotopic1`onderwerp voor het maken en verwijderen van gebeurtenisabonnementen:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershell"></a>[Powershell](#tab/powershell)
De volgende PowerShell-opdracht beperkt `alice@contoso.com` alleen on topic `demotopic1`voor het maken en verwijderen van gebeurtenisabonnementen:

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

Zie Beveiliging en verificatie van [gebeurtenisrastervoor](./security-authentication.md)meer informatie over het beheren van toegang voor gebeurtenisrasterbewerkingen.

## <a name="create-topics-and-subscriptions"></a>Onderwerpen en abonnementen maken

De gebeurtenisrasterservice maakt en beheert het bijbehorende onderwerp in een domein op basis van de aanroep om een gebeurtenisabonnement voor een domeinonderwerp te maken. Er is geen afzonderlijke stap om een onderwerp in een domein te maken. Op dezelfde manier wordt het laatste gebeurtenisabonnement voor een onderwerp verwijderd, het onderwerp ook verwijderd.

Een abonnement op een onderwerp in een domein is hetzelfde als een abonnement nemen op een andere Azure-bron. Geef voor de bronbron-id de gebeurtenisdomein-id op die is geretourneerd bij het maken van het domein eerder. Als u het onderwerp wilt opgeven `/topics/<my-topic>` waarop u zich wilt abonneren, voegt u toe aan het einde van de bronbron-id. Als u een gebeurtenisabonnement voor een domeinbereik wilt maken dat alle gebeurtenissen in het domein ontvangt, geeft u de gebeurtenisdomein-id op zonder onderwerpen op te geven.

De gebruiker tot wie u in de vorige sectie toegang hebt verleend, maakt het abonnement meestal. U dit artikel vereenvoudigen door een abonnement te wijzigen. 

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

Als u een testeindpunt nodig hebt om uw gebeurtenissen op te abonneren, u altijd een [vooraf gebouwde web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) implementeren die de binnenkomende gebeurtenissen weergeeft. U uw evenementen naar `https://<your-site-name>.azurewebsites.net/api/updates`uw testwebsite sturen op.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Machtigingen die zijn ingesteld voor een onderwerp, worden opgeslagen in Azure Active Directory en moeten expliciet worden verwijderd. Als u een gebeurtenisabonnement loslaat, wordt de toegang van gebruikers om gebeurtenisabonnementen te maken niet ingetrokken als ze schrijftoegang hebben voor een onderwerp.


## <a name="publish-events-to-an-event-grid-domain"></a>Gebeurtenissen publiceren naar een gebeurtenisrasterdomein

Het publiceren van gebeurtenissen in een domein is hetzelfde als [publiceren naar een aangepast onderwerp.](./post-to-custom-topic.md) In plaats van te publiceren naar het aangepaste onderwerp, publiceert u echter alle gebeurtenissen naar het eindpunt van het domein. In de JSON-gebeurtenisgegevens geeft u aan naar welk onderwerp u de gebeurtenissen wilt gaan. De volgende reeks gebeurtenissen zou `"id": "1111"` resulteren `demotopic1` in gebeurtenis `"id": "2222"` met onderwerp `demotopic2`terwijl gebeurtenis met zou worden verzonden naar onderwerp:

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)
Gebruik het domeineindpunt met Azure CLI om het domeineindpunt met Azure CLI te gebruiken

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Gebruik het ware om de sleutels voor een domein te krijgen:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershell"></a>[Powershell](#tab/powershell)
Gebruik het domeineindpunt met PowerShell om het domeineindpunt met PowerShell te

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Gebruik het ware om de sleutels voor een domein te krijgen:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

En gebruik vervolgens uw favoriete methode om een HTTP-BERICHT te maken om uw gebeurtenissen te publiceren naar uw Event Grid-domein.

## <a name="next-steps"></a>Volgende stappen

* Zie het [conceptuele overzicht van gebeurtenisdomeinen](event-domains.md)voor meer informatie over concepten op hoog niveau in gebeurtenisdomeinen en waarom deze nuttig zijn.
