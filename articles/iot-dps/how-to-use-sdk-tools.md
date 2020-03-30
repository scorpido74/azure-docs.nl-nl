---
title: Azure IoT Hub Device Provisioning Service SDKs-hulpprogramma's gebruiken
description: In dit document worden de hulpprogramma's voor de ontwikkeling van Azure IoT Hub Device Provisioning Service (DPS) (SDK's) beoordeeld
author: robinsh
ms.author: robinsh
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: b817b3cfe47ed08cae9e7e0b1c2c24363f2ccfed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271523"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Hoe tools in de SDK's te gebruiken om de ontwikkeling voor provisioning te vereenvoudigen
De IoT Hub Device Provisioning Service vereenvoudigt het inrichtingsproces met zero-touch, just-in-time [automatische inrichting](concepts-auto-provisioning.md) op een veilige en schaalbare manier.  Beveiligingsattest in de vorm van X.509-certificaat of Trusted Platform Module (TPM) is vereist.  Microsoft werkt ook samen met [andere beveiligingshardwarepartners](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) om het vertrouwen in het beveiligen van IoT-implementatie te verbeteren. Inzicht in de hardware beveiligingsvereiste kan heel uitdagend zijn voor ontwikkelaars. Er is een set Azure IoT Provisioning Service SDK's beschikbaar, zodat ontwikkelaars een gemakslaag kunnen gebruiken voor het schrijven van clients die met de inrichtingsservice praten. De SDK's bieden ook voorbeelden voor veelvoorkomende scenario's en een reeks tools om beveiligingsattestation in ontwikkeling te vereenvoudigen.

## <a name="trusted-platform-module-tpm-simulator"></a>Simulator Trusted Platform Module (TPM)
[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) kan verwijzen naar een standaard voor het veilig opslaan van sleutels om het platform te verifiëren, of het kan verwijzen naar de I / O-interface die wordt gebruikt om te communiceren met de modules die de standaard implementeren. TPM's kunnen bestaan als discrete hardware, geïntegreerde hardware, firmware-gebaseerde of software-based.  In productie bevindt TPM zich op het apparaat, als discrete hardware, geïntegreerde hardware of op firmware gebaseerde hardware. In de testfase wordt een softwaregebaseerde TPM-simulator aan ontwikkelaars geleverd.  Deze simulator is alleen beschikbaar voor ontwikkeling op Windows-platform voor nu.

Stappen voor het gebruik van de TPM-simulator zijn:
1. [Bereid de ontwikkelomgeving voor](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) en kloon de GitHub-repository:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Navigeer naar de map ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```TPM-simulator onder .
3. Voer Simulator.exe uit voordat u een clienttoepassing voor inrichtingsapparaat uitvoert.
4. Laat de simulator tijdens het inrichtingsproces op de achtergrond draaien om registratie-ID en goedkeuringssleutel te verkrijgen.  Beide waarden zijn slechts geldig voor één exemplaar van de run.

## <a name="x509-certificate-generator"></a>X.509 certificaatgenerator
[X.509-certificaten](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) kunnen worden gebruikt als een attestmechanisme om de productie te schalen en de inrichting van apparaten te vereenvoudigen.  Er zijn [verschillende manieren](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) om een X.509-certificaat te verkrijgen:
* Voor de productieomgeving raden we u aan een X.509 CA-certificaat aan te schaffen bij een openbare certificeringsinstantie.
* Voor de testomgeving u een X.509-basiscertificaat of X.509-certificaatketen genereren met behulp van:
    * OpenSSL: U scripts gebruiken voor het genereren van certificaten:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell of Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Device Identity Composition Engine (DICE) Emulator: DICE kan worden gebruikt voor cryptografische apparaatidentiteit en attestation op basis van TLS-protocol en X.509-clientcertificaten.  [Meer informatie](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) over de identiteit van apparaten met DICE.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>X.509 certificaatgenerator gebruiken met DICE emulator
De SDK's bieden een X.509 certificaat generator met DICE emulator, gelegen in de [Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Deze generator werkt cross-platform.  Het gegenereerde certificaat kan worden gebruikt voor ontwikkeling in andere talen.

Momenteel, terwijl de DICE Emulator een root certificaat, een tussentijds certificaat, een blad certificaat, en bijbehorende prive-sleutel.  Het basiscertificaat of het tussencertificaat kan echter niet worden gebruikt om een afzonderlijk bladcertificaat te ondertekenen.  Als u van plan bent het scenario voor groepsinschrijving te testen waarbij één ondertekeningscertificaat wordt gebruikt om de bladcertificaten van meerdere apparaten te ondertekenen, u OpenSSL gebruiken om een reeks certificaten te produceren.

Ga als volgt te werk om x.509-certificaat te genereren met behulp van deze generator:
1. [Bereid de ontwikkelomgeving voor](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) en kloon de GitHub-repository:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Wijzig de root in azure-iot-sdk-java.
3. Voer ```mvn install -DskipTests=true``` uit om alle vereiste pakketten te downloaden en de SDK samen te stellen
4. Navigeer naar de hoofdmap voor X.509-certificaatgenerator in ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```.
5. Bouwen met```mvn clean install```
6. Voer het hulpprogramma uit met behulp van de volgende opdrachten:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Wanneer u hierom wordt gevraagd, kunt u ook een _algemene naam_ invoeren voor de certificaten.
8. De tool genereert lokaal een **Client Cert,** de **Client Cert Private Key**, Intermediate **Cert**en het **Root Cert**.

**Client Cert** is het bladcertificaat op het apparaat.  **Client Cert** en de bijbehorende **Client Cert Private Key** zijn nodig in de apparaatclient. Afhankelijk van welke taal u kiest, kan het mechanisme om dit in de clienttoepassing te plaatsen, anders zijn.  Zie voor meer informatie de [Quickstarts](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) op het maken van gesimuleerd apparaat met X.509 voor meer informatie.

Het hoofdcertificaat of tussenproduct kan worden gebruikt om een inschrijvingsgroep of individuele inschrijving [programmatisch](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) of met behulp van de [portal](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments)te maken.

## <a name="next-steps"></a>Volgende stappen
* Ontwikkelen met behulp van de [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) voor Azure IoT Hub en Azure IoT Hub Device Provisioning Service
