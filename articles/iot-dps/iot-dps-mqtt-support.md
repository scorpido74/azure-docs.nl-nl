---
title: Informatie over MQTT-ondersteuning voor Azure IoT Device Provisioning Service | Microsoft Documenten
description: Handleiding voor ontwikkelaars - ondersteuning voor apparaten die verbinding maken met het DPS-apparaateindpunt (Azure IoT Device Provisioning Service) met behulp van het MQTT-protocol.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.openlocfilehash: ea6ece7e34ddb9c25f9f8349239ab3a1c3405abf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973370"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Communiceren met uw DPS met het MQTT-protocol

DPS stelt apparaten in staat om te communiceren met het eindpunt van het DPS-apparaat met:

* [MQTT v3.1.1](https://mqtt.org/) op poort 8883
* [MQTT v3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) via WebSocket op poort 443.

DPS is geen full-featured MQTT broker en ondersteunt niet alle gedragingen die zijn gespecificeerd in de MQTT v3.1.1-standaard. In dit artikel wordt beschreven hoe apparaten ondersteund MQTT-gedrag kunnen gebruiken om met DPS te communiceren.

Alle apparaatcommunicatie met DPS moet worden beveiligd met TLS/SSL. Daarom ondersteunt DPS geen niet-beveiligde verbindingen via poort 1883.

 > [!NOTE] 
 > DPS ondersteunt momenteel geen apparaten die [tpm-attestmechanisme](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism) gebruiken via het MQTT-protocol.

## <a name="connecting-to-dps"></a>Verbinding maken met DPS

Een apparaat kan het MQTT-protocol gebruiken om verbinding te maken met een DPS met behulp van een van de volgende opties.

* Bibliotheken in de [Azure IoT Provisioning SDKs](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks).
* Het MQTT protocol direct.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Het MQTT-protocol direct gebruiken (als apparaat)

Als een apparaat de sdk's van het apparaat niet kan gebruiken, kan het nog steeds verbinding maken met de eindpunten van openbare apparaten via het MQTT-protocol op poort 8883. In het **CONNECT-pakket** moet het apparaat de volgende waarden gebruiken:

* Gebruik **registrationId**voor het veld **ClientId** .

* Gebruik **Username** voor het `{idScope}/registrations/{registration_id}/api-version=2019-03-31`veld `{idScope}` Gebruikersnaam de [idScope](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope) van de DPS.

* Gebruik voor het veld **Wachtwoord** een SAS-token. Het formaat van het SAS-token is hetzelfde als voor zowel de HTTPS- als DEAMQP-protocollen:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration`De resourceURI moet in `{idScope}/registrations/{registration_id}`de indeling zijn . De beleidsnaam `registration`zou moeten zijn .

  > [!NOTE]
  > Als u X.509-certificaatverificatie gebruikt, zijn SAS-tokenwachtwoorden niet vereist.

  Zie het gedeelte beveiligingstokens van Beheer toegang tot [DPS voor](how-to-control-access.md#security-tokens)meer informatie over het genereren van SAS-tokens.

Het volgende is een lijst met DPS-implementatiespecifieke gedragingen:

 * DPS ondersteunt niet de functionaliteit van **cleansession-vlag** die wordt ingesteld op **0**.

 * Wanneer een apparaat-app zich abonneert op een onderwerp met **QoS 2,** verleent DPS maximaal QoS-niveau 1 in het **SUBACK-pakket.** Daarna levert DPS berichten aan het apparaat met QoS 1.

## <a name="tlsssl-configuration"></a>TLS/SSL-configuratie

Om het MQTT-protocol rechtstreeks te gebruiken, *moet* uw client verbinding maken via TLS 1.2. Pogingen om deze stap over te slaan mislukken met verbindingsfouten.


## <a name="registering-a-device"></a>Een apparaat registreren

Als u een apparaat wilt registreren via `$dps/registrations/res/#` DPS, moet een apparaat zich abonneren met behulp **van een onderwerpfilter.** De wildcard `#` op meerdere niveaus in het onderwerpfilter wordt alleen gebruikt om het apparaat extra eigenschappen in de onderwerpnaam te laten ontvangen. DPS staat het gebruik `#` van `?` de of wildcards voor het filteren van subonderwerpen niet toe. Aangezien DPS geen pub-sub berichtenmakelaar voor algemeen gebruik is, ondersteunt het alleen de gedocumenteerde onderwerpnamen en onderwerpfilters.

Het apparaat moet een registerbericht `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` naar DPS publiceren met als **onderwerpnaam**. Het laadvermogen moet het object [Apparaatregistratie](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) bevatten in JSON-indeling.
In een succesvol scenario ontvangt het apparaat `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` binnen enkele seconden een antwoord op de onderwerpnaam waarbij x de retry-after-waarde is. Het laadvermogen van het antwoord bevat het object [RegistrationOperationStatus](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) in JSON-formaat.

## <a name="polling-for-registration-operation-status"></a>Polling voor registratieoperatiestatus

Het apparaat moet de service periodiek peilen om het resultaat van de registratievan het apparaat te ontvangen. Ervan uitgaande dat het apparaat `$dps/registrations/res/#` zich al heeft geabonneerd op het onderwerp `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` zoals hierboven aangegeven, kan het een bericht over de status van het onderwerp publiceren op de onderwerpnaam. De bewerkings-ID in dit bericht moet de waarde zijn die in de vorige stap is ontvangen in het antwoordbericht RegistrationOperationStatus. In het succesvolle geval zal de `$dps/registrations/res/200/?$rid={request_id}` dienst reageren op het onderwerp. Het laadvermogen van het antwoord bevat het object RegistrationOperationStatus. Het apparaat moet de service blijven peilen als de responscode 202 is na een vertraging die gelijk is aan de retry-after-periode. De registratiebewerking van het apparaat is geslaagd als de service een statuscode van 200 retourneert.

## <a name="connecting-over-websocket"></a>Verbinding maken via Websocket
Wanneer u verbinding maakt via Websocket, geeft u het subprotocol op als `mqtt`. Volg [RFC 6455](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>Volgende stappen

Zie de [MQTT-documentatie](https://mqtt.org/documentation)voor meer informatie over het MQTT-protocol.

Zie:

* [Over IoT DPS](about-iot-dps.md)
