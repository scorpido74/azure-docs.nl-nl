---
title: Claimresolvers in aangepast beleid
titleSuffix: Azure AD B2C
description: Meer informatie over het gebruik van claimresolvers in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1c4bbd98682d964cfdf72031c7d6cb77cf42a809
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396072"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Informatie over claimresolvers in het aangepaste Azure Directory B2C-beleid

Aangepaste oplossingen voor claimresolvers in azure Active Directory [custom policies](custom-policy-overview.md) B2C (Azure AD B2C) bieden contextinformatie over een autorisatieaanvraag, zoals de beleidsnaam, de correlatie-id van de aanvraag, de taal van de gebruikersinterface en meer.

Als u een claimresolver wilt gebruiken in een invoer- of uitvoerclaim, definieert u een tekenreeks **ClaimType**onder het element [ClaimSchema](claimsschema.md) en stelt u de **standaardwaarde** in op de claimresolver in het element invoer- of uitvoerclaim. Azure AD B2C leest de waarde van de claimresolver en gebruikt de waarde in het technische profiel.

In het volgende voorbeeld wordt `correlationId` een claimtype met `string`de naam gedefinieerd met een **DataType** van .

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

Breng in het technische profiel de claimresolver in kaart met het claimtype. Azure AD B2C vult de waarde `{Context:CorrelationId}` van de `correlationId` claimresolver in de claim en stuurt de claim naar het technische profiel.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Typen claimresolver

In de volgende secties worden beschikbare claimresolvers vermeld.

### <a name="culture"></a>Cultuur

| Claim | Beschrijving | Voorbeeld |
| ----- | ----------- | --------|
| {Cultuur:LanguageName} | De twee letter ISO-code voor de taal. | nl |
| {Cultuur:LCID}   | De LCID van taalcode. | 1043 |
| {Cultuur:RegioNaam} | De tweeletterig ISO-code voor de regio. | VS |
| {Cultuur:RFC5646} | De RFC5646 taalcode. | nl-NL |

### <a name="policy"></a>Beleid

| Claim | Beschrijving | Voorbeeld |
| ----- | ----------- | --------|
| {Beleid:PolicyId} | De naam van het relying party-beleid. | B2C_1A_signup_signin |
| {Beleid:RelyingPartyTenantId} | De tenant-ID van het beleid van de relying party. | your-tenant.onmicrosoft.com |
| {Beleid:TenantObjectId} | De tenantobject-id van het beleid van de relying party. | 00000000-0000-0000-0000-000000000000 |
| {Beleid:TrustFrameworkTenantId} | De tenant-id van het vertrouwenskader. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Claim | Beschrijving | Voorbeeld |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |De `acr_values` parameter querytekenreeks. | N.v.t. |
| {OIDC:ClientId} |De `client_id` parameter querytekenreeks. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |De `domain_hint` parameter querytekenreeks. | facebook.com |
| {OIDC:LoginHint} |  De `login_hint` parameter querytekenreeks. | someone@contoso.com |
| {OIDC:MaxAge} | De `max_age`. | N.v.t. |
| {OIDC:Nonce} |De `Nonce` parameter querytekenreeks. | standaardNonce |
| {OIDC:Wachtwoord}| Het wachtwoordreferenties van de [broneigenaar stromen door](ropc-custom.md) het wachtwoord van de gebruiker.| wachtwoord1| 
| {OIDC:Prompt} | De `prompt` parameter querytekenreeks. | aanmelding |
| {OIDC:RedirectUri} |De `redirect_uri` parameter querytekenreeks. | https://jwt.ms |
| {OIDC:Resource} |De `resource` parameter querytekenreeks. | N.v.t. |
| {OIDC:Scope} |De `scope` parameter querytekenreeks. | Openid |
| {OIDC:Gebruikersnaam}| De wachtwoordreferenties van de [broneigenaar stromen door](ropc-custom.md) de gebruikersnaam van de gebruiker.| emily@contoso.com| 

### <a name="context"></a>Context

| Claim | Beschrijving | Voorbeeld |
| ----- | ----------- | --------|
| {Context:BuildNumber} | De Identity Experience Framework-versie (buildnummer).  | 1.0.507.0 |
| {Context:CorrelationId} | De correlatie-ID.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeinutc} |De datumtijd in UTC.  | 10-10-2018 12:00:00 uur |
| {Context:DeploymentMode} |De beleidsimplementatiemodus.  | Productie |
| {Context:IPAddress} | Het IP-adres van de gebruiker. | 11.111.111.11 |
| {Context:KMSI} | Hiermee geeft u aan [of Houd me aangemeld,](custom-policy-keep-me-signed-in.md) is ingeschakeld. |  waar |

### <a name="non-protocol-parameters"></a>Niet-protocolparameters

Elke parameternaam die is opgenomen als onderdeel van een OIDC- of OAuth2-aanvraag kan worden toegewezen aan een claim in de gebruikersreis. De aanvraag van de toepassing kan bijvoorbeeld een querytekenreeksparameter bevatten met een naam van `app_session`, `loyalty_number`of een aangepaste querytekenreeks.

| Claim | Beschrijving | Voorbeeld |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Een parameter querytekenreeks. | Hawaii |
| {OAUTH-KV:app_session} | Een parameter querytekenreeks. | A3C5R |
| {OAUTH-KV:loyalty_number} | Een parameter querytekenreeks. | 1234 |
| {OAUTH-KV:elke aangepaste querytekenreeks} | Een parameter querytekenreeks. | N.v.t. |

### <a name="oauth2"></a>OAuth2

