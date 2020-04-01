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
ms.date: 03/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e2b30e8f6bcbe7c0e739455f4942712f68ff8404
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437455"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een technisch profiel van een telefoonfactor definiëren in een aangepast Azure Directory B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor het inschrijven en verifiëren van telefoonnummers. Dit technische profiel:

- Biedt een gebruikersinterface om te communiceren met de gebruiker om een telefoonnummer te verifiëren of in te schrijven.
- Ondersteunt telefoongesprekken en sms-berichten om het telefoonnummer te valideren.
- Ondersteunt meerdere telefoonnummers. De gebruiker kan een van de telefoonnummers selecteren om te verifiëren.  
- Retourneert een claim die aangeeft of de gebruiker een nieuw telefoonnummer heeft opgegeven. U deze claim gebruiken om te bepalen of het telefoonnummer moet worden gehandhaafd in het Azure AD B2C-gebruikersprofiel.  
- Hiermee wordt een [inhoudsdefinitie gebruikt](contentdefinitions.md) om het uiterlijk te bepalen.

## <a name="protocol"></a>Protocol

Het kenmerk **Naam** van het element `Proprietary` **Protocol** moet worden ingesteld op . Het **handlerkenmerk** moet de volledig gekwalificeerde naam bevatten van de protocolhandlerassemblage die wordt gebruikt door Azure AD B2C voor telefoonfactor:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

In het volgende voorbeeld wordt een technisch profiel van de telefoonfactor weergegeven voor inschrijving en validatie:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Transformaties van invoerclaims

Het element InputClaimsTransformations kan een verzameling transformaties van invoerclaims bevatten die worden gebruikt om de invoerclaims te wijzigen of nieuwe claims te genereren. De volgende transformatie van `UserId` invoerclaims genereert een claim die later wordt gebruikt in de verzameling invoerclaims.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Invoerclaims

Het element InputClaims moet de volgende claims bevatten. U de naam van uw claim ook toewijzen aan de naam die is gedefinieerd in het technische profiel van de telefoonfactor. 

|  Gegevenstype| Vereist | Beschrijving |
| --------- | -------- | ----------- | 
| tekenreeks| Ja | Een unieke id voor de gebruiker. De claimnaam of PartnerClaimType moet `UserId`zijn ingesteld op . Deze claim mag geen persoonlijke identificeerbare informatie bevatten.|
| tekenreeks| Ja | Lijst met claimtypen. Elke claim bevat één telefoonnummer. Als een van de invoerclaims geen telefoonnummer bevat, wordt de gebruiker gevraagd zich in te schrijven en een nieuw telefoonnummer te verifiëren. Het gevalideerde telefoonnummer wordt geretourneerd als uitvoerclaim. Als een van de invoerclaims een telefoonnummer bevat, wordt de gebruiker gevraagd het te verifiëren. Als meerdere invoerclaims een telefoonnummer bevatten, wordt de gebruiker gevraagd een van de telefoonnummers te kiezen en te verifiëren. |

In het volgende voorbeeld wordt aangetoond dat u meerdere telefoonnummers gebruikt. Zie [voorbeeldbeleid voor](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)meer informatie .

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Outputclaims

Het element OutputClaims bevat een lijst met claims die zijn geretourneerd door het technische profiel van de telefoonfactor.

|  Gegevenstype| Vereist | Beschrijving |
|  -------- | ----------- |----------- |
| booleaans | Ja | Geeft aan of het nieuwe telefoonnummer door de gebruiker is ingevoerd. De claimnaam of PartnerClaimType moet zijn ingesteld op`newPhoneNumberEntered`|
| tekenreeks| Ja | Het geverifieerde telefoonnummer. De claimnaam of PartnerClaimType moet `Verified.OfficePhone`zijn ingesteld op .|

Het element OutputClaimsTransformations kan een verzameling outputclaimstransformatie-elementen bevatten die worden gebruikt om de uitvoerclaims te wijzigen of nieuwe claims te genereren.

## <a name="cryptographic-keys"></a>Cryptografische toetsen

Het element **CryptographicKeys** wordt niet gebruikt.


## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Ja | De id van de [inhoudsdefinitie](contentdefinitions.md) die aan dit technische profiel is gekoppeld. |
| ManualPhoneNumberEntryA toegestaan| Nee | Geef op of een gebruiker handmatig een telefoonnummer mag invoeren. Mogelijke waarden: `true` `false` , of (standaard).|
| instelling.authenticationMode | Nee | De methode om het telefoonnummer te valideren. Mogelijke `sms`waarden: `phone`, `mixed` of (standaard).|
| instelling.autodial| Nee| Geef op of het technische profiel automatisch moet bellen of automatisch een sms moet verzenden. Mogelijke waarden: `true` `false` , of (standaard). Voor automatisch `setting.authenticationMode` kiezen moet `sms`de `phone`metagegevens worden ingesteld op , of . De verzameling invoerclaims moet één telefoonnummer hebben. |

### <a name="ui-elements"></a>UI-elementen

De gebruikersinterface-elementen van de telefoonfactorverificatiepagina kunnen worden [gelokaliseerd.](localization-string-ids.md#azure-mfa-error-messages)

## <a name="next-steps"></a>Volgende stappen

- Controleer de [sociale en lokale accounts met MFA](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) starter pack.
