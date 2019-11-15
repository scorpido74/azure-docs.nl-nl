---
title: Meer informatie over Azure IoT Device Provisioning Service MQTT-ondersteuning | Microsoft Docs
description: 'Ontwikkelaars handleiding: ondersteuning voor apparaten die verbinding maken met het apparaat gericht eind punt van de Azure IoT Device Provisioning Service met behulp van het MQTT-protocol.'
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.openlocfilehash: fb39979e321c4815aec74c801cd2d04e81b7e533
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093902"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Communiceren met uw DPS met het MQTT-Protocol

Met DPS kunnen apparaten met het eind punt van het DPS-apparaat communiceren met:

* [MQTT v 3.1.1](https://mqtt.org/) op poort 8883
* [MQTT v 3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) over WebSocket op poort 443.

DPS is geen volledig functionele MQTT-Broker en biedt geen ondersteuning voor alle gedragingen die zijn opgegeven in de MQTT v 3.1.1-standaard. In dit artikel wordt beschreven hoe apparaten ondersteund MQTT-gedrag kunnen gebruiken om te communiceren met DPS.

Alle communicatie met DPS moet worden beveiligd met behulp van TLS/SSL. Daarom biedt DPS geen ondersteuning voor niet-beveiligde verbindingen via poort 1883.

 > [!NOTE] 
 > DPS biedt momenteel geen ondersteuning voor apparaten met behulp van TPM- [Attestation-mechanismen](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism) via het MQTT-protocol.

## <a name="connecting-to-dps"></a>Verbinding maken met DPS

Een apparaat kan het MQTT-protocol gebruiken om verbinding te maken met een DPS met behulp van een van de volgende opties.

* Bibliotheken in de [Azure IOT Provisioning sdk's](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks).
* Het MQTT-protocol direct.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Het MQTT-protocol direct gebruiken (als een apparaat)

Als een apparaat de Sdk's van het apparaat niet kan gebruiken, kan het nog steeds verbinding maken met de eind punten van het open bare apparaat met behulp van het MQTT-protocol op poort 8883. In het **Connect** -pakket moet het apparaat de volgende waarden gebruiken:

* Gebruik **registratie**voor het veld **ClientId** .

* Gebruik `{idScope}/registrations/{registration_id}/api-version=2019-03-31`voor het veld **username** , waarbij `{idScope}` de [IDSCOPE](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope) van de DPS is.

* Gebruik voor het **wachtwoord** veld een SAS-token. De indeling van de SAS-token is hetzelfde als voor de HTTPS-en AMQP-protocollen:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration` de resourceURI moet de indeling `{idScope}/registrations/{registration_id}`hebben. De beleids naam moet `registration`zijn.

  > [!NOTE]
  > Als u X. 509-certificaat authenticatie gebruikt, zijn SAS-token wachtwoorden niet vereist.

  Zie de sectie beveiligings tokens van [toegang tot DPS beheren](how-to-control-access.md#security-tokens)voor meer informatie over het genereren van SAS-tokens.

Hieronder vindt u een lijst met implementatie-specifieke gedragingen voor DPS:

 * DPS biedt geen ondersteuning voor de functionaliteit van de vlag **CleanSession** die wordt ingesteld op **0**.

 * Wanneer een apparaat-app zich abonneert op een onderwerp met **QoS 2**, verleent DPS Maxi maal QoS-niveau 1 in het **SUBACK** -pakket. Daarna levert DPS berichten op het apparaat met behulp van QoS 1.

## <a name="tlsssl-configuration"></a>TLS/SSL-configuratie

Als u het MQTT-protocol direct wilt gebruiken, *moet* de client verbinding maken via TLS 1,2. Pogingen om deze stap over te slaan mislukken met verbindings fouten.


## <a name="registering-a-device"></a>Een apparaat registreren

Als u een apparaat wilt registreren via DPS, moet een apparaat zich abonneren met `$dps/registrations/res/#` als een **onderwerps filter**. Het Joker teken `#` op meerdere niveaus in het Onderwerps filter wordt alleen gebruikt om toe te staan dat het apparaat extra eigenschappen in de onderwerpnaam ontvangt. DPS staat het gebruik van de `#` of `?` joker tekens voor het filteren van subonderwerpen niet toe. Omdat DPS geen algemene pub-berichten Broker is, worden alleen de gedocumenteerde onderwerps namen en onderwerps filters ondersteund.

Het apparaat moet een REGI ster-bericht publiceren naar DPS met `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` als **onderwerpnaam**. De payload moet het [apparaatregistratie](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) -object in JSON-indeling bevatten.
In een geslaagd scenario ontvangt het apparaat een antwoord op de naam van het `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` onderwerp, waarbij x de waarde voor opnieuw proberen (in seconden) is. De nettolading van het antwoord bevat het object [RegistrationOperationStatus](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) in JSON-indeling.

## <a name="polling-for-registration-operation-status"></a>Polling voor de status van de registratie bewerking

Het apparaat moet de service periodiek pollen om het resultaat van de registratie van het apparaat te ontvangen. Ervan uitgaande dat het apparaat al is geabonneerd op het `$dps/registrations/res/#` onderwerp dat hierboven is aangegeven, kan het een Get operationstatus-bericht publiceren naar de naam van het `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` onderwerp. De bewerkings-ID in dit bericht moet de waarde zijn die wordt ontvangen in het RegistrationOperationStatus-respons bericht in de vorige stap. In het geval van succes reageert de service op het `$dps/registrations/res/200/?$rid={request_id}` onderwerp. De nettolading van het antwoord bevat het object RegistrationOperationStatus. Op het apparaat moet de service worden gecontroleerd als de antwoord code 202 na een vertraging gelijk is aan de periode voor opnieuw proberen. De registratie van het apparaat slaagt als de service een 200-status code retourneert.

## <a name="connecting-over-websocket"></a>Verbinding maken via WebSocket
Wanneer u verbinding maakt via WebSocket, geeft u het subprotocol op als `mqtt`. Volg [RFC 6455](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>Volgende stappen

Zie de [MQTT-documentatie](https://mqtt.org/documentation)voor meer informatie over het MQTT-protocol.

Zie voor meer informatie over de mogelijkheden van DPS:

* [Over IoT DPS](about-iot-dps.md)