| Claim | Beschrijving | Voorbeeld |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | Het toegangstoken. | N.v.t. |


### <a name="saml"></a>SAML

| Claim | Beschrijving | Voorbeeld |
| ----- | ----------- | --------|
| {SAML:AuthnContextClassReferences} | De `AuthnContextClassRef` elementwaarde, van de SAML-aanvraag. | urn:oase:namen:tc:SAML:2.0:ac:klassen:PasswordProtectedTransport |
| {SAML:NameIdPolicyFormat} | Het `Format` kenmerk, `NameIDPolicy` van het element van de SAML-aanvraag. | urn:oase:namen:tc:SAML:1.1:nameid-formaat:emailAddress |
| {SAML:Uitgever} |  De SAML-elementwaarde `Issuer` van de SAML-aanvraag.| `https://contoso.com` |
| {SAML:Create toestaan} | De `AllowCreate` kenmerkwaarde, `NameIDPolicy` van het element van de SAML-aanvraag. | True |
| {SAML:ForceAuthn} | De `ForceAuthN` kenmerkwaarde, `AuthnRequest` van het element van de SAML-aanvraag. | True |
| {SAML:ProviderName} | De `ProviderName` kenmerkwaarde, `AuthnRequest` van het element van de SAML-aanvraag.| Contoso.com |

## <a name="using-claim-resolvers"></a>Claimresolvers gebruiken

U claimresolvers gebruiken met de volgende elementen:

| Item | Element | Instellingen |
| ----- | ----------------------- | --------|
|Technisch profiel Application Insights |`InputClaim` | |
|[Technisch azure Directory](active-directory-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|[OAuth2](oauth2-technical-profile.md) technisch profiel| `InputClaim`, `OutputClaim`| 1, 2|
|Technisch profiel [van OpenID Connect](openid-connect-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|[Technische profiel van claimtransformatie](claims-transformation-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|[RESTful provider](restful-technical-profile.md) technisch profiel| `InputClaim`| 1, 2|
|[SAML2](saml-technical-profile.md) technisch profiel| `OutputClaim`| 1, 2|
|[Zelf-beweerde](self-asserted-technical-profile.md) technisch profiel| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinitie](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|Het technische profiel [van RelyingParty](relyingparty.md#technicalprofile)| `OutputClaim`| 2 |

Instellingen:
1. De `IncludeClaimResolvingInClaimsHandling` metagegevens `true`moeten worden ingesteld op .
1. Het kenmerk `AlwaysUseDefaultValue` invoer- of uitvoerclaims moet worden ingesteld op `true`.

## <a name="claim-resolvers-samples"></a>Voorbeelden van claimresolvers

### <a name="restful-technical-profile"></a>RESTful technisch profiel

In een technisch profiel [van RESTful](restful-technical-profile.md) u de gebruikerstaal, de beleidsnaam, het bereik en de client-id verzenden. Op basis van de claims kan de REST API aangepaste bedrijfslogica uitvoeren en zo nodig een gelokaliseerd foutbericht verhogen.

In het volgende voorbeeld wordt een RESTful technisch profiel met dit scenario weergegeven:

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:Scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Direct aanmelden

Met behulp van claimresolvers u de aanmeldingsnaam of directe aanmelding vooraf invullen bij een specifieke aanbieder van sociale identiteit, zoals Facebook, LinkedIn of een Microsoft-account. Zie [Directe aanmelding instellen met Azure Active Directory B2C](direct-signin.md)voor meer informatie.

### <a name="dynamic-ui-customization"></a>Dynamische aanpassing van de gebruikersinterface

Met Azure AD B2C u querytekenreeksparameters doorgeven aan uw eindpunten voor html-inhoudsdefinitie om de pagina-inhoud dynamisch weer te geven. Met deze functie u bijvoorbeeld de achtergrondafbeelding op de aanmeldings- of aanmeldingspagina van Azure AD B2C wijzigen op basis van een aangepaste parameter die u vanaf uw web- of mobiele toepassing passeert. Zie [De gebruikersinterface dynamisch configureren met behulp van aangepaste beleidsregels in Azure Active Directory B2C](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri)voor meer informatie. U uw HTML-pagina ook lokaliseren op basis van een taalparameter of u de inhoud wijzigen op basis van de client-id.

In het volgende voorbeeld wordt de parameter querytekenreeks `Hawaii`met de naam **campaignId** doorgegeven met een waarde van , een **taalcode** van `en-US`en **app** die de client-id vertegenwoordigt:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Als gevolg hiervan stuurt Azure AD B2C de bovenstaande parameters naar de HTML-inhoudspagina:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Inhoudsdefinitie

In een [ContentDefinition](contentdefinitions.md) `LoadUri`u claimresolvers verzenden om inhoud van verschillende plaatsen te halen, op basis van de gebruikte parameters.

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Technisch profiel Application Insights

Met Azure Application Insights en claimresolvers u inzicht krijgen in het gedrag van gebruikers. In het technische profiel Application Insights verzendt u invoerclaims die blijven bestaan naar Azure Application Insights. Zie [Gebruikersgedrag in Azure AD B2C-ritten bijhouden met Behulp van Toepassingsinzichten](analytics-with-application-insights.md)voor meer informatie. In het volgende voorbeeld worden de beleids-id, correlatie-id, taal en de client-id naar Azure Application Insights verstuurt.

```XML
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

### <a name="relying-party-policy"></a>Vertrouwenpartijbeleid

In een technisch profiel [van relying party-beleid](relyingparty.md) u de tenant-id of correlatie-ID naar de toepassing van de relying party binnen de JWT sturen.

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
