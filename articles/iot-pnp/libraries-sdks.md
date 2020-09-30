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
ms.openlocfilehash: 6ea80c88f2c16614c8568bc6ccfa3f4308e954b0
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577302"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Micro soft Sdk's voor IoT Plug en Play

Met de IoT Plug en Play-bibliotheken en Sdk's kunnen ontwikkel aars IoT-oplossingen bouwen met behulp van verschillende programmeer talen op meerdere platforms. De volgende tabel bevat koppelingen naar voor beelden en Quick starts waarmee u aan de slag kunt gaan:

## <a name="device-sdks"></a>Apparaat-Sdk's

| Taal | Pakket | Code opslagplaats | Voorbeelden | Snelstart | Referentie |
|---|---|---|---|---|---|
| C-apparaat | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Verbinding maken met IoT Hub](quickstart-connect-device-c.md) | [Verwijzing](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-apparaat | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Verbinding maken met IoT Hub](quickstart-connect-device-csharp.md) | [Verwijzing](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet&preserve-view=true) |
| Java-apparaat | [Maven 1.25.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Verbinding maken met IoT Hub](quickstart-connect-device-java.md) | [Verwijzing](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable&preserve-view=true) |
| Python-apparaat | [PIP 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Verbinding maken met IoT Hub](quickstart-connect-device-python.md) | [Verwijzing](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python&preserve-view=true) |
| Knoop punt-apparaat | [NPM 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Verbinding maken met IoT Hub](quickstart-connect-device-node.md) | [Verwijzing](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest&preserve-view=true) |

## <a name="device-sdks-preview"></a>Apparaat-Sdk's (preview-versie)

| Taal | Code opslagplaats/voor beelden |
|---|---|
|Azure SDK voor embedded| [GitHub](https://github.com/Azure/azure-sdk-for-c/#) |
|Azure RTO'S IoT-middleware| [GitHub](https://github.com/azure-rtos/azure-iot-preview#) |
|Hand leidingen aan de slag met Azure RTO'S | [GitHub](https://github.com/azure-rtos/getting-started) |

## <a name="service-sdks"></a>Service-SDK's

| Taal | Pakket | Code opslagplaats | Voorbeelden | Snelstart | Referentie |
|---|---|---|---|---|---|
| .NET-IoT Hub-service | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | N.v.t. | [Verwijzing](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet&preserve-view=true) |
| Java-IoT Hub-service | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | N.v.t. | [Verwijzing](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable&preserve-view=true) |
| Node-IoT Hub-service | [NPM 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | N.v.t. | [Verwijzing](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest&preserve-view=true) |
| Python-Digital Apparaatdubbels-service | [PIP 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interactie met IoT Hub Digital Apparaatdubbelse API](quickstart-service-python.md) | N.v.t. |
| Node-Digital Apparaatdubbels-service | [NPM 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interactie met IoT Hub Digital Apparaatdubbelse API](quickstart-service-node.md) | N.v.t. |

## <a name="next-steps"></a>Volgende stappen

Als u de Sdk's en bibliotheken wilt uitproberen, raadpleegt u de  [hand leiding voor ontwikkel aars](concepts-developer-guide-device-csharp.md) en de Quick Start van het [apparaat](quickstart-connect-device-c.md) en de [service Snelstartgids](quickstart-service-node.md).
