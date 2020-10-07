---
title: Azure Attestation-claimsets
description: De claimsets van Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 555dccbd3c2dfe61bac5891514deface6f8a877d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89236863"
---
# <a name="claim-sets"></a>Claimsets

Claims die worden gegenereerd in het proces van het attesten van enclaves met behulp van Microsoft Azure Attestation kunnen worden onderverdeeld in de volgende categorieën:

- **Binnenkomende claims**: Claims die zijn gegenereerd door Microsoft Azure Attestation na het parseren van het Attestation-bewijs.

- **Uitgaande claims**: Claims die zijn gemaakt als uitvoer door Azure Attestation. Het bevat alle claims die moeten eindigen in het Attestation-token.

- **Eigenschapsclaims**: Claims die zijn gemaakt als uitvoer door Azure Attestation. Het bevat alle claims die eigenschappen van het Attestation-token vertegenwoordigen, zoals het coderen van het rapport, de geldigheidsduur van het rapport, enzovoort.

Hieronder worden de claims beschreven die zijn gedefinieerd door de JWT-RFC en gebruikt door Azure Attestation in het antwoordobject:

- **"iss" (verlener)-claim**: De "iss" (verlener)-claim identificeert de principal die de JWT heeft uitgegeven. De verwerking van deze claim is in het algemeen toepassingsspecifiek. De "iss"-waarde is een hoofdlettergevoelige tekenreeks met een StringOrURI-waarde.
- **"iat" (uitgegeven op)-claim**: The "iat" (uitgegeven op)-claim geeft de tijd aan wanneer de JWT was uitgegeven. Deze claim kan worden gebruikt om de leeftijd van de JWT te bepalen. De waarde MOET een getal zijn dat een NumericDate-waarde bevat.
- **"exp" (vervaltijd)-claim**: De "exp" (vervaltijd)-claim identificeert de vervaltijd op of waarna de JWT niet moet worden geaccepteerd voor verwerking. Voor de verwerking van de "exp"-claim MOET de huidige datum/tijd vóór de vervaldatum/-tijd van de "exp"-claim zijn.

  Opmerking: Een speling van 5 minuten wordt toegevoegd aan de probleemtijd (IAT), om rekening te houden met tijdverschil.
- **"nbf" (niet vóór)-claim**: De "nbf" (niet vóór)-claim identificeert de tijd waarna de JWT NIET wordt geaccepteerd voor verwerking. Voor de verwerking van de "nbf"-claim MOET de huidige datum/tijd later of gelijk zijn aan de niet-vóór-datum/tijd die wordt vermeld in de "nbf"-claim.
  Opmerking: Een speling van 5 minuten wordt toegevoegd aan de probleemtijd (IAT), om rekening te houden met tijdverschil.

## <a name="claims-issued-by-azure-attestation-in-sgx-enclaves"></a>Claims die zijn uitgegeven door Azure Attestation in SGX-enclaves

### <a name="incoming-claims"></a>Binnenkomende claims 

- **$is-debuggable**: Een Booleaanse waarde die aangeeft of de enclave is ingeschakeld voor foutopsporing of niet
- **$sgx-mrsigner**: hexadecimale waarde van het veld "mrsigner" van de prijsopgave
- **$sgx-mrenclave**: hexadecimale waarde van het veld "mrenclave" van de prijsopgave
- **$product-id**
- **$svn**: nummer van de beveiligingsversie dat is gecodeerd in de prijsopgave 
- **$tee**: type enclave 

### <a name="outgoing-claims"></a>Uitgaande claims

- **is-debuggable**: Een Booleaanse waarde die aangeeft of de enclave is ingeschakeld voor foutopsporing of niet
- **sgx-mrsigner**: hexadecimale waarde van het veld "mrsigner" van de prijsopgave
- **sgx-mrenclave**: hexadecimale waarde van het veld "mrenclave" van de prijsopgave
- **product-id**
- **svn**: nummer van de beveiligingsversie dat is gecodeerd in de prijsopgave 
- **tee**: type enclave 
- **maa-ehd**:  Base64Url-gecodeerde versie van de "enclave-gegevens" die is opgegeven in de Attestation-aanvraag 
- **aas-ehd**:  Base64Url-gecodeerde versie van de "enclave-gegevens" die is opgegeven in de Attestation-aanvraag 

## <a name="claims-issued-by-azure-attestation-in-vbs-enclaves"></a>Claims die zijn uitgegeven door Azure Attestation in VBS-enclaves

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>Binnenkomende claims (kunnen ook worden gebruikt als uitgaande claims)

