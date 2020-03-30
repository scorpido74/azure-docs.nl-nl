---
title: Ondersteuning voor Azure IoT-apparaat SDKs-platform | Microsoft Documenten
description: Open-source apparaat SDKs zijn beschikbaar op GitHub in C, .NET (C#), Java, Node.js en Python, om apparaten aan te sluiten op Azure IoT Hub en Device Provisioning Service (DPS).
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: 496b890cc49b6b6b9f15213a48472447f801b1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045119"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Ondersteuning voor Azure IoT Device SDKs Platform

Microsoft streeft ernaar om het universum van Azure IoT Hub-compatibele apparaten voortdurend uit te breiden. Microsoft publiceert open-source apparaat SDK's op GitHub om apparaten te verbinden met Azure IoT Hub en de Device Provisioning Service. De sdk's van het apparaat zijn beschikbaar voor C, .NET (C#), Java, Node.js en Python. Microsoft test elke SDK om ervoor te zorgen dat deze wordt uitgevoerd op de ondersteunde configuraties die hiervoor zijn beschreven in de [ondersteuningssectie van Microsoft SDK's en apparaatplatforms.](#microsoft-sdks-and-device-platform-support)

Naast de sdks van het apparaat biedt Microsoft verschillende andere mogelijkheden om klanten en ontwikkelaars in staat te stellen hun apparaten aan te sluiten op Azure IoT:

* Microsoft werkt samen met verschillende partnerbedrijven om hen te helpen bij het publiceren van ontwikkelingskits, gebaseerd op de Azure IoT C SDK, voor hun hardwareplatforms.

* Microsoft werkt samen met vertrouwde Microsoft-partners om een steeds groter wordende set apparaten te bieden die zijn getest en gecertificeerd voor Azure IoT. Zie de [Azure-gecertificeerde voor IoT-apparaatcatalogus voor](https://catalog.azureiotsolutions.com/)een huidige lijst met deze apparaten.

* Microsoft biedt een platformabstractielaag (PAL) in de Azure IoT Hub Device C SDK waarmee ontwikkelaars de SDK eenvoudig naar hun platform kunnen porten. Zie voor meer informatie de [richtlijnen voor het overzetten van de C SDK.](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)

In dit onderwerp vindt u informatie over de Microsoft SDKs en de platformconfiguraties die zij ondersteunen, evenals elk van de andere hierboven genoemde opties.

## <a name="microsoft-sdks-and-device-platform-support"></a>Ondersteuning voor Microsoft SDKs en apparaatplatform

Microsoft publiceert open-source SDK's op GitHub voor de volgende talen: C, .NET (C#), Node.js, Java en Python. De SDK's en hun afhankelijkheden worden in deze sectie weergegeven. De SDK's worden ondersteund op elk apparaatplatform dat aan deze afhankelijkheden voldoet.

Voor elk van de genoemde SDK's: Microsoft:

* Bouwt en voert voortdurend end-to-end tests uit tegen de masterbranch van de betreffende SDK in GitHub op verschillende populaire platforms.  Om testdekking te bieden in verschillende compilerversies, testen we over het algemeen tegen de nieuwste LTS-versie en de populairste versie.

* Biedt installatiebegeleiding of installatiepakketten indien van toepassing.

* Ondersteunt de SDKs op GitHub volledig met open-sourcecode, een pad voor klantbijdragen en betrokkenheid van productteams bij GitHub-problemen.

### <a name="c-sdk"></a>C SDK

Het [Azure IoT Hub C-apparaat SDK](https://github.com/Azure/azure-iot-sdk-c) is getest met en ondersteunt de volgende configuraties.

| OS                  | TLS-bibliotheek                  | Aanvullende eisen                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL of BearSSL | Berkeley stopcontacten</br></br>Draagbare interface voor besturingssysteem (POSIX)                       |
| iOS 12.2            | OpenSSL                      | XCode geëmuleerd in OSX 10.13.4                                                               |
| Windows 10-gezin   | Schannel                     |                                                                                             |
| Mbed OS 5.4         | Mbed TLS 2                   | [MXChip IoT dev kit](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure Sphere OS     | WolfSSL (WolfSSL)                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | BearSSL (BearSSL)                      | [ESP32 of ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python SDK

De [Azure IoT Hub Python-apparaat SDK](https://github.com/Azure/azure-iot-sdk-python) is getest met en ondersteunt de volgende configuraties.

| OS                  | Compiler                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7.*, 3,5 of hoger |
| MacOS High Sierra   | Python 2.7.*, 3,5 of hoger |
| Windows 10-gezin   | Python 2.7.*, 3,5 of hoger |

Alleen Python versie 3.5.3 of hoger ondersteunen de asynchrone API's, raden we aan versie 3.7 of hoger te gebruiken.

### <a name="net-sdk"></a>.NET SDK

Het [Azure IoT Hub .NET (C#)-apparaat SDK](https://github.com/Azure/azure-iot-sdk-csharp) is getest met en ondersteunt de volgende configuraties.

| OS                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| SKU's voor Windows 10-bureaublad en server   | .NET Core 2.1, .NET Framework 4.5.1 of .NET Framework 4.7 |

De .NET SDK kan ook worden gebruikt met Windows IoT Core met de [Azure Device Agent](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) of een aangepaste [NTService die RPC kan gebruiken om te communiceren met UWP-toepassingen.](https://docs.microsoft.com/samples/microsoft/windows-iotcore-samples/ntservice-rpc/)

### <a name="nodejs-sdk"></a>Node.js SDK

De [Azure IoT Hub Node.js-apparaat SDK](https://github.com/Azure/azure-iot-sdk-node) is getest met en ondersteunt de volgende configuraties.

| OS                  | Knooppuntversie    |
|---------------------|-----------------|
| Linux               | LTS en Huidige |
| Windows 10-gezin   | LTS en Huidige |

### <a name="java-sdk"></a>Java SDK

De [Azure IoT Hub Java-apparaat SDK](https://github.com/Azure/azure-iot-sdk-java) is getest met en ondersteunt de volgende configuraties.

| OS                     | Java-versie |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Windows 10-gezin x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>Partner ondersteunde ontwikkelingskits

Microsoft werkt samen met verschillende partners om ontwikkelingskits te leveren voor verschillende microprocessorarchitecturen. Deze partners hebben de Azure IoT C SDK overgezet naar hun platform. Partners maken en onderhouden de platformabstractielaag (PAL) van de SDK. Microsoft werkt samen met deze partners om uitgebreide ondersteuning te bieden.

| Partner             | Apparaten                            | Koppeling                     | Ondersteuning |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif (Espressif)           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT-modem     | [Qualcomm LTE voor IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Micro-elektronica | STM32L4-serie <br/> STM32F4-serie <br/>  STM32F7-serie <br/>  STM32L4 Discovery Kit voor IoT-knooppunt    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Ondersteuning](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instrumenten   | CC3220SF LaunchPad </br> CC3220S LaunchPad </br> CC3235SF LaunchPad </br> CC3235S LaunchPad </br> MSP432E4 LaunchPad | [Azure IoT-plug-in voor SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E Forum](https://e2e.ti.com) <br/> [TI E2E Forum voor CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [TI E2E Forum voor MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>De Microsoft Azure IoT C SDK overzetten

Als uw apparaatplatform niet onder een van de vorige secties valt, u overwegen de Azure IoT C SDK te overzetten. Het aanzetten van de C SDK bestaat in de eerste plaats uit de implementatie van de platformabstractielaag (PAL) van de SDK. De PAL definieert primitieven die de lijm tussen uw apparaat en functies op een hoger niveau in de SDK bieden. Zie [Porting Guidance voor](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)meer informatie.

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Microsoft-partners en gecertificeerde Azure IoT-apparaten

Microsoft werkt samen met een aantal partners om het Azure IoT-universum voortdurend uit te breiden met azure IoT-geteste en gecertificeerde apparaten.

* Zie [Microsoft Azure Certified for IoT Device Catalog](https://catalog.azureiotsolutions.com/)als u door Azure IoT-gecertificeerde apparaten wilt bladeren.

* Zie [Lid worden van het Certified for IoT-ecosysteem](https://catalog.azureiotsolutions.com/register)voor Azure Certified for IoT.

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Verbinding maken met IoT-hub zonder SDK

Als u een van de SDK's van het IoT Hub-apparaat niet gebruiken, u rechtstreeks verbinding maken met IoT Hub via de [IoT Hub REST API's](https://docs.microsoft.com/rest/api/iothub/) van elke toepassing die HTTPS-verzoeken en -antwoorden kan verzenden en ontvangen.

## <a name="support-and-other-resources"></a>Ondersteuning en andere bronnen

Als u problemen ondervindt tijdens het gebruik van de SDK's van het Azure IoT-apparaat, zijn er verschillende manieren om ondersteuning te zoeken. U een van de volgende kanalen proberen:

**Bugs melden** - Bugs in de sdks van het apparaat kunnen worden gerapporteerd op de uitgiftepagina van het desbetreffende GitHub-project. Fixes snel hun weg van het project in om product updates.

* [Azure IoT Hub C SDK-problemen](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Azure IoT Hub .NET (C#) SDK-problemen](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Azure IoT Hub Java SDK-problemen](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Azure IoT Hub Node.js SDK-problemen](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Azure IoT Hub Python SDK-problemen](https://github.com/Azure/azure-iot-sdk-python/issues)

**Microsoft Customer Support-team** : gebruikers met een [ondersteuningsplan](https://azure.microsoft.com/support/plans/) kunnen het Microsoft Customer Support-team inschakelen door rechtstreeks vanuit de [Azure-portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)een nieuwe ondersteuningsaanvraag te maken.

**Functieaanvragen** – Azure IoT-functieaanvragen worden bijgehouden via de [gebruikersstempagina van](https://feedback.azure.com/forums/321918-azure-iot)het product.

## <a name="next-steps"></a>Volgende stappen

* [Apparaat- en service-SDK's](iot-hub-devguide-sdks.md)
* [Richtlijnen voor het overzetten](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
