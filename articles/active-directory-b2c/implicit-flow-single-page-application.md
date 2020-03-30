---
title: Aanmelding met één pagina met impliciete stroom
titleSuffix: Azure AD B2C
description: Meer informatie over het toevoegen van aanmelding met één pagina met de impliciete stroom van OAuth 2.0 met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37b59c2a23a8f00e8376be2ac4a7b35a6d58aa28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399002"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Aanmelden op één pagina met de impliciete stroom van OAuth 2.0 in Azure Active Directory B2C

Veel moderne toepassingen hebben een app-front-end van één pagina die voornamelijk in JavaScript is geschreven. Vaak wordt de app geschreven met behulp van een framework zoals React, Angular of Vue.js. Apps met één pagina en andere JavaScript-apps die voornamelijk in een browser worden uitgevoerd, hebben een aantal extra uitdagingen voor verificatie:

- De beveiligingskenmerken van deze apps verschillen van traditionele servergebaseerde webtoepassingen.
- Veel autorisatieservers en identiteitsproviders ondersteunen geen CORS-aanvragen (Cross-Origin Resource Sharing).
- Paginagrote browseromleidingen weg van de app kan invasief zijn voor de gebruikerservaring.

Om deze toepassingen te ondersteunen, gebruikt Azure Active Directory B2C (Azure AD B2C) de impliciete oauth 2.0-stroom. De impliciete subsidiestroom van de OAuth 2.0-autorisatie wordt beschreven in [punt 4.2 van de OAuth 2.0-specificatie](https://tools.ietf.org/html/rfc6749). In impliciete stroom ontvangt de app tokens rechtstreeks van het Azure Active Directory-eindpunt (Azure AD) dat het eindpunt autoriseert, zonder dat er een server-naar-server-uitwisseling is. Alle verificatielogica en sessieafhandeling gebeurt volledig in de JavaScript-client met een paginaomleiding of een pop-upvak.

Azure AD B2C breidt de standaard OAuth 2.0 impliciete stroom uit naar meer dan eenvoudige verificatie en autorisatie. Azure AD B2C introduceert de [beleidsparameter](user-flow-overview.md). Met de beleidsparameter u OAuth 2.0 gebruiken om beleidsregels toe te voegen aan uw app, zoals aanmeldings-, aanmeldings- en profielbeheergebruikersstromen. In het voorbeeld HTTP-aanvragen in dit artikel wordt **{tenant}.onmicrosoft.com** als voorbeeld gebruikt. Vervang `{tenant}` door de naam van uw tenant als u er een hebt en ook een gebruikersstroom hebt gemaakt.

De impliciete aanmeldingsstroom lijkt op de volgende figuur. Elke stap wordt later in het artikel in detail beschreven.

![Swimlane-stijl diagram met de impliciete stroom van OpenID Connect](./media/implicit-flow-single-page-application/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Verificatieverzoeken verzenden

Wanneer uw webtoepassing de gebruiker moet verifiëren en een gebruikersstroom `/authorize` moet uitvoeren, kan deze de gebruiker naar het eindpunt leiden. De gebruiker onderneemt actie, afhankelijk van de gebruikersstroom.

In deze aanvraag geeft de client de machtigingen aan die `scope` deze moet verkrijgen van de gebruiker in de parameter en de gebruikersstroom die moet worden uitgevoerd. Als u een gevoel wilt krijgen over hoe het verzoek werkt, probeert u het verzoek in een browser te plakken en het uit te voeren. Vervang `{tenant}` de naam van uw Azure AD B2C-tenant. Vervang `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` de app-id van de toepassing die u eerder in uw tenant hebt geregistreerd. Vervang `{policy}` bijvoorbeeld `b2c_1_sign_in`door de naam van een beleid dat u in uw tenant hebt gemaakt.

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parameter | Vereist | Beschrijving |
| --------- | -------- | ----------- |
|{tenant}| Ja | Naam van uw Azure AD B2C-tenant|
|{beleid}| Ja| De gebruikersstroom die moet worden uitgevoerd. Geef de naam op van een gebruikersstroom die u hebt gemaakt in uw Azure AD B2C-tenant. Bijvoorbeeld: `b2c_1_sign_in`, `b2c_1_sign_up`, `b2c_1_edit_profile`of . |
| client_id | Ja | De toepassings-id die de [Azure-portal](https://portal.azure.com/) aan uw toepassing heeft toegewezen. |
| response_type | Ja | Moet `id_token` voor OpenID Connect aanmelden. Het kan ook het `token`antwoordtype bevatten. Als u `token`deze gebruikt, kan uw app onmiddellijk een toegangstoken ontvangen van het geautoriseerde eindpunt, zonder een tweede verzoek te doen aan het geautoriseerde eindpunt.  Als u `token` het antwoordtype `scope` gebruikt, moet de parameter een bereik bevatten dat aangeeft voor welke resource het token moet worden opgegeven. |
| redirect_uri | Nee | De omleiding uri van uw app, waar authenticatie reacties kunnen worden verzonden en ontvangen door uw app. Het moet precies overeenkomen met een van de omleiding URI's die u geregistreerd in de portal, behalve dat het moet worden URL-gecodeerd. |
| response_mode | Nee | Hiermee geeft u de methode op die moet worden gebruikt om het resulterende token terug te sturen naar uw app.  Voor impliciete stromen `fragment`u. |
| scope | Ja | Een ruimte-gescheiden lijst met scopes. Een enkele scopewaarde geeft Azure AD aan beide machtigingen die worden aangevraagd. Het `openid` bereik geeft een toestemming aan om de gebruiker aan te melden en gegevens over de gebruiker te krijgen in de vorm van ID-tokens. Het `offline_access` bereik is optioneel voor web-apps. Dit geeft aan dat uw app een vernieuwingstoken nodig heeft voor langdurige toegang tot bronnen. |
| state | Nee | Een waarde die is opgenomen in het verzoek en die ook wordt geretourneerd in het tokenantwoord. Het kan een reeks van alle inhoud die u wilt gebruiken. Meestal wordt een willekeurig gegenereerde, unieke waarde gebruikt om cross-site request vervalsingsaanvallen te voorkomen. De status wordt ook gebruikt om informatie over de status van de gebruiker in de app te coderen voordat het verificatieverzoek plaatsvond, zoals de pagina waarop ze zich bevonden. |
| Nonce | Ja | Een waarde die is opgenomen in het verzoek (gegenereerd door de app) die is opgenomen in het resulterende ID-token als een claim. De app kan deze waarde vervolgens verifiëren om token replay-aanvallen te beperken. Meestal is de waarde een gerandomiseerde, unieke tekenreeks die kan worden gebruikt om de oorsprong van het verzoek te identificeren. |
| Prompt | Nee | Het type gebruikersinteractie dat vereist is. Momenteel is `login`de enige geldige waarde . Deze parameter dwingt de gebruiker om zijn referenties op dat verzoek in te voeren. Eenmalige aanmelding treedt niet in werking. |

Op dit moment wordt de gebruiker gevraagd om de werkstroom van het beleid te voltooien. Mogelijk moet de gebruiker zijn gebruikersnaam en wachtwoord invoeren, zich aanmelden met een sociale identiteit, zich aanmelden voor de directory of een ander aantal stappen. Gebruikersacties zijn afhankelijk van de manier waarop de gebruikersstroom is gedefinieerd.

Nadat de gebruiker de gebruikersstroom heeft voltooid, retourneert Azure AD `redirect_uri`een antwoord op uw app tegen de waarde die u hebt gebruikt voor . Het maakt gebruik van `response_mode` de methode die is opgegeven in de parameter. Het antwoord is precies hetzelfde voor elk van de actiescenario's van de gebruiker, onafhankelijk van de gebruikersstroom die is uitgevoerd.

### <a name="successful-response"></a>Succesvolle reactie
Een succesvolle reactie `response_mode=fragment` `response_type=id_token+token` die gebruik maakt van en eruit ziet als volgt, met regeleinden voor leesbaarheid:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parameter | Beschrijving |
| --------- | ----------- |
| access_token | Het toegangstoken dat de app heeft aangevraagd. |
| token_type | De waarde van het tokentype. Het enige type dat Azure AD ondersteunt, is Drager. |
| expires_in | De tijdsduur dat het toegangstoken geldig is (in seconden). |
| scope | De scopes waarvoor het token geldig is. U ook scopes gebruiken om tokens in de cache te plaatsen voor later gebruik. |
| id_token | Het ID-token dat de app heeft aangevraagd. U het ID-token gebruiken om de identiteit van de gebruiker te verifiëren en een sessie met de gebruiker te beginnen. Zie de naslaggids voor Azure [AD B2C-token](tokens-overview.md)voor meer informatie over ID-tokens en de inhoud daarvan. |
| state | Als `state` een parameter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weergegeven. De app moet `state` controleren of de waarden in de aanvraag en het antwoord identiek zijn. |

### <a name="error-response"></a>Foutreactie
Foutreacties kunnen ook naar de omleidinguri worden verzonden, zodat de app ze op de juiste manier kan verwerken:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beschrijving |
| --------- | ----------- |
| error | Een code die wordt gebruikt om typen fouten te classificeren die optreden. |
| error_description | Een specifiek foutbericht waarmee u de hoofdoorzaak van een verificatiefout identificeren. |
| state | Als `state` een parameter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weergegeven. De app moet `state` controleren of de waarden in de aanvraag en het antwoord identiek zijn.|

## <a name="validate-the-id-token"></a>Het ID-token valideren

Het ontvangen van een ID-token is niet voldoende om de gebruiker te verifiëren. Valideer de handtekening van het ID-token en verifieer de claims in het token volgens de vereisten van uw app. Azure AD B2C gebruikt [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) en public key cryptography om tokens te ondertekenen en te controleren of ze geldig zijn.

Veel open-source bibliotheken zijn beschikbaar voor het valideren van JWT's, afhankelijk van de taal die u liever gebruikt. Overweeg om beschikbare open-sourcebibliotheken te verkennen in plaats van uw eigen validatielogica te implementeren. U de informatie in dit artikel gebruiken om u te helpen leren hoe u deze bibliotheken goed gebruiken.

Azure AD B2C heeft een eindpunt met metagegevens van OpenID Connect. Een app kan het eindpunt gebruiken om informatie over Azure AD B2C op te halen tijdens de runtime. Deze informatie omvat eindpunten, tokeninhoud en tokenondertekeningssleutels. Er is een JSON-metagegevensdocument voor elke gebruikersstroom in uw Azure AD B2C-tenant. Het metagegevensdocument voor de b2c_1_sign_in gebruikersstroom in de fabrikamb2c.onmicrosoft.com tenant bevindt zich bijvoorbeeld op:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Een van de eigenschappen van `jwks_uri`dit configuratiedocument is de . De waarde voor dezelfde gebruikersstroom zou zijn:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Als u wilt bepalen van welke gebruikersstroom een ID-token is getekend (en waar u de metagegevens ophalen), hebt u twee opties. Ten eerste wordt de naam `acr` van `id_token`de gebruikersstroom opgenomen in de claim in . Zie de [naslaggids voor Azure AD B2C-token](tokens-overview.md)voor informatie over het ontzeggen van de claims van een ID-token. Uw andere optie is om de gebruikersstroom `state` te coderen in de waarde van de parameter wanneer u de aanvraag uitgeeft. Decodeer vervolgens `state` de parameter om te bepalen welke gebruikersstroom is gebruikt. Beide methoden zijn geldig.

Nadat u het metagegevensdocument hebt verkregen van het eindpunt van de metagegevens van OpenID Connect, u de openbare RSA-256-sleutels (op dit eindpunt) gebruiken om de handtekening van het ID-token te valideren. Er kunnen meerdere sleutels op dit eindpunt op een bepaald moment `kid`worden vermeld, elk geïdentificeerd door een . De header `id_token` van `kid` bevat ook een claim. Het geeft aan welke van deze sleutels is gebruikt om het ID-token te ondertekenen. Zie de [ab2C-tokenverwijzing](tokens-overview.md)voor meer informatie, waaronder het [valideren van tokens.](tokens-overview.md)
<!--TODO: Improve the information on this-->

Nadat u de handtekening van het ID-token hebt gevalideerd, vereisen verschillende claims verificatie. Bijvoorbeeld:

* Valideer de `nonce` claim om token replay-aanvallen te voorkomen. De waarde moet zijn wat u hebt opgegeven in de aanmeldingsaanvraag.
* Valideer de `aud` claim om ervoor te zorgen dat het ID-token is uitgegeven voor uw app. De waarde ervan moet de toepassings-ID van uw app zijn.
* Valideer de `iat` en `exp` claims om ervoor te zorgen dat het ID-token niet is verlopen.

Nog een aantal validaties die u moet uitvoeren, worden in detail beschreven in de [OpenID Connect Core Spec.](https://openid.net/specs/openid-connect-core-1_0.html) Mogelijk wilt u ook aanvullende claims valideren, afhankelijk van uw scenario. Enkele veelvoorkomende validaties zijn:

* Ervoor zorgen dat de gebruiker of organisatie zich heeft aangemeld voor de app.
* Ervoor zorgen dat de gebruiker de juiste autorisatie en bevoegdheden heeft.
* Ervoor zorgen dat er een bepaalde sterkte van verificatie is opgetreden, bijvoorbeeld door Azure Multi-Factor Authentication te gebruiken.

Zie de [naslaggids voor Azure AD B2C-token](tokens-overview.md)voor meer informatie over de claims in een ID-token.

Nadat u het ID-token hebt gevalideerd, u een sessie met de gebruiker beginnen. Gebruik in uw app de claims in het ID-token om informatie over de gebruiker te verkrijgen. Deze informatie kan worden gebruikt voor weergave, records, autorisatie, enzovoort.

## <a name="get-access-tokens"></a>Toegangstokens ontvangen
Als het enige wat uw web-apps hoeft te doen is het uitvoeren van gebruikersstromen, u de volgende paar secties overslaan. De informatie in de volgende secties is alleen van toepassing op web-apps die geverifieerde aanroepen naar een web-API en die worden beschermd door Azure AD B2C.

Nu u de gebruiker hebt aangemeld bij uw app met één pagina, u toegangstokens krijgen voor het aanroepen van web-API's die zijn beveiligd door Azure AD. Zelfs als u al een token `token` hebt ontvangen met behulp van het antwoordtype, u deze methode gebruiken om tokens te verkrijgen voor extra bronnen zonder de gebruiker om te leiden om opnieuw in te loggen.

In een typische web-app-stroom zou `/token` u een verzoek indienen bij het eindpunt. Het eindpunt ondersteunt echter geen CORS-verzoeken, dus het is geen optie om ajax te bellen om een vernieuwingstoken te krijgen. In plaats daarvan u de impliciete stroom in een verborgen HTML-iframe-element gebruiken om nieuwe tokens voor andere web-API's te krijgen. Hier is een voorbeeld, met lijneinden voor leesbaarheid:

```HTTP
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
```

| Parameter | Vereist? | Beschrijving |
| --- | --- | --- |
|{tenant}| Vereist | Naam van uw Azure AD B2C-tenant|
{beleid}| Vereist| De gebruikersstroom die moet worden uitgevoerd. Geef de naam op van een gebruikersstroom die u hebt gemaakt in uw Azure AD B2C-tenant. Bijvoorbeeld: `b2c_1_sign_in`, `b2c_1_sign_up`, `b2c_1_edit_profile`of . |
| client_id |Vereist |De toepassings-id die aan uw app is toegewezen in de [Azure-portal.](https://portal.azure.com) |
| response_type |Vereist |Moet `id_token` voor OpenID Connect aanmelden.  Het kan ook het `token`antwoordtype bevatten. Als u `token` hier gebruikt, kan uw app onmiddellijk een toegangstoken ontvangen van het geautoriseerde eindpunt, zonder een tweede verzoek te doen aan het geautoriseerde eindpunt. Als u `token` het antwoordtype `scope` gebruikt, moet de parameter een bereik bevatten dat aangeeft voor welke resource het token moet worden opgegeven. |
| redirect_uri |Aanbevolen |De omleiding uri van uw app, waar authenticatie reacties kunnen worden verzonden en ontvangen door uw app. Het moet precies overeenkomen met een van de omleiding URI's die u geregistreerd in de portal, behalve dat het moet worden URL-gecodeerd. |
| scope |Vereist |Een ruimte-gescheiden lijst met scopes.  Voor het verkrijgen van tokens, omvatten alle scopes die u nodig hebt voor de beoogde bron. |
| response_mode |Aanbevolen |Hiermee geeft u de methode op die wordt gebruikt om het resulterende token terug te sturen naar uw app. Gebruik voor impliciete `fragment`stroom . Twee andere modi kunnen `query` `form_post`worden opgegeven en , maar werken niet in de impliciete stroom. |
| state |Aanbevolen |Een waarde die is opgenomen in het verzoek dat wordt geretourneerd in het tokenantwoord.  Het kan een reeks van alle inhoud die u wilt gebruiken.  Meestal wordt een willekeurig gegenereerde, unieke waarde gebruikt om cross-site request vervalsingsaanvallen te voorkomen.  De status wordt ook gebruikt om informatie over de status van de gebruiker in de app te coderen voordat de verificatieaanvraag plaatsvond. Bijvoorbeeld de pagina of weergave waarop de gebruiker zich bevond. |
| Nonce |Vereist |Een waarde die is opgenomen in het verzoek, gegenereerd door de app, die is opgenomen in het resulterende ID-token als een claim.  De app kan deze waarde vervolgens verifiëren om token replay-aanvallen te beperken. Meestal is de waarde een gerandomiseerde, unieke tekenreeks die de oorsprong van het verzoek identificeert. |
| Prompt |Vereist |Als u tokens wilt vernieuwen en `prompt=none` ontvangen in een verborgen iframe, gebruikt u om ervoor te zorgen dat het iframe niet vastkomt te zitten op de aanmeldingspagina en onmiddellijk terugkeert. |
| login_hint |Vereist |Als u tokens wilt vernieuwen en ontvangen in een verborgen iframe, neemt u de gebruikersnaam van de gebruiker op in deze hint om onderscheid te maken tussen meerdere sessies die de gebruiker op een bepaald moment zou kunnen hebben. U de gebruikersnaam uit een eerdere aanmelding halen met behulp van de `preferred_username` claim (het `profile` bereik is vereist om de `preferred_username` claim te ontvangen). |
| domain_hint |Vereist |Deze waarde kan `consumers` of `organizations` zijn.  Voor het vernieuwen en krijgen van tokens `domain_hint` in een verborgen iframe, neem de waarde in het verzoek.  Haal `tid` de claim uit het ID-token van een eerdere aanmelding `profile` om te bepalen welke `tid` waarde moet worden gebruikt (het bereik is vereist om de claim te ontvangen). Als `tid` de claimwaarde `9188040d-6c67-4c5b-b112-36a304b66dad` `domain_hint=consumers`is, gebruik .  Gebruik anders `domain_hint=organizations`. |

Door de `prompt=none` parameter in te stellen, slaagt of mislukt deze aanvraag onmiddellijk en keert deze terug naar uw toepassing.  Er wordt een succesvol antwoord naar uw app verzonden met `response_mode` de aangegeven omleidingsURI met behulp van de methode die in de parameter is opgegeven.

### <a name="successful-response"></a>Succesvolle reactie
Een succesvolle reactie `response_mode=fragment` door het gebruik van ziet er als volgt uit:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parameter | Beschrijving |
| --- | --- |
| access_token |Het token dat de app heeft aangevraagd. |
| token_type |Het tokentype zal altijd drager zijn. |
| state |Als `state` een parameter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weergegeven. De app moet `state` controleren of de waarden in de aanvraag en het antwoord identiek zijn. |
| expires_in |Hoe lang het toegangstoken geldig is (in seconden). |
| scope |De scopes waarvoor het toegangstoken geldig is. |

### <a name="error-response"></a>Foutreactie
Foutreacties kunnen ook naar de omleidinguri worden verzonden, zodat de app ze op de juiste manier kan verwerken.  Voor `prompt=none`, een verwachte fout ziet er als volgt uit:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beschrijving |
| --- | --- |
| error |Een foutcodetekenreeks die kan worden gebruikt om typen fouten te classificeren die optreden. U de tekenreeks ook gebruiken om op fouten te reageren. |
| error_description |Een specifiek foutbericht waarmee u de hoofdoorzaak van een verificatiefout identificeren. |

Als u deze fout in het iframe-verzoek ontvangt, moet de gebruiker zich interactief opnieuw aanmelden om een nieuw token op te halen.

## <a name="refresh-tokens"></a>Tokens vernieuwen
ID-tokens en toegangstokens verlopen beide na een korte periode. Uw app moet bereid zijn om deze tokens periodiek te vernieuwen.  Als u een van beide typen token wilt vernieuwen, voert u hetzelfde `prompt=none` verborgen iframe-verzoek uit dat we in een eerder voorbeeld hebben gebruikt, door de parameter te gebruiken om Azure AD-stappen te beheren.  Als u `id_token` een nieuwe waarde `response_type=id_token` wilt `scope=openid`ontvangen, `nonce` moet u een parameter gebruiken en en een parameter gebruiken.

## <a name="send-a-sign-out-request"></a>Een afmeldingsverzoek verzenden
Wanneer u de gebruiker wilt afmelden bij de app, leidt u de gebruiker door naar Azure AD om zich af te melden. Als u de gebruiker niet omleidt, kunnen deze mogelijk opnieuw verifiëren naar uw app zonder hun referenties opnieuw in te voeren omdat deze een geldige aanmeldingssessie met Azure AD heeft.

U de gebruiker `end_session_endpoint` eenvoudig doorverwijzen naar het document dat wordt vermeld in hetzelfde Metagegevensdocument van OpenID Connect dat is beschreven in [Het ID-token valideren.](#validate-the-id-token) Bijvoorbeeld:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parameter | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| {tenant} | Ja | Naam van uw Azure AD B2C-tenant |
| {beleid} | Ja | De gebruikersstroom die u wilt gebruiken om de gebruiker uit te loggen bij uw toepassing. |
| post_logout_redirect_uri | Nee | De URL waarnaar de gebruiker moet worden doorgestuurd nadat deze succesvol is afmeld. Als het niet is opgenomen, toont Azure AD B2C de gebruiker een algemeen bericht. |
| state | Nee | Als `state` een parameter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weergegeven. De toepassing moet `state` controleren of de waarden in de aanvraag en het antwoord identiek zijn. |


> [!NOTE]
> Als u de `end_session_endpoint` gebruiker doorverwijst naar de afzonderlijke aanmeldingsstatus van de gebruiker met Azure AD B2C. Het meldt de gebruiker echter niet uit de sessie van de gebruikers van de aanbieder van sociale identiteit. Als de gebruiker dezelfde identiteitsprovider selecteert tijdens een volgende aanmelding, wordt de gebruiker opnieuw geverifieerd, zonder zijn of haar referenties in te voeren. Als een gebruiker zich wil afmelden bij uw Azure AD B2C-toepassing, betekent dit niet noodzakelijkerwijs dat hij zich volledig wil afmelden bij bijvoorbeeld zijn Facebook-account. Voor lokale accounts wordt de sessie van de gebruiker echter correct beëindigd.
>

## <a name="next-steps"></a>Volgende stappen

### <a name="code-sample-azure-ad-b2c-with-microsoft-authentication-library-for-javascript"></a>Codevoorbeeld: Azure AD B2C met Microsoft-verificatiebibliotheek voor JavaScript

[Toepassing op één pagina die is gebouwd met msal.js voor Azure AD B2C][github-msal-js-example] (GitHub)

Dit voorbeeld op GitHub is bedoeld om u op weg te helpen naar Azure AD B2C in een eenvoudige webtoepassing die is gebouwd met [msal.js][github-msal-js] en met behulp van pop-up-stijl verificatie.

<!-- Links - EXTERNAL -->
[github-msal-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
[github-msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
