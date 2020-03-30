---
title: Webaanmelding met OpenID Connect - Azure Active Directory B2C
description: Webtoepassingen maken met het OpenID Connect-verificatieprotocol in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 6640ab1660e6499a97a8c990a0001d5fbae4e997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264386"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Webaanmelding met OpenID Connect in Azure Active Directory B2C

OpenID Connect is een verificatieprotocol, gebouwd bovenop OAuth 2.0, dat kan worden gebruikt om gebruikers veilig aan te melden bij webapplicaties. Door de Azure Active Directory B2C -implementatie van OpenID Connect (Azure AD B2C) te gebruiken, u aanmeldingen, aanmeldings- en andere identiteitsbeheerervaringen in uw webtoepassingen uitbesteden aan Azure Active Directory (Azure AD). Deze gids laat u zien hoe u dit op een taal-onafhankelijke manier doen. Het beschrijft hoe http-berichten te verzenden en te ontvangen zonder gebruik te maken van een van onze open-source bibliotheken.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) breidt het OAuth 2.0-autorisatieprotocol *uit* voor gebruik als *verificatieprotocol.* Met dit verificatieprotocol u één aanmelding uitvoeren. Het introduceert het concept van een *ID-token,* waarmee de klant de identiteit van de gebruiker kan verifiëren en basisprofielinformatie over de gebruiker kan verkrijgen.

Omdat het OAuth 2.0 uitbreidt, stelt het toepassingen ook in staat om veilig *toegangstokens*te verkrijgen. U toegangstokens gebruiken om toegang te krijgen tot bronnen die zijn beveiligd door een [autorisatieserver.](protocols-overview.md) OpenID Connect wordt aanbevolen als u een webtoepassing bouwt die wordt gehost op een server en toegankelijk is via een browser. Zie het overzicht van [tokens in Azure Active Directory B2C](tokens-overview.md) voor meer informatie over tokens

Azure AD B2C breidt het standaard OpenID Connect-protocol uit om meer te doen dan eenvoudige verificatie en autorisatie. Het introduceert de [parameter user flow](user-flow-overview.md), waarmee u OpenID Connect gebruiken om gebruikerservaringen aan uw toepassing toe te voegen, zoals aanmelding, aanmelding en profielbeheer.

## <a name="send-authentication-requests"></a>Verificatieverzoeken verzenden

Wanneer uw webtoepassing de gebruiker moet verifiëren en een gebruikersstroom `/authorize` moet uitvoeren, kan deze de gebruiker naar het eindpunt leiden. De gebruiker onderneemt actie, afhankelijk van de gebruikersstroom.

