---
title: Verwijzing naar besturings element weer geven
titleSuffix: Azure AD B2C
description: Verwijzing voor de besturings elementen Azure AD B2C weer geven. Gebruik weer gave-besturings elementen voor het aanpassen van de gebruikers trajecten die zijn gedefinieerd in uw aangepaste beleids regels.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4998fb19e42e123edd57bfcf10931d594ac4cb44
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188729"
---
# <a name="display-controls"></a>Besturings elementen weer geven

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een **Weergave besturings element** is een gebruikers interface-element met speciale functionaliteit en interactie met de Azure Active Directory B2C (Azure AD B2C) back-end-service. Hiermee kan de gebruiker acties uitvoeren op de pagina die een [validatie technische profiel](validation-technical-profile.md) aan de back-end aanroept. Besturings elementen voor weer gave worden op de pagina weer gegeven en er wordt verwezen door een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md).

In de volgende afbeelding ziet u een zelf-bevestigde aanmeldings pagina met twee besturings elementen voor weer gave waarmee een primair en secundair e-mail adres wordt gevalideerd.

![Voor beeld van weer gave-besturings element](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Vereisten

 In de sectie [meta gegevens](self-asserted-technical-profile.md#metadata) van een niet [-bevestigd technisch profiel](self-asserted-technical-profile.md)moet de [ContentDefinition](contentdefinitions.md) waarnaar wordt verwezen, `DataUri` instellen op pagina-contract versie 2.0.0 of hoger. Bijvoorbeeld:

```XML
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Besturings elementen voor weer gave definiëren

Het element **DisplayControl** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id die wordt gebruikt voor het weergave besturings element. Hiernaar kan worden [verwezen](#referencing-display-controls). |
| UserInterfaceControlType | Ja | Het type van het weergave besturings element. Momenteel wordt de [VerificationControl](display-control-verification.md) ondersteund |

Het element **DisplayControl** bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** worden gebruikt om vooraf de waarde in te vullen van de claims die moeten worden verzameld van de gebruiker. |
| DisplayClaims | 0:1 | **DisplayClaims** worden gebruikt om claims weer te geven die van de gebruiker moeten worden verzameld. |
| OutputClaims | 0:1 | **OutputClaims** worden gebruikt om claims weer te geven die tijdelijk moeten worden opgeslagen voor dit **besturings element**. |
| Acties | 0:1 | **Acties** worden gebruikt om de technische profielen voor validatie weer te geven die moeten worden aangeroepen voor gebruikers acties die worden uitgevoerd op de front-end. |

### <a name="input-claims"></a>Invoer claims

In een besturings element voor weer gave kunt u **InputClaims** -elementen gebruiken om de waarde van claims die van de gebruiker op de pagina moeten worden verzameld vooraf in te vullen. Alle **InputClaimsTransformations** kunnen worden gedefinieerd in het door uzelf beweringen technische profiel dat verwijst naar dit weergave besturings element.

In het volgende voor beeld wordt het e-mail adres dat is gecontroleerd met het adres dat al aanwezig is, vooraf ingevuld.

```XML
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Claims weer geven

Elk type weergave besturings element vereist een andere set weer gave claims, [uitvoer claims](#output-claims)en [acties](#display-control-actions) die moeten worden uitgevoerd.

Net als de **weer gave-claims** die in een niet [-bevestigd technisch profiel](self-asserted-technical-profile.md#display-claims)zijn gedefinieerd, vertegenwoordigen de weer gave claims de claims die moeten worden verzameld van de gebruiker in het besturings element voor weer gave. Voor het element **claim** type waarnaar wordt verwezen, moet het **UserInputType** -element worden opgegeven voor een gebruikers invoer die wordt ondersteund door Azure AD B2C, zoals `TextBox` of `DropdownSingleSelect`. Als een aanvraag waarde voor weer geven vereist is voor een **actie**, stelt u het **vereiste** kenmerk in op `true` om ervoor te zorgen dat de gebruiker een waarde voor die specifieke weergave claim opgeeft.

Bepaalde weergave claims zijn vereist voor bepaalde typen weergave besturings elementen. **VerificationCode** is bijvoorbeeld vereist voor het besturings element weer gave van het type **VerificationControl**. Gebruik het kenmerk **ControlClaimType** om op te geven welke DisplayClaim is aangewezen voor die vereiste claim. Bijvoorbeeld:

```XML
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Uitvoer claims

De **uitvoer claims** van een besturings element voor weer gave worden niet naar de volgende Orchestration-stap verzonden. Ze worden tijdelijk alleen opgeslagen voor de huidige sessie voor weergave beheer. Deze tijdelijke claims kunnen worden gedeeld tussen de verschillende acties van hetzelfde weergave besturings element.

Als u de uitvoer claims wilt samen stellen aan de volgende indelings stap, gebruikt u de **OutputClaims** van het daad werkelijke zelf-beweringen technische profiel dat verwijst naar dit weergave besturings element.

### <a name="display-control-actions"></a>Besturings acties weer geven

De **acties** van een weergave besturings element zijn procedures die zich voordoen in de Azure AD B2C back-end wanneer een gebruiker een bepaalde actie uitvoert aan de client zijde (de browser). Bijvoorbeeld de validaties die moeten worden uitgevoerd wanneer de gebruiker een knop op de pagina selecteert.

Een actie definieert een lijst met **technische profielen voor validatie**. Ze worden gebruikt voor het valideren van enkele of alle weer gave claims van het besturings element voor weer gave. Het validatie-technische profiel valideert de gebruikers invoer en retourneert mogelijk een fout naar de gebruiker. U kunt **ContinueOnError**, **ContinueOnSuccess**en **voor waarden** in de actie voor het weer geven van besturings elementen gebruiken die vergelijkbaar zijn met de manier waarop ze worden gebruikt bij [validatie van technische profielen](validation-technical-profile.md) in een zelf bevestigd technisch profiel.

In het volgende voor beeld wordt een code verzonden via e-mail of SMS op basis van de selectie van de **mfaType** claim van de gebruiker.

```XML
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>Verwijzen naar weergave besturings elementen

Er wordt verwezen naar weer gave-besturings elementen in de [weer gave claims](self-asserted-technical-profile.md#display-claims) van het [zelf-beweringen technische profiel](self-asserted-technical-profile.md).

Bijvoorbeeld:

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```
