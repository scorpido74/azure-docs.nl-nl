---
title: Apparaatconnectiviteit en telemetrie-ingress - Azure Digital Twins | Microsoft Documenten
description: Meer informatie over het verbinden, onboarden en verzenden van telemetrie vanaf een IoT-apparaat in Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 5c2c519ece9806b92c3e455d5f550bc2abfc9f3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862472"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Apparaatconnectiviteit en inkomende telemetriegegevens

De telemetriegegevens die door apparaten en sensoren worden verzonden, vormen de ruggengraat van elke IoT-oplossing. Hoe deze verschillende resources te vertegenwoordigen en te beheren binnen de context van een locatie zijn belangrijkste zorgen in IoT app ontwikkeling. Azure Digital Twins vereenvoudigt het proces van het ontwikkelen van IoT-oplossingen door apparaten en sensoren te verenigen met een grafiek met ruimtelijke intelligentie.

Maak een Azure IoT Hub-bron aan de basis van de ruimtelijke grafiek om aan de slag te gaan. Met de IoT Hub-bron kunnen alle apparaten onder de hoofdruimte berichten verzenden. Nadat de IoT Hub is gemaakt, registreert u apparaten met sensoren in het exemplaar Digital Twins. De apparaten kunnen gegevens naar een Digital Twins-service verzenden via de [Azure IoT-apparaat SDK.](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks)

Lees de [zelfstudie om Digital Twins te implementeren en te configureren voor](tutorial-facilities-setup.md)een stapsgewijze handleiding over het aan boord brengen van apparaten. In één oogopslag zijn de stappen:

- Een exemplaar van Digital Twins implementeren vanuit de [Azure-portal.](https://portal.azure.com)
- Maak spaties in uw grafiek.
- Maak een IoT Hub-bron en wijs deze toe aan een spatie in uw grafiek.
- Maak apparaten en sensoren in uw grafiek en wijs ze toe aan de ruimten die in de vorige stappen zijn gemaakt.
- Maak een matcher om telemetrieberichten te filteren op basis van voorwaarden.
- Maak een [door de gebruiker gedefinieerde functie](concepts-user-defined-functions.md)en wijs deze toe aan een spatie in de grafiek voor aangepaste verwerking van uw telemetrieberichten.
- Wijs een rol toe om de door de gebruiker gedefinieerde functie toegang te geven tot de grafiekgegevens.
- Haal de ienaar voor de verbinding met iot-hub-apparaten uit de API's voor digital twins management.
- Configureer de tekenreeks voor apparaatverbinding op het apparaat met de Azure IoT-apparaat SDK.

In de volgende secties leert u hoe u de verbindingstekenreeks van iot-hub-apparaten uit de Digital Twins Management API halen. U leert ook hoe u de telemetrieberichtindeling van de IoT Hub gebruiken om telemetrie op basis van sensoren te verzenden. Digital Twins vereist elk stukje telemetrie dat het ontvangt om te worden geassocieerd met een sensor in de ruimtelijke grafiek. Deze vereiste zorgt ervoor dat de gegevens worden verwerkt en gerouteerd binnen de juiste ruimtelijke context.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>De verbindingstekenreeks voor IoT Hub-apparaten ophalen van de Beheer-API

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Doe een GET-aanroep op `includes=ConnectionString` de Apparaat-API met een parameter om de verbindingstekenreeks voor IoT Hub-apparaten op te halen. Filter op de GUID van het apparaat of de hardware-id om het opgegeven apparaat te vinden.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parameter | Vervangen door |
| --- | --- |
| *YOUR_DEVICE_GUID* | De apparaat-id |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Parameterwaarde | Vervangen door |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | De hardware-id van het apparaat |

Kopieer in de reactiepayload de **eigenschap connectionString van** het apparaat. U gebruikt het wanneer u de SDK van het Azure IoT-apparaat belt om gegevens naar Digital Twins te verzenden.

## <a name="device-to-cloud-message"></a>Apparaat-naar-cloudbericht

U de berichtindeling en de laadvoorziening van uw apparaat aanpassen aan de behoeften van uw oplossing. Gebruik een gegevenscontract dat kan worden geserialiseerd tot een bytearray of stream die wordt ondersteund door de [klasse Azure IoT Device Client Message, Message(byte[] byteArray).](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___) Het bericht kan een aangepaste binaire indeling van uw keuze zijn, zolang u het gegevenscontract decodeert in een overeenkomstige door de gebruiker gedefinieerde functie. Er is slechts één vereiste voor een device-to-cloud-bericht. Houd een set eigenschappen bij om ervoor te zorgen dat uw bericht op de juiste manier naar de verwerkingsengine wordt doorgestuurd.

### <a name="telemetry-properties"></a>Telemetrie-eigenschappen

 De payload-inhoud van een **bericht** kan willekeurige gegevens tot 256 KB groot zijn. Er zijn een paar eisen [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) verwacht voor eigenschappen van het type. De tabel toont de vereiste en optionele eigenschappen die door het systeem worden ondersteund.

| Naam van eigenschap | Waarde | Vereist | Beschrijving |
|---|---|---|---|
| **DigitalTwins-Telemetrie** | 1.0 | Ja | Een constante waarde die een bericht aan het systeem identificeert. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Ja | Een unieke id van de sensor die het **bericht**verzendt. Deze waarde moet overeenkomen met de **eigenschap HardwareId** van een object om het systeem te kunnen verwerken. Bijvoorbeeld `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Nee | Een [ISO 8601-opgemaakte](https://www.iso.org/iso-8601-date-and-time-format.html) datumtekenreeks die de bemonsteringstijd van het laadvermogen identificeert. Bijvoorbeeld `2018-09-20T07:35:00.8587882-07:00`. |
| **CorrelationId** | `string` | Nee | Een UUID die wordt gebruikt om gebeurtenissen in het hele systeem te traceren. Bijvoorbeeld `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Stuur uw bericht naar Digital Twins

Gebruik de DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) of [SendEventBatchAsync-oproep](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) om uw bericht naar Digital Twins te sturen.

## <a name="next-steps"></a>Volgende stappen

- Lees [Azure Digital Twins-gegevensverwerking en door de gebruiker gedefinieerde functies](concepts-user-defined-functions.md)voor meer informatie over azure Digital Twins-gegevensverwerking en door de gebruiker gedefinieerde functies.
