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
ms.openlocfilehash: 5638cd9973c6a4df809e0b200efe85b067aae026
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407792"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Micro soft Sdk's voor IoT Plug en Play

Met de IoT Plug en Play-bibliotheken en Sdk's kunnen ontwikkel aars IoT-oplossingen bouwen met behulp van verschillende programmeer talen op meerdere platforms. De volgende tabel bevat koppelingen naar voor beelden en Quick starts waarmee u aan de slag kunt gaan:

## <a name="device-sdks-ga"></a>Apparaat-Sdk's (GA)

| Taal | Pakket | Code opslagplaats | Voorbeelden | Snelstartgids | Naslaginformatie |
|---|---|---|---|---|---|
| C-apparaat | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/releases/tag/2020-07-19) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-c/tree/2020-07-19/iothub_client/samples/pnp) | [Verbinding maken met IoT Hub](quickstart-connect-device-c.md) | [Verwijzing](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-apparaat | [NuGet 1.27.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/1.27.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples/PnpDeviceSamples) | [Verbinding maken met IoT Hub](quickstart-connect-device-csharp.md) | [Verwijzing](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet) |
| Java-apparaat | [Maven 1.24.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client/1.24.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Verbinding maken met IoT Hub](quickstart-connect-device-java.md) | [Verwijzing](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable) |
| Python-apparaat | [PIP 2.1.4](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Verbinding maken met IoT Hub](quickstart-connect-device-python.md) | [Verwijzing](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python) |
| Knoop punt-apparaat | [NPM 1.17.0](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Verbinding maken met IoT Hub](quickstart-connect-device-node.md) | [Verwijzing](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest) |

## <a name="device-sdks-preview"></a>Apparaat-Sdk's (preview-versie)

| Taal | Code opslagplaats/voor beelden |
|---|---|
|Azure SDK voor embedded| [GitHub](https://github.com/Azure/azure-sdk-for-c/#) |
|Azure RTO'S IoT-middleware| [GitHub](https://github.com/azure-rtos/azure-iot-preview#) |
|Hand leidingen aan de slag met Azure RTO'S | [GitHub](https://github.com/azure-rtos/getting-started) |

## <a name="service-sdks-preview"></a>Service-Sdk's (preview-versie)

| Taal | Pakket | Code opslagplaats | Voorbeelden | Snelstartgids | Naslaginformatie |
|---|---|---|---|---|---|
| Preview van .NET-IoT Hub-service | [NuGet 1.27.1-Preview-002](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.27.1-preview-002 ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh/iothub/service/samples/PnpServiceSamples) | N.v.t. | N.v.t. |
| Preview van Java-IoT Hub-service | [Maven 1.1.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client-preview/1.1.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh/service/iot-service-samples/pnp-service-sample) | N.v.t. | N.v.t. |
| Preview-versie van node-IoT Hub-service | [NPM 1.12.4-PnP-refresh. 4](https://www.npmjs.com/package/azure-iothub/v/1.12.4-pnp-refresh.4) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/service/samples) | N.v.t. | N.v.t. |
| Preview-versie van python-IoT Hub/Digital Apparaatdubbels-service | [PIP 2.2.1 RC1](https://pypi.org/project/azure-iot-hub/2.2.1rc1/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh/azure-iot-hub/samples) | [Interactie met IoT Hub Digital Apparaatdubbelse API](quickstart-service-python.md) | N.v.t. |
| Knoop punt-Digital Apparaatdubbels service preview | [NPM 1.0.0-PnP-refresh. 3](https://www.npmjs.com/package/azure-iot-digitaltwins-service/v/1.0.0-pnp-refresh.3) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/digitaltwins/samples/service/javascript) | [Interactie met IoT Hub Digital Apparaatdubbelse API](quickstart-service-node.md) | N.v.t. |

## <a name="next-steps"></a>Volgende stappen

Als u de Sdk's en bibliotheken wilt uitproberen, raadpleegt u de [hand leiding voor ontwikkel aars](concepts-developer-guide.md) en de Quick Start van het [apparaat](quickstart-connect-device-c.md) en de [service Snelstartgids](quickstart-service-node.md).
