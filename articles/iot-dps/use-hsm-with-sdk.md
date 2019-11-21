---
title: Use different attestation mechanisms with the Azure IoT Hub Device Provisioning Service Client SDK
description: 'Azure-procedure: Verschillende attestation-mechanismen gebruiken met de SDK van de Device Provisioning Service-client in Azure'
author: robinsh
ms.author: robinsh
ms.date: 03/30/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 0cde591d2ec8c6f2f51c83b3f263c188c8cf2605
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228275"
---
# <a name="how-to-use-different-attestation-mechanisms-with-device-provisioning-service-client-sdk-for-c"></a>Verschillende attestation-mechanismen gebruiken met de SDK van de Device Provisioning Service-client voor C

In dit artikel wordt beschreven hoe u verschillende [attestation-mechanismen](concepts-security.md#attestation-mechanism) gebruikt met de SDK van de Device Provisioning Service-client voor C. U kunt een fysiek apparaat of een simulator gebruiken. The provisioning service supports authentication for two types of attestation mechanisms: X.509 and Trusted Platform Module (TPM).

## <a name="prerequisites"></a>Vereisten

Bereid de ontwikkelomgeving voor volgens de richtlijnen in het gedeelte De ontwikkelomgeving voorbereiden in de gids [Een gesimuleerd apparaat maken en inrichten](./quick-create-simulated-device.md).

### <a name="choose-an-attestation-mechanism"></a>Een attestation-mechanisme kiezen

Als apparaatfabrikant moet u eerst een attestation-mechanisme kiezen op basis van een van de ondersteunde typen. Momenteel biedt de [SDK van de Device Provisioning Service-client voor C](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) ondersteuning voor de volgende attestation-mechanismen: 

- [TPM (Trusted Platform Module)](https://en.wikipedia.org/wiki/Trusted_Platform_Module): TPM is een vastgestelde norm voor de meeste Windows-apparaatplatforms, evenals enkele Linux/Ubuntu-apparaten. Als apparaatfabrikant kunt u dit attestation-mechanisme kiezen als een van deze besturingssystemen worden uitgevoerd op uw apparaten, en u op zoek bent naar een vastgestelde norm. Met TPM-chips kunt u elk apparaat alleen afzonderlijk inschrijven bij Device Provisioning Service. U kunt de TPM-simulator voor ontwikkeldoeleinden gebruiken op een Windows- of Linux-ontwikkelmachine.

- [X.509](https://cryptography.io/en/latest/x509/): X.509-certificaten kunnen worden opgeslagen op relatief nieuwe chips, [Hardware Security Modules (HSM)](concepts-security.md#hardware-security-module) genoemd. Er wordt ook gewerkt in Microsoft, op RIoT- of DICE-chips, die de X.509-certificaten implementeren. Met X.509-chips kunt u apparaten bulksgewijs inschrijven in de portal. Het biedt ook ondersteuning voor bepaalde niet -Windows-besturingssystemen, zoals embedOS. Voor ontwikkeldoeleinden biedt de SDK van de Device Provisioning Service-client ook ondersteuning voor een X.509-apparaatsimulator. 

Zie [Beveiligingsconcepten](concepts-security.md) en [Concepten van automatische inrichting](/azure/iot-dps/concepts-auto-provisioning) van de IoT Hub Device Provisioning Service, voor meer informatie.

## <a name="enable-authentication-for-supported-attestation-mechanisms"></a>Verificatie inschakelen voor ondersteunde attestation-mechanismen

The SDK authentication mode (X.509 or TPM) must be enabled for the physical device or simulator before they can be enrolled in the Azure portal. Navigeer naar de hoofdmap voor azure-iot-sdk-c. Voer vervolgens de opgegeven opdracht uit, afhankelijk van de gekozen verificatiemodus:

### <a name="use-x509-with-simulator"></a>Use X.509 with simulator

The provisioning service ships with a Device Identity Composition Engine (DICE) emulator that generates an **X.509** certificate for authenticating the device. To enable **X.509** authentication, run the following command: 

```
cmake -Ddps_auth_type=x509 ..
```

Informatie met betrekking tot hardware met DICE vindt u [hier](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/).

### <a name="use-x509-with-hardware"></a>Use X.509 with hardware

The provisioning service can be used with **X.509** on other hardware. Er is een interface tussen de hardware en de SDK nodig om verbinding te maken. Neem contact op met uw HSM-fabrikant voor informatie over de interface.

### <a name="use-tpm"></a>TPM gebruiken

De inrichtingsservice kan verbinding maken met TPM-chips van Windows- en Linux-hardware met SAS-token. Voer de volgende opdracht uit om TPM-verificatie in te schakelen:

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>TPM met simulator gebruiken

Als u geen apparaat met TPM-chips hebt, kunt u een simulator gebruiken als u wilt ontwikkelen voor Windows-besturingssystemen. Voer de volgende opdracht uit om TPM-verificatie in te schakelen en de TPM-simulator uit te voeren:

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>De SDK bouwen
Bouw de SDK voordat u een apparaatregistratie maakt.

### <a name="linux"></a>Linux
- De SDK bouwen in Linux:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- Voor het maken van binaire foutopsporingsbestanden voegt u de bijbehorende CMake-optie toe aan de opdracht voor het genereren van het project, bijvoorbeeld:
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- Er zijn veel [CMake-configuratieopties](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html) beschikbaar voor het bouwen van de SDK. Zo kunt u bijvoorbeeld een van de beschikbare protocolstacks uitschakelen door een argument toe te voegen aan de opdracht voor het maken van een CMake-project:
    ```
    cmake -Duse_amqp=OFF ..
    ```
- U kunt ook een moduletest bouwen en uitvoeren:
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- Als u de SDK in Windows wilt bouwen, voer dan de volgende stappen uit om projectbestanden te genereren:
  - Open een 'Opdrachtprompt voor ontwikkelaars voor VS2015'
  - Voer de volgende CMake-opdrachten uit vanuit de hoofdmap van de opslagplaats:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake -G "Visual Studio 14 2015" ..
    ```
    Met deze opdracht bouwt u x86-bibliotheken. Wijzig het argument van de cmake-generator als u x64-bibliotheken wilt bouwen: 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- Als het project met succes is gegenereerd, wordt er onder de map `cmake` een Visual Studio-oplossingsbestand (.sln) weergegeven. De SDK bouwen:
    - Open **cmake\azure_iot_sdks.sln** in Visual Studio en bouw het, **of**
    - Voer de volgende opdracht uit achter de opdrachtprompt die u hebt gebruikt om de projectbestanden te genereren:
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- Gebruik het bijbehorende MSBuild-argument als u binaire debugbestanden wilt bouwen: 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- Er zijn veel CMake-configuratieopties beschikbaar voor het bouwen van de SDK. Zo kunt u bijvoorbeeld een van de beschikbare protocolstacks uitschakelen door een argument toe te voegen aan de opdracht voor het maken van een CMake-project:
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- U kunt ook moduletests bouwen en uitvoeren:
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>Bibliotheken die u moet opnemen
- Deze bibliotheken moeten worden opgenomen in de SDK:
    - De inrichtingsservice: dps_http_transport, dps_client, dps_security_client
    - IoTHub Security: iothub_security_client

## <a name="create-a-device-enrollment-entry-in-device-provisioning-services"></a>Een vermelding voor apparaatinschrijving maken in Device Provisioning Services

### <a name="tpm"></a>TPM
Als u een TPM gebruikt, volgt u de instructies in [Een gesimuleerd apparaat maken en inrichten met IoT Hub Device Provisioning Service](./quick-create-simulated-device.md) als u een vermelding voor een apparaatinschrijving wilt maken in Device Provisioning Service en de eerste keer opstarten wilt simuleren.

### <a name="x509"></a>X.509

1. Als u een apparaat wilt registreren in de inrichtingsservice, moet u de goedkeuringssleutel en registratie-id voor elk apparaat noteren. Deze worden weergegeven in het inrichtingshulpprogramma dat door de Client SDK is verstrekt. Voer de volgende opdracht om het basis-CA-certificaat (voor inschrijvingsgroepen) en het certificaat (voor afzonderlijke inschrijving) af te drukken:
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Meld u aan bij Azure Portal, klik in het linkermenu op de knop **Alle bronnen** en open Device Provisioning Service.
   - **X.509 Individual Enrollment**: On the provisioning service summary blade, select **Manage enrollments**. Selecteer het tabblad **Individual Enrollments** en klik bovenaan op de knop **Add**. Select **X.509** as the identity attestation *Mechanism*, upload the leaf certificate as required by the blade. Klik op de knop **Save** als u klaar bent. 
   - **X.509 Group Enrollment**: On the provisioning service  summary blade, select **Manage enrollments**. Selecteer het tabblad **Group Enrollments** en klik bovenaan op de knop **Add**. Select **X.509** as the identity attestation *Mechanism*, enter a group name and certification name, upload the CA/Intermediate certificate as required by the blade. Klik op de knop **Save** als u klaar bent. 

## <a name="enable-authentication-for-devices-using-a-custom-attestation-mechanism-optional"></a>Verificatie inschakelen voor apparaten met een aangepast attestation-mechanisme (optioneel)

> [!NOTE]
> Deze sectie is alleen van toepassing op apparaten die ondersteuning vereisen voor een aangepast platform of attestation-mechanismen die momenteel niet worden ondersteund door de SDK van de Device Provisioning Service voor C. In de SDK wordt de term 'HSM' vaak gebruikt in plaats van 'attestation-mechanisme'.

U moet eerst een opslagplaats en bibliotheek voor uw aangepaste attestation-mechanisme ontwikkelen:

1. Ontwikkel een bibliotheek voor toegang tot uw attestation-mechanisme. Voor dit project moet u een statische bibliotheek produceren die kan worden gebruikt voor de Device Provisioning SDK.

2. Implementeer de functies die zijn gedefinieerd in het volgende header-bestand, in de bibliotheek: 

    - Voor een aangepaste TPM: implementeer de functies die zijn gedefinieerd onder [HSM TPM API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api).  
    - Voor een aangepaste X.509: implementeer de functies die zijn gedefinieerd onder [HSM X509 API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api). 

Zodra uw bibliotheek zelfstandig kan uitbreiden, moet u deze integreren met de SDK van de Device Provisioning Service-client, door de SDK te koppelen aan de bibliotheek. :

1. Geef de aangepaste GitHub-opslagplaats en de bibliotheeknaam op in de volgende opdracht `cmake`:
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
2. Open het Visual Studio-oplossingsbestand dat is gebouwd met CMake (`\azure-iot-sdk-c\cmake\azure_iot_sdks.sln`), en bouw het. 

    - Tijdens het bouwproces wordt de SDK-bibliotheek gecompileerd.
    - Er wordt een poging gedaan de SDK te koppelen aan de aangepaste bibliotheek die in de opdracht `cmake` is gedefinieerd.

3. Voer de voorbeeld-app prov_dev_client_ll_sample onder Provision_Samples (onder `\azure-iot-sdk-c\cmake\provisioning_client\samples\prov_dev_client_ll_sample`) uit, om te verifiëren dat uw aangepaste attestation-mechanisme juist is geïmplementeerd.

## <a name="connecting-to-iot-hub-after-provisioning"></a>Na inrichting verbinding maken met IoT Hub

Once the device has been provisioned with the provisioning service, this API uses the specified authentication mode (**X.509** or TPM) to connect with IoT Hub: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```
