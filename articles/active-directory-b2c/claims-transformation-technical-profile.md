---
title: Een technisch profiel voor claimtransformatie definiëren
titleSuffix: Azure AD B2C
description: Definieer een technisch profiel voor claimtransformatie in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84c1cf798e88e4067da8a495c1591143d2ee1bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189783"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een technisch profiel voor claimtransformatie definiëren in een aangepast Azure Directory B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Met een technisch profiel voor claimtransformatie u uitvoerclaimstransformaties aanroepen om claimwaarden te manipuleren, claims te valideren of standaardwaarden in te stellen voor een reeks uitvoerclaims.

## <a name="protocol"></a>Protocol

Het kenmerk **Naam** van het element `Proprietary` **Protocol** moet worden ingesteld op . Het **handlerkenmerk** moet de volledig gekwalificeerde naam bevatten van de protocolhandlerassemblage `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`die wordt gebruikt door Azure AD B2C: .

In het volgende voorbeeld ziet u een technisch profiel voor claimtransformatie:

```XML
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Outputclaims

Het element **OutputClaims** is verplicht. U dient ten minste één uitvoerclaim op te geven die door het technische profiel wordt geretourneerd. In het volgende voorbeeld ziet u hoe u standaardwaarden instelt in de uitvoerclaims:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Transformaties van uitvoerclaims

Het element **OutputClaimsTransformations** kan een verzameling **outputclaimstransformatie-elementen** bevatten die worden gebruikt om claims te wijzigen of nieuwe te genereren. Het volgende technische profiel roept de **RemoveAlternativeSecurityIdByIdentityProvider** claims transformatie. Deze claims transformatie verwijdert een sociale identificatie uit de verzameling van **AlternativeSecurityIds**. De output claims van dit technische profiel zijn `facebook.com` **identityProvider2**, die is ingesteld op , en **AlternativeSecurityIds**, die de lijst van sociale identiteiten die zijn gekoppeld aan deze gebruiker bevat na facebook.com identiteit is verwijderd.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider"
TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="IdentityProvider2"
TransformationClaimType="identityProvider" />
      <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
...
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider2" DefaultValue="facebook.com" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="RemoveAlternativeSecurityIdByIdentityProvider" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Met het technische profiel voor claimtransformatie u een claimtransformatie uitvoeren van de orchestration-stap van elke gebruikersreis. In het volgende voorbeeld roept de orchestration-stap een van de koppelingsprofielen aan, zoals **UnLink-Facebook-OAUTH**. Dit technische profiel noemt de claims transformatie technisch profiel **RemoveAlternativeSecurityIdByIdentityProvider**, die een nieuwe **AlternativeSecurityIds2** claim die de lijst van gebruikers sociale identiteiten bevat genereert, terwijl het verwijderen van de Facebook-identiteit uit de collecties genereert.

```XML
<UserJourney Id="AccountUnLink">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="UnLinkFacebookExchange" TechnicalProfileReferenceId="UnLink-Facebook-OAUTH" />
        <ClaimsExchange Id="UnLinkMicrosoftExchange" TechnicalProfileReferenceId="UnLink-Microsoft-OAUTH" />
        <ClaimsExchange Id="UnLinkGitHubExchange" TechnicalProfileReferenceId="UnLink-GitHub-OAUTH" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ...
  </OrchestrationSteps>
</UserJourney>
```

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| IncludeClaimResolvingInClaimsHandling  | Nee | Voor invoer- en uitvoerclaims geeft u aan of [schadeafhandeling](claim-resolver-overview.md) is opgenomen in het technische profiel. Mogelijke waarden: `true` `false`  , of (standaard). Als u een claimresolver in het technische profiel `true`wilt gebruiken, stelt u dit in op . |

## <a name="use-a-validation-technical-profile"></a>Een validatietechnisch profiel gebruiken

Een technisch profiel voor schadetransformatie kan worden gebruikt om informatie te valideren. In het volgende voorbeeld vraagt het [zelf geclaimde technische profiel](self-asserted-technical-profile.md) met de naam **LocalAccountSignUpWithLogonEmail** de gebruiker om de e-mail twee keer in te voeren en roept vervolgens het [validatietechnische profiel](validation-technical-profile.md) met de naam **Validate-Email** aan om de e-mails te valideren. Het technische profiel **Validate-Email** roept de claimtransformatie **AssertEmailAreEqual** op om de twee claims **e-mail** en **e-mailRepeat**te vergelijken en een uitzondering te maken als ze niet gelijk zijn volgens de opgegeven vergelijking.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="AssertEmailAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="emailRepeat" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    </InputParameters>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Het technische profiel van de claimstransformatie noemt de **AssertEmailAreEqual-claimtransformatie,** die beweert dat e-mails van de gebruiker hetzelfde zijn.

```XML
<TechnicalProfile Id="Validate-Email">
  <DisplayName>Unlink Facebook</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailRepeat" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAreEqual" />
  </OutputClaimsTransformations>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Een zelfgeclaimd technisch profiel kan het validatietechnische profiel aanroepen en het foutbericht weergeven zoals opgegeven in de **metagegevens van UserMessageIfClaimsTransformationStringsAreNotEqual.**

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>User ID signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    ...
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">The email addresses you provided are not the same</Item>
  </Metadata>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="emailRepeat" />
    ...
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="Validate-Email" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```
