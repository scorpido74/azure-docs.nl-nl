---
title: Snelstart - Gebeurtenissen verzenden naar gebeurtenisraster
description: In deze quickstart schakelt u gebeurtenisrastergebeurtenissen in voor uw containerregister en verzendt u vervolgens containerafbeeldingspush- en verwijdergebeurtenissen naar een voorbeeldtoepassing.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18
ms.openlocfilehash: dbeba56820a520e3435eeb0c5c8dbc5aae981241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403238"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Snelstart: gebeurtenissen verzenden vanuit het register van privécontainers naar gebeurtenisraster

Azure Event Grid is een volledig beheerde gebeurtenisrouteringsservice die een uniform gebeurtenisverbruik biedt met behulp van een publicatie-abonnementmodel. In deze quickstart gebruikt u de Azure CLI om een containerregister te maken, u te abonneren op registergebeurtenissen en vervolgens een voorbeeldwebtoepassing te implementeren om de gebeurtenissen te ontvangen. Ten slotte activeert `push` `delete` u containerafbeelding en -gebeurtenissen en bekijkt u het gebeurtenislaadvermogen in de voorbeeldtoepassing.

Nadat u de stappen in dit artikel hebt voltooid, worden gebeurtenissen die vanuit uw containerregister naar gebeurtenisraster worden verzonden, weergegeven in de voorbeeldweb-app:

![Webbrowser die de voorbeeldwebtoepassing met drie ontvangen gebeurtenissen rendert][sample-app-01]

Als u geen Azure-abonnement hebt, maakt u een [gratis account][azure-account] voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

De Azure CLI-opdrachten in dit artikel zijn opgemaakt voor de **Shell Bash.** Als u een andere shell gebruikt, zoals PowerShell of Opdrachtprompt, moet u mogelijk regelvervolgtekens of variabele toewijzingsregels dienovereenkomstig aanpassen. In dit artikel worden variabelen gebruikt om de benodigde hoeveelheid opdrachtbewerking te minimaliseren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-brongroep is een logische container waarin u uw Azure-resources implementeert en beheert. Met de volgende opdracht [az-groep maken,][az-group-create] wordt een resourcegroep gemaakt met de naam *myResourceGroup* in het *eastus-gebied.* Als u een andere naam voor uw `RESOURCE_GROUP_NAME` resourcegroep wilt gebruiken, stelt u een andere waarde in.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Een containerregister maken

Implementeer vervolgens een containerregister in de resourcegroep met de volgende opdrachten. Voordat u de [opdracht az acr create uitvoert,][az-acr-create] stelt u `ACR_NAME` een naam in voor uw register. De naam moet uniek zijn binnen Azure en is beperkt tot 5-50 alfanumerieke tekens.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Zodra het register is gemaakt, retourneert de Azure CLI de uitvoer die vergelijkbaar is met de volgende:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
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

U moet de voorbeeld-app weergegeven zonder gebeurtenisberichten weergegeven:

