---
title: Informatie over de Azure IoT Hub-bericht indeling | Microsoft Docs
description: 'Ontwikkelaars handleiding: beschrijft de indeling en de verwachte inhoud van IoT Hub berichten.'
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: asrastog
ms.openlocfilehash: 28537ac2389fbb1ca43ca4014515564bddeba4ce
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872490"
---
# <a name="create-and-read-iot-hub-messages"></a>IoT Hub-berichten maken en lezen

IoT Hub definieert een gemeen schappelijke bericht indeling voor alle protocollen op het apparaat om een naadloze samen werking tussen protocollen te ondersteunen. Deze bericht indeling wordt gebruikt voor zowel [apparaat-naar-Cloud routering](iot-hub-devguide-messages-d2c.md) als [Cloud-naar-apparaat](iot-hub-devguide-messages-c2d.md) -berichten. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub implementeert apparaat-naar-Cloud-berichten met behulp van een streaming-berichten patroon. IoT Hub apparaat-naar-Cloud-berichten zijn meer soort gelijke [Event hubs](/azure/event-hubs/) *gebeurtenissen* dan [Service Bus](/azure/service-bus-messaging/) *berichten* dat er een groot aantal gebeurtenissen door de service wordt door gegeven die door meerdere lezers kunnen worden gelezen.

Een IoT Hub bericht bestaat uit:

* Een vooraf vastgestelde set *systeem eigenschappen* , zoals hieronder wordt weer gegeven.

* Een set eigenschappen van de *toepassing*. Een woorden lijst met teken reeks eigenschappen die de toepassing kan definiëren en openen, zonder dat de hoofd tekst van het bericht hoeft te worden gedeserialiseerd. Met IoT Hub worden deze eigenschappen nooit gewijzigd.

* Een ondoorzichtige binaire hoofd tekst.

