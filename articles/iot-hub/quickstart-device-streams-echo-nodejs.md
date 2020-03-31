---
title: Communiceren met de apparaat-app in Node.js met Azure IoT Hub-apparaatstreams
description: In deze quickstart voert u een Node.js-service-side-toepassing uit die via een apparaatstream communiceert met een IoT-apparaat.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 0757c5eb8639e4a864b049adc92c97a7cf69adba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675510"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Snelstart: communiceren met een apparaattoepassing in Node.js via IoT Hub-apparaatstreams (voorbeeld)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

In deze quickstart voert u een service-side applicatie uit en stelt u de communicatie tussen een apparaat en een service in met behulp van apparaatstreams. Azure IoT Hub-apparaatstreams stellen service- en apparaattoepassingen in staat om op een veilige en firewallvriendelijke manier te communiceren. Tijdens de openbare preview ondersteunt de Node.js SDK alleen apparaatstreams aan de servicezijde. Daarom bevat deze quickstart alleen instructies voor het uitvoeren van de toepassing aan de servicezijde.

## <a name="prerequisites"></a>Vereisten

* Voltooiing van [communiceren met apparaat-apps in C via IoT Hub-apparaatstreams](./quickstart-device-streams-echo-c.md) of [Communiceren met apparaat-apps in C# via IoT Hub-apparaatstreams.](./quickstart-device-streams-echo-csharp.md)

* Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Node.js 10+](https://nodejs.org).

* [Een voorbeeld node.js project](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

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

Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IoT-extensie voegt DpS-opdrachten (IoT Hub, IoT Edge en IoT Device Provisioning Service) toe aan Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

U kunt deze stap overslaan als u eerder deze zelfstudie hebt voltooid: [Snelstartgids: Telemetriegegevens vanaf een apparaat verzenden naar een IoT-hub en de telemetriegegevens op de hub lezen met een back-endtoepassing (Python)](quickstart-send-telemetry-node.md).

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

U kunt deze stap overslaan als u eerder deze zelfstudie hebt voltooid: [Snelstartgids: Telemetriegegevens vanaf een apparaat verzenden naar een IoT-hub en de telemetriegegevens op de hub lezen met een back-endtoepassing (Python)](quickstart-send-telemetry-node.md).

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze snelstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdracht uit in Azure Cloud Shell om de apparaatidentiteit te maken.

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

   **MyDevice:** dit is de naam voor het apparaat dat u registreert. Het wordt aanbevolen om **MyDevice** te gebruiken zoals getoond. Als u een andere naam voor uw apparaat kiest, moet u die naam ook in dit artikel gebruiken en de apparaatnaam bijwerken in de voorbeeldtoepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. U hebt ook een *service-verbindingsreeks* nodig, zodat de back-end-toepassing verbinding kan maken met de IoT-hub en de berichten kan ophalen. Met de volgende opdracht haalt u de serviceverbindingsreeks voor uw IoT-hub op:

    **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Let op de tekenreeks geretourneerde serviceverbinding voor later gebruik in deze quickstart. Het lijkt op het volgende voorbeeld:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Communicatie tussen apparaat en service via apparaatstreams

In deze sectie voert u zowel de toepassing aan de apparaatzijde als de service-side-applicatie uit en communiceert u tussen de twee.

### <a name="run-the-device-side-application"></a>De toepassing aan de apparaatzijde uitvoeren

Zoals eerder vermeld, biedt de IoT Hub Node.js SDK alleen ondersteuning voor apparaatstreams aan de servicezijde. Gebruik voor een apparaat-side applicatie een van de bijbehorende apparaatprogramma's die beschikbaar zijn in deze quickstarts:

* [Communiceren met apparaat-apps in C via IoT Hub-apparaatstreams](./quickstart-device-streams-echo-c.md)

* [Communiceren met apparaat-apps in C# via IoT Hub-apparaatstreams](./quickstart-device-streams-echo-csharp.md)

Controleer of de toepassing aan apparaatzijde wordt uitgevoerd voordat u naar de volgende stap gaat.

### <a name="run-the-service-side-application"></a>De toepassing aan de servicezijde uitvoeren

De Node.js-toepassing aan de servicezijde in deze quickstart heeft de volgende functionaliteit:

* Maakt een apparaatstream naar een IoT-apparaat.
* Leest invoer vanaf de opdrachtregel en verzendt deze naar de apparaattoepassing, die de invoer op zijn beurt terugstuurt.

De code toont het initiatieproces van een apparaatstream en hoe u deze gebruiken om gegevens te verzenden en te ontvangen.

Als u ervan gaat dat de toepassing aan de apparaatzijde wordt uitgevoerd, voert u de onderstaande stappen uit in een lokaal terminalvenster om de service-side-toepassing in Node.js uit te voeren:

* Geef de referenties van uw service en de apparaat-id op als omgevingsvariabelen.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Wijzig de tijdelijke aanduiding ServiceConnectionString om aan te passen aan uw tekenreeks voor serviceverbinding en **MyDevice** om overeen te komen met uw apparaat-id als u de uwe een andere naam hebt opgegeven.

* Navigeer naar `Quickstarts/device-streams-service` in de uitgepakte projectmap en voer het voorbeeld uit met behulp van het knooppunt.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

Aan het einde van de laatste stap initieert de toepassing aan de servicezijde een stream naar uw apparaat die vervolgens via de stroom een tekenreeksbuffer naar de service verzendt. In dit voorbeeld, de service-side `stdin` programma leest gewoon de op de terminal en stuurt het naar het apparaat, die vervolgens echo het terug. Hiermee wordt een geslaagde bidirectionele communicatie tussen de twee toepassingen aangegeven.

![Console-uitvoer aan de servicezijde](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Vervolgens kunt u het programma beëindigen door nogmaals op Enter te drukken.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart stelt u een IoT-hub in, registreerde u een apparaat, vestigde u een apparaatstroom tussen toepassingen aan de apparaat- en servicezijde en gebruikt u de stroom om gegevens heen en weer te sturen tussen de toepassingen.

Gebruik de onderstaande koppelingen voor meer informatie over apparaatstreams:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md) 