---
title: Azure SignalR Service-gebeurtenissen verzenden naar gebeurtenisraster
description: Een handleiding om u te laten zien hoe u gebeurtenisrastergebeurtenissen inschakelt voor uw SignalR-service en vervolgens clientverbinding die is verbonden/verbroken verzenden naar een voorbeeldtoepassing.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: a76c9aaabf984723e2b60a7cd42425c9b29c916a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710834"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Gebeurtenissen van Azure SignalR Service naar Event Grid verzenden

Azure Event Grid is een volledig beheerde gebeurtenisrouteringsservice die een uniform gebeurtenisverbruik biedt met behulp van een pub-submodel. In deze handleiding gebruikt u de Azure CLI om een Azure SignalR-service te maken, u te abonneren op verbindingsgebeurtenissen en vervolgens een voorbeeldwebtoepassing te implementeren om de gebeurtenissen te ontvangen. Ten slotte u verbinding maken en de verbinding verbreken en de gebeurtenispayload in de voorbeeldtoepassing bekijken.

Als u geen Azure-abonnement hebt, maakt u een [gratis account][azure-account] voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

De Azure CLI-opdrachten in dit artikel zijn opgemaakt voor de **Shell Bash.** Als u een andere shell gebruikt, zoals PowerShell of Opdrachtprompt, moet u mogelijk regelvervolgtekens of variabele toewijzingsregels dienovereenkomstig aanpassen. In dit artikel worden variabelen gebruikt om de benodigde hoeveelheid opdrachtbewerking te minimaliseren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-brongroep is een logische container waarin u uw Azure-resources implementeert en beheert. Met de volgende opdracht [az-groep maken,][az-group-create] wordt een resourcegroep gemaakt met de naam *myResourceGroup* in het *eastus-gebied.* Als u een andere naam voor uw `RESOURCE_GROUP_NAME` resourcegroep wilt gebruiken, stelt u een andere waarde in.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Een SignalR-service maken

Implementeer vervolgens een Azure Signalr-service in de brongroep met de volgende opdrachten.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

Zodra de SignalR-service is gemaakt, retourneert de Azure CLI de uitvoer die vergelijkbaar is met de volgende:

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>Een eindpunt van een gebeurtenis maken

In deze sectie gebruikt u een resourcebeheersjabloon in een GitHub-opslagplaats om een vooraf gebouwde voorbeeldwebtoepassing te implementeren in Azure App Service. Later abonneert u zich op de gebeurtenisrastergebeurtenissen van uw register en geeft u deze app op als eindpunt waarnaar de gebeurtenissen worden verzonden.

Als u de voorbeeld-app wilt implementeren, stelt u een unieke naam voor uw web-app in `SITE_NAME` en voert u de volgende opdrachten uit. De sitenaam moet uniek zijn binnen Azure omdat deze deel uitmaakt van de volledig gekwalificeerde domeinnaam (FQDN) van de web-app. In een latere sectie navigeert u naar de FQDN van de app in een webbrowser om de gebeurtenissen van uw register te bekijken.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Zodra de implementatie is geslaagd (het kan enkele minuten duren), opent u een browser en navigeert u naar uw web-app om te controleren of deze wordt uitgevoerd:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Abonneren op registergebeurtenissen

In Event Grid abonneer je je op een *onderwerp* om te vertellen welke evenementen je wilt bijhouden en waar je ze naartoe wilt sturen. Met het volgende [az-eventgrid-gebeurtenisabonnement wordt][az-eventgrid-event-subscription-create] opdracht gemaakt en wordt de URL van uw web-app opgegeven als eindpunt waarnaar gebeurtenissen moeten worden verzonden. De omgevingsvariabelen die u in eerdere secties hebt ingevuld, worden hier opnieuw gebruikt, zodat er geen bewerkingen nodig zijn.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Wanneer het abonnement is voltooid, ziet u uitvoer vergelijkbaar met het volgende:

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Registergebeurtenissen activeren

Schakel over naar `Serverless Mode` de servicemodus om een clientverbinding met de SignalR-service in te stellen en in te stellen. U [Serverless Sample](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) als referentie nemen.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Registergebeurtenissen weergeven

U hebt nu een client aangesloten op de SignalR Service. Navigeer naar de web-app Logboeken en `ClientConnectionConnected` u ziet een gebeurtenis. Als u de client beëindigt, `ClientConnectionDisconnected` ziet u ook een gebeurtenis.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
