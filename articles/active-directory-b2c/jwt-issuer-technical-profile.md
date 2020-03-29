---
title: Een technisch profiel definiëren voor een JWT-uitgever in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een technisch profiel voor een JWT-uitgever (JSON-webtoken) in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/06/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c23648d70192607b2a5b977dcdd445931e995154
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671787"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Een technisch profiel definiëren voor een JWT-tokenuitgever in een aangepast Azure Directory B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zendt verschillende soorten beveiligingstokens uit terwijl elke verificatiestroom wordt verwerkt. Een technisch profiel voor een JWT-tokenuitgever zendt een JWT-token uit dat wordt teruggestuurd naar de toepassing van de relying party. Meestal is dit technische profiel de laatste orkestratiestap in de gebruikersreis.

## <a name="protocol"></a>Protocol

Het kenmerk **Naam** van het element `None` **Protocol** moet worden ingesteld op . Stel het element **OutputTokenFormat** in op `JWT`.

In het volgende voorbeeld `JwtIssuer`ziet u een technisch profiel voor :

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Beweringen voor invoer, uitvoer en aanhouden

De elementen **InputClaims,** **OutputClaims**en **PersistClaims** zijn leeg of afwezig. De **Elementen InutputClaimsTransformations** en **OutputClaimsTransformations** ontbreken ook.

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Ja | De claim die moet worden gebruikt als de gebruikersidentiteitsclaim binnen de OAuth2-autorisatiecodes en tokens vernieuwen. Standaard moet u dit `objectId`instellen op , tenzij u een ander claimtype SubjectNamingInfo opgeeft. |
| SendTokenResponseBodyWithJsonNumbers | Nee | Altijd ingesteld `true`op . Voor een verouderde notatie waarbij numerieke waarden worden gegeven `false`als tekenreeksen in plaats van JSON-nummers, ingesteld op . Dit kenmerk is nodig voor clients die afhankelijk zijn van een eerdere implementatie die eigenschappen als tekenreeksen heeft geretourneerd. |
| token_lifetime_secs | Nee | Toegang tot token levensduur. De levensduur van het OAuth 2.0-token aan toonder dat wordt gebruikt om toegang te krijgen tot een beveiligde bron. De standaardinstelling is 3.600 seconden (1 uur). Het minimum (inclusief) is 300 seconden (5 minuten). Het maximum (inclusief) is 86.400 seconden (24 uur). |
| id_token_lifetime_secs | Nee | LEVENSDUUR van ID-token. De standaardinstelling is 3.600 seconden (1 uur). Het minimum (inclusief) is 300 seconden (5 minuten). Het maximum (inclusief) is seconden 86.400 (24 uur). |
| refresh_token_lifetime_secs | Nee | Tokenlevensduur vernieuwen. De maximale periode waarvoor een vernieuwingstoken kan worden gebruikt om een nieuw toegangstoken te verkrijgen, als uw toepassing het offline_access-bereik had gekregen. De standaardinstelling is 120.9600 seconden (14 dagen). Het minimum (inclusief) is 86.400 seconden (24 uur). Het maximum (inclusief) is 7.776.000 seconden (90 dagen). |
| rolling_refresh_token_lifetime_secs | Nee | Vernieuwen token schuifvenster levensduur. Na deze periode verstrijkt de gebruiker wordt gedwongen om opnieuw te verifiëren, ongeacht de geldigheidsperiode van de meest recente refresh token verworven door de toepassing. Als u de levensduur van een schuifvenster niet wilt `true`afdwingen, stelt u de waarde van allow_infinite_rolling_refresh_token in op . De standaardinstelling is 7.776.000 seconden (90 dagen). Het minimum (inclusief) is 86.400 seconden (24 uur). Het maximum (inclusief) is 31.536.000 seconden (365 dagen). |
| allow_infinite_rolling_refresh_token | Nee | Als ingesteld `true`op , verloopt de levensduur van het vernieuwen van tokenschuivende venster nooit. |
| Uitgifteclaimpatroon | Nee | Hiermee bepaalt u de claim van de emittent (iss). Een van de waarden:<ul><li>AuthorityAndTenantGuid - De claim van ISS `login.microsoftonline` bevat `tenant-name.b2clogin.com`uw domeinnaam, zoals\/of , en uw tenant-id https: /login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp - De iss-claim bevat `login.microsoftonline` uw `tenant-name.b2clogin.com`domeinnaam, zoals of, uw tenant-id en uw beleidsnaam van uw relying party. https:\//login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> Standaardwaarde: AuthorityAndTenantGuid |
| AuthenticationContextReferenceClaimPattern | Nee | Hiermee `acr` bepaalt u de claimwaarde.<ul><li>Geen - Azure AD B2C geeft de acr-claim niet uit</li><li>PolicyId - `acr` de claim bevat de beleidsnaam</li></ul>De opties voor het instellen van deze waarde zijn TFP (trust framework policy) en ACR (authentication context reference). Het wordt aanbevolen deze waarde in te stellen op `<Item>` TFP, om de waarde in te stellen, ervoor te zorgen dat de met de `Key="AuthenticationContextReferenceClaimPattern"` bestaat en de waarde is `None`. Voeg in uw beleid `<OutputClaims>` van relying `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`party item toe, voeg dit element toe. Zorg er ook voor dat uw beleid het claimtype bevat`<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |
|RefreshTokenUserJourneyId| Nee | De id van een gebruikersreis die moet worden uitgevoerd tijdens `/token` het vernieuwen van een [post-verzoek voor toegangstoken](authorization-code-flow.md#4-refresh-the-token) naar het eindpunt. |

## <a name="cryptographic-keys"></a>Cryptografische toetsen

Het element CryptographicKeys bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| issuer_secret | Ja | De X509-certificaat (RSA-toetsset) die moet worden gebruikt om het JWT-token te ondertekenen. Dit is `B2C_1A_TokenSigningKeyContainer` de sleutel die u hebt geconfigureerd in [Aan de slag met aangepast beleid](custom-policy-get-started.md). |
| issuer_refresh_token_key | Ja | De X509-certificaat (RSA-toetsset) die moet worden gebruikt om het vernieuwingstoken te versleutelen. U hebt `B2C_1A_TokenEncryptionKeyContainer` de sleutel geconfigureerd in [Aan de slag met aangepast beleid](custom-policy-get-started.md) |














