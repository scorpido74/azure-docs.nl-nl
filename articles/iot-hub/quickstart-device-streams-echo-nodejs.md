---
title: Communiceren met een apparaat-app in node. js via IoT Hub apparaat streams (preview) | Microsoft Docs
description: In deze Quick Start voert u een node. js-service-side-toepassing uit die met een IoT-apparaat communiceert via een apparaatgegevens stroom.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 4acfe148e424d41308e310abd2a10e4e21133799
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900850"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Quick Start: communiceren met een toepassings toepassing in node. js via IoT Hub apparaat streams (preview)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT Hub biedt momenteel ondersteuning voor het streamen van apparaten als een [Preview-functie](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-apparaatstreams](./iot-hub-device-streams-overview.md) zorgen ervoor dat service- en apparaattoepassingen kunnen communiceren op een beveiligde manier die de firewall toestaat. Gedurende de openbare preview biedt de Node.js SDK alleen ondersteuning voor apparaatstreams aan de servicezijde. Daarom bevat deze quickstart alleen instructies voor het uitvoeren van de toepassing aan de servicezijde. U moet een bijgevoegde toepassing aan het apparaat uitvoeren vanuit een van de volgende Quick starts:

* [Communiceren met apparaat-apps in C via IoT Hub Device streams](./quickstart-device-streams-echo-c.md)

* [Communiceren met apparaat-apps C# in via IOT hub Device streams](./quickstart-device-streams-echo-csharp.md).

De Node.js-toepassing aan de servicezijde in deze quickstart heeft de volgende functionaliteit:

* Maakt een apparaatstream naar een IoT-apparaat.

* Leest invoer vanaf de opdrachtregel en verzendt deze naar de apparaattoepassing, die de invoer op zijn beurt terugstuurt.

De code demonstreert het initiatie proces van een apparaat stroom en hoe dit kan worden gebruikt om gegevens te verzenden en te ontvangen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De preview van Device streams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:

  * VS - centraal
  * Centrale VS-EUAP
  * Europa - noord
  * Azië - zuidoost

Als u de toepassing aan de service zijde in deze Snelstartgids wilt uitvoeren, moet u node. js V10 toevoegen. x. x of hoger op uw ontwikkel machine hebben.

U kunt node. js voor meerdere platforms downloaden vanuit [Nodejs.org](https://nodejs.org).

Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

```cmd/sh
node --version
```

Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. Met de IOT-extensie worden DPS-opdrachten (IoT Hub, IoT Edge en IoT Device Provisioning Service) toegevoegd aan Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Als u dit nog niet hebt gedaan, downloadt u het voorbeeldproject met Node.js van https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip en pakt u het ZIP-archief uit.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

U kunt deze stap overslaan als u eerder deze zelfstudie hebt voltooid: [Snelstartgids: Telemetriegegevens vanaf een apparaat verzenden naar een IoT-hub en de telemetriegegevens op de hub lezen met een back-endtoepassing (Node.js)](quickstart-send-telemetry-node.md).

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Een apparaat registreren

U kunt deze stap overslaan als u eerder deze zelfstudie hebt voltooid: [Snelstartgids: Telemetriegegevens vanaf een apparaat verzenden naar een IoT-hub en de telemetriegegevens op de hub lezen met een back-endtoepassing (Node.js)](quickstart-send-telemetry-node.md).

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze snelstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdracht uit in Azure Cloud Shell om de apparaat-id te maken.

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

   **Mijn**: dit is de naam voor het apparaat dat u registreert. Het is raadzaam om **mijn** te gebruiken zoals wordt weer gegeven. Als u een andere naam kiest voor uw apparaat, moet u deze naam ook in dit artikel gebruiken en de apparaatnaam bijwerken in de voorbeeld toepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. U hebt ook een *service-verbindingsreeks* nodig, zodat de back-end-toepassing verbinding kan maken met de IoT-hub en de berichten kan ophalen. Met de volgende opdracht haalt u de serviceverbindingsreeks voor uw IoT-hub op:

    **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Bekijk de geretourneerde service connection string voor later gebruik in deze Quick Start. Het lijkt op het volgende voorbeeld:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Communicatie tussen apparaat en service via apparaatstreams

In deze sectie voert u zowel de toepassing op het apparaat als de toepassing aan de service zijde uit en communiceert u tussen de twee toepassingen.

### <a name="run-the-device-side-application"></a>De toepassing aan de apparaatzijde uitvoeren

Zoals eerder vermeld, biedt de IoT Hub Node.js SDK alleen ondersteuning voor apparaatstreams aan de servicezijde. Voor een toepassing aan het apparaat gebruikt u een van de begeleidende apparaten in deze Quick starts:

   * [Communiceren met apparaat-apps in C via IoT Hub Device streams](./quickstart-device-streams-echo-c.md)

   * [Communiceren met apparaat-apps C# in via IOT hub Device streams](./quickstart-device-streams-echo-csharp.md)

Controleer of de toepassing aan apparaatzijde wordt uitgevoerd voordat u naar de volgende stap gaat.

### <a name="run-the-service-side-application"></a>De toepassing aan de servicezijde uitvoeren

Ervan uitgaande dat de toepassing op het apparaat wordt uitgevoerd, volgt u de onderstaande stappen in een lokaal Terminal venster om de toepassing aan de service zijde in node. js uit te voeren:

* Geef de referenties van uw service en de apparaat-id op als omgevingsvariabelen.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Wijzig de tijdelijke aanduiding ServiceConnectionString zodat deze overeenkomt met uw service connection string en **mijn** overeenkomen met uw apparaat-id als u een andere naam hebt opgegeven.

* Navigeer naar `Quickstarts/device-streams-service` in de uitgepakte projectmap en voer het voorbeeld uit met behulp van het knooppunt.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

Aan het einde van de laatste stap initieert de toepassing aan de servicezijde een stream naar uw apparaat die vervolgens via de stroom een tekenreeksbuffer naar de service verzendt. In dit voor beeld wordt het `stdin` op de Terminal door het programma aan de service zijde gewoon gelezen en naar het apparaat verzonden. Hiermee wordt een geslaagde bidirectionele communicatie tussen de twee toepassingen aangegeven.

![Console-uitvoer aan service zijde](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Vervolgens kunt u het programma beëindigen door nogmaals op Enter te drukken.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick start gaat u een IoT-hub instellen, een apparaat registreren, een apparaat stroom tot stand brengen tussen toepassingen op het apparaat en de service kant en de stream gebruiken om gegevens heen en weer te sturen tussen de toepassingen.

Gebruik de onderstaande koppelingen voor meer informatie over apparaatstreams:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md) 