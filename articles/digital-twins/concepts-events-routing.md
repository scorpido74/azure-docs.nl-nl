---
title: Gebeurtenissen en berichten routeren - Azure Digital Twins | Microsoft Documenten
description: Overzicht van het routeren van gebeurtenissen en berichten naar serviceeindpunten met Azure Digital Twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 65b760eaf28d907fab3654ed92f960be7556b0d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862353"
---
# <a name="routing-iot-events-and-messages"></a>IoT-gebeurtenissen en -berichten routeren

Internet of Things-oplossingen verenigen vaak verschillende krachtige services, waaronder opslag, analyse en meer. In dit artikel wordt beschreven hoe u Azure Digital Twins-apps koppelen aan Azure-analyse-, AI- en opslagservices om hen diepere inzichten en functionaliteiten te bieden.

## <a name="route-types"></a>Routetypen  

Azure Digital Twins biedt twee manieren om IoT-gebeurtenissen te verbinden met andere Azure-services of bedrijfstoepassingen:

* **Azure Digital Twins-gebeurtenissen routeren:** een object in de ruimtelijke grafiek dat verandert, telemetriegegevens die zijn ontvangen of een door de gebruiker gedefinieerde functie die een melding maakt op basis van vooraf gedefinieerde voorwaarden, kan Azure Digital Twins-gebeurtenissen activeren. Gebruikers kunnen deze gebeurtenissen verzenden naar [Azure Event Hubs,](https://azure.microsoft.com/services/event-hubs/) [Azure Service Bus-onderwerpen](https://azure.microsoft.com/services/service-bus/)of [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) voor verdere verwerking.

* **Telemetrie van het routeringsapparaat:** Azure Digital Twins kan naast routeringsgebeurtenissen ook onbewerkte apparaattelemetrieberichten doorsturen naar gebeurtenishubs voor verdere inzichten en analyses. Dit soort berichten worden niet verwerkt door Azure Digital Twins. En ze worden alleen doorgestuurd naar de evenementenhub.

Gebruikers kunnen een of meer uitgangseindpunten opgeven om gebeurtenissen uit te zenden of berichten door te sturen. Gebeurtenissen en berichten worden naar de eindpunten verzonden volgens deze vooraf gedefinieerde routeringsvoorkeuren. Met andere woorden, gebruikers kunnen een bepaald eindpunt opgeven om grafiekbewerkingen te ontvangen, een ander om telemetriegebeurtenissen van het apparaat te ontvangen, enzovoort.

[![Azure Digital Twins-gebeurtenisroutering](media/concepts/digital-twins-events-routing.png)](media/concepts/digital-twins-events-routing.png#lightbox)

Routering naar gebeurtenishubs behoudt de volgorde waarin telemetrieberichten worden verzonden. Dus ze komen op het eindpunt in dezelfde volgorde als ze oorspronkelijk werden ontvangen. 

Event Grid en Service Bus garanderen niet dat eindpunten gebeurtenissen ontvangen in dezelfde volgorde als ze hebben plaatsgevonden. Het gebeurtenisschema bevat echter een tijdstempel die kan worden gebruikt om de volgorde te identificeren nadat de gebeurtenissen op het eindpunt zijn aangekomen.

## <a name="route-implementation"></a>Routeimplementatie

De Azure Digital Twins-service ondersteunt momenteel de volgende **EndpointTypes:**

* **EventHub** is het eindpunt van de verbinding smettenverbinding voor gebeurtenissenhubs.
* **ServiceBus** is het eindpunt van de servicebusverbindingstring.
* **EventGrid** is het eindpunt van de gebeurtenisrasterkoppeling.

Azure Digital Twins ondersteunt momenteel de volgende **Gebeurtenistypen** die naar het gekozen eindpunt worden verzonden:

* **DeviceMessages** zijn telemetrieberichten die vanaf de apparaten van de gebruikers worden verzonden en door het systeem worden doorgestuurd.
* **TopologieOperatie** is een bewerking die de grafiek of metagegevens van de grafiek wijzigt. Een voorbeeld is het toevoegen of verwijderen van een entiteit, zoals een spatie.
* **SpaceChange** is een wijziging in de berekende waarde van een ruimte die voortvloeit uit een telemetriebericht van een apparaat.
* **SensorChange** is een wijziging in de berekende waarde van een sensor die voortvloeit uit een telemetriebericht van een apparaat.
* **UdfCustom** is een aangepaste melding van een door de gebruiker gedefinieerde functie.

> [!IMPORTANT]  
> Niet alle **EndpointTypes** ondersteunen alle **Gebeurtenistypen**.
> Bekijk de volgende tabel voor de **gebeurtenistypen** die zijn toegestaan voor elk **EndpointType**.

|             | DeviceMessages | TopologieOperatie | Ruimtewijzigen | Sensorverandering | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid (EventGrid)|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Lees [Uitgangen en eindpunten](how-to-egress-endpoints.md)voor meer informatie over het maken van eindpunten en voorbeelden van het schema van gebeurtenissen.

## <a name="next-steps"></a>Volgende stappen

- Lees Voor meer informatie over de previewlimieten van Azure Digital Twins [de limieten voor openbare preview-service](concepts-service-limits.md).

- Als u een voorbeeld van Azure Digital Twins wilt uitproberen, leest u de [snelle start om beschikbare kamers te vinden.](quickstart-view-occupancy-dotnet.md)