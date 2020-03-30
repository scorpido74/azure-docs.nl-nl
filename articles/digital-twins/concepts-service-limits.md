---
title: Limieten voor openbare preview-service - Azure Digital Twins | Microsoft Documenten
description: Meer informatie over openbare preview-service, abonnementen, instantie en tarieflimieten voor Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 5e323d8faa19ceb0712aa6183df17740ce2a0a1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370373"
---
# <a name="public-preview-service-limits"></a>Servicebeperkingen van de openbare preview

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Tijdens de openbare preview heeft Azure Digital Twins de volgende tijdelijke abonnements-, instantie- en tarieflimieten voor bestaande klanten. Deze beperkingen bestaan om het leren over de nieuwe service en de vele functies ervan te vereenvoudigen en worden verhoogd of verwijderd door algemene beschikbaarheid (GA).

## <a name="per-subscription-limits"></a>Limieten per abonnement

Tijdens de openbare preview kan elk Azure-abonnement slechts één Azure Digital Twins-exemplaar tegelijk maken of uitvoeren. Als u uw exemplaar verwijdert, u een nieuwe maken.

## <a name="per-instance-limits"></a>Limieten per instantie

Op zijn beurt kan elk Azure Digital Twins-exemplaar het als nodig zijn:

- Precies één ingesloten **IoTHub-bron** die automatisch wordt gemaakt tijdens het inrichten van de service.
- Precies één **EventHub-eindpunt** voor het gebeurtenistype **DeviceMessage**.
- Maximaal drie **EventHub-,** **ServiceBus-** of **EventGrid-eindpunten** van het gebeurtenistype **SensorChange,** **SpaceChange,** **TopologyOperation**of **UdfCustom**.

> [!NOTE]
> Sommige parameters die gewoonlijk zijn gedefinieerd bij het maken van de bovenstaande Azure IoT-entiteiten zijn niet vereist tijdens openbare preview.
> - Raadpleeg de [Swagger referentiedocumentatie](./how-to-use-swagger.md) voor de meest recente API specificaties.

## <a name="azure-digital-twins-management-api-limits"></a>Azure Digital Twins Management API-limieten

De limieten voor het aanvraagtarief voor uw Azure Digital Twins Management API zijn:

- 100 aanvragen per seconde voor de Azure Digital Twins Management API.
- Maximaal 1.000 objecten die worden geretourneerd door één Azure Digital Twins Management API-query.

> [!IMPORTANT]
> Als u de limiet van 1000 objecten overschrijdt, ontvangt u een foutmelding en moet u uw query vereenvoudigen.

## <a name="user-defined-functions-rate-limits"></a>Door de gebruiker gedefinieerde functiestarieflimieten

Met de volgende limieten wordt het totale aantal door de gebruiker gedefinieerde functieoproepen ingesteld voor uw Azure Digital Twins-exemplaar:

- 400 oproepen in de clientbibliotheek per seconde
- 100 **SendNotification-oproepen** per seconde

> [!NOTE]
> De volgende acties kunnen ertoe leiden dat extra tarieflimieten tijdelijk worden toegepast:
> - Bewerkingen die zijn aangebracht aan de metagegevens van het topologieobject
> - Updates die zijn aangebracht in de door de gebruiker gedefinieerde functiedefinitie
> - Apparaten die voor het eerst telemetrie verzenden

## <a name="device-telemetry-limits"></a>Limieten voor apparaattelemetrie

Met de volgende limieten wordt het totale aantal berichten dat uw apparaten naar uw Azure Digital Twins-exemplaar kunnen verzenden, beperkt:

- 100 berichten per seconde op alle apparaten
-    25 berichten per seconde per apparaat

## <a name="next-steps"></a>Volgende stappen

- Als u een voorbeeld van Azure Digital Twins wilt uitproberen, gaat u naar [Snelstart om beschikbare kamers te vinden.](./quickstart-view-occupancy-dotnet.md)
