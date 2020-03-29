---
title: Handleiding voor ontwikkelaars voor Azure IoT Hub | Microsoft Documenten
description: De Azure IoT Hub-ontwikkelaarshandleiding bevat discussies over eindpunten, beveiliging, het identiteitsregister, apparaatbeheer, directe methoden, apparaattweelingen, bestandsuploads, taken, de IoT Hub-querytaal en messaging.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 1ff7d430edd3f638ad5efcc5a89604e4ed732211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60400147"
---
# <a name="azure-iot-hub-developer-guide"></a>Azure IoT Hub-ontwikkelaarshandleiding

Azure IoT Hub is een volledig beheerde service die betrouwbare en veilige bidirectionele communicatie tussen miljoenen apparaten en een back-end van een oplossing mogelijk maakt.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT Hub biedt u:

* Beveilig communicatie met behulp van beveiligingsreferenties per apparaat en toegangscontrole.

* Meerdere communicatieopties tussen apparaat en cloud en cloud tot apparaat.

* Opvraagbare opslag van statusgegevens per apparaat en metagegevens.

* Eenvoudige apparaatconnectiviteit met apparaatbibliotheken voor de populairste talen en platforms.

Deze IoT Hub-ontwikkelaarshandleiding bevat de volgende artikelen:

* [Met de communicatierichtlijnen voor apparaat tot cloud](iot-hub-devguide-d2c-guidance.md) u kiezen tussen device-to-cloud-berichten, de gerapporteerde eigenschappen van device twin en het uploaden van bestanden.

* [Communicatiebegeleiding tussen cloud-to-device](iot-hub-devguide-c2d-guidance.md) helpt u bij het kiezen tussen directe methoden, de gewenste eigenschappen van device twin en cloud-to-device-berichten.

* [Device-to-cloud en cloud-to-device messaging met IoT Hub](iot-hub-devguide-messaging.md) beschrijft de messaging-functies (device-to-cloud en cloud-to-device) die IoT Hub blootlegt.

  * [Stuur device-to-cloud berichten naar IoT Hub.](iot-hub-devguide-messages-d2c.md)

  * [Lees device-to-cloud berichten van het ingebouwde eindpunt.](iot-hub-devguide-messages-read-builtin.md)

  * [Gebruik aangepaste eindpunten en routeringsregels voor device-to-cloud-berichten.](iot-hub-devguide-messages-read-custom.md)

  * [Cloud-to-device-berichten verzenden vanuit IoT Hub.](iot-hub-devguide-messages-c2d.md)

  * [IoT Hub-berichten maken en lezen](iot-hub-devguide-messages-construct.md).

* [Als u bestanden uploadt vanaf een apparaat,](iot-hub-devguide-file-upload.md) wordt beschreven hoe u bestanden vanaf een apparaat uploaden. Het artikel bevat ook informatie over onderwerpen zoals de meldingen die het uploadproces kan verzenden.

* [Apparaatidentiteiten beheren in IoT Hub](iot-hub-devguide-identity-registry.md)beschrijft welke informatie de identiteitsregister van elke IoT-hub opslaat. Het artikel beschrijft ook hoe u het openen en wijzigen.

* [Controletoegang tot IoT Hub](iot-hub-devguide-security.md) beschrijft het beveiligingsmodel dat wordt gebruikt om toegang te verlenen tot IoT Hub-functionaliteit voor zowel apparaten als cloudcomponenten. Het artikel bevat informatie over het gebruik van tokens en X.509-certificaten en details van de machtigingen die u verlenen.

* [Gebruik apparaattweelingen om de status en configuraties te synchroniseren](iot-hub-devguide-device-twins.md) en beschrijft het concept van de *apparaattweeling.* Het artikel beschrijft ook de functionaliteit apparaat tweelingen bloot, zoals het synchroniseren van een apparaat met zijn apparaat twin. Het artikel bevat informatie over de gegevens die zijn opgeslagen in een apparaattweeling.

* [Een directe methode op een apparaat aanroepen,](iot-hub-devguide-direct-methods.md) beschrijft de levenscyclus van een directe methode. In het artikel wordt beschreven hoe u methoden op een apparaat aanroept vanuit uw back-end-app en de directe methode op uw apparaat afhandelen.

* [Als u taken op meerdere apparaten plant,](iot-hub-devguide-jobs.md) wordt beschreven hoe u taken op meerdere apparaten plannen. In het artikel wordt beschreven hoe u taken indient die taken uitvoeren als het uitvoeren van een directe methode, waarbij een apparaat wordt bijgewerkt met een apparaattweeling. Het beschrijft ook hoe u de status van een taak opvragen.

* [Referentie - kies een communicatieprotocol](iot-hub-devguide-protocols.md) beschrijft de communicatieprotocollen die IoT Hub ondersteunt voor apparaatcommunicatie en geeft een overzicht van de poorten die moeten worden geopend.

* [Referentie - IoT Hub-eindpunten](iot-hub-devguide-endpoints.md) beschrijven de verschillende eindpunten die elke IoT-hub blootlegt voor runtime- en beheerbewerkingen. In het artikel wordt ook beschreven hoe u extra eindpunten maken in uw IoT-hub en hoe u een veldgateway gebruiken om connectiviteit met uw IoT Hub-eindpunten in niet-standaardscenario's in te schakelen.

* [Referentie - IoT Hub-querytaal voor apparaattweelingen, taken en berichtroutering](iot-hub-devguide-query-language.md) beschrijft die IoT Hub-querytaal waarmee u informatie uit uw hub ophalen over uw apparaattweeling en -taken.

* [Referentie - quota en beperking](iot-hub-devguide-quotas-throttling.md) geven een overzicht van de quota die zijn ingesteld in de IoT Hub-service en de beperking die optreedt wanneer u een quotum overschrijdt.

* [Referentie - prijzen](iot-hub-devguide-pricing.md) bieden algemene informatie over verschillende SKU's en prijzen voor IoT Hub en details over hoe de verschillende IoT Hub-functionaliteiten worden gemeten als berichten door IoT Hub.

* [Referentie - Apparaat- en service-SDK's](iot-hub-devguide-sdks.md) bevat de Azure IoT-SDK's voor het ontwikkelen van apparaat- en service-apps die werken met uw IoT-hub. Het artikel bevat links naar online API-documentatie.

* [Referentie - IoT Hub MQTT-ondersteuning](iot-hub-mqtt-support.md) biedt gedetailleerde informatie over hoe IoT Hub het MQTT-protocol ondersteunt. Het artikel beschrijft de ondersteuning voor het MQTT-protocol dat is ingebouwd in de Azure IoT SDK's en geeft informatie over het rechtstreeks gebruik van het MQTT-protocol.

* [Woordenlijst een](iot-hub-devguide-glossary.md) lijst met algemene IoT Hub-gerelateerde termen.