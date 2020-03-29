---
title: X.509 CA-certificaten verifiëren met Azure IoT Hub Device Provisioning Service
description: Bewijs van bezit voor X.509 CA-certificaten doen met Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: b008c4ebc83200043d51fc8ef367f1983c549949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973438"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Bewijs van bezit voor X.509 CA-certificaten doen met uw Service voor het inrichten van apparaten

Een geverifieerd X.509 Certificate Authority (CA)-certificaat is een CA-certificaat dat is geüpload en geregistreerd bij uw inprovisioningsservice en door middel van bewijs van bezit met de service is gegaan. 

Proof-of-possession omvat de volgende stappen:
1. Ontvang een unieke verificatiecode die wordt gegenereerd door de inrichtingsservice voor uw X.509 CA-certificaat. U kunt dit doen vanuit Azure Portal.
2. Maak een X.509-verificatiecertificaat met de verificatiecode als onderwerp en onderteken het certificaat met de privésleutel die is gekoppeld aan uw X.509 CA-certificaat.
3. Upload het ondertekende verificatiecertificaat naar de service. De service valideert het verificatiecertificaat met behulp van het openbare gedeelte van het TE verifiëren CA-certificaat, waardoor wordt aangetoond dat u in het bezit bent van de privésleutel van het CA-certificaat.

Geverifieerde certificaten spelen een belangrijke rol bij het gebruik van inschrijvingsgroepen. Het verifiëren van het eigendom van certificaten biedt een extra beveiligingslaag door ervoor te zorgen dat de uploader van het certificaat in het bezit is van de privésleutel van het certificaat. Verificatie voorkomt dat een kwaadwillende actor die uw verkeer snuift, een tussencertificaat kan extraheren en dat certificaat gebruikt om een inschrijvingsgroep in hun eigen inrichtingsservice te maken, waardoor uw apparaten effectief worden gekaapt. Door het eigendom van de root of een tussencertificaat in een certificaatketen te bewijzen, bewijst u dat u toestemming hebt om bladcertificaten te genereren voor de apparaten die zich registreren als onderdeel van die inschrijvingsgroep. Daarom moet het basis- of tussencertificaat dat is geconfigureerd in een inschrijvingsgroep een geverifieerd certificaat zijn of moet het worden uitgevoerd tot een geverifieerd certificaat in de certificaatketen die een apparaat presenteert wanneer het zich verifieert met de service. Zie [X.509-certificaten](concepts-security.md#x509-certificates) en [Apparaattoegang tot de inrichtingsservice beheren met X.509-certificaten](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates)voor meer informatie over inschrijvingsgroepen.

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Registreer het openbare deel van een X.509-certificaat en ontvang een verificatiecode

Als u een CA-certificaat wilt registreren bij uw inrichtingsservice en een verificatiecode wilt krijgen die u gebruiken tijdens het bewijs van bezit, voert u deze stappen uit. 

1. Navigeer in de Azure-portal naar uw inrichtingsservice en open **Certificaten** in het linkermenu. 
2. Klik **op Toevoegen** om een nieuw certificaat toe te voegen.
3. Voer een vriendelijke weergavenaam in voor uw certificaat. Blader naar het .cer- of .pem-bestand dat het openbare deel van uw X.509-certificaat vertegenwoordigt. Klik op **Uploaden**.
4. Zodra u een melding krijgt dat uw certificaat is geüpload, klikt u op **Opslaan**.

    ![Certificaat uploaden](./media/how-to-verify-certificates/add-new-cert.png)  

   Uw certificaat wordt weergegeven in de lijst **Certificate Explorer.** Houd er rekening mee dat de **status** van dit certificaat niet is *geverifieerd*.

5. Klik op het certificaat dat u in de vorige stap hebt toegevoegd.

6. Klik **in certificaatgegevens**op **Verificatiecode genereren**.

7. De inrichtingsservice maakt een **verificatiecode** die u gebruiken om het certificaateigendom te valideren. Kopieer de code naar het klembord. 

   ![Certificaat verifiëren](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>De verificatiecode digitaal ondertekenen om een verificatiecertificaat te maken

U moet nu de *verificatiecode* ondertekenen met de privésleutel die is gekoppeld aan uw X.509 CA-certificaat, waarmee een handtekening wordt gegenereerd. Dit staat bekend als [bewijs van bezit](https://tools.ietf.org/html/rfc5280#section-3.1) en resulteert in een ondertekend verificatiecertificaat.

Microsoft biedt hulpprogramma's en voorbeelden waarmee u een ondertekend verificatiecertificaat maken: 

- De **Azure IoT Hub C SDK** biedt PowerShell -scripts (Windows) en Bash (Linux) om u te helpen CA- en bladcertificaten te maken voor ontwikkeling en om bewijs van bezit uit te voeren met behulp van een verificatiecode. U de [bestanden](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) die relevant zijn voor uw systeem downloaden naar een werkende map en de instructies in de readme voor het beheren van [CA-certificaten](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) volgen om bewijs van bezit uit te voeren op een CA-certificaat. 
- De **Azure IoT Hub C# SDK** bevat het [groepscertificaatverificatievoorbeeld](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample), waarmee u een bewijs van bezit maken.
 
> [!IMPORTANT]
> Naast het uitvoeren van proof-of-possession, de PowerShell en Bash scripts aangehaald eerder vermeld ook u root certificaten, tussenliggende certificaten en blad certificaten die kunnen worden gebruikt om te verifiëren en provisiondevices te maken. Deze certificaten mogen alleen voor ontwikkeling worden gebruikt. Ze mogen nooit worden gebruikt in een productieomgeving. 

De PowerShell- en Bash-scripts in de documentatie en SDK's zijn afhankelijk van [OpenSSL.](https://www.openssl.org/) U ook OpenSSL of andere tools van derden gebruiken om u te helpen bewijs van bezit te maken. Zie Hoe u tools in de SDK's gebruikt voor meer informatie over tooling die bij de [SDK's wordt geleverd.](how-to-use-sdk-tools.md) 


## <a name="upload-the-signed-verification-certificate"></a>Het ondertekende verificatiecertificaat uploaden

1. Upload de resulterende handtekening als verificatiecertificaat naar uw inrichtingsservice in de portal. Gebruik in **Certificaatgegevens** op de Azure-portal het pictogram _Verkenner_ naast het **veld Verificatiecertificaat .pem of .cer** om het ondertekende verificatiecertificaat van uw systeem te uploaden.

2. Zodra het certificaat is geüpload, klikt u op **Verifiëren**. De **status** van uw certificaat wordt gewijzigd in **_Geverifieerd_** in de lijst **Certificate Explorer.** Klik **op Vernieuwen** als deze niet automatisch wordt bijgewerkt.

   ![Verificatie van het uploadcertificaat](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Volgende stappen

- Zie [Apparaatinschrijvingen beheren met Azure-portal](how-to-manage-enrollments.md)voor meer informatie over het gebruik van de portal om een inschrijvingsgroep te maken.
- Zie [Apparaatinschrijvingen beheren met service-SDK's](how-to-manage-enrollments-sdks.md)voor meer informatie over het gebruik van de service-SDK's om een inschrijvingsgroep te maken.










