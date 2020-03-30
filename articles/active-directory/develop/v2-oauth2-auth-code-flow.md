---
title: OAuth-autorisatiecodestroom - Microsoft-identiteitsplatform | Azure
description: Webtoepassingen bouwen met behulp van de implementatie van het OAuth 2.0-verificatieprotocol door het Microsoft-identiteitsplatform.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 366389ddf88cfb72c9ed9d0543c9985eb25f47ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262397"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft-identiteitsplatform en OAuth 2.0-autorisatiecodestroom

De OAuth 2.0-autorisatiecodeverlening kan worden gebruikt in apps die op een apparaat zijn geïnstalleerd om toegang te krijgen tot beveiligde bronnen, zoals web-API's. Met behulp van de implementatie van OAuth 2.0 van het Microsoft-identiteitsplatform u aanmeldings- en API-toegang toevoegen aan uw mobiele en desktop-apps. Deze handleiding is taalonafhankelijk en beschrijft hoe u HTTP-berichten verzendt en ontvangt zonder gebruik te maken van een van de [azure open-source verificatiebibliotheken.](reference-v2-libraries.md)

In dit artikel wordt beschreven hoe u rechtstreeks programmeren tegen het protocol in uw toepassing.  Waar mogelijk raden we u aan de ondersteunde Microsoft Authentication Libraries (MSAL) te gebruiken om tokens te [verkrijgen en beveiligde web-API's te bellen.](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)  Kijk ook eens naar de [voorbeeld-apps die MSAL gebruiken.](sample-v2-code.md)

> [!NOTE]
> Niet alle Azure Active Directory-scenario's & functies worden ondersteund door het eindpunt van het Microsoft-identiteitsplatform. Als u wilt bepalen of u het eindpunt van het Microsoft-identiteitsplatform moet gebruiken, leest u over [de beperkingen van het Microsoft-identiteitsplatform.](active-directory-v2-limitations.md)

De OAuth 2.0 autorisatiecodestroom wordt beschreven in [punt 4.1 van de OAuth 2.0-specificatie](https://tools.ietf.org/html/rfc6749). Het wordt gebruikt om verificatie en autorisatie uit te voeren in de meeste app-typen, waaronder [web-apps](v2-app-types.md#web-apps) en [native geïnstalleerde apps.](v2-app-types.md#mobile-and-native-apps) De stroom stelt apps in staat om veilig access_tokens te verwerven die kunnen worden gebruikt om toegang te krijgen tot bronnen die zijn beveiligd door het eindpunt van het Microsoft-identiteitsplatform.

## <a name="protocol-diagram"></a>Protocoldiagram

Op een hoog niveau ziet de volledige verificatiestroom voor een native/mobiele toepassing er een beetje als volgt uit:

![OAuth Auth-codestroom](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>Een autorisatiecode aanvragen

De autorisatiecodestroom begint met de client `/authorize` die de gebruiker naar het eindpunt leidt. In deze aanvraag vraagt `openid`de `offline_access`client `https://graph.microsoft.com/mail.read `de , en machtigingen van de gebruiker.  Sommige machtigingen zijn beperkt door beheerders, bijvoorbeeld het schrijven van `Directory.ReadWrite.All`gegevens naar de map van een organisatie met behulp van . Als uw toepassing toegang vraagt tot een van deze machtigingen van een organisatiegebruiker, ontvangt de gebruiker een foutbericht waarin staat dat hij geen toestemming heeft om in te stemmen met de machtigingen van uw app. Als u toegang wilt vragen tot beheerdersbeperkte scopes, moet u deze rechtstreeks bij een bedrijfsbeheerder aanvragen.  Lees voor meer informatie [machtigingen met beperkte machtigingen voor beheerders](v2-permissions-and-consent.md#admin-restricted-permissions).

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [!TIP]
> Klik op de onderstaande link om dit verzoek uit te voeren! Na het aanmelden moet uw browser `https://localhost/myapp/` worden `code` doorgestuurd naar met een in de adresbalk.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parameter    | Vereist/optioneel | Beschrijving |
|--------------|-------------|--------------|
| `tenant`    | vereist    | De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich bij de toepassing kan aanmelden. De toegestane waarden `common` `organizations`zijn `consumers`, , en tenant-id's. Zie [protocolbasics](active-directory-v2-protocols.md#endpoints)voor meer details.  |
| `client_id`   | vereist    | De **toepassings-id (client)** die de [Azure-portal – App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring toegewezen aan uw app.  |
| `response_type` | vereist    | Moet `code` voor de stroom van de vergunningscode omvatten.       |
| `redirect_uri`  | vereist | De redirect_uri van uw app, waar verificatiereacties door uw app kunnen worden verzonden en ontvangen. Het moet precies overeenkomen met een van de redirect_uris die u hebt geregistreerd in de portal, behalve dat het moet worden gecodeerd. Voor native & mobiele apps, moet `https://login.microsoftonline.com/common/oauth2/nativeclient`u de standaardwaarde van .   |
| `scope`  | vereist    | Een lijst met [scopes](v2-permissions-and-consent.md) die door de gebruiker is gescheiden.  Voor `/authorize` de poot van het verzoek kan dit betrekking hebben op meerdere bronnen, zodat uw app toestemming kan krijgen voor meerdere web-API's die u wilt bellen. |
| `response_mode`   | Aanbevolen | Hiermee geeft u de methode op die moet worden gebruikt om het resulterende token terug te sturen naar uw app. Kan een van de volgende:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query`geeft de code als parameter voor querytekenreeksen op uw omleidings-URI. Als u een ID-token aanvraagt met behulp van `query` de impliciete stroom, u niet gebruiken zoals opgegeven in de [OpenID-spec](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Als u alleen de code aanvraagt, `query` `fragment`kunt `form_post`u , of . `form_post`hiermee wordt een post met de code naar uw omleiding uri uitgevoerd. Zie [OpenID Connect-protocol](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)voor meer informatie .  |
| `state`                 | Aanbevolen | Een waarde die is opgenomen in het verzoek en die ook wordt geretourneerd in het tokenantwoord. Het kan een reeks van alle inhoud die u wenst. Een willekeurig gegenereerde unieke waarde wordt meestal gebruikt voor [het voorkomen van cross-site request vervalsing aanvallen](https://tools.ietf.org/html/rfc6749#section-10.12). De waarde kan ook informatie coderen over de status van de gebruiker in de app voordat het verificatieverzoek is opgetreden, zoals de pagina of weergave waarop ze zich bevonden. |
| `prompt`  | optioneel    | Geeft het type gebruikersinteractie aan dat vereist is. De enige geldige waarden `login`op `none`dit `consent`moment zijn , en .<br/><br/>- `prompt=login`zal de gebruiker dwingen om hun referenties in te voeren op dat verzoek, ontkennen single-sign aan.<br/>- `prompt=none`is het tegenovergestelde - het zal ervoor zorgen dat de gebruiker niet wordt gepresenteerd met een interactieve prompt dan ook. Als de aanvraag niet in stilte kan worden voltooid via één aanmelding, `interaction_required` wordt een fout weergegeven in het eindpunt van het Microsoft-identiteitsplatform.<br/>- `prompt=consent`activeert het dialoogvenster OAuth-toestemming nadat de gebruiker zich heeft aanmeldt en vraagt de gebruiker om machtigingen voor de app te verlenen.<br/>- `prompt=select_account`zal eenmalige aanmelding onderbreken met ervaring in accountselectie waarin alle accounts in sessie of een onthouden account of een optie worden vermeld om ervoor te kiezen om een ander account helemaal te gebruiken.<br/> |
| `login_hint`  | optioneel    | Kan worden gebruikt om vooraf de gebruikersnaam/e-mailadres veld van de aanmeldingspagina voor de gebruiker in te vullen, als u hun gebruikersnaam van tevoren kent. Vaak gebruiken apps deze parameter tijdens de herverificatie, nadat ze de gebruikersnaam `preferred_username` al uit een eerdere aanmelding hebben gehaald met behulp van de claim.   |
| `domain_hint`  | optioneel    | Kan een `consumers` van `organizations`of .<br/><br/>Als dit wordt opgenomen, wordt het detectieproces op basis van e-mail dat door de gebruiker op de aanmeldingspagina wordt uitgevoerd, wat leidt tot een iets meer gestroomlijnde gebruikerservaring. Vaak gebruiken apps deze parameter tijdens herverificatie, `tid` door het uit een vorige aanmelding te halen. Als `tid` de claimwaarde `9188040d-6c67-4c5b-b112-36a304b66dad`is, `domain_hint=consumers`moet u . Gebruik anders `domain_hint=organizations`.  |
| `code_challenge_method` | optioneel    | De methode die wordt `code_verifier` gebruikt `code_challenge` om de parameter te coderen. Kan een van de volgende waarden zijn:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Indien uitgesloten, `code_challenge` wordt verondersteld plaintext `code_challenge` te zijn als wordt opgenomen. Microsoft-identiteitsplatform `plain` ondersteunt `S256`beide en . Zie voor meer informatie de [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| `code_challenge`  | optioneel | Wordt gebruikt om autorisatiecode-subsidies te beveiligen via Proof Key for Code Exchange (PKCE) van een native client. Vereist `code_challenge_method` als is inbegrepen. Zie voor meer informatie de [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

Op dit punt wordt de gebruiker gevraagd om zijn referenties in te voeren en de verificatie te voltooien. Het eindpunt van het Microsoft-identiteitsplatform zorgt er ook voor dat `scope` de gebruiker heeft ingestemd met de machtigingen die in de queryparameter zijn vermeld. Als de gebruiker niet heeft ingestemd met een van deze machtigingen, zal het de gebruiker vragen om toestemming te geven voor de vereiste machtigingen. Details van [machtigingen, toestemming en apps met meerdere huurders worden hier verstrekt.](v2-permissions-and-consent.md)

Zodra de gebruiker zich verifieert en toestemming verleent, retourneert het `redirect_uri`eindpunt van het Microsoft-identiteitsplatform een antwoord op uw app op de aangegeven , met behulp van de methode die in de `response_mode` parameter is opgegeven.

#### <a name="successful-response"></a>Succesvolle reactie

Een succesvol `response_mode=query` antwoord met de daarop beoogt:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parameter | Beschrijving  |
|-----------|--------------|
| `code` | De authorization_code die de app heeft aangevraagd. De app kan de autorisatiecode gebruiken om een toegangstoken voor de doelbron aan te vragen. Authorization_codes zijn van korte duur, meestal vervallen ze na ongeveer 10 minuten. |
| `state` | Als een parameter in de status in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weergegeven. De app moet controleren of de statuswaarden in de aanvraag en het antwoord identiek zijn. |

#### <a name="error-response"></a>Foutreactie

Foutreacties kunnen ook naar `redirect_uri` de app worden verzonden, zodat de app ze op de juiste manier kan afhandelen:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschrijving  |
|----------|------------------|
| `error`  | Een foutcodetekenreeks die kan worden gebruikt om typen fouten die zich voordoen te classificeren en kan worden gebruikt om op fouten te reageren. |
| `error_description` | Een specifiek foutbericht waarmee een ontwikkelaar de hoofdoorzaak van een verificatiefout kan identificeren. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Foutcodes voor eindpuntfouten voor autorisatie

In de volgende tabel worden de verschillende foutcodes beschreven die kunnen worden geretourneerd in de `error` parameter van de foutreactie.

| Foutcode  | Beschrijving    | Clientactie   |
|-------------|----------------|-----------------|
| `invalid_request` | Protocolfout, zoals een ontbrekende vereiste parameter. | De aanvraag herstellen en opnieuw indienen. Dit is een ontwikkelingsfout meestal gevangen tijdens de eerste tests. |
| `unauthorized_client` | De clientaanvraag is niet toegestaan om een autorisatiecode aan te vragen. | Deze fout treedt meestal op wanneer de clienttoepassing niet is geregistreerd in Azure AD of niet wordt toegevoegd aan de Azure AD-tenant van de gebruiker. De toepassing kan de gebruiker instructies geven voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| `access_denied`  | Eigenaar van de resource heeft geen toestemming geweigerd  | De clienttoepassing kan de gebruiker laten weten dat deze niet door kan gaan, tenzij de gebruiker hiermee instemt. |
| `unsupported_response_type` | De autorisatieserver ondersteunt het antwoordtype in de aanvraag niet. | De aanvraag herstellen en opnieuw indienen. Dit is een ontwikkelingsfout meestal gevangen tijdens de eerste tests.  |
| `server_error`  | Er is een onverwachte fout opgetreden op de server.| Probeer het verzoek opnieuw. Deze fouten kunnen het gevolg zijn van tijdelijke omstandigheden. De clienttoepassing kan de gebruiker uitleggen dat de reactie is vertraagd op een tijdelijke fout. |
| `temporarily_unavailable`   | De server is tijdelijk te druk om de aanvraag af te handelen. | Probeer het verzoek opnieuw. De clienttoepassing kan de gebruiker uitleggen dat de reactie is vertraagd vanwege een tijdelijke aandoening. |
| `invalid_resource`  | De doelbron is ongeldig omdat deze niet bestaat, Azure AD deze niet kan vinden of niet correct is geconfigureerd. | Deze fout geeft aan dat de resource, als deze bestaat, niet is geconfigureerd in de tenant. De toepassing kan de gebruiker instructies geven voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| `login_required` | Te veel of geen gebruikers gevonden | De client vroeg om`prompt=none`stille verificatie ( ), maar een enkele gebruiker kon niet vinden. Dit kan betekenen dat er meerdere gebruikers actief zijn in de sessie of dat er geen gebruikers zijn. Hierbij wordt rekening gehouden met de gekozen tenant (bijvoorbeeld als er twee `consumers` Azure AD-accounts actief zijn en één Microsoft-account, en wordt gekozen, werkt stille verificatie). |
| `interaction_required` | De aanvraag vereist interactie van de gebruiker. | Een extra verificatiestap of toestemming is vereist. Probeer het verzoek `prompt=none`opnieuw zonder . |

## <a name="request-an-access-token"></a>Een toegangstoken aanvragen

Nu u een authorization_code hebt verkregen en toestemming hebt gekregen van `code` de `access_token` gebruiker, u de voor een naar de gewenste bron inwisselen. Doe dit door `POST` een `/token` aanvraag naar het eindpunt te sturen:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Probeer dit verzoek uit te voeren in Postman! (Vergeet niet om de `code`) [Probeer dit verzoek uit te voeren in Postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parameter  | Vereist/optioneel | Beschrijving     |
|------------|-------------------|----------------|
| `tenant`   | vereist   | De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich bij de toepassing kan aanmelden. De toegestane waarden `common` `organizations`zijn `consumers`, , en tenant-id's. Zie [protocolbasics](active-directory-v2-protocols.md#endpoints)voor meer details.  |
| `client_id` | vereist  | De toepassings-id (client) die de [pagina Azure-portal – App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) aan uw app heeft toegewezen. |
| `grant_type` | vereist   | Moet `authorization_code` voor de autorisatiecodestroom zijn.   |
| `scope`      | vereist   | Een ruimte-gescheiden lijst met scopes. De in dit been gevraagde scopes moeten gelijkwaardig zijn aan of een subset van de in het eerste deel gevraagde scopes. De scopes moeten allemaal afkomstig zijn van één resource, `openid` `email`samen met OIDC-scopes (`profile`, , ). Raadpleeg voor een meer gedetailleerde uitleg van scopes [machtigingen, toestemming en scopes](v2-permissions-and-consent.md). |
| `code`          | vereist  | De authorization_code die je hebt verworven in het eerste deel van de flow. |
| `redirect_uri`  | vereist  | Dezelfde redirect_uri waarde die werd gebruikt om de authorization_code te verwerven. |
| `client_secret` | vereist voor web-apps | Het toepassingsgeheim dat u hebt gemaakt in de app-registratieportal voor uw app. U moet het toepassingsgeheim niet gebruiken in een native app, omdat client_secrets niet betrouwbaar kan worden opgeslagen op apparaten. Het is vereist voor web-apps en web API's, die de mogelijkheid hebben om de client_secret veilig op te slaan aan de serverkant.  Het clientgeheim moet worden gecodeerd door de URL voordat het wordt verzonden. Voor meer informatie klik [hier](https://tools.ietf.org/html/rfc3986#page-12). |
| `code_verifier` | optioneel  | Dezelfde code_verifier die werd gebruikt om de authorization_code te verkrijgen. Vereist als PKCE werd gebruikt in de vergunningscode subsidieaanvraag. Zie voor meer informatie de [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Succesvolle reactie

Een succesvolle tokenreactie ziet eruit als volgt:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parameter     | Beschrijving   |
|---------------|------------------------------|
| `access_token`  | Het gevraagde toegangstoken. De app kan dit token gebruiken om te verifiëren voor de beveiligde bron, zoals een web-API.  |
| `token_type`    | Geeft de waarde van het tokentype aan. Het enige type dat Azure AD ondersteunt, is Drager |
| `expires_in`    | Hoe lang het toegangstoken geldig is (in seconden). |
| `scope`         | De scopes waarvoor de access_token geldig is. |
| `refresh_token` | Een OAuth 2.0 refresh token. De app kan dit token gebruiken om extra toegangstokens te verkrijgen nadat het huidige toegangstoken is verlopen. Refresh_tokens zijn van lange duur en kunnen worden gebruikt om toegang tot bronnen voor langere tijd te behouden. Zie de [sectie hieronder](#refresh-the-access-token)voor meer informatie over het vernieuwen van een access token. <br> **Let op:** Alleen verstrekt `offline_access` als scope werd gevraagd. |
| `id_token`      | Een JSON-webtoken (JWT). De app kan de segmenten van dit token decoderen om informatie op te vragen over de gebruiker die zich heeft aangemeld. De app kan de waarden in de cache bewaren en weergeven, maar mag er niet op vertrouwen voor autorisatie- of beveiligingsgrenzen. Zie voor meer informatie [`id_token reference`](id-tokens.md)over id_tokens de . <br> **Let op:** Alleen verstrekt `openid` als scope werd gevraagd. |

### <a name="error-response"></a>Foutreactie

Foutreacties zien eruit als volgt:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter         | Beschrijving    |
|-------------------|----------------|
| `error`       | Een foutcodetekenreeks die kan worden gebruikt om typen fouten die zich voordoen te classificeren en kan worden gebruikt om op fouten te reageren. |
| `error_description` | Een specifiek foutbericht waarmee een ontwikkelaar de hoofdoorzaak van een verificatiefout kan identificeren. |
| `error_codes` | Een lijst met STS-specifieke foutcodes die kunnen helpen bij diagnostiek.  |
| `timestamp`   | Het tijdstip waarop de fout is opgetreden. |
| `trace_id`    | Een unieke id voor de aanvraag die kan helpen bij diagnostische gegevens. |
| `correlation_id` | Een unieke id voor de aanvraag die kan helpen bij diagnostische gegevens over onderdelen. |

### <a name="error-codes-for-token-endpoint-errors"></a>Foutcodes voor tokeneindpuntfouten

| Foutcode         | Beschrijving        | Clientactie    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Protocolfout, zoals een ontbrekende vereiste parameter. | De aanvraag oplossen en opnieuw indienen   |
| `invalid_grant`    | De autorisatiecode of PKCE-codeverificateur is ongeldig of is verlopen. | Probeer een nieuw `/authorize` verzoek naar het eindpunt en controleer of de parameter code_verifier juist was.  |
| `unauthorized_client` | De geverifieerde client is niet bevoegd om dit type autorisatieverlening te gebruiken. | Dit gebeurt meestal wanneer de clienttoepassing niet is geregistreerd in Azure AD of niet wordt toegevoegd aan de Azure AD-tenant van de gebruiker. De toepassing kan de gebruiker instructies geven voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| `invalid_client` | Clientverificatie is mislukt.  | De clientreferenties zijn niet geldig. Om het probleem op te lossen, werkt de toepassingsbeheerder de referenties bij.   |
| `unsupported_grant_type` | De autorisatieserver ondersteunt het type autorisatieverlening niet. | Wijzig het subsidietype in de aanvraag. Dit type fout mag alleen optreden tijdens de ontwikkeling en worden gedetecteerd tijdens de eerste tests. |
| `invalid_resource` | De doelbron is ongeldig omdat deze niet bestaat, Azure AD deze niet kan vinden of niet correct is geconfigureerd. | Dit geeft aan dat de resource, als deze bestaat, niet is geconfigureerd in de tenant. De toepassing kan de gebruiker instructies geven voor het installeren van de toepassing en het toevoegen aan Azure AD.  |
| `interaction_required` | De aanvraag vereist interactie van de gebruiker. Er is bijvoorbeeld een extra verificatiestap vereist. | Probeer de aanvraag opnieuw met dezelfde resource.  |
| `temporarily_unavailable` | De server is tijdelijk te druk om de aanvraag af te handelen. | Probeer het verzoek opnieuw. De clienttoepassing kan de gebruiker uitleggen dat de reactie is vertraagd vanwege een tijdelijke aandoening. |

## <a name="use-the-access-token"></a>Het toegangstoken gebruiken

Nu u een `access_token`token hebt aangeschaft, u het token gebruiken in aanvragen `Authorization` voor web-API's door het in de koptekst op te nemen:

> [!TIP]
> Voer dit verzoek uit in Postman! (Vervang `Authorization` eerst de koptekst) [Probeer dit verzoek uit te voeren in Postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Het toegangstoken vernieuwen

Access_tokens van korte duur zijn en u moet ze vernieuwen nadat ze zijn verlopen om toegang te blijven krijgen tot bronnen. U dit doen `POST` door een `/token` ander verzoek in `refresh_token` te dienen `code`bij het eindpunt, dit keer met de in plaats van de.  Vernieuwingstokens zijn geldig voor alle machtigingen waarvoor uw klant al toestemming heeft ontvangen `scope=mail.read` - dus een vernieuwingstoken `scope=api://contoso.com/api/UseResource`die is uitgegeven op een verzoek om een nieuw toegangstoken aan te vragen voor .  

Vernieuwingstokens hebben geen opgegeven levensduur. Meestal zijn de levensduur van vernieuwingstokens relatief lang. In sommige gevallen verlopen vernieuwingstokens echter, worden ze ingetrokken of ontbreken ze niet over voldoende bevoegdheden voor de gewenste actie. Uw toepassing moet fouten die door het eindpunt voor tokenuitgifte worden [geretourneerd,](#error-codes-for-token-endpoint-errors) correct verwachten en verwerken. 

Hoewel vernieuwingstokens niet worden ingetrokken wanneer ze worden gebruikt om nieuwe toegangstokens te verkrijgen, wordt van u verwacht dat u het oude vernieuwingstoken verwijdert. De [OAuth 2.0-spec](https://tools.ietf.org/html/rfc6749#section-6) zegt: "De autorisatieserver kan een nieuw vernieuwingstoken uitgeven, in welk geval de client het oude vernieuwingstoken moet verwijderen en vervangen door het nieuwe vernieuwingstoken. De autorisatieserver kan het oude vernieuwingstoken intrekken nadat het een nieuw vernieuwingstoken aan de client heeft uitgegeven."  

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> Probeer dit verzoek uit te voeren in Postman! (Vergeet niet om de `refresh_token`) [Probeer dit verzoek uit te voeren in Postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
> 

| Parameter     |                | Beschrijving        |
|---------------|----------------|--------------------|
| `tenant`        | vereist     | De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich bij de toepassing kan aanmelden. De toegestane waarden `common` `organizations`zijn `consumers`, , en tenant-id's. Zie [protocolbasics](active-directory-v2-protocols.md#endpoints)voor meer details.   |
| `client_id`     | vereist    | De **toepassings-id (client)** die de [Azure-portal – App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring toegewezen aan uw app. |
| `grant_type`    | vereist    | Moet `refresh_token` voor dit been van de autorisatie code stroom. |
| `scope`         | vereist    | Een ruimte-gescheiden lijst met scopes. De in dit been gevraagde scopes moeten gelijkwaardig zijn aan of een subset van de in de oorspronkelijke authorization_code aanvraagpoot gevraagde scopes. Als de scopes die in deze aanvraag zijn opgegeven meerdere resourceserver omvatten, retourneert het eindpunt van het Microsoft-identiteitsplatform een token voor de bron die is opgegeven in het eerste bereik. Raadpleeg voor een meer gedetailleerde uitleg van scopes [machtigingen, toestemming en scopes](v2-permissions-and-consent.md). |
| `refresh_token` | vereist    | De refresh_token die je hebt verworven in het tweede deel van de flow. |
| `client_secret` | vereist voor web-apps | Het toepassingsgeheim dat u hebt gemaakt in de app-registratieportal voor uw app. Het mag niet worden gebruikt in een native app, omdat client_secrets niet betrouwbaar kan worden opgeslagen op apparaten. Het is vereist voor web-apps en web API's, die de mogelijkheid hebben om de client_secret veilig op te slaan aan de serverkant. Dit geheim moet worden URL-gecodeerd, voor meer informatie klik [hier](https://tools.ietf.org/html/rfc3986#page-12). |

#### <a name="successful-response"></a>Succesvolle reactie

Een succesvolle tokenreactie ziet eruit als volgt:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parameter     | Beschrijving         |
|---------------|-------------------------------------------------------------|
| `access_token`  | Het gevraagde toegangstoken. De app kan dit token gebruiken om te verifiëren voor de beveiligde bron, zoals een web-API. |
| `token_type`    | Geeft de waarde van het tokentype aan. Het enige type dat Azure AD ondersteunt, is Drager |
| `expires_in`    | Hoe lang het toegangstoken geldig is (in seconden).   |
| `scope`         | De scopes waarvoor de access_token geldig is.    |
| `refresh_token` | Een nieuw OAuth 2.0 refresh token. U moet het oude vernieuwingstoken vervangen door dit nieuw aangeschafte vernieuwingstoken om ervoor te zorgen dat uw vernieuwingstokens zo lang mogelijk geldig blijven. <br> **Let op:** Alleen verstrekt `offline_access` als scope werd gevraagd.|
| `id_token`      | Een niet-ondertekende JSON-webtoken (JWT). De app kan de segmenten van dit token decoderen om informatie op te vragen over de gebruiker die zich heeft aangemeld. De app kan de waarden in de cache bewaren en weergeven, maar mag er niet op vertrouwen voor autorisatie- of beveiligingsgrenzen. Zie voor meer informatie [`id_token reference`](id-tokens.md)over id_tokens de . <br> **Let op:** Alleen verstrekt `openid` als scope werd gevraagd. |

#### <a name="error-response"></a>Foutreactie

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter         | Beschrijving                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Een foutcodetekenreeks die kan worden gebruikt om typen fouten die zich voordoen te classificeren en kan worden gebruikt om op fouten te reageren. |
| `error_description` | Een specifiek foutbericht waarmee een ontwikkelaar de hoofdoorzaak van een verificatiefout kan identificeren.           |
| `error_codes` |Een lijst met STS-specifieke foutcodes die kunnen helpen bij diagnostiek. |
| `timestamp` | Het tijdstip waarop de fout is opgetreden. |
| `trace_id` | Een unieke id voor de aanvraag die kan helpen bij diagnostische gegevens. |
| `correlation_id` | Een unieke id voor de aanvraag die kan helpen bij diagnostische gegevens over onderdelen. |

Zie [Foutcodes voor tokeneindpuntfouten voor](#error-codes-for-token-endpoint-errors)een beschrijving van de foutcodes en de aanbevolen clientactie.
