---
title: Een technisch profiel van OpenID Connect definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een Technisch profiel van OpenID Connect in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e259a57a9cd6b24362862ffd6cb738157ca912d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332768"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een technisch profiel van OpenID Connect definiëren in een aangepast Azure Directory B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor de [CloudID Connect-protocolidentiteitsprovider.](https://openid.net/2015/04/17/openid-connect-certification-program/) OpenID Connect 1.0 definieert een identiteitslaag bovenop OAuth 2.0 en vertegenwoordigt de stand van de techniek in moderne verificatieprotocollen. Met een technisch profiel van OpenID Connect u zich gedragen met een op OpenID Connect gebaseerde identiteitsprovider, zoals Azure AD. Met Federatie met een identiteitsprovider kunnen gebruikers zich aanmelden met hun bestaande sociale of bedrijfsidentiteiten.

## <a name="protocol"></a>Protocol

Het kenmerk **Naam** van het element `OpenIdConnect` **Protocol** moet worden ingesteld op . Het protocol voor het technische profiel **MSA-OIDC** is `OpenIdConnect`bijvoorbeeld:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
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

Het element **OutputClaims** bevat een lijst met claims die zijn geretourneerd door de OpenID Connect-identiteitsprovider. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in de identiteitsprovider. U ook claims opnemen die niet worden geretourneerd door de `DefaultValue` identiteitsprovider, zolang u het kenmerk instelt.

Het element **OutputClaimsTransformations** kan een verzameling **outputclaimstransformatie-elementen** bevatten die worden gebruikt om de uitvoerclaims te wijzigen of nieuwe claims te genereren.

In het volgende voorbeeld worden de claims weergegeven die zijn geretourneerd door de microsoft-accountidentiteitsprovider:

- De **subclaim** die is toegewezen aan de **claim van de uitgeverUserId.**
- De **naamclaim** die is toegewezen aan de **claim displayName.**
- De **e-mail** zonder naamtoewijzing.

Het technische profiel retourneert ook claims die niet worden geretourneerd door de identiteitsprovider:

- De **identityProvider** claim die de naam van de identiteitsprovider bevat.
- De **claim authenticationSource** met een standaardwaarde van **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| client_id | Ja | De toepassings-id van de identiteitsprovider. |
| IdTokenAudience | Nee | Het publiek van de id_token. Indien opgegeven, controleert Azure AD `aud` B2C of de claim in een token die door de identiteitsprovider is geretourneerd, gelijk is aan de claim die is opgegeven in de metagegevens van IdTokenAudience.  |
| Metagegevens | Ja | Een URL die verwijst naar een Configuratiedocument van de OpenID Connect-identiteitsprovider, dat ook wel openid-bekend configuratieeindpunt wordt genoemd. De URL kan `{tenant}` de expressie bevatten, die wordt vervangen door de tenantnaam.  |
| authorization_endpoint | Nee | Een URL die verwijst naar een eindpunt van een verbindingspunt voor identiteitsautorisatie van de OpenID Connect-identiteitsprovider. De waarde van authorization_endpoint metagegevens heeft voorrang op het `authorization_endpoint` opgegeven in het bekende configuratieeindpunt van OpenID. De URL kan `{tenant}` de expressie bevatten, die wordt vervangen door de tenantnaam. |
| uitgever | Nee | De unieke id van een OpenID Connect-identiteitsprovider. De waarde van metagegevens `issuer` van uitgevende instellingen heeft voorrang op het opgegeven in het bekende configuratieeindpunt van OpenID.  Indien opgegeven, controleert Azure AD `iss` B2C of de claim in een token die door de identiteitsprovider is geretourneerd, gelijk is aan de claim die is opgegeven in de metagegevens van de uitgever. |
| ProviderName | Nee | De naam van de identiteitsprovider.  |
| response_types | Nee | Het antwoordtype volgens de OpenID Connect Core 1.0-specificatie. Mogelijke `id_token`waarden: `code`, `token`, of . |
| response_mode | Nee | De methode die de identiteitsprovider gebruikt om het resultaat terug te sturen naar Azure AD B2C. Mogelijke `query`waarden: `form_post` , (standaard) of `fragment`. |
| scope | Nee | Het bereik van de aanvraag die is gedefinieerd volgens de OpenID Connect Core 1.0-specificatie. Zoals `openid`, `profile`en `email`. |
| HttpBinding | Nee | De verwachte HTTP-binding met de eindpunten voor toegangstoken en claimstoken. Mogelijke `GET` waarden: `POST`of .  |
| ValidTokenIssuerVoorvoegsels | Nee | Een sleutel die kan worden gebruikt om zich bij elk van de tenants aan te melden wanneer u een multi-tenant identiteitsprovider zoals Azure Active Directory gebruikt. |
| GebruikPolicyinredirecturi | Nee | Geeft aan of u een beleid moet gebruiken bij het maken van de omleidingsURI. Wanneer u uw toepassing configureert in de identiteitsprovider, moet u de omleidings-URI opgeven. De URI-punten omleiden naar `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`Azure AD B2C, .  Als u `false`dit opgeeft, moet u een omleidingsURI toevoegen voor elk beleid dat u gebruikt. Bijvoorbeeld: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Nee | Geeft aan of een aanvraag voor een externe service moet worden gemarkeerd als een fout als de Http-statuscode zich in het 5xx-bereik bevindt. De standaardwaarde is `false`. |
| Metagegevensaaltokenuitgever ontdekken | Nee | Geeft aan of de OIDC-metagegevens moeten worden gedetecteerd met behulp van de uitgever in het JWT-token. |
| IncludeClaimResolvingInClaimsHandling  | Nee | Voor invoer- en uitvoerclaims geeft u aan of [schadeafhandeling](claim-resolver-overview.md) is opgenomen in het technische profiel. Mogelijke waarden: `true` `false`  , of (standaard). Als u een claimresolver in het technische profiel `true`wilt gebruiken, stelt u dit in op . |

