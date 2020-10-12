---
title: Technische profielen voor Azure MFA in aangepast beleid
titleSuffix: Azure AD B2C
description: Aangepaste beleids verwijzing voor de technische profielen van Azure Multi-Factor Authentication (MFA) in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 71040f831ed7a64f2bc7be7f3a75218976fc2559
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85385940"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Een Azure MFA-technische profiel definiëren in een Azure AD B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor het controleren van een telefoon nummer met behulp van Azure Multi-Factor Authentication (MFA). Gebruik dit technische profiel om een code te genereren en te verzenden naar een telefoon nummer en controleer vervolgens de code. Het technische profiel voor Azure MFA kan ook een fout bericht retour neren.  Het validatie-technische profiel valideert de door de gebruiker verschafte gegevens voordat de gebruikers traject doorgaat. Met het technische profiel voor validatie wordt een fout bericht weer gegeven op een zelf-bevestigingen pagina.

Dit technische profiel:

- Biedt geen interface om met de gebruiker te communiceren. In plaats daarvan wordt de gebruikers interface aangeroepen vanuit een [zelfbevestigend](self-asserted-technical-profile.md) technisch profiel of een [weer gave-besturings element](display-controls.md) als [technische profiel voor validatie](validation-technical-profile.md).
- Maakt gebruik van de Azure MFA-service om een code te genereren en te verzenden naar een telefoon nummer, waarna de code wordt geverifieerd.  
- Hiermee wordt een telefoon nummer gevalideerd via SMS-berichten.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `Proprietary` . Het kenmerk **handler** moet de volledig gekwalificeerde naam van de assembly van de protocolhandler bevatten die wordt gebruikt door Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

In het volgende voor beeld ziet u een technisch Azure MFA-profiel:

```xml
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
| landinstelling | Nee | De land instelling van het SMS. Als dat niet is vermeld, wordt de land instelling van de gebruiker gebruikt. |

Het **InputClaimsTransformations** -element kan een verzameling **InputClaimsTransformation** -elementen bevatten die worden gebruikt om de invoer claims te wijzigen of nieuwe te genereren voordat deze naar de Azure MFA-service worden verzonden.

### <a name="output-claims"></a>Uitvoer claims

De Azure MFA-protocol provider retourneert geen **OutputClaims**, dus u hoeft geen uitvoer claims op te geven. U kunt echter claims toevoegen die niet worden geretourneerd door de Azure MFA-ID-provider zolang u het kenmerk hebt ingesteld `DefaultValue` .

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

### <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Bewerking | Ja | Moet **OneWaySMS**zijn.  |

#### <a name="ui-elements"></a>UI-elementen

De volgende meta gegevens kunnen worden gebruikt voor het configureren van de fout berichten die worden weer gegeven bij het verzenden van een SMS-fout. De meta gegevens moeten worden geconfigureerd in het [zelfondertekende](self-asserted-technical-profile.md) technische profiel. De fout berichten kunnen worden [gelokaliseerd](localization-string-ids.md#azure-mfa-error-messages).

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | Nee | Fout bericht van gebruiker als het telefoon nummer geen SMS accepteert. |
| UserMessageIfInvalidFormat | Nee | Fout bericht van gebruiker als het gegeven telefoon nummer geen geldig telefoon nummer is. |
| UserMessageIfServerError | Nee | Fout bericht van gebruiker als er een interne fout is opgetreden op de server. |
| UserMessageIfThrottled| Nee | Gebruikers fout bericht als een aanvraag is beperkt.|

### <a name="example-send-an-sms"></a>Voor beeld: een SMS verzenden

In het volgende voor beeld ziet u een technisch Azure MFA-profiel dat wordt gebruikt om via SMS een code te verzenden.

```xml
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

De Azure MFA-protocol provider retourneert geen **OutputClaims**, dus u hoeft geen uitvoer claims op te geven. U kunt echter claims toevoegen die niet worden geretourneerd door de Azure MFA-ID-provider zolang u het kenmerk hebt ingesteld `DefaultValue` .

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

### <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Bewerking | Ja | Moet worden **gecontroleerd** |

#### <a name="ui-elements"></a>UI-elementen

De volgende meta gegevens kunnen worden gebruikt voor het configureren van de fout berichten die worden weer gegeven wanneer de code verificatie mislukt. De meta gegevens moeten worden geconfigureerd in het [zelfondertekende](self-asserted-technical-profile.md) technische profiel. De fout berichten kunnen worden [gelokaliseerd](localization-string-ids.md#azure-mfa-error-messages).

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| Nee | Fout bericht van gebruiker als de gebruiker te vaak een verificatie code heeft geprobeerd. |
| UserMessageIfServerError | Nee | Fout bericht van gebruiker als er een interne fout is opgetreden op de server. |
| UserMessageIfThrottled| Nee | Fout bericht van gebruiker als de aanvraag wordt beperkt.|
| UserMessageIfWrongCodeEntered| Nee| Fout bericht van gebruiker als de code die is ingevoerd voor verificatie, onjuist is.|

### <a name="example-verify-a-code"></a>Voor beeld: een code verifiëren

In het volgende voor beeld ziet u een technisch Azure MFA-profiel dat wordt gebruikt om de code te controleren.

```xml
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
