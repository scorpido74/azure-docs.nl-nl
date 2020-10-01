---
title: Verbinding maken met een IoT Plug en Play Bridge-voor beeld dat wordt uitgevoerd in Linux of Windows naar een IoT-hub | Microsoft Docs
description: Maak een IoT-Plug en Play-brug op Linux of Windows en voer deze uit om verbinding te maken met een IoT-hub. Gebruik het hulpprogramma Azure IoT Explorer om de gegevens te bekijken die door het apparaat naar de hub worden verzonden.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4c84fbb131809c92d597b46d18c53e46b636f8ef
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613472"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Verbinding maken met een IoT Plug en Play Bridge-voor beeld dat wordt uitgevoerd in Linux of Windows naar IoT Hub

In deze procedure ziet u hoe u de voor beeld-omgeving van de IoT Plug en Play-brug bouwt, verbinding maakt met uw IoT-hub en het Azure IoT Explorer-hulp programma gebruikt om de telemetrie-verzen ding weer te geven. De IoT Plug en Play-brug wordt geschreven in C en bevat de Azure IoT Device SDK voor C. Aan het einde van deze zelf studie moet u de IoT Plug en Play-brug kunnen uitvoeren en de telemetrie van het rapport in azure IoT Explorer bekijken: :::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="een scherm opname waarin Azure IOT Explorer wordt weer gegeven met een tabel met gerapporteerde telemetrie (vochtigheid, Tempe ratuur) van IOT-Plug en Play Bridge.":::

## <a name="prerequisites"></a>Vereisten

U kunt deze quickstart uitvoeren in Linux of Windows. De shell-opdrachten in deze hand leiding volgen de Windows-Conventie voor pad scheidings tekens ' `\` ', als u de volgende stap uitvoert in Linux: Zorg ervoor dat u deze scheidings tekens verwisselt voor `/` .

De vereisten verschillen per besturingssysteem:

### <a name="linux"></a>Linux

In deze quickstart wordt ervan uitgegaan dat u Ubuntu Linux gebruikt. De stappen in deze quickstart zijn getest met Ubuntu 18.04.

Om deze quickstart in Linux te voltooien, installeert u de volgende software in uw lokale Linux-omgeving:

Installeer **GCC**, **Git**, **cmake**en alle vereiste afhankelijkheden met behulp van de opdracht `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Controleer of de versie van `cmake` hoger is dan **2.8.12** en of de versie van **GCC** hoger dan **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Om deze quickstart in Windows te voltooien, installeert u de volgende software in uw lokale Windows-omgeving:

* [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/): zorg ervoor dat u de workload **Desktop Development with C++** kiest tijdens het [installeren](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019&preserve-view=true) van Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Om in het tweede deel van deze quickstart te communiceren met het voorbeeldapparaat, gebruikt u het hulpprogramma **Azure IoT Explorer**. [Download en installeer de nieuwste release van Azure IoT Explorer](./howto-use-iot-explorer.md) voor uw besturingssysteem.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IoT Hub Connection String_ voor uw hub op te halen. Noteer deze verbindingsreeks voor later gebruik in deze quickstart:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> U kunt het hulpprogramma Azure IoT Explorer ook gebruiken om de verbindingsreeks voor IoT Hub te vinden.

Voer de volgende opdracht uit om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u aan de hub hebt toegevoegd. Noteer deze verbindingsreeks voor later gebruik in deze quickstart:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="view-the-model"></a>Het model weer geven

U gebruikt Azure IoT Explorer in latere stappen om het apparaat weer te geven wanneer dit verbinding maakt met uw IoT-hub. Azure IoT Explorer heeft een lokale kopie van het model bestand nodig dat overeenkomt met de **model-id** die uw apparaat verzendt. Met het model bestand kunnen IoT Explorer de telemetrie, eigenschappen en opdrachten weer geven die uw apparaat implementeert.

Wanneer u de code in de onderstaande stap downloadt, bevat de voorbeeld model bestanden in de `pnpbridge/docs/schema` map. Azure IoT Explorer voorbereiden:

1. Maak een map *models* op uw lokale computer.
1. EnvironmentalSensor.jsweer geven [op](https://aka.ms/iot-pnp-bridge-env-model) en het JSON-bestand opslaan in de map *modellen*
1. Bekijk [RootBridgeSampleDevice.jsop](https://aka.ms/iot-pnp-bridge-root-model) en sla het JSON-bestand op in de map *modellen* .

## <a name="download-the-code"></a>De code downloaden

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de [IoT Plug en Play](https://aka.ms/iotplugandplaybridge) -github-opslag plaats naar deze locatie te klonen:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Nadat u de IoT Plug en Play Bridge opslag plaats naar uw computer hebt gekloond, opent u een opdracht prompt van de beheer opdracht en navigeert u naar de map van de gekloonde opslag plaats:

```cmd
cd pnpbridge
git submodule update --init --recursive
```

Deze bewerking kan enkele minuten in beslag nemen.

>[!NOTE]
> Als u problemen ondervindt met het uitvoeren van een update van de Git-kloon submodule, is dit een bekend probleem met Windows-bestands paden en git Zie: https://github.com/msysgit/git/pull/110 . U kunt de volgende opdracht proberen om het probleem op te lossen: `git config --system core.longpaths true`

## <a name="setting-up-the-configuration-json"></a>De configuratie-JSON instellen

Nadat u de IoT Plug en Play Bridge opslag plaats naar uw computer hebt gekopieerd, gaat u naar de `pnpbridge/docs/schema` map van de gekloonde opslag plaats waar u de [configuratie-JSON](https://aka.ms/iot-pnp-bridge-env-config) kunt vinden of `config.json` voor het omgevings voorbeeld van de brug. Meer informatie over de configuratie bestanden vindt u in het [document IoT Plug en Play Bridge-concepten](concepts-iot-pnp-bridge.md).

Voor het `root-_interface_model_id` veld moet u de IoT Plug en Play model-id kopiëren die het model voor uw apparaat identificeert. In dit voorbeeld is het `dtmi:com:example:SampleDevice;1`. Wijzig de volgende para meters onder **pnp_bridge_parameters** knoop punt in het `config.json` bestand in de:

  Verbindings reeks gebruiken (Opmerking: de symmetric_key moeten overeenkomen met de SAS-sleutel in de connection string):

  ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "[To fill in]",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
  ```

 Wanneer u het bestand hebt ingevuld, moet het er als volgt `config.json` uitzien:

   ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "dtmi:com:example:SampleDevice;1",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
