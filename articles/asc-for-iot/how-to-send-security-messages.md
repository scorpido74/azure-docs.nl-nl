---
title: Apparaatbeveiligingsberichten verzenden
description: Meer informatie over het verzenden van uw beveiligingsberichten met Azure Security Center voor IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 4877493982671b1b5db686715ef854f25c2966ea
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310982"
---
# <a name="send-security-messages-sdk"></a>Beveiligingsberichten SDK verzenden

In deze handleiding worden de mogelijkheden van het Azure Security Center for IoT-service uitgelegd wanneer u ervoor kiest om beveiligingsberichten van uw apparaat te verzamelen en te verzenden zonder een Azure Security Center voor IoT-agent te gebruiken en wordt uitgelegd hoe u dit doet.

In deze handleiding leert u het volgende:

> [!div class="checklist"]
> * Beveiligingsberichten verzenden met de Azure IoT C SDK
> * Beveiligingsberichten verzenden met de Azure IoT C# SDK
> * Beveiligingsberichten verzenden met de Azure IoT Python SDK
> * Beveiligingsberichten verzenden met de Azure IoT Node.js SDK
> * Beveiligingsberichten verzenden met de Azure IoT Java SDK

## <a name="azure-security-center-for-iot-capabilities"></a>Azure Security Center voor IoT-mogelijkheden

Azure Security Center for IoT kan elke vorm van beveiligingsberichtgegevens verwerken en analyseren, zolang de verzonden gegevens voldoen aan het [Azure Security Center for IoT-schema](https://aka.ms/iot-security-schemas) en het bericht is ingesteld als een beveiligingsbericht.

## <a name="security-message"></a>Beveiligingsbericht

Azure Security Center for IoT definieert een beveiligingsbericht aan de hand van de volgende criteria:

- Als het bericht is verzonden met Azure IoT SDK
- Als het bericht voldoet aan het [beveiligingsberichtschema](https://aka.ms/iot-security-schemas)
- Als het bericht is ingesteld als een beveiligingsbericht voordat het

Elk beveiligingsbericht bevat de metagegevens `AgentVersion` `MessageSchemaVersion` van de afzender, zoals `AgentId`, en een lijst met beveiligingsgebeurtenissen.
Het schema definieert de geldige en vereiste eigenschappen van het beveiligingsbericht, inclusief de typen gebeurtenissen.

> [!NOTE]
> Verzonden berichten die niet aan het schema voldoen, worden genegeerd. Zorg ervoor dat u het schema verifieert voordat u de verzendende gegevens start, omdat genegeerde berichten momenteel niet zijn opgeslagen.

> [!NOTE]
> Berichten die niet zijn ingesteld als een beveiligingsbericht met de Azure IoT SDK, worden niet doorgestuurd naar de Azure Security Center for IoT-pijplijn.

## <a name="valid-message-example"></a>Voorbeeld van geldig bericht

In het onderstaande voorbeeld ziet u een geldig object met een beveiligingsbericht. Het voorbeeld bevat de `ProcessCreate` metagegevens van het bericht en één beveiligingsgebeurtenis.

Zodra dit bericht is ingesteld als een beveiligingsbericht en is verzonden, wordt dit bericht verwerkt door Azure Security Center for IoT.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Beveiligingsberichten verzenden

Verstuur beveiligingsberichten *zonder* azure security center voor IoT-agent te gebruiken, met behulp van de [Azure IoT C-apparaat SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview), [Azure IoT C#-apparaat SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview), Azure [IoT Node.js SDK,](https://github.com/Azure/azure-iot-sdk-node) [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python)of Azure [IoT Java SDK](https://github.com/Azure/azure-iot-sdk-java).

Als u de apparaatgegevens van uw apparaten wilt verzenden voor verwerking door Azure Security Center for IoT, gebruikt u een van de volgende API's om berichten te markeren voor de juiste routering naar Azure Security Center voor IoT-verwerkingspijplijn.

Alle gegevens die worden verzonden, zelfs als ze zijn gemarkeerd met de juiste koptekst, moeten ook voldoen aan het [Azure Security Center for IoT-berichtschema.](https://aka.ms/iot-security-schemas)

### <a name="send-security-message-api"></a>API voor beveiligingsbericht verzenden

De API **voor beveiligingsberichten verzenden** is momenteel beschikbaar in C en C#, Python, Node.js en Java.

#### <a name="c-api"></a>C-API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {

    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;

    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);

    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }

    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }

    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }

cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }

    return success;
}

static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }

    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

#### <a name="c-api"></a>C# API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="nodejs-api"></a>Node.js API

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>Python-API

Als u de Python API wilt gebruiken, moet u het [azure-iot-apparaat](https://pypi.org/project/azure-iot-device/)van het pakket installeren.

Wanneer u de Python API gebruikt, u het beveiligingsbericht via de module of via het apparaat verzenden met behulp van de unieke apparaat- of moduleverbindingstekenreeks. Gebruik **IoTHubDeviceClient**en met een module bij het gebruik van het volgende Voorbeeld van Python-script met een apparaat **IoTHubDeviceClient**.

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>Java-API

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```

## <a name="next-steps"></a>Volgende stappen

- Lees het [overzicht](overview.md) van de Azure Security Center for IoT-service
- Meer informatie over Azure Security Center voor [IoT-architectuur](architecture.md)
- De [service inschakelen](quickstart-onboard-iot-hub.md)
- Lees de [faq](resources-frequently-asked-questions.md)
- Meer informatie over hoe u toegang krijgt tot [ruwe beveiligingsgegevens](how-to-security-data-access.md)
- Aanbevelingen [begrijpen](concept-recommendations.md)
- Inzicht in [waarschuwingen](concept-security-alerts.md)
