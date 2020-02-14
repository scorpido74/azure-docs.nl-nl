---
title: Claim resolvers in aangepast beleid
titleSuffix: Azure AD B2C
description: Meer informatie over het gebruik van claim resolvers in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e3a80628e5729813e1d405e58ecb623925b63076
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193376"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Over claim resolvers in Azure Active Directory B2C aangepast beleid

Claim resolvers in Azure Active Directory B2C (Azure AD B2C) [aangepaste beleids regels](custom-policy-overview.md) bieden context informatie over een autorisatie aanvraag, zoals de beleids naam, de correlatie-id van de aanvraag, de taal van de gebruikers interface en nog veel meer.

Als u een claim resolver wilt gebruiken in een invoer-of uitvoer claim, definieert u een teken reeks **claim**type onder het element [ClaimsSchema](claimsschema.md) en stelt u de **DefaultValue** in op de claim resolver in het element input of output claim. Azure AD B2C leest de waarde van de claim resolver en gebruikt de waarde in het technische profiel.

In het volgende voor beeld wordt een claim type met de naam `correlationId` gedefinieerd met het **gegevens type** `string`.

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

Wijs in het technische profiel de claim resolver toe aan het claim type. Azure AD B2C vult de waarde van de claim resolver `{Context:CorrelationId}` in de claim `correlationId` en verzendt de claim naar het technische profiel.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Typen claim conflicten

De volgende secties bevatten een lijst met beschik bare claim resolvers.

### <a name="culture"></a>Culture

| Claim | Beschrijving | Voorbeeld |
| ----- | ----------- | --------|
| {Culture:LanguageName} | De ISO-code van twee letters voor de taal. | nl |
| {Culture: LCID}   | De LCID van de taal code. | 1043 |
| {Culture: regionaam} | De ISO-code van twee tekens voor de regio. | VS |
| {Culture: RFC5646} | De RFC5646-taal code. | nl-NL |

### <a name="policy"></a>Beleid

| Claim | Beschrijving | Voorbeeld |
| ----- | ----------- | --------|
| {Beleid: PolicyId} | De naam van het Relying Party-beleid. | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | De Tenant-ID van het Relying Party-beleid. | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | De Tenant-object-ID van het Relying Party-beleid. | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | De Tenant-ID van het vertrouwens raamwerk. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Claim | Beschrijving | Voorbeeld |
| ----- | ----------- | --------|
| {OIDC: AuthenticationContextReferences} |De query reeks parameter `acr_values`. | N.v.t. |
| {OIDC: ClientId} |De query reeks parameter `client_id`. | 00000000-0000-0000-0000-000000000000 |
| {OIDC: DomainHint} |De query reeks parameter `domain_hint`. | facebook.com |
| {OIDC: LoginHint} |  De query reeks parameter `login_hint`. | someone@contoso.com |
| {OIDC: MaxAge} | Het `max_age`. | N.v.t. |
| {OIDC: nonce} |De query reeks parameter `Nonce`. | defaultNonce |
| {OIDC: prompt} | De query reeks parameter `prompt`. | aanmelding |
| {OIDC: resource} |De query reeks parameter `resource`. | N.v.t. |
| {OIDC: bereik} |De query reeks parameter `scope`. | OpenID Connect |

### <a name="context"></a>Context

| Claim | Beschrijving | Voorbeeld |
| ----- | ----------- | --------|
| {Context: BuildNumber} | De Framework-versie van de identiteits ervaring (build-nummer).  | 1.0.507.0 |
| {Context:CorrelationId} | De correlatie-ID.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |De datum en tijd in UTC.  | 10/10/2018 12:00:00 UUR |
| {Context:DeploymentMode} |De implementatie modus voor beleid.  | Productie |
| {Context:IPAddress} | Het IP-adres van de gebruiker. | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>Niet-protocol parameters

Een parameter naam die deel uitmaakt van een OIDC-of OAuth2-aanvraag kan worden toegewezen aan een claim in de gebruikers traject. De aanvraag van de toepassing kan bijvoorbeeld een query teken reeks parameter bevatten met de naam `app_session`, `loyalty_number`of een aangepaste query teken reeks.

