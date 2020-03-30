---
title: Technische azure MFA-profielen in aangepast beleid
titleSuffix: Azure AD B2C
description: Aangepaste beleidsverwijzing voor Azure Multi-Factor Authentication (MFA) technische profielen in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c9ed0e329b498112feafaf21c34e85ea436cbb77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332805"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Een technisch azure MFA-profiel definiëren in een aangepast Azure AD B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor het verifiëren van een telefoonnummer met behulp van Azure Multi-Factor Authentication (MFA). Gebruik dit technische profiel om een code te genereren en naar een telefoonnummer te verzenden en controleer vervolgens de code. Het technische azure MFA-profiel kan ook een foutbericht retourneren.  Het validatietechnische profiel valideert de door de gebruiker verstrekte gegevens voordat de gebruikersreis wordt voortgezet. Met het validatietechnische profiel wordt een foutbericht weergegeven op een zelfverklaarde pagina.

Dit technische profiel:

- Biedt geen interface om met de gebruiker te communiceren. In plaats daarvan wordt de gebruikersinterface aangeroepen vanuit een [zelfgeclaimd](self-asserted-technical-profile.md) technisch profiel of een [weergavebesturingselement](display-controls.md) als [een technisch validatieprofiel.](validation-technical-profile.md)
- Gebruikt de Azure MFA-service om een code te genereren en naar een telefoonnummer te verzenden en verifieert vervolgens de code.  
- Valideert een telefoonnummer via tekstberichten.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol

Het kenmerk **Naam** van het element `Proprietary` **Protocol** moet worden ingesteld op . Het **handlerkenmerk** moet de volledig gekwalificeerde naam bevatten van de protocolhandlerassemblage die wordt gebruikt door Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

In het volgende voorbeeld wordt een technisch azure MFA-profiel weergegeven:

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Sms verzenden

De eerste modus van dit technische profiel is het genereren van een code en verzenden. Voor deze modus kunnen de volgende opties worden geconfigureerd.

### <a name="input-claims"></a>Invoerclaims

Het element **InputClaims** bevat een lijst met claims die naar Azure MFA moeten worden verzonden. U de naam van uw claim ook toewijzen aan de naam die is gedefinieerd in het technische mfa-profiel.

| ClaimReferenceId | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| userPrincipalName | Ja | De id voor de gebruiker die eigenaar is van het telefoonnummer. |
| phoneNumber | Ja | Het telefoonnummer om een sms-code naar te sturen. |
| Bedrijfsnaam | Nee |De bedrijfsnaam in de SMS. Indien niet voorzien, wordt de naam van uw toepassing gebruikt. |
| landinstellingen | Nee | De plaats van de SMS. Indien niet voorzien, wordt de browserlocale van de gebruiker gebruikt. |

Het element **InputClaimsTransformations** kan een verzameling **inputclaimstransformatie-elementen** bevatten die worden gebruikt om de invoerclaims te wijzigen of nieuwe claims te genereren voordat ze naar de Azure MFA-service worden verzonden.

### <a name="output-claims"></a>Outputclaims

De Azure MFA-protocolprovider retourneert geen **outputclaims,** dus er is geen noodzaak om uitvoerclaims op te geven. U echter claims opnemen die niet worden geretourneerd door de Azure `DefaultValue` MFA-identiteitsprovider zolang u het kenmerk instelt.

Het element **OutputClaimsTransformations** kan een verzameling **outputclaimstransformatie-elementen** bevatten die worden gebruikt om de uitvoerclaims te wijzigen of nieuwe claims te genereren.

### <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Bewerking | Ja | Moet **OneWaySMS**zijn.  |

#### <a name="ui-elements"></a>UI-elementen

De volgende metagegevens kunnen worden gebruikt om de foutmeldingen te configureren die worden weergegeven bij het verzenden van sms-fouten. De metagegevens moeten worden geconfigureerd in het [zelfverklaarde](self-asserted-technical-profile.md) technische profiel. De foutberichten kunnen worden [gelokaliseerd](localization-string-ids.md#azure-mfa-error-messages).

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | Nee | Foutbericht van de gebruiker als het opgegeven telefoonnummer geen sms accepteert. |
| UserMessageIfInvalidOpmaak | Nee | Foutbericht van de gebruiker als het opgegeven telefoonnummer geen geldig telefoonnummer is. |
| UserMessageIfServerFout | Nee | Foutbericht van de gebruiker als de server een interne fout heeft opgelopen. |
| UserMessageIfThrottled| Nee | Foutbericht van de gebruiker als een verzoek is beperkt.|

### <a name="example-send-an-sms"></a>Voorbeeld: stuur een SMS

In het volgende voorbeeld wordt een technisch azure MFA-profiel weergegeven dat wordt gebruikt om een code via sms te verzenden.

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
  <DisplayName>Send Sms</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">OneWaySMS</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
    <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Code verifiëren

De tweede modus van dit technische profiel is het verifiëren van een code. Voor deze modus kunnen de volgende opties worden geconfigureerd.

### <a name="input-claims"></a>Invoerclaims

Het element **InputClaims** bevat een lijst met claims die naar Azure MFA moeten worden verzonden. U de naam van uw claim ook toewijzen aan de naam die is gedefinieerd in het technische mfa-profiel.

| ClaimReferenceId | Vereist | Beschrijving |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Ja | Hetzelfde telefoonnummer als voorheen gebruikt om een code te verzenden. Het wordt ook gebruikt om een telefonische verificatiesessie te vinden. |
| verificatieCode  | Ja | De door de te verifiëren verificatiecode van de gebruiker |

Het element **InputClaimsTransformations** kan een verzameling **inputclaimstransformatie-elementen** bevatten die worden gebruikt om de invoerclaims te wijzigen of nieuwe claims te genereren voordat u de Azure MFA-service aanroept.

### <a name="output-claims"></a>Outputclaims

De Azure MFA-protocolprovider retourneert geen **outputclaims,** dus er is geen noodzaak om uitvoerclaims op te geven. U echter claims opnemen die niet worden geretourneerd door de Azure `DefaultValue` MFA-identiteitsprovider zolang u het kenmerk instelt.

Het element **OutputClaimsTransformations** kan een verzameling **outputclaimstransformatie-elementen** bevatten die worden gebruikt om de uitvoerclaims te wijzigen of nieuwe claims te genereren.

### <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Bewerking | Ja | Moet **verifiëren** zijn |

#### <a name="ui-elements"></a>UI-elementen

De volgende metagegevens kunnen worden gebruikt om de foutberichten te configureren die worden weergegeven bij het mislukken van de codeverificatie. De metagegevens moeten worden geconfigureerd in het [zelfverklaarde](self-asserted-technical-profile.md) technische profiel. De foutberichten kunnen worden [gelokaliseerd](localization-string-ids.md#azure-mfa-error-messages).

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| Nee | Foutbericht van de gebruiker als de gebruiker te vaak een verificatiecode heeft geprobeerd. |
| UserMessageIfServerFout | Nee | Foutbericht van de gebruiker als de server een interne fout heeft opgelopen. |
| UserMessageIfThrottled| Nee | Foutbericht van de gebruiker als het verzoek wordt beperkt.|
| UserMessageIfWrongCodeentered| Nee| Foutbericht van de gebruiker als de code die voor verificatie is ingevoerd, onjuist is.|

### <a name="example-verify-a-code"></a>Voorbeeld: een code verifiëren

In het volgende voorbeeld wordt een technisch azure MFA-profiel weergegeven dat wordt gebruikt om de code te verifiëren.

```XML
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
