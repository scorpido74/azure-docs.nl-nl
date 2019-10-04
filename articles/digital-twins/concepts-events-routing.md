---
title: Routerings gebeurtenissen en berichten-Azure Digital Apparaatdubbels | Microsoft Docs
description: Overzicht van de route ring van gebeurtenissen en berichten naar service-eind punten met Azure Digital Apparaatdubbels
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 217a1d94a4a5235fc5886f34986ffcb3aef60873
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949252"
---
# <a name="routing-events-and-messages"></a>Routering van gebeurtenissen en berichten

IoT-oplossingen zijn vaak een aantal krachtige services die opslag, analyses en meer omvatten. In dit artikel wordt beschreven hoe u Azure Digital Apparaatdubbels-apps verbindt met Azure Analytics-, AI-en Storage-services om hen meer inzicht en functionaliteit te bieden.

## <a name="route-types"></a>Route typen  

Azure Digital Apparaatdubbels biedt twee manieren om IoT-gebeurtenissen te verbinden met andere Azure-Services of zakelijke toepassingen:

* **Azure Digital apparaatdubbels-gebeurtenissen routeren**: Een object in de ruimtelijke grafiek dat wijzigingen aanbrengt, de ontvangen telemetriegegevens of een door de gebruiker gedefinieerde functie die een melding maakt op basis van vooraf gedefinieerde voor waarden kan gebeurtenissen van het Azure Digital Apparaatdubbels activeren. Gebruikers kunnen deze gebeurtenissen verzenden naar [Azure Event hubs](https://azure.microsoft.com/services/event-hubs/), [Azure Service Bus onderwerpen](https://azure.microsoft.com/services/service-bus/)of [Azure Event grid](https://azure.microsoft.com/services/event-grid/) voor verdere verwerking.

* **Telemetrie van routerings apparaat**: Azure Digital Apparaatdubbels kan naast routerings gebeurtenissen ook telemetrie-berichten van onbewerkte apparaten routeren naar Event Hubs voor meer inzichten en analyses. Deze typen berichten worden niet verwerkt door Azure Digital Apparaatdubbels. En ze worden alleen doorgestuurd naar de Event Hub.

Gebruikers kunnen een of meer uitstaande eind punten opgeven voor het verzenden van gebeurtenissen of het door sturen van berichten. Gebeurtenissen en berichten worden verzonden naar de eind punten volgens deze vooraf gedefinieerde routerings voorkeuren. Met andere woorden, gebruikers kunnen een bepaald eind punt opgeven om grafiek bewerkings gebeurtenissen te ontvangen, een andere voor het ontvangen van telemetrie-gebeurtenissen van apparaten, enzovoort.

[route ring voor 1Azure digitale Apparaatdubbels-gebeurtenissen @no__t](media/concepts/digital-twins-events-routing.png)](media/concepts/digital-twins-events-routing.png#lightbox)

Route ring naar Event Hubs houdt de volg orde bij waarin telemetrie-berichten worden verzonden. Ze arriveren op het eind punt in dezelfde volg orde als waarin ze oorspronkelijk werden ontvangen. Event Grid en Service Bus niet garanderen dat eind punten gebeurtenissen ontvangen in dezelfde volg orde als waarin ze zijn opgetreden. Het gebeurtenis schema bevat echter een tijds tempel dat kan worden gebruikt om de volg orde te identificeren nadat de gebeurtenissen op het eind punt arriveren.

## <a name="route-implementation"></a>Route-implementatie

De Azure Digital Apparaatdubbels-service ondersteunt momenteel de volgende **EndpointTypes**:

* **EventHub** is het event hubs Connection String eind punt.
* **ServiceBus** is het service bus connection string eind punt.
* **EventGrid** is het event grid Connection String eind punt.

Azure Digital Apparaatdubbels ondersteunt momenteel de volgende **EventTypes** die naar het gekozen eind punt worden verzonden:

* **DeviceMessages** zijn telemetriegegevens die van de apparaten van de gebruiker worden verzonden en door het systeem worden doorgestuurd.
* **TopologyOperation** is een bewerking waarmee de grafiek of de meta gegevens van de grafiek worden gewijzigd. Een voor beeld is het toevoegen of verwijderen van een entiteit, zoals een spatie.
* **SpaceChange** is een wijziging in de berekende waarde van een Space die het resultaat is van een telemetrie-bericht van een apparaat.
* **SensorChange** is een wijziging in de berekende waarde van een sensor die het resultaat is van een telemetrie van een apparaat.
* **UdfCustom** is een aangepaste melding van een door de gebruiker gedefinieerde functie.

> [!IMPORTANT]  
> Niet alle **EndpointTypes** ondersteunen alle **EventTypes**.
> Zie de volgende tabel voor de **EventTypes** die zijn toegestaan voor elke **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Zie [uitgangen en eind punten](how-to-egress-endpoints.md)voor meer informatie over het maken van eind punten en voor beelden van het schema van gebeurtenissen.

## <a name="next-steps"></a>Volgende stappen

- Zie [limieten voor open bare Preview-Service](concepts-service-limits.md)voor meer informatie over de limieten voor Azure Digital apparaatdubbels preview.

- Als u een Azure Digital Apparaatdubbels-voor beeld wilt uitproberen, raadpleegt [u de Quick Start om beschik bare kamers te vinden](quickstart-view-occupancy-dotnet.md).