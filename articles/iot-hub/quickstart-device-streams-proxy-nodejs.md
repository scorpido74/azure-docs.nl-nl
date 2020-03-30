---
title: Azure IoT Hub-apparaat streamt Node.js quickstart voor SSH en RDP
description: In deze quickstart voert u een voorbeeld-Node.js-toepassing uit die fungeert als proxy om SSH- en RDP-scenario's in te schakelen via IoT Hub-apparaatstreams.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c372a0a09fd3143f570aa4b316c9191e617c69e2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675448"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Snelstart: SSH en RDP inschakelen via een IoT Hub-apparaatstream met behulp van een Node.js-proxytoepassing (voorbeeld)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

In deze quickstart u Secure Shell -verkeer (SSH) en Rdp-verkeer (Remote Desktop Protocol) via een apparaatstream naar het apparaat sturen. Azure IoT Hub-apparaatstreams stellen service- en apparaattoepassingen in staat om op een veilige en firewallvriendelijke manier te communiceren. Deze quickstart beschrijft de uitvoering van een node.js-proxytoepassing die aan de servicezijde wordt uitgevoerd. Tijdens de openbare preview ondersteunt de Node.js SDK alleen apparaatstreams aan de servicezijde. Als gevolg hiervan heeft deze quickstart instructies nodig om alleen de service-lokale proxy-toepassing uit te voeren.

## <a name="prerequisites"></a>Vereisten

