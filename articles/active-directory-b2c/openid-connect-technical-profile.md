---
title: Definieer een OpenID Connect Connect Technical-profiel in een aangepast beleid in Azure Active Directory B2C | Microsoft Docs
description: Definieer een OpenID Connect Connect Technical-profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1e8f03b17c5e8ea68affa9fe83875382fd5d8512
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716702"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een OpenID Connect Connect Technical-profiel definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory-B2C (Azure AD) biedt ondersteuning voor de [OpenID Connect Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) protocol-ID-provider. OpenID Connect Connect 1,0 definieert een Identity Layer boven op OAuth 2,0 en vertegenwoordigt de status van de kunst in moderne verificatie protocollen. Met een OpenID Connect Connect Technical-profiel kunt u communiceren met een OpenID connect-verbinding op basis van een id-provider, zoals Azure AD. Met federeren met een id-provider kunnen gebruikers zich aanmelden met hun bestaande sociale of bedrijfs identiteiten.

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `OpenIdConnect`. Het protocol voor het technische profiel **MSA-OIDC** is `OpenIdConnect`bijvoorbeeld:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Invoer claims

De **InputClaims** -en **InputClaimsTransformations** -elementen zijn niet vereist. Maar u wilt mogelijk extra para meters naar uw ID-provider verzenden. In het volgende voor beeld wordt de **domain_hint** -query teken reeks parameter `contoso.com` met de waarde van aan de autorisatie aanvraag toegevoegd.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Uitvoer claims

Het **OutputClaims** -element bevat een lijst met claims die zijn geretourneerd door de OpenID Connect Connect-ID-provider. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in de ID-provider. U kunt ook claims toevoegen die niet worden geretourneerd door de ID-provider, zolang u het `DefaultValue` kenmerk hebt ingesteld.

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

In het volgende voor beeld worden de claims weer gegeven die zijn geretourneerd door de micro soft-account-ID-provider:

- De **subclaim die** is toegewezen aan de **issuerUserId** -claim.
- De **naam** claim die is toegewezen aan de **DisplayName** -claim.
- Het **e-mail adres** zonder naam toewijzing.

Het technische profiel retourneert ook claims die niet worden geretourneerd door de ID-provider:

- De claim **Identity provider** die de naam van de ID-provider bevat.
- De **authenticationSource** claim met de standaard waarde **socialIdpAuthentication**.

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

| Kenmerk | Vereist | Description |
| --------- | -------- | ----------- |
| client_id | Ja | De toepassings-id van de ID-provider. |
| IdTokenAudience | Nee | De doel groep van de id_token. Indien opgegeven, Azure AD B2C controleert of het token zich in een claim bevindt die wordt geretourneerd door de ID-provider en is deze gelijk aan de waarde die is opgegeven. |
| METAGEGEVENSARCHIEFMETHODE | Ja | Een URL die verwijst naar een JSON-configuratie document dat is opgemaakt volgens de OpenID Connect Connect-detectie specificatie, ook wel bekend als een bekende OpenID Connect-configuratie-eind punt. |
| ProviderName | Nee | De naam van de ID-provider. |
| response_types | Nee | Het antwoord type volgens de OpenID Connect Connect Core 1,0-specificatie. Mogelijke waarden: `id_token`, `code`of `token`. |
| response_mode | Nee | De methode die de ID-provider gebruikt om het resultaat terug te sturen naar Azure AD B2C. Mogelijke waarden: `query`, `form_post` (standaard) of `fragment`. |
| bereik | Nee | Het bereik van de aanvraag die is gedefinieerd volgens de OpenID Connect Connect Core 1,0-specificatie. `openid`Zoals, `profile`, en `email`. |
| HttpBinding | Nee | De verwachte HTTP-binding met het toegangs token en claims token-eind punten. Mogelijke waarden: `GET` of `POST`.  |
| ValidTokenIssuerPrefixes | Nee | Een sleutel die kan worden gebruikt om u aan te melden bij elk van de tenants wanneer u een multi tenant-ID-provider gebruikt, zoals Azure Active Directory. |
| UsePolicyInRedirectUri | Nee | Hiermee wordt aangegeven of een beleid moet worden gebruikt bij het samen stellen van de omleidings-URI. Wanneer u uw toepassing in de ID-provider configureert, moet u de omleidings-URI opgeven. De omleidings-URI verwijst `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` naar Azure AD B2C, (login.microsoftonline.com kan veranderen met your-Tenant-name.b2clogin.com).  Als u opgeeft `false`, moet u een omleidings-URI toevoegen voor elk beleid dat u gebruikt. Bijvoorbeeld: `https://login.microsoftonline.com/te/{tenant}/{policy}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Nee | Hiermee wordt aangegeven of een aanvraag naar een externe service als een fout moet worden gemarkeerd als de HTTP-status code zich in het 5xx bereik bevindt. De standaardwaarde is `false`. |
| DiscoverMetadataByTokenIssuer | Nee | Geeft aan of de OIDC-meta gegevens moeten worden gedetecteerd met behulp van de verlener in het JWT-token. |

## <a name="cryptographic-keys"></a>Cryptografische sleutels

Het element **CryptographicKeys** bevat het volgende kenmerk:

| Kenmerk | Vereist | Description |
| --------- | -------- | ----------- |
| client_secret | Ja | Het client geheim van de identiteits provider toepassing. De cryptografische sleutel is alleen vereist als de **response_types** -meta gegevens `code`zijn ingesteld op. In dit geval maakt Azure AD B2C een andere aanroep voor het uitwisselen van de autorisatie code voor een toegangs token. Als de meta gegevens zijn ingesteld `id_token` op, kunt u de cryptografische sleutel weglaten.  |

## <a name="redirect-uri"></a>Omleidings-URI

Wanneer u de omleidings-URI van uw ID- `https://login.microsoftonline.com/te/tenant/oauth2/authresp`provider configureert, voert u in. Zorg ervoor dat u de **Tenant** vervangt door de naam van de Tenant (bijvoorbeeld contosob2c.onmicrosoft.com) of de Tenant-id. De omleidings-URI moet in alle kleine letters zijn.

Als u het **b2clogin.com** -domein gebruikt in plaats van **login.microsoftonline.com** , moet u ervoor zorgen dat u b2clogin.com gebruikt in plaats van login.microsoftonline.com.

Voorbeelden:

- [Micro soft-account (MSA) toevoegen als een id-provider met behulp van aangepast beleid](active-directory-b2c-custom-setup-msa-idp.md)
- [Aanmelden met Azure AD-accounts](active-directory-b2c-setup-aad-custom.md)
- [Gebruikers toestaan zich aan te melden bij een multi tenant Azure AD-ID-provider met behulp van aangepast beleid](active-directory-b2c-setup-commonaad-custom.md)

 














