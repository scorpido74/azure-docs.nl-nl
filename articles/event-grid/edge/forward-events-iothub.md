---
title: Gebeurtenisrastergebeurtenissen doorsturen naar IoTHub - Azure Event Grid IoT Edge | Microsoft Documenten
description: Gebeurtenisrastergebeurtenissen doorsturen naar IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d0034810ff86de2a40e275ca54a2f0f9cbc856c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844697"
---
# <a name="tutorial-forward-events-to-iothub"></a>Zelfstudie: Gebeurtenissen doorsturen naar IoTHub

In dit artikel worden alle stappen doorlopen die nodig zijn om gebeurtenisrastergebeurtenissen door te sturen naar andere IoT Edge-modules, IoTHub met behulp van routes. Misschien wilt u dit doen om de volgende redenen:

* Gebruik nog steeds bestaande investeringen die al zijn gedaan met de routering van EdgeHub
* Stuur alle gebeurtenissen liever alleen vanaf een apparaat via IoT Hub

Als u deze zelfstudie wilt voltooien, moet u de volgende concepten begrijpen:

- [Concepten voor gebeurtenisrasters](concepts.md)
- [IoT Edge-hub](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Vereisten 
Om deze tutorial te voltooien, moet je:

* **Azure-abonnement** - Maak een [gratis account](https://azure.microsoft.com/free) als u er nog geen hebt. 
* **Azure IoT Hub en IoT Edge-apparaat** - Volg de stappen in de snelle start voor [Linux-](../../iot-edge/quickstart-linux.md) of [Windows-apparaten](../../iot-edge/quickstart.md) als u er nog geen hebt.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Onderwerp maken

Als uitgever van een gebeurtenis moet u een gebeurtenisrasteronderwerp maken. Het onderwerp verwijst naar een eindpunt waar uitgevers vervolgens gebeurtenissen naartoe kunnen sturen.

1. Maak topic4.json met de volgende inhoud. Zie onze [API-documentatie](api.md) voor meer informatie over de payload.

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Voer de volgende opdracht uit om het onderwerp te maken. HTTP-statuscode van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Voer de volgende opdracht uit om te controleren of het onderwerp is gemaakt. HTTP-statuscode van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   Voorbeelduitvoer:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>Gebeurtenisabonnement maken

Abonnees kunnen zich inschrijven voor evenementen die zijn gepubliceerd in een onderwerp. Om een evenement te ontvangen, moeten ze een abonnement op het evenementraster maken voor een interessant onderwerp.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Maak subscription4.json met de onderstaande content. Raadpleeg onze [API-documentatie](api.md) voor meer informatie over de payload.

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > De `endpointType` geeft aan `edgeHub`dat de abonnee is . Hiermee `outputName` geeft u de uitvoer op waarop de module Gebeurtenisraster gebeurtenissen routeert die overeenkomen met dit abonnement op edgeHub. Gebeurtenissen die overeenkomen met het bovenstaande abonnement `/messages/modules/eventgridmodule/outputs/sampleSub4`worden bijvoorbeeld naar geschreven .
2. Voer de volgende opdracht uit om het abonnement te maken. HTTP-statuscode van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Voer de volgende opdracht uit om te controleren of het abonnement is gemaakt. HTTP-statuscode van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    Voorbeelduitvoer:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>Een randhubroute instellen

Werk de route van de edge hub bij om de gebeurtenissen van het evenementabonnement door te sturen om als volgt naar IoTHub te worden doorgestuurd:

1. Aanmelden bij de [Azure-portal](https://ms.portal.azure.com)
1. Navigeer naar de **IoT-hub**.
1. **IoT Edge selecteren** in het menu
1. Selecteer de id van het doelapparaat in de lijst met apparaten.
1. Selecteer **Modules instellen**.
1. Selecteer **Volgende** en naar de sectie routes.
1. Voeg in de routes een nieuwe route toe

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  Bijvoorbeeld:

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > De bovenstaande route stuurt alle gebeurtenissen door die overeenkomen met dit abonnement dat moet worden doorgestuurd naar de IoT-hub. U de functies voor het routeren van de [Edge-hub](../../iot-edge/module-composition.md) gebruiken om de gebeurtenissen in het gebeurtenisraster verder te filteren en door te sturen naar andere IoT Edge-modules.

## <a name="setup-iot-hub-route"></a>IoT-hubroute instellen

Bekijk de [zelfstudie voor het routeren van IoT-hub](../../iot-hub/tutorial-routing.md) om een route in te stellen vanuit de IoT-hub, zodat u gebeurtenissen bekijken die zijn doorgestuurd vanuit de module Gebeurtenisraster. Gebruik `true` voor de query om de zelfstudie eenvoudig te houden.  



## <a name="publish-an-event"></a>Een gebeurtenis publiceren

1. Maak event4.json met de volgende inhoud. Zie onze [API-documentatie](api.md) voor meer informatie over de payload.

    ```json
        [
          {
            "id": "eventId-iothub-1",
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

1. Voer de volgende opdracht uit om gebeurtenis te publiceren:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>De levering van gebeurtenissen verifiëren

Zie de zelfstudie voor [het routeren van](../../iot-hub/tutorial-routing.md) IoT-hub voor de stappen om de gebeurtenissen weer te geven.

## <a name="cleanup-resources"></a>Resources opruimen

* Voer de volgende opdracht uit om het onderwerp en alle abonnementen aan de rand te verwijderen:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Verwijder ook alle resources die zijn gemaakt tijdens het instellen van IoTHub-routering in de cloud.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een onderwerp van het gebeurtenisraster, een abonnement op de edge-hub en gepubliceerde gebeurtenissen gemaakt. Nu u de basisstappen kent om door te sturen naar een randhub, raadpleegt u de volgende artikelen:

* Zie [Gids voor probleemoplossing](troubleshoot.md)voor het oplossen van problemen met het gebruik van Azure Event Grid op IoT Edge.
* Filters [voor randhubroute](../../iot-edge/module-composition.md) gebruiken om gebeurtenissen te partitioneren
* Persistentie van Event Grid-module instellen op [linux](persist-state-linux.md) of [Windows](persist-state-windows.md)
* [Documentatie volgen](configure-client-auth.md) om clientverificatie te configureren
* Gebeurtenissen doorsturen naar Azure Event Grid in de cloud door deze [zelfstudie](forward-events-event-grid-cloud.md) te volgen
* [Onderwerpen en abonnementen op de rand bewaken](monitor-topics-subscriptions.md)