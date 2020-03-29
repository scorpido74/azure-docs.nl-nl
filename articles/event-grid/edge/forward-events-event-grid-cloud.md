---
title: Randgebeurtenissen doorsturen naar gebeurtenisrastercloud - Azure Event Grid IoT Edge | Microsoft Documenten
description: Randgebeurtenissen doorsturen naar gebeurtenisrasterwolk
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7184fb5c45ce41de2bd63b55fb67cbd9ba6361e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844714"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Zelfstudie: Gebeurtenissen doorsturen naar de cloud van gebeurtenisraster

In dit artikel worden alle stappen doorlopen die nodig zijn om randgebeurtenissen door te sturen naar gebeurtenisraster in de Azure-cloud. Misschien wilt u dit doen om de volgende redenen:

* Reageer op randgebeurtenissen in de cloud.
* Gebeurtenissen doorsturen naar gebeurtenisraster in de cloud en Azure Event Hubs of Azure Storage-wachtrijen gebruiken om gebeurtenissen te bufferen voordat ze in de cloud worden verwerkt.

 Als u deze zelfstudie wilt voltooien, moet u inzicht hebben in concepten voor [gebeurtenisrasters](concepts.md) op edge en [Azure.](../concepts.md) Zie [gebeurtenishandlers](event-handlers.md)voor extra doeltypen . 

## <a name="prerequisites"></a>Vereisten 
Om deze tutorial te voltooien, moet je:

* **Azure-abonnement** - Maak een [gratis account](https://azure.microsoft.com/free) als u er nog geen hebt. 
* **Azure IoT Hub en IoT Edge-apparaat** - Volg de stappen in de snelle start voor [Linux-](../../iot-edge/quickstart-linux.md) of [Windows-apparaten](../../iot-edge/quickstart.md) als u er nog geen hebt.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Gebeurtenisrasteronderwerp en -abonnement maken in de cloud

Maak een gebeurtenisrasteronderwerp en -abonnement in de cloud door [deze zelfstudie te](../custom-event-quickstart-portal.md)volgen. Noteer `topicURL` `sasKey`, `topicName` en van het nieuw gemaakte onderwerp dat u later in de zelfstudie zult gebruiken.

Als u bijvoorbeeld een onderwerp `testegcloudtopic` hebt gemaakt met de naam In West-VS, zien de waarden er ongeveer als:

* **TopicUrl**:`https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **TopicName**:`testegcloudtopic`
* **SasKey**: Beschikbaar onder **AccessKey** van uw onderwerp. Toets **1 gebruiken**.

## <a name="create-event-grid-topic-at-the-edge"></a>Gebeurtenisrasteronderwerp aan de rand maken

1. Maak topic3.json met de volgende inhoud. Zie onze [API-documentatie](api.md) voor meer informatie over de payload.

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Voer de volgende opdracht uit om het onderwerp te maken. HTTP-statuscode van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Voer de volgende opdracht uit om te controleren of het onderwerp is gemaakt. HTTP-statuscode van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   Voorbeelduitvoer:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Abonnement op gebeurtenisraster aan de rand maken

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Maak subscription3.json met de volgende content. Zie onze [API-documentatie](api.md) voor meer informatie over de payload.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > De **endpointUrl** geeft aan dat de URL van het gebeurtenisrasteronderwerp in de cloud. De **sasKey** verwijst naar de sleutel van event grid cloud topic. De waarde in **topicName** wordt gebruikt om alle uitgaande gebeurtenissen te stempelen op Gebeurtenisraster. Dit kan handig zijn bij het plaatsen naar een gebeurtenisrasterdomeinonderwerp. Zie Gebeurtenisdomeinen voor meer informatie over het domeinonderwerp [EventGrid](../event-domains.md)

    Bijvoorbeeld:
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. Voer de volgende opdracht uit om het abonnement te maken. HTTP-statuscode van 200 OK moet worden geretourneerd.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Voer de volgende opdracht uit om te controleren of het abonnement is gemaakt. HTTP-statuscode van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    Voorbeelduitvoer:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>Een gebeurtenis aan de rand publiceren

1. Maak event3.json met de volgende inhoud. Zie [API-documentatie](api.md) voor meer informatie over de payload.

    ```json
        [
          {
            "id": "eventId-egcloud-0",
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

1. Voer de volgende opdracht uit:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>Randgebeurtenis in de cloud verifiëren

Zie de [zelfstudie](../custom-event-quickstart-portal.md)voor informatie over het bekijken van gebeurtenissen die door het cloudonderwerp worden geleverd.

## <a name="cleanup-resources"></a>Resources opruimen

* Voer de volgende opdracht uit om het onderwerp en alle abonnementen te verwijderen

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Verwijder ook onderwerp en abonnementen die in de cloud zijn gemaakt (Azure Event Grid).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een gebeurtenis op de rand gepubliceerd en doorgestuurd naar gebeurtenisraster in de Azure-cloud. Nu u de basisstappen kent om door te sturen naar Event Grid in de cloud:

* Zie [Gids voor probleemoplossing](troubleshoot.md)voor het oplossen van problemen met het gebruik van Azure Event Grid op IoT Edge.
* Gebeurtenissen doorsturen naar IoTHub door deze [zelfstudie](forward-events-iothub.md) te volgen
* Gebeurtenissen doorsturen naar Webhook in de cloud door deze [zelfstudie te volgen](pub-sub-events-webhook-cloud.md)
* [Onderwerpen en abonnementen op de rand bewaken](monitor-topics-subscriptions.md)
