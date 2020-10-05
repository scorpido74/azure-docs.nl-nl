---
title: IoT Plug en Play-bibliotheken en Sdk's
description: Informatie over de apparaten en service bibliotheken die beschikbaar zijn voor het ontwikkelen van IoT Plug en Play-oplossingen.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 87e701bfd742c48cf7d99b2fa09fa408e900a77d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714828"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Micro soft Sdk's voor IoT Plug en Play

Met de IoT Plug en Play-bibliotheken en Sdk's kunnen ontwikkel aars IoT-oplossingen bouwen met behulp van verschillende programmeer talen op meerdere platforms. De volgende tabel bevat koppelingen naar voor beelden en Quick starts waarmee u aan de slag kunt gaan:

## <a name="device-sdks"></a>Apparaat-Sdk's

| Taal | Pakket | Code opslagplaats | Voorbeelden | Snelstartgids | Naslaginformatie |
|---|---|---|---|---|---|
| C-apparaat | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Verbinding maken met IoT Hub](quickstart-connect-device-c.md) | [Verwijzing](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-apparaat | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Verbinding maken met IoT Hub](quickstart-connect-device-csharp.md) | [Verwijzing](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet&preserve-view=true) |
| Java-apparaat | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Verbinding maken met IoT Hub](quickstart-connect-device-java.md) | [Verwijzing](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable&preserve-view=true) |
| Python-apparaat | [PIP 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Verbinding maken met IoT Hub](quickstart-connect-device-python.md) | [Verwijzing](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python&preserve-view=true) |
| Knoop punt-apparaat | [NPM 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Verbinding maken met IoT Hub](quickstart-connect-device-node.md) | [Verwijzing](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest&preserve-view=true) |
| Inge sloten C-apparaat | N.v.t. | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Voorbeelden](howto-use-embedded-c.md#samples) | [Embedded C gebruiken](howto-use-embedded-c.md) | N.v.t.

## <a name="service-sdks"></a>Service-SDK's

| Platform  | Pakket | Code opslagplaats | Voorbeelden | Snelstartgids | Naslaginformatie |
|---|---|---|---|---|---|
| .NET-IoT Hub-service | [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | N.v.t. | [Verwijzing](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet&preserve-view=true) |
| Java-IoT Hub-service | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | N.v.t. | [Verwijzing](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable&preserve-view=true) |
| Node-IoT Hub-service | [NPM 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | N.v.t. | [Verwijzing](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest&preserve-view=true) |
| Python-Digital Apparaatdubbels-service | [PIP 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interactie met IoT Hub Digital Apparaatdubbelse API](quickstart-service-python.md) | N.v.t. |
| Node-Digital Apparaatdubbels-service | [NPM 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interactie met IoT Hub Digital Apparaatdubbelse API](quickstart-service-node.md) | N.v.t. |

## <a name="next-steps"></a>Volgende stappen

Als u de Sdk's en bibliotheken wilt uitproberen, raadpleegt u de  [hand leiding voor ontwikkel aars](concepts-developer-guide-device-csharp.md) en de Quick Start van het [apparaat](quickstart-connect-device-c.md) en de [service Snelstartgids](quickstart-service-node.md).
