---
title: De Azure IoT SDK's begrijpen | Microsoft Documenten
description: Handleiding voor ontwikkelaars - informatie over en koppelingen naar de verschillende Azure IoT-apparaat- en service-SDK's die u gebruiken om apparaat-apps en back-end-apps te bouwen.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: be5fae45ee513dddf002995ce9c37c6b6565f50c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258385"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Azure IoT Hub SDKs begrijpen en gebruiken

Er zijn twee categorieën software development kits (SDKs) voor het werken met IoT Hub:

* **Met IoT Hub Device SDKs** u apps bouwen die op uw IoT-apparaten worden uitgevoerd met behulp van de client of moduleclient van het apparaat. Deze apps verzenden telemetrie naar uw IoT-hub en kunnen eventueel berichten, taken, methoden of dubbele updates ontvangen van uw IoT-hub.  U moduleclient ook gebruiken om [modules](../iot-edge/iot-edge-modules.md) te maken voor [Azure IoT Edge-runtime.](../iot-edge/about-iot-edge.md)

* **Met IoT Hub Service SDK's** u backend-toepassingen bouwen om uw IoT-hub te beheren en optioneel berichten verzenden, taken plannen, directe methoden aanroepen of gewenste eigenschapsupdates verzenden naar uw IoT-apparaten of -modules.

Daarnaast bieden we ook een set SDK's voor het werken met de [Device Provisioning Service.](../iot-dps/about-iot-dps.md)
* **Met inrichtingsapparatuur-SDK's** u apps bouwen die op uw IoT-apparaten worden uitgevoerd om te communiceren met de Service Voor het inrichten van apparaten.

* **Met De Inprovisioning Service SDK's** u backend-toepassingen bouwen om uw inschrijvingen in de Service Voor het inrichten van apparaten te beheren.

