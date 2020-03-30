---
title: Een technisch profiel van een telefoonfactor definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een technisch profiel van een telefoonfactor in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3a0511a19477f3d76baf9c453316c5348cc31397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332658"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een technisch profiel van een telefoonfactor definiëren in een aangepast Azure Directory B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor het inschrijven en verifiëren van telefoonnummers. Dit technische profiel:

- Biedt een gebruikersinterface om met de gebruiker te communiceren.
- Gebruikt inhoudsdefinitie om het uiterlijk te bepalen.
- Ondersteunt zowel telefoongesprekken als sms-berichten om het telefoonnummer te valideren.
- Ondersteunt meerdere telefoonnummers. De gebruiker kan een van de telefoonnummers selecteren om te verifiëren.  
- Als een telefoonnummer wordt opgegeven, vraagt de gebruikersinterface van de telefoonfactor de gebruiker om het telefoonnummer te verifiëren. Indien dit niet is voorzien, vraagt het de gebruiker om een nieuw telefoonnummer in te schrijven.
- Retourneert een claim die aangeeft of de gebruiker een nieuw telefoonnummer heeft opgegeven. U deze claim gebruiken om te bepalen of het telefoonnummer moet worden gehandhaafd in het Azure AD-gebruikersprofiel.  

## <a name="protocol"></a>Protocol

Het kenmerk **Naam** van het element `Proprietary` **Protocol** moet worden ingesteld op . Het **handlerkenmerk** moet de volledig gekwalificeerde naam bevatten van de protocolhandlerassemblage die wordt gebruikt door Azure AD B2C voor telefoonfactor:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

In het volgende voorbeeld wordt een technisch profiel van de telefoonfactor weergegeven voor inschrijving en validatie:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Invoerclaims

Het element InputClaims moet volgende claims bevatten. U de naam van uw claim ook toewijzen aan de naam die is gedefinieerd in het technische profiel van de telefoonfactor. 

```XML
<InputClaims>
  <!--A unique identifier of the user. The partner claim type must be set to `UserId`. -->
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <!--A claim that contains the phone number. If the claim is empty, Azure AD B2C asks the user to enroll a new phone number. Otherwise, it asks the user to verify the phone number. -->
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
</InputClaims>
```

In het volgende voorbeeld wordt aangetoond dat u meerdere telefoonnummers gebruikt. Zie [voorbeeldbeleid voor](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)meer informatie .

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

Het element InputClaimsTransformations kan een verzameling inputclaimstransformatie-elementen bevatten die worden gebruikt om de invoerclaims te wijzigen of nieuwe claims te genereren voordat ze worden gepresenteerd aan de pagina met de telefoonfactor.

## <a name="output-claims"></a>Outputclaims

Het element OutputClaims bevat een lijst met claims die zijn geretourneerd door het technische profiel van de telefoonfactor.

```xml
<OutputClaims>
  <!-- The verified phone number. The partner claim type must be set to `Verified.OfficePhone`. -->
  <OutputClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="Verified.OfficePhone" />
  <!-- Indicates whether the new phone number has been entered by the user. The partner claim type must be set to `newPhoneNumberEntered`. -->
  <OutputClaim ClaimTypeReferenceId="newPhoneNumberEntered" PartnerClaimType="newPhoneNumberEntered" />
</OutputClaims>
```

Het element OutputClaimsTransformations kan een verzameling outputclaimstransformatie-elementen bevatten die worden gebruikt om de uitvoerclaims te wijzigen of nieuwe claims te genereren.

## <a name="cryptographic-keys"></a>Cryptografische toetsen

Het element **CryptographicKeys** wordt niet gebruikt.


## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Ja | De id van de [inhoudsdefinitie](contentdefinitions.md) die aan dit technische profiel is gekoppeld. |
| ManualPhoneNumberEntryA toegestaan| Nee | Geef op of een gebruiker handmatig een telefoonnummer mag invoeren. Mogelijke `true` waarden: `false` of (standaard).|

### <a name="ui-elements"></a>UI-elementen

De gebruikersinterface-elementen van de telefoonfactorverificatiepagina kunnen worden [gelokaliseerd.](localization-string-ids.md#azure-mfa-error-messages)

## <a name="next-steps"></a>Volgende stappen

- Controleer de [sociale en lokale accounts met MFA](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) starter pack.

