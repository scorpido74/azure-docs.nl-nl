---
title: Azure IoT Hub Device Provisioning Service - Beveiligingsconcepten
description: Beschrijft beveiligingsconcepten die specifiek zijn voor apparaten met DPS (Device Provisioning Service) en IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3191e9886604af9b2a26b71a89cee699197585c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271562"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Beveiligingsconcepten van iot-hubapparaatinrichtingsservice 

IoT Hub Device Provisioning Service is een helperservice voor IoT Hub die u gebruikt om zero-touch apparaatinrichting te configureren op een bepaalde IoT-hub. Met de Service Voor het inrichten van apparaten u miljoenen apparaten [automatisch inrichten](concepts-auto-provisioning.md) op een veilige en schaalbare manier. Dit artikel geeft een overzicht van de *beveiligingsconcepten* die betrokken zijn bij het inrichten van apparaten. Dit artikel is relevant voor alle persona's die betrokken zijn bij het klaarmaken van een apparaat voor implementatie.

## <a name="attestation-mechanism"></a>Attestmechanisme

Het attestmechanisme is de methode die wordt gebruikt om de identiteit van een apparaat te bevestigen. Het attestmechanisme is ook relevant voor de inschrijvingslijst, die de inrichtingsdienst vertelt welke methode van attest moet gebruiken met een bepaald apparaat.

> [!NOTE]
> IoT Hub maakt gebruik van "authenticatie schema" voor een soortgelijk concept in die dienst.

