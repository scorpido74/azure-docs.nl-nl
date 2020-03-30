---
title: Azure IoT Hub Device Provisioning Service - TPM Attestation
description: Dit artikel biedt een conceptueel overzicht van de TPM-atteststroom met behulp van IoT Device Provisioning Service (DPS).
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 624171ffc10a06ac3089b6dceb1683c63c88dbda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975275"
---
# <a name="tpm-attestation"></a>TPM-attestation

IoT Hub Device Provisioning Service is een helperservice voor IoT Hub die u gebruikt om zero-touch apparaatinrichting te configureren op een bepaalde IoT-hub. Met de Service Apparaatinrichting u miljoenen apparaten op een veilige manier inrichten.

In dit artikel wordt het identiteitsattestationproces beschreven bij het gebruik van een [TPM.](./concepts-device.md) TPM staat voor Trusted Platform Module en is een soort hardware security module (HSM). In dit artikel wordt ervan uitgegaan dat u een discrete firmware of geïntegreerde TPM gebruikt. Software geëmuleerde TPM's zijn zeer geschikt voor prototypen of testen, maar ze bieden niet hetzelfde beveiligingsniveau als discrete, firmware of geïntegreerde TPM's doen. We raden het gebruik van software-TPM's in productie af. Zie [Een korte inleiding tot TPM voor](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf)meer informatie over soorten TPM.For more information about types of TPMMs, see A Brief introduction to TPM .

