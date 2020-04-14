---
title: Informatie over Azure IoT Hub MQTT-ondersteuning | Microsoft Documenten
description: Handleiding voor ontwikkelaars - ondersteuning voor apparaten die verbinding maken met een eindpunt dat met een IoT Hub-apparaat is gericht met behulp van het MQTT-protocol. Bevat informatie over ingebouwde MQTT-ondersteuning in de Azure IoT-apparaat-SDK's.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.openlocfilehash: 9ccfaa57b8e8fdea325bed908ffe8815b09d0d15
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257790"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Communiceren met uw IoT-hub met het MQTT-protocol

Met IoT Hub kunnen apparaten communiceren met de eindpunten van het IoT Hub-apparaat met:

* [MQTT v3.1.1](https://mqtt.org/) op poort 8883
* MQTT v3.1.1 via WebSocket op poort 443.

IoT Hub is niet een volledig functionele MQTT-broker en biedt geen ondersteuning voor al het gedrag in de MQTT v3.1.1-standaard. In dit artikel wordt beschreven hoe apparaten ondersteund MQTT-gedrag kunnen gebruiken om te communiceren met IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Alle apparaatcommunicatie met IoT Hub moet worden beveiligd met TLS/SSL. Daarom biedt IoT Hub geen ondersteuning voor niet-beveiligde verbindingen via poort 1883.

## <a name="connecting-to-iot-hub"></a>Verbinding maken met IoT Hub

Een apparaat kan het MQTT-protocol gebruiken om verbinding te maken met een IoT-hub met behulp van een van de volgende opties.

* Bibliotheken in de [Azure IoT-SDK's](https://github.com/Azure/azure-iot-sdks).
* Het MQTT protocol direct.

De MQTT-poort (8883) is geblokkeerd in veel zakelijke en educatieve netwerkomgevingen. Als u poort 8883 niet in uw firewall openen, raden we u aan MQTT via websockets te gebruiken. MQTT via Web Sockets communiceert via poort 443, die bijna altijd open is in netwerkomgevingen. Zie De [SDK's van het apparaat](#using-the-device-sdks)gebruiken voor meer informatie over het opgeven van de MQTT- en MQTT-protocollen via Web Sockets.

## <a name="using-the-device-sdks"></a>Het apparaat SDKs gebruiken

[Device SDKs](https://github.com/Azure/azure-iot-sdks) die het MQTT-protocol ondersteunen, zijn beschikbaar voor Java, Node.js, C, C#en Python. De sdk's van het apparaat gebruiken de standaard IoT Hub-verbindingstekenreeks om een verbinding met een IoT-hub tot stand te brengen. Als u het MQTT-protocol wilt gebruiken, moet de parameter clientprotocol zijn ingesteld op **MQTT**. U MQTT ook via Web Sockets opgeven in de parameter clientprotocol. Standaard maken de SDK's van het apparaat verbinding met een IoT-hub met de **CleanSession-vlag** ingesteld op **0** en gebruiken **het QoS 1** voor het uitwisselen van berichten met de IoT-hub.

Wanneer een apparaat is verbonden met een IoT-hub, bieden de SDK's van het apparaat methoden waarmee het apparaat berichten kan uitwisselen met een IoT-hub.

De volgende tabel bevat koppelingen naar codevoorbeelden voor elke ondersteunde taal en geeft de parameter op die moet worden gebruikt om een verbinding met IoT-hub tot stand te brengen met behulp van de MQTT of het MQTT-protocol via het Sockets-protocol.

| Taal | MQTT-protocolparameter | MQTT over de parameter Web Sockets-protocol
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azure-iot-device-mqtt. Mqtt Mqtt Mqtt | azure-iot-device-mqtt. MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol?view=azure-java-stable). MQTT MQTT | iotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.transporttype?view=azure-dotnet). Mqtt Mqtt Mqtt | TransportType.Mqtt valt terug naar MQTT via Web Sockets als MQTT uitvalt. Als u MQTT alleen via Web Sockets wilt opgeven, gebruikt u TransportType.Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Ondersteunt MQTT standaard | Voeg `websockets=True` de aanroep toe om de client te maken |

In het volgende fragment ziet u hoe u het MQTT-protocol opgeeft via websockets bij het gebruik van de Azure IoT Node.js SDK:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

In het volgende fragment ziet u hoe u het MQTT-protocol opgeeft via websockets bij het gebruik van de Azure IoT Python SDK:

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Standaard keep-alive time-out

Om ervoor te zorgen dat een client/IoT Hub-verbinding in leven blijft, sturen zowel de service als de klant elkaar regelmatig een *keep-alive* ping. De client die IoT SDK gebruikt, stuurt een keep-alive op het interval dat in deze onderstaande tabel is gedefinieerd:

|Taal  |Standaard keep-alive interval  |Configureerbaar  |
|---------|---------|---------|
|Node.js     |   180 seconden      |     Nee    |
|Java     |    230 seconden     |     Nee    |
|C     | 240 seconden |  [Ja](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 seconden |  [Ja](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python (V2)   | 60 seconden |  Nee   |

Na [MQTT-specificaties](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)is het keep-alive ping-interval van IoT Hub 1,5 keer de waarde van de klant. IoT Hub beperkt de maximale time-out aan de serverzijde echter tot 29,45 minuten (1767 seconden), omdat alle Azure-services zijn gebonden aan de idle time-out van Azure load balancer TCP, die 29,45 minuten bedraagt. 

Een apparaat met de Java SDK verzendt bijvoorbeeld de keep-alive ping en verliest vervolgens de netwerkverbinding. 230 seconden later mist het apparaat de keep-alive ping omdat het offline is. IoT Hub sluit de verbinding echter niet onmiddellijk `(230 * 1.5) - 230 = 115` - het wacht nog een paar seconden voordat het apparaat wordt losgekoppeld met de fout [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md). 

De maximale waarde die u `1767 / 1.5 = 1177` kunt instellen voor het in leven houden van de client is seconden. Elk verkeer zal de in leven houden resetten. Een succesvolle Vernieuwing van sas-token wordt bijvoorbeeld opnieuw ingesteld.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Een apparaat-app migreren van AMQP naar MQTT

Als u de [sdks](https://github.com/Azure/azure-iot-sdks)van het apparaat gebruikt, moet u van het gebruik van AMQP naar MQTT de protocolparameter in de clientinitialisatie wijzigen zoals eerder vermeld.

Controleer daarbij de volgende items:

* AMQP retourneert fouten voor veel voorwaarden, terwijl MQTT de verbinding beëindigt. Als gevolg hiervan kan voor de logica voor het afhandelen van uitzonderingen een aantal wijzigingen nodig zijn.

* MQTT ondersteunt de *afwijsbewerkingen* niet bij het ontvangen van [cloud-to-device-berichten.](iot-hub-devguide-messaging.md) Als uw back-end-app een antwoord van de apparaat-app moet ontvangen, u overwegen directe methoden te [gebruiken.](iot-hub-devguide-direct-methods.md)

* AMQP wordt niet ondersteund in de Python SDK

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Het MQTT-protocol direct gebruiken (als apparaat)

Als een apparaat de sdk's van het apparaat niet kan gebruiken, kan het nog steeds verbinding maken met de eindpunten van openbare apparaten via het MQTT-protocol op poort 8883. In het **CONNECT-pakket** moet het apparaat de volgende waarden gebruiken:

* Gebruik het **apparaatId**voor het veld **ClientId** .

* Gebruik voor het `{iothubhostname}/{device_id}/?api-version=2018-06-30`veld `{iothubhostname}` **Gebruikersnaam** de volledige CName van de IoT-hub.

    Als de naam van uw IoT-hub bijvoorbeeld **contoso.azure-devices.net** is en als de naam van uw apparaat **MyDevice01**is, moet het veld volledige gebruikersnaam het volledige **gebruikersnaamveld** bevatten:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* Gebruik voor het veld **Wachtwoord** een SAS-token. Het formaat van het SAS-token is hetzelfde als voor zowel de HTTPS- als DEAMQP-protocollen:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Als u X.509-certificaatverificatie gebruikt, zijn SAS-tokenwachtwoorden niet vereist. Zie [X.509-beveiliging instellen in uw Azure IoT Hub](iot-hub-security-x509-get-started.md) en volg [onderstaande](#tlsssl-configuration)code-instructies voor meer informatie.

  Zie het apparaatgedeelte van Het gebruik van [IoT Hub-beveiligingstokens](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)voor meer informatie over het genereren van SAS-tokens.

  Bij het testen u ook de cross-platform [Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) of de CLI-extensie az iot hub [generate-sas-token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token) gebruiken om snel een SAS-token te genereren dat u in uw eigen code kopiëren en plakken:

### <a name="for-azure-iot-tools"></a>Voor Azure IoT-hulpprogramma's

1. Vouw het tabblad **AZURE IOT HUB-apparaten** uit in de linkerbenedenhoek van Visual Studio Code.
  
2. Klik met de rechtermuisknop op uw apparaat en selecteer **SAS-token genereren voor apparaat**.
  
3. Stel **de vervaldatum** in en druk op 'Enter'.
  
4. Het SAS-token wordt gemaakt en gekopieerd naar het klembord.

   Het SAS-token dat wordt gegenereerd, heeft de volgende structuur:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Het deel van dit token dat moet worden gebruikt als het veld **Wachtwoord** om verbinding te maken met MQTT is:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Voor MQTT connect en disconnect packets geeft IoT Hub een gebeurtenis uit op het **Operations Monitoring-kanaal.** Deze gebeurtenis heeft aanvullende informatie waarmee u verbindingsproblemen oplossen.

De apparaat-app kan een **Wil-bericht** opgeven in het **CONNECT-pakket.** De apparaat-app `devices/{device_id}/messages/events/` `devices/{device_id}/messages/events/{property_bag}` moet gebruiken of als de plaats **Will-onderwerp** om **wilberichten** te definiëren die moeten worden doorgestuurd als een telemetriebericht. Als in dit geval de netwerkverbinding is gesloten, maar een **DISCONNECT-pakket** niet eerder van het apparaat is ontvangen, verzendt IoT Hub het bericht **Zal** dat in het **CONNECT-pakket** wordt geleverd naar het telemetriekanaal. Het telemetriekanaal kan het standaardeindpunt **gebeurtenissen** zijn of een aangepast eindpunt dat is gedefinieerd door IoT Hub-routering. Het bericht heeft de eigenschap **iothub-MessageType** met een waarde van **Wil** die eraan is toegewezen.

### <a name="an-example-of-c-code-using-mqtt-without-azure-iot-c-sdk"></a>Een voorbeeld van C-code met MQTT zonder Azure IoT C SDK
In deze [repository](https://github.com/Azure-Samples/IoTMQTTSample)vindt u een paar C/C++-demoprojecten die laten zien hoe u telemetrieberichten verzendt, gebeurtenissen met een IoT-hub ontvangt zonder de Azure IoT C SDK te gebruiken. 

Deze voorbeelden maken gebruik van de Eclipse Mosquitto bibliotheek om bericht te sturen naar de MQTT Broker geïmplementeerd in de IoT hub.

Deze repository bevat:

**Voor Windows:**

* TelemetrieMQTTWin32: bevat code om een telemetriebericht te verzenden naar een Azure IoT-hub, gebouwd en uitgevoerd op een Windows-machine.

* SubscribeMQTTWin32: bevat code om u te abonneren op gebeurtenissen van een bepaalde IoT-hub op een Windows-machine.

* DeviceTwinMQTTWin32: bevat code om de dubbele gebeurtenissen van een apparaat op te vragen en u te abonneren op de twee gebeurtenissen van een apparaat in de Azure IoT-hub op een Windows-machine.

* PnPMQTTWin32: bevat code om een telemetriebericht met IoT Plug te verzenden & Play Preview-apparaatmogelijkheden naar een Azure IoT-hub, gebouwd en uitgevoerd op een Windows-machine. Meer over IoT Plug & Play [hier](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)

**Voor Linux:**

* MQTTLinux: bevat code en build script om te draaien op Linux (WSL, Ubuntu, en Raspbian zijn tot nu toe getest).

* LinuxConsoleVS2019: bevat dezelfde code, maar in een VS2019-project gericht op WSL (Windows Linux subsysteem). Met dit project u de code die op Linux wordt uitgevoerd stap voor stap debuggen vanuit Visual Studio.

**Voor mosquitto_pub:**

Deze map bevat twee voorbeelden opdrachten die worden gebruikt met mosquitto_pub hulpprogramma die door Mosquitto.org.

* Mosquitto_sendmessage: een eenvoudig sms-bericht verzenden naar een Azure IoT-hub die fungeert als een apparaat.

* Mosquitto_subscribe: gebeurtenissen zien in een Azure IoT-hub.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Het MQTT-protocol direct gebruiken (als module)

Verbinding maken met IoT Hub via MQTT met behulp van een module-identiteit is vergelijkbaar met het apparaat [(hierboven](#using-the-mqtt-protocol-directly-as-a-device)beschreven), maar u moet het volgende gebruiken:

* Stel de client-id in op `{device_id}/{module_id}`.

* Als u authenticeert met gebruikersnaam en `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` wachtwoord, stelt u de gebruikersnaam in op en gebruikt u het SAS-token dat is gekoppeld aan de identiteit van de module als uw wachtwoord.

* Gebruiken `devices/{device_id}/modules/{module_id}/messages/events/` als onderwerp voor het publiceren van telemetrie.

* Gebruik `devices/{device_id}/modules/{module_id}/messages/events/` als WILL onderwerp.

* De dubbele GET- en PATCH-onderwerpen zijn identiek voor modules en apparaten.

* Het onderwerp met twee statussen is identiek voor modules en apparaten.

## <a name="tlsssl-configuration"></a>TLS/SSL-configuratie

Als u het MQTT-protocol rechtstreeks wilt gebruiken, *moet* uw client verbinding maken via TLS/SSL. Pogingen om deze stap over te slaan mislukken met verbindingsfouten.

Om een TLS-verbinding tot stand te brengen, moet u mogelijk het DigiCert Baltimore Root Certificate downloaden en verwijzen. Dit certificaat is het certificaat dat Azure gebruikt om de verbinding te beveiligen. U dit certificaat vinden in de [Azure-iot-sdk-c-repository.](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) Meer informatie over deze certificaten is te vinden op [de website van Digicert.](https://www.digicert.com/digicert-root-certificates.htm)

Een voorbeeld van hoe dit te implementeren met behulp van de Python-versie van de [Paho MQTT-bibliotheek](https://pypi.python.org/pypi/paho-mqtt) door de Eclipse Foundation kan er als volgt uitzien.

Installeer eerst de Paho-bibliotheek vanuit uw opdrachtlijnomgeving:

```cmd/sh
pip install paho-mqtt
```

Implementeer vervolgens de client in een Python-script. Vervang de tijdelijke aanduidingen als volgt:

* `<local path to digicert.cer>`is het pad naar een lokaal bestand dat het DigiCert Baltimore Root-certificaat bevat. U dit bestand maken door de certificaatgegevens van [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) in de `-----BEGIN CERTIFICATE-----` Azure `-----END CERTIFICATE-----`IoT `"` SDK voor C te `\r\n` kopiëren.

* `<device id from device registry>`is de id van een apparaat dat u aan uw IoT-hub hebt toegevoegd.

* `<generated SAS token>`is een SAS-token voor het apparaat dat is gemaakt zoals eerder beschreven in dit artikel.

* `<iot hub name>`de naam van uw IoT-hub.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Als u wilt verifiëren met een apparaatcertificaat, werkt u het bovenstaande codefragment bij met de volgende wijzigingen (zie [Een X.509 CA-certificaat krijgen](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) voor het voorbereiden op verificatie op basis van certificaten):

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Device-to-cloudberichten verzenden

Nadat een succesvolle verbinding is gemaakt, kan een `devices/{device_id}/messages/events/` apparaat `devices/{device_id}/messages/events/{property_bag}` berichten naar IoT Hub verzenden met of als **onderwerpnaam**. Het `{property_bag}` element stelt het apparaat in staat om berichten met extra eigenschappen in een url-gecodeerde indeling te verzenden. Bijvoorbeeld:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Dit `{property_bag}` element gebruikt dezelfde codering als querytekenreeksen in het HTTPS-protocol.

Het volgende is een lijst met specifiek gedrag van iot-hubimplementaties:

* IoT Hub ondersteunt geen QoS 2-berichten. Als een apparaat-app een bericht publiceert met **QoS 2,** sluit IoT Hub de netwerkverbinding.

* IoT Hub blijft niet bestaan Berichten behouden. Als een apparaat een bericht verzendt met de vlag **BEHOUDEN** ingesteld op 1, voegt IoT Hub de eigenschap **x-opt-retain** applicatie toe aan het bericht. In dit geval geeft IoT Hub het door aan de backend-app, in plaats van het bericht te behouden.

* IoT Hub ondersteunt slechts één actieve MQTT-verbinding per apparaat. Elke nieuwe MQTT-verbinding namens dezelfde apparaat-id zorgt ervoor dat IoT Hub de bestaande verbinding laat vallen.

Zie [De handleiding van de ontwikkelaar messaging](iot-hub-devguide-messaging.md)voor meer informatie.

## <a name="receiving-cloud-to-device-messages"></a>Berichten van cloud-to-device ontvangen

Als u berichten van IoT Hub wilt `devices/{device_id}/messages/devicebound/#` ontvangen, moet een apparaat zich abonneren met behulp **van een onderwerpfilter.** De wildcard `#` op meerdere niveaus in het onderwerpfilter wordt alleen gebruikt om het apparaat extra eigenschappen in de onderwerpnaam te laten ontvangen. IoT Hub staat het gebruik `#` `?` van de of wildcards voor het filteren van subonderwerpen niet toe. Aangezien IoT Hub geen pub-subberichtenmakelaar voor algemene doeleinden is, ondersteunt het alleen de gedocumenteerde onderwerpnamen en onderwerpfilters.

Het apparaat ontvangt geen berichten van IoT Hub, totdat het zich heeft geabonneerd op `devices/{device_id}/messages/devicebound/#` het apparaatspecifieke eindpunt, vertegenwoordigd door het onderwerpfilter. Nadat een abonnement is ingesteld, ontvangt het apparaat cloud-to-device-berichten die naar het apparaat zijn verzonden na het tijdstip van het abonnement. Als het apparaat verbinding maakt met **CleanSession** flag ingesteld op **0,** blijft het abonnement bestaan in verschillende sessies. In dit geval ontvangt het apparaat de volgende keer dat het verbinding maakt met **CleanSession 0** alle openstaande berichten die naar het apparaat worden verzonden terwijl het is losgekoppeld. Als het apparaat echter **cleansession-vlag** gebruikt die is ingesteld op **1,** ontvangt het geen berichten van IoT Hub totdat het zich abonneert op het apparaat-eindpunt.

IoT Hub levert berichten met de `devices/{device_id}/messages/devicebound/{property_bag}` **onderwerpnaam** `devices/{device_id}/messages/devicebound/`of wanneer er berichteigenschappen zijn. `{property_bag}`bevat url-gecodeerde sleutel/waardeparen van berichteigenschappen. Alleen toepassingseigenschappen en door de gebruiker ingestelde systeemeigenschappen (zoals **messageId** of **correlationId)** zijn opgenomen in de eigenschappenzak. Systeemeigenschapnamen hebben het **$** voorvoegsel, toepassingseigenschappen gebruiken de oorspronkelijke eigenschapsnaam zonder voorvoegsel.

Wanneer een apparaat-app zich abonneert op een onderwerp met **QoS 2,** verleent IoT Hub maximaal QoS-niveau 1 in het **SUBACK-pakket.** Daarna levert IoT Hub berichten aan het apparaat met QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>De eigenschappen van een apparaattweeling ophalen

Eerst is een apparaat `$iothub/twin/res/#`geabonneerd op , om de reacties van de bewerking te ontvangen. Vervolgens wordt een leeg bericht `$iothub/twin/GET/?$rid={request id}`naar onderwerp verzonden, met een bevolkte waarde voor **aanvraag-id**. De service stuurt vervolgens een antwoordbericht met `$iothub/twin/res/{status}/?$rid={request id}`de apparaat twin data on topic, met dezelfde **aanvraag-ID** als het verzoek.

Aanvraag-id kan elke geldige waarde zijn voor een berichteigenschapwaarde, volgens de [handleiding van de IoT Hub-berichtenontwikkelaar](iot-hub-devguide-messaging.md)en wordt de status gevalideerd als een geheel getal.

De antwoordtekst bevat de eigenschappensectie van de apparaattweeling, zoals in het volgende antwoordvoorbeeld wordt weergegeven:

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

De mogelijke statuscodes zijn:

|Status | Beschrijving |
| ----- | ----------- |
| 200 | Geslaagd |
| 429 | Te veel aanvragen (throttled), volgens [IoT Hub-beperking](iot-hub-devguide-quotas-throttling.md) |
| 5** | Serverfouten |

Zie [de handleiding van de ontwikkelaar van apparaattweelingen](iot-hub-devguide-device-twins.md)voor meer informatie.

## <a name="update-device-twins-reported-properties"></a>De gerapporteerde eigenschappen van apparaattweeling bijwerken

Als u gerapporteerde eigenschappen wilt bijwerken, geeft het apparaat een verzoek uit naar IoT Hub via een publicatie over een aangewezen MQTT-onderwerp. Na het verwerken van de aanvraag reageert IoT Hub via een publicatie op het succes of de foutstatus van de updatebewerking op een ander onderwerp. Dit onderwerp kan worden geabonneerd door het apparaat om het te informeren over het resultaat van zijn dubbele update verzoek. Om dit type interactie tussen verzoek en antwoord in MQTT`$rid`te implementeren, maken we gebruik van het begrip request ID () dat het apparaat in eerste instantie in zijn updateverzoek heeft verstrekt. Deze aanvraag-ID is ook opgenomen in het antwoord van IoT Hub, zodat het apparaat het antwoord op zijn specifieke eerdere verzoek kan correleren.

In de volgende reeks wordt beschreven hoe een apparaat de gerapporteerde eigenschappen in de apparaattweeling in IoT Hub bijwerkt:

1. Een apparaat moet zich `$iothub/twin/res/#` eerst abonneren op het onderwerp om de reacties van de bewerking van IoT Hub te ontvangen.

2. Een apparaat stuurt een bericht dat de `$iothub/twin/PATCH/properties/reported/?$rid={request id}` dubbele update van het apparaat bevat naar het onderwerp. Dit bericht bevat een **waarde van de aanvraag-id.**

3. De service verzendt vervolgens een antwoordbericht met de nieuwe ETag-waarde voor de verzameling gerapporteerde eigenschappen op onderwerp `$iothub/twin/res/{status}/?$rid={request id}`. In dit antwoordbericht wordt dezelfde **aanvraag-id** gebruikt als het verzoek.

De hoofdtekst van het aanvraagbericht bevat een JSON-document, dat nieuwe waarden bevat voor gerapporteerde eigenschappen. Elk lid in het JSON-document werkt het bijbehorende lid bij of voegt het bijbehorende lid toe aan het document van de apparaattweeling. Een lid `null`ingesteld op , verwijdert het lid uit het bevattende object. Bijvoorbeeld:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

De mogelijke statuscodes zijn:

|Status | Beschrijving |
| ----- | ----------- |
| 204 | Succes (er wordt geen inhoud geretourneerd) |
| 400 | Slecht verzoek. Misvormde JSON |
| 429 | Te veel aanvragen (throttled), volgens [IoT Hub-beperking](iot-hub-devguide-quotas-throttling.md) |
| 5** | Serverfouten |

Het python-codefragment hieronder toont het dubbele gerapporteerde eigenschappenupdateproces over MQTT (met behulp van Paho MQTT-client):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Na het succes van twin reported properties update operatie hierboven, de `$iothub/twin/res/204/?$rid=1&$version=6`publicatie `204` bericht van IoT Hub `$rid=1` zal het volgende onderwerp: , waar is `$version` de statuscode die succes aangeeft, komt overeen met de aanvraag-ID die door het apparaat in de code, en komt overeen met de versie van de gerapporteerde eigenschappen sectie van het apparaat tweelingen na de update.

Zie [de handleiding van de ontwikkelaar van apparaattweelingen](iot-hub-devguide-device-twins.md)voor meer informatie.

## <a name="receiving-desired-properties-update-notifications"></a>De gewenste meldingen voor het bijwerken van eigenschappen ontvangen

Wanneer een apparaat is verbonden, stuurt IoT Hub meldingen naar het onderwerp `$iothub/twin/PATCH/properties/desired/?$version={new version}`, die de inhoud van de update bevatten die wordt uitgevoerd door de back-end van de oplossing. Bijvoorbeeld:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Wat eigenschapsupdates `null` betreft, betekenen waarden dat het JSON-objectlid wordt verwijderd. Houd er `$version` ook rekening mee dat de nieuwe versie van de gewenste eigenschappensectie van de tweeling aangeeft.

> [!IMPORTANT]
> IoT Hub genereert alleen wijzigingsmeldingen wanneer apparaten zijn verbonden. Zorg ervoor dat u de [apparaatkoppelingsstroom](iot-hub-devguide-device-twins.md#device-reconnection-flow) implementeert om de gewenste eigenschappen gesynchroniseerd te houden tussen IoT Hub en de apparaat-app.

Zie [de handleiding van de ontwikkelaar van apparaattweelingen](iot-hub-devguide-device-twins.md)voor meer informatie.

## <a name="respond-to-a-direct-method"></a>Reageren op een directe methode

Eerst moet een apparaat `$iothub/methods/POST/#`zich abonneren op . IoT Hub stuurt methodeaanvragen `$iothub/methods/POST/{method name}/?$rid={request id}`naar het onderwerp, met een geldige JSON of een leeg lichaam.

Om te reageren stuurt het apparaat een bericht met een `$iothub/methods/res/{status}/?$rid={request id}`geldige JSON of lege body naar het onderwerp. In dit bericht moet de **aanvraag-id** overeenkomen met de id in het aanvraagbericht en moet de **status** een geheel getal zijn.

Zie [Gids van methodeontwikkelaars voor](iot-hub-devguide-direct-methods.md)meer informatie.

## <a name="additional-considerations"></a>Aanvullende overwegingen

Als laatste overweging moet u de [Azure IoT-protocolgateway](iot-hub-protocol-gateway.md)controleren als u het mqtt-protocolgedrag aan de cloudzijde moet aanpassen. Met deze software u een krachtige aangepaste protocolgateway implementeren die rechtstreeks met IoT Hub wordt gekoppeld. Met de Azure IoT-protocolgateway u het apparaatprotocol aanpassen aan brownfield MQTT-implementaties of andere aangepaste protocollen. Deze aanpak vereist echter wel dat u een aangepaste protocolgateway uitvoert en bedient.

## <a name="next-steps"></a>Volgende stappen

Zie de [MQTT-documentatie](https://mqtt.org/documentation)voor meer informatie over het MQTT-protocol.

Zie voor meer informatie over het plannen van uw IoT Hub-implementatie:

* [Azure Certified voor IoT-apparaatcatalogus](https://catalog.azureiotsolutions.com/)
* [Aanvullende protocollen ondersteunen](iot-hub-protocol-gateway.md)
* [Vergelijken met gebeurtenishubs](iot-hub-compare-event-hubs.md)
* [Schalen, HA en DR](iot-hub-scaling.md)

Zie:

* [Handleiding voor IoT Hub-ontwikkelaars](iot-hub-devguide.md)
* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
