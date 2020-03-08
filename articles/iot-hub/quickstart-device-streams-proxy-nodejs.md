---
title: Quick start voor Azure IoT Hub Device streams node. js voor SSH en RDP
description: In deze Quick Start voert u een node. js-voorbeeld toepassing uit die fungeert als een proxy om SSH-en RDP-scenario's in te scha kelen via IoT Hub apparaten.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c372a0a09fd3143f570aa4b316c9191e617c69e2
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675448"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Quick Start: SSH en RDP via een stroom van een IoT Hub apparaat inschakelen met behulp van een node. js-proxy toepassing (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

In deze Quick Start schakelt u het verkeer van Secure Shell (SSH) en Remote Desktop Protocol (RDP) in voor verzen ding naar het apparaat via een apparaatgegevens stroom. Met Azure IoT Hub-apparaatklassen kunnen service-en apparaat-apps communiceren op een veilige en firewall vriendelijke manier. In deze Quick Start wordt de uitvoering van een node. js-proxy toepassing beschreven die aan de service zijde wordt uitgevoerd. Tijdens de open bare preview ondersteunt de node. js SDK alleen Device streams aan de kant van de service. Als gevolg hiervan wordt in deze Snelstartgids beschreven hoe alleen de service-Local proxy-toepassing wordt uitgevoerd.

## <a name="prerequisites"></a>Vereisten

