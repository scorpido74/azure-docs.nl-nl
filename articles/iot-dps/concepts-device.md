---
title: Concepten van apparaten in azure Device Provisioning | Microsoft Docs
description: Hierin worden de concepten beschreven voor het inrichten van apparaten met Device Provisioning Service en IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: dc4ffc4965d6a3c05693c7566a2c51538584f372
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72897588"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Concepten van IoT Hub Device Provisioning Service-apparaten

IoT Hub Device Provisioning Service is een Helper-service voor IoT Hub die u gebruikt om het inrichten van Zero-Touch-apparaten te configureren voor een opgegeven IoT-hub. Met de Device Provisioning Service kunt u miljoenen apparaten inrichten op een veilige en schaalbare manier.

Dit artikel bevat een overzicht van de *concepten van het apparaat die* bij het inrichten van apparaten betrokken zijn. Dit artikel is het meest relevant voor personen die deel uitmaken van de [stap productie](about-iot-dps.md#manufacturing-step) van het voorbereiden van een apparaat dat klaar is voor implementatie.

## <a name="attestation-mechanism"></a>Attestation-mechanisme

Het Attestation-mechanisme is de methode die wordt gebruikt om de identiteit van een apparaat te bevestigen. Het Attestation-mechanisme is ook relevant voor de registratie lijst, waarmee wordt aangegeven dat de inrichtings service wordt gebruikt voor het gebruik van een bepaald apparaat.

> [!NOTE]
> IoT Hub gebruikt verificatie schema voor een vergelijkbaar concept in die service.

De Device Provisioning Service ondersteunt de volgende vormen van Attestation:
* **X. 509-certificaten** op basis van de standaard x. 509 certificaat verificatie stroom.
* **Trusted Platform Module (TPM)** op basis van een nonce-Challenge, met behulp van de TPM-standaard voor sleutels om een ondertekend Shared Access Signature-token (SAS) aan te bieden. Hiervoor is geen fysieke TPM vereist op het apparaat, maar de service verwacht een verklaring te maken met behulp van de goedkeurings sleutel per [TPM-specificatie](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Symmetrische sleutel** op basis van [beveiligings tokens](../iot-hub/iot-hub-devguide-security.md#security-tokens)voor Shared Access Signature (SAS), die een gehashte hand tekening en een Inge sloten verloop bevatten. Zie [symmetrische sleutel Attestation](concepts-symmetric-key-attestation.md)(Engelstalig) voor meer informatie.

## <a name="hardware-security-module"></a>Hardware Security module

De Hardware Security module, of HSM, wordt gebruikt voor veilige, op hardware gebaseerde opslag van geheimen van apparaten en is de veiligste vorm van geheime opslag. Zowel X. 509-certificaten als SAS-tokens kunnen worden opgeslagen in de HSM. Hsm's kan worden gebruikt met beide Attestation-mechanismen die door de inrichtings service worden ondersteund.

> [!TIP]
> We raden u ten zeerste aan een HSM te gebruiken met apparaten om veilig geheimen op uw apparaten op te slaan.

Er kunnen ook geheimen voor apparaten worden opgeslagen in software (geheugen), maar dit is een minder veilige opslag ruimte dan een HSM.

## <a name="registration-id"></a>Registratie-ID

De registratie-ID wordt gebruikt om een apparaat in de Device Provisioning-Service op unieke wijze te identificeren. De apparaat-ID moet uniek zijn in het bereik van de service [-id](#id-scope)voor de inrichting. Elk apparaat moet een registratie-ID hebben. De registratie-ID is alfanumeriek, niet hoofdletter gevoelig en kan speciale tekens bevatten, waaronder dubbele punt, punt, onderstrepings teken en afbreek streepje.

* In het geval van TPM wordt de registratie-ID door de TPM zelf verschaft.
* In het geval van X. 509 Attestation wordt de registratie-ID gegeven als de onderwerpnaam van het certificaat.

## <a name="device-id"></a>Apparaat-id

De apparaat-ID is de ID zoals deze wordt weer gegeven in IoT Hub. De gewenste apparaat-ID kan in de inschrijvings vermelding worden ingesteld, maar deze hoeft niet te worden ingesteld. Als er geen gewenste apparaat-ID is opgegeven in de registratie lijst, wordt de registratie-ID gebruikt als de apparaat-ID bij het registreren van het apparaat. Meer informatie over [apparaat-id's in IOT hub](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>ID-bereik

Het ID-bereik wordt toegewezen aan een Device Provisioning Service wanneer deze door de gebruiker wordt gemaakt en wordt gebruikt om de specifieke inrichtings service te identificeren die door het apparaat wordt geregistreerd. Het ID-bereik wordt gegenereerd door de service en is onveranderbaar, waardoor uniekheid wordt gegarandeerd.

> [!NOTE]
> Uniekheid is belang rijk voor langlopende implementatie bewerkingen en fusie-en verwervings scenario's.
