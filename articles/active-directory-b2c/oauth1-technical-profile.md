---
title: Een OAuth1-technisch profiel definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een OAuth 1.0-technisch profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7f734844859d44e66bddbc2ddd999659e52f9668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184074"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een OAuth1-technisch profiel definiëren in een aangepast Azure Directory B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor de [oAuth 1.0-protocolidentiteitsprovider.](https://tools.ietf.org/html/rfc5849) In dit artikel worden de specifieke kenmerken beschreven van een technisch profiel voor interactie met een claimprovider die dit gestandaardiseerde protocol ondersteunt. Met een OAuth1 technisch profiel u zich gedragen met een op OAuth1 gebaseerde identiteitsprovider, zoals Twitter. Met Federatie met de identiteitsprovider kunnen gebruikers zich aanmelden met hun bestaande sociale of bedrijfsidentiteiten.

## <a name="protocol"></a>Protocol

Het kenmerk **Naam** van het element `OAuth1` **Protocol** moet worden ingesteld op . Bijvoorbeeld, het protocol voor de **Twitter-OAUTH1** technisch profiel is `OAuth1`.

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Invoerclaims

De elementen **InputClaims** en **InputClaimsTransformations** zijn leeg of afwezig.

## <a name="output-claims"></a>Outputclaims

Het element **OutputClaims** bevat een lijst met claims die zijn geretourneerd door de OAuth1-identiteitsprovider. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in de identiteitsprovider. U ook claims opnemen die niet worden geretourneerd door de identiteitsprovider zolang u het kenmerk **DefaultValue** instelt.

Het element **OutputClaimsTransformations** kan een verzameling **outputclaimstransformatie-elementen** bevatten die worden gebruikt om de uitvoerclaims te wijzigen of nieuwe claims te genereren.

In het volgende voorbeeld ziet u de claims die zijn geretourneerd door de Twitter-identiteitsprovider:

- De **user_id** claim die is toegewezen aan de **claim van de uitgeverUserId.**
- De **screen_name** claim die is toegewezen aan de **displayName-claim.**
- De **e-mailclaim** zonder naamtoewijzing.

Het technische profiel retourneert ook claims die niet worden geretourneerd door de identiteitsprovider:

- De **identityProvider** claim die de naam van de identiteitsprovider bevat.
- De **claim authenticationSource** met `socialIdpAuthentication`een standaardwaarde van .

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| client_id | Ja | De toepassings-id van de identiteitsprovider. |
| ProviderName | Nee | De naam van de identiteitsprovider. |
| request_token_endpoint | Ja | De URL van het eindpunt van het aanvraagtoken per RFC 5849. |
| authorization_endpoint | Ja | De URL van het autorisatieeindpunt volgens RFC 5849. |
| access_token_endpoint | Ja | De URL van het tokeneindpunt volgens RFC 5849. |
| ClaimsEindpunt | Nee | De URL van het eindpunt van de gebruikersinformatie. |
| ClaimsResponseFormat | Nee | De claimresponsindeling.|

## <a name="cryptographic-keys"></a>Cryptografische toetsen

Het element **CryptographicKeys** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| client_secret | Ja | Het klantgeheim van de aanvraag van de identiteitsprovider.   |

## <a name="redirect-uri"></a>Omleidings-URI

Wanneer u de omleidings-URL van `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`uw identiteitsprovider configureert, voert u . Zorg ervoor dat u **tenant** vervangt door uw tenantnaam (bijvoorbeeld contosob2c.onmicrosoft.com) en **policyId** door de id van uw beleid (bijvoorbeeld b2c_1a_policy). De omleiding URI moet in alle kleine letters. Voeg een omleidings-URL toe voor alle beleidsregels die de aanmelding van de identiteitsprovider gebruiken.

Als u het **b2clogin.com-domein** gebruikt in plaats van **login.microsoftonline.com** Zorg ervoor dat u b2clogin.com gebruikt in plaats van login.microsoftonline.com.

Voorbeelden:

- [Twitter toevoegen als een OAuth1-identiteitsprovider met behulp van aangepast beleid](identity-provider-twitter-custom.md)













