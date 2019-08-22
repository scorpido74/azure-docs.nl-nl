---
title: Stroom van autorisatie code-Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het bouwen van web-apps met behulp van Azure AD B2C en OpenID Connect Connect Authentication Protocol.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 50d697f6ddff42690d4d674bd43a01d8a32c313c
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874076"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>OAuth 2,0-autorisatie code stroom in Azure Active Directory B2C

U kunt de OAuth 2,0-autorisatie code toekenning gebruiken in apps die op een apparaat zijn geïnstalleerd om toegang te krijgen tot beveiligde bronnen, zoals web-Api's. Met de Azure Active Directory B2C (Azure AD B2C)-implementatie van OAuth 2,0 kunt u registratie, aanmelding en andere identiteits beheer taken toevoegen aan uw mobiele en desktop-apps. Dit artikel is taal onafhankelijk. In het artikel wordt beschreven hoe u HTTP-berichten verzendt en ontvangt zonder gebruik te maken van open-source bibliotheken.

De OAuth 2,0-autorisatie code stroom wordt beschreven in [sectie 4,1 van de oauth 2,0-specificatie](https://tools.ietf.org/html/rfc6749). U kunt deze gebruiken voor verificatie en autorisatie in de meeste [toepassings typen](active-directory-b2c-apps.md), waaronder webtoepassingen en systeem eigen geïnstalleerde toepassingen. U kunt de OAuth 2,0-autorisatie code stroom gebruiken om veilig toegangs tokens te verkrijgen en tokens te vernieuwen voor uw toepassingen, die kunnen worden gebruikt voor toegang tot bronnen die worden beveiligd door een [autorisatie server](active-directory-b2c-reference-protocols.md).  Met het vernieuwings token kan de client nieuwe toegangs-en vernieuwings tokens verkrijgen zodra het toegangs token is verlopen, doorgaans na een uur.

Dit artikel is gericht op de OAuth 2,0-autorisatie code stroom voor **open bare clients** . Een open bare client is een client toepassing die niet vertrouwd kan worden om de integriteit van een geheim wachtwoord veilig te houden. Dit omvat mobiele apps, bureaublad toepassingen en alle toepassingen die op een apparaat worden uitgevoerd en die toegang moeten krijgen tot tokens.

> [!NOTE]
> Als u identiteits beheer wilt toevoegen aan een web-app met behulp van Azure AD B2C, gebruikt u [OpenID Connect Connect](active-directory-b2c-reference-oidc.md) in plaats van OAuth 2,0.

Azure AD B2C breidt de standaard OAuth 2,0-stromen uit om meer dan eenvoudige verificatie en autorisatie uit te voeren. Hiermee wordt de [gebruikers stroom](active-directory-b2c-reference-policies.md)geïntroduceerd. Met gebruikers stromen kunt u OAuth 2,0 gebruiken om gebruikers ervaringen toe te voegen aan uw toepassing, zoals aanmelden, aanmelden en Profiel beheer. Id-providers die gebruikmaken van het OAuth 2,0-protocol zijn [Amazon](active-directory-b2c-setup-amzn-app.md), [Azure Active Directory](active-directory-b2c-setup-oidc-azure-active-directory.md), [Facebook](active-directory-b2c-setup-fb-app.md), [github](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md)en [LinkedIn](active-directory-b2c-setup-li-app.md).

De HTTP-aanvragen in dit artikel proberen:

1. Vervang `{tenant}` met de naam van uw Azure AD B2C-tenant.
1. Vervang `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` door de app-id van een toepassing die u eerder hebt geregistreerd in uw Azure AD B2C-Tenant.
1. Vervang `{policy}` door de naam van een beleid dat u hebt gemaakt in uw Tenant, bijvoorbeeld `b2c_1_sign_in`.

## <a name="1-get-an-authorization-code"></a>1. Een autorisatie code ophalen
De autorisatie code stroom begint met de client die de gebruiker omleidt naar `/authorize` het eind punt. Dit is het interactieve deel van de stroom, waar de gebruiker actie onderneemt. In deze aanvraag geeft de client in de `scope` para meter de machtigingen op die nodig zijn voor het verkrijgen van de gebruiker. De volgende drie voor beelden (met regel einden voor de Lees baarheid) gebruiken elk een andere gebruikers stroom.


```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
```


| Parameter | Vereist? | Description |
| --- | --- | --- |
|bouw| Vereist | De naam van uw Azure AD B2C-Tenant|
| verslaggev | Vereist | De gebruikers stroom die moet worden uitgevoerd. Geef de naam op van een gebruikers stroom die u hebt gemaakt in uw Azure AD B2C-Tenant. Bijvoorbeeld: `b2c_1_sign_in`, `b2c_1_sign_up`of. `b2c_1_edit_profile` |
| client_id |Vereist |De toepassings-ID die is toegewezen aan uw app in de [Azure Portal](https://portal.azure.com). |
| response_type |Vereist |Het antwoord type, dat voor de `code` autorisatie code stroom moet bevatten. |
| redirect_uri |Vereist |De omleidings-URI van uw app, waar verificatie reacties worden verzonden en ontvangen door uw app. De waarde moet exact overeenkomen met een van de omleidings-Uri's die u in de portal hebt geregistreerd, behalve dat de URL moet worden gecodeerd. |
| bereik |Vereist |Een lijst met door spaties gescheiden bereiken. Een enkele bereik waarde geeft aan Azure Active Directory (Azure AD) beide machtigingen die worden aangevraagd. Het gebruik van de client-ID als het bereik geeft aan dat uw app een toegangs token nodig heeft dat kan worden gebruikt voor uw eigen service of Web-API, die wordt vertegenwoordigd door dezelfde client-ID.  Het `offline_access` bereik geeft aan dat uw app een vernieuwings token nodig heeft voor lange levens toegang tot bronnen. U kunt ook de `openid` scope gebruiken om een id-token aan te vragen bij Azure AD B2C. |
| response_mode |Aanbevolen |De methode die u gebruikt om de resulterende autorisatie code terug te sturen naar uw app. Dit kan `query`, `form_post`, of `fragment`. |
| toestand |Aanbevolen |Een waarde die in de aanvraag is opgenomen en die een teken reeks kan zijn van alle inhoud die u wilt gebruiken. Normaal gesp roken wordt een wille keurig gegenereerde unieke waarde gebruikt om vervalsing van aanvragen op meerdere sites te voor komen. De status wordt ook gebruikt om informatie over de status van de gebruiker in de app te coderen voordat de verificatie aanvraag is opgetreden. Bijvoorbeeld de pagina waarop de gebruiker zich bevond of de gebruikers stroom die werd uitgevoerd. |
| verschijnt |Optioneel |Het type gebruikers interactie dat is vereist. Op dit moment is de enige geldige `login`waarde die de gebruiker in staat stelt hun referenties in te voeren voor deze aanvraag. Eenmalige aanmelding wordt niet van kracht. |

Op dit moment wordt de gebruiker gevraagd om de werk stroom van de gebruikers stroom te volt ooien. Dit kan betekenen dat de gebruiker die de gebruikers naam en het wacht woord invoert, zich aanmeldt met een sociale identiteit, zich aanmeldt voor de Directory of een ander aantal stappen uitvoert. Gebruikers acties zijn afhankelijk van de manier waarop de gebruikers stroom is gedefinieerd.

Nadat de gebruiker de gebruikers stroom heeft voltooid, retourneert Azure AD een reactie op uw app op basis van de waarde die `redirect_uri`u hebt gebruikt voor. Hierbij wordt gebruikgemaakt van de methode `response_mode` die is opgegeven in de para meter. Het antwoord is precies hetzelfde voor elk van de gebruikers actie scenario's, onafhankelijk van de uitgevoerde gebruikers stroom.

Een geslaagd antwoord dat `response_mode=query` er als volgt uitziet:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parameter | Description |
| --- | --- |
| code |De autorisatie code die door de app is aangevraagd. De app kan de autorisatie code gebruiken om een toegangs token voor een doel bron aan te vragen. Autorisatie codes zijn zeer korter. Normaal gesp roken verlopen ze na ongeveer 10 minuten. |
| toestand |Zie de volledige beschrijving in de tabel in de voor gaande sectie. Als een `state` para meter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weer gegeven. De app moet controleren of de `state` waarden in de aanvraag en het antwoord identiek zijn. |

Fout reacties kunnen ook worden verzonden naar de omleidings-URI, zodat de app deze op de juiste wijze kan afhandelen:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Description |
| --- | --- |
| fout |Een teken reeks voor de fout code die u kunt gebruiken om de typen fouten te classificeren die zich voordoen. U kunt de teken reeks ook gebruiken om te reageren op fouten. |
| error_description |Een specifiek fout bericht die u kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |
| toestand |Zie de volledige beschrijving in de voor gaande tabel. Als een `state` para meter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weer gegeven. De app moet controleren of de `state` waarden in de aanvraag en het antwoord identiek zijn. |

## <a name="2-get-a-token"></a>2. Een Token ophalen
Nu u een autorisatie code hebt aangeschaft, kunt u de `code` voor een-token inwisselen voor de beoogde resource door een post-aanvraag naar het `/token` eind punt te verzenden. In Azure AD B2C kunt u [toegangs tokens aanvragen voor andere api's](active-directory-b2c-access-tokens.md#request-a-token) zoals gebruikelijk door hun bereik (en) op te geven in de aanvraag.

U kunt ook een toegangs token aanvragen voor de eigen back-end web-API van uw app door middel van het gebruik van de client-ID van de app als het aangevraagde bereik (wat resulteert in een toegangs token met die client-ID als ' doel groep '):

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parameter | Vereist? | Description |
| --- | --- | --- |
|bouw| Vereist | De naam van uw Azure AD B2C-Tenant|
|verslaggev| Vereist| De gebruikers stroom die is gebruikt om de autorisatie code op te halen. U kunt in deze aanvraag niet een andere gebruikers stroom gebruiken. |
| client_id |Vereist |De toepassings-ID die is toegewezen aan uw app in de [Azure Portal](https://portal.azure.com).|
| grant_type |Vereist |Het type toekenning. Voor de autorisatie code stroom moet het toekennings type zijn `authorization_code`. |
| bereik |Aanbevolen |Een lijst met door spaties gescheiden bereiken. Een enkele Scope waarde geeft aan dat er voor Azure AD beide machtigingen worden aangevraagd. Het gebruik van de client-ID als het bereik geeft aan dat uw app een toegangs token nodig heeft dat kan worden gebruikt voor uw eigen service of Web-API, die wordt vertegenwoordigd door dezelfde client-ID.  Het `offline_access` bereik geeft aan dat uw app een vernieuwings token nodig heeft voor lange levens toegang tot bronnen.  U kunt ook de `openid` scope gebruiken om een id-token aan te vragen bij Azure AD B2C. |
| code |Vereist |De autorisatie code die u hebt verkregen in het eerste gedeelte van de stroom. |
| redirect_uri |Vereist |De omleidings-URI van de toepassing waarvoor u de autorisatie code hebt ontvangen. |

Een geslaagd token antwoord ziet er als volgt uit:

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
| Parameter | Description |
| --- | --- |
| not_before |Het tijdstip waarop het token als geldig wordt beschouwd in de epoche-tijd. |
| token_type |De waarde van het token type. Het enige type dat door Azure AD wordt ondersteund, is Bearer. |
| access_token |De door u aangevraagde ondertekende JSON Web Token (JWT). |
| bereik |De bereiken waarvoor het token geldig is. U kunt scopes ook gebruiken om tokens in de cache op te slaan voor later gebruik. |
| expires_in |De tijds duur dat het token geldig is (in seconden). |
| refresh_token |Een OAuth 2,0-vernieuwings token. De app kan dit token gebruiken om aanvullende tokens te verkrijgen nadat het huidige token verloopt. Vernieuwings tokens zijn lang in het geleefde. U kunt ze gebruiken om de toegang tot resources te bewaren gedurende lange Peri Oden. Zie de [Azure AD B2C-token verwijzing](active-directory-b2c-reference-tokens.md)voor meer informatie. |

Fout berichten zien er als volgt uit:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Description |
| --- | --- |
| fout |Een teken reeks voor de fout code die u kunt gebruiken om de typen fouten te classificeren die zich voordoen. U kunt de teken reeks ook gebruiken om te reageren op fouten. |
| error_description |Een specifiek fout bericht die u kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |

## <a name="3-use-the-token"></a>3. Het token gebruiken
Nu u een toegangs token hebt aangeschaft, kunt u het token gebruiken in aanvragen voor uw back-end-web-api's door dit op te nemen `Authorization` in de header:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Het token vernieuwen
Toegangs tokens en ID-tokens zijn korte duur. Nadat deze zijn verlopen, moet u ze vernieuwen om toegang te blijven houden tot resources. Als u dit wilt doen, moet u een nieuwe `/token` post-aanvraag indienen bij het eind punt. Geef deze keer de `refresh_token` `code`volgende opties op in plaats van:

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&client_secret=JqQX2PNo9bpM0uEihUPzyrh&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Vereist? | Description |
| --- | --- | --- |
|bouw| Vereist | De naam van uw Azure AD B2C-Tenant|
|verslaggev |Vereist |De gebruikers stroom die is gebruikt om het oorspronkelijke vernieuwings token te verkrijgen. U kunt in deze aanvraag niet een andere gebruikers stroom gebruiken. |
| client_id |Vereist |De toepassings-ID die is toegewezen aan uw app in de [Azure Portal](https://portal.azure.com). |
| client_secret |Vereist |De client_secret die is gekoppeld aan uw client_id in de [Azure Portal](https://portal.azure.com). |
| grant_type |Vereist |Het type toekenning. Voor dit gedeelte van de autorisatie code stroom moet het toekennings type zijn `refresh_token`. |
| bereik |Aanbevolen |Een lijst met door spaties gescheiden bereiken. Een enkele Scope waarde geeft aan dat er voor Azure AD beide machtigingen worden aangevraagd. Het gebruik van de client-ID als het bereik geeft aan dat uw app een toegangs token nodig heeft dat kan worden gebruikt voor uw eigen service of Web-API, die wordt vertegenwoordigd door dezelfde client-ID.  Het `offline_access` bereik geeft aan dat uw app een vernieuwings token nodig heeft voor lange levens toegang tot bronnen.  U kunt ook de `openid` scope gebruiken om een id-token aan te vragen bij Azure AD B2C. |
| redirect_uri |Optioneel |De omleidings-URI van de toepassing waarvoor u de autorisatie code hebt ontvangen. |
| refresh_token |Vereist |Het oorspronkelijke vernieuwings token dat u hebt verkregen in het tweede gedeelte van de stroom. |

Een geslaagd token antwoord ziet er als volgt uit:

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
| Parameter | Description |
| --- | --- |
| not_before |Het tijdstip waarop het token als geldig wordt beschouwd in de epoche-tijd. |
| token_type |De waarde van het token type. Het enige type dat door Azure AD wordt ondersteund, is Bearer. |
| access_token |De ondertekende JWT die u hebt aangevraagd. |
| bereik |De bereiken waarvoor het token geldig is. U kunt de scopes ook gebruiken om tokens in de cache op te slaan voor later gebruik. |
| expires_in |De tijds duur dat het token geldig is (in seconden). |
| refresh_token |Een OAuth 2,0-vernieuwings token. De app kan dit token gebruiken om aanvullende tokens te verkrijgen nadat het huidige token verloopt. Vernieuwings tokens worden lang bewaard en kunnen worden gebruikt om de toegang tot resources te bewaren gedurende lange Peri Oden. Zie de [Azure AD B2C-token verwijzing](active-directory-b2c-reference-tokens.md)voor meer informatie. |

Fout berichten zien er als volgt uit:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Description |
| --- | --- |
| fout |Een teken reeks voor de fout code die u kunt gebruiken om typen fouten te classificeren die zich voordoen. U kunt de teken reeks ook gebruiken om te reageren op fouten. |
| error_description |Een specifiek fout bericht die u kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Uw eigen Azure AD B2C Directory gebruiken
Voer de volgende stappen uit om deze aanvragen zelf te proberen. Vervang de voorbeeld waarden die we in dit artikel hebben gebruikt met uw eigen waarden.

1. [Een Azure AD B2C Directory maken](active-directory-b2c-get-started.md). Gebruik de naam van uw directory in de aanvragen.
2. [Maak een toepassing](active-directory-b2c-app-registration.md) voor het verkrijgen van een toepassings-id en een omleidings-URI. Neem een systeem eigen client op in uw app.
3. [Maak uw gebruikers stromen](active-directory-b2c-reference-policies.md) om de namen van uw gebruikers stroom te verkrijgen.