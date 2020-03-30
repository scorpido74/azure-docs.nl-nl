---
title: IoT Plug and Play-bibliotheken en SDK's
description: Informatie over de apparaat- en servicebibliotheken die beschikbaar zijn voor het ontwikkelen van IoT Plug and Play-oplossingen.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064329"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>IoT Plug and Play-bibliotheken en SDK's

De IoT Plug and Play-bibliotheken en SDK's stellen ontwikkelaars in staat om IoT-oplossingen te bouwen met behulp van verschillende programmeertalen op meerdere platforms. In de volgende tabel vindt u koppelingen naar voorbeelden en snelle starts waarmee u aan de slag kunt:

## <a name="microsoft-supported-libraries-and-sdks"></a>Door Microsoft ondersteunde bibliotheken en SDK's

| Platform | Bibliotheek/pakket | Broncode | Voorbeeld | Snelstartgids | Naslaginformatie |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Device SDK op apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digital Twin-clientvoorbeelden](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Verbinding maken met IoT Hub](./quickstart-connect-pnp-device-c-linux.md) | [Naslaginformatie](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Apparaat SDK op Vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digital Twin-clientvoorbeelden](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Verbinding maken met IoT Hub](./quickstart-connect-pnp-device-c-windows.md) | [Naslaginformatie](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [Device SDK op EMBED](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digital Twin-clientvoorbeelden](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Naslaginformatie](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [Device SDK in Arduino IDE](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digital Twin-clientvoorbeelden](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Naslaginformatie](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [Apparaat SDK op CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digital Twin-clientvoorbeelden](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Naslaginformatie](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Digital Twin monsters](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Verbinding maken met IoT Hub](./quickstart-connect-pnp-device-csharp.md) | [Naslaginformatie](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Digital Twin monsters](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Verbinding maken met IoT Hub](./quickstart-connect-pnp-device-java.md) | [Naslaginformatie](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Digital Twin monsters](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Verbinding maken met IoT Hub](./quickstart-connect-pnp-device-node.md) | [Naslaginformatie](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>Ondersteuning voor IoT Hub

IoT Plug and Play-apparaatmogelijkheden worden alleen ondersteund door [gratis en standaard IoT-hubs.](../iot-hub/iot-hub-scaling.md)

## <a name="next-steps"></a>Volgende stappen

Naast de sdk's en bibliotheken van het apparaat u REST API's gebruiken om te communiceren met de modelrepositories.