---
title: OTP-verificatie (one-time password) inschakelen
titleSuffix: Azure AD B2C
description: Meer informatie over het instellen van een OTP-scenario (one-time password) met behulp van Azure AD B2C aangepast beleid.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9becb91cfffd4553b2b8aa1a2d616963eae92ab0
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114054"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Een eenmalige wachtwoord technische profiel definiëren in een Azure AD B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor het beheren van het genereren en verifiëren van een eenmalig wacht woord. Gebruik een technisch profiel om een code te genereren en controleer de code later.

Het eenmalige wacht woord technisch profiel kan ook een fout bericht retour neren tijdens de verificatie van de code. Ontwerp de integratie met het eenmalige wacht woord met behulp van een **validatie technische profiel**. Een profiel voor validatie technische verificatie roept het technische profiel voor eenmalige wacht woorden aan om een code te controleren. Het validatie-technische profiel valideert de door de gebruiker verschafte gegevens voordat de gebruikers traject doorgaat. Met het technische profiel voor validatie wordt er een fout bericht weer gegeven op een zelf-bevestigingen pagina.

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `Proprietary`. Het kenmerk **handler** moet de volledig gekwalificeerde naam van de assembly van de protocolhandler bevatten die wordt gebruikt door Azure AD B2C:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

In het volgende voor beeld ziet u een technisch profiel met een eenmalig wacht woord:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Code genereren

De eerste modus van dit technische profiel is het genereren van een code. Hieronder vindt u de opties die kunnen worden geconfigureerd voor deze modus.

### <a name="input-claims"></a>Invoer claims

Het **InputClaims** -element bevat een lijst met claims die vereist zijn voor verzen ding naar de provider voor eenmalig wachtwoord protocol. U kunt de naam van uw claim ook toewijzen aan de hieronder gedefinieerde naam.

| ClaimReferenceId | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ID | Ja | De id waarmee de gebruiker wordt geïdentificeerd die de code later moet verifiëren. Dit wordt vaak gebruikt als de id van de bestemming waarnaar de code wordt geleverd, bijvoorbeeld e-mail adres of telefoon nummer. |

Het **InputClaimsTransformations** -element kan een verzameling **InputClaimsTransformation** -elementen bevatten die worden gebruikt om de invoer claims te wijzigen of nieuwe te genereren voordat deze naar de eenmalige wachtwoord protocol provider worden verzonden.

### <a name="output-claims"></a>Uitvoer claims

Het **OutputClaims** -element bevat een lijst met claims die zijn gegenereerd door de provider voor eenmalig wacht woord protocol. U kunt de naam van uw claim ook toewijzen aan de hieronder gedefinieerde naam.

| ClaimReferenceId | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| otpGenerated | Ja | De gegenereerde code waarvan de sessie wordt beheerd door Azure AD B2C. |

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

### <a name="metadata"></a>Metagegevens

De volgende instellingen kunnen worden gebruikt voor het configureren van code generatie en-onderhoud:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | Nee | Tijd in seconden totdat de code verloopt. Minimum: `60`; Maximum: `1200`; Standaard: `600`. |
| CodeLength | Nee | Lengte van de code. De standaard waarde is `6`. |
| CharacterSet | Nee | De tekenset voor de code, opgemaakt voor gebruik in een reguliere expressie. Bijvoorbeeld `a-z0-9A-Z`. De standaard waarde is `0-9`. De tekenset moet mini maal tien verschillende tekens bevatten in de opgegeven set. |
| NumRetryAttempts | Nee | Het aantal verificatie pogingen voordat de code als ongeldig wordt beschouwd. De standaard waarde is `5`. |
| Bewerking | Ja | De bewerking die moet worden uitgevoerd. Mogelijke waarden: `GenerateCode`of `VerifyCode`. |
| ReuseSameCode | Nee | Of er een dubbele code moet worden gegeven in plaats van een nieuwe code te genereren wanneer de gegeven code niet is verlopen en nog geldig is. De standaard waarde is `false`. |

### <a name="returning-error-message"></a>Fout bericht retour neren

Er is geen fout bericht geretourneerd voor de modus voor code generatie.

### <a name="example"></a>Voorbeeld

Het volgende voor beeld `TechnicalProfile` wordt gebruikt voor het genereren van een code:

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

De tweede modus van dit technische profiel is om een code te controleren. Hieronder vindt u de opties die kunnen worden geconfigureerd voor deze modus.

### <a name="input-claims"></a>Invoer claims

Het **InputClaims** -element bevat een lijst met claims die vereist zijn voor verzen ding naar de provider voor eenmalig wachtwoord protocol. U kunt de naam van uw claim ook toewijzen aan de hieronder gedefinieerde naam.

| ClaimReferenceId | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ID | Ja | De id voor het identificeren van de gebruiker die eerder een code heeft gegenereerd. Dit wordt vaak gebruikt als de id van de bestemming waarnaar de code wordt geleverd, bijvoorbeeld e-mail adres of telefoon nummer. |
| otpToVerify | Ja | De verificatie code van de gebruiker. |

Het **InputClaimsTransformations** -element kan een verzameling **InputClaimsTransformation** -elementen bevatten die worden gebruikt om de invoer claims te wijzigen of nieuwe te genereren voordat deze naar de eenmalige wachtwoord protocol provider worden verzonden.

### <a name="output-claims"></a>Uitvoer claims

Er zijn geen uitvoer claims beschikbaar tijdens de code verificatie van deze protocol provider.

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

### <a name="metadata"></a>Metagegevens

De volgende instellingen kunnen worden gebruikt voor het configureren van het fout bericht dat wordt weer gegeven bij fout bij code verificatie:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | Nee | Het bericht dat wordt weer gegeven aan de gebruiker als de sessie met de code verificatie is verlopen. Het is de code verlopen of de code is nooit voor een bepaalde id gegenereerd. |
| UserMessageIfMaxRetryAttempted | Nee | Het bericht dat aan de gebruiker wordt weer gegeven als het maximum aantal toegestane verificatie pogingen is overschreden. |
| UserMessageIfInvalidCode | Nee | Het bericht dat wordt weer gegeven aan de gebruiker als deze een ongeldige code heeft gegeven. |

### <a name="returning-error-message"></a>Fout bericht retour neren

Zoals wordt beschreven in [meta gegevens](#metadata), kunt u het fout bericht dat wordt weer gegeven aan de gebruiker aanpassen voor verschillende fout situaties. U kunt deze berichten verder lokaliseren door de land instelling te prefixen, bijvoorbeeld:

```XML
<Item Key="en.UserMessageIfInvalidCode">Wrong code has been entered.</Item>
```

### <a name="example"></a>Voorbeeld

Het volgende voor beeld `TechnicalProfile` wordt gebruikt voor het controleren van een code:

```XML
<TechnicalProfile Id="VerifyCode">
    <DisplayName>Verify Code</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">VerifyCode</Item>
        <Item Key="UserMessageIfInvalidCode">Wrong code has been entered.</Item>
        <Item Key="UserMessageIfSessionDoesNotExist">Code has expired.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You've tried too many times.</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
    </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor een voor beeld van het gebruik van een technial-profiel met een eenmalig wacht woord met aangepaste e-mail verificatie:

- [Aangepaste e-mail verificatie in Azure Active Directory B2C](custom-email.md)

