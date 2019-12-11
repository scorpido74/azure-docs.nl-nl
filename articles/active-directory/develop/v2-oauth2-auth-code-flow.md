---
title: Micro soft Identity platform & OAuth-autorisatie code stroom | Azure
description: Het bouwen van webtoepassingen met behulp van de micro soft Identity platform-implementatie van het OAuth 2,0-verificatie protocol.
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
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58a82caf825daf83ce58dffdf7056e063e74faa6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964375"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Micro soft Identity platform en OAuth 2,0-autorisatie code stroom

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

De OAuth 2,0-autorisatie code toekenning kan worden gebruikt in apps die op een apparaat zijn geïnstalleerd om toegang te krijgen tot beveiligde bronnen, zoals web-Api's. Met de micro soft Identity platform-implementatie van OAuth 2,0 kunt u aanmeldings-en API-toegang toevoegen aan uw mobiele en desktop-apps. Deze hand leiding is taal onafhankelijk en beschrijft hoe u HTTP-berichten verzendt en ontvangt zonder gebruik te maken van de [open source-verificatie bibliotheken van Azure](reference-v2-libraries.md).

In dit artikel wordt beschreven hoe u direct kunt Program meren met het protocol in uw toepassing.  Als dat mogelijk is, kunt u het beste de ondersteunde micro soft-verificatie bibliotheken (MSAL) gebruiken in plaats van [tokens te verkrijgen en beveiligde web-api's](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)aan te roepen.  Bekijk ook de voor beeld- [apps die gebruikmaken van MSAL](sample-v2-code.md).

> [!NOTE]
> Niet alle Azure Active Directory scenario's & functies worden ondersteund door het micro soft Identity platform-eind punt. Lees over [micro soft Identity platform-beperkingen](active-directory-v2-limitations.md)om te bepalen of u het micro soft Identity platform-eind punt moet gebruiken.

De OAuth 2,0-autorisatie code stroom wordt beschreven in [sectie 4,1 van de oauth 2,0-specificatie](https://tools.ietf.org/html/rfc6749). Het wordt gebruikt om verificatie en autorisatie uit te voeren in het meren deel van de app-typen, inclusief [Web apps](v2-app-types.md#web-apps) en [systeem eigen geïnstalleerde apps](v2-app-types.md#mobile-and-native-apps). Met de stroom kunnen apps veilig access_tokens ophalen dat kan worden gebruikt om toegang te krijgen tot resources die zijn beveiligd met het micro soft Identity platform-eind punt.

## <a name="protocol-diagram"></a>Protocol diagram

Op hoog niveau ziet de volledige verificatie stroom voor een systeem eigen/mobiele toepassing er ongeveer als volgt uit:

![OAuth-verificatie code stroom](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>Een autorisatie code aanvragen

De autorisatie code stroom begint met de client die de gebruiker omleidt naar het `/authorize`-eind punt. In deze aanvraag geeft de client aan welke machtigingen moeten worden verkregen van de gebruiker:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
```

> [!TIP]
> Klik op de onderstaande koppeling om deze aanvraag uit te voeren. Nadat u zich hebt aangemeld, moet uw browser worden omgeleid naar `https://localhost/myapp/` met een `code` in de adres balk.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parameter    | Vereist/optioneel | Beschrijving |
|--------------|-------------|--------------|
| `tenant`    | vereist    | De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich kan aanmelden bij de toepassing. De toegestane waarden zijn `common`, `organizations`, `consumers`en Tenant-id's. Zie [basis beginselen van protocollen](active-directory-v2-protocols.md#endpoints)voor meer informatie.  |
| `client_id`   | vereist    | De **client-id** van de toepassing die de [Azure Portal – app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring die aan uw app is toegewezen.  |
| `response_type` | vereist    | Moet `code` voor de autorisatie code stroom bevatten.       |
| `redirect_uri`  | vereist | De redirect_uri van uw app, waar verificatie reacties kunnen worden verzonden en ontvangen door uw app. Het moet exact overeenkomen met een van de redirect_uris die u in de portal hebt geregistreerd, behalve het moet een URL-code ring zijn. Voor systeem eigen & mobiele apps moet u de standaard waarde van `https://login.microsoftonline.com/common/oauth2/nativeclient`gebruiken.   |
| `scope`  | vereist    | Een lijst met door spaties gescheiden [bereiken](v2-permissions-and-consent.md) waarvan u wilt dat de gebruiker toestemming geeft.  Voor de `/authorize` poot van de aanvraag kan dit meerdere resources omvatten, zodat uw app toestemming kan krijgen voor meerdere web-Api's die u wilt aanroepen. |
| `response_mode`   | aanbevolen | Hiermee geeft u de methode op die moet worden gebruikt om het resulterende token terug naar uw app te verzenden. Een van de volgende kan zijn:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` levert de code als een query reeks parameter op de omleidings-URI. Als u een ID-token met behulp van de impliciete stroom aanvraagt, kunt u `query` niet gebruiken zoals opgegeven in de [OpenID Connect-specificatie](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Als u alleen de code wilt aanvragen, kunt u `query`, `fragment`of `form_post`gebruiken. `form_post` voert een bericht met de code naar uw omleidings-URI uit. Zie [OpenID Connect Connect protocol](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)(Engelstalig) voor meer informatie.  |
| `state`                 | aanbevolen | Een waarde die in de aanvraag is opgenomen en die ook wordt geretourneerd in de token reactie. Dit kan een teken reeks zijn van alle inhoud die u wilt. Een wille keurig gegenereerde unieke waarde wordt doorgaans gebruikt om [vervalsing van aanvragen op meerdere sites](https://tools.ietf.org/html/rfc6749#section-10.12)te voor komen. Met de waarde kan ook informatie over de status van de gebruiker in de app worden gecodeerd voordat de verificatie aanvraag is uitgevoerd, zoals de pagina of weer gave waarin ze zich bevonden. |
| `prompt`  | optioneel    | Hiermee wordt het type gebruikers interactie aangegeven dat vereist is. De enige geldige waarden op dit moment zijn `login`, `none`en `consent`.<br/><br/>- `prompt=login` dwingt de gebruiker de referenties op die aanvraag in te voeren, waarbij eenmalige aanmelding wordt genegeerd.<br/>- `prompt=none` het tegenovergestelde is, zorgt er dan voor dat de gebruiker niet bij elke interactieve prompt wordt weer gegeven. Als de aanvraag niet op de achtergrond kan worden voltooid via eenmalige aanmelding, wordt door het micro soft Identity platform-eind punt een `interaction_required` fout geretourneerd.<br/>- `prompt=consent` wordt het dialoog venster OAuth-toestemming geactiveerd nadat de gebruiker zich heeft aangemeld, waarin de gebruiker wordt gevraagd om machtigingen te verlenen aan de app. |
| `login_hint`  | optioneel    | Kan worden gebruikt om het veld gebruikers naam/e-mail adres vooraf in te vullen op de aanmeldings pagina voor de gebruiker, als u de gebruikers naam van tevoren kent. Vaak gebruiken apps deze para meter tijdens het opnieuw verifiëren, waarbij de gebruikers naam al is geëxtraheerd van een eerdere aanmelding met behulp van de `preferred_username` claim.   |
| `domain_hint`  | optioneel    | Dit kan een van `consumers` of `organizations`zijn.<br/><br/>Indien opgenomen, wordt het op e-mail gebaseerde detectie proces overs Laan dat de gebruiker op de aanmeldings pagina doorloopt, waardoor er iets meer gestroomlijnde gebruikers ervaring is. Vaak gebruiken apps deze para meter tijdens het opnieuw verifiëren door de `tid` uit te pakken vanaf een eerdere aanmelding. Als de `tid` claim waarde `9188040d-6c67-4c5b-b112-36a304b66dad`is, moet u `domain_hint=consumers`gebruiken. Gebruik anders `domain_hint=organizations`.  |
| `code_challenge_method` | optioneel    | De methode die wordt gebruikt voor het coderen van de `code_verifier` voor de `code_challenge`-para meter. Dit kan een van de volgende waarden zijn:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Als deze uitsluiting is, wordt ervan uitgegaan dat `code_challenge` geen tekst als `code_challenge` is opgenomen. Micro soft Identity platform ondersteunt zowel `plain` als `S256`. Zie [PKCE RFC](https://tools.ietf.org/html/rfc7636)(Engelstalig) voor meer informatie. |
| `code_challenge`  | optioneel | Wordt gebruikt voor het beveiligen van autorisatie code subsidies via de bewijs code voor code Exchange (PKCE) van een systeem eigen client. Vereist als `code_challenge_method` is opgenomen. Zie [PKCE RFC](https://tools.ietf.org/html/rfc7636)(Engelstalig) voor meer informatie. |

Op dit moment wordt de gebruiker gevraagd om hun referenties in te voeren en de verificatie te volt ooien. Het micro soft Identity platform-eind punt zorgt er ook voor dat de gebruiker heeft ingestemd met de machtigingen die in de `scope` query parameter worden aangegeven. Als de gebruiker niet heeft ingestemd met een van deze machtigingen, wordt de gebruiker gevraagd om toestemming te geven voor de vereiste machtigingen. Hier vindt u meer informatie over [machtigingen, toestemming en apps met meerdere tenants](v2-permissions-and-consent.md).

Zodra de gebruiker zich heeft geverifieerd en toestemming verleent, stuurt het micro soft Identity platform-eind punt een reactie op uw app bij de aangegeven `redirect_uri`, met de methode die is opgegeven in de para meter `response_mode`.

#### <a name="successful-response"></a>Geslaagde reactie

Een geslaagde reactie met `response_mode=query` ziet er als volgt uit:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parameter | Beschrijving  |
|-----------|--------------|
| `code` | Het authorization_code dat door de app is aangevraagd. De app kan de autorisatie code gebruiken om een toegangs token aan te vragen voor de doel bron. Authorization_codes zijn korte tijd en verlopen doorgaans na ongeveer 10 minuten. |
| `state` | Als een para meter State is opgenomen in de aanvraag, moet dezelfde waarde in het antwoord worden weer gegeven. De app moet controleren of de status waarden in de aanvraag en het antwoord identiek zijn. |

#### <a name="error-response"></a>Fout bericht

Fout reacties kunnen ook worden verzonden naar de `redirect_uri` zodat de app deze op de juiste wijze kan afhandelen:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschrijving  |
|----------|------------------|
| `error`  | Een teken reeks voor fout codes die kan worden gebruikt voor het classificeren van typen fouten die optreden en kunnen worden gebruikt om te reageren op fouten. |
| `error_description` | Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Fout codes voor verificatie eindpunt fouten

In de volgende tabel worden de verschillende fout codes beschreven die kunnen worden geretourneerd in de para meter `error` van het fout bericht.

| Foutcode  | Beschrijving    | Client actie   |
|-------------|----------------|-----------------|
| `invalid_request` | Protocol fout, zoals een ontbrekende vereiste para meter. | Corrigeer en verzend de aanvraag opnieuw. Dit is een ontwikkelings fout die doorgaans tijdens de eerste test is opgetreden. |
| `unauthorized_client` | De client toepassing mag geen autorisatie code aanvragen. | Deze fout treedt meestal op wanneer de client toepassing niet is geregistreerd in azure AD of niet is toegevoegd aan de Azure AD-Tenant van de gebruiker. De toepassing kan de gebruiker vragen met instructies voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| `access_denied`  | De resource-eigenaar heeft toestemming geweigerd  | De client toepassing kan de gebruiker hiervan op de hoogte stellen dat deze niet kan door gaan, tenzij de gebruiker ermee akkoord gaat. |
| `unsupported_response_type` | De autorisatie server biedt geen ondersteuning voor het antwoord type in de aanvraag. | Corrigeer en verzend de aanvraag opnieuw. Dit is een ontwikkelings fout die doorgaans tijdens de eerste test is opgetreden.  |
| `server_error`  | Er is een onverwachte fout opgetreden op de server.| Voer de aanvraag opnieuw uit. Deze fouten kunnen worden veroorzaakt door tijdelijke voor waarden. De client toepassing kan bijvoorbeeld verklaren dat het antwoord van de gebruiker is vertraagd tot een tijdelijke fout. |
| `temporarily_unavailable`   | De server is tijdelijk niet actief om de aanvraag af te handelen. | Voer de aanvraag opnieuw uit. De client toepassing kan bijvoorbeeld verklaren dat de reactie van de gebruiker is vertraagd vanwege een tijdelijke voor waarde. |
| `invalid_resource`  | De doel resource is ongeldig omdat deze niet bestaat, Azure AD niet kan worden gevonden of niet juist is geconfigureerd. | Deze fout geeft aan dat de bron, indien aanwezig, niet is geconfigureerd in de Tenant. De toepassing kan de gebruiker vragen met instructies voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| `login_required` | Er zijn te veel of geen gebruikers gevonden | De client heeft een stille authenticatie (`prompt=none`) aangevraagd, maar er is geen enkele gebruiker gevonden. Dit kan betekenen dat er meerdere gebruikers actief zijn in de sessie of dat er zich geen gebruikers bevinden. Hierbij wordt rekening gehouden met de gekozen Tenant (als er bijvoorbeeld twee Azure AD-accounts actief zijn en een Microsoft-account, en `consumers` is gekozen, zal de stille verificatie werken). |
| `interaction_required` | De aanvraag vereist een gebruikers interactie. | Er is een extra verificatie stap of toestemming vereist. Voer de aanvraag opnieuw uit zonder `prompt=none`. |

## <a name="request-an-access-token"></a>Een toegangs token aanvragen

Nu u een authorization_code hebt aangeschaft en toestemming hebt gegeven door de gebruiker, kunt u de `code` voor een `access_token` naar de gewenste resource inwisselen. Dit doet u door een `POST` aanvraag naar het `/token` eind punt te verzenden:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> Probeer deze aanvraag uit te voeren in postman! (Vergeet niet om de `code`te vervangen) [![probeer deze aanvraag uit te voeren in postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parameter  | Vereist/optioneel | Beschrijving     |
|------------|-------------------|----------------|
| `tenant`   | vereist   | De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich kan aanmelden bij de toepassing. De toegestane waarden zijn `common`, `organizations`, `consumers`en Tenant-id's. Zie [basis beginselen van protocollen](active-directory-v2-protocols.md#endpoints)voor meer informatie.  |
| `client_id` | vereist  | De client-ID van de toepassing die de [Azure Portal-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) pagina die is toegewezen aan uw app. |
| `grant_type` | vereist   | Moet worden `authorization_code` voor de autorisatie code stroom.   |
| `scope`      | vereist   | Een lijst met door spaties gescheiden bereiken. De bereiken die in dit gedeelte worden aangevraagd, moeten gelijk zijn aan of een subset zijn van de bereiken die in de eerste poot zijn aangevraagd. De scopes moeten allemaal afkomstig zijn van één resource, samen met OIDC-bereiken (`profile`, `openid``email`). Raadpleeg [machtigingen, toestemming en bereiken](v2-permissions-and-consent.md)voor een gedetailleerdere uitleg van scopes. |
| `code`          | vereist  | Het authorization_code dat u hebt verkregen in het eerste gedeelte van de stroom. |
| `redirect_uri`  | vereist  | Dezelfde redirect_uri waarde die is gebruikt om de authorization_code op te halen. |
| `client_secret` | vereist voor web-apps | Het toepassings geheim dat u hebt gemaakt in de app-registratie portal voor uw app. U moet het toepassings geheim niet gebruiken in een systeem eigen app omdat client_secrets niet betrouwbaar kan worden opgeslagen op apparaten. Het is vereist voor web-apps en Web-Api's, die de mogelijkheid hebben om de client_secret veilig op te slaan aan de server zijde.  Het client geheim moet URL-gecodeerd zijn voordat het wordt verzonden.  |
| `code_verifier` | optioneel  | Hetzelfde code_verifier dat is gebruikt om de authorization_code op te halen. Vereist als PKCE is gebruikt in de aanvraag voor autorisatie code toekenning. Zie [PKCE RFC](https://tools.ietf.org/html/rfc7636)(Engelstalig) voor meer informatie. |

### <a name="successful-response"></a>Geslaagde reactie

Een geslaagd token antwoord ziet er als volgt uit:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parameter     | Beschrijving   |
|---------------|------------------------------|
| `access_token`  | Het aangevraagde toegangs token. De app kan dit token gebruiken om te verifiëren bij de beveiligde bron, zoals een web-API.  |
| `token_type`    | Geeft de waarde van het token type aan. Het enige type dat door Azure AD wordt ondersteund, is Bearer |
| `expires_in`    | Hoe lang het toegangs token geldig is (in seconden). |
| `scope`         | De bereiken waarvoor het access_token geldig is. |
| `refresh_token` | Een OAuth 2,0-vernieuwings token. De app kan dit token gebruiken om aanvullende toegangs tokens te verkrijgen nadat het huidige toegangs token is verlopen. Refresh_tokens lang duren en kunnen worden gebruikt om de toegang tot resources gedurende lange tijd te bewaren. Raadpleeg de [volgende sectie](#refresh-the-access-token)voor meer informatie over het vernieuwen van een toegangs token. <br> **Opmerking:** Alleen opgegeven als `offline_access` bereik is aangevraagd. |
| `id_token`      | Een JSON Web Token (JWT). De app kan de segmenten van deze token decoderen om informatie aan te vragen over de gebruiker die zich heeft aangemeld. De app kan de waarden in de cache opslaan en weer geven, maar deze moet niet afhankelijk zijn van autorisatie of beveiligings grenzen. Zie de [`id_token reference`](id-tokens.md)voor meer informatie over id_tokens. <br> **Opmerking:** Alleen opgegeven als `openid` bereik is aangevraagd. |

### <a name="error-response"></a>Fout bericht

Fout reacties zien er als volgt uit:

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
| `error`       | Een teken reeks voor fout codes die kan worden gebruikt voor het classificeren van typen fouten die optreden en kunnen worden gebruikt om te reageren op fouten. |
| `error_description` | Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |
| `error_codes` | Een lijst met STS-specifieke fout codes die u kunnen helpen bij het diagnosticeren.  |
| `timestamp`   | Het tijdstip waarop de fout is opgetreden. |
| `trace_id`    | Een unieke id voor de aanvraag die kan helpen bij diagnostische gegevens. |
| `correlation_id` | Een unieke id voor de aanvraag die kan helpen bij het diagnosticeren van verschillende onderdelen. |

### <a name="error-codes-for-token-endpoint-errors"></a>Fout codes voor token eindpunt fouten

| Foutcode         | Beschrijving        | Client actie    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Protocol fout, zoals een ontbrekende vereiste para meter. | De aanvraag herstellen en opnieuw verzenden   |
| `invalid_grant`    | De verificatie code of PKCE code Verifier is ongeldig of is verlopen. | Probeer een nieuwe aanvraag naar het `/authorize`-eind punt en controleer of de para meter code_verifier juist is.  |
| `unauthorized_client` | De geverifieerde client is niet gemachtigd om dit type autorisatie toekenning te gebruiken. | Dit gebeurt meestal wanneer de client toepassing niet is geregistreerd in azure AD of niet is toegevoegd aan de Azure AD-Tenant van de gebruiker. De toepassing kan de gebruiker vragen met instructies voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| `invalid_client` | Client verificatie is mislukt.  | De referenties van de client zijn niet geldig. De toepassings beheerder werkt de referenties bij om deze te herstellen.   |
| `unsupported_grant_type` | De autorisatie server biedt geen ondersteuning voor het type autorisatie toekenning. | Wijzig het toekennings type in de aanvraag. Dit type fout moet alleen tijdens de ontwikkeling optreden en tijdens de eerste test worden gedetecteerd. |
| `invalid_resource` | De doel resource is ongeldig omdat deze niet bestaat, Azure AD niet kan worden gevonden of niet juist is geconfigureerd. | Dit geeft aan dat de resource, indien aanwezig, niet is geconfigureerd in de Tenant. De toepassing kan de gebruiker vragen met instructies voor het installeren van de toepassing en het toevoegen aan Azure AD.  |
| `interaction_required` | De aanvraag vereist een gebruikers interactie. Zo is een extra verificatie stap vereist. | Voer de aanvraag opnieuw uit met dezelfde resource.  |
| `temporarily_unavailable` | De server is tijdelijk niet actief om de aanvraag af te handelen. | Voer de aanvraag opnieuw uit. De client toepassing kan bijvoorbeeld verklaren dat de reactie van de gebruiker is vertraagd vanwege een tijdelijke voor waarde. |

## <a name="use-the-access-token"></a>Het toegangs token gebruiken

Nu u een `access_token`hebt aangeschaft, kunt u het token gebruiken in aanvragen voor web-Api's door dit op te nemen in de `Authorization`-header:

> [!TIP]
> Voer deze aanvraag uit in postman! (Vervang eerst de `Authorization` header) [![probeer deze aanvraag uit te voeren in postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Het toegangs token vernieuwen

Access_tokens kort in de weg staan en u moet ze vernieuwen nadat ze verlopen zijn om door te gaan met de toegang tot resources. U kunt dit doen door een andere `POST` aanvraag naar het `/token`-eind punt te verzenden. deze tijd geeft de `refresh_token` in plaats van de `code`.  Vernieuwings tokens zijn geldig voor alle machtigingen die uw client al toestemming heeft gegeven, waardoor een vernieuwings token dat is uitgegeven op een aanvraag voor `scope=mail.read`, kan worden gebruikt om een nieuw toegangs token aan te vragen voor `scope=api://contoso.com/api/UseResource`.  

Voor vernieuwings tokens is geen levens duur opgegeven. De levens duur van vernieuwings tokens is doorgaans relatief lang. In sommige gevallen verloopt het vernieuwen van tokens, worden deze ingetrokken of beschikt over onvoldoende machtigingen voor de gewenste actie. Uw toepassing moet verwachten en fouten afhandelen [die door het eind punt voor token uitgifte zijn geretourneerd](#error-codes-for-token-endpoint-errors) . 

Hoewel het vernieuwen van tokens niet wordt ingetrokken wanneer het wordt gebruikt om nieuwe toegangs tokens te verkrijgen, wordt u verwacht het oude vernieuwings token te negeren. De [OAuth 2,0 spec](https://tools.ietf.org/html/rfc6749#section-6) vertelt: ' de autorisatie server kan een nieuw vernieuwings token uitgeven. in dat geval moet de client het oude vernieuwings token verwijderen en vervangen door het nieuwe vernieuwings token. De autorisatie server kan het oude vernieuwings token intrekken nadat er een nieuw vernieuwings token is uitgegeven aan de client.  

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> Probeer deze aanvraag uit te voeren in postman! (Vergeet niet om de `refresh_token`te vervangen) [![probeer deze aanvraag uit te voeren in postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
> 

| Parameter     |                | Beschrijving        |
|---------------|----------------|--------------------|
| `tenant`        | vereist     | De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich kan aanmelden bij de toepassing. De toegestane waarden zijn `common`, `organizations`, `consumers`en Tenant-id's. Zie [basis beginselen van protocollen](active-directory-v2-protocols.md#endpoints)voor meer informatie.   |
| `client_id`     | vereist    | De **client-id** van de toepassing die de [Azure Portal – app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring die aan uw app is toegewezen. |
| `grant_type`    | vereist    | Moet `refresh_token` zijn voor dit gedeelte van de autorisatie code stroom. |
| `scope`         | vereist    | Een lijst met door spaties gescheiden bereiken. De scopes die in deze leg worden aangevraagd, moeten gelijk zijn aan of een subset zijn van de bereiken die in de oorspronkelijke authorization_code aanvraag poot zijn aangevraagd. Als de scopes die in deze aanvraag zijn opgegeven meerdere bron servers beslaan, retourneert het micro soft Identity platform-eind punt een token voor de resource die in het eerste bereik is opgegeven. Raadpleeg [machtigingen, toestemming en bereiken](v2-permissions-and-consent.md)voor een gedetailleerdere uitleg van scopes. |
| `refresh_token` | vereist    | Het refresh_token dat u hebt verkregen in het tweede gedeelte van de stroom. |
| `client_secret` | vereist voor web-apps | Het toepassings geheim dat u hebt gemaakt in de app-registratie portal voor uw app. Het mag niet worden gebruikt in een systeem eigen app, omdat client_secrets niet betrouwbaar kan worden opgeslagen op apparaten. Het is vereist voor web-apps en Web-Api's, die de mogelijkheid hebben om de client_secret veilig op te slaan aan de server zijde. |

#### <a name="successful-response"></a>Geslaagde reactie

Een geslaagd token antwoord ziet er als volgt uit:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parameter     | Beschrijving         |
|---------------|-------------------------------------------------------------|
| `access_token`  | Het aangevraagde toegangs token. De app kan dit token gebruiken om te verifiëren bij de beveiligde bron, zoals een web-API. |
| `token_type`    | Geeft de waarde van het token type aan. Het enige type dat door Azure AD wordt ondersteund, is Bearer |
| `expires_in`    | Hoe lang het toegangs token geldig is (in seconden).   |
| `scope`         | De bereiken waarvoor het access_token geldig is.    |
| `refresh_token` | Een nieuw OAuth 2,0-vernieuwings token. U moet het oude vernieuwings token vervangen door dit nieuwe vernieuwings token om ervoor te zorgen dat uw vernieuwings tokens zo lang mogelijk geldig blijven. <br> **Opmerking:** Alleen opgegeven als `offline_access` bereik is aangevraagd.|
| `id_token`      | Een niet-ondertekende JSON Web Token (JWT). De app kan de segmenten van deze token decoderen om informatie aan te vragen over de gebruiker die zich heeft aangemeld. De app kan de waarden in de cache opslaan en weer geven, maar deze moet niet afhankelijk zijn van autorisatie of beveiligings grenzen. Zie de [`id_token reference`](id-tokens.md)voor meer informatie over id_tokens. <br> **Opmerking:** Alleen opgegeven als `openid` bereik is aangevraagd. |

#### <a name="error-response"></a>Fout bericht

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
| `error`           | Een teken reeks voor fout codes die kan worden gebruikt voor het classificeren van typen fouten die optreden en kunnen worden gebruikt om te reageren op fouten. |
| `error_description` | Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout.           |
| `error_codes` |Een lijst met STS-specifieke fout codes die u kunnen helpen bij het diagnosticeren. |
| `timestamp` | Het tijdstip waarop de fout is opgetreden. |
| `trace_id` | Een unieke id voor de aanvraag die kan helpen bij diagnostische gegevens. |
| `correlation_id` | Een unieke id voor de aanvraag die kan helpen bij het diagnosticeren van verschillende onderdelen. |

Zie voor een beschrijving van de fout codes en de aanbevolen client actie [fout codes voor token eindpunt fouten](#error-codes-for-token-endpoint-errors).
