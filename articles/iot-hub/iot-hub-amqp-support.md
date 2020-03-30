---
title: Informatie over Azure IoT Hub AMQP-ondersteuning | Microsoft Documenten
description: Handleiding voor ontwikkelaars - ondersteuning voor apparaten die verbinding maken met iot-hub-apparaatgerichte en servicegerichte eindpunten met behulp van het AMQP-protocol. Bevat informatie over ingebouwde AMQP-ondersteuning in de Azure IoT-apparaat-SDK's.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.openlocfilehash: 7f7e957502419b766f7da63048e8168192ea20da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284783"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Communiceer met uw IoT-hub met het AMQP-protocol

Azure IoT Hub ondersteunt [OASIS Advanced Message Queuing Protocol (AMQP) versie 1.0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) om een verscheidenheid aan functionaliteiten te leveren via apparaatgerichte en servicegerichte eindpunten. In dit document wordt het gebruik van AMQP-clients beschreven om verbinding te maken met een IoT-hub om iot-hubfunctionaliteit te gebruiken.

## <a name="service-client"></a>Serviceclient

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Verbinding maken en verifiëren met een IoT-hub (serviceclient)

Als u verbinding wilt maken met een IoT-hub met AMQP, kan een client de [op claims gebaseerde beveiliging (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) of [SASL-verificatie (Simple Authentication and Security Layer)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)gebruiken.

De volgende informatie is vereist voor de serviceclient:

| Informatie | Waarde |
|-------------|--------------|
| Hostname van iot-hub | `<iot-hub-name>.azure-devices.net` |
| Sleutelnaam | `service` |
| Toegangssleutel | Een primaire of secundaire sleutel die is gekoppeld aan de service |
| Handtekening voor gedeelde toegang | Een handtekening van een kortstondige gedeelde `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`toegang in de volgende indeling: . Zie [Toegang tot IoT Hub beheren voor](./iot-hub-devguide-security.md#security-token-structure)het genereren van de code voor het genereren van deze handtekening.

Het volgende codefragment gebruikt de [uAMQP-bibliotheek in Python](https://github.com/Azure/azure-uamqp-python) om verbinding te maken met een IoT-hub via een afzenderkoppeling.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Cloud-to-device-berichten aanroepen (serviceclient)

Zie Berichten van cloud naar apparaat [verzenden vanaf uw IoT-hub](iot-hub-devguide-messages-c2d.md)voor meer informatie over de berichten-uitwisseling tussen de cloud naar het apparaat en tussen het apparaat en tussen de IoT-hub. De serviceclient gebruikt twee koppelingen om berichten te verzenden en feedback te ontvangen voor eerder verzonden berichten vanaf apparaten, zoals beschreven in de volgende tabel:

| Gemaakt door | Type koppeling | Koppelingspad | Beschrijving |
|------------|-----------|-----------|-------------|
| Service | Koppeling afzender | `/messages/devicebound` | Cloud-to-device berichten die zijn bestemd voor apparaten worden door de service naar deze koppeling verzonden. Berichten die via deze `To` link worden verzonden, hebben hun `/devices/<deviceID>/messages/devicebound`eigenschap ingesteld op het verbindingspad van het doelapparaat, . |
| Service | Koppeling naar ontvanger | `/messages/serviceBound/feedback` | Feedbackberichten voor voltooiing, afwijzing en stopzetting die afkomstig zijn van apparaten die op deze link per service zijn ontvangen. Zie [Cloud-to-device-berichten verzenden vanaf een IoT-hub](./iot-hub-devguide-messages-c2d.md#message-feedback)voor meer informatie over feedbackberichten. |

In het volgende codefragment wordt uitgelegd hoe u een cloud-to-device-bericht maakt en naar een apparaat verzendt met behulp van de [uAMQP-bibliotheek in Python.](https://github.com/Azure/azure-uamqp-python)

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Om feedback te ontvangen, maakt de serviceclient een ontvangerkoppeling. In het volgende codefragment wordt uitgelegd hoe u een koppeling maakt met behulp van de [uAMQP-bibliotheek in Python:](https://github.com/Azure/azure-uamqp-python)

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
    print('received a message')
    # Check content_type in message property to identify feedback messages coming from device
    if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
        msg_body_raw = msg.get_data()
        msg_body_str = ''.join(msg_body_raw)
        msg_body = json.loads(msg_body_str)
        print(json.dumps(msg_body, indent=2))
        print('******************')
        for feedback in msg_body:
            print('feedback received')
            print('\tstatusCode: ' + str(feedback['statusCode']))
            print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
            print('\tdeviceId: ' + str(feedback['deviceId']))
            print
    else:
        print('unknown message:', msg.properties.content_type)
```

Zoals in de vorige code wordt weergegeven, heeft een feedbackbericht van cloud-naar-apparaat een *inhoudstype van toepassing/vnd.microsoft.iothub.feedback.json*. U de eigenschappen in de JSON-body van het bericht gebruiken om de leveringsstatus van het oorspronkelijke bericht af te leiden:

* De `statusCode` sleutel in de feedbackbody heeft een van de volgende waarden: *Succes*, *Verlopen*, *DeliveryCountExceeded*, *Rejected*of *Purged*.

* De `deviceId` sleutel in de feedbackbody heeft de ID van het doelapparaat.

* De `originalMessageId` sleutel in de feedbackbody heeft de id van het oorspronkelijke cloud-to-device-bericht dat door de service is verzonden. U deze leveringsstatus gebruiken om feedback te correleren met berichten van cloud naar apparaat.

### <a name="receive-telemetry-messages-service-client"></a>Telemetrieberichten ontvangen (serviceclient)

Standaard worden ingenomen apparaattelemetrieberichten van uw IoT-hub opgeslagen in een ingebouwde gebeurtenishub. Uw serviceclient kan het AMQP-protocol gebruiken om de opgeslagen gebeurtenissen te ontvangen.

Hiervoor moet de serviceclient eerst verbinding maken met het Eindpunt van de IoT-hub en een omleidingsadres ontvangen naar de ingebouwde gebeurtenishubs. De serviceclient gebruikt vervolgens het opgegeven adres om verbinding te maken met de ingebouwde gebeurtenishub.

In elke stap moet de klant de volgende informatie presenteren:

* Geldige servicereferenties (handtekeningtoken voor gedeelde servicetoegang).

* Een goed geformatteerd pad naar de partitie van de consumentengroep waarvan het berichten wil ophalen. Voor een bepaalde consumentengroep en partitie-id heeft `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` het pad de `$Default`volgende indeling: (de standaardgroep is ).

* Een optioneel filterpredicaat om een startpunt in de partitie aan te wijzen. Dit predicaat kan in de vorm van een volgnummer, offset of in de wachtrij staande tijdstempel zijn.

In het volgende codefragment wordt de [uAMQP-bibliotheek in Python](https://github.com/Azure/azure-uamqp-python) gebruikt om de voorgaande stappen weer te geven:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI


def set_endpoint_filter(uri, endpoint_filter=''):
    source_uri = uamqp.address.Source(uri)
    source_uri.set_filter(endpoint_filter)
    return source_uri


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Voor een bepaalde apparaat-id gebruikt de IoT-hub een hash van de apparaat-id om te bepalen in welke partitie de berichten moeten worden opgeslagen. Het voorgaande codefragment laat zien hoe gebeurtenissen worden ontvangen van één dergelijke partitie. Houd er echter rekening mee dat een typische toepassing vaak gebeurtenissen moet ophalen die zijn opgeslagen in alle gebeurtenishubpartities.

## <a name="device-client"></a>Apparaatclient

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Verbinding maken en verifiëren met een IoT-hub (apparaatclient)

Als u verbinding wilt maken met een IoT-hub met AMQP, kan een apparaat [op claims gebaseerde beveiliging (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) of [SASL-verificatie (Simple Authentication and Security Layer)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) gebruiken.

De volgende informatie is vereist voor de apparaatclient:

| Informatie | Waarde |
|-------------|--------------|
| Hostname van iot-hub | `<iot-hub-name>.azure-devices.net` |
| Toegangssleutel | Een primaire of secundaire sleutel die aan het apparaat is gekoppeld |
| Handtekening voor gedeelde toegang | Een handtekening van een kortstondige gedeelde `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`toegang in de volgende indeling: . Zie [Toegang tot IoT Hub beheren voor](./iot-hub-devguide-security.md#security-token-structure)het genereren van de code voor het genereren van deze handtekening.

Het volgende codefragment gebruikt de [uAMQP-bibliotheek in Python](https://github.com/Azure/azure-uamqp-python) om verbinding te maken met een IoT-hub via een afzenderkoppeling.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

De volgende koppelingspaden worden ondersteund als apparaatbewerkingen:

| Gemaakt door | Type koppeling | Koppelingspad | Beschrijving |
|------------|-----------|-----------|-------------|
| Apparaten | Koppeling naar ontvanger | `/devices/<deviceID>/messages/devicebound` | Cloud-to-device berichten die zijn bestemd voor apparaten worden op deze link ontvangen door elk doelapparaat. |
| Apparaten | Koppeling afzender | `/devices/<deviceID>/messages/events` | Device-to-cloud berichten die vanaf een apparaat worden verzonden, worden via deze link verzonden. |
| Apparaten | Koppeling afzender | `/messages/serviceBound/feedback` | Feedback van berichten in de cloud naar het apparaat die via deze koppeling door apparaten naar de service worden verzonden. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Cloud-to-device-opdrachten ontvangen (apparaatclient)

Cloud-to-device-opdrachten die naar apparaten worden `/devices/<deviceID>/messages/devicebound` verzonden, komen op een koppeling aan. Apparaten kunnen deze berichten in batches ontvangen en de payload van berichtgegevens, berichteigenschappen, annotaties of toepassingseigenschappen gebruiken in het bericht als dat nodig is.

Het volgende codefragment gebruikt de [uAMQP-bibliotheek in Python](https://github.com/Azure/azure-uamqp-python)) om cloud-to-device-berichten te ontvangen door een apparaat.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
    batch = receive_client.receive_message_batch(max_batch_size=5)
    for msg in batch:
        print('*** received a message ***')
        print(''.join(msg.get_data()))

        # Property 'to' is set to: '/devices/device1/messages/devicebound',
        print('\tto:                     ' + str(msg.properties.to))

        # Property 'message_id' is set to value provided by the service
        print('\tmessage_id:             ' + str(msg.properties.message_id))

        # Other properties are present if they were provided by the service
        print('\tcreation_time:          ' + str(msg.properties.creation_time))
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Telemetrieberichten verzenden (apparaatclient)

U ook telemetrieberichten verzenden vanaf een apparaat met BEHULP van AMQP. Het apparaat kan optioneel een woordenboek met toepassingseigenschappen of verschillende berichteigenschappen, zoals bericht-id, bieden.

Het volgende codefragment gebruikt de [uAMQP-bibliotheek in Python](https://github.com/Azure/azure-uamqp-python) om apparaat-naar-cloudberichten vanaf een apparaat te verzenden.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* De AMQP-verbindingen kunnen worden verstoord als gevolg van een netwerkstoring of het verlopen van het verificatietoken (gegenereerd in de code). De serviceclient moet deze omstandigheden afhandelen en indien nodig de verbinding en koppelingen herstellen. Als een verificatietoken verloopt, kan de client een verbindingsdaling voorkomen door het token proactief te vernieuwen voordat het afloopt.

* Uw klant moet af en toe in staat zijn om link omleidingen correct te behandelen. Zie uw AMQP-clientdocumentatie om een dergelijke bewerking te begrijpen.

## <a name="next-steps"></a>Volgende stappen

Zie de [AMQP v1.0-specificatie voor](https://www.amqp.org/sites/amqp.org/files/amqp.pdf)meer informatie over het AMQP-protocol.

Zie voor meer informatie over IoT Hub-berichten:

* [Cloud-to-device berichten](./iot-hub-devguide-messages-c2d.md)
* [Ondersteuning voor aanvullende protocollen](iot-hub-protocol-gateway.md)
* [Ondersteuning voor het Message Queuing Telemetry Transport (MQTT) Protocol](./iot-hub-mqtt-support.md)
