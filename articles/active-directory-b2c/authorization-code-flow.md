---
title: Machtigingscodestroom - Azure Active Directory B2C | Microsoft Documenten
description: Meer informatie over het bouwen van web-apps met behulp van Azure AD B2C en OpenID Connect-verificatieprotocol.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 8248ca0abb1d633786b09b894bcd6b1089ab2d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260889"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>OAuth 2.0-autorisatiecodestroom in Azure Active Directory B2C

U de OAuth 2.0-autorisatiecodeverlening gebruiken in apps die op een apparaat zijn geïnstalleerd om toegang te krijgen tot beveiligde bronnen, zoals web-API's. Door de Azure Active Directory B2C -implementatie van OAuth 2.0 (Azure Active Directory B2C) te gebruiken, u aanmeldings-, aanmeldings- en andere identiteitsbeheertaken toevoegen aan uw mobiele en bureaublad-apps. Dit artikel is taalonafhankelijk. In het artikel beschrijven we hoe we HTTP-berichten verzenden en ontvangen zonder gebruik te maken van open-source bibliotheken.

De OAuth 2.0 autorisatiecodestroom wordt beschreven in [punt 4.1 van de OAuth 2.0-specificatie](https://tools.ietf.org/html/rfc6749). U het gebruiken voor verificatie en autorisatie in de meeste [toepassingstypen,](application-types.md)waaronder webtoepassingen en native geïnstalleerde toepassingen. U de OAuth 2.0-autorisatiecodestroom gebruiken om veilig toegangstokens te verkrijgen en tokens voor uw toepassingen te vernieuwen, die kunnen worden gebruikt om toegang te krijgen tot bronnen die zijn beveiligd door een [autorisatieserver.](protocols-overview.md)  Met het vernieuwingstoken kan de client nieuwe toegangstokens aanschaffen (en vernieuwen) zodra het toegangstoken verloopt, meestal na een uur.

Dit artikel richt zich op de **openbare klanten** OAuth 2.0 autorisatie code flow. Een openbare client is een clienttoepassing die niet kan worden vertrouwd om de integriteit van een geheim wachtwoord veilig te handhaven. Dit omvat mobiele apps, desktoptoepassingen en in wezen elke toepassing die op een apparaat wordt uitgevoerd en toegangstokens moet krijgen.

> [!NOTE]
> Als u identiteitsbeheer wilt toevoegen aan een web-app met Azure AD B2C, gebruikt u [OpenID Connect](openid-connect.md) in plaats van OAuth 2.0.

Azure AD B2C breidt de standaard OAuth 2.0-stromen uit om meer te doen dan eenvoudige verificatie en autorisatie. Het introduceert de [gebruikersstroom.](user-flow-overview.md) Met gebruikersstromen u OAuth 2.0 gebruiken om gebruikerservaringen aan uw toepassing toe te voegen, zoals aanmelding, aanmelding en profielbeheer. Identiteitsproviders die het OAuth 2.0-protocol gebruiken, zijn [Amazon,](identity-provider-amazon.md) [Azure Active Directory,](identity-provider-azure-ad-single-tenant.md) [Facebook,](identity-provider-facebook.md) [GitHub,](identity-provider-github.md) [Google](identity-provider-google.md)en [LinkedIn](identity-provider-linkedin.md).

Ga als volgt te werk om de HTTP-aanvragen in dit artikel uit te proberen:

1. Vervang `{tenant}` de naam van uw Azure AD B2C-tenant.
1. Vervang `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` de app-id van een toepassing die u eerder hebt geregistreerd in uw Azure AD B2C-tenant.
1. Vervang `{policy}` bijvoorbeeld `b2c_1_sign_in`door de naam van een beleid dat u in uw tenant hebt gemaakt.

## <a name="1-get-an-authorization-code"></a>1. Een autorisatiecode krijgen
De autorisatiecodestroom begint met de client `/authorize` die de gebruiker naar het eindpunt leidt. Dit is het interactieve deel van de stroom, waar de gebruiker actie onderneemt. In deze aanvraag geeft de `scope` client in de parameter de machtigingen aan die deze van de gebruiker moet verkrijgen. De volgende drie voorbeelden (met regeleinden voor leesbaarheid) gebruiken elk een andere gebruikersstroom.


```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
```


| Parameter | Vereist? | Beschrijving |
| --- | --- | --- |
|{tenant}| Vereist | Naam van uw Azure AD B2C-tenant|
| {beleid} | Vereist | De gebruikersstroom die moet worden uitgevoerd. Geef de naam op van een gebruikersstroom die u hebt gemaakt in uw Azure AD B2C-tenant. Bijvoorbeeld: `b2c_1_sign_in`, `b2c_1_sign_up`, `b2c_1_edit_profile`of . |
| client_id |Vereist |De toepassings-id die aan uw app is toegewezen in de [Azure-portal.](https://portal.azure.com) |
| response_type |Vereist |Het antwoordtype, dat `code` moet zijn opgenomen voor de machtigingscodestroom. |
| redirect_uri |Vereist |De omleiding uri van uw app, waar authenticatie reacties worden verzonden en ontvangen door uw app. Het moet precies overeenkomen met een van de omleiding URI's die u geregistreerd in de portal, behalve dat het moet worden URL-gecodeerd. |
| scope |Vereist |Een ruimte-gescheiden lijst met scopes. Een enkele scopewaarde geeft azure ad (Azure AD) aan zowel de machtigingen die worden aangevraagd. Als u de client-id als bereik gebruikt, geeft u aan dat uw app een toegangstoken nodig heeft dat kan worden gebruikt tegen uw eigen service of web-API, vertegenwoordigd door dezelfde client-ID.  Het `offline_access` bereik geeft aan dat uw app een vernieuwingstoken nodig heeft voor langdurige toegang tot bronnen. U het `openid` bereik ook gebruiken om een ID-token aan te vragen bij Azure AD B2C. |
| response_mode |Aanbevolen |De methode die u gebruikt om de resulterende autorisatiecode terug te sturen naar uw app. Het kan `query` `form_post`, `fragment`, of . |
| state |Aanbevolen |Een waarde die in de aanvraag is opgenomen en die een reeks inhoud kan zijn die u wilt gebruiken. Meestal wordt een willekeurig gegenereerde unieke waarde gebruikt, om cross-site request vervalsingsaanvallen te voorkomen. De status wordt ook gebruikt om informatie over de status van de gebruiker in de app te coderen voordat de verificatieaanvraag plaatsvond. Bijvoorbeeld de pagina waarop de gebruiker zich bevond of de gebruikersstroom die werd uitgevoerd. |
| Prompt |Optioneel |Het type gebruikersinteractie dat vereist is. Momenteel is `login`de enige geldige waarde , waardoor de gebruiker zijn referenties op dat verzoek moet invoeren. Eenmalige aanmelding wordt niet van kracht. |

Op dit moment wordt de gebruiker gevraagd om de werkstroom van de gebruikersstroom te voltooien. Dit kan betekenen dat de gebruiker zijn gebruikersnaam en wachtwoord invoert, zich aanmeldt met een sociale identiteit, zich aanmeldt voor de directory of een ander aantal stappen. Gebruikersacties zijn afhankelijk van de manier waarop de gebruikersstroom is gedefinieerd.

Nadat de gebruiker de gebruikersstroom heeft voltooid, retourneert Azure AD `redirect_uri`een antwoord op uw app tegen de waarde die u hebt gebruikt voor . Het maakt gebruik van `response_mode` de methode die is opgegeven in de parameter. Het antwoord is precies hetzelfde voor elk van de actiescenario's van de gebruiker, onafhankelijk van de gebruikersstroom die is uitgevoerd.

Een succesvolle reactie `response_mode=query` die gebruikt ziet er als volgt uit:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parameter | Beschrijving |
| --- | --- |
| code |De autorisatiecode die de app heeft aangevraagd. De app kan de autorisatiecode gebruiken om een toegangstoken voor een doelbron aan te vragen. Autorisatiecodes zijn van zeer korte duur. Meestal verlopen ze na ongeveer 10 minuten. |
| state |Zie de volledige beschrijving in de tabel in de vorige sectie. Als `state` een parameter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weergegeven. De app moet `state` controleren of de waarden in de aanvraag en het antwoord identiek zijn. |

Foutreacties kunnen ook naar de omleidinguri worden verzonden, zodat de app ze op de juiste manier kan verwerken:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beschrijving |
| --- | --- |
| error |Een foutcodetekenreeks die u gebruiken om de typen fouten te classificeren die zich voordoen. U de tekenreeks ook gebruiken om op fouten te reageren. |
| error_description |Een specifiek foutbericht waarmee u de hoofdoorzaak van een verificatiefout identificeren. |
| state |Bekijk de volledige beschrijving in de vorige tabel. Als `state` een parameter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weergegeven. De app moet `state` controleren of de waarden in de aanvraag en het antwoord identiek zijn. |

## <a name="2-get-a-token"></a>2. Een token
Nu u een autorisatiecode hebt verkregen, `code` kunt u de voor een token inwisselen `/token` voor de beoogde bron door een post-verzoek naar het eindpunt te sturen. In Azure AD B2C u zoals gebruikelijk [toegangstokens aanvragen voor andere API's](access-tokens.md#request-a-token) door hun bereik(en) in de aanvraag op te geven.

U ook een toegangstoken aanvragen voor de eigen back-end web-API van uw app op basis van de conventie om de client-id van de app als het gevraagde bereik te gebruiken (wat resulteert in een toegangstoken met die client-id als 'doelgroep'):

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parameter | Vereist? | Beschrijving |
| --- | --- | --- |
|{tenant}| Vereist | Naam van uw Azure AD B2C-tenant|
|{beleid}| Vereist| De gebruikersstroom die is gebruikt om de autorisatiecode te verkrijgen. U geen andere gebruikersstroom gebruiken in dit verzoek. |
| client_id |Vereist |De toepassings-id die aan uw app is toegewezen in de [Azure-portal.](https://portal.azure.com)|
| client_secret | Ja, in Web Apps | Het toepassingsgeheim dat is gegenereerd in de [Azure-portal.](https://portal.azure.com/) Klantgeheimen worden gebruikt in deze stroom voor Web App-scenario's, waarbij de klant een klantgeheim veilig kan opslaan. Voor native app -scenario's (openbare client) kunnen klantgegevens niet veilig worden opgeslagen en worden daarom niet gebruikt in dit gesprek. Als u een klantgeheim gebruikt, u dit op gezette tijden wijzigen. |
| grant_type |Vereist |Het type subsidie. Voor de stroom van de autorisatiecode moet het subsidietype . `authorization_code` |
| scope |Aanbevolen |Een ruimte-gescheiden lijst met scopes. Een enkele scopewaarde geeft Azure AD aan beide machtigingen die worden aangevraagd. Als u de client-id als bereik gebruikt, geeft u aan dat uw app een toegangstoken nodig heeft dat kan worden gebruikt tegen uw eigen service of web-API, vertegenwoordigd door dezelfde client-ID.  Het `offline_access` bereik geeft aan dat uw app een vernieuwingstoken nodig heeft voor langdurige toegang tot bronnen.  U het `openid` bereik ook gebruiken om een ID-token aan te vragen bij Azure AD B2C. |
| code |Vereist |De autorisatiecode die u in het eerste deel van de stroom hebt verkregen. |
| redirect_uri |Vereist |De omleiding uri van de toepassing waar u de autorisatiecode hebt ontvangen. |

Een succesvolle tokenreactie ziet er als volgt uit:

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
| --- | --- |
| not_before |Het tijdstip waarop het token als geldig wordt beschouwd, in tijdvaktijd. |
| token_type |De waarde van het tokentype. Het enige type dat Azure AD ondersteunt, is Drager. |
| access_token |De ondertekende JSON Web Token (JWT) die u hebt aangevraagd. |
| scope |De scopes waarvoor het token geldig is. U ook scopes gebruiken om tokens in de cache te plaatsen voor later gebruik. |
| expires_in |De tijdsduur dat het token geldig is (in seconden). |
| refresh_token |Een OAuth 2.0 refresh token. De app kan dit token gebruiken om extra tokens te verkrijgen nadat het huidige token is verlopen. Vernieuwingstokens zijn van lange duur. U ze gebruiken om gedurende langere tijd toegang tot bronnen te behouden. Zie de [naslaggids voor Azure AD B2C-token voor](tokens-overview.md)meer informatie . |

Foutreacties zien er als volgt uit:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beschrijving |
| --- | --- |
| error |Een foutcodetekenreeks die u gebruiken om de typen fouten te classificeren die zich voordoen. U de tekenreeks ook gebruiken om op fouten te reageren. |
| error_description |Een specifiek foutbericht waarmee u de hoofdoorzaak van een verificatiefout identificeren. |

## <a name="3-use-the-token"></a>3. Gebruik het token
Nu u een toegangstoken hebt aangeschaft, u het token gebruiken in aanvragen voor uw `Authorization` back-end web-API's door het in de koptekst op te nemen:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Het token vernieuwen
Toegangstokens en ID-tokens zijn van korte duur. Nadat ze zijn verlopen, moet u ze vernieuwen om toegang te blijven krijgen tot bronnen. Om dit te doen, dient `/token` u een ander post-verzoek in bij het eindpunt. Geef deze keer `refresh_token` de `code`in plaats van de:

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Vereist? | Beschrijving |
| --- | --- | --- |
|{tenant}| Vereist | Naam van uw Azure AD B2C-tenant|
|{beleid} |Vereist |De gebruikersstroom die is gebruikt om het oorspronkelijke vernieuwingstoken te verkrijgen. U geen andere gebruikersstroom gebruiken in dit verzoek. |
| client_id |Vereist |De toepassings-id die aan uw app is toegewezen in de [Azure-portal.](https://portal.azure.com) |
| client_secret | Ja, in Web Apps | Het toepassingsgeheim dat is gegenereerd in de [Azure-portal.](https://portal.azure.com/) Klantgeheimen worden gebruikt in deze stroom voor Web App-scenario's, waarbij de klant een klantgeheim veilig kan opslaan. Voor native app -scenario's (openbare client) kunnen klantgegevens niet veilig worden opgeslagen en worden daarom niet gebruikt in dit gesprek. Als u een klantgeheim gebruikt, u dit op gezette tijden wijzigen. |
| grant_type |Vereist |Het type subsidie. Voor dit deel van de autorisatiecodestroom `refresh_token`moet het subsidietype . |
| scope |Aanbevolen |Een ruimte-gescheiden lijst met scopes. Een enkele scopewaarde geeft Azure AD aan beide machtigingen die worden aangevraagd. Als u de client-id als bereik gebruikt, geeft u aan dat uw app een toegangstoken nodig heeft dat kan worden gebruikt tegen uw eigen service of web-API, vertegenwoordigd door dezelfde client-ID.  Het `offline_access` bereik geeft aan dat uw app een vernieuwingstoken nodig heeft voor langdurige toegang tot bronnen.  U het `openid` bereik ook gebruiken om een ID-token aan te vragen bij Azure AD B2C. |
| redirect_uri |Optioneel |De omleiding uri van de toepassing waar u de autorisatiecode hebt ontvangen. |
| refresh_token |Vereist |Het oorspronkelijke vernieuwingstoken dat u in het tweede deel van de stroom hebt aangeschaft. |

Een succesvolle tokenreactie ziet er als volgt uit:

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
| --- | --- |
| not_before |Het tijdstip waarop het token als geldig wordt beschouwd, in tijdvaktijd. |
| token_type |De waarde van het tokentype. Het enige type dat Azure AD ondersteunt, is Drager. |
| access_token |De ondertekende JWT die u hebt aangevraagd. |
| scope |De scopes waarvoor het token geldig is. U de scopes ook gebruiken om tokens in de cache te plaatsen voor later gebruik. |
| expires_in |De tijdsduur dat het token geldig is (in seconden). |
| refresh_token |Een OAuth 2.0 refresh token. De app kan dit token gebruiken om extra tokens te verkrijgen nadat het huidige token is verlopen. Vernieuwingstokens zijn van lange duur en kunnen worden gebruikt om toegang tot bronnen voor langere tijd te behouden. Zie de [naslaggids voor Azure AD B2C-token voor](tokens-overview.md)meer informatie . |

Foutreacties zien er als volgt uit:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beschrijving |
| --- | --- |
| error |Een foutcodetekenreeks die u gebruiken om typen fouten te classificeren die optreden. U de tekenreeks ook gebruiken om op fouten te reageren. |
| error_description |Een specifiek foutbericht waarmee u de hoofdoorzaak van een verificatiefout identificeren. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Uw eigen Azure AD B2C-map gebruiken
Voer de volgende stappen uit om deze verzoeken zelf uit te proberen. Vervang de voorbeeldwaarden die we in dit artikel hebben gebruikt door uw eigen waarden.

1. [Een Azure AD B2C-map maken.](tutorial-create-tenant.md) Gebruik de naam van uw map in de aanvragen.
2. [Maak een toepassing](tutorial-register-applications.md) om een toepassings-ID en een omleiding URI te verkrijgen. Neem een native client op in uw app.
3. [Maak uw gebruikersstromen](user-flow-overview.md) om uw gebruikersstroomnamen te verkrijgen.
