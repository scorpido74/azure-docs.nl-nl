---
title: Een OAuth2 technisch profiel definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een OAuth2 technisch profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f9db8fd0865b7dba869795526cf6b68f4ad3ffb9
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585778"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een OAuth2 technisch profiel definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor de ID-provider van het OAuth2-protocol. OAuth2 is het primaire protocol voor autorisatie en gedelegeerde authenticatie. Zie [RFC 6749 het OAuth 2,0 Authorization Framework](https://tools.ietf.org/html/rfc6749)(Engelstalig) voor meer informatie. Met een OAuth2-technisch profiel kunt u communiceren met een OAuth2-gebaseerde ID-provider, zoals Facebook. Met federeren met een id-provider kunnen gebruikers zich aanmelden met hun bestaande sociale of bedrijfs identiteiten.

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `OAuth2`. Het protocol voor het **Facebook-OAUTH-** technische profiel is bijvoorbeeld `OAuth2`:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Invoer claims

De **InputClaims** -en **InputClaimsTransformations** -elementen zijn niet vereist. Maar u wilt mogelijk extra para meters naar uw ID-provider verzenden. In het volgende voor beeld wordt de **domain_hint** query teken reeks parameter met de waarde `contoso.com` aan de autorisatie aanvraag toegevoegd.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Uitvoer claims

Het **OutputClaims** -element bevat een lijst met claims die zijn geretourneerd door de OAuth2-ID-provider. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in de ID-provider. U kunt ook claims toevoegen die niet worden geretourneerd door de ID-provider zolang u het kenmerk `DefaultValue` hebt ingesteld.

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

In het volgende voor beeld worden de claims weer gegeven die zijn geretourneerd door de Facebook-ID-provider:

- De **first_name** claim wordt toegewezen aan de claim voor de **OpgegevenNaam** .
- De **last_name** claim wordt toegewezen aan de claim **Achternaam** .
- De **DisplayName** claim zonder naam toewijzing.
- De **e-mail** claim zonder naam toewijzing.

Het technische profiel retourneert ook claims die niet worden geretourneerd door de ID-provider:

