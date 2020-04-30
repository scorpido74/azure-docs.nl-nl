---
title: 'Door gebeurtenis gestuurde architecturen op Edge: Azure Event Grid op IoT Edge'
description: Gebruik Azure Event Grid als een module op IoT Edge voor het door sturen van gebeurtenissen tussen modules, edge-apparaten en de Cloud.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 10/22/2019
ms.author: babanisa
ms.openlocfilehash: feac5891734731e6f7377750127958a40a815036
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76844650"
---
# <a name="what-is-azure-event-grid-on-azure-iot-edge"></a>Wat is Azure Event Grid op Azure IoT Edge?
Event Grid op IoT Edge biedt de kracht en flexibiliteit van Azure Event Grid tot aan de rand. Maak onderwerpen, publiceer gebeurtenissen en Abonneer u op meerdere doelen, ongeacht of ze modules zijn op hetzelfde apparaat, andere edge-apparaten of services in de Cloud.

Net als in de Cloud, wordt door de module Event Grid op IoT Edge de route ring, het filteren en de betrouw bare levering van gebeurtenissen op schaal verwerkt. Filter gebeurtenissen om ervoor te zorgen dat alleen relevante gebeurtenissen worden verzonden naar verschillende gebeurtenis-handlers met behulp van geavanceerde teken reeks-, numerieke en Booleaanse filters. Pogings logica zorgt ervoor dat de gebeurtenis de doel bestemming bereikt, zelfs als deze niet beschikbaar is op het moment van publiceren. Hiermee kunt u Event Grid op IoT Edge als een krachtig archief-en doorstuur mechanisme gebruiken.

Event Grid op IoT Edge ondersteunt zowel CloudEvents v 1.0 als aangepaste gebeurtenis schema's. Het ondersteunt ook dezelfde pub/sub-semantiek als Event Grid in de Cloud voor eenvoudige interoperabiliteit.

Dit artikel bevat een overzicht van Azure Event Grid op IoT Edge. Zie [publiceren, abonneren op gebeurtenissen lokaal](pub-sub-events-webhook-local.md)voor stapsgewijze instructies voor het gebruik van deze module aan de rand. 

![Event Grid op IoT Edge model van bronnen en handlers](../media/edge-overview/functional-model.png)

In deze afbeelding ziet u een aantal manieren waarop u Event Grid op IoT Edge kunt gebruiken. Dit is geen uitgebreide lijst met ondersteunde functies.

## <a name="when-to-use-event-grid-on-iot-edge"></a>Wanneer gebruikt u Event Grid op IoT Edge

Event Grid op IoT Edge biedt een eenvoudig te gebruiken, betrouw bare gebeurtenis model voor tussen de rand en de Cloud.

Event Grid op IoT Edge is gebouwd met een symmetrische runtime surface area naar de Azure-Cloud service, zodat u altijd dezelfde gebeurtenissen en API-aanroepen kunt gebruiken als u dat nodig hebt. Of u nu pub/sub in de Cloud, aan de rand of tussen de twee, Event Grid op IoT Edge kunt u de oplossing die u wilt bezoeken.

Gebruik Event Grid op IoT Edge om eenvoudige werk stromen tussen modules te activeren. U kunt bijvoorbeeld een onderwerp maken en de gebeurtenissen opslag-BLOB gemaakt publiceren vanuit uw opslag module naar het onderwerp. U kunt nu een of meer functies of aangepaste modules abonneren op die onderwerpen.

Breid uw functionaliteit uit tussen apparaten van de rand. Als u de gebeurtenissen van de BLOB-module publiceert en u de reken kracht van meerdere nabij door edge-apparaten wilt gebruiken, maakt u abonnementen voor meerdere apparaten.

Tot slot maakt u verbinding met de Cloud. Als de gebeurtenissen van uw BLOB-module periodiek moeten worden gesynchroniseerd met de Cloud, gebruikt u de grotere beschik bare reken kracht in de Cloud of verwerkte gegevens naar boven, maakt u aanvullende Cloud service abonnementen.

Event Grid op IoT Edge biedt een flexibele en betrouw bare, losgekoppelde gebeurtenis architectuur.

## <a name="event-sources"></a>Gebeurtenisbronnen

Net als in de cloud kan Event Grid op IoT Edge directe integratie tussen modules toestaan voor het bouwen van op gebeurtenissen gebaseerde architecturen. Op dit moment kunnen de gebeurtenissen worden verzonden naar Event Grid op IoT Edge van:

* Azure Blob Storage op IoT Edge
* CloudEvents bronnen
* Aangepaste modules & containers via HTTP POST

## <a name="event-handlers"></a>Event Handlers

Event Grid op IoT Edge is gebouwd voor het verzenden van gebeurtenissen naar elke gewenste locatie. Momenteel worden de volgende bestemmingen ondersteund:

* Andere modules, waaronder IoT Hub, functies en aangepaste modules
* Andere edge-apparaten
* WebHooks
* Azure Event Grid-Cloud service
* Event Hubs
* Service Bus-wachtrijen
* Service Bus-onderwerpen
* Opslagwachtrijen

## <a name="supported-environments"></a>Ondersteunde omgevingen
Momenteel worden de Windows 64-bits, Linux 64-bits en ARM 32-bits omgevingen ondersteund.

## <a name="concepts"></a>Concepten

Er zijn vijf concepten in Azure Event Grid waarmee u aan de slag kunt gaan:

* **Gebeurtenissen** : wat er is gebeurd.
* **Gebeurtenis bronnen** : waar het evenement plaatsvond.
* **Onderwerpen** : het eind punt waar uitgevers gebeurtenissen verzenden.
* **Gebeurtenis abonnementen** : het eind punt of het ingebouwde mechanisme om gebeurtenissen te routeren, soms naar meer dan één handler. Abonnementen worden ook gebruikt door handlers om binnenkomende gebeurtenissen op een slimme manier te filteren.
* **Gebeurtenis-handlers** : de app of service die reageert op de gebeurtenis.

## <a name="cost"></a>Kosten

Event Grid op IoT Edge is gratis tijdens de open bare preview.

## <a name="issues"></a>Problemen
Meld problemen met het gebruik van Event Grid op IoT Edge [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)op.

## <a name="next-steps"></a>Volgende stappen

* [Publiceren, lokaal abonneren op gebeurtenissen](pub-sub-events-webhook-local.md)
* [Publiceren, abonneren op gebeurtenissen in de Cloud](pub-sub-events-webhook-cloud.md)
* [Gebeurtenissen door sturen naar Event Grid Cloud](forward-events-event-grid-cloud.md)
* [Gebeurtenissen door sturen naar IoTHub](forward-events-iothub.md)
* [Lokaal reageren op gebeurtenissen in Blob Storage](react-blob-storage-events-locally.md)