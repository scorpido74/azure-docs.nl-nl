---
title: Connectiviteit van apparaten en inkomend telemetrie-Azure Digital Apparaatdubbels | Microsoft Docs
description: Overzicht van het gebruik van een apparaat onboarding met Azure Digital Apparaatdubbels
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 4e7a179980abb164754941f345d2ee9ae7f5c9de
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930393"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Apparaatconnectiviteit en inkomende telemetriegegevens

De telemetriegegevens die worden verzonden door apparaten en Sens oren vormen de backbone van een IoT-oplossing. Als u deze verschillende resources wilt weer geven en deze wilt beheren in de context van een locatie, zijn de belangrijkste problemen in de IoT-app-ontwikkeling. Azure Digital Apparaatdubbels vereenvoudigt het proces van het ontwikkelen van IoT-oplossingen door apparaten en Sens oren te voorzien van een ruimtelijke Intelligence-grafiek.

Als u aan de slag wilt gaan, maakt u een Azure IoT Hub-resource aan de basis van de ruimtelijke grafiek. Met de IoT Hub resource kunnen alle apparaten onder de hoofd ruimte berichten verzenden. Nadat de IoT Hub is gemaakt, registreert u apparaten met Sens oren binnen het digitale Apparaatdubbels-exemplaar. De apparaten kunnen via de [Azure IOT Device SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks)gegevens verzenden naar een Digital apparaatdubbels-service.

Raadpleeg de [zelf studie voor het implementeren en configureren van digitale apparaatdubbels](tutorial-facilities-setup.md)voor een stapsgewijze hand leiding voor het maken van apparaten op de onboarding. In één oogopslag zijn de stappen:

- Implementeer een Digital Apparaatdubbels-exemplaar vanuit het [Azure Portal](https://portal.azure.com).
- Maak ruimten in uw grafiek.
- Maak een IoT Hub resource en wijs deze toe aan een ruimte in uw grafiek.
- Maak apparaten en Sens oren in uw grafiek en wijs deze toe aan de ruimten die u in de vorige stappen hebt gemaakt.
- Maak een Matcher om telemetrie-berichten te filteren op basis van voor waarden.
- Maak een door de [gebruiker gedefinieerde functie](concepts-user-defined-functions.md)en wijs deze toe aan een ruimte in de grafiek voor aangepaste verwerking van uw telemetrie-berichten.
- Wijs een rol toe om de door de gebruiker gedefinieerde functie toe te staan om toegang te krijgen tot de grafiek gegevens.
- Haal de IoT Hub apparaat connection string van de Digital Apparaatdubbels Management-Api's.
- Configureer het apparaat connection string op het apparaat met de Azure IoT Device SDK.

In de volgende secties vindt u informatie over het ophalen van de IoT Hub apparaat connection string van de Digital Apparaatdubbels Management API. U leert ook hoe u de IoT Hub indeling voor telemetrie-berichten kunt gebruiken voor het verzenden van telemetrie op basis van Sens oren. Digital Apparaatdubbels vereist elk stukje telemetrie dat het wordt gekoppeld aan een sensor binnen de ruimtelijke grafiek. Deze vereiste zorgt ervoor dat de gegevens worden verwerkt en gerouteerd binnen de juiste ruimtelijke context.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>De IoT Hub apparaat-connection string ophalen uit de beheer-API

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Haal een GET-aanroep op de apparaat-API met een `includes=ConnectionString` para meter op om de IoT Hub apparaat connection string op te halen. Filter op de GUID van het apparaat of de hardware-ID om het opgegeven apparaat te zoeken.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parameter | Vervangen door |
| --- | --- |
| *YOUR_DEVICE_GUID* | De apparaat-ID |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Parameterwaarde | Vervangen door |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | De hardware-ID van het apparaat |

In de nettolading van de reactie kopieert u de eigenschap **Connections Tring** van het apparaat. U gebruikt deze wanneer u de Azure IoT Device SDK aanroept om gegevens naar Digital Apparaatdubbels te verzenden.

## <a name="device-to-cloud-message"></a>Apparaat-naar-Cloud-bericht

U kunt de bericht indeling en payload van uw apparaat aanpassen aan de behoeften van uw oplossing. Gebruik een wille keurig gegevens contract dat kan worden geserialiseerd in een byte matrix of-stroom die wordt ondersteund door de [Azure IOT Device client bericht klasse, Message (byte [] byte array)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Het bericht kan een aangepaste binaire indeling van uw keuze zijn, zolang u het gegevens contract in een bijbehorende door de gebruiker gedefinieerde functie hebt gedecodeerd. Er is slechts één vereiste voor een apparaat-naar-Cloud-bericht. Een set eigenschappen onderhouden om ervoor te zorgen dat uw bericht correct wordt gerouteerd naar de verwerkings engine.

### <a name="telemetry-properties"></a>Telemetrie-eigenschappen

 De inhoud van de nettolading van een **bericht** kan wille keurige gegevens tot 256 KB groot zijn. Er worden enkele vereisten verwacht voor eigenschappen van het type [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) . De tabel bevat de vereiste en optionele eigenschappen die door het systeem worden ondersteund.

| Naam van eigenschap | Waarde | Vereist | Beschrijving |
|---|---|---|---|
| **DigitalTwins-telemetrie** | 1.0 | Ja | Een constante waarde die een bericht identificeert aan het systeem. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Ja | Een unieke id van de sensor die het **bericht**verzendt. Deze waarde moet overeenkomen met de eigenschap **HardwareId** van een object voor het systeem om het te verwerken. Bijvoorbeeld `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Nee | Een datum reeks in [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -notatie waarmee de steekproef tijd van de nettolading wordt geïdentificeerd. Bijvoorbeeld `2018-09-20T07:35:00.8587882-07:00`. |
| **Correlatie** | `string` | Nee | Een UUID die wordt gebruikt voor het traceren van gebeurtenissen in het systeem. Bijvoorbeeld `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Uw bericht verzenden naar Digital Apparaatdubbels

Gebruik de aanroep DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) of [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) om uw bericht naar Digital apparaatdubbels te verzenden.

## <a name="next-steps"></a>Volgende stappen

- Lees [Azure Digital apparaatdubbels data processing en door de gebruiker gedefinieerde functies](concepts-user-defined-functions.md)voor meer informatie over de mogelijkheden van Azure Digital apparaatdubbels data processing en door de gebruiker gedefinieerde functies.