Dit artikel is alleen relevant voor apparaten die TPM 2.0 gebruiken met HMAC-sleutelondersteuning en hun goedkeuringssleutels. Het is niet voor apparaten die X.509-certificaten gebruiken voor verificatie. TPM is een industriebrede ISO-standaard van de Trusted Computing Group en u meer lezen over TPM op de [volledige TPM 2.0 spec](https://trustedcomputinggroup.org/tpm-library-specification/) of de [ISO/IEC 11889 spec.](https://www.iso.org/standard/66510.html) In dit artikel wordt ook ervan uitgegaan dat u bekend bent met openbare en private sleutelparen en hoe deze worden gebruikt voor versleuteling.

De SDK's van het Apparaat provisioning Service verwerken alles wat in dit artikel voor u wordt beschreven. U hoeft niets extra's te implementeren als u de SDK's op uw apparaten gebruikt. Dit artikel helpt u conceptueel te begrijpen wat er aan de hand is met uw TPM-beveiligingschip wanneer uw apparaat voorzieningen en waarom het zo veilig is.

## <a name="overview"></a>Overzicht

TPM's gebruiken iets genaamd de goedkeuring skey (EK) als de veilige wortel van vertrouwen. De EK is uniek voor de TPM en het veranderen van het apparaat in wezen verandert het apparaat in een nieuwe.

Er is een ander type sleutel dat TPM's hebben, genaamd de storage root key (SRK). Een SRK kan worden gegenereerd door de eigenaar van de TPM nadat het eigenaar is van de TPM. Het nemen van eigendom van de TPM is de TPM-specifieke manier om te zeggen "iemand stelt een wachtwoord op de HSM." Als een TPM-apparaat wordt verkocht aan een nieuwe eigenaar, kan de nieuwe eigenaar eigenaar worden van de TPM om een nieuwe SRK te genereren. De nieuwe SRK-generatie zorgt ervoor dat de vorige eigenaar de TPM niet kan gebruiken. Omdat de SRK uniek is voor de eigenaar van de TPM, kan de SRK worden gebruikt om gegevens te verzegelen in de TPM zelf voor die eigenaar. De SRK biedt een sandbox voor de eigenaar om hun sleutels op te slaan en biedt toegang herroepbaarheid als het apparaat of TPM wordt verkocht. Het is als het verplaatsen naar een nieuw huis: eigenaarschap is het veranderen van de sloten op de deuren en het vernietigen van alle meubels achtergelaten door de vorige eigenaren (SRK), maar je niet veranderen het adres van het huis (EK).

Zodra een apparaat is ingesteld en klaar voor gebruik, zal het zowel een EK en een SRK beschikbaar voor gebruik.

![Eigenaar worden van een TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

Een opmerking over het nemen van eigendom van de TPM: Het nemen van eigendom van een TPM is afhankelijk van veel dingen, waaronder TPM fabrikant, de set van TPM tools worden gebruikt, en het apparaat OS. Volg de instructies die relevant zijn voor uw systeem om eigenaarschap te nemen.

De Dienst Apparaatinrichting gebruikt het openbare gedeelte van de EK (EK_pub) om apparaten te identificeren en in te schrijven. De leverancier van het apparaat kan de EK_pub lezen tijdens de productie of de laatste test en de EK_pub uploaden naar de inrichtingsservice, zodat het apparaat wordt herkend wanneer het verbinding maakt met de voorziening. De Service Apparaatvoorziening controleert de SRK of -eigenaar niet, dus "clearing" de TPM wist klantgegevens, maar de EK (en andere leveranciersgegevens) worden bewaard en het apparaat wordt nog steeds herkend door de Dienst Apparaatinrichting wanneer deze verbinding maakt met de voorziening.

## <a name="detailed-attestation-process"></a>Gedetailleerd attestproces

Wanneer een apparaat met een TPM voor het eerst verbinding maakt met de Apparaatinrichtingsservice, controleert de service eerst de opgegeven EK_pub op de EK_pub die zijn opgeslagen in de inschrijvingslijst. Als de EK_pubs niet overeenkomen, mag het apparaat niet worden ingericht. Als de EK_pubs overeenkomen, vereist de service dat het apparaat het eigendom van het privégedeelte van de EK bewijst via een nonce-uitdaging, wat een veilige uitdaging is die wordt gebruikt om identiteit te bewijzen. De Device Provisioning Service genereert een nonce en versleutelt deze vervolgens met de SRK en vervolgens met de EK_pub, die beide door het apparaat worden geleverd tijdens het eerste registratiegesprek. De TPM houdt altijd het privégedeelte van de EK veilig. Dit voorkomt namaak en zorgt ervoor dat SAS-tokens veilig zijn ingericht op geautoriseerde apparaten.

Laten we het attestproces in detail doorlopen.

### <a name="device-requests-an-iot-hub-assignment"></a>Apparaat vraagt een Toewijzing van een IoT-hub aan

Eerst maakt het apparaat verbinding met de Apparaatinrichtingsservice en aanvragen tot inlevering. Daarbij biedt het apparaat de service met zijn registratie-ID, een ID-scope en de EK_pub en SRK_pub van de TPM. De service geeft de versleutelde nonce terug aan het apparaat en vraagt het apparaat om de nonce te decoderen en dat te gebruiken om een SAS-token te ondertekenen om opnieuw verbinding te maken en de inrichting af te ronden.

![Apparaataanvragen inrichten](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Nonce uitdaging

Het apparaat neemt de nonce en gebruikt de prive-delen van de EK en SRK om de nonce te decoderen in de TPM; de volgorde van nonce encryptie afgevaardigden vertrouwen van de EK, dat onveranderlijk is, aan de SRK, die kan veranderen als een nieuwe eigenaar neemt eigendom van de TPM.

![Het decoderen van de nonce](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>De nonce valideren en referenties ontvangen

Het apparaat kan vervolgens een SAS-token ondertekenen met behulp van de gedecodeerde nonce en een verbinding met de Apparaatprovisioning-service herstellen met behulp van het ondertekende SAS-token. Met de Nonce uitdaging voltooid, de dienst kan het apparaat in te richten.

![Apparaat herstelt verbinding met apparaatinrichtingsservice om het eigendom van EK te valideren](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Volgende stappen

Nu maakt het apparaat verbinding met IoT Hub en u bent veilig in de wetenschap dat de sleutels van uw apparaten veilig worden opgeslagen. Nu u weet hoe de apparaatinrichtingsservice de identiteit van een apparaat veilig verifieert met Behulp van TPM, raadpleegt u de volgende artikelen voor meer informatie:

* [Meer informatie over alle concepten in auto-provisioning](./concepts-auto-provisioning.md)
* [Ga aan de slag met het gebruik van automatische provisioning](./quick-setup-auto-provision.md) met behulp van de SDKs om de stroom te verzorgen.
