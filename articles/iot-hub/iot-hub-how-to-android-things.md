---
title: Ontwikkelen voor Android Things-platform met Azure IoT SDKs | Microsoft Documenten
description: Handleiding voor ontwikkelaars - Meer informatie over hoe u zich ontwikkelen op Android Things met Azure IoT Hub SDKs.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: a06583e9aab4b082517d47c1022f7bec5184b9bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673384"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Ontwikkelen voor Android Things-platform met Azure IoT SDKs

[Azure IoT Hub SDKs](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) bieden eerste rang ondersteuning voor populaire platforms zoals Windows, Linux, OSX, MBED en mobiele platforms zoals Android en iOS.  Als onderdeel van onze toewijding om meer keuze en flexibiliteit in IoT-implementaties mogelijk te maken, ondersteunt de Java SDK ook [Android Things-platform.](https://developer.android.com/things/)  Ontwikkelaars kunnen gebruik maken van de voordelen van android things-besturingssysteem aan de apparaatzijde, terwijl ze [Azure IoT Hub](about-iot-hub.md) gebruiken als de centrale berichtenhub die wordt geschaald naar miljoenen tegelijk verbonden apparaten.

In deze zelfstudie worden de stappen beschreven om een toepassing aan de apparaatzijde op Android Things te bouwen met behulp van de Azure IoT Java SDK.

## <a name="prerequisites"></a>Vereisten

* Een Android Things ondersteunde hardware met Android Things OS draait.  Je [Android Things-documentatie](https://developer.android.com/things/get-started/kits#flash-at) volgen over het flashen van Android Things OS.  Zorg ervoor dat uw Android Things-apparaat is verbonden met het internet met essentiële randapparatuur, zoals toetsenbord, beeldscherm en muis aangesloten.  Deze tutorial maakt gebruik van Raspberry Pi 3.

* Nieuwste versie van [Android Studio](https://developer.android.com/studio/)

* Nieuwste versie van [Git](https://git-scm.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze snelstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdrachten uit in Azure Cloud Shell om de IoT Hub CLI-extensie toe te voegen en de apparaat-id te maken.

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

   **MyAndroidThingsDevice** : Dit is de naam gegeven voor het geregistreerde apparaat. Gebruik MyAndroidThingsDevice zoals getoond. Als u een andere naam voor het apparaat kiest, moet u deze naam ook in de rest van dit artikel gebruiken. Bovendien moet u de apparaatnaam bijwerken in de voorbeeldtoepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Voer de volgende opdrachten uit in Azure Cloud Shell om de tekenreeks voor de *apparaatverbinding* voor het apparaat dat u zojuist hebt geregistreerd, op te halen. Vervang `YourIoTHubName` hieronder de naam die u kiest voor uw IoT-hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Noteer de apparaatverbindingsreeks. Deze ziet er ongeveer als volgt uit:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    U gebruikt deze waarde verderop in de snelstartgids.

## <a name="building-an-android-things-application"></a>Een Android Things-toepassing bouwen

1. De eerste stap naar het bouwen van een Android Things-applicatie is verbinding maken met je Android Things-apparaten. Sluit je Android Things-apparaat aan op een beeldscherm en verbind het met het internet. Android Things biedt [documentatie](https://developer.android.com/things/get-started/kits) over hoe je verbinding maken met WiFi. Nadat u verbinding hebt gemaakt met internet, neemt u een notitie van het IP-adres dat wordt vermeld onder Netwerken.

2. Gebruik de [adb-tool](https://developer.android.com/studio/command-line/adb) om verbinding te maken met je Android Things-apparaat met het IP-adres dat hierboven wordt vermeld. Controleer de verbinding met behulp van deze opdracht van uw terminal. U moet uw apparaten als 'verbonden' zien.

   ```
   adb devices
   ```

3. Download ons voorbeeld voor Android/Android Things uit deze [repository](https://github.com/Azure-Samples/azure-iot-samples-java) of gebruik Git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. Open in Android Studio het Android Project in '\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample'.

5. Open het bestand gradle.properties en vervang 'Device_connection_string' door de eerder genoteerde verbindingstekenreeks van uw apparaat.
 
6. Klik op Uitvoeren - Foutopsporing en selecteer uw apparaat om deze code te implementeren op uw Android Things-apparaten.

7. Wanneer de toepassing is gestart, u een toepassing zien die wordt uitgevoerd op uw Android Things-apparaat. Deze voorbeeldtoepassing verzendt willekeurig gegenereerde temperatuurmetingen.

## <a name="read-the-telemetry-from-your-hub"></a>De telemetrie van uw hub lezen

U de gegevens bekijken via uw IoT-hub terwijl deze worden ontvangen. De IoT Hub CLI-extensie kan verbinding maken met het eindpunt **Events** aan de servicezijde van uw IoT-hub. De extensie ontvangt de berichten die van het gesimuleerde apparaat naar de cloud worden verzonden. Een back-endtoepassing van IoT Hub wordt meestal uitgevoerd in de cloud om berichten van apparaat naar cloud te ontvangen en verwerken.

Voer de volgende opdrachten uit in Azure Cloud Shell. Vervang daarbij `YourIoTHubName` door de naam van uw IoT-hub:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het beheren van connectiviteit en betrouwbare berichten](iot-hub-reliability-features-in-sdks.md) met behulp van de IoT Hub SDKs.
* Meer informatie over hoe je je [ontwikkelen voor mobiele platforms](iot-hub-how-to-develop-for-mobile-devices.md) zoals iOS en Android.
* [Ondersteuning voor Azure IoT SDK-platform](iot-hub-device-sdk-platform-support.md)
