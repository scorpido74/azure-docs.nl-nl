---
title: Een OAuth2-technisch profiel definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een OAuth2-technisch profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 86ec7a5745a58546faf6f0ff15d6dc5f452baa88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184040"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een OAuth2-technisch profiel definiëren in een aangepast Azure Directory B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor de OAuth2-protocolidentiteitsprovider. OAuth2 is het primaire protocol voor autorisatie en gedelegeerde verificatie. Zie voor meer informatie het [RFC 6749 The OAuth 2.0 Authorization Framework](https://tools.ietf.org/html/rfc6749). Met een OAuth2 technisch profiel kun je zakendoen met een oauth2-gebaseerde identiteitsprovider, zoals Facebook. Met Federatie met een identiteitsprovider kunnen gebruikers zich aanmelden met hun bestaande sociale of bedrijfsidentiteiten.

## <a name="protocol"></a>Protocol

Het kenmerk **Naam** van het element `OAuth2` **Protocol** moet worden ingesteld op . Het protocol voor het technische profiel Van `OAuth2` **Facebook-OAUTH** is bijvoorbeeld:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Invoerclaims

De elementen **InputClaims** en **InputClaimsTransformations** zijn niet vereist. Maar u aanvullende parameters naar uw identiteitsprovider sturen. In het volgende voorbeeld wordt de parameter `contoso.com` **domain_hint** querytekenreeks met de waarde van de autorisatieaanvraag toegevoegd.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Outputclaims

Het element **OutputClaims** bevat een lijst met claims die zijn geretourneerd door de OAuth2-identiteitsprovider. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in de identiteitsprovider. U ook claims opnemen die niet worden geretourneerd door `DefaultValue` de identiteitsprovider, zolang u het kenmerk instelt.

Het element **OutputClaimsTransformations** kan een verzameling **outputclaimstransformatie-elementen** bevatten die worden gebruikt om de uitvoerclaims te wijzigen of nieuwe claims te genereren.

In het volgende voorbeeld worden de claims weergegeven die zijn geretourneerd door de Facebook-identiteitsprovider:

- De **first_name** claim is toegewezen aan de **givenName claim.**
- De **last_name** claim is toegewezen aan de **achternaam** claim.
- De **claim displayName** zonder naamtoewijzing.
- De **e-mailclaim** zonder naamtoewijzing.

Het technische profiel retourneert ook claims die niet worden geretourneerd door de identiteitsprovider:

