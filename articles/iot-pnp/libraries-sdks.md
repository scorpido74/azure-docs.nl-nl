---
title: IoT Plug en Play-bibliotheken en Sdk's
description: Informatie over de apparaten en service bibliotheken die beschikbaar zijn voor het ontwikkelen van IoT Plug en Play-oplossingen.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064329"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>IoT Plug en Play-bibliotheken en Sdk's

Met de IoT Plug en Play-bibliotheken en Sdk's kunnen ontwikkel aars IoT-oplossingen bouwen met behulp van verschillende programmeer talen op meerdere platforms. De volgende tabel bevat koppelingen naar voor beelden en Quick starts waarmee u aan de slag kunt gaan:

## <a name="microsoft-supported-libraries-and-sdks"></a>Door micro soft ondersteunde bibliotheken en Sdk's

| Platform | Bibliotheek/pakket | Broncode | Voorbeeld | Snelstartgids | Naslaginformatie |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Apparaat-SDK op apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Voor beelden van digitale dubbele client](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Verbinding maken met IoT Hub](./quickstart-connect-pnp-device-c-linux.md) | [Naslaginformatie](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Apparaat-SDK op Vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Voor beelden van digitale dubbele client](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Verbinding maken met IoT Hub](./quickstart-connect-pnp-device-c-windows.md) | [Naslaginformatie](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [SDK voor apparaten op INSLUITEN](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Voor beelden van digitale dubbele client](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Naslaginformatie](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [SDK voor apparaat in Arduino IDE](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Voor beelden van digitale dubbele client](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Naslaginformatie](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [Apparaat-SDK op CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Voor beelden van digitale dubbele client](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Naslaginformatie](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Digitale dubbele steek proeven](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Verbinding maken met IoT Hub](./quickstart-connect-pnp-device-csharp.md) | [Naslaginformatie](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Digitale dubbele steek proeven](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Verbinding maken met IoT Hub](./quickstart-connect-pnp-device-java.md) | [Naslaginformatie](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Digitale dubbele steek proeven](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Verbinding maken met IoT Hub](./quickstart-connect-pnp-device-node.md) | [Naslaginformatie](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>Ondersteuning voor IoT Hub

IoT Plug en Play-apparaten worden alleen ondersteund door [IOT-hubs met gratis en Standard-laag](../iot-hub/iot-hub-scaling.md).

## <a name="next-steps"></a>Volgende stappen

Naast de apparaat-Sdk's en-bibliotheken kunt u REST-Api's gebruiken om te communiceren met de model opslagplaatsen.