### <a name="ui-elements"></a>UI-elementen
 
De volgende instellingen kunnen worden gebruikt om het foutbericht te configureren dat bij een storing wordt weergegeven. De metagegevens moeten worden geconfigureerd in het technische profiel van OpenID Connect. De foutberichten kunnen worden [gelokaliseerd](localization-string-ids.md#sign-up-or-sign-in-error-messages).

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalNietExist | Nee | Het bericht dat aan de gebruiker wordt weergegeven als een account met de opgegeven gebruikersnaam niet in de map wordt gevonden. |
| UserMessageIfInvalidPassword | Nee | Het bericht dat aan de gebruiker moet worden weergegeven als het wachtwoord onjuist is. |
| UserMessageIfOldPasswordGebruikt| Nee |  Het bericht dat aan de gebruiker moet worden weergegeven als een oud wachtwoord wordt gebruikt.|

## <a name="cryptographic-keys"></a>Cryptografische toetsen

Het element **CryptographicKeys** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| client_secret | Ja | Het klantgeheim van de aanvraag van de identiteitsprovider. De cryptografische sleutel is **response_types** alleen vereist als `code`de response_types metagegevens is ingesteld op . In dit geval voert Azure AD B2C nog een oproep om de autorisatiecode om te wisselen voor een toegangstoken. Als de metagegevens zijn ingesteld op, `id_token` u de cryptografische sleutel weglaten.  |

## <a name="redirect-uri"></a>Uri omleiden

Wanneer u de omleidingsuri van `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`uw identiteitsprovider configureert, voert u . Zorg ervoor `{your-tenant-name}` dat u de naam van uw huurder vervangt. De omleiding URI moet in alle kleine letters.

Voorbeelden:

- [Microsoft-account (MSA) toevoegen als identiteitsprovider met aangepaste beleidsregels](identity-provider-microsoft-account-custom.md)
- [Aanmelden met Azure AD-accounts](identity-provider-azure-ad-single-tenant-custom.md)
- [Gebruikers toestaan zich aan te melden bij een Azure AD-identiteitsprovider met meerdere tenantn met behulp van aangepast beleid](identity-provider-azure-ad-multi-tenant-custom.md)