* Voltooiing van [Enable SSH en RDP via IoT Hub-apparaatstreams met behulp van een C-proxytoepassing](./quickstart-device-streams-proxy-c.md) of [Inschakelen SSH en RDP via IoT Hub-apparaatstreams met behulp van een C#-proxytoepassing](./quickstart-device-streams-proxy-csharp.md).

* Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Node.js 10+](https://nodejs.org).

* [Een voorbeeld node.js project](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

U de huidige versie van Node.js op uw ontwikkelingsmachine verifiëren met behulp van de volgende opdracht:

```cmd/sh
node --version
```

Microsoft Azure IoT Hub ondersteunt momenteel apparaatstromen als [voorbeeldfunctie.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!IMPORTANT]
> De preview van apparaatstreams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:
>
> * VS - centraal
> * Centrale EUAP van de VS
> * Europa - noord
> * Azië - zuidoost
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT-extensie toevoegen

Voeg de Azure IoT-extensie voor Azure CLI toe aan uw Cloud Shell-exemplaar door de volgende opdracht uit te voeren. De IoT-extensie voegt dps-specifieke opdrachten (IoT Hub, IoT Edge en IoT Device Provisioning Service) toe aan de Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

U kunt deze stap overslaan als u eerder deze zelfstudie hebt voltooid: [Snelstartgids: Telemetriegegevens vanaf een apparaat verzenden naar een IoT-hub en de telemetriegegevens op de hub lezen met een back-endtoepassing (Python)](quickstart-send-telemetry-node.md).

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Als u Quickstart hebt [voltooid: telemetrie van een apparaat naar een IoT-hub](quickstart-send-telemetry-node.md)verzenden, u deze stap overslaan.

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze sectie gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdracht uit in Cloud Shell als u de identiteit van het apparaat wilt maken:

   > [!NOTE]
   > * Vervang de *Placeholder YourIoTHubName* door de naam die u voor uw IoT-hub hebt gekozen.
   > * Voor de naam van het apparaat dat u registreert, wordt aanbevolen *om MyDevice* te gebruiken zoals weergegeven. Als u een andere naam voor uw apparaat kiest, gebruikt u die naam in dit artikel en werkt u de apparaatnaam bij in de voorbeeldtoepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Om de back-endtoepassing in staat te stellen verbinding te maken met uw IoT-hub en de berichten op te halen, hebt u ook een *tekenreeks voor serviceverbinding*nodig. Met de volgende opdracht wordt de tekenreeks voor uw IoT-hub opgehaald:

   > [!NOTE]
   > Vervang de *Placeholder YourIoTHubName* door de naam die u voor uw IoT-hub hebt gekozen.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

   Let op de tekenreeks geretourneerde serviceverbinding voor later gebruik in deze quickstart. Het lijkt op het volgende voorbeeld:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH naar een apparaat via apparaatstreams

In dit gedeelte stel je een end-to-end stroom vast om ssh-verkeer te tunnelen.

### <a name="run-the-device-local-proxy-application"></a>Voer de proxytoepassing in het apparaat uit

Zoals eerder vermeld, ondersteunt de IoT Hub Node.js SDK alleen apparaatstreams aan de servicezijde. Gebruik voor de apparaatlokale toepassing een apparaatproxytoepassing die beschikbaar is in een van de volgende snelstarts:

   * [SSH en RDP inschakelen via IoT Hub-apparaatstreams met behulp van een C-proxytoepassing](./quickstart-device-streams-proxy-c.md)
   * [SSH en RDP inschakelen via IoT Hub-apparaatstreams met behulp van een C#-proxytoepassing](./quickstart-device-streams-proxy-csharp.md) 

Voordat u doorgaat met de volgende stap, moet u ervoor zorgen dat de apparaatlokale proxy-toepassing wordt uitgevoerd. Zie Voorbeeld van lokale [proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)voor een overzicht van de installatie.

### <a name="run-the-service-local-proxy-application"></a>Voer de proxytoepassing in de service uit

In dit artikel wordt de instelling voor SSH beschreven (met behulp van poort 22) en wordt vervolgens beschreven hoe u de installatie voor RDP (die poort 3389 gebruikt) wijzigen. Omdat apparaatstromen toepassings- en protocolagnostisch zijn, u hetzelfde voorbeeld wijzigen om rekening te houden met andere typen client-servertoepassingsverkeer, meestal door de communicatiepoort te wijzigen.

Als de apparaatlokale proxytoepassing wordt uitgevoerd, voert u de service-lokale proxy-toepassing uit die in Node.js is geschreven door het volgende te doen in een lokaal terminalvenster:

1. Geef voor omgevingsvariabelen uw servicereferenties, de doelapparaat-id waar de SSH-daemon wordt uitgevoerd en het poortnummer voor de proxy die op het apparaat wordt uitgevoerd, op.

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

   Wijzig de tijdelijke aanduiding ServiceConnectionString om aan te passen aan uw tekenreeks voor serviceverbinding en **MyDevice** om overeen te komen met uw apparaat-id als u de uwe een andere naam hebt opgegeven.

1. Navigeer naar `Quickstarts/device-streams-service` de map in de projectmap met uitgepakte uitgepakte map. Gebruik de volgende code om de service-local proxy-toepassing uit te voeren:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH naar uw apparaat via apparaatstreams

Voer in Linux SSH `ssh $USER@localhost -p 2222` uit met behulp van een terminal. Gebruik in Windows uw favoriete SSH-client (bijvoorbeeld PuTTY).

Console-uitvoer op de service-local nadat de SSH-sessie is ingesteld (de service-lokale proxy-toepassing luistert op poort 2222):

![SSH-terminaloutput](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Console-uitvoer van de SSH-clienttoepassing (SSH-client communiceert met SSH-daemon door verbinding te maken met poort 22, waar de service-lokale proxy-toepassing luistert):

![SSH-clientuitvoer](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP naar uw apparaat via apparaatstreams

Gebruik nu uw RDP-clienttoepassing en maak verbinding met de serviceproxy op poort 2222, een willekeurige poort die u eerder hebt gekozen.

> [!NOTE]
> Zorg ervoor dat uw apparaat-proxy correct is geconfigureerd voor RDP met poort 3389.

![De RDP-client maakt verbinding met de service-local proxy-toepassing](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart stelt u een IoT-hub in, registreerde u een apparaat en hebt u een serviceproxytoepassing geïmplementeerd om RDP en SSH in te schakelen op een IoT-apparaat. Het RDP- en SSH-verkeer wordt via een apparaatstroom via de IoT-hub getunneld. Dit proces elimineert de noodzaak van directe verbinding met het apparaat.

Zie voor meer informatie over apparaatstreams:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md)