- De **identityProvider** claim die de naam van de identiteitsprovider bevat.
- De **claim authenticationSource** met een standaardwaarde van **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| client_id | Ja | De toepassings-id van de identiteitsprovider. |
| IdTokenAudience | Nee | Het publiek van de id_token. Indien opgegeven, controleert Azure AD B2C of het token zich in een claim bevindt die is geretourneerd door de identiteitsprovider en gelijk is aan de opgegeven. |
| authorization_endpoint | Ja | De URL van het autorisatieeindpunt per RFC 6749. |
| AccessTokenEindpunt | Ja | De URL van het tokeneindpunt per RFC 6749. |
| ClaimsEindpunt | Ja | De URL van het eindpunt van de gebruikersinformatie per RFC 6749. |
| AccessTokenResponseFormat | Nee | De indeling van de endpoint-aanroep voor toegangstoken. Facebook vereist bijvoorbeeld een HTTP GET-methode, maar het antwoord op het toegangstoken is in JSON-indeling. |
| AdditionalRequestQueryParameters | Nee | Aanvullende queryparameters voor aanvragen. U bijvoorbeeld aanvullende parameters naar uw identiteitsprovider sturen. U meerdere parameters opnemen met kommascheidinger. |
| ClaimsEndpointAccessTokenName | Nee | De naam van de parameter querytekenreeks voor toegangstoken. De claims van sommige identiteitsproviders ondersteunen GET HTTP-aanvraag. In dit geval wordt het token aan toonder verzonden met behulp van een querytekenreeksparameter in plaats van de autorisatiekop. |
| ClaimsEndpointFormatName | Nee | De naam van de parameter querytekenreeks opmaak. U bijvoorbeeld de naam `format` instellen zoals in `https://api.linkedin.com/v1/people/~?format=json`dit eindpunt voor LinkedIn-claims. |
| ClaimsEndpointFormat | Nee | De waarde van de parameter querytekenreeks opmaak. U bijvoorbeeld de waarde `json` instellen zoals in `https://api.linkedin.com/v1/people/~?format=json`dit eindpunt voor LinkedIn-claims. |
| ProviderName | Nee | De naam van de identiteitsprovider. |
| response_mode | Nee | De methode die de identiteitsprovider gebruikt om het resultaat terug te sturen naar Azure AD B2C. Mogelijke `query`waarden: `form_post` , (standaard) of `fragment`. |
| scope | Nee | De reikwijdte van het verzoek dat is gedefinieerd volgens de oauth2-specificatie van identiteitsprovider. Zoals `openid`, `profile`en `email`. |
| HttpBinding | Nee | De verwachte HTTP-binding met de eindpunten voor toegangstoken en claimstoken. Mogelijke `GET` waarden: `POST`of .  |
| ResponseErrorCodeParamName | Nee | De naam van de parameter die het foutbericht bevat dat is geretourneerd via HTTP 200 (Ok). |
| ExtraParamsInAccessTokenEndpointResponse | Nee | Bevat de extra parameters die door sommige identiteitsproviders kunnen worden geretourneerd in het antwoord van **AccessTokenEndpoint.** Het antwoord van **AccessTokenEndpoint** bevat bijvoorbeeld een `openid`extra parameter, zoals een verplichte parameter naast de access_token in een **queryreeks voor ClaimsEndpoint-aanvragen.** Meerdere parameternamen moeten worden ontsnapt en gescheiden door de komma '', de limiter. |
| ExtraParamsInClaimsEndpointRequest | Nee | Bevat de extra parameters die door sommige identiteitsproviders kunnen worden geretourneerd in de **ClaimEndpoint-aanvraag.** Meerdere parameternamen moeten worden ontsnapt en gescheiden door de komma '', de limiter. |
| IncludeClaimResolvingInClaimsHandling  | Nee | Voor invoer- en uitvoerclaims geeft u aan of [schadeafhandeling](claim-resolver-overview.md) is opgenomen in het technische profiel. Mogelijke waarden: `true` `false`  , of (standaard). Als u een claimresolver in het technische profiel `true`wilt gebruiken, stelt u dit in op . |
| ResolveJsonPathsInJsonTokens  | Nee | Geeft aan of het technische profiel JSON-paden oplost. Mogelijke waarden: `true` `false` , of (standaard). Gebruik deze metagegevens om gegevens uit een genest JSON-element te lezen. Stel in een [OutputClaim](technicalprofiles.md#outputclaims)het `PartnerClaimType` JSON-padelement in dat u wilt uitvoeren. Bijvoorbeeld: `firstName.localized`, `data.0.to.0.email`of .|

## <a name="cryptographic-keys"></a>Cryptografische toetsen

Het element **CryptographicKeys** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| client_secret | Ja | Het klantgeheim van de aanvraag van de identiteitsprovider. De cryptografische sleutel is **response_types** alleen vereist als `code`de response_types metagegevens is ingesteld op . In dit geval voert Azure AD B2C nog een oproep om de autorisatiecode om te wisselen voor een toegangstoken. Als de metagegevens zijn ingesteld op `id_token`, u de cryptografische sleutel weglaten. |

## <a name="redirect-uri"></a>Omleidings-URI

Wanneer u de omleidings-URL van `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`uw identiteitsprovider configureert, voert u . Zorg ervoor dat **u tenant** vervangt door de naam van uw tenant (bijvoorbeeld contosob2c.onmicrosoft.com) en **policyId** door de id van uw beleid (bijvoorbeeld b2c_1a_policy). De omleiding URI moet in alle kleine letters.

Als u het **b2clogin.com-domein** gebruikt in plaats van **login.microsoftonline.com** Zorg ervoor dat u b2clogin.com gebruikt in plaats van login.microsoftonline.com.

Voorbeelden:

- [Google+ toevoegen als oauth2-identiteitsprovider met aangepaste beleidsregels](identity-provider-google-custom.md)













