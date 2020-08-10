---
title: Integratie met andere services
titleSuffix: Azure Digital Twins
description: Meer informatie over de ingangs-en uitgangs vereisten bij het implementeren van Azure Digital Apparaatdubbels.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ca500401a6bff8a00dd9c51eecb29aa93fdbc82b
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042646"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Azure Digital Apparaatdubbels integreren met andere services

Azure Digital Apparaatdubbels wordt doorgaans samen met andere services gebruikt. Met behulp van [**gebeurtenis routes**](concepts-route-events.md)ontvangen Azure Digital apparaatdubbels gegevens van upstream-Services, zoals [IOT hub](../iot-hub/about-iot-hub.md), die worden gebruikt voor het leveren van telemetriegegevens en meldingen. 

Azure Digital Apparaatdubbels kan ook gegevens naar downstream-Services routeren, zoals Azure Maps ([*How to: use Azure Digital apparaatdubbels to update a Azure Mapsing in een binnenshuis*](how-to-integrate-maps.md)) en time series Insights ([*How to: Integration with time series Insights*](how-to-integrate-time-series-insights.md)), voor opslag, werk stroom integratie, analyses en meer. 

## <a name="data-ingress"></a>Gegevens binnenkomend

Azure Digital Apparaatdubbels kan worden aangedreven met gegevens en gebeurtenissen van elke service: IoT Hub, Logic Apps, uw eigen aangepaste service, enzovoort. Zo kunt u telemetrie van fysieke apparaten in uw omgeving verzamelen en deze gegevens verwerken met behulp van de Azure Digital Apparaatdubbels-grafiek in de Cloud.

Azure Digital Apparaatdubbels heeft geen ingebouwde IoT Hub. U kunt een bestaande IoT Hub gebruiken die momenteel in productie is of een nieuwe implementeren. Hiermee hebt u volledige toegang tot alle functies van het beheer van het apparaat van IoT Hub.

Gebruik een [Azure-functie](../azure-functions/functions-overview.md)om gegevens uit een wille keurige bron op te nemen in azure Digital apparaatdubbels. Meer informatie over dit patroon vindt [*u in procedures: opname telemetrie van IOT hub*](how-to-ingest-iot-hub-data.md)of probeer het zelf in de Azure Digital Apparaatdubbels [*zelf studie: verbinding maken met een end-to-end oplossing*](tutorial-end-to-end.md).

## <a name="data-egress-services"></a>Services voor gegevens uitgaand verkeer

Azure Digital Apparaatdubbels kan gegevens verzenden naar verbonden **eind punten**. Ondersteunde eind punten kunnen zijn:
* [Event Hub](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Eind punten worden aan Azure Digital Apparaatdubbels gekoppeld met behulp van beheer-Api's of de Azure Portal. Meer informatie over het koppelen van een eind punt aan Azure Digital Apparaatdubbels in [*procedures: eind punten en routes beheren*](how-to-manage-routes-apis-cli.md).

Er zijn veel andere services waar u uw gegevens uiteindelijk wilt door sturen, zoals [Azure Storage](../storage/common/storage-introduction.md) of [Time Series Insights](../time-series-insights/time-series-insights-update-overview.md). Als u uw gegevens naar Services wilt verzenden, koppelt u de doel service aan een eind punt.

Als u bijvoorbeeld ook [Azure Maps](../azure-maps/about-azure-maps.md) gebruikt en de locatie wilt correleren met uw apparaatdubbels [dubbele grafiek](concepts-twins-graph.md)van Azure, kunt u Azure functions met Event grid gebruiken om communicatie tussen alle services in uw implementatie tot stand te brengen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over eind punten en routerings gebeurtenissen naar externe services:
* [*Concepten: Azure Digital Apparaatdubbels-gebeurtenissen routeren*](concepts-route-events.md)

Zie Azure Digital Apparaatdubbels instellen om gegevens op te nemen uit IoT Hub:
* [*Instructies: telemetrie opnemen van IoT Hub*](how-to-ingest-iot-hub-data.md)
