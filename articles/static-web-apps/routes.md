---
title: Routes in statische Azure-Web Apps
description: Meer informatie over back-end-routerings regels en hoe u routes met rollen kunt beveiligen.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 84067917a43fc7c84770b8852f11622ffe2af930
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629313"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Routes in de preview-versie van statische Web Apps van Azure

Route ring in statische Azure-Web Apps definieert back-end-routerings regels en autorisatie gedrag voor zowel statische inhoud als Api's. De regels worden gedefinieerd als een matrix met regels in het bestand _routes. json_ .

- Het bestand _routes. json_ moet bestaan in de hoofdmap van de map build artefact van de app.
- Regels worden uitgevoerd in de volg orde zoals ze worden weer gegeven in de `routes` matrix.
- De regel evaluatie stopt bij de eerste overeenkomst. Routerings regels worden niet aan elkaar gekoppeld.
- Rollen worden gedefinieerd in het bestand _routes. json_ en gebruikers zijn gekoppeld aan rollen via [uitnodigingen](authentication-authorization.md).
- U hebt volledige controle over de namen van rollen.

Het onderwerp van route ring overlapt aanzienlijk met verificatie-en autorisatie concepten. Lees de hand leiding voor [verificatie en autorisatie](authentication-authorization.md) samen met dit artikel.

## <a name="location"></a>Locatie

Het bestand _routes. json_ moet bestaan in de hoofdmap van de map build artefact van de app. Als uw web-app een build-stap bevat waarmee ingebouwde bestanden van een specifieke map naar de map build-artefact worden gekopieerd, moet het bestand _routes. json_ aanwezig zijn in die specifieke map.

De volgende tabel bevat de juiste locatie voor het plaatsen van uw _routes. json_ -bestand voor een aantal front-end Java script-frameworks en-bibliotheken.

|Framework/bibliotheek | Locatie  |
|---------|----------|
| Angular | _publicatie_   |
| React   | _Open_  |
| Svelte  | _Open_   |
| Vue     | _Open_ |

## <a name="defining-routes"></a>Routes definiëren

