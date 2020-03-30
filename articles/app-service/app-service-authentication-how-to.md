---
title: Geavanceerd gebruik van AuthN/AuthO
description: Leer de verificatie- en autorisatiefunctie in App-service aanpassen voor verschillende scenario's en ontvang gebruikersclaims en verschillende tokens.
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d57b196bf95ebdf31bc459ad4b9d718fd32ca495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280831"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Geavanceerd gebruik van verificatie en autorisatie in Azure App Service

In dit artikel ziet u hoe u de ingebouwde [verificatie en autorisatie in App Service](overview-authentication-authorization.md)aanpassen en de identiteit van uw toepassing beheren. 

Zie een van de volgende zelfstudies om snel aan de slag te gaan:

* [Zelfstudie: gebruikers end-to-end verifiëren en autoriseren in Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)
* [Zelfstudie: gebruikers end-to-end verifiëren en autoriseren in Azure App Service voor Linux](containers/tutorial-auth-aad.md)
* [Uw app configureren voor aanmelding met Azure Active Directory](configure-authentication-provider-aad.md)
* [Uw app configureren voor aanmelding met Facebook](configure-authentication-provider-facebook.md)
* [Uw app configureren voor aanmelding met Google](configure-authentication-provider-google.md)
* [Uw app configureren voor aanmelding met een Microsoft Account](configure-authentication-provider-microsoft.md)
* [Uw app configureren voor aanmelding met Twitter](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Meerdere aanmeldingsproviders gebruiken

De portalconfiguratie biedt geen turn-key manier om meerdere aanmeldingsproviders aan uw gebruikers te presenteren (zoals zowel Facebook als Twitter). Het is echter niet moeilijk om de functionaliteit aan uw app toe te voegen. De stappen worden als volgt beschreven:

Configureer eerst op de pagina **Verificatie / Autorisatie** in de Azure-portal elk van de identiteitsprovider die u wilt inschakelen.

Selecteer **Anonieme verzoeken toestaan (geen actie)** in **Actie die u wilt uitvoeren wanneer het verzoek niet is geverifieerd.**

Voeg op de aanmeldingspagina, de navigatiebalk of een andere locatie van uw app een aanmeldingskoppeling toe aan elk van de providers die u hebt ingeschakeld (`/.auth/login/<provider>`). Bijvoorbeeld:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Wanneer de gebruiker op een van de koppelingen klikt, wordt de respectievelijke aanmeldingspagina geopend om zich bij de gebruiker aan te melden.

Als u de gebruiker na aanmelding wilt omleiden naar een aangepaste URL, gebruikt u de `post_login_redirect_url` parameter querytekenreeks (niet te verwarren met de CONFIGURATIE van uri omleiden in uw identiteitsprovider). Als u bijvoorbeeld de `/Home/Index` gebruiker na aanmelding naar de gebruiker wilt navigeren, gebruikt u de volgende HTML-code:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Tokens valideren van providers

In een op de client gerichte aanmelding wordt de toepassing handmatig bij de provider ingediend en wordt het verificatietoken vervolgens ingediend bij App Service voor validatie (zie [Verificatiestroom).](overview-authentication-authorization.md#authentication-flow) Deze validatie zelf geeft u eigenlijk geen toegang tot de gewenste app-bronnen, maar een succesvolle validatie geeft u een sessietoken dat u gebruiken om toegang te krijgen tot app-bronnen. 

Om het providertoken te valideren, moet de App Service-app eerst worden geconfigureerd met de gewenste provider. Bij runtime, nadat u het verificatietoken hebt opgehaald `/.auth/login/<provider>` bij uw provider, plaatst u het token voor validatie. Bijvoorbeeld: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Het tokenformaat varieert enigszins afhankelijk van de provider. Zie de volgende tabel voor meer informatie:

| Waarde van provider | Vereist in aanvraaginstantie | Opmerkingen |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | De `expires_in` accommodatie is optioneel. <br/>Wanneer u het token aanvraagt bij `wl.basic` Live-services, vraagt u altijd het bereik aan. |
| `google` | `{"id_token":"<id_token>"}` | De `authorization_code` accommodatie is optioneel. Wanneer opgegeven, kan het ook optioneel `redirect_uri` worden begeleid door de eigenschap. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Gebruik een geldig [gebruikerstoegangstoken](https://developers.facebook.com/docs/facebook-login/access-tokens) van Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Als het providertoken is gevalideerd, keert `authenticationToken` de API terug met een in de antwoordbody, wat uw sessietoken is. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Zodra u dit sessietoken hebt, hebt u `X-ZUMO-AUTH` toegang tot beveiligde app-bronnen door de koptekst toe te voegen aan uw HTTP-verzoeken. Bijvoorbeeld: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Afmelden bij een sessie

Gebruikers kunnen een afmelding starten `GET` door een verzoek `/.auth/logout` naar het eindpunt van de app te sturen. Het `GET` verzoek doet het volgende:

- Wist verificatiecookies uit de huidige sessie.
- Hiermee verwijdert u de tokens van de huidige gebruiker uit de tokenwinkel.
- Voor Azure Active Directory en Google wordt een afmelding aan de serverzijde uitgevoerd op de identiteitsprovider.

Dit is een voorbeeld van een eenvoudige afmeldingskoppeling op een webpagina:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Standaard leidt een succesvolle afmelding de client `/.auth/logout/done`door naar de URL. U de omleidingspagina na uitloging wijzigen door de `post_logout_redirect_uri` queryparameter toe te voegen. Bijvoorbeeld:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Het wordt aanbevolen dat [u](https://wikipedia.org/wiki/Percent-encoding) de `post_logout_redirect_uri`waarde van .

Wanneer u volledig gekwalificeerde URL's gebruikt, moet de URL worden gehost in hetzelfde domein of geconfigureerd als een toegestane externe omleidings-URL voor uw app. In het volgende voorbeeld `https://myexternalurl.com` wordt u naar het volgende voorbeeld omgeleid naar dat wordt niet gehost in hetzelfde domein:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Voer de volgende opdracht uit in de [Azure Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>URL-fragmenten behouden

Nadat gebruikers zich bij uw app hebben aangemeld, willen ze meestal worden doorgestuurd `/wiki/Main_Page#SectionZ`naar hetzelfde gedeelte van dezelfde pagina, zoals . Omdat [URL-fragmenten](https://wikipedia.org/wiki/Fragment_identifier) `#SectionZ`(bijvoorbeeld) nooit naar de server worden verzonden, worden ze echter niet standaard bewaard nadat de OAuth-aanmelding is voltooid en wordt teruggestuurd naar uw app. Gebruikers krijgen dan een suboptimale ervaring wanneer ze weer naar het gewenste anker moeten navigeren. Deze beperking is van toepassing op alle verificatieoplossingen aan de serverzijde.

In App Service-verificatie u URL-fragmenten bewaren in de OAuth-aanmelding. Stel hiervoor een app-instelling `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` `true`in die wordt aangeroepen als . U dit doen in de [Azure-portal](https://portal.azure.com)of gewoon de volgende opdracht uitvoeren in de [Azure Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Gebruikersclaims openen

App Service geeft claims van gebruikers door aan uw toepassing met behulp van speciale headers. Externe aanvragen mogen deze kopteksten niet instellen, dus ze zijn alleen aanwezig als ze zijn ingesteld door App Service. Enkele voorbeeldkoppen zijn:

* X-MS-CLIENT-PRINCIPAL-NAAM
* X-MS-CLIENT-PRINCIPAL-ID

Code die is geschreven in elke taal of kader kan de informatie die het nodig heeft van deze headers te krijgen. Voor ASP.NET 4.6-apps wordt de **Claimprincipal** automatisch ingesteld met de juiste waarden. ASP.NET Core biedt echter geen verificatiemiddleware die integreert met app service-gebruikersclaims. Zie [MaximeRouiller.Azure.AppService.EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)voor een tijdelijke oplossing.

Uw toepassing kan ook aanvullende gegevens over `/.auth/me`de geverifieerde gebruiker verkrijgen door te bellen. De SDK's van de Mobile Apps-server bieden helpermethoden om met deze gegevens te werken. Zie [De SDK voor Azure Mobile Apps Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)en Werken met de SDK voor [.NET-backendserver voor Azure Mobile Apps voor](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info)meer informatie.

## <a name="retrieve-tokens-in-app-code"></a>Tokens ophalen in app-code

Vanuit uw servercode worden de providerspecifieke tokens geïnjecteerd in de aanvraagheader, zodat u ze gemakkelijk openen. In de volgende tabel worden mogelijke namen van tokenkoppen weergegeven:

| Provider | Koptekstnamen |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook-token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft-account | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Vanuit uw clientcode (zoals een mobiele app of javascript `GET` in `/.auth/me`de browser) stuurt u een HTTP-verzoek naar . De geretourneerde JSON heeft de provider-specifieke tokens.

> [!NOTE]
> Toegangstokens zijn voor toegang tot providerbronnen, dus ze zijn alleen aanwezig als u uw provider configureert met een clientgeheim. Zie Toegangstokens vernieuwen om te zien hoe u vernieuwingstokens ontvangen.

## <a name="refresh-identity-provider-tokens"></a>Tokens voor identiteitsprovider vernieuwen

Wanneer het toegangstoken van uw provider (niet het [sessietoken)](#extend-session-token-expiration-grace-period)verloopt, moet u de gebruiker opnieuw verifiëren voordat u dat token opnieuw gebruikt. U token-vervaldatum `GET` voorkomen door `/.auth/refresh` te bellen naar het eindpunt van uw toepassing. Wanneer de app-service wordt aangeroepen, worden de toegangstokens in de tokenstore automatisch vernieuwd voor de geverifieerde gebruiker. Latere aanvragen voor tokens door uw app-code krijgen de vernieuwde tokens. Om tokenvernieuwing te laten werken, moet de tokenwinkel echter [vernieuwingstokens](https://auth0.com/learn/refresh-tokens/) voor uw provider bevatten. De manier om vernieuwingstokens te krijgen, wordt gedocumenteerd door elke provider, maar de volgende lijst is een korte samenvatting:

- **Google:** een `access_type=offline` parameter voor querytekenreeksen toevoegen aan uw `/.auth/login/google` API-aanroep. Als u de SDK voor mobiele apps gebruikt, `LogicAsync` u de parameter toevoegen aan een van de overbelastingen (zie [Google Refresh Tokens).](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)
- **Facebook**: Biedt geen vernieuwingstokens. Langlevende tokens verlopen binnen 60 dagen (zie [Facebook Expiratie en uitbreiding van toegangstokens).](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)
- **Twitter**: Toegangstokens verlopen niet (zie [Twitter OAuth FAQ](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Microsoft-account**: Selecteer bij het configureren `wl.offline_access` van instellingen voor [Microsoft-accountverificatie](configure-authentication-provider-microsoft.md)het bereik.
- **Azure Active**Directory [https://resources.azure.com](https://resources.azure.com): In, de volgende stappen uitvoeren:
    1. Selecteer boven aan de pagina **Lezen/schrijven**.
    2. Navigeer in de linkerbrowser naar**_\<\_abonnementsnaam_** **subscriptions** >  > **_\<\_ _**  >  **providers** >   >  > **sites** > **resourceGroups** >  > resourceGroepen**_\<resourcegroup\_\_name>_** providers**Microsoft.Web**sites app name>**config****authsettings**. 
    3. Klik op **Bewerken**.
    4. Wijzig de volgende eigenschap. Vervang _ \<\_de app-id>_ door de Azure Active Directory-toepassings-id van de service die u wilt openen.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Klik **op Zetten**. 

Zodra uw provider is geconfigureerd, u [het vernieuwingstoken en de vervaldatum voor het toegangstoken vinden](#retrieve-tokens-in-app-code) in de tokenwinkel. 

Als u uw toegangstoken op `/.auth/refresh` elk gewenst moment wilt vernieuwen, belt u gewoon in elke taal. In het volgende fragment wordt jQuery gebruikt om uw toegangstokens van een JavaScript-client te vernieuwen.

```JavaScript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Als een gebruiker de machtigingen voor uw app `/.auth/me` intrekt, `403 Forbidden` mislukt uw gesprek mogelijk met een antwoord. Als u fouten wilt diagnosticeren, controleert u uw toepassingslogboeken op details.

## <a name="extend-session-token-expiration-grace-period"></a>Verloopperiode voor sessietoken verlengen

De geverifieerde sessie verloopt na 8 uur. Nadat een geverifieerde sessie is verlopen, is er standaard een respijtperiode van 72 uur. Binnen deze respijtperiode u het sessietoken vernieuwen met App Service zonder de gebruiker opnieuw te authenticeren. U `/.auth/refresh` gewoon bellen wanneer uw sessietoken ongeldig wordt en u hoeft de vervaldatum van token niet zelf bij te houden. Zodra de respijtperiode van 72 uur is verstreken, moet de gebruiker zich opnieuw aanmelden om een geldig sessietoken te krijgen.

Als 72 uur niet genoeg tijd voor u is, u dit verloopvenster verlengen. Het verlengen van de vervaldatum over een lange periode kan aanzienlijke beveiligingsimplicaties hebben (zoals wanneer een verificatietoken is gelekt of gestolen). Dus je moet laten op de standaard 72 uur of stel de verlengingsperiode op de kleinste waarde.

Als u het standaardverloopvenster wilt verlengen, voert u de volgende opdracht uit in de [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> De respijtperiode is alleen van toepassing op de geverifieerde sessie van de App-service, niet op de tokens van de identiteitsproviders. Er is geen respijtperiode voor de verlopen providertokens. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Het domein van aanmeldingsaccounts beperken

Met zowel Microsoft Account als Azure Active Directory u zich aanmelden vanuit meerdere domeinen. Met Microsoft-account kunnen bijvoorbeeld _outlook.com-,_ _live.com-_ en _hotmail.com-accounts_ worden outlook.com. Azure AD staat een willekeurig aantal aangepaste domeinen toe voor de aanmeldingsaccounts. U uw gebruikers echter wel rechtstreeks naar uw eigen azure AD-aanmeldingspagina (zoals `contoso.com`) versnellen. Voer de volgende stappen uit om de domeinnaam van de aanmeldingsaccounts voor te stellen.

Navigeer [https://resources.azure.com](https://resources.azure.com)in, naar**_\<\_abonnementsnaam_** **subscriptions** >  > **resourceGroepen** > **_\<\_resource group\_name>_**  >  **providers** > **Microsoft.Web** > **sites** > **_\<app\_name>_**  >  **config** > **authsettings**. 

Klik **op Bewerken,** wijzig de volgende eigenschap en klik vervolgens op **Zetten**. Zorg ervoor dat _ \<u domeinnaam\_>_ vervangt door het domein dat u wilt.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Met deze instelling `domain_hint` voegt u de parameter querytekenreeks toe aan de URL van de aanmeldingsomleiding. 

> [!IMPORTANT]
> Het is mogelijk voor de `domain_hint` client om de parameter te verwijderen na ontvangst van de omleidings-URL en vervolgens in te loggen met een ander domein. Dus hoewel deze functie handig is, is het geen beveiligingsfunctie.
>

## <a name="authorize-or-deny-users"></a>Gebruikers autoriseren of weigeren

Hoewel App Service de eenvoudigste autorisatieaanvraag verzorgt (d.w.z. niet-geverifieerde aanvragen afwijzen), kan voor uw app meer fijnmazig autorisatiegedrag nodig zijn, zoals het beperken van de toegang tot slechts een specifieke groep gebruikers. In bepaalde gevallen moet u aangepaste toepassingscode schrijven om de aangemelde gebruiker toegang te verlenen of te weigeren. In andere gevallen kan App Service of uw identiteitsprovider mogelijk helpen zonder dat er codewijzigingen nodig zijn.

- [Serverniveau](#server-level-windows-apps-only)
- [Niveau van identiteitsprovider](#identity-provider-level)
- [Toepassingsniveau](#application-level)

### <a name="server-level-windows-apps-only"></a>Serverniveau (alleen Windows-apps)

Voor elke Windows-app u het autorisatiegedrag van de IIS-webserver definiëren door het *bestand Web.config* te bewerken. Linux-apps maken geen gebruik van IIS en kunnen niet worden geconfigureerd via *Web.config.*

1. Navigeren naar`https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. Navigeer in de browserverkenner van uw App-servicebestanden naar *site/wwwroot.* Als een *Web.config* niet bestaat, maakt **+**  > u deze door **Nieuw bestand te**selecteren. 

1. Selecteer het potlood voor *Web.config* om het te bewerken. Voeg de volgende configuratiecode toe en klik op **Opslaan**. Als *Web.config* al bestaat, `<authorization>` voeg dan gewoon het element met alles erin. Voeg de accounts toe die `<allow>` u wilt toestaan in het element.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Niveau van identiteitsprovider

De identiteitsleverancier kan bepaalde turn-key autorisatie verstrekken. Bijvoorbeeld:

- Voor [Azure App Service](configure-authentication-provider-aad.md)u toegang op [bedrijfsniveau](../active-directory/manage-apps/what-is-access-management.md) rechtstreeks beheren in Azure AD. Zie De [toegang van een gebruiker tot een toepassing verwijderen](../active-directory/manage-apps/methods-for-removing-user-access.md)voor instructies.
- Voor [Google](configure-authentication-provider-google.md)kunnen Google API-projecten die deel uitmaken van een [organisatie](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) worden geconfigureerd om alleen toegang te verlenen aan gebruikers in uw organisatie (zie [de ondersteuningspagina **OAuth 2.0 instellen van** Google).](https://support.google.com/cloud/answer/6158849?hl=en)

### <a name="application-level"></a>Toepassingsniveau

Als een van de andere niveaus niet de autorisatie biedt die u nodig hebt, of als uw platform of identiteitsprovider niet wordt ondersteund, moet u aangepaste code schrijven om gebruikers te autoriseren op basis van de [gebruikersclaims.](#access-user-claims)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Gebruikers end-to-end (Windows)](app-service-web-tutorial-auth-aad.md)
> [Zelfstudie verifiëren en autoriseren: gebruikers end-to-end verifiëren en autoriseren (Linux)](containers/tutorial-auth-aad.md)