* Voltooiing van het [inschakelen van SSH en RDP via IOT hub apparaat streams met behulp van een C-proxy toepassing](./quickstart-device-streams-proxy-c.md) of [Schakel SSH en RDP in C# IOT hub via een proxy toepassing](./quickstart-device-streams-proxy-csharp.md).

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node. js 10 +](https://nodejs.org).

* [Een voor beeld van een node. js-project](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

U kunt de huidige versie van node. js op uw ontwikkel computer controleren met behulp van de volgende opdracht:

```cmd/sh
node --version
```

Microsoft Azure IoT Hub biedt momenteel ondersteuning voor het streamen van apparaten als een [Preview-functie](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> De preview van Device streams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:
>
> * VS - centraal
> * Centrale VS-EUAP
> * Europa - noord
> * Azië - zuidoost
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT-extensie toevoegen

Voeg de Azure IoT-extensie voor Azure CLI toe aan uw Cloud Shell-exemplaar door de volgende opdracht uit te voeren. De IoT-extensie voegt IoT Hub, IoT Edge en IoT-specifieke opdrachten (Device Provisioning Service) toe aan de Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

U kunt deze stap overslaan als u eerder deze zelfstudie hebt voltooid: [Snelstartgids: Telemetriegegevens vanaf een apparaat verzenden naar een IoT-hub en de telemetriegegevens op de hub lezen met een back-endtoepassing (Node.js)](quickstart-send-telemetry-node.md).

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Als u de [Snelstartgids hebt voltooid: telemetrie van een apparaat naar een IOT-hub verzenden](quickstart-send-telemetry-node.md), kunt u deze stap overs Laan.

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze sectie gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Als u de apparaat-id wilt maken, voert u de volgende opdracht uit in Cloud Shell:

   > [!NOTE]
   > * Vervang de tijdelijke aanduiding *YourIoTHubName* door de naam die u hebt gekozen voor uw IOT-hub.
   > * Voor de naam van het apparaat dat u wilt registreren, is het raadzaam om *mijn* te gebruiken zoals wordt weer gegeven. Als u een andere naam kiest voor uw apparaat, gebruikt u die naam in dit artikel en werkt u de apparaatnaam bij in de voorbeeld toepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Als u wilt dat de back-endtoepassing verbinding maakt met uw IoT-hub en de berichten ophaalt, hebt u ook een *service connection string*nodig. Met de volgende opdracht wordt de teken reeks voor uw IoT-hub opgehaald:

   > [!NOTE]
   > Vervang de tijdelijke aanduiding *YourIoTHubName* door de naam die u hebt gekozen voor uw IOT-hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

   Bekijk de geretourneerde service connection string voor later gebruik in deze Quick Start. Het lijkt op het volgende voorbeeld:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH naar een apparaat via apparaatstreams

In deze sectie maakt u een end-to-end-stroom voor het tunnelen van SSH-verkeer.

### <a name="run-the-device-local-proxy-application"></a>Voer de proxytoepassing in het apparaat uit

Zoals eerder vermeld, ondersteunt de IoT Hub node. js SDK alleen Device streams aan de kant van de service. Gebruik voor de apparaat-Local toepassing een apparaat proxy toepassing die beschikbaar is in een van de volgende Quick starts:

   * [SSH-en RDP-apparaten via IoT Hub apparaat-streams inschakelen met behulp van een C-proxy toepassing](./quickstart-device-streams-proxy-c.md)
   * [SSH en RDP via IoT Hub apparaat-streams inschakelen met C# behulp van een proxy toepassing](./quickstart-device-streams-proxy-csharp.md) 

Voordat u verdergaat met de volgende stap, moet u ervoor zorgen dat de toepassing voor de lokale proxy van het apparaat wordt uitgevoerd. Zie voor een overzicht van de installatie [lokale proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)-voor beeld.

### <a name="run-the-service-local-proxy-application"></a>Voer de proxytoepassing in de service uit

In dit artikel wordt de installatie van SSH (met behulp van poort 22) beschreven en wordt beschreven hoe u de installatie van RDP (die gebruikmaakt van poort 3389) wijzigt. Omdat apparaatversleuteling het toepassings-en protocol-neutraal zijn, kunt u hetzelfde voor beeld wijzigen om andere typen client-server toepassingen verkeer te bieden, meestal door de communicatie poort te wijzigen.

Met de toepassing voor het uitvoeren van een apparaat-lokale proxy voert u de service-Local proxy-toepassing uit die is geschreven in node. js door de volgende handelingen uit te voeren in een lokaal Terminal venster:

1. Geef voor omgevings variabelen uw service referenties op, de doel apparaat-ID waar de SSH-daemon wordt uitgevoerd en het poort nummer van de proxy die op het apparaat wordt uitgevoerd.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   Wijzig de tijdelijke aanduiding ServiceConnectionString zodat deze overeenkomt met uw service connection string en **mijn** overeenkomen met uw apparaat-id als u een andere naam hebt opgegeven.

1. Navigeer naar de map `Quickstarts/device-streams-service` in de map ungezipte project. Gebruik de volgende code om de service-Local proxy-toepassing uit te voeren:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH naar uw apparaat via apparaatstreams

Voer in Linux SSH uit met behulp van `ssh $USER@localhost -p 2222` op een Terminal. Gebruik in Windows uw favoriete SSH-client (bijvoorbeeld PuTTy).

Console-uitvoer op de service-Local na SSH-sessie wordt tot stand gebracht (de service-Local proxy-toepassing luistert op poort 2222):

![SSH-terminal uitvoer](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Console-uitvoer van de SSH-client toepassing (SSH-client communiceert met SSH-daemon door verbinding te maken met poort 22, waarbij de toepassing service-Local proxy luistert):

![SSH-client uitvoer](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP naar uw apparaat via apparaatstreams

Gebruik nu uw RDP-client toepassing en maak verbinding met de Service proxy op poort 2222, een wille keurige poort die u eerder hebt gekozen.

> [!NOTE]
> Zorg ervoor dat uw apparaat-proxy correct is geconfigureerd voor RDP met poort 3389.

![De RDP-client maakt verbinding met de service-lokale proxy toepassing](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids stelt u een IoT-hub in, registreert u een apparaat en implementeert u een Service proxy toepassing om RDP en SSH in te scha kelen op een IoT-apparaat. Het RDP-en SSH-verkeer wordt via de IoT-hub getunneld via een apparaat stroom. Dit proces elimineert de nood zaak van rechtstreekse verbinding met het apparaat.

Zie voor meer informatie over het streamen van apparaten:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md)
