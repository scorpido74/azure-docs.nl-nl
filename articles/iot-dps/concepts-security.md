---
title: Azure-IoT Hub Device Provisioning Service-beveiligings concepten
description: Beschrijft de concepten voor het inrichten van beveiliging die specifiek zijn voor apparaten met de Device Provisioning Service (DPS) en IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3191e9886604af9b2a26b71a89cee699197585c4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378880"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Beveiligings concepten IoT Hub Device Provisioning Service 

IoT Hub Device Provisioning Service is een Helper-service voor IoT Hub die u gebruikt om het inrichten van Zero-Touch-apparaten te configureren voor een opgegeven IoT-hub. Met de Device Provisioning Service kunt u miljoenen apparaten op een veilige en schaal bare manier [automatisch inrichten](concepts-auto-provisioning.md) . Dit artikel geeft een overzicht van de *beveiligings* concepten die bij het inrichten van apparaten betrokken zijn. Dit artikel is relevant voor alle personen bij het ophalen van een apparaat dat klaar is voor implementatie.

## <a name="attestation-mechanism"></a>Attestation-mechanisme

Het Attestation-mechanisme is de methode die wordt gebruikt om de identiteit van een apparaat te bevestigen. Het Attestation-mechanisme is ook relevant voor de registratie lijst, waarmee wordt aangegeven dat de inrichtings service wordt gebruikt voor het gebruik van een bepaald apparaat.

> [!NOTE]
> IoT Hub gebruikt verificatie schema voor een vergelijkbaar concept in die service.