Routes worden gedefinieerd in het bestand _routes. json_ als een matrix van route regels voor de `routes` eigenschap. Elke regel bestaat uit een route patroon, samen met een of meer van de optionele regel eigenschappen. Zie het [voor beeld van een routerings bestand](#example-route-file) voor gebruiks voorbeelden.

| Regel eigenschap  | Vereist | Standaardwaarde | Opmerking                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Ja      | N.v.t.          | Het route patroon dat is aangevraagd door de aanroeper.<ul><li>[Joker tekens](#wildcards) worden aan het einde van route paden ondersteund. De routerings _beheerder/ \* _ komt bijvoorbeeld overeen met een wille keurige route onder het pad van de _beheerder_ .<li>Het standaard bestand van een route is _index. html_.</ul>|
| `serve`        | Nee       | N.v.t.          | Hiermee wordt het bestand of het pad gedefinieerd dat door de aanvraag wordt geretourneerd. Het bestandspad en de naam kunnen afwijken van het aangevraagde pad. Als een `serve` waarde is gedefinieerd, wordt het aangevraagde pad gebruikt. |
| `allowedRoles` | Nee       | toegang     | Een matrix met namen van rollen. <ul><li>Geldige tekens zijn `a-z`, `A-Z`, `0-9` en `_`.<li>De ingebouwde rol `anonymous` is van toepassing op alle niet-geverifieerde gebruikers.<li>De ingebouwde rol `authenticated` is van toepassing op elke aangemelde gebruiker.<li>Gebruikers moeten deel uitmaken van ten minste één rol.<li>Rollen worden _op basis van_ elkaar vergeleken. Als een gebruiker zich in een van de vermelde rollen bevindt, wordt de toegang verleend.<li>Afzonderlijke gebruikers zijn gekoppeld aan rollen door middel van [uitnodigingen](authentication-authorization.md).</ul> |
| `statusCode`   | Nee       | 200           | Het antwoord van de [HTTP-status code](https://wikipedia.org/wiki/List_of_HTTP_status_codes) voor de aanvraag. |

## <a name="securing-routes-with-roles"></a>Routes beveiligen met rollen

Routes worden beveiligd door een of meer rolnaams toe te voegen aan de matrix van een regel `allowedRoles` . Zie het [voor beeld van een routerings bestand](#example-route-file) voor gebruiks voorbeelden.

Elke gebruiker maakt standaard deel uit van de ingebouwde `anonymous` rol en alle aangemelde gebruikers zijn lid van de `authenticated` rol. Als u bijvoorbeeld een route wilt beperken naar alleen geverifieerde gebruikers, moet u de ingebouwde `authenticated` rol toevoegen aan de `allowedRoles` matrix.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

U kunt indien nodig nieuwe rollen maken in de `allowedRoles` matrix. Als u een route wilt beperken tot alleen beheerders, kunt u een `administrator` rol definiëren in de `allowedRoles` matrix.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- U hebt volledige controle over de namen van rollen. Er is geen hoofd lijst waaraan uw rollen moeten voldoen.
- Afzonderlijke gebruikers zijn gekoppeld aan rollen via [uitnodigingen](authentication-authorization.md).

## <a name="wildcards"></a>Jokertekens

Joker regels komen overeen met alle aanvragen met een bepaald route patroon. Als u een `serve` waarde in uw regel definieert, wordt het genoemde bestand of pad behandeld als het antwoord.

Als u bijvoorbeeld routes voor een agenda toepassing wilt implementeren, kunt u alle Url's die onder de _kalender_ route vallen, toewijzen om één bestand te gebruiken.

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

Het bestand _Calendar. html_ kan vervolgens client-side routering gebruiken om een andere weer gave te leveren voor URL-variaties zoals `/calendar/january/1` , `/calendar/2020` , en `/calendar/overview` .

U kunt routes ook beveiligen met Joker tekens. In het volgende voor beeld is voor elk bestand dat is aangevraagd onder het pad van de beheerder, een geverifieerde gebruiker die lid is _van de rol_ _Administrator_ .

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

> [!NOTE]
> Aanvragen voor bestanden waarnaar wordt verwezen door een geleverd bestand worden alleen geëvalueerd voor verificatie controles. Bijvoorbeeld: aanvragen voor een CSS-bestand onder een Joker teken worden het CSS-bestand en niet de definitie van het `serve` bestand. Het CSS-bestand wordt geleverd zolang de gebruiker voldoet aan de vereiste verificatie vereisten.

## <a name="fallback-routes"></a>Terugval routes

Front-end Java script frameworks of-bibliotheken zijn vaak afhankelijk van route ring aan client zijde voor het navigeren door Web-apps. Deze routerings regels aan de client zijde voeren de venster locatie van de browser bij zonder aanvragen terug te sturen naar de server. Als u de pagina vernieuwt of rechtstreeks navigeert naar locaties die worden gegenereerd door routerings regels aan de client zijde, is er een terugval route aan de server zijde vereist voor de juiste HTML-pagina.

In het volgende voor beeld wordt een algemene terugval route weer gegeven:

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

De terugval route moet als laatste worden weer gegeven in uw routerings regels, omdat alle aanvragen die niet door eerder gedefinieerde regels zijn onderschept, worden onderschept.

## <a name="redirects"></a>Omleidingen

U kunt de HTTP-status codes [301](https://en.wikipedia.org/wiki/HTTP_301) en [302](https://en.wikipedia.org/wiki/HTTP_302) gebruiken om aanvragen van de ene route om te leiden naar een andere.

De volgende regel maakt bijvoorbeeld een omleiding van 301 van _Old-page. html_ naar _New-page. html_.

```json
{
  "route": "/old-page.html",
  "serve": "/new-page.html",
  "statusCode": 301
}
```

Omleidingen werken ook met paden die geen afzonderlijke bestanden definiëren.

```json
{
  "route": "/about-us",
  "serve": "/about",
  "statusCode": 301
}
```

## <a name="custom-error-pages"></a>Aangepaste foutpagina's

Gebruikers kunnen een aantal verschillende situaties tegen komen die ertoe kunnen leiden dat er een fout optreedt. Met de `platformErrorOverrides` matrix kunt u een aangepaste ervaring bieden als reactie op deze fouten. Raadpleeg het [voor beeld-route bestand](#example-route-file) voor plaatsing van de matrix in het bestand _routes. json_ .

De volgende tabel bevat een overzicht van de beschik bare platform fout onderdrukkingen:

| Fout type  | HTTP-statuscode | Beschrijving |
|---------|---------|---------|
| `NotFound` | 404  | Er is geen pagina gevonden op de server. |
| `Unauthenticated` | 401 | De gebruiker is niet aangemeld met een [verificatie provider](authentication-authorization.md). |
| `Unauthorized_InsufficientUserInformation` | 401 | Het account van de gebruiker op de verificatie provider is niet geconfigureerd om de vereiste gegevens beschikbaar te stellen. Deze fout kan optreden in situaties zoals wanneer de app de verificatie provider vraagt om het e-mail adres van de gebruiker, maar de gebruiker heeft besloten de toegang tot het e-mail adres te beperken. |
| `Unauthorized_InvalidInvitationLink` | 401 | Een uitnodiging is verlopen of de gebruiker heeft een uitnodigings koppeling gevolgd die voor een andere ontvanger is gegenereerd.  |
| `Unauthorized_MissingRoles` | 401 | De gebruiker is geen lid van een vereiste rol. |
| `Unauthorized_TooManyUsers` | 401 | De site heeft het maximum aantal gebruikers bereikt en de server beperkt verdere toevoegingen. Deze fout wordt weer gegeven aan de client omdat er geen limiet is voor het aantal [uitnodigingen](authentication-authorization.md) dat u kunt genereren, en sommige gebruikers kunnen de uitnodiging nooit accepteren.|
| `Unauthorized_Unknown` | 401 | Er is een onbekend probleem opgetreden bij het verifiëren van de gebruiker. Een oorzaak van deze fout is dat de gebruiker niet wordt herkend omdat deze geen toestemming voor de toepassing heeft verleend.|

## <a name="example-route-file"></a>Voor beeld van een route bestand

In het volgende voor beeld ziet u hoe u routerings regels voor statische inhoud en Api's in een _routes. json_ -bestand bouwt. Sommige routes gebruiken de [systeemmap _/.auth_ ](authentication-authorization.md) die toegang heeft tot de authenticatie-gerelateerde eind punten.

```json
{
  "routes": [
    {
      "route": "/profile",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/admin/*",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/api/admin",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "serve": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": "404"
    },
    {
      "route": "/logout",
      "serve": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "serve": "/calendar.html"
    },
    {
      "route": "/specials",
      "serve": "/deals",
      "statusCode": 301
    }
  ],
  "platformErrorOverrides": [
    {
      "errorType": "NotFound",
      "serve": "/custom-404.html"
    },
    {
      "errorType": "Unauthenticated",
      "statusCode": "301",
      "serve": "/login"
    }
  ]
}
```

In de volgende voor beelden wordt beschreven wat er gebeurt wanneer een aanvraag overeenkomt met een regel.

|Aanvragen naar...  | Resultaat in... |
|---------|---------|---------|
| _/profile_ | Geverifieerde gebruikers worden het _/profile/index.html-_ bestand geleverd. Niet-geverifieerde gebruikers omgeleid naar _/login_. |
| _/admin/reports_ | Geverifieerde gebruikers in de rol _Administrators_ worden het _/Admin/Reports/index.html-_ bestand verwerkt. Geverifieerde _gebruikers die niet in de beheerdersrol_ zijn, worden 401-fout<sup>1</sup>geleverd. Niet-geverifieerde gebruikers omgeleid naar _/login_. |
| _/api/admin_ | Aanvragen van geverifieerde gebruikers in de rol _Administrators_ worden verzonden naar de API. Geverifieerde gebruikers die niet voor komen in de rol _Administrator_ en niet-geverifieerde gebruikers, krijgen een 401-fout. |
| _/customers/contoso_ | Geverifieerde gebruikers die deel uitmaken van de _ \_ Contoso_ -rollen van de _beheerder_ of klanten, worden het _/Customers/contoso/index.html-_ <sup>bestand.</sup> Geverifieerde gebruikers die geen toegang hebben tot de _ \_ Contoso_ -rollen van de _groep Administrators_ of klanten, worden 401-fouten geleverd. Niet-geverifieerde gebruikers omgeleid naar _/login_. |
| _/login_     | Niet-geverifieerde gebruikers worden gevraagd om te verifiëren met GitHub. |
| _/.auth/login/twitter_     | Autorisatie met Twitter is uitgeschakeld. De server reageert met een 404-fout. |
| _/logout_     | Gebruikers worden afgemeld bij een verificatie provider. |
| _/calendar/2020/01_ | De browser wordt het _/Calendar.html_ -bestand geleverd. |
| _/specials_ | De browser wordt omgeleid naar _/deals_. |
| _/unknown-folder_     | Het _/Custom-404.html_ -bestand wordt geleverd. |

<sup>1</sup> u kunt een aangepaste fout pagina opgeven door een `Unauthorized_MissingRoles` regel in de matrix te definiëren `platformErrorOverrides` .

## <a name="restrictions"></a>Beperkingen

- Het bestand _routes. json_ mag niet groter zijn dan 100 KB
- Het bestand _routes. json_ ondersteunt een maximum van 50 afzonderlijke rollen

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verificatie en autorisatie instellen](authentication-authorization.md)