In deze aanvraag geeft de client de machtigingen aan die `scope` de client moet verkrijgen van de gebruiker in de parameter en geeft hij de gebruikersstroom op die moet worden uitgevoerd. Als u een gevoel wilt krijgen over hoe het verzoek werkt, probeert u het verzoek in een browser te plakken en het uit te voeren. Vervang `{tenant}` door de naam van uw huurder. Vervang `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` de app-id van de toepassing die u eerder in uw tenant hebt geregistreerd. Wijzig ook de`{policy}`beleidsnaam ( ) in de beleidsnaam `b2c_1_sign_in`die u in uw tenant hebt, bijvoorbeeld .

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parameter | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| {tenant} | Ja | Naam van uw Azure AD B2C-tenant |
| {beleid} | Ja | De gebruikersstroom die moet worden uitgevoerd. Geef de naam op van een gebruikersstroom die u hebt gemaakt in uw Azure AD B2C-tenant. Bijvoorbeeld: `b2c_1_sign_in`, `b2c_1_sign_up`, `b2c_1_edit_profile`of . |
| client_id | Ja | De toepassings-id die de [Azure-portal](https://portal.azure.com/) aan uw toepassing heeft toegewezen. |
| Nonce | Ja | Een waarde die is opgenomen in de aanvraag (gegenereerd door de toepassing) die is opgenomen in het resulterende ID-token als een claim. De toepassing kan deze waarde vervolgens verifiëren om token replay-aanvallen te beperken. De waarde is meestal een gerandomiseerde unieke tekenreeks die kan worden gebruikt om de oorsprong van het verzoek te identificeren. |
| response_type | Ja | Moet een ID-token voor OpenID Connect bevatten. Als uw webtoepassing ook tokens nodig heeft voor `code+id_token`het aanroepen van een web-API, u. |
| scope | Ja | Een ruimte-gescheiden lijst met scopes. Het `openid` bereik geeft een toestemming aan om de gebruiker aan te melden en gegevens over de gebruiker te krijgen in de vorm van ID-tokens. Het `offline_access` bereik is optioneel voor webtoepassingen. Dit geeft aan dat uw toepassing een *vernieuwingstoken* nodig heeft voor uitgebreide toegang tot bronnen. |
| Prompt | Nee | Het type gebruikersinteractie dat vereist is. De enige geldige waarde `login`op dit moment is , die de gebruiker dwingt om hun referenties in te voeren op dat verzoek. |
| redirect_uri | Nee | De `redirect_uri` parameter van uw toepassing, waar authenticatiereacties door uw toepassing kunnen worden verzonden en ontvangen. Het moet precies overeenkomen `redirect_uri` met een van de parameters die u hebt geregistreerd in de Azure-portal, behalve dat het URL-gecodeerd moet zijn. |
| response_mode | Nee | De methode die wordt gebruikt om de resulterende autorisatiecode terug te sturen naar uw aanvraag. Het kan `query`een `form_post`van `fragment`beide zijn, of .  De `form_post` responsmodus wordt aanbevolen voor de beste beveiliging. |
| state | Nee | Een waarde die is opgenomen in het verzoek en die ook is geretourneerd in het tokenantwoord. Het kan een reeks van alle inhoud die u wilt. Een willekeurig gegenereerde unieke waarde wordt meestal gebruikt voor het voorkomen van cross-site request vervalsing aanvallen. De status wordt ook gebruikt om informatie over de status van de gebruiker in de toepassing te coderen voordat de verificatieaanvraag plaatsvond, zoals de pagina waarop ze zich bevonden. |

Op dit moment wordt de gebruiker gevraagd om de workflow te voltooien. Mogelijk moet de gebruiker zijn gebruikersnaam en wachtwoord invoeren, zich aanmelden met een sociale identiteit of zich aanmelden voor de directory. Er kunnen andere stappen zijn, afhankelijk van hoe de gebruikersstroom is gedefinieerd.

Nadat de gebruiker de gebruikersstroom heeft voltooid, wordt een `redirect_uri` antwoord naar uw toepassing geretourneerd met `response_mode` behulp van de methode die is opgegeven in de parameter. Het antwoord is hetzelfde voor elk van de voorgaande gevallen, onafhankelijk van de gebruikersstroom.

Een succesvolle `response_mode=fragment` reactie met zou eruit zien als:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beschrijving |
| --------- | ----------- |
| id_token | Het ID-token dat de toepassing heeft aangevraagd. U het ID-token gebruiken om de identiteit van de gebruiker te verifiëren en een sessie met de gebruiker te beginnen. |
| code | De autorisatiecode die de aanvraag heeft `response_type=code+id_token`aangevraagd, als u deze hebt gebruikt. De toepassing kan de autorisatiecode gebruiken om een toegangstoken voor een doelbron aan te vragen. Autorisatiecodes verlopen meestal na ongeveer 10 minuten. |
| state | Als `state` een parameter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weergegeven. De toepassing moet `state` controleren of de waarden in de aanvraag en het antwoord identiek zijn. |

Foutreacties kunnen ook naar `redirect_uri` de parameter worden verzonden, zodat de toepassing ze op de juiste manier kan verwerken:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beschrijving |
| --------- | ----------- |
| error | Een code die kan worden gebruikt om de typen fouten te classificeren die zich voordoen. |
| error_description | Een specifiek foutbericht waarmee de hoofdoorzaak van een verificatiefout kan worden geïdentificeerd. |
| state | Als `state` een parameter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weergegeven. De toepassing moet `state` controleren of de waarden in de aanvraag en het antwoord identiek zijn. |

## <a name="validate-the-id-token"></a>Het ID-token valideren

Alleen het ontvangen van een ID-token is niet genoeg om de gebruiker te verifiëren. Valideer de handtekening van het ID-token en verifieer de claims in het token volgens de vereisten van uw toepassing. Azure AD B2C gebruikt [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) en public key cryptography om tokens te ondertekenen en te controleren of ze geldig zijn. Er zijn veel open-source bibliotheken die beschikbaar zijn voor het valideren van JWT's, afhankelijk van uw taal van voorkeur. We raden u aan deze opties te verkennen in plaats van uw eigen validatielogica te implementeren.

Azure AD B2C heeft een Endpoint metagegevens van OpenID Connect, waarmee een toepassing informatie over Azure AD B2C tijdens runtime kan krijgen. Deze informatie omvat eindpunten, tokeninhoud en tokenondertekeningssleutels. Er is een JSON-metagegevensdocument voor elke gebruikersstroom in uw B2C-tenant. Het metagegevensdocument voor `b2c_1_sign_in` de `fabrikamb2c.onmicrosoft.com` gebruikersstroom bevindt zich bijvoorbeeld op:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Een van de eigenschappen van `jwks_uri`dit configuratiedocument is , waarvan de waarde voor dezelfde gebruikersstroom zou zijn:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Als u wilt bepalen welke gebruikersstroom is gebruikt bij het ondertekenen van een ID-token (en van waar u de metagegevens krijgen), hebt u twee opties. Ten eerste wordt de naam `acr` van de gebruikersstroom opgenomen in de claim in het ID-token. Uw andere optie is om de gebruikersstroom `state` te coderen in de waarde van de parameter wanneer u de aanvraag uitgeeft en deze vervolgens te decoderen om te bepalen welke gebruikersstroom is gebruikt. Beide methoden zijn geldig.

Nadat u het metagegevensdocument hebt aangeschaft via het eindpunt met metagegevens van OpenID Connect, u de openbare RSA 256-sleutels gebruiken om de handtekening van het ID-token te valideren. Er kunnen meerdere sleutels worden vermeld op dit eindpunt, elk geïdentificeerd door een `kid` claim. De header van het ID-token bevat ook een `kid` claim, die aangeeft welke van deze sleutels is gebruikt om het ID-token te ondertekenen.

Als u de tokens van Azure AD B2C wilt verifiëren, moet u de openbare sleutel genereren met de exponent(e) en modulus(n). U moet bepalen hoe u dit in uw respectievelijke programmeertaal dienovereenkomstig doen. De officiële documentatie over Public Key generatie met het RSA protocol is hier te vinden:https://tools.ietf.org/html/rfc3447#section-3.1

Nadat u de handtekening van het ID-token hebt gevalideerd, zijn er verschillende claims die u moet verifiëren. Bijvoorbeeld:

- Valideer de `nonce` claim om token replay-aanvallen te voorkomen. De waarde moet zijn wat u hebt opgegeven in de aanmeldingsaanvraag.
- Valideer de `aud` claim om ervoor te zorgen dat het ID-token is uitgegeven voor uw toepassing. De waarde ervan moet de toepassings-ID van uw toepassing zijn.
- Valideer de `iat` en `exp` claims om ervoor te zorgen dat het ID-token niet is verlopen.

Er zijn ook nog een aantal validaties die u moet uitvoeren. De validaties worden in detail beschreven in de [OpenID Connect Core Spec](https://openid.net/specs/openid-connect-core-1_0.html). Mogelijk wilt u ook aanvullende claims valideren, afhankelijk van uw scenario. Enkele veelvoorkomende validaties zijn:

- Ervoor zorgen dat de gebruiker/organisatie zich heeft aangemeld voor de toepassing.
- Ervoor zorgen dat de gebruiker de juiste autorisatie/bevoegdheden heeft.
- Ervoor zorgen dat er een bepaalde sterkte van verificatie is opgetreden, zoals Azure Multi-Factor Authentication.

Nadat u het ID-token hebt gevalideerd, u een sessie met de gebruiker beginnen. U de claims in het ID-token gebruiken om informatie over de gebruiker in uw toepassing te verkrijgen. Toepassingen voor deze informatie zijn weergave, records en autorisatie.

## <a name="get-a-token"></a>Een token ophalen

Als u uw webtoepassing nodig hebt om alleen gebruikersstromen uit te voeren, u de volgende secties overslaan. Deze secties zijn alleen van toepassing op webtoepassingen die geverifieerde aanroepen naar een web-API en ook worden beschermd door Azure AD B2C.

U de autorisatiecode die u `response_type=code+id_token`hebt verkregen (met behulp van `POST` ) `/token` voor een token inwisselen voor de gewenste bron door een aanvraag naar het eindpunt te sturen. In Azure AD B2C u zoals gebruikelijk [toegangstokens voor andere API's aanvragen](access-tokens.md#request-a-token) door hun bereik(en) in de aanvraag op te geven.

U ook een toegangstoken aanvragen voor de eigen back-end web-API van uw app op basis van de conventie om de client-id van de app als het gevraagde bereik te gebruiken (wat resulteert in een toegangstoken met die client-id als 'doelgroep'):

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| {tenant} | Ja | Naam van uw Azure AD B2C-tenant |
| {beleid} | Ja | De gebruikersstroom die is gebruikt om de autorisatiecode te verkrijgen. U geen andere gebruikersstroom gebruiken in dit verzoek. Voeg deze parameter toe aan de querytekenreeks, niet aan de post-hoofdtekst. |
| client_id | Ja | De toepassings-id die de [Azure-portal](https://portal.azure.com/) aan uw toepassing heeft toegewezen. |
| client_secret | Ja, in Web Apps | Het toepassingsgeheim dat is gegenereerd in de [Azure-portal.](https://portal.azure.com/) Klantgeheimen worden gebruikt in deze stroom voor Web App-scenario's, waarbij de klant een klantgeheim veilig kan opslaan. Voor native app (public client) scenario's, client geheimen kunnen niet veilig worden opgeslagen, threfore niet gebruikt op deze stroom. Als u een klantgeheim gebruikt, u dit op gezette tijden wijzigen. |
| code | Ja | De autorisatiecode die u in het begin van de gebruikersstroom hebt verkregen. |
| grant_type | Ja | Het type subsidie, dat `authorization_code` moet worden voor de vergunningscode stroom. |
| redirect_uri | Ja | De `redirect_uri` parameter van de toepassing waar u de autorisatiecode hebt ontvangen. |
| scope | Nee | Een ruimte-gescheiden lijst met scopes. Het `openid` bereik geeft een toestemming aan om de gebruiker aan te melden en gegevens over de gebruiker te krijgen in de vorm van id_token parameters. Het kan worden gebruikt om tokens te krijgen voor de eigen back-end web-API van uw toepassing, die wordt vertegenwoordigd door dezelfde toepassings-ID als de client. Het `offline_access` bereik geeft aan dat uw toepassing een vernieuwingstoken nodig heeft voor uitgebreide toegang tot bronnen. |

Een succesvolle tokenreactie ziet eruit als volgt:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parameter | Beschrijving |
| --------- | ----------- |
| not_before | Het tijdstip waarop het token als geldig wordt beschouwd, in tijdvaktijd. |
| token_type | De waarde van het tokentype. `Bearer`is het enige type dat wordt ondersteund. |
| access_token | Het ondertekende JWT-token dat u hebt aangevraagd. |
| scope | De scopes waarvoor het token geldig is. |
| expires_in | De tijdsduur dat het toegangstoken geldig is (in seconden). |
| refresh_token | Een OAuth 2.0 refresh token. De toepassing kan dit token gebruiken om extra tokens te verkrijgen nadat het huidige token is verlopen. Vernieuwingstokens kunnen worden gebruikt om toegang tot bronnen voor langere tijd te behouden. Het `offline_access` bereik moet zijn gebruikt in zowel de autorisatie- als tokenaanvragen om een vernieuwingstoken te ontvangen. |

Foutreacties zien eruit als volgt:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beschrijving |
| --------- | ----------- |
| error | Een code die kan worden gebruikt om typen fouten die zich voordoen te classificeren. |
| error_description | Een bericht dat kan helpen bij het identificeren van de hoofdoorzaak van een verificatiefout. |

## <a name="use-the-token"></a>Het token gebruiken

Nu u een toegangstoken hebt aangeschaft, u het token gebruiken in aanvragen voor uw `Authorization` back-end web-API's door het in de koptekst op te nemen:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Het token vernieuwen

ID-tokens verlopen in een korte periode. Vernieuw de tokens nadat ze zijn verlopen om toegang te kunnen blijven krijgen tot bronnen. U een token vernieuwen `POST` door `/token` een ander verzoek in te dienen bij het eindpunt. Geef deze keer `refresh_token` de parameter `code` op in plaats van de parameter:

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| {tenant} | Ja | Naam van uw Azure AD B2C-tenant |
| {beleid} | Ja | De gebruikersstroom die is gebruikt om het oorspronkelijke vernieuwingstoken te verkrijgen. U geen andere gebruikersstroom gebruiken in dit verzoek. Voeg deze parameter toe aan de querytekenreeks, niet aan de post-hoofdtekst. |
| client_id | Ja | De toepassings-id die de [Azure-portal](https://portal.azure.com/) aan uw toepassing heeft toegewezen. |
| client_secret | Ja, in Web Apps | Het toepassingsgeheim dat is gegenereerd in de [Azure-portal.](https://portal.azure.com/) Klantgeheimen worden gebruikt in deze stroom voor Web App-scenario's, waarbij de klant een klantgeheim veilig kan opslaan. Voor native app (openbare client) scenario's, client geheimen kunnen niet veilig worden opgeslagen, threfore niet gebruikt op dit gesprek. Als u een klantgeheim gebruikt, u dit op gezette tijden wijzigen. |
| grant_type | Ja | Het type subsidie, dat een vernieuwingstoken moet zijn voor dit deel van de autorisatiecodestroom. |
| refresh_token | Ja | Het oorspronkelijke vernieuwingstoken dat is aangeschaft in het tweede deel van de stroom. Het `offline_access` bereik moet worden gebruikt in zowel de autorisatie- als tokenaanvragen om een vernieuwingstoken te ontvangen. |
| redirect_uri | Nee | De `redirect_uri` parameter van de toepassing waar u de autorisatiecode hebt ontvangen. |
| scope | Nee | Een ruimte-gescheiden lijst met scopes. Het `openid` bereik geeft een toestemming aan om de gebruiker aan te melden en gegevens over de gebruiker te krijgen in de vorm van ID-tokens. Het kan worden gebruikt om tokens te verzenden naar de eigen back-end web-API van uw toepassing, die wordt vertegenwoordigd door dezelfde toepassings-ID als de client. Het `offline_access` bereik geeft aan dat uw toepassing een vernieuwingstoken nodig heeft voor uitgebreide toegang tot bronnen. |

Een succesvolle tokenreactie ziet eruit als volgt:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parameter | Beschrijving |
| --------- | ----------- |
| not_before | Het tijdstip waarop het token als geldig wordt beschouwd, in tijdvaktijd. |
| token_type | De waarde van het tokentype. `Bearer`is het enige type dat wordt ondersteund. |
| access_token | De ondertekende JWT token dat werd aangevraagd. |
| scope | De ruimte waarvoor het token geldig is. |
| expires_in | De tijdsduur dat het toegangstoken geldig is (in seconden). |
| refresh_token | Een OAuth 2.0 refresh token. De toepassing kan dit token gebruiken om extra tokens te verkrijgen nadat het huidige token is verlopen. Vernieuwingstokens kunnen worden gebruikt om toegang tot bronnen voor langere tijd te behouden. |

Foutreacties zien eruit als volgt:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beschrijving |
| --------- | ----------- |
| error | Een code die kan worden gebruikt om typen fouten die zich voordoen te classificeren. |
| error_description | Een bericht dat kan helpen bij het identificeren van de hoofdoorzaak van een verificatiefout. |

## <a name="send-a-sign-out-request"></a>Een afmeldingsverzoek verzenden

Wanneer u de gebruiker uit de toepassing wilt afmelden, volstaat het niet om de cookies van de toepassing te wissen of de sessie op een andere manier met de gebruiker te beëindigen. Stuur de gebruiker door naar Azure AD B2C om zich af te melden. Als u dit niet doet, kan de gebruiker zich mogelijk opnieuw verifiëren bij uw toepassing zonder zijn of haar referenties opnieuw in te voeren.

Als u de gebruiker wilt afmelden, leidt u de gebruiker door naar het `end_session` eindpunt dat wordt vermeld in het eerder beschreven document metagegevens van OpenID Connect:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Parameter | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| {tenant} | Ja | Naam van uw Azure AD B2C-tenant |
| {beleid} | Ja | De gebruikersstroom die u wilt gebruiken om de gebruiker uit te loggen bij uw toepassing. |
| id_token_hint| Nee | Een eerder uitgegeven ID-token om door te geven aan het afmeldpunt als een hint over de huidige geverifieerde sessie van de eindgebruiker met de client. Het `id_token_hint` zorgt `post_logout_redirect_uri` ervoor dat het een geregistreerde antwoord-URL is in de instellingen van uw Azure AD B2C-toepassingsinstellingen. |
| client_id | Nee* | De toepassings-id die de [Azure-portal](https://portal.azure.com/) aan uw toepassing heeft toegewezen.<br><br>\**Dit is vereist `Application` bij het gebruik van isolatie SSO-configuratie `No`en _Vereisen ID-token_ in afmelden aanvraag is ingesteld op .* |
| post_logout_redirect_uri | Nee | De URL waarnaar de gebruiker moet worden doorgestuurd nadat deze succesvol is afmeld. Als het niet is opgenomen, toont Azure AD B2C de gebruiker een algemeen bericht. Tenzij u `id_token_hint`een , moet u deze URL niet registreren als een antwoord-URL in uw Azure AD B2C-toepassingsinstellingen. |
| state | Nee | Als `state` een parameter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weergegeven. De toepassing moet `state` controleren of de waarden in de aanvraag en het antwoord identiek zijn. |

### <a name="secure-your-logout-redirect"></a>Uw afmeldingsomleiding beveiligen

Na het afmelden wordt de gebruiker doorgestuurd `post_logout_redirect_uri` naar de URI die in de parameter is opgegeven, ongeacht de antwoord-URL's die voor de toepassing zijn opgegeven. Als een geldige `id_token_hint` geldige waarde echter wordt doorgegeven, controleert `post_logout_redirect_uri` Azure AD B2C of de waarde van een van de geconfigureerde omleidings-URI's van de toepassing overeenkomt voordat de omleiding wordt uitgevoerd. Als er geen overeenkomende antwoord-URL is geconfigureerd voor de toepassing, wordt een foutbericht weergegeven en wordt de gebruiker niet omgeleid.

### <a name="external-identity-provider-sign-out"></a>Afmelden voor externe identiteitsprovider

Als u de `end_session` gebruiker naar het eindpunt leidt, wordt een deel van de enkele aanmeldingsstatus van de gebruiker gewist met Azure AD B2C, maar wordt de gebruiker niet uitdeinen voor de IDP-sessie (Social Identity Provider). Als de gebruiker dezelfde IDP selecteert tijdens een volgende aanmelding, wordt deze opnieuw geverifieerd zonder hun referenties in te voeren. Als een gebruiker zich wil afmelden bij de toepassing, betekent dit niet noodzakelijkerwijs dat hij zich wil afmelden bij zijn/haar Facebook-account. Als er echter lokale accounts worden gebruikt, eindigt de sessie van de gebruiker naar behoren.