Device Provisioning Service ondersteunt de volgende vormen van attest:
* **X.509-certificaten** op basis van de standaard x.509-certificaatverificatiestroom.
* **Trusted Platform Module (TPM)** op basis van een nonce-uitdaging, met behulp van de TPM-standaard voor sleutels om een ondertekend SAS-token (Shared Access Signature) te presenteren. Deze vorm van attest vereist geen fysieke TPM op het apparaat, maar de service verwacht te getuigen met behulp van de goedkeuringssleutel per de [TPM-spec](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Symmetrische sleutel** op basis van SAS-beveiligingstokens (Shared Access Signature), waaronder een gehashte handtekening en een ingesloten vervaldatum. [Security tokens](../iot-hub/iot-hub-devguide-security.md#security-tokens) Zie [Symmetrische sleutelattest](concepts-symmetric-key-attestation.md)voor meer informatie .


## <a name="hardware-security-module"></a>Hardwarebeveiligingsmodule

De hardware beveiligingsmodule, of HSM, wordt gebruikt voor veilige, hardware-gebaseerde opslag van apparaat geheimen, en is de meest veilige vorm van geheime opslag. Zowel X.509 certificaten als SAS tokens kunnen worden opgeslagen in de HSM. HSM's kunnen worden gebruikt met beide attestmechanismen die de provisioning ondersteunt.

> [!TIP]
> We raden ten zeerste aan om een HSM met apparaten te gebruiken om geheimen veilig op uw apparaten op te slaan.

Apparaatgeheimen kunnen ook worden opgeslagen in software (geheugen), maar het is een minder veilige vorm van opslag dan een HSM.

## <a name="trusted-platform-module"></a>Trusted Platform Module

TPM kan verwijzen naar een standaard voor het veilig opslaan van sleutels die worden gebruikt om het platform te verifiëren, of het kan verwijzen naar de I /O-interface die wordt gebruikt om te communiceren met de modules die de standaard implementeren. TPM's kunnen bestaan als discrete hardware, geïntegreerde hardware, firmware-gebaseerde of software-based. Meer informatie over [TPM's en TPM-attest.](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation) Device Provisioning Service ondersteunt alleen TPM 2.0.

TPM-attestation is gebaseerd op een nonce-uitdaging, die de goedkeurings- en opslagbasissleutels gebruikt om een ondertekend SAS-token (Shared Access Signature) te presenteren.

### <a name="endorsement-key"></a>Goedkeuringssleutel

De goedkeuringssleutel is een asymmetrische sleutel in de TPM, die intern werd gegenereerd of geïnjecteerd tijdens de productie en uniek is voor elke TPM. De goedkeuringssleutel kan niet worden gewijzigd of verwijderd. Het privégedeelte van de goedkeuringssleutel wordt nooit buiten de TPM vrijgegeven, terwijl het openbare gedeelte van de goedkeuringssleutel wordt gebruikt om een echte TPM te erkennen. Meer informatie over de [goedkeuringssleutel](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Hoofdtoets opslag

De opslaghoofdsleutel wordt opgeslagen in de TPM en wordt gebruikt om TPM-sleutels die door toepassingen zijn gemaakt te beschermen, zodat deze sleutels niet kunnen worden gebruikt zonder de TPM. De opslaghoofdsleutel wordt gegenereerd wanneer u eigenaar wordt van de TPM; wanneer u de TPM wist zodat een nieuwe gebruiker eigenaar kan worden, wordt een nieuwe opslaghoofdsleutel gegenereerd. Meer informatie over de [hoofdtoets voor opslag](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>X.509-certificaten

Het gebruik van X.509-certificaten als attestmechanisme is een uitstekende manier om de productie te schalen en de inrichting van apparaten te vereenvoudigen. X.509-certificaten worden doorgaans gerangschikt in een vertrouwensketen waarin elk certificaat in de keten wordt ondertekend door de privésleutel van het volgende hogere certificaat, enzovoort, eindigend in een zelfondertekend rootcertificaat. Met deze regeling wordt een gedelegeerde vertrouwensketen gemaakt van het basiscertificaat dat is gegenereerd door een vertrouwde basiscertificaatautoriteit (CA) die via elke tussenliggende CA naar het "leaf"-certificaat van de eindentiteit wordt gebracht dat op een apparaat is geïnstalleerd. Zie [Apparaatverificatie met X.509 CA-certificaten](/azure/iot-hub/iot-hub-x509ca-overview)voor meer informatie. 

Vaak vertegenwoordigt de certificaatketen een logische of fysieke hiërarchie die is gekoppeld aan apparaten. Een fabrikant kan bijvoorbeeld:
- een zelfondertekend root-CA-certificaat uitgeven
- gebruik het rootcertificaat om voor elke fabriek een uniek tussenliggend CA-certificaat te genereren
- het certificaat van elke fabriek gebruiken om een uniek tussenliggend CA-certificaat te genereren voor elke productielijn in de fabriek
- en ten slotte het productielijncertificaat gebruiken om een uniek apparaat (end-entity) certificaat te genereren voor elk apparaat dat op de lijn wordt vervaardigd. 

Zie [Conceptueel begrip van X.509 CA-certificaten in de IoT-industrie](/azure/iot-hub/iot-hub-x509ca-concept)voor meer informatie. 

### <a name="root-certificate"></a>Basiscertificaat

Een rootcertificaat is een zelfondertekend X.509-certificaat dat een certificaatautoriteit (CA) vertegenwoordigt. Het is het eindpunt, of vertrouwen anker, van de certificaatketen. Rootcertificaten kunnen zelf worden uitgegeven door een organisatie of worden gekocht bij een rootcertificaatautoriteit. Zie [X.509 CA-certificaten ophalen](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates)voor meer informatie. Het basiscertificaat kan ook worden aangeduid als een root CA-certificaat.

### <a name="intermediate-certificate"></a>Tussencertificaat

Een tussencertificaat is een X.509-certificaat, dat is ondertekend door het basiscertificaat (of door een ander tussencertificaat met het basiscertificaat in de keten). Het laatste tussencertificaat in een ketting wordt gebruikt om het bladcertificaat te ondertekenen. Een tussencertificaat kan ook worden aangeduid als een tussentijds CA-certificaat.

### <a name="end-entity-leaf-certificate"></a>Certificaat "blad" van de eindentiteit

Het bladcertificaat of het certificaat van de eindentiteit identificeert de certificaathouder. Het heeft het rootcertificaat in zijn certificaatketen en nul of meer tussenliggende certificaten. Het bladcertificaat wordt niet gebruikt om andere certificaten te ondertekenen. Het identificeert het apparaat op unieke wijze aan de inrichtingsdienst en wordt soms het apparaatcertificaat genoemd. Tijdens de verificatie gebruikt het apparaat de privésleutel die aan dit certificaat is gekoppeld om te reageren op een bewijs van bezitsuitdaging van de service.

Bladcertificaten die worden gebruikt bij een [afzonderlijke inschrijvingsvermelding,](./concepts-service.md#individual-enrollment) hebben de vereiste dat de **onderwerpnaam** moet worden ingesteld op de registratie-id van de individuele inschrijvingsvermelding. Bladcertificaten die worden gebruikt bij een [inschrijvingsgroep](./concepts-service.md#enrollment-group) moeten de **onderwerpnaam** hebben ingesteld op de gewenste apparaat-id die wordt weergegeven in de **registratierecords** voor het geverifieerde apparaat in de inschrijvingsgroep.

Zie [Apparaten verifiëren die zijn ondertekend met X.509 CA-certificaten](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates)voor meer informatie.

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Apparaattoegang tot de inrichtingsservice beheren met X.509-certificaten

De inrichtingsservice onthult twee typen inschrijvingsinvoer die u gebruiken om de toegang te beheren voor apparaten die het x.509-attestmechanisme gebruiken:  

- [Afzonderlijke inschrijvingsitems](./concepts-service.md#individual-enrollment) zijn geconfigureerd met het apparaatcertificaat dat is gekoppeld aan een specifiek apparaat. Deze vermeldingen beheren inschrijvingen voor specifieke apparaten.
- [Inschrijvingsgroepvermeldingen](./concepts-service.md#enrollment-group) zijn gekoppeld aan een specifiek tussenliggend of basis CA-certificaat. Deze vermeldingen beheren inschrijvingen voor alle apparaten die dat tussenliggende of hoofdcertificaat in hun certificaatketen hebben. 

Wanneer een apparaat verbinding maakt met de inrichtingsservice, geeft de service prioriteit aan meer specifieke inschrijvingsvermeldingen boven minder specifieke inschrijvingsvermeldingen. Dat wil zeggen, als er een individuele inschrijving voor het apparaat bestaat, past de inrichtingsservice die vermelding toe. Als er geen individuele inschrijving voor het apparaat is en er een inschrijvingsgroep voor het eerste tussentijdse certificaat in de certificaatketen van het apparaat bestaat, past de service die vermelding toe, enzovoort, op de keten naar de root. De dienst past de eerste toepasselijke vermelding toe die zij constateert, zodat:

- Als de eerste gevonden inschrijvingis ingeschakeld, worden de servicevoorzieningen van het apparaat ingeschakeld.
- Als de eerste gevonden inschrijving is uitgeschakeld, biedt de service geen inrichting van het apparaat.  
- Als er geen inschrijvingsvermelding wordt gevonden voor een van de certificaten in de certificaatketen van het apparaat, biedt de service het apparaat niet. 

Dit mechanisme en de hiërarchische structuur van certificaatketens bieden krachtige flexibiliteit in hoe u de toegang voor afzonderlijke apparaten en voor groepen apparaten beheren. Stel je bijvoorbeeld vijf apparaten voor met de volgende certificaatketens: 

- *Apparaat 1*: basiscertificaat -> certificaat A -> apparaat 1 certificaat
- *Apparaat 2*: basiscertificaat -> certificaat A -> apparaat 2 certificaat
- *Apparaat 3*: basiscertificaat -> certificaat A -> apparaat 3 certificaat
- *Apparaat 4*: basiscertificaat -> certificaat B -> apparaat 4 certificaat
- *Apparaat 5*: basiscertificaat -> certificaat B -> apparaat 5 certificaat

In eerste instantie u één ingeschakelde groepsinschrijvingsvermelding voor het hoofdcertificaat maken om toegang voor alle vijf apparaten in te schakelen. Als certificaat B later wordt gecompromitteerd, u een uitgeschakelde inschrijvingsgroep vermelding voor certificaat B maken om te voorkomen dat *Apparaat 4* en *Apparaat 5* zich inschrijven. Als apparaat *3* nog later wordt gecompromitteerd, u een uitgeschakelde individuele inschrijvingsvermelding voor het certificaat maken. Hierdoor wordt de toegang voor *apparaat 3*ingetrokken, maar kunnen *apparaat 1* en apparaat *2* zich nog steeds inschrijven.