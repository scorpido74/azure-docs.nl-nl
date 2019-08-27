---
title: Snelstartgids voor Azure IoT Hub Device streams node. js voor SSH en RDP (preview) | Microsoft Docs
description: In deze Quick Start voert u een node. js-voorbeeld toepassing uit die fungeert als een proxy om SSH-en RDP-scenario's in te scha kelen via IoT Hub apparaten.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 83339273d9161c3947df191d10e788980db39b28
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "67445970"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Quickstart: Schakel SSH en RDP via een stroom van een IoT Hub apparaat in met behulp van een node. js-proxy toepassing (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub biedt momenteel ondersteuning voor het streamen van apparaten als een [Preview-functie](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-apparaatstreams](./iot-hub-device-streams-overview.md) zorgen ervoor dat service- en apparaattoepassingen kunnen communiceren op een beveiligde manier die de firewall toestaat. 

In deze Quick Start wordt de uitvoering beschreven van een node. js-proxy toepassing die wordt uitgevoerd aan de service kant, zodat het verkeer van Secure Shell (SSH) en Remote Desktop Protocol (RDP) kan worden verzonden naar het apparaat via een apparaatgegevens stroom. Zie voor een overzicht van de installatie [lokale proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)-voor beeld. 

Tijdens de open bare preview ondersteunt de node. js SDK alleen Device streams aan de kant van de service. Als gevolg hiervan wordt in deze Snelstartgids beschreven hoe alleen de service-Local proxy-toepassing wordt uitgevoerd. Zie voor het uitvoeren van de toepassing apparaat-lokale proxy:  

   * [SSH-en RDP-apparaten via IoT Hub apparaat-streams inschakelen met behulp van een C-proxy toepassing](./quickstart-device-streams-proxy-c.md)
   * [SSH en RDP via IoT Hub apparaat-streams inschakelen met C# behulp van een proxy toepassing](./quickstart-device-streams-proxy-csharp.md)

In dit artikel wordt de installatie van SSH (met behulp van poort 22) beschreven en wordt beschreven hoe u de installatie van RDP (die gebruikmaakt van poort 3389) wijzigt. Omdat apparaatversleuteling het toepassings-en protocol-neutraal zijn, kunt u hetzelfde voor beeld wijzigen om andere typen client-server toepassingen verkeer te bieden, meestal door de communicatie poort te wijzigen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* De preview van Device streams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:

  * US - centraal
  * US - centraal EUAP

* Als u de service-Local-toepassing in deze Quick Start wilt uitvoeren, moet u node. js V10 toevoegen. x. x of hoger op uw ontwikkel machine hebben.
  * Down load [node. js](https://nodejs.org) voor meerdere platforms.
  * Controleer de huidige versie van node. js op uw ontwikkel computer met behulp van de volgende opdracht:

   ```
   node --version
   ```

* Voeg de Azure IoT-extensie voor Azure CLI toe aan uw Cloud Shell-exemplaar door de volgende opdracht uit te voeren. De IOT-extensie voegt IoT Hub, IoT Edge en IoT-specifieke opdrachten (Device Provisioning Service) toe aan de Azure CLI.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* Als u dit nog niet hebt gedaan, [downloadt u het voor beeld node. js-project](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip) en extraheert u het zip-archief.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

Als u [Snelstart: Als u telemetrie vanaf een apparaat wilt verzenden naar een IoT-hub](quickstart-send-telemetry-node.md), kunt u deze stap overslaan.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Als u klaar [bent met Quick Start: Als u telemetrie vanaf een apparaat wilt verzenden naar een IoT-hub](quickstart-send-telemetry-node.md), kunt u deze stap overslaan.

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze sectie gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Als u de apparaat-id wilt maken, voert u de volgende opdracht uit in Cloud Shell:

   > [!NOTE]
   > * Vervang de tijdelijke aanduiding *YourIoTHubName* door de naam die u voor uw IOT-hub hebt gekozen.
   > * Gebruik *mijn*, zoals wordt weer gegeven. Dit is de naam die is opgegeven voor het geregistreerde apparaat. Als u een andere naam kiest voor uw apparaat, gebruikt u die naam in dit artikel en werkt u de apparaatnaam bij in de voorbeeld toepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Als u wilt dat de back-endtoepassing verbinding maakt met uw IoT-hub en de berichten ophaalt, hebt u ook een *service connection string*nodig. Met de volgende opdracht wordt de teken reeks voor uw IoT-hub opgehaald:

   > [!NOTE]
   > Vervang de tijdelijke aanduiding *YourIoTHubName* door de naam die u voor uw IOT-hub hebt gekozen.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Bekijk de geretourneerde waarde voor later gebruik in deze Quick Start. Het lijkt op het volgende voorbeeld:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH naar een apparaat via apparaatstreams

In deze sectie maakt u een end-to-end-stroom voor het tunnelen van SSH-verkeer.

### <a name="run-the-device-local-proxy-application"></a>Voer de proxytoepassing in het apparaat uit

Zoals eerder vermeld, ondersteunt de IoT Hub node. js SDK alleen Device streams aan de kant van de service. Gebruik voor de apparaat-Local toepassing een apparaat proxy toepassing die beschikbaar is in een van de volgende Quick starts:

   * [SSH-en RDP-apparaten via IoT Hub apparaat-streams inschakelen met behulp van een C-proxy toepassing](./quickstart-device-streams-proxy-c.md)
   * [SSH en RDP via IoT Hub apparaat-streams inschakelen met C# behulp van een proxy toepassing](./quickstart-device-streams-proxy-csharp.md) 

Voordat u verdergaat met de volgende stap, moet u ervoor zorgen dat de toepassing voor de lokale proxy van het apparaat wordt uitgevoerd.

### <a name="run-the-service-local-proxy-application"></a>Voer de proxytoepassing in de service uit

Voer de volgende handelingen uit als u de toepassing voor het starten van een apparaat-lokale proxy wilt uitvoeren in node. js:

1. Geef voor omgevings variabelen uw service referenties op, de doel apparaat-ID waar de SSH-daemon wordt uitgevoerd en het poort nummer van de proxy die op het apparaat wordt uitgevoerd.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   Wijzig de voor gaande waarden zodat deze overeenkomen met uw apparaat-ID en connection string.

1. Ga naar de map Quick starts */device-streams-service* in uw ongecomprimeerde projectmap en voer de service-Local proxy-toepassing uit.

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH naar uw apparaat via apparaatstreams

Voer in Linux SSH uit met behulp `ssh $USER@localhost -p 2222` van op een Terminal. Gebruik in Windows uw favoriete SSH-client (bijvoorbeeld PuTTy).

Console-uitvoer op de service-Local na SSH-sessie wordt tot stand gebracht (de service-Local proxy-toepassing luistert op poort 2222):

![SSH-terminal uitvoer](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Console-uitvoer van de SSH-client toepassing (SSH-client communiceert met SSH-daemon door verbinding te maken met poort 22, waar de toepassing voor de lokale proxy van de service luistert):

![SSH-client uitvoer](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP naar uw apparaat via apparaatstreams

Gebruik nu uw RDP-client toepassing en maak verbinding met de Service proxy op poort 2222, een wille keurige poort die u eerder hebt gekozen.

> [!NOTE]
> Zorg ervoor dat uw apparaat-proxy correct is geconfigureerd voor RDP met poort 3389.

![De RDP-client maakt verbinding met de service-lokale proxy toepassing](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een IoT-hub ingesteld, een apparaat geregistreerd en een Service proxy toepassing geïmplementeerd om RDP en SSH in te scha kelen op een IoT-apparaat. Het RDP-en SSH-verkeer wordt via de IoT-hub getunneld via een apparaat stroom. Dit proces elimineert de nood zaak van rechtstreekse verbinding met het apparaat.

Zie voor meer informatie over het streamen van apparaten:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md)
