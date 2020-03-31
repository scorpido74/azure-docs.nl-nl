---
title: Gebeurtenishandlers en -bestemmingen - Azure Event Grid IoT Edge | Microsoft Documenten
description: Gebeurtenishandlers en -bestemmingen in gebeurtenisraster op rand
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 35bf5af90aa5f0456aa8d68f0e4e8aaacc6cf84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849745"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Gebeurtenishandlers en -bestemmingen in gebeurtenisraster op rand

Een gebeurtenishandler is de plaats waar de gebeurtenis voor verdere actie of om de gebeurtenis te verwerken. Met de module Gebeurtenisraster op rand kan de gebeurtenishandler zich op hetzelfde randapparaat, een ander apparaat of in de cloud bevinden. U elke WebHook gebruiken om gebeurtenissen af te handelen of gebeurtenissen verzenden naar een van de native handlers zoals Azure Event Grid.

In dit artikel vindt u informatie over het configureren van elk artikel.

## <a name="webhook"></a>WebHook (WebHook)

Als u wilt publiceren op een `endpointType` `WebHook` WebHook-eindpunt, stelt u het in en geeft u het:

* endpointUrl: de webhook-eindpunt-URL

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure Event Grid

Als u wilt publiceren naar een Azure `endpointType` `eventGrid` Event Grid-cloudeindpunt, stelt u het instellen en op bieden:

* endpointUrl: URL van gebeurtenisrasteronderwerp in de cloud
* sasKey: De SAS-toets van Event Grid Topic
* topicName: Geef de naam om alle uitgaande gebeurtenissen te stempelen op Gebeurtenisraster. Onderwerpnaam is handig bij het plaatsen naar een onderwerp eventgriddomein.

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

## <a name="iot-edge-hub"></a>IoT Edge-hub

Als u wilt publiceren in `endpointType` een `edgeHub` Edge Hub-module, stelt u het in en geeft u het:

* outputName: de uitvoer waarop de module Gebeurtenisraster gebeurtenissen routeert die overeenkomen met dit abonnement naar edgeHub. Gebeurtenissen die overeenkomen met het onderstaande abonnement worden bijvoorbeeld naar /messages/modules/eventgridmodule/outputs/sampleSub4 geschreven.

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

## <a name="event-hubs"></a>Event Hubs

Als u wilt publiceren naar `endpointType` `eventHub` een gebeurtenishub, stelt u het in en geeft u het:

* connectionString: verbindingstekenreeks voor de specifieke gebeurtenishub die u target die wordt gegenereerd via een beleid voor gedeelde toegang.

    >[!NOTE]
    > De verbindingstekenreeks moet entiteitsspecifiek zijn. Het gebruik van een verbindingstekenreeks voor naamruimte werkt niet. U een entiteitsspecifieke verbindingstekenreeks genereren door te navigeren naar de specifieke gebeurtenishub die u wilt publiceren in de Azure Portal en te klikken op **Beleid voor gedeelde toegang** om een nieuwe entiteitsspecifieke connecection-tekenreeks te genereren.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Service Bus-wachtrijen

Als u wilt publiceren in `endpointType` een `serviceBusQueue` servicebuswachtrij, stelt u het instellen en op bieden:

* connectionString: verbindingstekenreeks voor de specifieke servicebuswachtrij die u target die wordt gegenereerd via een beleid voor gedeelde toegang.

    >[!NOTE]
    > De verbindingstekenreeks moet entiteitsspecifiek zijn. Het gebruik van een verbindingstekenreeks voor naamruimte werkt niet. Genereer een entiteitsspecifieke verbindingstekenreeks door te navigeren naar de specifieke servicebuswachtrij die u wilt publiceren in de Azure Portal en te klikken op **Beleid voor gedeelde toegang** om een nieuwe entiteitsspecifieke connecection-tekenreeks te genereren.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Service Bus-onderwerpen

Als u wilt publiceren naar `endpointType` een `serviceBusTopic` servicebusonderwerp, stelt u het aan en de informatie in:

* connectionString: verbindingstekenreeks voor het specifieke servicebusonderwerp dat u target, gegenereerd via een beleid voor gedeelde toegang.

    >[!NOTE]
    > De verbindingstekenreeks moet entiteitsspecifiek zijn. Het gebruik van een verbindingstekenreeks voor naamruimte werkt niet. Genereer een entiteitsspecifieke verbindingstekenreeks door te navigeren naar het specifieke servicebusonderwerp dat u wilt publiceren in de Azure Portal en te klikken op **Beleid voor gedeelde toegang** om een nieuwe entiteitsspecifieke connecection-tekenreeks te genereren.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>Opslagwachtrijen

Als u wilt publiceren in `endpointType` `storageQueue` een opslagwachtrij, stelt u het in en geeft u het:

* queueName: naam van de opslagwachtrij die u publiceert.
* verbindingTekenreeks: verbindingstekenreeks voor het opslagaccount waarin de opslagwachtrij zich bevindt.

    >[!NOTE]
    > De verbindingstekenreeks die wordt gebruikt voor opslagwachtrijen is niet entiteitsspecifiek. In plaats daarvan moet het alleen de verbindingstekenreeks voor het opslagaccount.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```