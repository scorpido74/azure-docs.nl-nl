---
title: Python-voorbeeldcode voor een IoT Plug and Play Preview-apparaat met meerdere onderdelen koppelen aan IoT Hub | Microsoft Docs
description: Schrijf Python-voorbeeldcode voor een IoT Plug and Play Preview-apparaat dat meerdere onderdelen gebruikt en verbinding maakt met een IoT-hub, en voer deze uit. Gebruik het hulpprogramma Azure IoT Explorer om de gegevens te bekijken die door het apparaat naar de hub worden verzonden.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 571f0e0ceff0adfbf1814abc627fcab6b23acbe1
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905853"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-python"></a>Zelfstudie: Een voorbeeld van een IoT Plug and Play Preview-apparaattoepassing met verschillende onderdelen verbinden met IoT Hub (Python)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

In deze zelfstudie ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing met onderdelen en een hoofdinterface maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u het hulpprogramma Azure IoT Explorer gebruikt om de gegevens weer te geven die naar de hub worden verzonden. De voorbeeldtoepassing wordt geschreven in Python en is inbegrepen in de Azure IoT device-SDK voor Python. Een ontwikkelaar van oplossingen kan het hulpprogramma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u Python 3.7 nodig in uw ontwikkelomgeving. U kunt de nieuwste aanbevolen omgeving voor meerdere platforms downloaden van [python.org](https://www.python.org/). U kunt uw Python-versie controleren met de volgende opdracht:  

```cmd/sh
python --version
```

U kunt de nieuwste aanbevolen omgeving voor meerdere platforms downloaden van [python.org](https://www.python.org/).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Om in het tweede deel van deze zelfstudie te communiceren met het voorbeeldapparaat, gebruikt u het hulpprogramma **Azure IoT Explorer**. [Download en installeer de nieuwste release van Azure IoT Explorer](./howto-use-iot-explorer.md) voor uw besturingssysteem.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _verbindingsreeks voor IoT Hub_ op te halen voor uw hub. Noteer deze verbindingsreeks voor later gebruik in deze zelfstudie:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> U kunt het hulpprogramma Azure IoT Explorer ook gebruiken om de verbindingsreeks voor IoT Hub te vinden.

Voer de volgende opdracht uit om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u aan de hub hebt toegevoegd. Noteer deze verbindingsreeks voor later gebruik in deze zelfstudie:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="set-up-your-environment"></a>Uw omgeving instellen

Dit pakket wordt gepubliceerd als een PIP voor de vernieuwing van de openbare preview. De pakketversie moet de meest recente of `2.1.4` zijn

Installeer het bestand in uw lokale python-omgeving als volgt:

```cmd/sh
pip install azure-iot-device
```

Kloon de IoT-opslagplaats van de Python-SDK en bekijk **pnp-preview-refresh**:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>De code bekijken

Met dit voorbeeld wordt een IoT Plug and Play-temperatuurregelingsapparaat geïmplementeerd. Het model dat met dit voorbeeld wordt geïmplementeerd, maakt gebruik van [meerdere onderdelen](concepts-components.md). Het [Digital Twins Definition Language-modelbestand (DTDL) voor het temperatuurregelingsapparaat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definieert de telemetrie, eigenschappen en opdrachten die het apparaat implementeert.

De map *azure-iot-sdk-python\azure-iot-device\samples\pnp* bevat de voorbeeldcode voor het IoT Plug en Play-apparaat. De bestanden voor het voorbeeld van een temperatuurregeling zijn:

- pnp_temp_controller_with_thermostats.py
- pnp_helper_preview_refresh.py

Temperatuurregeling heeft meerdere onderdelen en een hoofdinterface, gebaseerd op het DTDL-model van de temperatuurregeling.

Open het bestand *pnp_temp_controller_with_thermostats.py* in een editor naar keuze. De code in dit bestand:

1. Importeert `pnp_helper_preview_refresh.py` om toegang te krijgen tot de hulpmethoden.

2. Definieert twee DTMI's (digital twin model identifiers) die twee verschillende interfaces vertegenwoordigen, zoals gedefinieerd in het DTDL-model. De onderdelen van een echte temperatuurregeling moeten deze twee interfaces implementeren. Deze twee interfaces zijn al gepubliceerd in een centrale opslagplaats. Deze DTMI's moeten bekend zijn bij de gebruiker, en variëren in functie van het scenario van de apparaatimplementatie. Voor het huidige voorbeeld vertegenwoordigen deze twee interfaces:

  - Een thermostaat
  - Apparaatgegevens ontwikkeld door Azure.

3. Definieert de DTMI `model_id` voor het apparaat dat wordt geïmplementeerd. De DTMI is gebruikersgedefinieerd en moet overeenkomen met de DTMI in het DTDL-modelbestand.

4. Definieert de namen voor de onderdelen in het DTDL-bestand. De DTDL bevat twee thermostaten en één onderdeel met apparaatgegevens. Er wordt ook een constante met de naam `serial_number` gedefinieerd in de hoofdinterface. Een `serial_number` kan niet veranderen voor apparaat.

5. Definieert de implementaties van opdrachthandlers. Deze bepalen wat het apparaat doet wanneer het opdrachtaanvragen ontvangt.

6. Definieert functies om een opdrachtreactie te maken. Deze bepalen hoe het apparaat reageert op opdrachtaanvragen. U maakt functies voor opdrachtreacties als een opdracht een aangepaste reactie moet terugsturen naar de IoT-hub. Als er geen antwoordfunctie is opgegeven voor een opdracht, wordt een algemeen antwoord verzonden. In dit voorbeeld heeft alleen de **getMaxMinReport**-opdracht een aangepast antwoord.

7. Definieert een functie om telemetrie te verzenden vanaf dit apparaat. Zowel de thermostaten als de hoofdinterface verzenden telemetrie. Deze functie bevat een optionele parameter voor de onderdeelnaam om te kunnen bepalen welk onderdeel de telemetrie heeft verzonden.

8. Definieert een listener voor opdrachtverzoeken.

9. Definieert een listener voor gewenste updates van eigenschappen.

10. Heeft een `main`-functie die:

    1. De apparaat-SDK gebruikt om een apparaatclient te maken en verbinding te maken met uw IoT-hub. Het apparaat verzendt de `model_id` zodat de IoT-hub het apparaat kan identificeren als een IoT Plug en Play-apparaat.

    1. Maakt gebruik van de functie `create_reported_properties` in het hulpbestand om de eigenschappen te maken. Geeft de onderdeelnaam en de eigenschappen door aan deze functie als sleutel-/waardeparen.

    1. Werkt de leesbare eigenschappen voor zijn onderdelen bij door `patch_twin_reported_properties` aan te roepen.

    1. Begint te luisteren naar opdrachtaanvragen met de functie `execute_command_listener`. De functie stelt een listener in voor opdrachtverzoeken vanaf de service. Wanneer u de listener instelt, geeft u een `method_name`, `user_command_handler` en een optionele `create_user_response_handler` in als parameters.
        - De `method_name` definieert de opdrachtaanvraag. In dit voorbeeld definieert het model de opdrachten **reboot** en **getMaxMinReport**.
        - De functie `user_command_handler` definieert wat het apparaat moet doen wanneer het een opdracht ontvangt.
        - De functie `create_user_response_handler` maakt een antwoord aan dat naar uw IoT-hub verzonden kan worden wanneer een opdracht met succes wordt uitgevoerd. U kunt dit antwoord bekijken in de portal. Als deze functie niet wordt opgegeven, dan wordt een algemeen antwoord verzonden naar de service.

    1. Gebruikt `execute_property_listener` om naar updates van eigenschappen te luisteren.

    1. Begint telemetrie te verzenden met `send_telemetry`. De voorbeeldcode gebruikt een lus om drie functies voor het verzenden van telemetrie aan te roepen. Ze worden elk om de acht seconden aangeroepen

    1. Schakelt alle listeners en taken uit, en sluit de lus af wanneer u op **Q** of **q** drukt.

Nu dat u de code heeft gezien, maakt u een omgevingsvariabele genaamd **IOTHUB_DEVICE_CONNECTION_STRING** om de apparaatverbindingsreeks op te slaan die u eerder hebt genoteerd. Gebruik de volgende opdracht om het voorbeeld uit te voeren:

```cmd/sh
python pnp_temp_controller_with_thermostats.py
```

Het voorbeeldapparaat verzendt om de paar seconden telemetrieberichten naar uw IoT-hub.

U ziet de volgende uitvoer, wat aangeeft dat het apparaat telemetriegegevens naar de hub stuurt en nu klaar is om opdrachten en updates van eigenschappen te ontvangen.

![Bevestigingsberichten apparaat](media/tutorial-multiple-components-python/multiple-component.png)

Laat het voorbeeld actief tijdens het uitvoeren van de volgende stappen.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Code valideren met Azure IoT Explorer

Nadat het voorbeeld van de apparaatclient is gestart, gebruikt u het hulpprogramma Azure IoT Explorer om te verifiëren dat het werkt.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een IoT Plug and Play-apparaat met onderdelen kunt verbinden met een IoT-hub. Voor meer informatie over IoT Plug and Play-apparaatmodellen raadpleegt u:

> [!div class="nextstepaction"]
> [Handleiding voor ontwikkelaars van IoT Plug en Play-previewmodellen](concepts-developer-guide.md)
