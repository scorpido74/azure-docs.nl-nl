---
title: OTP-verificatie (one-time password) inschakelen
titleSuffix: Azure AD B2C
description: Meer informatie over het instellen van een OTP-scenario (een eenmalig wachtwoord) met behulp van aangepast Azure AD B2C-beleid.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd5fed45332c73c633db1137bdc23aea66fd3403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332785"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Een eenmalig wachtwoordtechnisch profiel definiëren in een aangepast Azure AD B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor het beheren van het genereren en verifiëren van een eenmalig wachtwoord. Gebruik een technisch profiel om een code te genereren en controleer die code later.

Het eenmalige wachtwoordtechnische profiel kan ook een foutbericht retourneren tijdens codeverificatie. Ontwerp de integratie met het eenmalige wachtwoord met behulp van **een technisch validatieprofiel**. Een technisch validatieprofiel roept het eenmalige wachtwoordtechnische profiel aan om een code te verifiëren. Het validatietechnische profiel valideert de door de gebruiker verstrekte gegevens voordat de gebruikersreis wordt voortgezet. Met het validatietechnische profiel wordt een foutmelding weergegeven op een zelfverklaarde pagina.

## <a name="protocol"></a>Protocol

Het kenmerk **Naam** van het element `Proprietary` **Protocol** moet worden ingesteld op . Het **handlerkenmerk** moet de volledig gekwalificeerde naam bevatten van de protocolhandlerassemblage die wordt gebruikt door Azure AD B2C:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

In het volgende voorbeeld wordt een eenmalig wachtwoordtechnisch profiel weergegeven:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Code genereren

De eerste modus van dit technische profiel is het genereren van een code. Hieronder vindt u de opties die voor deze modus kunnen worden geconfigureerd.

### <a name="input-claims"></a>Invoerclaims

Het element **InputClaims** bevat een lijst met claims die nodig zijn om naar de eenmalige wachtwoordprotocolprovider te worden verzonden. U de naam van uw claim ook toewijzen aan de onderstaande naam.

| ClaimReferenceId | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | De id om de gebruiker te identificeren die de code later moet verifiëren. Het wordt vaak gebruikt als de id van de bestemming waar de code wordt geleverd aan, bijvoorbeeld e-mailadres of telefoonnummer. |

Het element **InputClaimsTransformations** kan een verzameling **inputclaimstransformatie-elementen** bevatten die worden gebruikt om de invoerclaims te wijzigen of nieuwe claims te genereren voordat ze naar de eenmalige wachtwoordprotocolprovider worden verzonden.

### <a name="output-claims"></a>Outputclaims

Het element **OutputClaims** bevat een lijst met claims die zijn gegenereerd door de eenmalige wachtwoordprotocolprovider. U de naam van uw claim ook toewijzen aan de onderstaande naam.

| ClaimReferenceId | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| otpGegenereerd | Ja | De gegenereerde code waarvan de sessie wordt beheerd door Azure AD B2C. |

Het element **OutputClaimsTransformations** kan een verzameling **outputclaimstransformatie-elementen** bevatten die worden gebruikt om de uitvoerclaims te wijzigen of nieuwe claims te genereren.

### <a name="metadata"></a>Metagegevens

De volgende instellingen kunnen worden gebruikt om de modus voor het genereren van code te configureren:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Codeexpirinseconden | Nee | Tijd in seconden tot de code verloopt. Minimum: `60`; Maximum: `1200`; Standaard: `600`. |
| CodeLengte | Nee | Lengte van de code. De standaardwaarde is `6`. |
| Tekenset | Nee | De tekenset voor de code, opgemaakt voor gebruik in een reguliere expressie. Bijvoorbeeld `a-z0-9A-Z`. De standaardwaarde is `0-9`. De tekenset moet minimaal 10 verschillende tekens in de opgegeven set bevatten. |
| NumRetryAttempts | Nee | Het aantal verificatiepogingen voordat de code als ongeldig wordt beschouwd. De standaardwaarde is `5`. |
| Bewerking | Ja | De uit te voeren bewerking. Mogelijke waarde: `GenerateCode`. |
| HergebruikSameCode | Nee | Of een dubbele code moet worden gegeven in plaats van het genereren van een nieuwe code wanneer bepaalde code is niet verlopen en is nog steeds geldig. De standaardwaarde is `false`. |

### <a name="example"></a>Voorbeeld

Het volgende `TechnicalProfile` voorbeeld wordt gebruikt voor het genereren van een code:

```XML
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Code verifiëren

De tweede modus van dit technische profiel is het verifiëren van een code. Hieronder vindt u de opties die voor deze modus kunnen worden geconfigureerd.

### <a name="input-claims"></a>Invoerclaims

Het element **InputClaims** bevat een lijst met claims die nodig zijn om naar de eenmalige wachtwoordprotocolprovider te worden verzonden. U de naam van uw claim ook toewijzen aan de onderstaande naam.

| ClaimReferenceId | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | De id om de gebruiker te identificeren die eerder een code heeft gegenereerd. Het wordt vaak gebruikt als de id van de bestemming waar de code wordt geleverd aan, bijvoorbeeld e-mailadres of telefoonnummer. |
| otpToverify | Ja | De verificatiecode die door de gebruiker wordt verstrekt. |

Het element **InputClaimsTransformations** kan een verzameling **inputclaimstransformatie-elementen** bevatten die worden gebruikt om de invoerclaims te wijzigen of nieuwe claims te genereren voordat ze naar de eenmalige wachtwoordprotocolprovider worden verzonden.

### <a name="output-claims"></a>Outputclaims

Er worden geen uitvoerclaims verstrekt tijdens de codeverificatie van deze protocolprovider.

Het element **OutputClaimsTransformations** kan een verzameling **outputclaimstransformatie-elementen** bevatten die worden gebruikt om de uitvoerclaims te wijzigen of nieuwe claims te genereren.

### <a name="metadata"></a>Metagegevens

De volgende instellingen kunnen worden gebruikt om de verificatiemodus code:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Bewerking | Ja | De uit te voeren bewerking. Mogelijke waarde: `VerifyCode`. |


### <a name="ui-elements"></a>UI-elementen

De volgende metagegevens kunnen worden gebruikt om de foutberichten te configureren die worden weergegeven bij het mislukken van de codeverificatie. De metagegevens moeten worden geconfigureerd in het [zelfverklaarde](self-asserted-technical-profile.md) technische profiel. De foutberichten kunnen worden [gelokaliseerd](localization-string-ids.md#one-time-password-error-messages).

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| UserMessageIfSessionNietexist | Nee | Het bericht dat aan de gebruiker wordt weergegeven als de codeverificatiesessie is verlopen. Het is of de code is verlopen of de code is nooit gegenereerd voor een bepaalde id. |
| UserMessageIfMaxRetryAttempted | Nee | Het bericht dat aan de gebruiker wordt weergegeven als deze de maximaal toegestane verificatiepogingen heeft overschreden. |
| UserMessageIfInvalidCode | Nee | Het bericht dat aan de gebruiker wordt weergegeven als deze een ongeldige code heeft opgegeven. |
|UserMessageIfSessionConflict|Nee| Het bericht dat aan de gebruiker wordt weergegeven als de code niet kan worden geverifieerd.|

### <a name="example"></a>Voorbeeld

Het volgende `TechnicalProfile` voorbeeld wordt gebruikt voor het verifiëren van een code:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel bijvoorbeeld van het gebruik van een eenmalig wachtwoord technisch profiel met aangepaste e-mail verificatie:

- [Aangepaste e-mailverificatie in Azure Active Directory B2C](custom-email.md)

