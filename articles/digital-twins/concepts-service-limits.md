---
title: Limieten voor open bare Preview-Service-Azure Digital Apparaatdubbels | Microsoft Docs
description: Meer informatie over de open bare Preview-Service, het abonnement, het exemplaar en de frequentie limieten voor Azure Digital Apparaatdubbels.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: f54311af65d9678b2a51b23a38bab66111a818ca
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383076"
---
# <a name="public-preview-service-limits"></a>Servicebeperkingen van de openbare preview

Tijdens de open bare preview heeft Azure Digital Apparaatdubbels het volgende tijdelijke abonnement, exemplaar en frequentie limieten.

Deze beperkingen bestaan om het leren van de nieuwe service en de vele functies te vereenvoudigen.

> [!NOTE]
> Deze limieten worden verhoogd of verwijderd door algemene Beschik baarheid (GA).

## <a name="per-subscription-limits"></a>Limieten per abonnement

Tijdens de open bare preview-periode kan elk Azure-abonnement slechts één Azure Digital Apparaatdubbels-exemplaar tegelijk maken of uitvoeren.

> [!TIP]
> Als u uw exemplaar verwijdert, kunt u een nieuwe maken.

## <a name="per-instance-limits"></a>Limieten per exemplaar

Elk Azure Digital Apparaatdubbels-exemplaar kan op zijn beurt het volgende hebben:

- Precies één Inge sloten **IoTHub** -resource die automatisch wordt gemaakt tijdens het inrichten van de service.
- Precies één **EventHub** -eind punt voor het gebeurtenis type **DeviceMessage**.
- Maxi maal drie **EventHub**-, **ServiceBus**-of **EventGrid** -eind punten van het gebeurtenis type **SensorChange**, **SpaceChange**, **TopologyOperation**of **UdfCustom**.

> [!NOTE]
> Sommige para meters die meestal worden gedefinieerd in het maken van de hierboven genoemde Azure IoT-entiteiten, zijn niet vereist tijdens de open bare preview.
> - Raadpleeg de [Swagger-referentie documentatie](./how-to-use-swagger.md) voor de meest recente API-specificaties.

## <a name="azure-digital-twins-management-api-limits"></a>Azure Digital Apparaatdubbels Management API-limieten

De limieten voor de aanvraag frequentie voor uw Azure Digital Apparaatdubbels Management API zijn:

- 100 aanvragen per seconde voor de Azure Digital Apparaatdubbels Management-API.
- Maxi maal 1.000 objecten die door één Azure Digital Apparaatdubbels Management API-query worden geretourneerd.

> [!IMPORTANT]
> Als u de limiet van 1.000 objecten overschrijdt, wordt er een fout melding weer gegeven en moet uw query worden vereenvoudigd.

## <a name="user-defined-functions-rate-limits"></a>Frequentie limieten voor door de gebruiker gedefinieerde functies

Met de volgende limieten stelt u het totale aantal door de gebruiker gedefinieerde functie aanroepen in voor uw Azure Digital Apparaatdubbels-exemplaar:

- 400-client bibliotheek aanroepen per seconde
- 100 **SendNotification** -aanroepen per seconde

> [!NOTE]
> De volgende acties kunnen ertoe leiden dat extra frequentie limieten tijdelijk worden toegepast:
> - Wijzigingen aangebracht in de meta gegevens van het topologie object
> - Updates die zijn aangebracht in de door de gebruiker gedefinieerde functie definitie
> - Apparaten die de voor de eerste keer telemetrie verzenden

## <a name="device-telemetry-limits"></a>Limieten voor telemetrie van apparaat

De volgende limieten zijn het totaal aantal berichten dat uw apparaten kunnen verzenden naar uw Azure Digital Apparaatdubbels-exemplaar:

- 100 berichten per seconde op alle apparaten
-   25 berichten per seconde per apparaat

## <a name="next-steps"></a>Volgende stappen

- Als u een Azure Digital Apparaatdubbels-voor beeld wilt uitproberen, gaat u naar [Quick Start om beschik bare kamers te vinden](./quickstart-view-occupancy-dotnet.md).
