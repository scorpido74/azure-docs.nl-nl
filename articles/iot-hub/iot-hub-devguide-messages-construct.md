---
title: Informatie over de berichtindeling van Azure IoT Hub | Microsoft Documenten
description: Ontwikkelaarshandleiding - beschrijft de indeling en verwachte inhoud van IoT Hub-berichten.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: asrastog
ms.openlocfilehash: 28537ac2389fbb1ca43ca4014515564bddeba4ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69872490"
---
# <a name="create-and-read-iot-hub-messages"></a>IoT Hub-berichten maken en lezen

Om naadloze interoperabiliteit tussen protocollen te ondersteunen, definieert IoT Hub een gemeenschappelijke berichtindeling voor alle apparaatgerichte protocollen. Deze berichtindeling wordt gebruikt voor zowel [device-to-cloud routing](iot-hub-devguide-messages-d2c.md) als [cloud-to-device](iot-hub-devguide-messages-c2d.md) berichten. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub implementeert device-to-cloud messaging met behulp van een streaming messaging patroon. De device-to-cloud-berichten van IoT Hub lijken meer op [gebeurtenissen in event hubs](/azure/event-hubs/) *dan* op Service [Service Bus](/azure/service-bus-messaging/) *Bus-berichten,* omdat er een groot aantal gebeurtenissen door de service gaat die door meerdere lezers kunnen worden gelezen.

Een IoT Hub-bericht bestaat uit:

* Een vooraf bepaalde set *systeemeigenschappen* zoals hieronder vermeld.

* Een set *toepassingseigenschappen*. Een woordenboek met tekenreekseigenschappen die de toepassing kan definiëren en openen, zonder dat de berichttekst hoeft te worden gedeserialiseren. IoT Hub wijzigt deze eigenschappen nooit.

* Een ondoorzichtig binair lichaam.

