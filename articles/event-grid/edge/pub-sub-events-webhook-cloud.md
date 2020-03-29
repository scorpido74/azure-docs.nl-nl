---
title: Publiceren, abonneren op gebeurtenissen in de cloud - Azure Event Grid IoT Edge | Microsoft Documenten
description: Publiceren, abonneren op gebeurtenissen in de cloud met Webhook met Event Grid op IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c82f1edfc3acd73c1d38425f963aaaf2976a1cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844583"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Zelfstudie: Publiceren, abonneren op gebeurtenissen in de cloud

In dit artikel worden alle stappen doorlopen die nodig zijn om evenementen te publiceren en u te abonneren met behulp van Gebeurtenisraster op IoT Edge. Deze zelfstudie gebruikt en Azure-functie als gebeurtenishandler. Zie [gebeurtenishandlers](event-handlers.md)voor extra doeltypen .

Zie [Gebeurtenisrasterconcepten](concepts.md) om te begrijpen wat een gebeurtenisrasteronderwerp en -abonnement zijn voordat u verdergaat.

## <a name="prerequisites"></a>Vereisten 
Om deze tutorial te voltooien, moet je:

* **Azure-abonnement** - Maak een [gratis account](https://azure.microsoft.com/free) als u er nog geen hebt. 
* **Azure IoT Hub en IoT Edge-apparaat** - Volg de stappen in de snelle start voor [Linux-](../../iot-edge/quickstart-linux.md) of [Windows-apparaten](../../iot-edge/quickstart.md) als u er nog geen hebt.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Een Azure-functie maken in de Azure-portal

Volg de stappen die in de [zelfstudie](../../azure-functions/functions-create-first-azure-function.md) worden beschreven om een Azure-functie te maken. 

Vervang het codefragment door de volgende code:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

Selecteer in de nieuwe functie **DE URL** van de functie ophalen rechtsboven, selecteer standaard (**functietoets)** en selecteer **Vervolgens Kopiëren**. U gebruikt de URL-waarde van de functie later in de zelfstudie.

> [!NOTE]
> Raadpleeg de documentatie [azure-functies](../../azure-functions/functions-overview.md) voor meer voorbeelden en zelfstudies over het reageren op gebeurtenissen en het gebruik van EventGrid-gebeurtenistriggers.

## <a name="create-a-topic"></a>Een onderwerp maken

Als uitgever van een gebeurtenis moet u een gebeurtenisrasteronderwerp maken. Onderwerp verwijst naar een eindpunt waar uitgevers gebeurtenissen naartoe kunnen sturen.

1. Maak topic2.json met de volgende inhoud. Zie onze [API-documentatie](api.md) voor meer informatie over de payload.

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Voer de volgende opdracht uit om het onderwerp te maken. HTTP-statuscode van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Voer de volgende opdracht uit om te controleren of het onderwerp is gemaakt. HTTP-statuscode van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   Voorbeelduitvoer:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Een gebeurtenisabonnement maken

Abonnees kunnen zich inschrijven voor evenementen die zijn gepubliceerd in een onderwerp. Om een evenement te ontvangen, moeten de abonnees een abonnement op het eventraster maken voor een interessant onderwerp.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Maak subscription2.json met de volgende content. Raadpleeg onze [API-documentatie](api.md) voor meer informatie over de payload.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > Het **endpointType** geeft aan dat de abonnee een Webhook is.  De **endpointUrl** geeft de URL op waarop de abonnee naar gebeurtenissen luistert. Deze URL komt overeen met het voorbeeld van de Azure-functie dat u eerder hebt ingesteld.
2. Voer de volgende opdracht uit om het abonnement te maken. HTTP-statuscode van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Voer de volgende opdracht uit om te controleren of het abonnement is gemaakt. HTTP-statuscode van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    Voorbeelduitvoer:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Een gebeurtenis publiceren

1. Maak event2.json met de volgende inhoud. Raadpleeg onze [API-documentatie](api.md) voor meer informatie over de payload.

    ```json
        [
          {
            "id": "eventId-func-1",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```
1. De volgende opdracht uitvoeren om gebeurtenis te publiceren

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>De levering van gebeurtenissen verifiëren

U de gebeurtenis bekijken die wordt geleverd in de Azure-portal onder de optie **Monitor** van uw functie.

## <a name="cleanup-resources"></a>Resources opruimen

* Voer de volgende opdracht uit om het onderwerp en alle abonnementen te verwijderen

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Verwijder de Azure-functie die is gemaakt in de Azure-portal.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een onderwerp, abonnement en gepubliceerde gebeurtenissen voor een gebeurtenisraster gemaakt. Nu u de basisstappen kent, raadpleegt u de volgende artikelen:

* Zie [Gids voor probleemoplossing](troubleshoot.md)voor het oplossen van problemen met het gebruik van Azure Event Grid op IoT Edge.
* Abonnement maken/bijwerken met [filters](advanced-filtering.md).
* Persistentie van Event Grid-module instellen op [linux](persist-state-linux.md) of [Windows](persist-state-windows.md)
* [Documentatie volgen](configure-client-auth.md) om clientverificatie te configureren
* Gebeurtenissen doorsturen naar Azure Event Grid in de cloud door deze [zelfstudie](forward-events-event-grid-cloud.md) te volgen
* [Onderwerpen en abonnementen op de rand bewaken](monitor-topics-subscriptions.md)
