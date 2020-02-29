---
title: Technische profielen voor Azure MFA in aangepast beleid
titleSuffix: Azure AD B2C
description: Aangepaste beleids verwijzing voor de technische profielen van Azure Multi-Factor Authentication (MFA) in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 05851dba9de06b5dfba2da4f455fbaf5e9376d08
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184278"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Een Azure MFA-technische profiel definiëren in een Azure AD B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor het controleren van een telefoon nummer met behulp van Azure Multi-Factor Authentication (MFA). Gebruik dit technische profiel om een code te genereren en te verzenden naar een telefoon nummer en controleer vervolgens de code.

Het technische profiel voor Azure MFA kan ook een fout bericht retour neren. U kunt de integratie met Azure MFA ontwerpen met behulp van een **validatie technische profiel**. Een technisch profiel voor validatie roept de Azure MFA-service aan. Het validatie-technische profiel valideert de door de gebruiker verschafte gegevens voordat de gebruikers traject doorgaat. Met het technische profiel voor validatie wordt een fout bericht weer gegeven op een zelf-bevestigingen pagina.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `Proprietary`. Het kenmerk **handler** moet de volledig gekwalificeerde naam van de assembly van de protocolhandler bevatten die wordt gebruikt door Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

In het volgende voor beeld ziet u een technisch Azure MFA-profiel:

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>SMS verzenden

De eerste modus van dit technische profiel is om een code te genereren en deze te verzenden. De volgende opties kunnen worden geconfigureerd voor deze modus.

### <a name="input-claims"></a>Invoer claims

Het **InputClaims** -element bevat een lijst met claims die naar Azure MFA moeten worden verzonden. U kunt de naam van uw claim ook toewijzen aan de naam die is gedefinieerd in het technische profiel voor MFA.

| ClaimReferenceId | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| userPrincipalName | Ja | De id voor de gebruiker die eigenaar is van het telefoon nummer. |
| phoneNumber | Ja | Het telefoon nummer waarnaar een SMS-code moet worden verzonden. |
| companyName | Nee |De bedrijfs naam in het SMS. Als u dit niet opgeeft, wordt de naam van uw toepassing gebruikt. |
| instelling | Nee | De land instelling van het SMS. Als dat niet is vermeld, wordt de land instelling van de gebruiker gebruikt. |

Het **InputClaimsTransformations** -element kan een verzameling **InputClaimsTransformation** -elementen bevatten die worden gebruikt om de invoer claims te wijzigen of nieuwe te genereren voordat deze naar de Azure MFA-service worden verzonden.

### <a name="output-claims"></a>Uitvoer claims

De Azure MFA-protocol provider retourneert geen **OutputClaims**, dus u hoeft geen uitvoer claims op te geven. U kunt echter claims toevoegen die niet worden geretourneerd door de Azure MFA-ID-provider zolang u het kenmerk `DefaultValue` hebt ingesteld.

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

### <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Bewerking | Ja | Moet **OneWaySMS**zijn.  |
| UserMessageIfInvalidFormat | Nee | Aangepast fout bericht als het ingevoerde telefoon nummer geen geldig telefoon nummer is |
| UserMessageIfCouldntSendSms | Nee | Aangepast fout bericht als het door u ingevoerde telefoon nummer geen SMS accepteert |
| UserMessageIfServerError | Nee | Aangepast fout bericht als er een interne fout is opgetreden op de server |

### <a name="return-an-error-message"></a>Een fout bericht retour neren

Zoals beschreven in [meta gegevens](#metadata), kunt u het fout bericht dat wordt weer gegeven aan de gebruiker aanpassen voor verschillende fout cases. U kunt deze berichten verder lokaliseren door het voor voegsel van de land instelling. Bijvoorbeeld:

```XML
<Item Key="en.UserMessageIfInvalidFormat">Invalid phone number.</Item>
```

### <a name="example-send-an-sms"></a>Voor beeld: een SMS verzenden

In het volgende voor beeld ziet u een technisch Azure MFA-profiel dat wordt gebruikt om via SMS een code te verzenden.

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

De tweede modus van dit technische profiel is om een code te controleren. De volgende opties kunnen worden geconfigureerd voor deze modus.

### <a name="input-claims"></a>Invoer claims

Het **InputClaims** -element bevat een lijst met claims die naar Azure MFA moeten worden verzonden. U kunt de naam van uw claim ook toewijzen aan de naam die is gedefinieerd in het technische profiel voor MFA.

| ClaimReferenceId | Vereist | Beschrijving |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Ja | Hetzelfde telefoon nummer als eerder gebruikt om een code te verzenden. Het wordt ook gebruikt om een sessie voor telefoon verificatie te vinden. |
| verificationCode  | Ja | De verificatie code van de gebruiker die moet worden geverifieerd |

Het **InputClaimsTransformations** -element kan een verzameling **InputClaimsTransformation** -elementen bevatten die worden gebruikt om de invoer claims te wijzigen of nieuwe te genereren voordat de Azure MFA-service wordt aangeroepen.

### <a name="output-claims"></a>Uitvoer claims

De Azure MFA-protocol provider retourneert geen **OutputClaims**, dus u hoeft geen uitvoer claims op te geven. U kunt echter claims toevoegen die niet worden geretourneerd door de Azure MFA-ID-provider zolang u het kenmerk `DefaultValue` hebt ingesteld.

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Bewerking | Ja | Moet worden **gecontroleerd** |
| UserMessageIfInvalidFormat | Nee | Aangepast fout bericht als het ingevoerde telefoon nummer geen geldig telefoon nummer is |
| UserMessageIfWrongCodeEntered | Nee | Aangepast fout bericht als de code die is ingevoerd voor verificatie, onjuist is |
| UserMessageIfMaxAllowedCodeRetryReached | Nee | Aangepast fout bericht als de gebruiker te vaak een verificatie code heeft geprobeerd |
| UserMessageIfThrottled | Nee | Aangepast fout bericht als de gebruiker wordt beperkt |
| UserMessageIfServerError | Nee | Aangepast fout bericht als er een interne fout is opgetreden op de server |

### <a name="return-an-error-message"></a>Een fout bericht retour neren

Zoals beschreven in [meta gegevens](#metadata), kunt u het fout bericht dat wordt weer gegeven aan de gebruiker aanpassen voor verschillende fout cases. U kunt deze berichten verder lokaliseren door het voor voegsel van de land instelling. Bijvoorbeeld:

```XML
<Item Key="en.UserMessageIfWrongCodeEntered">Wrong code has been entered.</Item>
```

### <a name="example-verify-a-code"></a>Voor beeld: een code verifiëren

In het volgende voor beeld ziet u een technisch Azure MFA-profiel dat wordt gebruikt om de code te controleren.

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
