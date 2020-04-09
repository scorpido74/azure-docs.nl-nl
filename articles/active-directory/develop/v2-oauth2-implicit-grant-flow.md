---
title: Impliciete subsidiestroom oAuth 2.0 - Microsoft-identiteitsplatform | Azure
description: Beveilig apps met één pagina met de impliciete stroom van het Microsoft-identiteitsplatform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4ef8ca9e116328e14182ca77c787befa837041d0
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886225"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft-identiteitsplatform en impliciete subsidiestroom

Met het eindpunt van het Microsoft-identiteitsplatform u gebruikers aanmelden bij uw apps met één pagina met zowel persoonlijke als werk- of schoolaccounts van Microsoft. Single-page en andere JavaScript-apps die voornamelijk in een browser worden uitgevoerd, worden geconfronteerd met een paar interessante uitdagingen als het gaat om authenticatie:

* De beveiligingskenmerken van deze apps verschillen aanzienlijk van traditionele servergebaseerde webtoepassingen.
* Veel autorisatieservers en identiteitsproviders ondersteunen geen CORS-verzoeken.
* Volledige pagina browser omleidingen uit de buurt van de app worden bijzonder invasief voor de gebruikerservaring.

Voor deze toepassingen (Angular, Ember.js, React.js, enzovoort) ondersteunt microsoft identity platform de OAuth 2.0 Implicit Grant flow. De impliciete stroom wordt beschreven in de [OAuth 2.0-specificatie](https://tools.ietf.org/html/rfc6749#section-4.2). Het belangrijkste voordeel is dat de app hiermee tokens van het Microsoft-identiteitsplatform kan krijgen zonder een backend-server-referentie-uitwisseling uit te voeren. Hierdoor kan de app zich aanmelden bij de gebruiker, de sessie onderhouden en tokens naar andere web-API's krijgen, allemaal binnen de JavaScript-code van de client. Er zijn een paar belangrijke veiligheidsoverwegingen om rekening mee te houden bij het gebruik van de impliciete stroom specifiek rond [client](https://tools.ietf.org/html/rfc6749#section-10.3) en [gebruiker imitatie](https://tools.ietf.org/html/rfc6749#section-10.3).

In dit artikel wordt beschreven hoe u rechtstreeks programmeren tegen het protocol in uw toepassing.  Waar mogelijk raden we u aan de ondersteunde Microsoft Authentication Libraries (MSAL) te gebruiken om tokens te [verkrijgen en beveiligde web-API's te bellen.](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)  Kijk ook eens naar de [voorbeeld-apps die MSAL gebruiken.](sample-v2-code.md)

Als u echter liever geen bibliotheek gebruikt in uw app met één pagina en zelf protocolberichten verzendt, volgt u de algemene stappen hieronder.

> [!NOTE]
> Niet alle Azure AD-scenario's en -functies (Azure Active Directory) worden ondersteund door het eindpunt van het Microsoft-identiteitsplatform. Als u wilt bepalen of u het eindpunt van het Microsoft-identiteitsplatform moet gebruiken, leest u over [de beperkingen van het Microsoft-identiteitsplatform.](active-directory-v2-limitations.md)

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Geschikte scenario's voor de Impliciete OAuth2-subsidie

De OAuth2 specificatie verklaart dat de impliciete subsidie is bedacht om user-agent toepassingen mogelijk te maken - dat wil zeggen, JavaScript-toepassingen uitvoeren binnen een browser. Het bepalende kenmerk van dergelijke toepassingen is dat JavaScript-code wordt gebruikt voor toegang tot serverbronnen (meestal een web-API) en voor het bijwerken van de gebruikerservaring van de toepassing dienovereenkomstig. Denk aan toepassingen zoals Gmail of Outlook Web Access: wanneer u een bericht selecteert in uw postvak IN, wordt alleen het berichtvisualisatiepaneel gewijzigd om de nieuwe selectie weer te geven, terwijl de rest van de pagina ongewijzigd blijft. Dit kenmerk staat in contrast met traditionele redirect-gebaseerde webapps, waarbij elke gebruikersinteractie resulteert in een volledige pagina postback en een volledige paginaweergave van de nieuwe serverrespons.

Toepassingen die de JavaScript-gebaseerde benadering van het uiterste nemen, worden toepassingen met één pagina of SA's genoemd. Het idee is dat deze toepassingen alleen een eerste HTML-pagina en bijbehorende JavaScript bevatten, waarbij alle volgende interacties worden aangestuurd door Web API-aanroepen die via JavaScript worden uitgevoerd. Echter, hybride benaderingen, waar de toepassing is meestal postback-driven, maar voert af en toe JS-oproepen, zijn niet ongewoon - de discussie over impliciete stroom gebruik is relevant voor die ook.

Op omleidingen gebaseerde toepassingen beveiligen hun verzoeken doorgaans via cookies, maar die aanpak werkt niet zo goed voor JavaScript-toepassingen. Cookies werken alleen tegen het domein waarvoor ze zijn gegenereerd, terwijl JavaScript-oproepen kunnen worden gericht op andere domeinen. In feite zal dat vaak het geval zijn: denk aan toepassingen die een beroep doen op Microsoft Graph API, Office API, Azure API - allemaal buiten het domein van waaruit de toepassing wordt weergegeven. Een groeiende trend voor JavaScript-toepassingen is om helemaal geen backend te hebben, waarbij 100% afhankelijk is van webAPI's van derden om hun bedrijfsfunctie te implementeren.

Momenteel is de voorkeursmethode voor het beschermen van aanroepen naar een Web-API het gebruik van de OAuth2-tokenbenadering aan toonder, waarbij elke aanroep vergezeld gaat van een OAuth2-toegangstoken. De Web API onderzoekt het binnenkomende toegangstoken en verleent, als het daarin de benodigde scopes vindt, toegang tot de gevraagde bewerking. De impliciete stroom biedt een handig mechanisme voor JavaScript-toepassingen om toegangstokens voor een Web API te verkrijgen, met tal van voordelen met betrekking tot cookies:

* Tokens kunnen betrouwbaar worden verkregen zonder dat er kruisoorsprongsoproepen nodig zijn - verplichte registratie van de omleiding URI naar welke tokens terugkeergaranties zijn dat tokens niet worden verplaatst
* JavaScript-toepassingen kunnen zoveel toegangstokens verkrijgen als ze nodig hebben, voor zoveel web-API's die ze targeten - zonder beperking op domeinen
* HTML5-functies zoals sessie of lokale opslag bieden volledige controle over tokencaching en levenslang beheer, terwijl cookiesbeheer ondoorzichtig is voor de app
* Toegangstokens zijn niet gevoelig voor Cross-site request forgery (CSRF) aanvallen

De impliciete subsidiestroom geeft geen vernieuwingstokens uit, meestal om veiligheidsredenen. Een refresh token is niet zo eng scoped als access tokens, waardoor veel meer macht wordt toegekend, waardoor het veel meer schade toebrengt in het geval het wordt uitgelekt. In de impliciete stroom worden tokens geleverd in de URL, vandaar dat het risico op onderschepping hoger is dan in de autorisatiecodeverlening.

Een JavaScript-toepassing heeft echter een ander mechanisme tot zijn beschikking voor het vernieuwen van toegangstokens zonder herhaaldelijk de gebruiker om referenties te vragen. De toepassing kan een verborgen iframe gebruiken om nieuwe tokenaanvragen uit te voeren tegen het autorisatieeindpunt van Azure AD: zolang de browser nog steeds een actieve sessie heeft (lees: heeft een sessiecookie) tegen het Azure AD-domein, kan de verificatieaanvraag succesvol plaatsvinden zonder dat gebruikersinteractie nodig is.

Dit model geeft de JavaScript-toepassing de mogelijkheid om toegangstokens onafhankelijk te vernieuwen en zelfs nieuwe te verkrijgen voor een nieuwe API (op voorwaarde dat de gebruiker er eerder toestemming voor gaf). Dit voorkomt de extra belasting van het verwerven, onderhouden en beschermen van een artefact met een hoge waarde, zoals een vernieuwingstoken. Het artefact dat de stille verlenging mogelijk maakt, de Azure AD-sessiecookie, wordt buiten de toepassing beheerd. Een ander voordeel van deze aanpak is dat een gebruiker zich kan afmelden bij Azure AD, met behulp van een van de toepassingen die zijn aangemeld bij Azure AD, uitgevoerd in een van de browsertabbladen. Dit resulteert in het verwijderen van de Azure AD-sessiecookie en de JavaScript-toepassing verliest automatisch de mogelijkheid om tokens te verlengen voor de afgemelde gebruiker.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Is de impliciete subsidie geschikt voor mijn app?

De impliciete subsidie brengt meer risico's met zich mee dan andere subsidies en de gebieden waar u op moet letten zijn goed gedocumenteerd (bijvoorbeeld [Misbruik van Toegangstoken om zich voor te doen als resource-eigenaar in Impliciete stroom][OAuth2-Spec-Implicit-Misuse] en [OAuth 2.0-bedreigingsmodel en beveiligingsoverwegingen).][OAuth2-Threat-Model-And-Security-Implications] Het hogere risicoprofiel is echter grotendeels te wijten aan het feit dat het bedoeld is om toepassingen in te schakelen die actieve code uitvoeren, die door een externe bron aan een browser worden geserveerd. Als u een SPA-architectuur plant, geen backend-componenten hebt of van plan bent een web-API aan te roepen via JavaScript, wordt het gebruik van de impliciete stroom voor tokenacquisitie aanbevolen.

Als uw toepassing een native client is, past de impliciete stroom niet goed. Het ontbreken van de Azure AD-sessiecookie in de context van een native client ontneemt uw toepassing de middelen om een langdurige sessie te onderhouden. Dat betekent dat uw toepassing herhaaldelijk de gebruiker zal vragen wanneer u toegangstokens voor nieuwe bronnen verkrijgt.

Als u een webtoepassing ontwikkelt die een backend bevat en een API uit de backendcode verbruikt, past de impliciete stroom ook niet goed. Andere subsidies geven je veel meer macht. De OAuth2-clientreferenties grant biedt bijvoorbeeld de mogelijkheid om tokens te verkrijgen die de machtigingen weerspiegelen die aan de toepassing zelf zijn toegewezen, in tegenstelling tot gebruikersdelegaties. Dit betekent dat de client de mogelijkheid heeft om programmatische toegang tot bronnen te behouden, zelfs wanneer een gebruiker niet actief betrokken is bij een sessie, enzovoort. Niet alleen dat, maar dergelijke subsidies geven hogere zekerheidgaranties. Toegangstokens gaan bijvoorbeeld nooit door de browser van de gebruiker, ze lopen niet het risico te worden opgeslagen in de browsergeschiedenis, enzovoort. De clienttoepassing kan ook sterke verificatie uitvoeren bij het aanvragen van een token.

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

## <a name="protocol-diagram"></a>Protocoldiagram

In het volgende diagram ziet u hoe de volledige impliciete aanmeldingsstroom eruit ziet en de volgende secties beschrijven elke stap in meer detail.

![Diagram met de impliciete aanmeldingsstroom](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>De aanmeldingsaanvraag verzenden

Als u de gebruiker in eerste instantie wilt aanmelden bij uw `id_token` app, u een [OpenID Connect-verificatieverzoek](v2-protocols-oidc.md) verzenden en een antwoord krijgen van het eindpunt van het Microsoft-identiteitsplatform.

> [!IMPORTANT]
> Als u een ID-token en/of een toegangstoken wilt aanvragen, moet de app-registratie in de [Azure-portal - app-registratiepagina](https://go.microsoft.com/fwlink/?linkid=2083908) de bijbehorende impliciete subsidiestroom hebben ingeschakeld, door **ID-tokens** en.of **toegangstokens** te selecteren onder de sectie **Impliciete subsidie.** Als deze niet is ingeschakeld, wordt een `unsupported_response` fout geretourneerd: de opgegeven waarde voor de **invoerparameter 'response_type' is niet toegestaan voor deze client. Verwachte waarde is 'code'**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Als u het aanmelden wilt <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize..</a> testen met de impliciete stroom, klikt u op . Na het aanmelden moet uw browser `https://localhost/myapp/` worden `id_token` doorgestuurd naar een in de adresbalk.
>

| Parameter |  | Beschrijving |
| --- | --- | --- |
| `tenant` | vereist |De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich bij de toepassing kan aanmelden. De toegestane waarden `common` `organizations`zijn `consumers`, , en tenant-id's. Zie [protocolbasics](active-directory-v2-protocols.md#endpoints)voor meer details. |
| `client_id` | vereist | De toepassings-id (client) die de [Pagina Azure-portal - App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) aan uw app heeft toegewezen. |
| `response_type` | vereist |Moet `id_token` voor OpenID Connect aanmelden. Het kan ook `token`de response_type . Als `token` u dit gebruikt, kan uw app onmiddellijk vanaf het geautoriseerde eindpunt een toegangstoken ontvangen zonder dat u een tweede verzoek hoeft in te dienen bij het geautoriseerde eindpunt. Als u `token` de response_type, moet de `scope` parameter een bereik bevatten dat aangeeft voor welke resource het token moet worden opgegeven (bijvoorbeeld user.read on Microsoft Graph).  |
| `redirect_uri` | Aanbevolen |De redirect_uri van uw app, waar verificatiereacties door uw app kunnen worden verzonden en ontvangen. Het moet precies overeenkomen met een van de redirect_uris die u hebt geregistreerd in de portal, behalve dat het moet worden gecodeerd. |
| `scope` | vereist |Een lijst met [scopes](v2-permissions-and-consent.md)die door de ruimte zijn gescheiden. Voor OpenID Connect (id_tokens) moet `openid`het bereik bevatten, wat zich vertaalt naar de machtiging 'Aanmelden' in de toestemmingsgebruikersinterface. Optioneel u ook de `email` `profile` en scopes voor het verkrijgen van toegang tot extra gebruikersgegevens. U ook andere scopes opnemen in dit verzoek om toestemming te vragen voor verschillende bronnen, als er een toegangstoken wordt aangevraagd. |
| `response_mode` | optioneel |Hiermee geeft u de methode op die moet worden gebruikt om het resulterende token terug te sturen naar uw app. Standaard query's voor slechts een access token, maar fragment als het verzoek bevat een id_token. |
| `state` | Aanbevolen |Een waarde die is opgenomen in het verzoek en die ook wordt geretourneerd in het tokenantwoord. Het kan een reeks van alle inhoud die u wenst. Een willekeurig gegenereerde unieke waarde wordt meestal gebruikt voor [het voorkomen van cross-site request vervalsing aanvallen](https://tools.ietf.org/html/rfc6749#section-10.12). De status wordt ook gebruikt om informatie over de status van de gebruiker in de app te coderen voordat de verificatieaanvraag plaatsvond, zoals de pagina of weergave waarop ze zich bevonden. |
| `nonce` | vereist |Een waarde die is opgenomen in het verzoek, gegenereerd door de app, die zal worden opgenomen in de resulterende id_token als een claim. De app kan deze waarde vervolgens verifiëren om token replay-aanvallen te beperken. De waarde is meestal een gerandomiseerde, unieke tekenreeks die kan worden gebruikt om de oorsprong van het verzoek te identificeren. Alleen vereist wanneer een id_token wordt aangevraagd. |
| `prompt` | optioneel |Geeft het type gebruikersinteractie aan dat vereist is. De enige geldige waarden op dit moment zijn 'login', 'none', 'select_account' en 'consent'. `prompt=login`zal de gebruiker dwingen om hun referenties in te voeren op dat verzoek, ontkennen single-sign aan. `prompt=none`is het tegenovergestelde - het zal ervoor zorgen dat de gebruiker niet wordt gepresenteerd met een interactieve prompt dan ook. Als de aanvraag niet in stilte kan worden voltooid via één aanmelding, wordt een fout weergegeven in het eindpunt van het Microsoft-identiteitsplatform. `prompt=select_account`stuurt de gebruiker naar een accountkiezer waar alle accounts die in de sessie worden onthouden, worden weergegeven. `prompt=consent`activeert het dialoogvenster OAuth-toestemming nadat de gebruiker zich heeft aanmeldt en vraagt de gebruiker om machtigingen voor de app te verlenen. |
| `login_hint`  |optioneel |Kan worden gebruikt om vooraf de gebruikersnaam / e-mailadres veld van de aanmeldingspagina voor de gebruiker, als je weet dat hun gebruikersnaam van tevoren. Vaak gebruiken apps deze parameter tijdens de herverificatie, nadat ze de gebruikersnaam `preferred_username` al uit een eerdere aanmelding hebben gehaald met behulp van de claim.|
| `domain_hint` | optioneel |Als dit wordt opgenomen, wordt het detectieproces op basis van e-mail dat door de gebruiker op de aanmeldingspagina wordt uitgevoerd, wat leidt tot een iets meer gestroomlijnde gebruikerservaring. Dit wordt vaak gebruikt voor Line of Business-apps die in één tenant werken, waarbij ze een domeinnaam binnen een bepaalde tenant opgeven.  Hiermee wordt de gebruiker doorgestuurd naar de federatieprovider voor die tenant.  Houd er rekening mee dat gasten zich hierdoor niet aanmelden voor deze toepassing.  |

Op dit punt wordt de gebruiker gevraagd om zijn referenties in te voeren en de verificatie te voltooien. Het eindpunt van het Microsoft-identiteitsplatform zorgt er ook voor dat `scope` de gebruiker heeft ingestemd met de machtigingen die in de queryparameter zijn vermeld. Als de gebruiker heeft ingestemd met **geen** van deze machtigingen, zal het de gebruiker vragen om toestemming te geven voor de vereiste machtigingen. Zie [machtigingen, toestemming en apps met meerdere tenanten voor](v2-permissions-and-consent.md)meer informatie.

Zodra de gebruiker zich verifieert en toestemming verleent, retourneert het `redirect_uri`eindpunt van het Microsoft-identiteitsplatform een antwoord op uw app op de aangegeven , met behulp van de methode die in de `response_mode` parameter is opgegeven.

#### <a name="successful-response"></a>Succesvolle reactie

Een succesvolle `response_mode=fragment` reactie `response_type=id_token+token` met behulp van en ziet er als volgt uit (met regeleinden voor leesbaarheid):

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parameter | Beschrijving |
| --- | --- |
| `access_token` |Inbegrepen `response_type` indien `token`inclusief . Het toegangstoken dat de app heeft aangevraagd. Het toegangstoken mag niet worden gedecodeerd of anderszins worden geïnspecteerd, het moet worden behandeld als een ondoorzichtige tekenreeks. |
| `token_type` |Inbegrepen `response_type` indien `token`inclusief . Zal altijd. `Bearer` |
| `expires_in`|Inbegrepen `response_type` indien `token`inclusief . Geeft het aantal seconden aan dat het token geldig is, voor cachingdoeleinden. |
| `scope` |Inbegrepen `response_type` indien `token`inclusief . Geeft aan voor welk bereik(en) de access_token geldig is. Mogelijk niet alle gevraagde scopes bevatten, als ze niet van toepassing waren op de gebruiker (in het geval van Azure AD-scopes die worden aangevraagd wanneer een persoonlijk account wordt gebruikt om in te loggen). |
| `id_token` | Een gesigneerde JSON Web Token (JWT). De app kan de segmenten van dit token decoderen om informatie op te vragen over de gebruiker die zich heeft aangemeld. De app kan de waarden in de cache in de cache en ze weer te geven, maar het mag niet vertrouwen op hen voor een autorisatie of beveiligingsgrenzen. Zie voor meer informatie [`id_token reference`](id-tokens.md)over id_tokens de . <br> **Let op:** Alleen verstrekt `openid` als scope werd gevraagd. |
| `state` |Als een parameter in de status in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weergegeven. De app moet controleren of de statuswaarden in de aanvraag en het antwoord identiek zijn. |

#### <a name="error-response"></a>Foutreactie

Foutreacties kunnen ook naar `redirect_uri` de app worden verzonden, zodat de app ze op de juiste manier kan afhandelen:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschrijving |
| --- | --- |
| `error` |Een foutcodetekenreeks die kan worden gebruikt om typen fouten die zich voordoen te classificeren en kan worden gebruikt om op fouten te reageren. |
| `error_description` |Een specifiek foutbericht waarmee een ontwikkelaar de hoofdoorzaak van een verificatiefout kan identificeren. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Toegangstokens in stilte op de achtergrond krijgen

Nu u de gebruiker hebt aangemeld bij uw app met één pagina, u in stilte toegangstokens krijgen voor het bellen van web-API's die zijn beveiligd door het Microsoft-identiteitsplatform, zoals het [Microsoft Graph.](https://developer.microsoft.com/graph) Zelfs als u al een `token` token hebt ontvangen met behulp van de response_type, u deze methode gebruiken om tokens naar aanvullende bronnen te verkrijgen zonder dat u de gebruiker opnieuw hoeft om te leiden om zich opnieuw aan te melden.

In de normale OpenID Connect/OAuth-stroom zou u dit doen `/token` door een verzoek in te dienen bij het eindpunt van het Microsoft-identiteitsplatform. Het eindpunt van het Microsoft-identiteitsplatform ondersteunt echter geen CORS-verzoeken, dus het is uitgesloten om ajax-oproepen te doen om tokens te ontvangen en te vernieuwen. In plaats daarvan u de impliciete stroom in een verborgen iframe gebruiken om nieuwe tokens voor andere web-API's te krijgen:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Zie [de aanmeldingsaanvraag verzenden voor](#send-the-sign-in-request)meer informatie over de queryparameters in de URL.

> [!TIP]
> Probeer & het plakken van de onderstaande aanvraag in een browser tabblad! (Vergeet niet om de `login_hint` waarden te vervangen door de juiste waarde voor uw gebruiker)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

Dankzij de `prompt=none` parameter zal dit verzoek onmiddellijk slagen of mislukken en terugkeren naar uw toepassing. Er wordt een succesvol antwoord naar `redirect_uri`uw app verzonden `response_mode` met de aangegeven methode, met behulp van de methode die in de parameter is opgegeven.

#### <a name="successful-response"></a>Succesvolle reactie

Een succesvol `response_mode=fragment` antwoord met de daarop beoogt:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Parameter | Beschrijving |
| --- | --- |
| `access_token` |Inbegrepen `response_type` indien `token`inclusief . Het toegangstoken dat de app heeft aangevraagd, in dit geval voor de Microsoft Graph. Het toegangstoken mag niet worden gedecodeerd of anderszins worden geïnspecteerd, het moet worden behandeld als een ondoorzichtige tekenreeks. |
| `token_type` | Zal altijd. `Bearer` |
| `expires_in` | Geeft het aantal seconden aan dat het token geldig is, voor cachingdoeleinden. |
| `scope` | Geeft aan voor welk bereik(en) de access_token geldig is. Mogelijk niet alle gevraagde scopes bevatten, als ze niet van toepassing waren op de gebruiker (in het geval van Azure AD-scopes die worden aangevraagd wanneer een persoonlijk account wordt gebruikt om in te loggen). |
| `id_token` | Een gesigneerde JSON Web Token (JWT). Inbegrepen `response_type` indien `id_token`inclusief . De app kan de segmenten van dit token decoderen om informatie op te vragen over de gebruiker die zich heeft aangemeld. De app kan de waarden in de cache in de cache en ze weer te geven, maar het mag niet vertrouwen op hen voor een autorisatie of beveiligingsgrenzen. Zie de [ `id_token` referentie voor](id-tokens.md)meer informatie over id_tokens . <br> **Let op:** Alleen verstrekt `openid` als scope werd gevraagd. |
| `state` |Als een parameter in de status in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weergegeven. De app moet controleren of de statuswaarden in de aanvraag en het antwoord identiek zijn. |

#### <a name="error-response"></a>Foutreactie

Foutreacties kunnen ook naar `redirect_uri` de app worden verzonden, zodat deze op de juiste manier kunnen worden verwerkt. In het `prompt=none`geval van , een verwachte fout zal zijn:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beschrijving |
| --- | --- |
| `error` |Een foutcodetekenreeks die kan worden gebruikt om typen fouten die zich voordoen te classificeren en kan worden gebruikt om op fouten te reageren. |
| `error_description` |Een specifiek foutbericht waarmee een ontwikkelaar de hoofdoorzaak van een verificatiefout kan identificeren. |

Als u deze fout in het iframe-verzoek ontvangt, moet de gebruiker zich interactief opnieuw aanmelden om een nieuw token op te halen. U ervoor kiezen om deze zaak te behandelen op welke manier zinvol is voor uw toepassing.

## <a name="refreshing-tokens"></a>Verfrissende tokens

De impliciete subsidie biedt geen vernieuwingstokens. Zowel `id_token`s `access_token`als s vervallen na een korte periode, dus uw app moet bereid zijn om deze tokens periodiek te vernieuwen. Als u een van beide typen token wilt vernieuwen, kunt `prompt=none` u dezelfde verborgen iframe-aanvraag van bovenaf uitvoeren met behulp van de parameter om het gedrag van het identiteitsplatform te beheren. Als u een nieuwe `id_token`wilt ontvangen, `id_token` moet `response_type` `scope=openid`u deze gebruiken `nonce` in de en , evenals een parameter.

## <a name="send-a-sign-out-request"></a>Een afmeldverzoek verzenden

Met de `end_session_endpoint` OpenID Connect kan uw app een verzoek verzenden naar het eindpunt van het Microsoft-identiteitsplatform om de sessie van een gebruiker te beëindigen en cookies te wissen die zijn ingesteld door het eindpunt van het Microsoft-identiteitsplatform. Als u een gebruiker volledig wilt afmelden bij een webtoepassing, moet uw app zijn eigen sessie met de gebruiker beëindigen (meestal door een tokencache te wissen of cookies te laten vallen) en de browser omte leiden naar:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parameter |  | Beschrijving |
| --- | --- | --- |
| `tenant` |vereist |De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich bij de toepassing kan aanmelden. De toegestane waarden `common` `organizations`zijn `consumers`, , en tenant-id's. Zie [protocolbasics](active-directory-v2-protocols.md#endpoints)voor meer details. |
| `post_logout_redirect_uri` | Aanbevolen | De URL waarnaar de gebruiker moet worden geretourneerd nadat de afmelding is voltooid. Deze waarde moet overeenkomen met een van de omleidings-URI's die voor de toepassing zijn geregistreerd. Als deze niet is opgenomen, krijgt de gebruiker een algemeen bericht van het eindpunt van het Microsoft-identiteitsplatform te zien. |

## <a name="next-steps"></a>Volgende stappen

* Ga over de [MSAL JS monsters](sample-v2-code.md) om te beginnen met coderen.
