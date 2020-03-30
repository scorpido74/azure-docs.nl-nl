---
title: Ontwikkelen voor mobiele apparaten met Azure IoT SDKs | Microsoft Documenten
description: Handleiding voor ontwikkelaars - Meer informatie over het ontwikkelen voor mobiele apparaten met Azure IoT Hub SDKs.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: robinsh
ms.openlocfilehash: 945b02003a443c04e692fdc06ca5714de362d074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883082"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Ontwikkelen voor mobiele apparaten met Azure IoT SDKs

Dingen in het Internet of Things kan verwijzen naar een breed scala van apparaten met verschillende mogelijkheden: sensoren, microcontrollers, slimme apparaten, industriële gateways, en zelfs mobiele apparaten.  Een mobiel apparaat kan een IoT-apparaat zijn, waarbij het telemetrie van apparaat naar cloud verzendt en wordt beheerd door de cloud.  Het kan ook het apparaat zijn waarop een back-endservicetoepassing wordt uitgevoerd, die andere IoT-apparaten beheert.  In beide gevallen kunnen [Azure IoT Hub SDK's](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) worden gebruikt om toepassingen te ontwikkelen die werken voor mobiele apparaten.  

## <a name="develop-for-native-ios-platform"></a>Ontwikkelen voor native iOS-platform

Azure IoT Hub SDK's bieden native ondersteuning voor iOS-platforms via Azure IoT Hub C SDK.  Je het zien als een iOS SDK die je opnemen in je Swift of Objective C XCode-project.  Er zijn twee manieren om de C SDK op iOS te gebruiken:

* Gebruik de CocoaPod-bibliotheken in het XCode-project rechtstreeks.  
* Download de broncode voor C SDK en build voor iOS-platform naar aanleiding van de [buildinstructie](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) voor MacOS.  

Azure IoT Hub C SDK is geschreven in C99 voor maximale draagbaarheid naar verschillende platforms.  Het portingproces omvat het schrijven van een dunne adoptielaag voor de platformspecifieke componenten, die hier voor [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx)te vinden zijn.  De functies in de C SDK kunnen worden gebruikt op het iOS-platform, inclusief de ondersteunde Azure IoT Hub-primitieven en SDK-specifieke functies, zoals het beleid voor het opnieuw proberen voor netwerkbetrouwbaarheid.  De interface voor iOS SDK is ook vergelijkbaar met de interface voor Azure IoT Hub C SDK.  

Deze documentatie doorloopt hoe u een apparaattoepassing of servicetoepassing op een iOS-apparaat ontwikkelen:

* [Snelstartgids: Telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-ios.md)  
* [Berichten vanuit de cloud naar uw apparaat verzenden met IoT-hub](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Ontwikkelen met Azure IoT Hub CocoaPod-bibliotheken

Azure IoT Hub SDKs brengt een reeks Objective-C CocoaPod-bibliotheken uit voor iOS-ontwikkeling.  Zie [CocoaPods voor Microsoft Azure IoT voor](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md)de nieuwste lijst met CocoaPod-bibliotheken.  Zodra de relevante bibliotheken zijn opgenomen in uw XCode-project, zijn er twee manieren om IoT Hub-gerelateerde code te schrijven:

* Doelstelling C-functie: Als uw project is geschreven in Doelstelling-C, u API's rechtstreeks vanuit Azure IoT Hub C SDK bellen.  Als uw project is geschreven in `@objc func` Swift, u bellen voordat u uw functie maakt en overgaan tot het schrijven van alle logica met betrekking tot Azure IoT Hub met behulp van C- of Objective-C-code.  Een reeks voorbeelden die beide aantonen, zijn te vinden in de [monsteropslagplaats.](https://github.com/Azure-Samples/azure-iot-samples-ios)  

* C-voorbeelden opnemen: als u een C-apparaattoepassing hebt geschreven, u deze rechtstreeks in uw XCode-project verwijzen:
    * Voeg het voorbeeldbestand toe aan uw XCode-project van XCode.  
    * Voeg het kopbestand toe aan uw afhankelijkheid.  Een headerbestand is als voorbeeld opgenomen in de [voorbeeldopslagplaats.](https://github.com/Azure-Samples/azure-iot-samples-ios) Ga voor meer informatie naar de documentatiepagina van Apple voor [Objective-C.](https://developer.apple.com/documentation/objectivec)

## <a name="develop-for-android-platform"></a>Ontwikkelen voor Android-platform
Azure IoT Hub Java SDK ondersteunt Android-platform.  Voor de geteste specifieke API-versie u terecht op onze [platformondersteuningspagina](iot-hub-device-sdk-platform-support.md) voor de nieuwste update.

Deze documentatie loopt door hoe u een apparaattoepassing of servicetoepassing ontwikkelen op een Android-apparaat met Gradle en Android Studio:

* [Snelstartgids: Telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-android.md)  
* [Snelstart: een apparaat bedienen](quickstart-control-device-android.md) 

## <a name="next-steps"></a>Volgende stappen

* [Api-verwijzing naar IoT Hub REST](https://docs.microsoft.com/rest/api/iothub/)
* [Azure IoT C SDK-broncode](https://github.com/Azure/azure-iot-sdk-c)
