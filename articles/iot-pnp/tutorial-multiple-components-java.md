---
title: Java-voorbeeldcode voor een IoT Plug and Play Preview-apparaat met meerdere onderdelen koppelen aan IoT Hub | Microsoft Docs
description: Schrijf Java-voorbeeldcode voor een IoT Plug and Play Preview-apparaat dat meerdere onderdelen gebruikt en verbinding maakt met een IoT-hub, en voer deze uit. Gebruik het hulpprogramma Azure IoT Explorer om de gegevens te bekijken die door het apparaat naar de hub worden verzonden.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1d16d8c54939c4f659b6a1530e2d360b957a09ad
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351774"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-java"></a>Zelfstudie: Een voorbeeld van een IoT Plug and Play Preview-apparaattoepassing met verschillende onderdelen verbinden met IoT Hub (Java)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

In deze zelfstudie ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing met meerdere onderdelen maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u de Azure CLI gebruikt om de telemetrie weer te geven die wordt verzonden. De voorbeeldtoepassing wordt geschreven in Java en is inbegrepen in de Azure IoT Device SDK voor Java. Een ontwikkelaar van oplossingen kan de Azure CLI gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

In deze zelfstudie ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing met onderdelen en een hoofdinterface maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u het hulpprogramma Azure IoT Explorer gebruikt om de gegevens weer te geven die naar de hub worden verzonden. De voorbeeldtoepassing wordt geschreven in Java en is inbegrepen in de Azure IoT Device SDK voor Java. Een ontwikkelaar van oplossingen kan het hulpprogramma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie in Windows te voltooien, installeert u de volgende software in uw lokale Windows-omgeving:

* Java SE Development Kit 8. In [Java-langetermijnondersteuning voor Azure en Azure Stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable) onder **Langetermijnondersteuning** selecteert u **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

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

## <a name="download-the-code"></a>De code downloaden

In deze zelfstudie bereidt u een ontwikkelomgeving voor die kan worden gebruikt voor het klonen en compileren van de Azure IoT Hub Device Java-SDK.

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats [Azure IoT Java-SDK’s en -bibliotheken](https://github.com/Azure/azure-iot-sdk-java) te klonen op deze locatie:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

Deze bewerking kan enkele minuten in beslag nemen.

## <a name="build-the-code"></a>De code bouwen

Ga in Windows naar de hoofdmap van de gekloonde Java-SDK-opslagplaats. Ga vervolgens naar de map *\device\iot-device-samples\pnp-device-sample\temerature-controller-device-sample*.

Voer de volgende opdracht uit om de voorbeeldtoepassing te maken:

```java
mvn clean package
```

## <a name="run-the-device-sample"></a>Het apparaatvoorbeeld uitvoeren

Maak een omgevingsvariabele genaamd **IOTHUB_DEVICE_CONNECTION_STRING** om de apparaatverbindingsreeks op te slaan die u eerder hebt genoteerd.

Voer de volgende opdracht uit om de voorbeeldtoepassing uit te voeren:

```java
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

Het apparaat is nu klaar om opdrachten en updates van eigenschappen te ontvangen, en is begonnen met het verzenden van telemetriegegevens naar de hub. Laat het voorbeeld actief tijdens het uitvoeren van de volgende stappen.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Code valideren met Azure IoT Explorer

Nadat het voorbeeld van de apparaatclient is gestart, gebruikt u het hulpprogramma Azure IoT Explorer om te verifiëren dat het werkt.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>De code bekijken

Met dit voorbeeld wordt een IoT Plug and Play-temperatuurregelingsapparaat geïmplementeerd. Het model dat met dit voorbeeld wordt geïmplementeerd, maakt gebruik van [meerdere onderdelen](concepts-components.md). Het [Digital Twins Definition Language-modelbestand (DTDL) voor het thermostaatapparaat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definieert de telemetrie, eigenschappen en opdrachten die het apparaat implementeert.

De apparaatcode maakt gebruik van de standaard `DeviceClient`-klasse om verbinding te maken met uw IoT-hub. Het apparaat verzendt de model-id van het DTDL-model dat het in de verbindingsaanvraag implementeert. Een apparaat dat een model-id verzendt, is een IoT Plug and Play-apparaat:

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

De model-id wordt in de code opgeslagen, zoals weergegeven in het volgende fragment:

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

Nadat het apparaat verbinding heeft gemaakt met uw IoT-hub, registreert de code de opdrachthandlers.

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

Er zijn afzonderlijke handlers voor de gewenste updates voor eigenschappen in de twee thermostaatonderdelen:

```java
deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_1, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_2, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

De voorbeeldcode verzendt telemetrie vanuit elk thermostaatonderdeel:

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

De `sendTemperatureReading`-methode maakt gebruik van de `PnpHhelper`-klasse om berichten voor elk onderdeel te maken:

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

De `PnpHelper`-klasse bevat andere voorbeeldmethoden die u kunt gebruiken met een model met meerdere onderdelen.

Gebruik het hulpprogramma Azure IoT Explorer om de telemetrie en eigenschappen uit de twee thermostaatonderdelen weer te geven:

:::image type="content" source="media/tutorial-multiple-components-java/multiple-component.png" alt-text="Apparaat met meerdere onderdelen in Azure IoT Explorer":::

U kunt het hulpprogramma Azure IoT Explorer ook gebruiken om opdrachten aan te roepen in een van de twee thermostaatonderdelen of in de hoofdinterface.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een IoT Plug and Play-apparaat met onderdelen kunt verbinden met een IoT-hub. Voor meer informatie over IoT Plug and Play-apparaatmodellen raadpleegt u:

> [!div class="nextstepaction"]
> [Handleiding voor ontwikkelaars van IoT Plug and Play Preview-modellen](concepts-developer-guide.md)
