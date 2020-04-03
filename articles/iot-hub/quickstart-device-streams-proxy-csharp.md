---
title: Azure IoT Hub-apparaat streamt C# quickstart voor SSH en RDP
description: In deze quickstart voert u twee voorbeeld-C#-toepassingen uit waarmee SSH- en RDP-scenario's via een IoT Hub-apparaatstream kunnen worden ingeschakeld.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 97551ac63066f7064c16a001d9ce1f6bc31465ec
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586588"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Snelstart: SSH en RDP inschakelen via een IoT Hub-apparaatstream met behulp van een C#-proxytoepassing (voorbeeld)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub ondersteunt momenteel apparaatstromen als [voorbeeldfunctie.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[IoT Hub-apparaatstreams](iot-hub-device-streams-overview.md) zorgen ervoor dat service- en apparaattoepassingen kunnen communiceren op een beveiligde manier die de firewall toestaat. Deze quickstart-handleiding omvat twee C#-toepassingen waarmee client-servertoepassingsverkeer (zoals Secure Shell [SSH] en Remote Desktop Protocol [RDP] via een apparaatstream kan worden verzonden die is ingesteld via een IoT-hub. Zie Voorbeeld van lokale [proxytoepassingen voor SSH of RDP voor](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)een overzicht van de installatie.

In dit artikel wordt eerst de instelling voor SSH beschreven (met poort 22) en wordt vervolgens beschreven hoe u de poort van de installatie voor RDP wijzigen. Omdat apparaatstromen toepassings- en protocolagnostisch zijn, kan hetzelfde voorbeeld worden aangepast aan andere soorten toepassingsverkeer. Deze wijziging houdt meestal alleen in dat de communicatiepoort wordt gewijzigd in de poort die wordt gebruikt door de beoogde toepassing.

## <a name="how-it-works"></a>Hoe werkt het?

De volgende figuur illustreert hoe de apparaat-lokale en service-lokale proxy-toepassingen in dit voorbeeld end-to-end connectiviteit tussen de SSH-client en SSH daemon processen mogelijk maken. Hier gaan we ervan uit dat de daemon draait op hetzelfde apparaat als de apparaat-lokale proxy-toepassing.

![Instelling voor lokale proxy-toepassingen](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.png)

1. De service-local proxy-toepassing maakt verbinding met de IoT-hub en initieert een apparaatstream naar het doelapparaat.

1. De apparaat-lokale proxy-applicatie voltooit de stream initiatie handshake en stelt een end-to-end streaming tunnel door het streaming eindpunt van de IoT hub aan de kant van de service.

1. De apparaat-lokale proxy-applicatie maakt verbinding met de SSH daemon die luistert op poort 22 op het apparaat. Deze instelling is configureerbaar, zoals beschreven in de sectie 'Voer de apparaat-lokale proxy-toepassing uit'.

1. De service-local proxy-applicatie wacht op nieuwe SSH-verbindingen van een gebruiker door te luisteren op een aangewezen poort, die in dit geval poort 2222 is. Deze instelling is configureerbaar, zoals beschreven in de sectie 'De service-lokale proxy-toepassing uitvoeren'. Wanneer de gebruiker verbinding maakt via de SSH-client, maakt de tunnel het mogelijk om SSH-applicatieverkeer over te zetten tussen de SSH-client en servertoepassing.

