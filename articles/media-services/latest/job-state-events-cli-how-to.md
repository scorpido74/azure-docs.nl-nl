---
title: Azure Media Services-gebeurtenissen met gebeurtenisraster controleren met CLI | Microsoft Documenten
description: In dit artikel ziet u hoe u zich abonneren op gebeurtenisraster om Azure Media Services-gebeurtenissen te controleren.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 619d40ab56715b4444d8e5649c7fb3401b3f57ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71937289"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Media Services-gebeurtenissen maken en bewaken met gebeurtenisraster met behulp van de Azure CLI

Azure Event Grid is een gebeurtenisservice voor de cloud. Deze service maakt gebruik [van gebeurtenisabonnementen](../../event-grid/concepts.md#event-subscriptions) om gebeurtenisberichten door te sturen naar abonnees. Gebeurtenissen in Media Services bevatten alle informatie die u nodig hebt om te reageren op wijzigingen in uw gegevens. U een Gebeurtenis Media Services identificeren omdat de eigenschap eventType begint met 'Microsoft.Media'. Zie [Programmaschema's voor mediaservices-gebeurtenissen](media-services-event-schemas.md)voor meer informatie .

In dit artikel gebruikt u de Azure CLI om u te abonneren op gebeurtenissen voor uw Azure Media Services-account. Vervolgens activeert u gebeurtenissen om het resultaat te bekijken. Normaal gesproken verzendt u gebeurtenissen naar een eindpunt dat de gebeurtenisgegevens verwerkt en vervolgens in actie komt. In dit artikel stuurt u de gebeurtenissen naar een web-app die de berichten verzamelt en weergeeft.

## <a name="prerequisites"></a>Vereisten

- Een actief Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
- Installeer en gebruik de CLI lokaal. Voor dit artikel dient u gebruik te maken van Azure CLI, versie 2.0 of hoger. Voer `az --version` uit om te zien welke versie u hebt. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). 

    Momenteel werken niet alle [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref)-opdrachten in Azure Cloud Shell. U wordt aangeraden de CLI lokaal te gebruiken.

- [Een Azure Media Services-account maken](create-account-cli-how-to.md).

    Vergeet niet de waarden die u hebt gebruikt voor de namen van de resourcegroep en het Media Services-account.

## <a name="create-a-message-endpoint"></a>Het eindpunt van een bericht maken

Voordat u zich abonneert op de gebeurtenissen voor het Media Services-account, maken we het eindpunt voor het gebeurtenisbericht. Het eindpunt onderneemt normaal gesproken actie op basis van de gebeurtenisgegevens. In dit artikel implementeert u een [vooraf gebouwde web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) die de gebeurtenisberichten weergeeft. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

1. Selecteer **Implementeren in Azure** om de oplossing voor uw abonnement te implementeren. Geef in Azure Portal waarden op voor de parameters.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. De implementatie kan enkele minuten duren. Controleer of uw web-app wordt uitgevoerd nadat de implementatie is voltooid. Navigeer in een webbrowser naar: `https://<your-site-name>.azurewebsites.net`

Als u overschakelt naar de site 'Azure Event Grid Viewer', ziet u dat deze nog geen gebeurtenissen heeft.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Het Azure-abonnement instellen

Geef in de volgende opdracht de id van het Azure-abonnement op dat u wilt gebruiken voor het Media Services-account. U kunt een lijst met abonnementen bekijken waartoe u toegang hebt door naar [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) te gaan.

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Abonneer u op evenementen van Media Services

U abonneert u op een artikel om Event Grid te vertellen welke gebeurtenissen u wilt bijhouden. In het volgende voorbeeld wordt u geabonneerd op het Media Services-account dat u hebt gemaakt en wordt de URL doorgegeven aan de website die u hebt gemaakt als eindpunt voor gebeurtenismelding. 

Vervang `<event_subscription_name>` door een unieke naam voor uw evenementabonnement. Gebruik `<resource_group_name>` `<ams_account_name>`de waarden die u hebt gebruikt bij het maken van het Media Services-account en gebruikt deze. Geef `<endpoint_URL>`voor de URL van uw `api/updates` web-app de URL op en voeg toe aan de URL van de startpagina. Door het eindpunt op te geven wanneer u zich abonneert, verwerkt Gebeurtenisraster de routering van gebeurtenissen naar dat eindpunt. 

1. De resource-id oppakken

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Bijvoorbeeld:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Abonneer u op de evenementen

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Bijvoorbeeld:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > U krijgt mogelijk een waarschuwing voor een validatiehanddruk. Geef het een paar minuten en de handdruk moet valideren.

Laten we nu gebeurtenissen activeren om te zien hoe gebeurtenisraster het bericht naar uw eindpunt distribueert.

## <a name="send-an-event-to-your-endpoint"></a>Een gebeurtenis verzenden naar het eindpunt

U gebeurtenissen voor het Media Services-account activeren door een coderingstaak uit te voeren. U [deze quickstart](stream-files-dotnet-quickstart.md) volgen om een bestand te coderen en gebeurtenissen te verzenden. 

Bekijk opnieuw uw web-app en u zult zien dat er een validatiegebeurtenis voor een abonnement naartoe is verzonden. Via Event Grid wordt de validatiegebeurtenis verzonden zodat het eindpunt kan controleren of de gebeurtenisgegevens in aanmerking komen om ontvangen te worden. Het eindpunt moet `validationResponse` worden `validationCode`ingesteld op . Zie [Beveiliging en verificatie van gebeurtenisraster](../../event-grid/security-authentication.md)voor meer informatie. U de web-app-code bekijken om te zien hoe het abonnement wordt gevalideerd.

> [!TIP]
> Selecteer het oogpictogram om de gebeurtenisgegevens uit te breiden. Vernieuw de pagina niet als u alle gebeurtenissen wilt bekijken.

![Een abonnementgebeurtenis weergeven](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Volgende stappen

[Uploaden, coderen en streamen](stream-files-tutorial-with-api.md)

