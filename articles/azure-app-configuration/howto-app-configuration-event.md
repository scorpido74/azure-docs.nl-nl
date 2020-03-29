---
title: Gebeurtenissen naar een webeindpunt verzenden met Azure App-configuratie
description: Informatie over het gebruik van azure app-gebeurtenisabonnementen voor het verzenden van wijzigingen in de sleutelwaarde naar een webeindpunt
services: azure-app-configuration
author: lisaguthrie
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: da64f22981cc33772783093cfe75daa3eac5cef1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672145"
---
# <a name="route-azure-app-configuration-events-to-a-web-endpoint-with-azure-cli"></a>Azure App-configuratiegebeurtenissen routeren naar een webeindpunt met Azure CLI

In dit artikel vindt u informatie over het instellen van azure app-gebeurtenisabonnementen voor configuratie om wijzigingen in de sleutelwaarde naar een webeindpunt te verzenden. Gebruikers van Azure App-configuratie kunnen zich abonneren op gebeurtenissen die worden uitgezonden wanneer de sleutelwaarden worden gewijzigd. Deze gebeurtenissen kunnen leiden tot webhaken, Azure-functies, Azure Storage Queues of een andere gebeurtenishandler die wordt ondersteund door Azure Event Grid. Normaal gesproken verzendt u gebeurtenissen naar een eindpunt dat de gebeurtenisgegevens verwerkt en vervolgens in actie komt. Ter vereenvoudiging van dit artikel stuurt u hier de gebeurtenissen echter naar een web-app die de berichten verzamelt en weergeeft.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/). U optioneel de Azure Cloud Shell gebruiken.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest de CLI lokaal te installeren en te gebruiken, vereist dit artikel dat u de nieuwste versie van Azure CLI (2.0.70 of hoger) uitvoert. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

Als u Cloud Shell niet gebruikt, moet u zich eerst aanmelden met `az login`.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Event Grid-onderwerpen zijn Azure-resources en moeten in een Azure-resourcegroep worden geplaatst. De resourcegroep is een logische verzameling waarin Azure-resources worden geïmplementeerd en beheerd.

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). 

In het volgende voorbeeld `<resource_group_name>` wordt een resourcegroep met de naam *westus-locatie.*  Vervang `<resource_group_name>` door een unieke naam voor uw resourcegroep.

```azurecli-interactive
az group create --name <resource_group_name> --location westus
```

## <a name="create-an-app-configuration-store"></a>Een app-configuratiearchief maken

Vervang `<appconfig_name>` door een unieke naam voor `<resource_group_name>` uw configuratiearchief en door de resourcegroep die u eerder hebt gemaakt. De naam moet uniek zijn omdat deze wordt gebruikt als DNS-naam.

```azurecli-interactive
az appconfig create \
  --name <appconfig_name> \
  --location westus \
  --resource-group <resource_group_name> \
  --sku free
```

## <a name="create-a-message-endpoint"></a>Het eindpunt van een bericht maken

Voordat u zich abonneert op het onderwerp, gaan we het eindpunt voor het gebeurtenisbericht maken. Het eindpunt onderneemt normaal gesproken actie op basis van de gebeurtenisgegevens. Ter vereenvoudiging van deze snelstart gaat u een [vooraf gebouwde web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) implementeren waarmee de gebeurtenisberichten worden weergegeven. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

Vervang `<your-site-name>` door een unieke naam voor de web-app. De naam van de web-app moet uniek zijn omdat deze deel uitmaakt van de DNS-vermelding.

```azurecli-interactive
$sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

De implementatie kan enkele minuten duren. Controleer of uw web-app wordt uitgevoerd nadat de implementatie is voltooid. Navigeer in een webbrowser naar: `https://<your-site-name>.azurewebsites.net`

Op de site zouden momenteel geen berichten moeten wijn weergeven.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store"></a>Abonneer u op uw App Configuration Store

U abonneert u op een onderwerp om Event Grid te laten weten welke gebeurtenissen u wilt traceren en waar deze gebeurtenissen naartoe moeten worden gestuurd. In het volgende voorbeeld wordt u geabonneerd op de app-configuratie die u hebt gemaakt en wordt de URL van uw web-app doorgegeven als eindpunt voor gebeurtenismeldingen. Vervang `<event_subscription_name>` door een naam voor het gebeurtenisabonnement. Gebruik voor `<resource_group_name>` en `<appconfig_name>` de waarden die u eerder hebt gemaakt.

Het eindpunt voor uw web-app moet het achtervoegsel `/api/updates/` bevatten.

```azurecli-interactive
appconfigId=$(az appconfig show --name <appconfig_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $appconfigId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Bekijk opnieuw uw web-app en u zult zien dat er een validatiegebeurtenis voor een abonnement naartoe is verzonden. Selecteer het oogpictogram om de gebeurtenisgegevens uit te breiden. Via Event Grid wordt de validatiegebeurtenis verzonden zodat het eindpunt kan controleren of de gebeurtenisgegevens in aanmerking komen om ontvangen te worden. De web-app bevat code waarmee het abonnement kan worden gevalideerd.

![Een abonnementgebeurtenis weergeven](./media/quickstarts/event-grid/view-subscription-event.png)

## <a name="trigger-an-app-configuration-event"></a>Een gebeurtenis App-configuratie activeren

Nu gaan we een gebeurtenis activeren om te zien hoe het bericht via Event Grid naar het eindpunt wordt gedistribueerd. Maak een sleutelwaarde `<appconfig_name>` met de van vroeger.

```azurecli-interactive
az appconfig kv set --name <appconfig_name> --key Foo --value Bar --yes
```

U hebt de gebeurtenis geactiveerd en Event Grid heeft het bericht verzonden naar het eindpunt dat u hebt geconfigureerd toen u zich abonneerde. Bekijk uw web-app om de gebeurtenis te zien die u zojuist hebt verzonden.

```json
[{
  "id": "deb8e00d-8c64-4b6e-9cab-282259c7674f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/eventDemoGroup/providers/microsoft.appconfiguration/configurationstores/{appconfig-name}",
  "subject": "https://{appconfig-name}.azconfig.io/kv/Foo",
  "data": {
    "key": "Foo",
    "etag": "a1LIDdNEIV6wCnfv3xaip7fMXD3"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T18:59:54Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="clean-up-resources"></a>Resources opschonen
Als u van plan bent door te werken met dit app-configuratie- en gebeurtenisabonnement, moet u de bronnen die in dit artikel zijn gemaakt, niet opschonen. Als u niet verder wilt werken, gebruikt u de volgende opdracht om de resources te verwijderen die u in dit artikel hebt gemaakt.

Vervang `<resource_group_name>` door de resourcegroep die u eerder hebt gemaakt.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u onderwerpen en gebeurtenisabonnementen maken, vindt u meer informatie over belangrijke gebeurtenissen en wat Event Grid u kan helpen:

- [Reageren op gebeurtenissen met de sleutelwaarde](concept-app-configuration-event.md)
- [Over Event Grid](../event-grid/overview.md)
- [Azure Event Grid-handlers](../event-grid/event-handlers.md)