Eigenschapnamen en waarden kunnen alleen Alfanumerieke tekens ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` van ASCII bevatten, plus wanneer u apparaat-naar-cloudberichten verzendt met behulp van het HTTPS-protocol of berichten van cloud naar apparaat verzendt.

Device-to-cloud messaging met IoT Hub heeft de volgende kenmerken:

* Device-to-cloud berichten zijn duurzaam en bewaard in het standaard eindpunt van een IoT-hub voor **berichten/gebeurtenissen** gedurende maximaal zeven dagen.

* Device-to-cloud berichten kunnen maximaal 256 KB zijn en kunnen in batches worden gegroepeerd om verzendingen te optimaliseren. Batches kunnen hoogstens 256 KB zijn.

* IoT Hub staat geen willekeurige partitionering toe. Device-to-cloud berichten worden verdeeld op basis van hun oorspronkelijke **deviceId**.

* Zoals uitgelegd in [Control access to IoT Hub,](iot-hub-devguide-security.md)maakt IoT Hub verificatie per apparaat en toegangscontrole mogelijk.

* U berichten stempelen met informatie die in de toepassingseigenschappen gaat. Zie voor meer informatie [de verrijkingen van berichten](iot-hub-message-enrichments-overview.md).

Zie [Azure IoT SDKs](iot-hub-devguide-sdks.md)voor meer informatie over het coderen en decoderen van berichten die met verschillende protocollen worden verzonden.

## <a name="system-properties-of-d2c-iot-hub-messages"></a>Systeemeigenschappen van **D2C** IoT Hub-berichten

| Eigenschap | Beschrijving  |User Settable?|Trefwoord voor </br>routeringsquery|
| --- | --- | --- | --- |
| bericht-id |Een door de gebruiker ingestelde id voor het bericht dat wordt gebruikt voor antwoordpatronen. Indeling: een hoofdlettergevoelige tekenreeks (tot 128 tekens lang) met alfanumerieke `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`tekens van ASCII 7 - + .  | Ja | messageId |
| iothub-enqueuedtime |Datum en tijd het [Device-to-Cloud-bericht](iot-hub-devguide-d2c-guidance.md) is ontvangen door IoT Hub. | Nee | wachtrijtijd |
| user-id |Een ID die wordt gebruikt om de oorsprong van berichten op te geven. Wanneer berichten worden gegenereerd door IoT Hub, is deze ingesteld op `{iot hub name}`. | Ja | userId |
| iothub-verbinding-apparaat-id |Een ID die door IoT Hub is ingesteld op device-to-cloud-berichten. Het bevat de **deviceId** van het apparaat dat het bericht heeft verzonden. | Nee | verbindingDeviceId |
| iothub-connection-module-id |Een ID die door IoT Hub is ingesteld op device-to-cloud-berichten. Het bevat de **moduleId** van het apparaat dat het bericht verzonden. | Nee | connectionModuleId |
| iothub-connection-auth-generation-id |Een ID die door IoT Hub is ingesteld op device-to-cloud-berichten. Het bevat de **verbindingDeviceGenerationId** (volgens [de identiteit van het apparaat)](iot-hub-devguide-identity-registry.md#device-identity-properties)van het apparaat dat het bericht heeft verzonden. | Nee |verbindingDeviceGenerationId |
| iothub-connection-auth-methode |Een verificatiemethode die door IoT Hub is ingesteld op device-to-cloud-berichten. Deze eigenschap bevat informatie over de verificatiemethode die wordt gebruikt om het apparaat te verifiëren dat het bericht verzendt.| Nee | verbindingAuthMethod |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>Systeemeigenschappen van **C2D** IoT Hub-berichten

| Eigenschap | Beschrijving  |User Settable?|
| --- | --- | --- |
| bericht-id |Een door de gebruiker ingestelde id voor het bericht dat wordt gebruikt voor antwoordpatronen. Indeling: een hoofdlettergevoelige tekenreeks (tot 128 tekens lang) met alfanumerieke `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`tekens van ASCII 7 - + .  |Ja|
| volgnummer |Een nummer (uniek per apparaatwachtrij) dat door IoT Hub is toegewezen aan elk cloud-to-device-bericht. |Nee|
| tot |Een bestemming die is opgegeven in [berichten van cloud naar apparaat.](iot-hub-devguide-c2d-guidance.md) |Nee|
| absolute-expiry-time |Datum en tijd van het verstrijken van het bericht. |Nee|   |
| correlation-id |Een tekenreekseigenschap in een antwoordbericht dat doorgaans de MessageId van de aanvraag bevat, in antwoordpatronen. |Ja|
| user-id |Een ID die wordt gebruikt om de oorsprong van berichten op te geven. Wanneer berichten worden gegenereerd door IoT Hub, is deze ingesteld op `{iot hub name}`. |Ja|
| iothub-ack |Een feedback bericht generator. Deze eigenschap wordt gebruikt in berichten van cloud tot apparaat om IoT Hub te vragen om feedbackberichten te genereren als gevolg van het verbruik van het bericht door het apparaat. Mogelijke waarden: **geen** (standaard): er wordt geen feedbackbericht gegenereerd, **positief:** ontvang een feedbackbericht als het bericht is voltooid, **negatief:** ontvang een feedbackbericht als het bericht is verlopen (of het maximale aantal levering is bereikt) zonder dat het apparaat is voltooid, of **volledig:** zowel positief als negatief. |Ja|

## <a name="message-size"></a>Berichtgrootte

IoT Hub meet de grootte van berichten op een protocol-agnostische manier, rekening houdend met alleen de werkelijke payload. De grootte in bytes wordt berekend als de som van de volgende waarden:

* De lichaamsgrootte in bytes.
* De grootte in bytes van alle waarden van de eigenschappen van het berichtensysteem.
* De grootte in bytes van alle namen en waarden van gebruikerseigenschap.

Eigenschapnamen en waarden zijn beperkt tot ASCII-tekens, zodat de lengte van de tekenreeksen gelijk is aan de grootte in bytes.

## <a name="anti-spoofing-properties"></a>Anti-spoofing-eigenschappen

Om apparaatspoofing in device-to-cloud-berichten te voorkomen, stempelt IoT Hub alle berichten met de volgende eigenschappen:

* **iothub-verbinding-apparaat-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-methode**

De eerste twee bevatten de **deviceId** en **generationId** van het oorspronkelijke apparaat, volgens [de identiteitvan het apparaat eigenschappen](iot-hub-devguide-identity-registry.md#device-identity-properties).

De eigenschap **iothub-connection-auth-method** bevat een JSON-geserialiseerd object met de volgende eigenschappen:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [IoT Hub-quota en beperking voor](iot-hub-devguide-quotas-throttling.md)informatie over de groottelimieten van berichten in IoT Hub.

* Zie De [Quickstarts](quickstart-send-telemetry-node.md)voor meer informatie over het maken en lezen van IoT Hub-berichten in verschillende programmeertalen.
