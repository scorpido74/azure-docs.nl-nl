---
title: Problemen met Azure IoT-bericht routering oplossen
description: Probleem oplossing voor Azure IoT-bericht routering uitvoeren
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: asrastog
ms.openlocfilehash: 871a4c7d99fc44cf9868f19e41560e6e7a2e22f1
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84793322"
---
# <a name="troubleshooting-message-routing"></a>Problemen met bericht routering oplossen

Dit artikel bevat informatie over het controleren en oplossen van problemen met veelvoorkomende problemen en oplossingen voor de [route ring](iot-hub-devguide-messages-d2c.md)van IOT hub berichten. 

## <a name="monitoring-message-routing"></a>Bericht routering bewaken

Met [IOT hub metrische gegevens](iot-hub-metrics.md) worden alle metrische gegevens weer gegeven die standaard zijn ingeschakeld voor uw IOT hub. U wordt aangeraden metrische gegevens met betrekking tot bericht Routering en eind punten te controleren om een overzicht te krijgen van de verzonden berichten. Schakel ook [Diagnostische logboeken](iot-hub-monitor-resource-health.md) in azure monitor Diagnostische instellingen in om bewerkingen voor **routes**bij te houden. Deze Diagnostische logboeken kunnen worden verzonden naar Azure Monitor-logboeken, Event Hubs of Azure Storage voor aangepaste verwerking. Meer informatie over het [instellen en gebruiken van metrische gegevens en Diagnostische logboeken met een IOT hub](tutorial-use-metrics-and-diags.md).