> [!NOTE]
> SSH-verkeer dat via een apparaatstream wordt verzonden, wordt door het streaming-eindpunt van de IoT-hub getunneld in plaats van rechtstreeks tussen service en apparaat verzonden. Zie voor meer informatie de [voordelen van het gebruik van Iot Hub-apparaatstreams.](iot-hub-device-streams-overview.md#benefits)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* De preview van apparaatstreams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:

  * VS - centraal
  * Centrale EUAP van de VS
  * Azië - zuidoost
  * Europa - noord

* De twee voorbeeldtoepassingen die u in deze quickstart uitvoert, zijn geschreven in C#. Je hebt de .NET Core SDK 2.1.0 of hoger op je ontwikkelmachine nodig.

  U de [.NET Core SDK voor meerdere platforms](https://www.microsoft.com/net/download/all)downloaden van .NET.

* Controleer de huidige versie van C# op uw ontwikkelingsmachine met de volgende opdracht:

    ```
    dotnet --version
    ```

* Voer de volgende opdracht uit om de Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IOT-extensie voegt dps-specifieke opdrachten (IoT Hub, IoT Edge en IoT Device Provisioning Service) toe aan de Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   ```azurecli-interactive
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* [Download de Azure IoT C#-voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)en haal het ZIP-archief eruit.

* Een geldig gebruikersaccount en een geldig gebruikersaccount op het apparaat (Windows of Linux) dat wordt gebruikt om de gebruiker te verifiëren.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze quickstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdracht uit in Cloud Shell als u de identiteit van het apparaat wilt maken:

   > [!NOTE]
   > * Vervang de *Placeholder YourIoTHubName* door de naam die u voor uw IoT-hub hebt gekozen.
   > * Voor de naam van het apparaat dat u registreert, wordt aanbevolen *om MyDevice* te gebruiken zoals weergegeven. Als u een andere naam voor uw apparaat kiest, gebruikt u die naam in dit artikel en werkt u de apparaatnaam bij in de voorbeeldtoepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Voer de volgende opdrachten uit in Cloud Shell om de *tekenreeks voor apparaatverbinding voor* het apparaat dat u zojuist hebt geregistreerd, uit te voeren:

   > [!NOTE]
   > Vervang de *Placeholder YourIoTHubName* door de naam die u voor uw IoT-hub hebt gekozen.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Let op de tekenreeks voor geretourneerde apparaatverbinding voor later gebruik in deze quickstart. Het lijkt op het volgende voorbeeld:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

1. Als u verbinding wilt maken met uw IoT-hub en een apparaatstream wilt instellen, hebt u ook de *tekenreeks serviceverbinding* van uw IoT-hub nodig om de service-side-toepassing in te schakelen. Met de volgende opdracht haalt u deze waarde voor uw IoT-hub op:

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

Navigeer in een lokaal terminalvenster naar de `device-streams-proxy/device` map in de projectmap met uitgepakte items. Houd de volgende informatie bij de hand:

| Argumentnaam | Argumentwaarde |
|----------------|-----------------|
| `DeviceConnectionString` | De tekenreeks voor apparaatverbinding van het apparaat dat u eerder hebt gemaakt. |
| `targetServiceHostName` | Het IP-adres waar de SSH-server luistert. Het adres `localhost` zou zijn als het hetzelfde IP waar het apparaat-lokale proxy applicatie wordt uitgevoerd. |
| `targetServicePort` | De poort die wordt gebruikt door uw toepassingsprotocol (voor SSH is dit standaard poort 22).  |

Compileren en uitvoeren van de code met de volgende opdrachten:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 22

# In Windows
dotnet run {DeviceConnectionString} localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>Voer de proxytoepassing in de service uit

Navigeer in een ander `iot-hub/quickstarts/device-streams-proxy/service` lokaal terminalvenster naar in de projectmap met uitgepakte items. Houd de volgende informatie bij de hand:

| Parameternaam | Parameterwaarde |
|----------------|-----------------|
| `ServiceConnectionString` | De serviceverbindingsreeks van uw IoT Hub. |
| `MyDevice` | De id van het apparaat dat u eerder hebt gemaakt. |
| `localPortNumber` | Een lokale poort waarmee uw SSH-client verbinding maakt. We gebruiken poort 2222 in dit voorbeeld, maar je zou andere willekeurige getallen kunnen gebruiken. |

Compileren en uitvoeren van de code met de volgende opdrachten:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-the-ssh-client"></a>De SSH-client uitvoeren

Gebruik nu uw SSH-clienttoepassing en maak rechtstreeks verbinding met service-local proxy-applicatie op poort 2222 (in plaats van de SSH-daemon).

```
ssh {username}@localhost -p 2222
```

Op dit punt vraagt het SSH-aanmeldingsvenster u om uw referenties in te voeren.

Console-uitvoer aan de servicezijde (de service-lokale proxy-toepassing luistert op poort 2222):

![Service-lokale proxy-toepassingsuitvoer](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Console-uitvoer op de apparaat-lokale proxy-toepassing, die verbinding maakt met de SSH-daemon op *IP_address:22*:

![Apparaatlokale proxy-toepassingsuitvoer](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Console-uitvoer van de SSH-clienttoepassing. De SSH-client communiceert met de SSH daemon door verbinding te maken met poort 22, waarop de service-local proxy applicatie luistert:

![SSH-clienttoepassingsuitvoer](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>RDP naar een apparaat via apparaatstreams

De setup voor RDP is vergelijkbaar met de setup voor SSH (hierboven beschreven). U gebruikt in plaats daarvan het RDP-doel IP en poort 3389 en gebruikt de RDP-client (in plaats van de SSH-client).

### <a name="run-the-device-local-proxy-application-rdp"></a>De device-local proxy-toepassing (RDP) uitvoeren

Navigeer in een lokaal terminalvenster naar de `device-streams-proxy/device` map in de projectmap met uitgepakte items. Houd de volgende informatie bij de hand:

| Argumentnaam | Argumentwaarde |
|----------------|-----------------|
| `DeviceConnectionString` | De tekenreeks voor apparaatverbinding van het apparaat dat u eerder hebt gemaakt. |
| `targetServiceHostName` | De hostnaam of het IP-adres waar de RDP-server wordt uitgevoerd. Het adres `localhost` zou zijn als het hetzelfde IP waar het apparaat-lokale proxy applicatie wordt uitgevoerd. |
| `targetServicePort` | De poort die wordt gebruikt door uw toepassingsprotocol (voor RDP, standaard, zou dit poort 3389).  |

Compileren en uitvoeren van de code met de volgende opdrachten:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>De service-local proxy-toepassing (RDP) uitvoeren

Navigeer in een ander `device-streams-proxy/service` lokaal terminalvenster naar in de projectmap met uitgepakte items. Houd de volgende informatie bij de hand:

| Parameternaam | Parameterwaarde |
|----------------|-----------------|
| `ServiceConnectionString` | De serviceverbindingsreeks van uw IoT Hub. |
| `MyDevice` | De id van het apparaat dat u eerder hebt gemaakt. |
| `localPortNumber` | Een lokale poort waarmee uw SSH-client verbinding maakt. In dit voorbeeld gebruiken we poort 2222, maar u kunt dat wijzigen in willekeurige andere getallen. |

Compileren en uitvoeren van de code met de volgende opdrachten:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-rdp-client"></a>RDP-client uitvoeren

Gebruik nu uw RDP-clienttoepassing en maak verbinding met de service-lokale proxy-toepassing op poort 2222 (dit was een willekeurige beschikbare poort die u eerder hebt gekozen).

![RDP maakt verbinding met de service-local proxy-toepassing](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart stelt u een IoT-hub in, registreerde u een apparaat, hebt u apparaatlokale en service-lokale proxytoepassingen geïmplementeerd om een apparaatstroom via de IoT-hub tot stand te brengen en hebt u de proxytoepassingen gebruikt om SSH- of RDP-verkeer te tunnelen. Hetzelfde paradigma is geschikt voor andere client-server protocollen, waarbij de server draait op het apparaat (bijvoorbeeld de SSH daemon).

Zie voor meer informatie over apparaatstreams:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md)
