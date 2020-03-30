---
title: Apparaatconcepten in Azure-apparaatinrichting | Microsoft Documenten
description: Beschrijft concepten voor het inrichten van apparaten die specifiek zijn voor apparaten met DPS (Device Provisioning Service) en IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f5f931622f793a1146c04403e8c5e1a5ef7a7d62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285160"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Apparaatconcepten van de IoT Hub Device Provisioning Service

IoT Hub Device Provisioning Service is een helperservice voor IoT Hub die u gebruikt om zero-touch apparaatinrichting te configureren op een bepaalde IoT-hub. Met de Device Provisioning Service kunt u miljoenen apparaten inrichten op een veilige en schaalbare manier.

Dit artikel geeft een overzicht van de *apparaatconcepten* die betrokken zijn bij het inrichten van apparaten. Dit artikel is het meest relevant voor persona's die betrokken zijn bij de [productiestap](about-iot-dps.md#manufacturing-step) van het klaarmaken van een apparaat voor implementatie.

## <a name="attestation-mechanism"></a>Attestmechanisme

Het attestmechanisme is de methode die wordt gebruikt om de identiteit van een apparaat te bevestigen. Het attestmechanisme is ook relevant voor de inschrijvingslijst, die de inrichtingsdienst vertelt welke methode van attest moet gebruiken met een bepaald apparaat.

> [!NOTE]
> IoT Hub maakt gebruik van "authenticatie schema" voor een soortgelijk concept in die dienst.

De Apparaatinrichtingsservice ondersteunt de volgende vormen van attest:
* **X.509-certificaten** op basis van de standaard x.509-certificaatverificatiestroom.
* **Trusted Platform Module (TPM)** op basis van een nonce-uitdaging, met behulp van de TPM-standaard voor sleutels om een ondertekend SAS-token (Shared Access Signature) te presenteren. Dit vereist geen fysieke TPM op het apparaat, maar de service verwacht te getuigen met behulp van de goedkeuringssleutel per de [TPM-spec.](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)
* **Symmetrische sleutel** op basis van SAS-beveiligingstokens (Shared Access Signature), waaronder een gehashte handtekening en een ingesloten vervaldatum. [Security tokens](../iot-hub/iot-hub-devguide-security.md#security-tokens) Zie [Symmetrische sleutelattest](concepts-symmetric-key-attestation.md)voor meer informatie .

## <a name="hardware-security-module"></a>Hardwarebeveiligingsmodule

De hardware beveiligingsmodule, of HSM, wordt gebruikt voor veilige, hardware-gebaseerde opslag van apparaat geheimen, en is de meest veilige vorm van geheime opslag. Zowel X.509 certificaten als SAS tokens kunnen worden opgeslagen in de HSM. HSM's kunnen worden gebruikt met beide attestmechanismen die de inprovisioningservice ondersteunt.

> [!TIP]
> We raden ten zeerste aan om een HSM met apparaten te gebruiken om geheimen veilig op uw apparaten op te slaan.

Apparaatgeheimen kunnen ook worden opgeslagen in software (geheugen), maar het is een minder veilige vorm van opslag dan een HSM.

## <a name="registration-id"></a>Registratie-id

De registratie-ID wordt gebruikt om een apparaat op unieke wijze te identificeren in de Apparaatinrichtingsservice. De apparaat-ID moet uniek zijn in het bereik van de [inrichtingsservice-id.](#id-scope) Elk apparaat moet een registratie-ID hebben. De registratie-ID is alfanumeriek, geval ongevoelig, en kan speciale tekens bevatten, waaronder dubbele punt, punt, underscore en koppelteken.

* In het geval van TPM wordt de registratie-ID geleverd door de TPM zelf.
* In het geval van x.509-gebaseerde attest, wordt de registratie-ID verstrekt als de ondernaam van het certificaat.

## <a name="device-id"></a>Apparaat-id

De apparaat-ID is de ID zoals deze wordt weergegeven in IoT Hub. De gewenste apparaat-ID kan worden ingesteld in de inschrijvingsvermelding, maar deze hoeft niet te worden ingesteld. Het instellen van de gewenste apparaat-ID wordt alleen ondersteund in individuele inschrijvingen. Als er geen gewenste apparaat-ID is opgegeven in de inschrijvingslijst, wordt de registratie-ID gebruikt als apparaat-id bij het registreren van het apparaat. Meer informatie over [apparaat-id's in IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>ID-bereik

Het ID-bereik wordt toegewezen aan een apparaatinrichtingsservice wanneer deze door de gebruiker wordt gemaakt en wordt gebruikt om op unieke wijze de specifieke inrichtingsservice te identificeren die het apparaat registreert. De ID-scope wordt gegenereerd door de service en is onveranderlijk, wat uniciteit garandeert.

> [!NOTE]
> Uniciteit is belangrijk voor langlopende implementatieactiviteiten en fusie- en overnamescenario's.