| Claim | Beschrijving | Voorbeeld |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Een query reeks parameter. | Hawaï |
| {OAUTH-KV: app_session} | Een query reeks parameter. | A3C5R |
| {OAUTH-KV: loyalty_number} | Een query reeks parameter. | 1234 |
| {OAUTH-KV: elke aangepaste query reeks} | Een query reeks parameter. | N.v.t. |

### <a name="oauth2"></a>OAuth2

| Claim | Beschrijving | Voorbeeld |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | Het toegangs token. | N.v.t. |

## <a name="using-claim-resolvers"></a>Claim resolvers gebruiken 

U kunt met behulp van claim resolvers gebruikmaken van de volgende elementen: 

| Item | Element | Instellingen |
| ----- | ----------------------- | --------|
|Technisch profiel Application Insights |`InputClaim` | |
|Technisch profiel [Azure Active Directory](active-directory-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Technisch profiel voor [OAuth2](oauth2-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|[OpenID Connect Connect](openid-connect-technical-profile.md) technische profiel| `InputClaim`, `OutputClaim`| 1, 2|
|Technisch profiel voor [trans formatie van claims](claims-transformation-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Technisch profiel van de [Rest-provider](restful-technical-profile.md)| `InputClaim`| 1, 2|
|Technisch profiel voor [SAML2](saml-technical-profile.md)| `OutputClaim`| 1, 2|
|Een [zelf-bevestigd](self-asserted-technical-profile.md) technisch profiel| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|Technisch profiel voor [RelyingParty](relyingparty.md#technicalprofile)| `OutputClaim`| 2 |

Instellingen: 
1. De `IncludeClaimResolvingInClaimsHandling` meta gegevens moeten worden ingesteld op `true`
1. Het kenmerk voor de invoer-of uitvoer claims `AlwaysUseDefaultValue` moet worden ingesteld op `true`

## <a name="how-to-use-claim-resolvers"></a>Claim resolvers gebruiken

### <a name="restful-technical-profile"></a>Onderliggend technisch profiel

In een [](restful-technical-profile.md) onderliggend technisch profiel wilt u mogelijk de gebruikers taal, de beleids naam, het bereik en de client-id verzenden. Op basis van deze claims kan de REST API aangepaste bedrijfs logica uitvoeren en zo nodig een gelokaliseerd fout bericht genereren.

In het volgende voor beeld wordt een onderliggend technisch profiel weer gegeven:

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Direct aanmelden

Met behulp van claim resolvers kunt u de aanmeldings naam of de directe aanmelding vooraf invullen bij een specifieke ID-provider voor sociale netwerken, zoals Facebook, LinkedIn of een Microsoft-account. Zie voor meer informatie [instellen van direct aanmelden met Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Dynamische gebruikers interface aanpassen

Met Azure AD B2C kunt u query reeks parameters door geven aan uw HTML-inhouds definitie-eind punten zodat u de pagina-inhoud dynamisch kunt weer geven. U kunt bijvoorbeeld de achtergrond afbeelding wijzigen op de Azure AD B2C registratie-of aanmeldings pagina op basis van een aangepaste para meter die u doorgeeft vanuit uw web-of mobiele toepassing. Zie voor meer informatie [de gebruikers interface dynamisch configureren met behulp van aangepast beleid in azure Active Directory B2C](custom-policy-ui-customization-dynamic.md). U kunt ook uw HTML-pagina lokaliseren op basis van een taal parameter, of u kunt de inhoud wijzigen op basis van de client-ID.

In het volgende voor beeld wordt in de query reeks een para meter met de naam **campaignId** weer gegeven met de waarde `hawaii`, een **taal** code van `en-US`en een **app** die de client-id vertegenwoordigt:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Als resultaat Azure AD B2C worden de bovenstaande para meters naar de pagina HTML-inhoud verzonden:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="application-insights-technical-profile"></a>Technisch profiel Application Insights

Met Azure-toepassing inzichten en claim resolvers kunt u inzicht krijgen in het gedrag van gebruikers. In het Application Insights technische profiel verzendt u invoer claims die persistent zijn voor Azure-toepassing inzichten. Zie voor meer informatie [gebruikers gedrag bijhouden in azure AD B2C trajecten met behulp van Application Insights](analytics-with-application-insights.md). In het volgende voor beeld worden de beleids-ID, de correlatie-ID, de taal en de client-ID naar Azure-toepassing inzichten verzonden.

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
