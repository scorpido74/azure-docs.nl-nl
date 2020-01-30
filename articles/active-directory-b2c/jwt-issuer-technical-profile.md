---
title: Een technisch profiel voor een JWT-verlener in een aangepast beleid definiëren
titleSuffix: Azure AD B2C
description: Definieer een technisch profiel voor een JWT-verlener (JSON Web token) in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f0adbac14c2ae886bc002ae56ab0784b608d1e5d
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841979"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Een technisch profiel voor een JWT-token Uitgever definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) worden verschillende typen beveiligings tokens meegegeven wanneer elke verificatie stroom wordt verwerkt. Een technisch profiel voor een JWT-token uitgever verzendt een JWT-token dat terugkeert naar de Relying Party-toepassing. Normaal gesp roken is dit technische profiel de laatste Orchestration-stap in de gebruikers reis.

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `None`. Stel het element **OutputTokenFormat** in op `JWT`.

In het volgende voor beeld ziet u een technisch profiel voor `JwtIssuer`:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Invoer, uitvoer en persistente claims

De **InputClaims**-, **OutputClaims**-en **PersistClaims** -elementen zijn leeg of ontbreken. De **InutputClaimsTransformations** -en **OutputClaimsTransformations** -elementen zijn ook afwezig.

## <a name="metadata"></a>Metagegevens

| Kenmerk | Verplicht | Beschrijving |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Ja | De claim die moet worden gebruikt als de claim van de identiteit van de gebruiker in de OAuth2-autorisatie codes en vernieuwings tokens. Standaard moet u deze instellen op `objectId`, tenzij u een ander claim type SubjectNamingInfo opgeeft. |
| SendTokenResponseBodyWithJsonNumbers | Nee | Altijd ingesteld op `true`. Voor oudere indeling waarbij numerieke waarden worden gegeven als teken reeksen in plaats van JSON-nummers, stelt u in op `false`. Dit kenmerk is vereist voor clients die een afhankelijkheid hebben genomen van een eerdere implementatie waarbij dergelijke eigenschappen als teken reeksen zijn geretourneerd. |
| token_lifetime_secs | Nee | Levens duur van toegangs token. De levens duur van het OAuth 2,0 Bearer-token dat wordt gebruikt om toegang te krijgen tot een beveiligde bron. De standaard waarde is 3.600 seconden (1 uur). Het minimale (inclusief) is 300 seconden (5 minuten). Het maximum (inclusief) is 86.400 seconden (24 uur). |
| id_token_lifetime_secs | Nee | Levens duur van ID-token. De standaard waarde is 3.600 seconden (1 uur). Het minimale (inclusief) is 300 seconden (5 minuten). Het maximum (inclusief) is seconden 86.400 (24 uur). |
| refresh_token_lifetime_secs | Nee | De levens duur van het token vernieuwen. De maximale tijds duur waarna een vernieuwings token kan worden gebruikt om een nieuw toegangs token te verkrijgen als uw toepassing het offline_access bereik heeft gekregen. De standaard waarde is 120, 9600 seconden (14 dagen). Het minimale (inclusief) is 86.400 seconden (24 uur). Het maximum (inclusief) is 7.776.000 seconden (90 dagen). |
| rolling_refresh_token_lifetime_secs | Nee | De levens duur van het token sliding window vernieuwen. Nadat deze periode is verstreken, wordt de gebruiker gedwongen opnieuw te verifiëren, ongeacht de geldigheids periode van het meest recente vernieuwings token dat is verkregen door de toepassing. Als u geen sliding window levensduur wilt afdwingen, stelt u de waarde van allow_infinite_rolling_refresh_token in op `true`. De standaard waarde is 7.776.000 seconden (90 dagen). Het minimale (inclusief) is 86.400 seconden (24 uur). Het maximum (inclusief) is 31.536.000 seconden (365 dagen). |
| allow_infinite_rolling_refresh_token | Nee | Als deze is ingesteld op `true`, verloopt het vernieuwings token sliding window levens duur nooit. |
| IssuanceClaimPattern | Nee | Hiermee bepaalt u de claim van de verlener (ISS). Een van de volgende waarden:<ul><li>AuthorityAndTenantGuid: de ISS-claim bevat uw domein naam, zoals `login.microsoftonline` of `tenant-name.b2clogin.com`, en uw Tenant-id https:\//login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp: de ISS-claim bevat uw domein naam, zoals `login.microsoftonline` of `tenant-name.b2clogin.com`, uw Tenant-id en de naam van uw Relying Party-beleid. https:\/-login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> Standaard waarde: AuthorityAndTenantGuid |
| AuthenticationContextReferenceClaimPattern | Nee | Hiermee bepaalt u de waarde van de `acr` claim.<ul><li>Geen-Azure AD B2C geeft de claim ACR niet uit</li><li>PolicyId: de `acr` claim bevat de naam van het beleid</li></ul>De opties voor het instellen van deze waarde zijn TFP (Trust Framework Policy) en ACR (Naslag informatie over de verificatie context). U kunt deze waarde het beste instellen op TFP, om de waarde in te stellen, ervoor zorgen dat de `<Item>` met de `Key="AuthenticationContextReferenceClaimPattern"` bestaat en dat de waarde wordt `None`. Voeg in uw Relying Party-beleid `<OutputClaims>` item toe, voeg dit element toe `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. Zorg er ook voor dat uw beleid het claim type bevat `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |

## <a name="cryptographic-keys"></a>Cryptografische sleutels

Het CryptographicKeys-element bevat de volgende kenmerken:

| Kenmerk | Verplicht | Beschrijving |
| --------- | -------- | ----------- |
| issuer_secret | Ja | Het x509-certificaat (RSA-sleutelset) dat moet worden gebruikt voor het ondertekenen van het JWT-token. Dit is de `B2C_1A_TokenSigningKeyContainer` sleutel die u in de afbeelding aan de [slag met aangepaste beleids regels hebt aangepast](custom-policy-get-started.md). |
| issuer_refresh_token_key | Ja | Het x509-certificaat (RSA key set) dat moet worden gebruikt voor het versleutelen van het vernieuwings token. U hebt de `B2C_1A_TokenEncryptionKeyContainer` sleutel geconfigureerd in aan de [slag met aangepaste beleids regels](custom-policy-get-started.md) |














