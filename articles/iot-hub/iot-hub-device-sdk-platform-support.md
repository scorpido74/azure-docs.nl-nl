---
title: Ondersteuning voor Azure IoT Device Sdk's-platform | Microsoft Docs
description: "Concepten: een lijst met platforms die worden ondersteund door de Sdk's van het Azure IoT-apparaat"
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: robinsh
ms.openlocfilehash: 9a64c6e476910ab4fe983fa949680f05fdded3ae
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70161866"
---
# <a name="azure-iot-sdks-platform-support"></a>Azure IoT Sdk's-platform ondersteuning

De [Azure IOT sdk's](iot-hub-devguide-sdks.md) zijn een set bibliotheken voor interactie met IOT hub en de Device Provisioning Service met brede taal-en platform ondersteuning. De Sdk's worden op de meest voorkomende platforms uitgevoerd en ontwikkel aars kunnen de C SDK naar een specifiek platform poort door de [richt lijnen voor poort](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)gebruik te volgen. 

Micro soft biedt ondersteuning voor verschillende besturings systemen/platforms/frameworks en kan worden uitgebreid met behulp van de Azure IoT C-SDK. Sommige worden officieel ondersteund door het team, gegroepeerd in lagen die het niveau van ondersteunings gebruikers vertegenwoordigen. *Volledig ondersteunde platforms* houdt in dat micro soft:

- Bouwt voortdurend end-to-end-tests en voert deze uit op basis van de LTS ondersteunde versie (s).  Om een test dekking te bieden in verschillende versies, testen we doorgaans op de nieuwste LTS-versie en de meest populaire versie.  Andere versies van hetzelfde platform kunnen worden ondersteund via compatibiliteit met platform versies.
- Biedt installatie richtlijnen of pakketten, indien van toepassing.
- Biedt volledige ondersteuning voor de platforms op GitHub.

Daarnaast heeft onze C SDK door een lijst met partners naar meer platforms gestroomd en wordt de ' platform Abstraction Layer (PAL) onderhouden. [Azure Certified voor IOT Device Catalog](https://catalog.azureiotsolutions.com/) bevat ook een lijst met besturingssysteem platforms waarop de diverse sdk's zijn getest. De Sdk's bouwen ook regel matig op deze platformen, met beperkte testen en ondersteuning:

* MBED2
* Arduino
* Windows CE 2013 (in oktober 2018)
* .NET Standard 1,3 met .NET Core 2,1 en .NET Framework 4,7
* Xamarin iOS, Android, UWP

## <a name="supported-platforms"></a>Ondersteunde platforms

Er worden verschillende platforms ondersteund.

### <a name="c-sdk"></a>C SDK

| OS                  | Arch | Programma's             | TLS-bibliotheek       |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | gcc-5.4.0            | openssl-1.0.2 g |
| Ubuntu 18,04 LTS    | X64  | gcc-7,3              | WolfSSL – 1,13    |
| Ubuntu 18,04 LTS    | X64  | Clang 6.0.X          | Openssl – 1.1.0 g  |
| OSX 10.13.4         | x64  | XCode 9.4.1          | Systeem eigen OSX        |
| Windows Server 2016 | x64  | Visual Studio 14.0.X | Veilige          |
| Windows Server 2016 | x86  | Visual Studio 14.0.X | Veilige          |
| Debian 9 uitrekken    | x64  | gcc-7,3              | Openssl – 1.1.0 f  |

### <a name="python-sdk"></a>Python-SDK

| OS                  | Arch | Programma's   | TLS-bibliotheek |
|---------------------|------|------------|-------------|
| Windows Server 2016 | x86  | Python 2.7 | openssl     |
| Windows Server 2016 | x64  | Python 2.7 | openssl     |
| Windows Server 2016 | x86  | Python 3,5 | openssl     |
| Windows Server 2016 | x64  | Python 3,5 | openssl     |
| Ubuntu 18,04 LTS    | x86  | Python 2.7 | openssl     |
| Ubuntu 18,04 LTS    | x86  | Python 3,4 | openssl     |
| MacOS-hoge-Sierra   | x64  | Python 2.7 | openssl     |

### <a name="net-sdk"></a>.NET SDK

| OS                  | Arch | Framework            | Standard          |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | .NET Core 2.1        | .NET Standard 2,0 |
| Windows Server 2016 | X64  | .NET Core 2.1        | .NET Standard 2,0 |
| Windows Server 2016 | X64  | .NET Framework 4,7   | .NET Standard 2,0 |
| Windows Server 2016 | X64  | .NET Framework 4.5.1 | N/A               |

### <a name="nodejs-sdk"></a>Node.js SDK

| OS                                           | Arch | Knooppunt versie    |
|----------------------------------------------|------|-----------------|
| Ubuntu 16,04 LTS (met behulp van node 6 docker-installatie kopie) | X64  | LTS en actueel |
| Windows Server 2016                          | X64  | LTS en actueel |

### <a name="java-sdk"></a>Java-SDK

| OS                  | Arch | Java-versie |
|---------------------|------|--------------|
| Ubuntu 16.04 LTS    | X64  | Java 8       |
| Windows Server 2016 | X64  | Java 8       |
| Android API 28 | X64  | Java 8       |
| Android-dingen | X64  | Java 8      |

## <a name="partner-supported-platforms"></a>Door partners ondersteunde platforms

Klanten kunnen onze platform ondersteuning uitbreiden door de Azure IoT C SDK te poorten, met name het maken van de platform Abstraction Layer (PAL) van de SDK. Micro soft werkt samen met partners om uitgebreide ondersteuning te bieden. Een lijst met partners heeft de C SDK genoteerd op meer platforms en het onderhoud van de PAL.

| Partner             | Apparaten                            | Koppelen                     | Ondersteuning |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT-modem     | [Qualcomm LTE voor IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| STe elektronica | STM32L4-serie <br/> STM32F4-serie <br/>  STM32F7-serie <br/>  STM32L4 Discovery Kit voor IoT-knoop punt    | [X-CUBE-CLOUD](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html) <br/> [X-CUBE-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html) <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Ondersteuning](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | LaunchPad CC3220SF </br> LaunchPad CC3220S </br> LaunchPad CC3235SF </br> LaunchPad CC3235S </br> LaunchPad MSP432E4 | [Azure IoT-invoeg toepassing voor SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E-forum](https://e2e.ti.com) <br/> [TI E2E-forum voor CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [TI E2E-forum voor MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="next-steps"></a>Volgende stappen

* [Apparaat- en service-SDK's](iot-hub-devguide-sdks.md)
* [Richt lijnen voor poort](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