```

 Wanneer u de brug hebt gemaakt, moet u deze `config.json` in dezelfde map als de Bridge plaatsen of het pad van de Bridge opgeven wanneer deze wordt uitgevoerd.

## <a name="build-the-iot-plug-and-play-bridge"></a>De IoT Plug en Play-brug bouwen

Ga naar de map *pnpbridge* in de map opslag plaats.

Voor Windows voert u het volgende uit in een [opdracht prompt voor ontwikkel aars voor Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs):

```cmd
cd scripts\windows
build.cmd
```

Net als voor Linux voert u het volgende uit:

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>In Windows kunt u de oplossing openen die is gegenereerd door de opdracht cmake in Visual Studio 2019. Open het project bestand *azure_iot_pnp_bridge. SLN* in de map cmake en stel het *pnpbridge_bin* project in als het opstart project in de oplossing. U kunt het voorbeeld nu in Visual Studio bouwen en uitvoeren in foutopsporingsmodus.

## <a name="start-the-iot-plug-and-play-bridge"></a>De IoT Plug en Play-brug starten

 Start het voor beeld van de IoT Plug en Play-brug voor omgevings sensoren door te navigeren naar de map *pnpbridge* en het volgende in een opdracht prompt uit te voeren:

```bash
 cd cmake/pnpbridge_x86/src/adaptors/samples/environmental_sensor/
./pnpbridge_environmentalsensor

```

```cmd
REM Windows
cd cmake\pnpbridge_x86\src\adaptors\samples\environmental_sensor
Debug\pnpbridge_environmentalsensor.exe
```

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Code valideren met Azure IoT Explorer

Nadat het voorbeeld van de apparaatclient is gestart, gebruikt u het hulpprogramma Azure IoT Explorer om te verifiëren dat het werkt.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een IoT Plug and Play-apparaat kunt verbinden met een IoT-hub. Als u meer wilt weten over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten, leest u:

* [Wat is IoT Plug en Play Bridge](./concepts-iot-pnp-bridge.md)
* [Zie de naslag informatie voor GitHub-ontwikkel aars voor IoT Plug en Play-brug](https://aka.ms/iot-pnp-bridge-dev-doc)
* [IoT Plug en Play-brug op GitHub](https://aka.ms/iotplugandplaybridge)
