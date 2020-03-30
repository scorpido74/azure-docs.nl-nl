---
title: Eén aanmeldingssessiebeheer met aangepaste beleidsregels
titleSuffix: Azure AD B2C
description: Meer informatie over het beheren van SSO-sessies met aangepaste beleidsregels in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80cf0d101a29de7fca9d4dd36e188a500d35e290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246030"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Eén aanmeldingssessiebeheer in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Met eenmalige aanmeldingssessiebeheer (SSO) in Azure Active Directory B2C (Azure AD B2C) kan een beheerder de interactie met een gebruiker beheren nadat de gebruiker zich al heeft geverifieerd. De beheerder kan bijvoorbeeld bepalen of de selectie van identiteitsproviders wordt weergegeven of dat lokale accountgegevens opnieuw moeten worden ingevoerd. In dit artikel wordt beschreven hoe u de SSO-instellingen voor Azure AD B2C configureert.

SSO sessiemanagement bestaat uit twee delen. De eerste gaat over de interacties van de gebruiker rechtstreeks met Azure AD B2C en de andere gaat over de interacties van de gebruiker met externe partijen zoals Facebook. Azure AD B2C overschrijft of omzeilt geen SSO-sessies die mogelijk door externe partijen worden gehouden. In plaats daarvan wordt de route via Azure AD B2C om bij de externe partij te komen "onthouden", waardoor de gebruiker niet opnieuw hoeft te worden gevraagd om zijn sociale of zakelijke identiteitsprovider te selecteren. De uiteindelijke SSO-beslissing blijft bij de externe partij.

SSO-sessiebeheer gebruikt dezelfde semantiek als elk ander technisch profiel in aangepast beleid. Wanneer een orkestratiestap wordt uitgevoerd, wordt het technische profiel `UseTechnicalProfileForSessionManagement` dat aan de stap is gekoppeld, opgevraagd voor een referentie. Als er een bestaat, wordt de sso-sessieprovider waarnaar wordt verwezen vervolgens gecontroleerd om te zien of de gebruiker een sessiedeelnemer is. Als dit het zo is, wordt de SSO-sessieprovider gebruikt om de sessie opnieuw te vullen. Wanneer de uitvoering van een orchestration-stap is voltooid, wordt de provider ook gebruikt om informatie in de sessie op te slaan als een SSO-sessieprovider is opgegeven.

Azure AD B2C heeft een aantal SSO-sessieproviders gedefinieerd die kunnen worden gebruikt:

* NoopSSOSessionProvider
* DefaultsSOSessionProvider
* ExternalloginsSOSessionProvider
* SamlSSOSessionProvider

SSO management klassen worden `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` gespecificeerd met behulp van het element van een technisch profiel.

## <a name="input-claims"></a>Invoerclaims

Het `InputClaims` element is leeg of afwezig.

## <a name="persisted-claims"></a>Aanhoudende claims

Claims die moeten worden teruggestuurd naar de toepassing of die in volgende stappen door randvoorwaarden moeten worden gebruikt, moeten in de sessie worden opgeslagen of worden aangevuld met een lezing uit het profiel van de gebruiker in de directory. Het gebruik van aanhoudende claims zorgt ervoor dat uw verificatieritten niet mislukken bij ontbrekende claims. Als u claims in de `<PersistedClaims>` sessie wilt toevoegen, gebruikt u het element van het technische profiel. Wanneer de provider wordt gebruikt om de sessie opnieuw te bevolken, worden de aanhoudende claims toegevoegd aan de claimzak.

## <a name="output-claims"></a>Outputclaims

Het `<OutputClaims>` wordt gebruikt voor het ophalen van claims uit de sessie.

## <a name="session-providers"></a>Sessieproviders

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Zoals de naam al aangeeft, doet deze provider niets. Deze provider kan worden gebruikt voor het onderdrukken van SSO-gedrag voor een specifiek technisch profiel. Het `SM-Noop` volgende technische profiel is opgenomen in het [aangepaste beleidsstartpakket.](custom-policy-get-started.md#custom-policy-starter-pack)

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultsSOSessionProvider

Deze provider kan worden gebruikt voor het opslaan van claims in een sessie. Deze provider wordt meestal verwezen in een technisch profiel dat wordt gebruikt voor het beheren van lokale accounts. Het `SM-AAD` volgende technische profiel is opgenomen in het [aangepaste beleidsstartpakket.](custom-policy-get-started.md#custom-policy-starter-pack)

```XML
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

Het `SM-MFA` volgende technische profiel is opgenomen in het [aangepaste beleidsstartpakket.](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa` Dit technische profiel beheert de multi-factor authenticatiesessie.

```XML
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalloginsSOSessionProvider

Deze provider wordt gebruikt om het scherm 'kiezen identiteitsprovider' te onderdrukken. Het wordt meestal verwezen in een technisch profiel geconfigureerd voor een externe identiteit provider, zoals Facebook. Het `SM-SocialLogin` volgende technische profiel is opgenomen in het [aangepaste beleidsstartpakket.](custom-policy-get-started.md#custom-policy-starter-pack)

```XML
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="AlwaysFetchClaimsFromProvider">true</Item>
  </Metadata>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving|
| --- | --- | --- |
| AlwaysFetchclaimsFromProvider | Nee | Momenteel niet gebruikt, kan worden genegeerd. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Deze provider wordt gebruikt voor het beheer van de Azure AD B2C SAML-sessies tussen een relying party-toepassing of een federatieve SAML-identiteitsprovider. Wanneer u de SSO-provider gebruikt voor het `RegisterServiceProviders` opslaan van `false`een SAML-identiteitsprovidersessie, moet deze worden ingesteld op . Het `SM-Saml-idp` volgende technische profiel wordt gebruikt door het [SAML technisch profiel](saml-technical-profile.md).

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Wanneer u de provider gebruikt voor het opslaan `RegisterServiceProviders` van `true`de B2C SAML-sessie, moet de moet worden ingesteld op . SAML-sessieafmelding `SessionIndex` vereist `NameID` de en te voltooien.

Het `SM-Saml-idp` volgende technische profiel wordt gebruikt door [saml-emittent technisch profiel](saml-issuer-technical-profile.md)

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving|
| --- | --- | --- |
| SessieIndex opnemen | Nee | Momenteel niet gebruikt, kan worden genegeerd.|
| RegisterServiceProviders | Nee | Geeft aan dat de provider alle SAML-serviceproviders moet registreren die een bewering hebben gedaan. Mogelijke waarden: `true` (standaard) of `false`.|



