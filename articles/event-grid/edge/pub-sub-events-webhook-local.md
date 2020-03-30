---
title: Publiceren, u abonneren op gebeurtenissen lokaal - Azure Event Grid IoT Edge | Microsoft Documenten
description: Publiceren, u abonneren op gebeurtenissen lokaal met Webhook met Gebeurtenisraster op IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba82b1bea4753cd51e275a78b248247032d79a01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281000"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Zelfstudie: Publiceren, u abonneren op gebeurtenissen lokaal

In dit artikel vindt u alle stappen die nodig zijn om evenementen te publiceren en u te abonneren met behulp van Event Grid op IoT Edge.

> [!NOTE]
> Zie [Gebeurtenisrasterconcepten](concepts.md)voor meer informatie over onderwerpen en abonnementen van Azure Event Grid .

## <a name="prerequisites"></a>Vereisten 
Om deze tutorial te voltooien, moet je:

* **Azure-abonnement** - Maak een [gratis account](https://azure.microsoft.com/free) als u er nog geen hebt. 
* **Azure IoT Hub en IoT Edge-apparaat** - Volg de stappen in de quickstart voor [Linux-](../../iot-edge/quickstart-linux.md) of [Windows-apparaten](../../iot-edge/quickstart.md) als u er nog geen hebt.

## <a name="deploy-event-grid-iot-edge-module"></a>GebeurtenisrasterIoT Edge-module implementeren

Er zijn verschillende manieren om modules te implementeren op een IoT Edge-apparaat en ze werken allemaal voor Azure Event Grid op IoT Edge. In dit artikel worden de stappen beschreven om gebeurtenisraster op IoT Edge te implementeren vanuit de Azure-portal.

>[!NOTE]
> In deze zelfstudie implementeert u de module Event Grid zonder volharding. Dit betekent dat alle onderwerpen en abonnementen die u in deze zelfstudie maakt, worden verwijderd wanneer u de module opnieuw implementeert. Zie de volgende artikelen: Status blijven bestaan [in Linux](persist-state-linux.md) of Persist status in [Windows](persist-state-windows.md)voor meer informatie over het instellen van persistentie. Voor productieworkloads raden we u aan de Event Grid-module met volharding te installeren.


### <a name="select-your-iot-edge-device"></a>Selecteer uw IoT Edge-apparaat

1. Aanmelden bij de [Azure-portal](https://portal.azure.com)
1. Navigeer naar uw IoT-hub.
1. Selecteer **IoT Edge** in het menu in de sectie **Automatisch apparaatbeheer.** 
1. Klik op de id van het doelapparaat in de lijst met apparaten
1. Selecteer **Modules instellen**. Houd de pagina open. U gaat verder met de stappen in het volgende gedeelte.

### <a name="configure-a-deployment-manifest"></a>Een implementatiemanifest configureren

Een implementatiemanifest is een JSON-document dat beschrijft welke modules moeten worden geïmplementeerd, hoe gegevens tussen de modules stromen en de gewenste eigenschappen van de moduletweeling. De Azure-portal heeft een wizard die u door het maken van een implementatiemanifest leidt, in plaats van het JSON-document handmatig te bouwen.  Het heeft drie stappen: **Modules toevoegen,** **Routes opgeven**en **Implementatie controleren**.

### <a name="add-modules"></a>Modules toevoegen

1. Selecteer **Toevoegen** in de sectie **Implementatiemodules**
1. Selecteer **IoT Edge Module** in de typen modules in de vervolgkeuzelijst
1. Geef de opties voor het maken van containers op:

   * **Naam**: eventgridmodule
   * **Afbeelding URI**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opties voor het maken van containers:**

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. Klik **op Opslaan**
 1. Ga verder naar de volgende sectie om de Azure Event Grid Subscriber-module toe te voegen voordat u deze samen implementeert.

    >[!IMPORTANT]
    > In deze zelfstudie implementeert u de module Gebeurtenisraster waarbij clientverificatie is uitgeschakeld. Voor productieworkloads raden we u aan de clientverificatie in te schakelen. Zie [Beveiliging en verificatie](security-authentication.md)voor meer informatie over het veilig configureren van de gebeurtenisrastermodule.
    > 
    > Als u een Azure VM als randapparaat gebruikt, voegt u een inkomende poortregel toe om binnenkomend verkeer op poort 4438 toe te staan. Zie Poorten openen voor [een vm voor](../../virtual-machines/windows/nsg-quickstart-portal.md)instructies voor het toevoegen van de regel.
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>IoT Edge-module voor gebeurtenisrasterabonnees implementeren

In deze sectie ziet u hoe u een andere IoT-module implementeert die fungeert als gebeurtenishandler waaraan gebeurtenissen kunnen worden geleverd.

### <a name="add-modules"></a>Modules toevoegen

1. Selecteer opnieuw **toevoegen in** de sectie **Implementatiemodules.** 
1. Selecteer **IoT Edge Module** in de typen modules in de vervolgkeuzelijst
1. Geef de opties voor naam, afbeelding en containermaken van de container op:

   * **Naam**: abonnee
   * **Afbeelding URI**:`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Opties voor het maken van containers:** geen
1. Klik **op Opslaan**
1. Klik **op Volgende** om verder te gaan naar de sectie routes

 ### <a name="setup-routes"></a>Setup routes

De standaardroutes behouden en **Volgende** selecteren om door te gaan naar de controlesectie

### <a name="submit-the-deployment-request"></a>De implementatieaanvraag indienen

1. In de sectie beoordeling ziet u het JSON-implementatiemanifest dat is gemaakt op basis van uw selecties in de vorige sectie. Controleer of u beide modules ziet: **eventgridmodule** en **abonnee** in de JSON. 
1. Controleer uw implementatiegegevens en selecteer **Verzenden**. Nadat u de implementatie hebt verzonden, keert u terug naar de **apparaatpagina.**
1. Controleer **in**de sectie Modules of zowel **eventgrid-** als **abonneemodules** worden vermeld. En controleer of de **opgegeven in-implementatie** en **gerapporteerde apparaatkolommen** zijn ingesteld op **Ja**.

    Het kan even duren voordat de module op het apparaat is gestart en vervolgens wordt gerapporteerd aan IoT Hub. Vernieuw de pagina om een bijgewerkte status te zien.

## <a name="create-a-topic"></a>Een onderwerp maken

Als uitgever van een gebeurtenis moet u een gebeurtenisrasteronderwerp maken. In Azure Event Grid verwijst een onderwerp naar een eindpunt waar uitgevers gebeurtenissen naartoe kunnen sturen.

1. Maak topic.json met de volgende inhoud. Zie onze [API-documentatie](api.md)voor meer informatie over de payload.

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. Voer de volgende opdracht uit om een gebeurtenisrasteronderwerp te maken. Controleer of u de HTTP-statuscode ziet `200 OK`.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. Voer de volgende opdracht uit om te controleren of het onderwerp is gemaakt. HTTP-statuscode van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   Voorbeelduitvoer:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Een gebeurtenisabonnement maken

Abonnees kunnen zich inschrijven voor evenementen die zijn gepubliceerd in een onderwerp. Als je een evenement wilt ontvangen, moet je een Event Grid-abonnement maken voor een interessant onderwerp.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Maak subscription.json met de volgende inhoud. Zie onze [API-documentatie](api.md) voor meer informatie over de payload

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > De eigenschap **endpointType** geeft aan dat de abonnee een **Webhook**is.  De **endpointUrl** geeft de URL op waarop de abonnee naar gebeurtenissen luistert. Deze URL komt overeen met het voorbeeld van Azure Subscriber dat u eerder hebt geïmplementeerd.
2. Voer de volgende opdracht uit om een abonnement voor het onderwerp te maken. Controleer of u de HTTP-statuscode ziet `200 OK`.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. Voer de volgende opdracht uit om te controleren of het abonnement is gemaakt. HTTP-statuscode van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    Voorbeelduitvoer:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Een gebeurtenis publiceren

1. Maak event.json met de volgende inhoud. Zie onze [API-documentatie](api.md)voor meer informatie over de payload.

    ```json
        [
          {
            "id": "eventId-func-0",
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
1. Voer de volgende opdracht uit om een gebeurtenis te publiceren.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>De levering van gebeurtenissen verifiëren

1. SSH of RDP in uw IoT Edge VM.
1. Controleer de abonneelogboeken:

    Voer in Windows de volgende opdracht uit:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   Voer op Linux de volgende opdracht uit:

    ```sh
    sudo docker logs subscriber
    ```

    Voorbeelduitvoer:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>Resources opruimen

* Voer de volgende opdracht uit om het onderwerp en alle abonnementen te verwijderen.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* Verwijder de abonneemodule van uw IoT Edge-apparaat.


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een onderwerp, abonnement en gepubliceerde gebeurtenissen voor een gebeurtenisraster gemaakt. Nu u de basisstappen kent, raadpleegt u de volgende artikelen: 

- Zie [Gids voor probleemoplossing](troubleshoot.md)voor het oplossen van problemen met het gebruik van Azure Event Grid op IoT Edge.
- Abonnement maken/bijwerken met [filters](advanced-filtering.md).
- Persistentie van Event Grid-module inschakelen op [Linux](persist-state-linux.md) of [Windows](persist-state-windows.md)
- [Documentatie volgen](configure-client-auth.md) om clientverificatie te configureren
- Gebeurtenissen doorsturen naar Azure-functies in de cloud door deze [zelfstudie te volgen](pub-sub-events-webhook-cloud.md)
- [Reageren op blobopslaggebeurtenissen op IoT Edge](react-blob-storage-events-locally.md)
- [Onderwerpen en abonnementen op de rand bewaken](monitor-topics-subscriptions.md)