- De claim **Identity provider** die de naam van de ID-provider bevat.
- De **authenticationSource** claim met de standaard waarde **socialIdpAuthentication**.

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
| client_id | Ja | De toepassings-id van de ID-provider. |
| IdTokenAudience | Nee | De doel groep van de id_token. Indien opgegeven, Azure AD B2C controleert of het token zich in een claim bevindt die wordt geretourneerd door de ID-provider en is deze gelijk aan de waarde die is opgegeven. |
| authorization_endpoint | Ja | De URL van het autorisatie-eind punt conform RFC 6749. |
| AccessTokenEndpoint | Ja | De URL van het eind punt van het token conform RFC 6749. |
| ClaimsEndpoint | Ja | De URL van het eind punt voor gebruikers gegevens volgens RFC 6749. |
| AccessTokenResponseFormat | Nee | De indeling van de eindpunt aanroep van het toegangs token. Voor Facebook is bijvoorbeeld een HTTP GET-methode vereist, maar het antwoord van het toegangs token heeft de JSON-indeling. |
| AdditionalRequestQueryParameters | Nee | Aanvullende query parameters voor aanvragen. U kunt bijvoorbeeld extra para meters verzenden naar uw ID-provider. U kunt meerdere para meters met een komma als scheidings teken gebruiken. |
| ClaimsEndpointAccessTokenName | Nee | De naam van de query teken reeks parameter voor het toegangs token. De ondersteuning voor claims-eind punten van een id-provider kan een HTTP-aanvraag ophalen. In dit geval wordt het Bearer-token verzonden met behulp van een query reeks parameter in plaats van de autorisatie-header. |
| ClaimsEndpointFormatName | Nee | De naam van de query teken reeks parameter voor opmaak. U kunt bijvoorbeeld de naam instellen als `format` in dit LinkedIn-eind punt voor claims `https://api.linkedin.com/v1/people/~?format=json`. |
| ClaimsEndpointFormat | Nee | De waarde van de para meter voor de indelings query teken reeks. U kunt de waarde bijvoorbeeld instellen als `json` in dit LinkedIn-eind punt voor claims `https://api.linkedin.com/v1/people/~?format=json`. |
| ProviderName | Nee | De naam van de ID-provider. |
| response_mode | Nee | De methode die de ID-provider gebruikt om het resultaat terug te sturen naar Azure AD B2C. Mogelijke waarden: `query`, `form_post` (standaard) of `fragment`. |
| scope | Nee | Het bereik van de aanvraag die is gedefinieerd volgens de specificatie van de OAuth2-ID-provider. Zoals `openid`, `profile`en `email`. |
| HttpBinding | Nee | De verwachte HTTP-binding met het toegangs token en claims token-eind punten. Mogelijke waarden: `GET` of `POST`.  |
| ResponseErrorCodeParamName | Nee | De naam van de para meter die het fout bericht bevat dat via HTTP 200 (OK) wordt geretourneerd. |
| ExtraParamsInAccessTokenEndpointResponse | Nee | Bevat de extra para meters die kunnen worden geretourneerd in het antwoord van **AccessTokenEndpoint** door sommige id-providers. Het antwoord van **AccessTokenEndpoint** bevat bijvoorbeeld een extra para meter, zoals `openid`, een verplichte para meter naast de access_token in een query reeks voor **ClaimsEndpoint** -aanvragen. Meerdere parameter namen moeten worden voorafgegaan en gescheiden door een komma ', ' als scheidings teken. |
| ExtraParamsInClaimsEndpointRequest | Nee | Bevat de extra para meters die in de **ClaimsEndpoint** -aanvraag door sommige id-providers kunnen worden geretourneerd. Meerdere parameter namen moeten worden voorafgegaan en gescheiden door een komma ', ' als scheidings teken. |
| IncludeClaimResolvingInClaimsHandling  | Nee | Voor invoer-en uitvoer claims geeft u op of [claim omzetting](claim-resolver-overview.md) in het technische profiel is opgenomen. Mogelijke waarden: `true`, of `false` (standaard). Als u een claim conflict Oplosser wilt gebruiken in het technische profiel, stelt u dit in op `true`. |
| ResolveJsonPathsInJsonTokens  | Nee | Hiermee wordt aangegeven of het technische profiel JSON-paden oplost. Mogelijke waarden: `true`of `false` (standaard). Gebruik deze meta gegevens om gegevens van een genest JSON-element te lezen. Stel in een [output claim](technicalprofiles.md#outputclaims)de `PartnerClaimType` in op het JSON-padcomponent-element dat u wilt uitvoeren. Bijvoorbeeld: `firstName.localized`of `data.0.to.0.email`.|

## <a name="cryptographic-keys"></a>Cryptografische sleutels

Het element **CryptographicKeys** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| client_secret | Ja | Het client geheim van de identiteits provider toepassing. De cryptografische sleutel is alleen vereist als de **response_types** meta gegevens zijn ingesteld op `code`. In dit geval maakt Azure AD B2C een andere aanroep voor het uitwisselen van de autorisatie code voor een toegangs token. Als de meta gegevens zijn ingesteld op `id_token`, kunt u de cryptografische sleutel weglaten. |

## <a name="redirect-uri"></a>Omleidings-URI

Wanneer u de omleidings-URL van uw ID-provider configureert, voert u `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`in. Zorg ervoor dat u de **Tenant** vervangt door de naam van uw Tenant (bijvoorbeeld contosob2c.onmicrosoft.com) en **policyId** met de id van uw beleid (bijvoorbeeld b2c_1a_policy). De omleidings-URI moet in alle kleine letters zijn.

Als u het **b2clogin.com** -domein gebruikt in plaats van **login.microsoftonline.com** , moet u ervoor zorgen dat u b2clogin.com gebruikt in plaats van login.microsoftonline.com.

Voorbeelden:

- [Google + als OAuth2-ID-provider toevoegen met aangepaste beleids regels](identity-provider-google-custom.md)