- **aikValidated**:  Booleaanse waarde die informatie bevat of het certificaat van de Attestation-identiteitssleutel (AIK) is gevalideerd of niet.
- **aikPubHash**:  Tekenreeks met de base64(SHA256(openbare-sleutel van AIK n DER-formaat)).
- **tpmVersion**:   Geheel getal-waarde met de primaire versie van de Trusted Platform Module (TPM).
- **secureBootEnabled**: Booleaanse waarde die aangeeft of beveiligd opstarten is ingeschakeld.
- **iommuEnabled**:  Boolean waarde die aangeeft of Input-output-geheugensbeheer-unit (Iommu) is ingeschakeld.
- **bootDebuggingDisabled**: Booleaanse waarde die aangeeft of opstart-foutopsporing is uitgeschakeld.
- **notSafeMode**:  Booleaanse waarde die aangeeft of Windows niet wordt uitgevoerd in de veilige modus.
- **notWinPE**:  Booleaanse waarde die aangeeft of Windows niet in de WinPE-modus wordt uitgevoerd.
- **vbsEnabled**:  Booleaanse waarde die aangeeft of VBS is ingeschakeld.
- **vbsReportPresent**:  Booleaanse waarde waarmee wordt aangegeven of het VBS enclave-rapport beschikbaar is.
- **enclaveAuthorId**:  Tekenreekswaarde met de versleutelde waarde Base64Url van de enclave Auteur-id - de id van de auteur van de primaire module voor de enclave.
- **enclaveImageId**:  Tekenreekswaarde met de versleutelde waarde Base64Url van de enclave-installatie Installatiekopie-id - de id van de installatiekopie van de primaire module voor de enclave.
- **enclaveOwnerId**:  Tekenreekswaarde met de versleutelde waarde Base64Url van de enclave-installatie Eigenaars-id - de id van de eigenaar van de primaire module voor de enclave.
- **enclaveFamilyId**:  Tekenreekswaarde met de Base64Url gecodeerde waarde van de id van de enclave-familie. De familie-id van de primaire module voor de enclave.
- **enclaveSvn**:  Een geheel getalwaarde met het nummer van de beveiligingsversie voor de primaire module voor de enclave.
- **enclavePlatformSvn**:  Een geheel getalwaarde met het nummer van de beveiligingsversie van het platform waarop de enclave wordt gehost.
- **enclaveFlags**:  De enclaveFlags-claim is een geheel getal dat de vlaggen bevat waarmee het runtime-beleid voor de enclave wordt beschreven.
  
### <a name="outgoing-claims"></a>Uitgaande claims

- **policy_hash**:  Tekenreekswaarde met de SHA256-hash van de beleidstekst die wordt berekend door BASE64URL (SHA256 (BASE64URL (UTF8 (Beleidstekst)))).
- **policy_signer**:  Tekenreekswaarde met een JWK met de openbare sleutel of de certificaatketen die in de ondertekende beleidsheader voor komt.
- **ver (Versie)** :  Tekenreekswaarde met de versie van het rapport. Momenteel 1.0.
- **cnf (Bevestigings)-claim**:  De "cnf"-claim wordt gebruikt om de bewijs-van-bezit-sleutel te identificeren. Een bevestigingsclaim zoals gedefinieerd in RFC 7800 bevat het openbare deel van de Attestation-sleutelenclave die wordt weergegeven als een JSON Web Key (JWK)-object (RFC 7517).
- **rp_data (relying party-gegevens)** :  De Relying Party gegevens, indien van toepassing, die zijn opgegeven in de aanvraag, die door de Relying Party als gelegenheid worden gebruikt om de versheid van het rapport te garanderen.
- **"jti" (JWT ID)-claim**: De "jti" (JWT ID)-claim biedt een unieke id voor de JWT. De id-waarde wordt toegewezen op een manier die ervoor zorgt dat er een verwaarloosbare kans is dat dezelfde waarde per ongeluk wordt toegewezen aan een ander gegevensobject.

### <a name="property-claims"></a>Eigenschapsclaims

- **report_validity_in_minutes**: Een geheel getal-claim die aangeeft hoe lang het token geldig is.
  - **Standaardwaarde (tijd)** : Eén dag in minuten.
  - **Maximale waarde (tijd)** : Één jaar in minuten.
- **omit_x5c**: Een Booleaanse claim die aangeeft of het certificaat dat wordt gebruikt voor het controleren van de service-authenticiteit, door Azure Attestation moet worden weggelaten. Indien true, wordt x5t toegevoegd aan het Attestation-token. Indien false, wordt x5c toegevoegd aan het Attestation-token.

## <a name="next-steps"></a>Volgende stappen
- [Een Attestation-beleid ontwerpen en ondertekenen](author-sign-policy.md)
- [Azure Attestation instellen met behulp van PowerShell](quickstart-powershell.md)
