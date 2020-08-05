---
title: Python-voorbeeldcode voor een IoT Plug and Play Preview-apparaat koppelen aan Azure IoT Hub
description: Lees hoe u met behulp van Python voorbeeldcode voor een IoT Plug and Play Preview-apparaat schrijft en uitvoert die verbinding maakt met een IoT-hub. Gebruik het hulpprogramma Azure IoT Explorer om de gegevens te bekijken die door het apparaat naar de hub worden verzonden.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 84ef7ff18c294097da20640c1de237b41900cb40
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352631"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-python"></a>Quickstart: Een voorbeeld van een IoT Plug and Play Preview-apparaattoepassing verbinden met IoT Hub (Python)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

In deze quickstart ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u het hulpprogramma Azure IoT Explorer gebruikt om de telemetrie weer te geven die wordt verzonden. De voorbeeldtoepassing wordt geschreven voor Python en is inbegrepen in de Azure IoT Hub Device SDK for Python. Een ontwikkelaar van oplossingen kan het hulpprogramma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Als u deze quickstart wilt uitvoeren, moet Python 3.7 op uw ontwikkelcomputer zijn geïnstalleerd. U kunt de nieuwste aanbevolen omgeving voor meerdere platforms downloaden van [python.org](https://www.python.org/). U kunt uw Python-versie controleren met de volgende opdracht:  

```cmd/sh
python --version
```

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Om in het tweede deel van deze quickstart te communiceren met het voorbeeldapparaat, gebruikt u het hulpprogramma **Azure IoT Explorer**. [Download en installeer de nieuwste release van Azure IoT Explorer](./howto-use-iot-explorer.md) voor uw besturingssysteem.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _verbindingsreeks voor IoT Hub_ op te halen voor uw hub. Noteer deze verbindingsreeks voor later gebruik in deze quickstart:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> U kunt het hulpprogramma Azure IoT Explorer ook gebruiken om de verbindingsreeks voor IoT Hub te vinden.

Voer de volgende opdracht uit om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u aan de hub hebt toegevoegd. Noteer deze verbindingsreeks voor later gebruik in deze quickstart:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="set-up-your-environment"></a>Uw omgeving instellen

Dit pakket wordt gepubliceerd als een PIP voor de vernieuwing van de openbare preview. De pakketversie moet de meest recente of `2.1.4` zijn

Installeer het bestand in uw lokale Python-omgeving als volgt:

```cmd/sh
pip install azure-iot-device
```

Kloon de IoT-opslagplaats van de Python-SDK en bekijk **master**:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

De map *azure-iot-sdk-python\azure-iot-device\samples\pnp* bevat de voorbeeldcode voor het IoT Plug en Play-apparaat. In deze quickstart wordt het bestand *pnp_thermostat. py* gebruikt. Met deze voorbeeldcode wordt een apparaat geïmplementeerd dat compatibel is met IoT Plug. In de voorbeeldcode wordt gebruikgemaakt van de clientbibliotheek van het Azure IoT Python-apparaat.

Maak een omgevingsvariabele genaamd **IOTHUB_DEVICE_CONNECTION_STRING** om de apparaatverbindingsreeks op te slaan die u eerder hebt genoteerd.

Open het bestand **pnp_thermostat. py** in een teksteditor. U ziet het volgende:

1. Het bestand definieert één dubbele model-id (DTMI) van een apparaat die een unieke aanduiding vormt voor de [thermostaat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json). Er moet een DTMI bekend zijn bij de gebruiker, afhankelijk van het scenario voor de implementatie van het apparaat. In het huidige voorbeeld representeert het model een thermostaat met telemetrie, eigenschappen en opdrachten die zijn gekoppeld aan het bewaken van de temperatuur.

1. Bevat functies voor het definiëren van implementaties van opdrachthandlers. U schrijft deze handlers om te definiëren hoe het apparaat reageert op opdrachtaanvragen.

1. Heeft een functie om een opdrachtrespons te definiëren. U kunt een opdrachtrespons maken om een antwoord terug te sturen naar uw IoT-hub.

1. Hiermee wordt een functie voor het invoeren van toetsenbordaanslagen gedefinieerd, waarmee u de toepassing kunt afsluiten.

1. Heeft een **hoofdfunctie**. De **hoofdfunctie**:

    1. De apparaat-SDK gebruikt om een apparaatclient te maken en verbinding te maken met uw IoT-hub.

    1. Werkt de eigenschappen bij. Het model dat we gebruiken, **Thermostaat**, definieert `targetTemperature` en `maxTempSinceLastReboot` als de twee eigenschappen voor onze Thermostaat en zullen dus worden gebruikt. Eigenschappen worden bijgewerkt met behulp van de methode `patch_twin_reported_properties` die wordt gedefinieerd op `device_client`.

    1. Begint te luisteren naar opdrachtaanvrage. Hiervoor wordt gebruikgemaakt van de functie **execute_command_listener**. De functie stelt een listener in om naar opdrachtverzoeken te luisteren die afkomstig zijn van de service. Wanneer u de listener instelt, geeft u een `method_name`, `user_command_handler` en `create_user_response_handler` op. 
        - De functie `user_command_handler` definieert wat het apparaat moet doen wanneer het een opdracht ontvangt. Als uw wekker bijvoorbeeld afgaat, is het effect van deze opdracht dat u wakker wordt. Dit kunt u zien als het effect van de opdracht die wordt aangeroepen.
        - De functie `create_user_response_handler` maakt een antwoord aan dat naar uw IoT-hub verzonden kan worden wanneer een opdracht met succes wordt uitgevoerd. Als uw wekker bijvoorbeeld afgaat, reageert u door op Snooze te drukken, wat feedback is voor de service. U kunt dit zien als het antwoord dat u de service geeft. U kunt dit antwoord bekijken in de portal.

    1. Begint telemetrie te verzenden. De **pnp_send_telemetry** is gedefinieerd in het bestand pnp_methods.py. De voorbeeldcode gebruikt een lus om deze functie elke acht seconden aan te roepen.

    1. Schakelt alle listeners en taken uit, en sluit de lus af wanneer u op **Q** of **q** drukt.

Nu u de code hebt gezien, gebruikt u de volgende opdracht om het voorbeeld uit te voeren:

```cmd/sh
python pnp_thermostat.py
```

U ziet de volgende uitvoer, wat aangeeft dat het apparaat telemetriegegevens naar de hub stuurt en nu klaar is om opdrachten en updates van eigenschappen te ontvangen:

```cmd/sh
Connecting using Connection String HostName=<your hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<your device shared access key>
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

Laat het voorbeeld actief tijdens het uitvoeren van de volgende stappen.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Code valideren met Azure IoT Explorer

Nadat het voorbeeld van de apparaatclient is gestart, gebruikt u het hulpprogramma Azure IoT Explorer om te verifiëren dat het werkt.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een IoT Plug and Play-apparaat kunt verbinden met een IoT-hub. Als u meer wilt weten over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten, leest u:

> [!div class="nextstepaction"]
> [Interactie met een IoT Plug en Play Preview-apparaat dat is verbonden met uw oplossing](quickstart-service-python.md)