We raden u ook aan de [terugval route](iot-hub-devguide-messages-d2c.md#fallback-route) in te scha kelen als u berichten wilt behouden die niet overeenkomen met de query op een van de routes. Deze kunnen worden bewaard in het [ingebouwde eind punt](iot-hub-devguide-messages-read-builtin.md) voor de hoeveelheid Bewaar dagen die zijn geconfigureerd. 

## <a name="top-issues"></a>Belangrijkste problemen

Hieronder vindt u de meest voorkomende problemen met de route ring van berichten. Klik op het probleem voor gedetailleerde stappen om te beginnen met het oplossen van problemen.

* [Berichten van mijn apparaten worden niet naar verwachting gerouteerd](#messages-from-my-devices-are-not-being-routed-as-expected)
* [Ik heb plotseling gestopt met het ontvangen van berichten bij het ingebouwde Event Hubs-eind punt](#i-suddenly-stopped-getting-messages-at-the-built-in-endpoint)

### <a name="messages-from-my-devices-are-not-being-routed-as-expected"></a>Berichten van mijn apparaten worden niet naar verwachting gerouteerd

Analyseer het volgende om dit probleem op te lossen.

#### <a name="the-routing-metrics-for-this-endpoint"></a>De metrische gegevens van de route ring voor dit eind punt
Alle [IOT hub metrische gegevens](iot-hub-devguide-endpoints.md) met betrekking tot route ring worden voorafgegaan door *route ring*. U kunt informatie uit meerdere metrische gegevens combi neren om de hoofd oorzaak van problemen vast te stellen. Gebruik bijvoorbeeld metrische **route ring bezorgings pogingen** om het aantal berichten te identificeren dat aan een eind punt is geleverd of verwijderd wanneer de query's niet overeenkomen met een van de routes en de terugval route zijn uitgeschakeld. Controleer de metrische gegevens van het **routerings latentie** om na te gaan of de latentie voor de levering van berichten constant is of wordt verg root. Een groeiende latentie kan duiden op een probleem met een bepaald eind punt en het wordt aangeraden om [de status van het eind punt](#the-health-of-the-endpoint)te controleren. Deze routerings gegevens hebben ook [dimensies](iot-hub-metrics.md#dimensions) die details geven over de metriek, zoals het eindpunt type, de specifieke naam van het eind punt en een reden waarom het bericht niet is bezorgd.

#### <a name="the-diagnostic-logs-for-any-operational-issues"></a>De diagnostische logboeken voor operationele problemen 
Bekijk de **routes** [Diagnostische logboeken](iot-hub-monitor-resource-health.md#routes) voor meer informatie over de route ring en eindpunt [bewerkingen](#operation-names) , of Identificeer fouten en relevante [fout code](#common-error-codes) om het probleem verder te begrijpen. De bewerkings naam **RouteEvaluationError** in het logboek geeft bijvoorbeeld aan dat de route niet kan worden geëvalueerd vanwege een probleem met de bericht indeling. Gebruik de tips voor de namen van de specifieke [bewerkingen](#operation-names) om het probleem te verhelpen. Wanneer een gebeurtenis wordt geregistreerd als een fout, bevat het logboek ook meer informatie over waarom de evaluatie is mislukt. Als de naam van de bewerking bijvoorbeeld **EndpointUnhealthy**is, geeft de [fout codes](#common-error-codes) 403004 aan dat er onvoldoende ruimte is op het eind punt.

#### <a name="the-health-of-the-endpoint"></a>De status van het eind punt
Gebruik de REST API [eind punt status ophalen](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) om de [status van de eind punten op te](iot-hub-devguide-endpoints.md#custom-endpoints) halen. De API *Get endpoint Health* bevat ook informatie over de laatste keer dat een bericht is verzonden naar het eind punt, de [laatste bekende fout](#last-known-errors-for-iot-hub-routing-endpoints), de laatste bekende fout tijd en de laatste keer dat een verzend poging is gedaan voor dit eind punt. Gebruik de mogelijke beperking die is opgegeven voor de specifieke [Laatst bekende fout](#last-known-errors-for-iot-hub-routing-endpoints).

### <a name="i-suddenly-stopped-getting-messages-at-the-built-in-endpoint"></a>Ik heb plotseling gestopt met het ophalen van berichten op het ingebouwde eind punt

Analyseer het volgende om dit probleem op te lossen.

#### <a name="was-a-new-route-created"></a>Is er een nieuwe route gemaakt?
Zodra een route is gemaakt, stopt de gegevens stroom naar het ingebouwde eind punt, tenzij er een route naar dat eind punt wordt gemaakt. Om ervoor te zorgen dat berichten blijven stromen naar het ingebouwde eind punt als er een nieuwe route wordt toegevoegd, configureert u een route naar het eind punt *gebeurtenissen* . 

#### <a name="was-the-fallback-route-disabled"></a>Is de terugval route uitgeschakeld?
De terugval route verzendt alle berichten die niet voldoen aan de query voorwaarden voor een van de bestaande routes naar de [ingebouwde Event hubs](iot-hub-devguide-messages-read-builtin.md) (berichten/gebeurtenissen) die compatibel zijn met [Event hubs](https://docs.microsoft.com/azure/event-hubs/). Als bericht routering is ingeschakeld, kunt u de mogelijkheid om de terugval route te gebruiken in te scha kelen. Als er geen routes naar het ingebouwde eind punt en een terugval route zijn ingeschakeld, worden alleen berichten die niet overeenkomen met de query voorwaarden op routes, verzonden naar het ingebouwde eind punt. Als alle bestaande routes worden verwijderd, moet er ook een terugval route worden ingeschakeld om alle gegevens bij het ingebouwde eind punt te ontvangen.

U kunt de terugval route in-of uitschakelen op de Blade voor de Azure Portal->bericht routering. U kunt Azure Resource Manager ook gebruiken voor [FallbackRouteProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) om een aangepast eind punt voor terugval route te gebruiken.

## <a name="last-known-errors-for-iot-hub-routing-endpoints"></a>Laatste bekende fouten voor IoT Hub Routing-eind punten

<a id="last-known-errors"></a>
[!INCLUDE [iot-hub-include-last-known-errors](../../includes/iot-hub-include-last-known-errors.md)]

## <a name="routes-diagnostic-logs"></a>Routes Diagnostische logboeken

Hieronder ziet u de namen van de bewerkingen en de fout codes die in de [Diagnostische logboeken](iot-hub-monitor-resource-health.md#routes)zijn vastgelegd.

<a id="diagnostics-operation-names"></a>
### <a name="operation-names"></a>Bewerkings namen

[!INCLUDE [iot-hub-diagnostics-operation-names](../../includes/iot-hub-diagnostics-operation-names.md)]

<a id="diagnostics-error-codes"></a>
### <a name="common-error-codes"></a>Veelvoorkomende foutcodes

[!INCLUDE [iot-hub-diagnostics-error-codes](../../includes/iot-hub-diagnostics-error-codes.md)]

## <a name="next-steps"></a>Volgende stappen

Als u meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
