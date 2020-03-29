---
title: Referentie voor weergavebesturingselement
titleSuffix: Azure AD B2C
description: Referentie voor Azure AD B2C-weergavebesturingselementen. Gebruik weergavebesturingselementen voor het aanpassen van gebruikersreizen die zijn gedefinieerd in uw aangepaste beleid.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188729"
---
# <a name="display-controls"></a>Besturingselementen weergeven

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een **weergavebesturingselement** is een element van de gebruikersinterface dat speciale functionaliteit heeft en interageert met de Azure Active Directory B2C (Azure AD B2C) back-endservice. Hiermee kan de gebruiker acties uitvoeren op de pagina die een [validatietechnisch profiel](validation-technical-profile.md) aan de achterkant aanroepen. Display besturingselementen worden weergegeven op de pagina en worden verwezen door een [zelf-beweerde technisch profiel](self-asserted-technical-profile.md).

De volgende afbeelding illustreert een zelfverklaarde aanmeldingspagina met twee besturingselementen die een primair en secundair e-mailadres valideren.

![Voorbeeld van gerenderd weergavebesturingselement](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Vereisten

 In de sectie [Metagegevens](self-asserted-technical-profile.md#metadata) van een [zelfgeclaimd technisch profiel](self-asserted-technical-profile.md) `DataUri` moet de verwezen [ContentDefinition](contentdefinitions.md) zijn ingesteld op paginacontractversie 2.0.0 of hoger. Bijvoorbeeld:

```XML
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Weergavebesturingselementen definiëren

Het **element DisplayControl** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id die wordt gebruikt voor het weergavebesturingselement. Er kan [naar](#referencing-display-controls)verwezen worden. |
| UserInterfaceControlType | Ja | Het type weergavebesturingselement. Momenteel wordt [VerificationControl](display-control-verification.md) ondersteund |

Het **element DisplayControl** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Invoerclaims | 0:1 | **InputClaims** worden gebruikt om de waarde van de claims die van de gebruiker moeten worden verzameld vooraf in te vullen. |
| Weergaveclaims | 0:1 | **DisplayClaims** worden gebruikt om claims weer te geven die van de gebruiker moeten worden verzameld. |
| Uitvoerclaims | 0:1 | **Uitvoerclaims** worden gebruikt om claims weer te geven die tijdelijk zijn opgeslagen voor deze **DisplayControl**. |
| Acties | 0:1 | **Acties** worden gebruikt om de validatietechnische profielen weer te geven die u wilt aanroepen voor acties van gebruikers die aan de front-end plaatsvinden. |

### <a name="input-claims"></a>Invoerclaims

In een weergavebesturingselement u **inputclaims-elementen** gebruiken om de waarde van claims die van de gebruiker op de pagina moeten worden verzameld, vooraf in te vullen. **Alle InputClaimsTransformations** kunnen worden gedefinieerd in het zelfverklaarde technische profiel dat verwijst naar dit weergavebesturingselement.

In het volgende voorbeeld wordt vooraf het e-mailadres ingevuld dat moet worden geverifieerd met het adres dat al aanwezig is.

```XML
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Claims weergeven

Elk type weergavebesturingselement vereist een andere set weergaveclaims, [uitvoerclaims](#output-claims)en [acties die](#display-control-actions) moeten worden uitgevoerd.

Net als bij de **weergaveclaims** die zijn gedefinieerd in een [zelfgeclaimd technisch profiel,](self-asserted-technical-profile.md#display-claims)vertegenwoordigen de weergaveclaims de claims die moeten worden verzameld van de gebruiker binnen het weergavebesturingselement. Het **element ClaimType** waarnaar wordt verwezen, moet het element **UserInputType** opgeven voor een `TextBox` `DropdownSingleSelect`gebruikersinvoertype dat wordt ondersteund door Azure AD B2C, zoals of . Als een weergaveclaimwaarde vereist is door **Required** een `true` **actie,** stelt u het kenmerk Vereist in om de gebruiker te dwingen een waarde voor die specifieke weergaveclaim te geven.

Bepaalde weergaveclaims zijn vereist voor bepaalde typen weergavebeheer. **Verificatiecode** is bijvoorbeeld vereist voor het weergavebesturingselement van type **VerificationControl**. Gebruik het kenmerk **ControlClaimType** om op te geven welke DisplayClaim is aangewezen voor die vereiste claim. Bijvoorbeeld:

```XML
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Outputclaims

De **uitvoerclaims** van een weergavebesturingselement worden niet naar de volgende orchestration-stap verzonden. Ze worden tijdelijk alleen opgeslagen voor de huidige weergavecontrolesessie. Deze tijdelijke claims kunnen worden gedeeld tussen de verschillende acties van hetzelfde weergavebesturingselement.

Als u de uitvoerclaims naar de volgende orchestration-stap wilt opborrelen, gebruikt u de **uitvoerclaims** van het werkelijke zelfverklaarde technische profiel dat verwijst naar dit weergavebesturingselement.

### <a name="display-control-actions"></a>Controleacties weergeven

De **acties** van een weergavebesturingselement zijn procedures die plaatsvinden in de Azure AD B2C back-end wanneer een gebruiker een bepaalde actie uitvoert aan de clientzijde (de browser). Bijvoorbeeld de validaties die moeten worden uitgevoerd wanneer de gebruiker een knop op de pagina selecteert.

Een actie definieert een lijst met **technische validatieprofielen**. Ze worden gebruikt voor het valideren van sommige of alle weergaveclaims van het weergavebesturingselement. Het technische validatieprofiel valideert de gebruikersinvoer en kan een fout naar de gebruiker retourneren. U **ContinueOnError,** **ContinueOnSuccess**en **Randvoorwaarden** in de displaycontrol Action gebruiken, vergelijkbaar met de manier waarop ze worden gebruikt in [technische validatieprofielen](validation-technical-profile.md) in een zelf geclaimd technisch profiel.

In het volgende voorbeeld wordt een code per e-mail of sms gestuurd op basis van de selectie van de **mfaType-claim** door de gebruiker.

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

## <a name="referencing-display-controls"></a>Weergavebesturingselementen verwijzen

Display besturingselementen worden verwezen in de [display claims](self-asserted-technical-profile.md#display-claims) van de [self-asserted technische profiel](self-asserted-technical-profile.md).

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