Device Provisioning Service ondersteunt de volgende vormen van Attestation:
* **X. 509-certificaten** op basis van de standaard x. 509 certificaat verificatie stroom.
* **Trusted Platform Module (TPM)** op basis van een nonce-Challenge, met behulp van de TPM-standaard voor sleutels om een ondertekend Shared Access Signature-token (SAS) aan te bieden. Voor deze vorm van Attestation is geen fysieke TPM vereist op het apparaat, maar de service verwacht te voldoen aan de hand van de goedkeurings sleutel per [TPM-specificatie](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Symmetrische sleutel** op basis van [beveiligings tokens](../iot-hub/iot-hub-devguide-security.md#security-tokens)voor Shared Access Signature (SAS), die een gehashte hand tekening en een Inge sloten verloop bevatten. Zie [symmetrische sleutel Attestation](concepts-symmetric-key-attestation.md)(Engelstalig) voor meer informatie.


## <a name="hardware-security-module"></a>Hardware Security module

De Hardware Security module, of HSM, wordt gebruikt voor veilige, op hardware gebaseerde opslag van geheimen van apparaten en is de veiligste vorm van geheime opslag. Zowel X. 509-certificaten als SAS-tokens kunnen worden opgeslagen in de HSM. Hsm's kan worden gebruikt met beide Attestation-mechanismen die door het inrichten worden ondersteund.

> [!TIP]
> We raden u ten zeerste aan een HSM te gebruiken met apparaten om veilig geheimen op uw apparaten op te slaan.

Er kunnen ook geheimen voor apparaten worden opgeslagen in software (geheugen), maar dit is een minder veilige opslag ruimte dan een HSM.

## <a name="trusted-platform-module"></a>Trusted Platform Module

TPM kan verwijzen naar een standaard voor het veilig opslaan van sleutels die worden gebruikt om het platform te verifiëren, of kan verwijzen naar de I/O-interface die wordt gebruikt om te communiceren met de modules die de standaard implementeren. Tpm's kan bestaan als afzonderlijke hardware, geïntegreerde hardware, op firmware gebaseerd of op software gebaseerd. Meer informatie over [tpm's en TPM-Attestation](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Device Provisioning Service ondersteunt alleen TPM 2,0.

TPM-Attestation is gebaseerd op een nonce-Challenge, die gebruikmaakt van de goedkeurings-en opslag basis sleutels om een ondertekend Shared Access Signature-token (SAS) aan te bieden.

### <a name="endorsement-key"></a>Goedkeurings sleutel

De goedkeurings sleutel is een asymmetrische sleutel in de TPM die intern is gegenereerd of geïnjecteerd tijdens de productie tijd en uniek is voor elke TPM. De goedkeurings sleutel kan niet worden gewijzigd of verwijderd. Het persoonlijke gedeelte van de goedkeurings sleutel wordt nooit buiten de TPM vrijgegeven, terwijl het open bare gedeelte van de goedkeurings sleutel wordt gebruikt om een legitieme TPM te herkennen. Meer informatie over de [goedkeurings sleutel](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Opslag hoofd sleutel

De opslag hoofd sleutel wordt opgeslagen in de TPM en wordt gebruikt voor het beveiligen van TPM-sleutels die zijn gemaakt door toepassingen, zodat deze sleutels niet kunnen worden gebruikt zonder de TPM. De opslag hoofd sleutel wordt gegenereerd wanneer u eigenaar van de TPM bent; Wanneer u de TPM wist zodat een nieuwe gebruiker eigenaar kan worden, wordt een nieuwe opslag hoofd sleutel gegenereerd. Meer informatie over de [opslag hoofd sleutel](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>X. 509-certificaten

Het gebruik van X. 509-certificaten als Attestation-mechanisme is een uitstekende manier om productie te schalen en het inrichten van apparaten te vereenvoudigen. X. 509-certificaten worden meestal gerangschikt in een vertrouwens keten waarbij elk certificaat in de keten wordt ondertekend door de persoonlijke sleutel van het volgende hogere certificaat, enzovoort, wordt beëindigd in een zelfondertekend basis certificaat. Deze regeling brengt een overgedragen vertrouwens keten tot stand van het basis certificaat dat door een vertrouwde basis certificerings instantie (CA) wordt gegenereerd via elke tussenliggende CA naar het eind entiteit blad-certificaat dat op een apparaat is geïnstalleerd. Zie voor meer informatie [apparaat-verificatie met behulp van X. 509 CA-certificaten](/azure/iot-hub/iot-hub-x509ca-overview). 

Vaak vertegenwoordigt de certificaat keten een logische of fysieke hiërarchie die is gekoppeld aan apparaten. Een fabrikant kan bijvoorbeeld:
- een zelfondertekend basis-CA-certificaat uitgeven
- het basis certificaat gebruiken voor het genereren van een uniek tussenliggend CA-certificaat voor elke fabriek
- elk fabrieks certificaat gebruiken voor het genereren van een uniek tussenliggend CA-certificaat voor elke productie lijn in het bedrijf
- en ten slotte het certificaat van de productie lijn gebruiken om een uniek apparaat (eind entiteit) te genereren voor elk apparaat dat op de regel wordt geproduceerd. 

Zie voor meer informatie [conceptuele kennis van X. 509 CA-certificaten in de IOT-industrie](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Basis certificaat

Een basis certificaat is een zelfondertekend X. 509-certificaat dat een certificerings instantie (CA) vertegenwoordigt. Het is het ondersteuningabonnementen of vertrouwens anker van de certificaat keten. Basis certificaten kunnen zelf worden uitgegeven door een organisatie of worden aangeschaft bij een basis certificerings instantie. Zie [Get X. 509 ca certificates](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates)(Engelstalig) voor meer informatie. Het basis certificaat kan ook worden aangeduid als een basis-CA-certificaat.

### <a name="intermediate-certificate"></a>Tussenliggend certificaat

Een tussenliggend certificaat is een X. 509-certificaat dat is ondertekend door het basis certificaat (of door een ander tussenliggend certificaat met het basis certificaat in de keten). Het laatste tussenliggende certificaat in een keten wordt gebruikt voor het ondertekenen van het blad certificaat. Een tussenliggend certificaat kan ook worden aangeduid als een tussenliggend CA-certificaat.

### <a name="end-entity-leaf-certificate"></a>Leaf-certificaat van eind entiteit

Het blad certificaat of het certificaat van de eind entiteit identificeert de certificaat houder. Het bevat het basis certificaat in de certificaat keten en nul of meer tussenliggende certificaten. Het blad certificaat wordt niet gebruikt om andere certificaten te ondertekenen. Het apparaat wordt uniek geïdentificeerd voor de inrichtings service en wordt ook wel het certificaat van het apparaat genoemd. Tijdens de verificatie gebruikt het apparaat de persoonlijke sleutel die is gekoppeld aan dit certificaat om te reageren op de vraag naar een bewijs van de eigendom van de service.

Bij blad certificaten die worden gebruikt met een [afzonderlijke inschrijvings](./concepts-service.md#individual-enrollment) vermelding moet de **onderwerpnaam** worden ingesteld op de registratie-id van de afzonderlijke inschrijvings vermelding. Bij blad certificaten die worden gebruikt voor een [inschrijvings groep](./concepts-service.md#enrollment-group) moet de **onderwerpnaam** worden ingesteld op de gewenste apparaat-id die wordt weer gegeven in de **registratie records** voor het geverifieerde apparaat in de registratie groep.

Zie [apparaten verifiëren die zijn ondertekend met X. 509 CA-certificaten](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates)voor meer informatie.

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Toegang van apparaten tot de inrichtings service beheren met X. 509-certificaten

De inrichtings service laat twee typen inschrijvings vermeldingen zien die u kunt gebruiken voor het beheren van de toegang voor apparaten die gebruikmaken van het verificatie mechanisme X. 509:  

- [Afzonderlijke registratie](./concepts-service.md#individual-enrollment) vermeldingen worden geconfigureerd met het certificaat van het apparaat dat is gekoppeld aan een specifiek apparaat. Met deze vermeldingen worden inschrijvingen voor specifieke apparaten beheerd.
- Vermeldingen in de [registratie groep](./concepts-service.md#enrollment-group) zijn gekoppeld aan een specifiek tussenliggend of basis-CA-certificaat. Deze vermeldingen bepalen registraties voor alle apparaten die dat tussenliggende of basis certificaat in hun certificaat keten hebben. 

Wanneer een apparaat verbinding maakt met de inrichtings service, wordt met de service prioriteit gegeven aan meer specifieke inschrijvings vermeldingen voor minder specifieke inschrijvings vermeldingen. Dat wil zeggen, als er een afzonderlijke inschrijving voor het apparaat bestaat, de inrichtings service die vermelding toepast. Als er geen individuele inschrijving voor het apparaat is en een registratie groep voor het eerste tussenliggende certificaat in de certificaat keten van het apparaat bestaat, wordt die vermelding door de service toegepast, enzovoort, tot aan het hoofd niveau. De service past de eerste toepasselijke vermelding toe die wordt gevonden, bijvoorbeeld:

- Als de eerste inschrijvings vermelding is ingeschakeld, wordt het apparaat door de service ingericht.
- Als de eerste inschrijvings vermelding is uitgeschakeld, wordt het apparaat niet door de service ingericht.  
- Als er geen inschrijvings vermelding is gevonden voor de certificaten in de certificaat keten van het apparaat, wordt het apparaat niet door de service ingericht. 

Dit mechanisme en de hiërarchische structuur van certificaat ketens biedt krachtige flexibiliteit bij het beheren van de toegang voor afzonderlijke apparaten en voor groepen apparaten. Stel bijvoorbeeld vijf apparaten met de volgende certificaat ketens: 

- *Apparaat 1*: basis certificaat-> certificaat A-> apparaat 1 certificaat
- *Apparaat 2*: basis certificaat-> certificaat A-> apparaat 2 certificaat
- *Apparaat 3*: basis certificaat-> certificaat A-> apparaat 3
- *Apparaat 4*: basis certificaat-> certificaat B-> apparaat 4-certificaat
- *Apparaat 5*: basis certificaat-> certificaat B-> apparaat 5 certificaat

In eerste instantie kunt u een eenmalige registratie vermelding voor het basis certificaat maken om toegang voor alle vijf apparaten in te scha kelen. Als certificaat B later wordt aangetast, kunt u een uitgeschakelde registratie groep voor certificaat B maken om te voor komen dat *apparaat 4* en *apparaat 5* worden inge schreven. Als er nog steeds een nieuwer *apparaat 3* wordt aangetast, kunt u een uitgeschakelde individuele inschrijvings vermelding voor het certificaat maken. Hiermee wordt de toegang voor *apparaat 3*ingetrokken, maar kan *apparaat 1* en *apparaat 2* worden inge schreven.