Meer informatie over de [voordelen van het ontwikkelen met Azure IoT SDKs.](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>Compatibiliteit met OS-platforms en hardware

Ondersteunde platforms voor de SDKs zijn te vinden in [Azure IoT SDKs Platform Support.](iot-hub-device-sdk-platform-support.md)

Zie de [azure certified voor IoT-apparaatcatalogus](https://catalog.azureiotsolutions.com/) of afzonderlijke opslagplaatsen voor meer informatie over SDK-compatibiliteit met specifieke hardwareapparaten.

## <a name="azure-iot-hub-device-sdks"></a>Azure IoT Hub Device SDKs

De SDK's van het Microsoft Azure IoT-apparaat bevatten code die het bouwen van toepassingen vergemakkelijkt die verbinding maken met en worden beheerd door Azure IoT Hub-services.

Azure IoT Hub-apparaat SDK voor .NET: 

* Download van [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  De naamruimte is Microsoft.Azure.Devices.Clients, dat IoT Hub Device Clients (DeviceClient, ModuleClient) bevat.
* [Broncode](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-verwijzing](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Moduleverwijzing](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Azure IoT Hub-apparaat SDK voor C (ANSI C - C99):

* Installeren vanaf [apt-get, MBED, Arduino IDE of iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Broncode](https://github.com/Azure/azure-iot-sdk-c)
* [De C-apparaatSDK compileren](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [API-verwijzing](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Moduleverwijzing](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Het overzetten van de C SDK naar andere platforms](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Ontwikkelaarsdocumentatie](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) voor informatie over cross-compileren, aan de slag gaan op verschillende platformen, enz.
* [Informatie over azure IoT Hub C SDK-bronverbruik](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Azure IoT Hub-apparaat SDK voor Java: 

* Toevoegen aan [Maven-project](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Broncode](https://github.com/Azure/azure-iot-sdk-java)
* [API-verwijzing](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Moduleverwijzing](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

Azure IoT Hub-apparaat SDK voor Node.js: 

* Installeren vanaf [npm](https://www.npmjs.com/package/azure-iot-device)
* [Broncode](https://github.com/Azure/azure-iot-sdk-node)
* [API-verwijzing](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Moduleverwijzing](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Azure IoT Hub-apparaat SDK voor Python: 

* Installeren vanaf [pip](https://pypi.org/project/azure-iot-device/)
* [Broncode](https://github.com/Azure/azure-iot-sdk-python)
* [API-verwijzing](https://docs.microsoft.com/python/api/azure-iot-device)

Azure IoT Hub-apparaat SDK voor iOS: 

* Installeren vanaf [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-ios)
* API-verwijzing: zie [C API-verwijzing](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>Azure IoT Hub Service SDKs

De Azure IoT-service SDK's bevatten code om het bouwen van toepassingen te vergemakkelijken die rechtstreeks met IoT Hub communiceren om apparaten en beveiliging te beheren.

Azure IoT Hub-service SDK voor .NET:

* Download van [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  De naamruimte is Microsoft.Azure.Devices, dat IoT Hub Service Clients (RegistryManager, ServiceClients) bevat.
* [Broncode](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-verwijzing](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Azure IoT Hub-service SDK voor Java: 

* Toevoegen aan [Maven-project](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Broncode](https://github.com/Azure/azure-iot-sdk-java)
* [API-verwijzing](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Azure IoT Hub-service SDK voor Node.js: 

* Downloaden van [npm](https://www.npmjs.com/package/azure-iothub)
* [Broncode](https://github.com/Azure/azure-iot-sdk-node)
* [API-verwijzing](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Azure IoT Hub-service SDK voor Python: 

* Downloaden van [pip](https://pypi.python.org/pypi/azure-iot-hub/)
* [Broncode](https://github.com/Azure/azure-iot-sdk-python/tree/master)

Azure IoT Hub-service SDK voor C: 

* Download van [apt-get, MBED, Arduino IDE of NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Broncode](https://github.com/Azure/azure-iot-sdk-c)

Azure IoT Hub-service SDK voor iOS: 

* Installeren vanaf [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Zie de leesmijbestanden in de GitHub-repositories voor informatie over het gebruik van taal- en platformspecifieke pakketbeheerders om binaire bestanden en afhankelijkheden van uw ontwikkelmachine te installeren.

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure Provisioning SDKs

Met **de Microsoft Azure Provisioning SDK's** u apparaten inrichten op uw IoT-hub met behulp van de Service [Apparaatinrichting.](../iot-dps/about-iot-dps.md)

Azure Provisioning device and service SDKs for C#:

* Downloaden van [Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) en [Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) van NuGet.
* [Broncode](https://github.com/Azure/azure-iot-sdk-csharp/)
* [API-verwijzing](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Azure Provisioning device and service SDKs for C:

* Installeren vanaf [apt-get, MBED, Arduino IDE of iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Broncode](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [API-verwijzing](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Azure Provisioning device and service SDKs for Java:

* Toevoegen aan [Maven-project](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Broncode](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [API-verwijzing](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Azure Provisioning device and service SDKs for Node.js:

* [Broncode](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [API-verwijzing](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* [Device SDK](https://badge.fury.io/js/azure-iot-provisioning-device) en [Service SDK](https://badge.fury.io/js/azure-iot-provisioning-service) downloaden vanaf npm

Azure Provisioning device and service SDKs for Python:

* [Broncode](https://github.com/Azure/azure-iot-sdk-python)
* [Device SDK](https://pypi.org/project/azure-iot-device/) en [Service SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/) downloaden van pip

## <a name="next-steps"></a>Volgende stappen

Azure IoT SDK's bieden ook een reeks tools om te helpen bij de ontwikkeling:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): een cross-platform command line tool om problemen met betrekking tot de verbinding met IoT Hub te diagnosticeren.
* [azure-iot-explorer:](https://github.com/Azure/azure-iot-explorer)een cross-platform desktopapplicatie om verbinding te maken met uw IoT Hub en iot-apparaten toe te voegen/beheren/communiceren.

Relevante documenten met betrekking tot ontwikkeling met behulp van de Azure IoT SDK's:
* Meer informatie over [het beheren van connectiviteit en betrouwbare berichten](iot-hub-reliability-features-in-sdks.md) met behulp van de IoT Hub SDKs.
* Meer informatie over hoe je je [ontwikkelen voor mobiele platforms](iot-hub-how-to-develop-for-mobile-devices.md) zoals iOS en Android.
* [Ondersteuning voor Azure IoT SDK-platform](iot-hub-device-sdk-platform-support.md)


Andere referentieonderwerpen in deze IoT Hub-ontwikkelaarshandleiding zijn:

* [IoT Hub-eindpunten](iot-hub-devguide-endpoints.md)
* [IoT Hub-querytaal voor apparaattweelingen, taken en berichtroutering](iot-hub-devguide-query-language.md)
* [Quota en beperkingen](iot-hub-devguide-quotas-throttling.md)
* [Ondersteuning voor IoT Hub MQTT](iot-hub-mqtt-support.md)
* [Api-verwijzing naar IoT Hub REST](/rest/api/iothub/)
