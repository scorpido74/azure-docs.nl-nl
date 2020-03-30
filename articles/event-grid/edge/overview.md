---
title: Event driven architecturen aan de rand — Azure Event Grid on IoT Edge
description: Gebruik Azure Event Grid als een module op IoT Edge voor voorwaartse gebeurtenissen tussen modules, randapparaten en de cloud.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 10/22/2019
ms.author: babanisa
ms.openlocfilehash: feac5891734731e6f7377750127958a40a815036
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76844650"
---
# <a name="what-is-azure-event-grid-on-azure-iot-edge"></a>Wat is Azure Event Grid op Azure IoT Edge?
Event Grid on IoT Edge brengt de kracht en flexibiliteit van Azure Event Grid naar de rand. Maak onderwerpen, publiceer gebeurtenissen en abonneer meerdere bestemmingen, of het nu gaat om modules op hetzelfde apparaat, andere edge-apparaten of services in de cloud.

Net als in de cloud verwerkt de Module Gebeurtenisraster op IoT Edge routering, filtering en betrouwbare levering van gebeurtenissen op schaal. Filter gebeurtenissen om ervoor te zorgen dat alleen relevante gebeurtenissen naar verschillende gebeurtenishandlers worden verzonden met geavanceerde tekenreeks-, numerieke en booleaanse filters. Logica opnieuw proberen zorgt ervoor dat de gebeurtenis de doelbestemming bereikt, zelfs als deze niet beschikbaar is op het moment van publiceren. Hiermee u Event Grid op IoT Edge gebruiken als een krachtig winkel- en voorwaartsmechanisme.

Event Grid on IoT Edge ondersteunt zowel CloudEvents v1.0 als aangepaste gebeurtenisschema's. Het ondersteunt ook dezelfde Pub /Sub semantiek als Event Grid in de cloud voor eenvoudige interoperabiliteit.

In dit artikel vindt u een overzicht van Azure Event Grid op IoT Edge. Zie [Lokaal abonneren](pub-sub-events-webhook-local.md)op evenementen voor stapsgewijze instructies om deze module op de rand te gebruiken. 

![Event Grid on IoT Edge-model van bronnen en handlers](../media/edge-overview/functional-model.png)

Deze afbeelding toont enkele manieren waarop u Event Grid op IoT Edge gebruiken en is geen uitgebreide lijst met ondersteunde functionaliteit.

## <a name="when-to-use-event-grid-on-iot-edge"></a>Wanneer eventgrid gebruiken op IoT Edge

Event Grid on IoT Edge biedt een eenvoudig te gebruiken, betrouwbaar eventingmodel voor tussen de rand en de cloud.

Event Grid on IoT Edge is gebouwd met een symmetrisch runtime-oppervlak voor de Azure-cloudservice, zodat u dezelfde gebeurtenissen en API-aanroepen gebruiken waar u maar wilt. Of u nu pub/sub doet in de cloud, aan de rand of tussen de twee, Event Grid op IoT Edge kan nu uw oplossing zijn voor een go-to.

Gebruik Gebeurtenisraster op IoT Edge om eenvoudige workflows tussen modules te activeren. Maak bijvoorbeeld een onderwerp en publiceer gebeurtenissen met 'opslagblob gemaakt' vanuit uw opslagmodule naar het onderwerp. U nu een of meerdere functies of aangepaste modules abonneren op die onderwerpen.

Breid uw functionaliteit uit tussen randapparaten. Als u blobmodulegebeurtenissen publiceert en de rekenkracht van meerdere apparaten in de buurt wilt gebruiken, maakt u abonnementen voor meerdere apparaten.

Maak tot slot verbinding met de cloud. Als uw blobmodulegebeurtenissen periodiek moeten worden gesynchroniseerd met de cloud, de grotere rekenkracht gebruikt die beschikbaar is in de cloud of verwerkte gegevens wilt verzenden, maakt u extra abonnementen voor cloudservices.

Event Grid on IoT Edge biedt een flexibele en betrouwbare ontkoppelde eventingarchitectuur.

## <a name="event-sources"></a>Gebeurtenisbronnen

Net als in de cloud maakt Event Grid on IoT Edge directe integratie tussen modules mogelijk om eventdriven architecturen te bouwen. Momenteel kunnen de gebeurtenissen worden verzonden naar Event Grid op IoT Edge van:

* Azure Blob Storage op IoT Edge
* CloudEvents-bronnen
* Aangepaste modules & containers via HTTP POST

## <a name="event-handlers"></a>Event Handlers

Event Grid on IoT Edge is gebouwd om gebeurtenissen naar de gewenste plek te sturen. Momenteel worden de volgende bestemmingen ondersteund:

* Andere modules, waaronder IoT Hub, functies en aangepaste modules
* Andere randapparaten
* WebHooks
* Azure Event Grid-cloudservice
* Event Hubs
* Service Bus-wachtrijen
* Service Bus-onderwerpen
* Opslagwachtrijen

## <a name="supported-environments"></a>Ondersteunde omgevingen
Momenteel worden Windows 64-bits, Linux 64-bits en ARM 32-bits omgevingen ondersteund.

## <a name="concepts"></a>Concepten

Er zijn vijf concepten in Azure Event Grid waarmee u aan de slag kunt:

* **Gebeurtenissen** - Wat is er gebeurd.
* **Gebeurtenisbronnen** — Waar het evenement heeft plaatsgevonden.
* **Onderwerpen** : het eindpunt waarop uitgevers gebeurtenissen verzenden.
* **Gebeurtenisabonnementen** : het eindpunt of het ingebouwde mechanisme om gebeurtenissen te routeren, soms naar meer dan één handler. Abonnementen worden ook gebruikt door handlers om binnenkomende gebeurtenissen op een slimme manier te filteren.
* **Gebeurtenishandlers** : de app of service die op de gebeurtenis reageert.

## <a name="cost"></a>Kosten

Event Grid on IoT Edge is gratis tijdens de openbare preview.

## <a name="issues"></a>Problemen
Eventuele problemen melden met het gebruik [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)van gebeurtenisraster op IoT Edge op .

## <a name="next-steps"></a>Volgende stappen

* [Publiceren, u abonneren op evenementen lokaal](pub-sub-events-webhook-local.md)
* [Publiceren, u abonneren op gebeurtenissen in de cloud](pub-sub-events-webhook-cloud.md)
* [Gebeurtenissen doorsturen naar gebeurtenisrasterwolk](forward-events-event-grid-cloud.md)
* [Gebeurtenissen doorsturen naar IoTHub](forward-events-iothub.md)
* [Lokaal reageren op gebeurtenissen in Blob Storage](react-blob-storage-events-locally.md)