Eigenschaps namen en-waarden mogen alleen ASCII-alfanumerieke ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` tekens bevatten, plus wanneer u apparaat-naar-Cloud-berichten verzendt met het HTTPS-protocol of Cloud-naar-apparaat-berichten verzendt.

Het verzenden van apparaat-naar-Cloud-berichten met IoT Hub heeft de volgende kenmerken:

* Apparaat-naar-Cloud-berichten zijn duurzaam en blijven bewaard in het standaard eindpunt voor **berichten/gebeurtenissen** van een IOT-hub tot zeven dagen.

* Apparaat-naar-Cloud-berichten kunnen Maxi maal 256 KB groot zijn en kunnen worden gegroepeerd in batches om het verzenden te optimaliseren. Batches kunnen Maxi maal 256 KB groot zijn.

* Voor IoT Hub is geen wille keurige partitionering toegestaan. Apparaat-naar-Cloud-berichten worden gepartitioneerd op basis van hun oorspronkelijke **deviceId**.

* Zoals wordt beschreven in [toegang tot IOT hub beheren](iot-hub-devguide-security.md), wordt met behulp van IOT hub verificatie per apparaat en toegangs beheer ingeschakeld.

* U kunt berichten stem pelen met informatie die in de toepassings eigenschappen gaat. Zie voor meer informatie [bericht](iot-hub-message-enrichments-overview.md)verrijkingen.

Zie [Azure IOT sdk's](iot-hub-devguide-sdks.md)(Engelstalig) voor meer informatie over het coderen en decoderen van berichten die zijn verzonden met behulp van verschillende protocollen.

## <a name="system-properties-of-d2c-iot-hub-messages"></a>Systeem eigenschappen van **D2C** IOT hub berichten

| Eigenschap | Description  |Gebruiker instelbaar?|Tref woord voor </br>routerings query|
| --- | --- | --- | --- |
| message-id |Een door de gebruiker instel bare id voor het bericht dat wordt gebruikt voor de aanvraag/antwoord patronen. Indeling: Een hoofdletter gevoelige teken reeks (Maxi maal 128 tekens lang) van ASCII 7-bits alfanumerieke tekens `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`+.  | Ja | messageId |
| iothub-enqueuedtime |De datum en tijd waarop het [apparaat-naar-Cloud-](iot-hub-devguide-d2c-guidance.md) bericht is ontvangen door IOT hub. | Nee | enqueuedTime |
| user-id |Een ID die wordt gebruikt om de oorsprong van berichten op te geven. Wanneer berichten door IoT Hub worden gegenereerd, wordt deze ingesteld op `{iot hub name}`. | Ja | userId |
| iothub-verbinding-apparaat-id |Een ID die is ingesteld door IoT Hub op apparaat-naar-Cloud-berichten. Deze bevat de **deviceId** van het apparaat dat het bericht heeft verzonden. | Nee | connectionDeviceId |
| iothub-verbinding-module-id |Een ID die is ingesteld door IoT Hub op apparaat-naar-Cloud-berichten. Het bevat de **moduleId** van het apparaat dat het bericht heeft verzonden. | Nee | connectionModuleId |
| iothub-verbinding-generatie-id |Een ID die is ingesteld door IoT Hub op apparaat-naar-Cloud-berichten. Het bevat de **connectionDeviceGenerationId** (per [apparaat-id-eigenschappen](iot-hub-devguide-identity-registry.md#device-identity-properties)) van het apparaat dat het bericht heeft verzonden. | Nee |connectionDeviceGenerationId |
| iothub-Connection-auth-methode |Een verificatie methode die is ingesteld door IoT Hub op apparaat-naar-Cloud-berichten. Deze eigenschap bevat informatie over de verificatie methode die wordt gebruikt om het apparaat te verifiëren dat het bericht verzendt.| Nee | connectionAuthMethod |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>Systeem eigenschappen van **C2D** IOT hub berichten

| Eigenschap | Description  |Gebruiker instelbaar?|
| --- | --- | --- |
| message-id |Een door de gebruiker instel bare id voor het bericht dat wordt gebruikt voor de aanvraag/antwoord patronen. Indeling: Een hoofdletter gevoelige teken reeks (Maxi maal 128 tekens lang) van ASCII 7-bits alfanumerieke tekens `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`+.  |Ja|
| Volg nummer |Een getal (uniek per apparaat wachtrij) toegewezen door IoT Hub aan elk bericht van de Cloud naar het apparaat. |Nee|
| to |Een doel dat is opgegeven in [Cloud-naar-apparaat-](iot-hub-devguide-c2d-guidance.md) berichten. |Nee|
| absolute-expiry-time |De datum en tijd waarop het bericht is verlopen. |Nee|   |
| correlatie-id |Een teken reeks eigenschap in een antwoord bericht dat normaal gesp roken de MessageId van de aanvraag bevat, in antwoord patronen voor aanvragen. |Ja|
| user-id |Een ID die wordt gebruikt om de oorsprong van berichten op te geven. Wanneer berichten door IoT Hub worden gegenereerd, wordt deze ingesteld op `{iot hub name}`. |Ja|
| iothub-ACK |Een feedback bericht generator. Deze eigenschap wordt gebruikt in Cloud-naar-apparaat-berichten om IoT Hub te vragen om feedback berichten te genereren als gevolg van het verbruik van het bericht door het apparaat. Mogelijke waarden: **geen** (standaard): er wordt geen feedback bericht gegenereerd, **positief**: er wordt een feedback bericht ontvangen als het bericht is voltooid, **negatief**: Ontvang een feedback bericht als het bericht is verlopen (of het maximum aantal leveringen is bereikt) zonder dat het apparaat is voltooid, of **volledig**: zowel positief als negatief. |Ja|

## <a name="message-size"></a>Berichtgrootte

IoT Hub de grootte van het bericht in een protocol-neutraal manier meet, waarbij alleen de daad werkelijke lading wordt overwogen. De grootte in bytes wordt berekend als de som van de volgende waarden:

* De grootte van de hoofd tekst in bytes.
* De grootte in bytes van alle waarden van de eigenschappen van het bericht systeem.
* De grootte in bytes van alle namen en waarden van gebruikers eigenschappen.

Eigenschaps namen en-waarden zijn beperkt tot ASCII-tekens, waardoor de lengte van de teken reeksen gelijk is aan de grootte in bytes.

## <a name="anti-spoofing-properties"></a>Anti-spoofing-eigenschappen

Als u wilt voor komen dat apparaat-to-Cloud-berichten worden vervalst, IoT Hub stemt u alle berichten af met de volgende eigenschappen:

* **iothub-connection-device-id**
* **iothub-connection-auth-generation-id**
* **iothub-Connection-auth-methode**

De eerste twee bevatten de **deviceId** en **generationId** van het oorspronkelijke apparaat, zoals de eigenschappen van de [apparaat-id](iot-hub-devguide-identity-registry.md#device-identity-properties).

De eigenschap **iothub-Connection-Authentication-Method** bevat een JSON-geserialiseerd object met de volgende eigenschappen:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Volgende stappen

* [IOT hub Zie quota's en beperken](iot-hub-devguide-quotas-throttling.md)voor meer informatie over de limieten voor de bericht grootte in IOT hub.

* Meer informatie over het maken en lezen van IoT Hub berichten in verschillende programmeer talen vindt u in de [Quick](quickstart-send-telemetry-node.md)starts.
