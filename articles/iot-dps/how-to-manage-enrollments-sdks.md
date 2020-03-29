---
title: Apparaatinschrijvingen beheren met Azure DPS SDK's
description: Apparaatinschrijvingen beheren in de DpS (IoT Hub Device Provisioning Service) met behulp van de Service SDKs
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5cb0e25ec70956e66f7b867f0d0b9473160fc3ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975071"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Apparaatinschrijvingen beheren met Azure Device Provisioning Service SDKs
Met *een apparaatinschrijving* wordt een record van één apparaat of een groep apparaten die zich op een bepaald moment kunnen registreren bij de Service Apparaatinrichting. De inschrijvingsrecord bevat de initiële gewenste configuratie voor het apparaat(en) als onderdeel van die inschrijving, inclusief de gewenste IoT-hub. In dit artikel ziet u hoe u apparaatinschrijvingen voor uw inrichtingsservice programmatisch beheert met behulp van de SDK's van Azure IoT Provisioning Service.  De SDK's zijn beschikbaar op GitHub in dezelfde repository als Azure IoT SDKs.

## <a name="prerequisites"></a>Vereisten
* Verkrijg de verbindingstekenreeks van de instantie Device Provisioning Service.
* Verkrijg de apparaatbeveiligingsartefacten voor het gebruikte [attestmechanisme:](concepts-security.md#attestation-mechanism)
    * [**Trusted Platform Module (TPM)**](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Individuele inschrijving: registratie-id en TPM-goedkeuringssleutel vanaf een fysiek apparaat of van TPM Simulator.
        * De inschrijvingsgroep is niet van toepassing op tpm-attest.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * Individuele inschrijving: Het [Leaf-certificaat](/azure/iot-dps/concepts-security) van een fysiek apparaat of van de SDK [DICE](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) Emulator.
        * Inschrijvingsgroep: het [CA/rootcertificaat](/azure/iot-dps/concepts-security#root-certificate) of het [tussencertificaat](/azure/iot-dps/concepts-security#intermediate-certificate), dat wordt gebruikt om apparaatcertificaat op een fysiek apparaat te produceren.  Het kan ook worden gegenereerd uit de SDK DICE emulator.
* Exacte API-aanroepen kunnen verschillen in taal. Bekijk de voorbeelden op GitHub voor meer informatie:
   * [Java Provisioning Service Client-voorbeelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Node.js Provisioning Service Client samples](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [.NET Provisioning Service Client samples .NET Provisioning Service Client samples .NET Provisioning Service Client samples .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Een apparaatinschrijving maken
Er zijn twee manieren waarop u uw apparaten inschrijven bij de inrichtingsservice:

* Een **inschrijvingsgroep** is een vermelding voor een groep apparaten die een gemeenschappelijk attestmechanisme van X.509-certificaten delen, ondertekend door het [basiscertificaat](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) of het [tussencertificaat](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). We raden u aan een inschrijvingsgroep te gebruiken voor een groot aantal apparaten die een gewenste initiële configuratie delen, of voor apparaten die allemaal naar dezelfde tenant gaan. Houd er rekening mee dat u alleen apparaten inschrijven die het x.509-attestmechanisme gebruiken als *inschrijvingsgroepen*. 

    U een inschrijvingsgroep maken met de SDK's volgens deze werkstroom:

    1. Voor de inschrijvingsgroep gebruikt het attestmechanisme het R.509-basiscertificaat.  Call Service ```X509Attestation.createFromRootCertificate``` SDK API met rootcertificaat om attestation voor inschrijving te maken.  X.509 root certificaat wordt geleverd in een PEM-bestand of als een string.
    1. Maak een ```EnrollmentGroup``` nieuwe ```attestation``` variabele met ```enrollmentGroupId```behulp van de gemaakte en een unieke .  Optioneel u parameters ```Device ID```instellen ```IoTHubHostName``` ```ProvisioningStatus```zoals,,.
    2. Call Service ```createOrUpdateEnrollmentGroup``` SDK API in ```EnrollmentGroup``` uw backend-toepassing om een inschrijvingsgroep te maken.

* Een **individuele inschrijving** is een vermelding voor één apparaat dat zich kan registreren. Individuele inschrijvingen kunnen X.509-certificaten of SAS-tokens (van een fysieke of virtuele TPM) gebruiken als attestmechanismen. We raden u aan afzonderlijke inschrijvingen te gebruiken voor apparaten waarvoor unieke initiële configuraties nodig zijn, of voor apparaten die alleen SAS-tokens kunnen gebruiken via TPM of virtuele TPM als het attestmechanisme. Afzonderlijke inschrijvingen hebben mogelijk de gewenste apparaat-id voor IoT Hub die is opgegeven.

    U een individuele inschrijving maken bij de SDK's volgens deze werkstroom:
    
    1. Kies ```attestation``` uw mechanisme, dat tpm of X.509 kan zijn.
        1. **TPM:** Met behulp van de goedkeuringssleutel van een fysiek apparaat of ```TpmAttestation``` van TPM Simulator als invoer, u Service SDK API aanroepen om attestation voor inschrijving te maken. 
        2. **X.509**: Met behulp van het clientcertificaat als ```X509Attestation.createFromClientCertificate``` invoer u Service SDK API aanroepen om attestation voor inschrijving te maken.
    2. Maak een ```IndividualEnrollment``` nieuwe variabele ```attestation``` met behulp ```registrationId``` van de gemaakte en een unieke als input, die op uw apparaat of gegenereerd uit de TPM Simulator.  Optioneel u parameters ```Device ID```instellen ```IoTHubHostName``` ```ProvisioningStatus```zoals,,.
    3. Call Service ```createOrUpdateIndividualEnrollment``` SDK API in ```IndividualEnrollment``` uw backend-toepassing om een individuele inschrijving te maken.

Nadat u een inschrijving hebt gemaakt, retourneert de Service Apparaatinrichting een inschrijvingsresultaat. Deze werkstroom wordt gedemonstreerd in de [eerder genoemde](#prerequisites)voorbeelden.

## <a name="update-an-enrollment-entry"></a>Een inschrijvingsitem bijwerken

Nadat u een inschrijvingsitem hebt gemaakt, u de inschrijving bijwerken.  Mogelijke scenario's zijn het bijwerken van de gewenste eigenschap, het bijwerken van de attestmethode of het intrekken van apparaattoegang.  Er zijn verschillende API's voor individuele inschrijving en groepsinschrijving, maar geen onderscheid voor attestmechanisme.

U een inschrijvingsitem bijwerken volgens deze werkstroom:
* **Afzonderlijke inschrijving**:
    1. Ontvang eerst de nieuwste inschrijving van de ```getIndividualEnrollment```inprovisioningservice met Service SDK API.
    2. Wijzig indien nodig de parameter van de laatste inschrijving. 
    3. Met de nieuwste inschrijving belt ```createOrUpdateIndividualEnrollment``` u Service SDK API om uw inschrijvingsvermelding bij te werken.
* **Groepsinschrijving**:
    1. Ontvang eerst de nieuwste inschrijving van de ```getEnrollmentGroup```inprovisioningservice met Service SDK API.
    2. Wijzig indien nodig de parameter van de laatste inschrijving.
    3. Met de nieuwste inschrijving belt ```createOrUpdateEnrollmentGroup``` u Service SDK API om uw inschrijvingsvermelding bij te werken.

Deze werkstroom wordt gedemonstreerd in de [eerder genoemde](#prerequisites)voorbeelden.

## <a name="remove-an-enrollment-entry"></a>Een inschrijvingsitem verwijderen

* **Individuele inschrijving** kan worden verwijderd door ```deleteIndividualEnrollment``` ```registrationId```service SDK API aan te roepen met behulp van .
* **Groepsinschrijving** kan worden verwijderd door ```deleteEnrollmentGroup``` service ```enrollmentGroupId```SDK API aan te roepen met behulp van .

Deze werkstroom wordt gedemonstreerd in de [eerder genoemde](#prerequisites)voorbeelden.

## <a name="bulk-operation-on-individual-enrollments"></a>Bulkbewerking bij individuele inschrijvingen

U bulkbewerking uitvoeren om meerdere afzonderlijke inschrijvingen te maken, bij te werken of te verwijderen volgens deze werkstroom:

1. Maak een variabele ```IndividualEnrollment```die meerdere bevat.  De implementatie van deze variabele is voor elke taal anders.  Bekijk het voorbeeld van de bulkbewerking op GitHub voor meer informatie.
2. Call Service ```runBulkOperation``` SDK ```BulkOperationMode``` API met een voor gewenste bewerking en uw variabele voor individuele inschrijvingen. Vier modi worden ondersteund: maken, bijwerken, bijwerkenIfMatchEtag en verwijderen.

Nadat u een bewerking hebt uitgevoerd, retourneert de Service Apparaatvoorziening een bulkbewerkingsresultaat.

Deze werkstroom wordt gedemonstreerd in de [eerder genoemde](#prerequisites)voorbeelden.
