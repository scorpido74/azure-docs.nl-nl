---
title: Beheer van sessie voor eenmalige aanmelding met aangepaste beleids regels
titleSuffix: Azure AD B2C
description: Meer informatie over het beheren van SSO-sessies met aangepaste beleids regels in Azure AD B2C.
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
ms.sourcegitcommit: 3616b42a0d6bbc31b965995d861930e53d2cf0d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933018"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Sessie beheer voor eenmalige aanmelding in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Met eenmalige aanmelding (SSO)-sessie beheer in Azure Active Directory B2C (Azure AD B2C) kan een beheerder de interactie met een gebruiker beheren nadat de gebruiker al is geverifieerd. De beheerder kan bijvoorbeeld bepalen of de selectie van id-providers wordt weer gegeven of dat de gegevens van het lokale account opnieuw moeten worden ingevoerd. In dit artikel wordt beschreven hoe u de SSO-instellingen voor Azure AD B2C kunt configureren.

SSO-sessie beheer bestaat uit twee delen. De eerste behandelt de interactie van de gebruiker rechtstreeks met Azure AD B2C en de andere deals met de interactie van de gebruiker met externe partijen, zoals Facebook. Azure AD B2C geen SSO-sessies die mogelijk door externe partijen worden bewaard, overschrijft of overs Laan. In plaats daarvan wordt de route door Azure AD B2C om naar de externe partij te gaan ' onthouden ', zodat de gebruiker niet opnieuw wordt gevraagd om hun sociale of zakelijke ID-provider te selecteren. De ultieme SSO-beslissing blijft bij de externe partij.

SSO-sessie beheer maakt gebruik van dezelfde semantiek als andere technische profielen in aangepast beleid. Wanneer een Orchestration-stap wordt uitgevoerd, wordt het technische profiel dat is gekoppeld aan de stap, opgevraagd voor een `UseTechnicalProfileForSessionManagement` verwijzing. Als dat het geval is, wordt de SSO-sessie provider waarnaar wordt verwezen, gecontroleerd om te zien of de gebruiker een deel nemer voor de sessie is. Als dit het geval is, wordt de SSO-sessie provider gebruikt om de sessie opnieuw in te vullen. Op dezelfde manier wordt de provider gebruikt voor het opslaan van gegevens in de sessie als er een SSO-sessie provider is opgegeven, wanneer de uitvoering van een Orchestration-stap is voltooid.

Azure AD B2C heeft een aantal SSO-sessie providers gedefinieerd die kunnen worden gebruikt:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

SSO-beheer klassen worden opgegeven met behulp van het `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />`-element van een technisch profiel.

## <a name="input-claims"></a>Invoer claims

Het `InputClaims`-element is leeg of afwezig.

## <a name="persisted-claims"></a>Permanente claims

Claims die moeten worden geretourneerd naar de toepassing of worden gebruikt door de voor waarden in de volgende stappen, moeten worden opgeslagen in de sessie of worden uitgebreid met een lees bewerking vanuit het profiel van de gebruiker in de Directory. Door permanente claims te gebruiken zorgt u ervoor dat uw verificatie trajecten niet mislukken voor ontbrekende claims. Gebruik het `<PersistedClaims>`-element van het technische profiel om claims toe te voegen aan de sessie. Wanneer de provider wordt gebruikt om de sessie opnieuw in te vullen, worden de permanente claims toegevoegd aan de claim verzameling.

## <a name="output-claims"></a>Uitvoer claims

De `<OutputClaims>` wordt gebruikt voor het ophalen van claims uit de sessie.

## <a name="session-providers"></a>Sessie providers

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Zoals de naam bepaalt, heeft deze provider niets. Deze provider kan worden gebruikt om SSO-gedrag voor een specifiek technisch profiel te onderdrukken. Het volgende `SM-Noop` technische profiel is opgenomen in het [aangepaste beleids Starter Pack](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Deze provider kan worden gebruikt voor het opslaan van claims in een sessie. Er wordt doorgaans verwezen naar deze provider in een technisch profiel dat wordt gebruikt voor het beheren van lokale accounts. Het volgende `SM-AAD` technische profiel is opgenomen in het [aangepaste beleids Starter Pack](custom-policy-get-started.md#custom-policy-starter-pack).

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

Het volgende `SM-MFA` technische profiel is opgenomen in het [aangepaste beleid voor het Starter pack](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`. Dit technische profiel beheert de multi-factor Authentication-sessie.

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

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Deze provider wordt gebruikt om het scherm ' ID-provider kiezen ' te onderdrukken. Er wordt meestal verwezen naar een technisch profiel dat is geconfigureerd voor een externe ID-provider, zoals Facebook. Het volgende `SM-SocialLogin` technische profiel is opgenomen in het [aangepaste beleids Starter Pack](custom-policy-get-started.md#custom-policy-starter-pack).

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
| AlwaysFetchClaimsFromProvider | Nee | Momenteel niet gebruikt, kan worden genegeerd. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Deze provider wordt gebruikt voor het beheren van de Azure AD B2C SAML-sessies tussen een Relying Party toepassing of een federatieve SAML-ID-provider. Wanneer u de SSO-provider gebruikt voor het opslaan van een SAML ID-provider sessie, moet de `RegisterServiceProviders` worden ingesteld op `false`. Het volgende `SM-Saml-idp` technische profiel wordt gebruikt door het [technische SAML-profiel](saml-technical-profile.md).

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Wanneer u de provider gebruikt voor het opslaan van de B2C SAML-sessie, moet de `RegisterServiceProviders` worden ingesteld op `true`. Voor het afmelden van SAML-sessies moet `SessionIndex` en `NameID` zijn voltooid.

Het volgende `SM-Saml-idp` technische profiel wordt gebruikt door het [SAML-producttechnische profiel](saml-issuer-technical-profile.md)

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving|
| --- | --- | --- |
| IncludeSessionIndex | Nee | Momenteel niet gebruikt, kan worden genegeerd.|
| RegisterServiceProviders | Nee | Geeft aan dat de provider alle SAML-service providers moet registreren waarvoor een bevestiging is verleend. Mogelijke waarden: `true` (standaard) of `false`.|