![Webbrowser met voorbeeldweb-app zonder gebeurtenissen weergegeven][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Abonneren op registergebeurtenissen

In Event Grid abonneer je je op een *onderwerp* om te vertellen welke evenementen je wilt bijhouden en waar je ze naartoe wilt sturen. Met het volgende [az eventgrid-gebeurtenisabonnement][az-eventgrid-event-subscription-create] wordt opdracht gemaakt en wordt de URL van uw web-app opgegeven als eindpunt waarnaar gebeurtenissen moeten worden verzonden. De omgevingsvariabelen die u in eerdere secties hebt ingevuld, worden hier opnieuw gebruikt, zodat er geen bewerkingen nodig zijn.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Wanneer het abonnement is voltooid, ziet u uitvoer vergelijkbaar met het volgende:

```json
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Registergebeurtenissen activeren

Nu de voorbeeld-app actief is en u zich hebt geabonneerd op uw register met Gebeurtenisraster, bent u klaar om een aantal gebeurtenissen te genereren. In deze sectie gebruikt u ACR-taken om een containerafbeelding naar uw register te bouwen en te pushen. ACR Tasks is een functie van Azure Container Registry waarmee u containerafbeeldingen in de cloud bouwen, zonder dat de Docker Engine op uw lokale machine is geïnstalleerd.

### <a name="build-and-push-image"></a>Afbeelding maken en pushen

Voer de volgende opdracht Azure CLI uit om een containerafbeelding te bouwen op basis van de inhoud van een GitHub-opslagplaats. Standaard duwt ACR Tasks automatisch een met succes gebouwde afbeelding `ImagePushed` naar uw register, waardoor de gebeurtenis wordt gegenereerd.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

U ziet uitvoer vergelijkbaar met de volgende terwijl ACR Tasks bouwt en vervolgens duwt uw afbeelding. De volgende voorbeelduitvoer is afgekapt voor beknoptheid.

```output
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

Als u wilt controleren of de ingebouwde afbeelding zich in uw register bevindt, voert u de volgende opdracht uit om de tags in de opslagplaats 'myimage' weer te geven:

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

De "v1"-tag van de afbeelding die u hebt gemaakt, moet in de uitvoer worden weergegeven, vergelijkbaar met het volgende:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>De afbeelding verwijderen

Genereer `ImageDeleted` nu een gebeurtenis door de afbeelding te verwijderen met de opdracht [az acr repository delete:][az-acr-repository-delete]

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

U ziet uitvoer vergelijkbaar met het volgende en vraagt om bevestiging om het manifest en de bijbehorende afbeeldingen te verwijderen:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Registergebeurtenissen weergeven

U hebt nu een afbeelding naar uw register gepusht en vervolgens verwijderd. Navigeer naar de web-app Logboeken en `ImageDeleted` `ImagePushed` u ziet beide gebeurtenissen en gebeurtenissen. Mogelijk wordt er ook een gebeurtenis voor abonnementsvalidatie gegenereerd door de opdracht uit te voeren in de sectie [Abonneren op registergebeurtenissen.](#subscribe-to-registry-events)

De volgende schermafbeelding toont de voorbeeld-app `ImageDeleted` met de drie gebeurtenissen en de gebeurtenis wordt uitgebreid om de details weer te geven.

![Webbrowser met de voorbeeld-app met imagepushed- en imageverwijderde gebeurtenissen][sample-app-03]

Gefeliciteerd! Als u `ImagePushed` de `ImageDeleted` gebeurtenissen en gebeurtenissen ziet, verzendt uw register gebeurtenissen naar gebeurtenisraster en stuurt Gebeurtenisgrid deze gebeurtenissen door naar het eindpunt van uw web-app.

## <a name="clean-up-resources"></a>Resources opschonen

Zodra u klaar bent met de resources die u in deze quickstart hebt gemaakt, u ze allemaal verwijderen met de volgende opdracht Azure CLI. Wanneer u een brongroep verwijdert, worden alle bronnen die deze bevat, permanent verwijderd.

**WAARSCHUWING**: Deze bewerking is onomkeerbaar. Zorg ervoor dat u geen van de resources in de groep meer nodig hebt voordat u de opdracht uitvoert.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Gebeurtenisschema gebeurtenisraster

U de schemaverwijzing voor het gebeurtenisbericht van Azure Container Registry vinden in de documentatie gebeurtenisraster:

[Azure Event Grid-gebeurtenisschema voor containerregister](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een containerregister geïmplementeerd, een afbeelding met ACR-taken gemaakt, deze verwijderd en de gebeurtenissen van uw register uit gebeurtenisraster verbruikt met een voorbeeldtoepassing. Ga vervolgens verder met de zelfstudie ACR-taken voor meer informatie over het bouwen van containerafbeeldingen in de cloud, inclusief geautomatiseerde builds op basisafbeeldingsupdate:

> [!div class="nextstepaction"]
> [Containerafbeeldingen maken in de cloud met ACR-taken](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
