---
title: Toegang tot webapps autoriseren met OpenID Connect & Azure AD | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u HTTP-berichten gebruikt om toegang tot webtoepassingen en web-API's in uw tenant te autoriseren met Azure Active Directory en OpenID Connect.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eef8174056be7e6be35cea56788c0a519d02944e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154437"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Toegang verlenen aan webtoepassingen die gebruikmaken van OpenID Connect en Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) is een eenvoudige identiteitslaag die is gebouwd bovenop het OAuth 2.0-protocol. OAuth 2.0 definieert mechanismen om [**toegangstokens**](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) te verkrijgen en te gebruiken om toegang te krijgen tot beveiligde bronnen, maar ze definiëren geen standaardmethoden om identiteitsgegevens te verstrekken. OpenID Connect implementeert verificatie als een uitbreiding op het OAuth 2.0-autorisatieproces. Het biedt informatie over de eindgebruiker [`id_token`](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) in de vorm van een die de identiteit van de gebruiker verifieert en basisprofielinformatie over de gebruiker verstrekt.

OpenID Connect is onze aanbeveling als u een webapplicatie bouwt die op een server wordt gehost en toegankelijk is via een browser.

## <a name="register-your-application-with-your-ad-tenant"></a>Uw toepassing registreren bij uw AD-tenant
Registreer uw toepassing eerst bij uw Azure Active Directory-tenant (Azure AD). Na registratie beschikt u over een toepassings-id voor uw toepassing en kan uw toepassing tokens ontvangen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
   
1. Kies uw Azure AD-tenant door uw account te selecteren in de rechterbovenhoek van de pagina, gevolgd door de navigatie van de **Switch Directory** te selecteren en vervolgens de juiste tenant te selecteren. 
   - Sla deze stap over als u slechts één Azure AD-tenant onder uw account hebt of als u al de juiste Azure AD-tenant hebt geselecteerd.
   
1. Zoek en selecteer **Azure Active Directory**in de Azure-portal.
   
1. Selecteer **app-registratie**in het menu **Azure Active Directory** links en selecteer Nieuwe **registratie**.
   
1. Volg de aanwijzingen op het scherm en maak een nieuwe toepassing. Het maakt niet uit of het een webapplicatie of een openbare client (mobile & desktop) applicatie voor deze tutorial, maar als je wilt specifieke voorbeelden voor webapplicaties of openbare client toepassingen, check out onze [quickstarts](v1-overview.md).
   
   - **Naam** is de naam van de toepassing en beschrijft de toepassing voor eindgebruikers.
   - Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
   - Geef de **Omleiding URI**. Voor webtoepassingen is dit de basis-URL van uw app waar gebruikers zich kunnen aanmelden.  Bijvoorbeeld `http://localhost:12345`. Voor openbare clients (mobile & desktop) gebruikt Azure AD deze om tokenreacties terug te sturen. Voer een specifieke waarde in voor uw toepassing.  Bijvoorbeeld `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Zodra u de registratie hebt voltooid, wijst Azure AD uw toepassing een unieke client-id toe (de **toepassings-id**). U hebt deze waarde nodig in de volgende secties, dus kopieer deze van de toepassingspagina.
   
1. Als u uw toepassing wilt vinden in de Azure-portal, selecteert u **App-registraties**en selecteert u **Alle toepassingen weergeven**.

## <a name="authentication-flow-using-openid-connect"></a>Verificatiestroom waarbij OpenID Connect wordt gebruikt

De meest elementaire aanmeldingsstroom bevat de volgende stappen - elk van hen wordt hieronder in detail beschreven.

![Verificatiestroom van OpenId Connect](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Metagegevensdocument OpenID Connect

OpenID Connect beschrijft een metagegevensdocument dat de meeste informatie bevat die nodig is voor een app om aantemelding uit te voeren. Dit omvat informatie zoals de URL's die moeten worden gebruikt en de locatie van de openbare ondertekeningssleutels van de service. Het metagegevensdocument van OpenID Connect is te vinden op:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
De metagegevens zijn een eenvoudig Json-document (JavaScript Object Notation). Zie het volgende fragment voor een voorbeeld. De inhoud van het fragment wordt volledig beschreven in de [OpenID Connect-specificatie.](https://openid.net) Houd er rekening mee `common` dat het verstrekken van een tenant-id in plaats van in plaats van {tenant} hierboven zal resulteren in tenantspecifieke URI's in het JSON-object.

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    "userinfo_endpoint":"https://login.microsoftonline.com/{tenant}/openid/userinfo",
    ...
}
```

Als uw app aangepaste ondertekeningssleutels heeft als gevolg van het gebruik `appid` van de functie voor het toewijzen `jwks_uri` van [claims,](../develop/active-directory-claims-mapping.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) moet u een queryparameter toevoegen die de app-id bevat om een aanmelding naar de ondertekeningssleutelgegevens van uw app te krijgen. Bijvoorbeeld: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` bevat `jwks_uri` een `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`van .

## <a name="send-the-sign-in-request"></a>De aanmeldingsaanvraag verzenden

Wanneer uw webtoepassing de gebruiker moet verifiëren, moet `/authorize` deze de gebruiker naar het eindpunt leiden. Deze aanvraag is vergelijkbaar met de eerste etappe van de [OAuth 2.0 Authorization Code Flow,](v1-protocols-oauth-code.md)met een paar belangrijke verschillen:

* De aanvraag moet `openid` het `scope` bereik in de parameter bevatten.
* De `response_type` parameter `id_token`moet omvatten .
* De aanvraag moet `nonce` de parameter bevatten.

Dus een voorbeeld verzoek zou er als volgt uitzien:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%3a12345
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parameter |  | Beschrijving |
| --- | --- | --- |
| tenant |vereist |De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich bij de toepassing kan aanmelden. De toegestane waarden zijn tenant-id's, bijvoorbeeld, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` of `contoso.onmicrosoft.com` `common` voor tenant-onafhankelijke tokens |
| client_id |vereist |De toepassings-id die aan uw app is toegewezen toen u deze registreerde bij Azure AD. U vindt dit in de Azure-portal. Klik **op Azure Active Directory**, klik op **App-registraties,** kies de toepassing en zoek de toepassings-id op de toepassingspagina. |
| response_type |vereist |Moet `id_token` voor OpenID Connect aanmelden. Het kan ook andere response_types `code` `token`omvatten, zoals of . |
| scope | Aanbevolen | De OpenID Connect-specificatie `openid`vereist het bereik, wat zich vertaalt naar de machtiging 'Aanmelden' in de toestemmings-gebruikersinterface. Deze en andere OIDC scopes worden genegeerd op het v1.0 eindpunt, maar is nog steeds een best practice voor standaarden-compliant clients. |
| Nonce |vereist |Een waarde die is opgenomen in het verzoek, gegenereerd `id_token` door de app, die is opgenomen in de resulterende als een claim. De app kan deze waarde vervolgens verifiëren om token replay-aanvallen te beperken. De waarde is meestal een gerandomiseerde, unieke tekenreeks of GUID die kan worden gebruikt om de oorsprong van het verzoek te identificeren. |
| redirect_uri | Aanbevolen |De redirect_uri van uw app, waar verificatiereacties door uw app kunnen worden verzonden en ontvangen. Het moet precies overeenkomen met een van de redirect_uris die u hebt geregistreerd in de portal, behalve dat het moet worden gecodeerd. Als deze ontbreekt, wordt de gebruikersagent willekeurig teruggestuurd naar een van de omleidings-URI's die voor de app zijn geregistreerd. De maximale lengte is 255 bytes |
| response_mode |optioneel |Hiermee geeft u de methode op die moet worden gebruikt om de resulterende authorization_code terug te sturen naar uw app. Ondersteunde waarden `form_post` zijn voor *HTTP-formulierbericht* en `fragment` voor *URL-fragment*. Voor webapplicaties raden `response_mode=form_post` we u aan om de veiligste overdracht van tokens naar uw toepassing te garanderen. De standaardinstelling voor elke `fragment`stroom inclusief een id_token is .|
| state |Aanbevolen |Een waarde die is opgenomen in het verzoek dat wordt geretourneerd in het tokenantwoord. Het kan een reeks van alle inhoud die u wenst. Een willekeurig gegenereerde unieke waarde wordt meestal gebruikt voor [het voorkomen van cross-site request vervalsing aanvallen](https://tools.ietf.org/html/rfc6749#section-10.12). De status wordt ook gebruikt om informatie over de status van de gebruiker in de app te coderen voordat de verificatieaanvraag plaatsvond, zoals de pagina of weergave waarop ze zich bevonden. |
| Prompt |optioneel |Geeft het type gebruikersinteractie aan dat vereist is. Momenteel zijn de enige geldige waarden 'login', 'none' en 'consent'. `prompt=login`dwingt de gebruiker om hun referenties in te voeren op dat verzoek, ontkennen single-sign aan. `prompt=none`is het tegenovergestelde - het zorgt ervoor dat de gebruiker niet wordt gepresenteerd met een interactieve prompt dan ook. Als de aanvraag niet in stilte kan worden voltooid via één aanmelding, wordt een fout geretourneerd als het eindpunt. `prompt=consent`hiermee wordt het dialoogvenster OAuth-toestemming geactiveerd nadat de gebruiker zich heeft aanmeldt en de gebruiker wordt gevraagd machtigingen aan de app toe te kennen. |
| login_hint |optioneel |Kan worden gebruikt om vooraf de gebruikersnaam/e-mailadres veld van de aanmeldingspagina voor de gebruiker in te vullen, als u hun gebruikersnaam van tevoren kent. Vaak gebruiken apps deze parameter tijdens de herverificatie, nadat ze de `preferred_username` gebruikersnaam al uit een eerdere aanmelding hebben gehaald met behulp van de claim. |

Op dit punt wordt de gebruiker gevraagd om zijn referenties in te voeren en de verificatie te voltooien.

### <a name="sample-response"></a>Voorbeeldantwoord

Een voorbeeldantwoord dat `redirect_uri` naar de opgegeven in de aanmeldingsaanvraag wordt verzonden nadat de gebruiker is geverifieerd, kan er als volgt uitzien:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parameter | Beschrijving |
| --- | --- |
| id_token |De `id_token` app gevraagd. U `id_token` de gebruiker gebruiken om de identiteit van de gebruiker te verifiëren en een sessie met de gebruiker te beginnen. |
| state |Een waarde die is opgenomen in het verzoek en die ook wordt geretourneerd in het tokenantwoord. Een willekeurig gegenereerde unieke waarde wordt meestal gebruikt voor [het voorkomen van cross-site request vervalsing aanvallen](https://tools.ietf.org/html/rfc6749#section-10.12). De status wordt ook gebruikt om informatie over de status van de gebruiker in de app te coderen voordat de verificatieaanvraag plaatsvond, zoals de pagina of weergave waarop ze zich bevonden. |

### <a name="error-response"></a>Foutreactie

Foutreacties kunnen ook naar `redirect_uri` de app worden verzonden, zodat de app ze op de juiste manier kan afhandelen:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschrijving |
| --- | --- |
| error |Een foutcodetekenreeks die kan worden gebruikt om typen fouten die zich voordoen te classificeren en kan worden gebruikt om op fouten te reageren. |
| error_description |Een specifiek foutbericht waarmee een ontwikkelaar de hoofdoorzaak van een verificatiefout kan identificeren. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Foutcodes voor eindpuntfouten voor autorisatie

In de volgende tabel worden de verschillende foutcodes beschreven die kunnen worden geretourneerd in de `error` parameter van de foutreactie.

| Foutcode | Beschrijving | Clientactie |
| --- | --- | --- |
| invalid_request |Protocolfout, zoals een ontbrekende vereiste parameter. |De aanvraag herstellen en opnieuw indienen. Dit is een ontwikkelingsfout, en wordt meestal gevangen tijdens de eerste tests. |
| unauthorized_client |De clientaanvraag is niet toegestaan om een autorisatiecode aan te vragen. |Dit gebeurt meestal wanneer de clienttoepassing niet is geregistreerd in Azure AD of niet wordt toegevoegd aan de Azure AD-tenant van de gebruiker. De toepassing kan de gebruiker instructies geven voor het installeren van de toepassing en het toevoegen aan Azure AD. |
| access_denied |Eigenaar van de resource heeft geen toestemming geweigerd |De clienttoepassing kan de gebruiker laten weten dat deze niet door kan gaan, tenzij de gebruiker hiermee instemt. |
| unsupported_response_type |De autorisatieserver ondersteunt het antwoordtype in de aanvraag niet. |De aanvraag herstellen en opnieuw indienen. Dit is een ontwikkelingsfout, en wordt meestal gevangen tijdens de eerste tests. |
| server_error |Er is een onverwachte fout opgetreden op de server. |Probeer het verzoek opnieuw. Deze fouten kunnen het gevolg zijn van tijdelijke omstandigheden. De clienttoepassing kan de gebruiker uitleggen dat de reactie is vertraagd als gevolg van een tijdelijke fout. |
| temporarily_unavailable |De server is tijdelijk te druk om de aanvraag af te handelen. |Probeer het verzoek opnieuw. De clienttoepassing kan de gebruiker uitleggen dat de reactie is vertraagd als gevolg van een tijdelijke aandoening. |
| invalid_resource |De doelbron is ongeldig omdat deze niet bestaat, Azure AD deze niet kan vinden of niet correct is geconfigureerd. |Dit geeft aan dat de resource, als deze bestaat, niet is geconfigureerd in de tenant. De toepassing kan de gebruiker instructies geven voor het installeren van de toepassing en het toevoegen aan Azure AD. |

## <a name="validate-the-id_token"></a>De id_token valideren

Alleen het `id_token` ontvangen van een is niet voldoende om de gebruiker te verifiëren; u moet de handtekening valideren en `id_token` de claims verifiëren in de vereisten van uw app. Het Azure AD-eindpunt gebruikt JSON Web Tokens (JWT's) en public key cryptography om tokens te ondertekenen en te controleren of ze geldig zijn.

U ervoor `id_token` kiezen om de in-clientcode te `id_token` valideren, maar een gangbare praktijk is om de server naar een backendserver te verzenden en de validatie daar uit te voeren. 

U ook aanvullende claims valideren, afhankelijk van uw scenario. Enkele veelvoorkomende validaties zijn:

* Ervoor zorgen dat de gebruiker/organisatie zich heeft aangemeld voor de app.
* Ervoor zorgen dat de gebruiker `wids` beschikt `roles` over de juiste autorisatie / privileges met behulp van de of claims. 
* Er is een bepaalde sterkte van verificatie opgetreden, zoals meervoudige verificatie.

Zodra u de `id_token`, u een sessie beginnen met de `id_token` gebruiker en gebruik maken van de vorderingen in de om informatie over de gebruiker in uw app te verkrijgen. Deze informatie kan worden gebruikt voor weergave, records, personalisatie, enz. Voor meer `id_tokens` informatie over en claims, lees [AAD id_tokens](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="send-a-sign-out-request"></a>Een afmeldingsverzoek verzenden

Wanneer u de gebruiker uit de app wilt afmelden, volstaat het niet om de cookies van uw app te wissen of de sessie op een andere manier met de gebruiker te beëindigen. U moet de gebruiker `end_session_endpoint` ook doorverwijzen naar de voorafmelding. Als u dit niet doet, kan de gebruiker zich opnieuw verifiëren naar uw app zonder zijn of haar referenties opnieuw in te voeren, omdat hij of zij een geldige aanmeldingssessie heeft met het AD-eindpunt van Azure.

U de gebruiker `end_session_endpoint` eenvoudig doorverwijzen naar de lijst in het metagegevensdocument van OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parameter |  | Beschrijving |
| --- | --- | --- |
| post_logout_redirect_uri |Aanbevolen |De URL waarnaar de gebruiker moet worden doorgestuurd nadat deze succesvol is afmeld.  Deze URL moet overeenkomen met een van de omleidings-URI's die zijn geregistreerd voor uw toepassing in de app-registratieportal.  Als *post_logout_redirect_uri* niet wordt opgenomen, krijgt de gebruiker een algemeen bericht te zien. |

## <a name="single-sign-out"></a>Eenmalige afmelding

Wanneer u de gebruiker `end_session_endpoint`doorverwijst naar de , azure ad, wordt de sessie van de gebruiker gewist uit de browser. De gebruiker kan echter nog steeds zijn aangemeld bij andere toepassingen die Azure AD gebruiken voor verificatie. Azure AD stuurt een HTTP GET-verzoek naar de geregistreerde `LogoutUrl` van alle toepassingen waarop de gebruiker momenteel is aangemeld. Toepassingen moeten op dit verzoek reageren door elke sessie `200` te wissen die de gebruiker identificeert en een antwoord terug te sturen. Als u eenmalige afmelding in uw aanvraag wilt `LogoutUrl` ondersteunen, moet u een dergelijke in de code van uw toepassing implementeren. U `LogoutUrl` de azure-portal instellen:

1. Navigeer naar de [Azure-portal](https://portal.azure.com).
2. Kies uw Active Directory door rechtsboven op de pagina op uw account te klikken.
3. Kies in het navigatiepaneel aan de linkerkant **Azure Active Directory,** kies **vervolgens App-registraties** en selecteer uw toepassing.
4. Klik op **Instellingen**en vervolgens **op Eigenschappen** en zoek het tekstvak **AFmelden URL.** 

## <a name="token-acquisition"></a>Token-acquisitie
Veel webapps moeten niet alleen de gebruiker aanmelden, maar ook toegang krijgen tot een webservice namens die gebruiker met OAuth. Dit scenario combineert OpenID Connect voor gebruikersverificatie en aanschaft tegelijkertijd een `authorization_code` die kan worden gebruikt om de [OAuth-autorisatiecodestroom](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)te gebruiken. `access_tokens`

## <a name="get-access-tokens"></a>Toegangstokens ontvangen
Als u toegangstokens wilt verkrijgen, moet u het aanmeldingsverzoek van bovenaf wijzigen:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F        // The identifier of the protected resource (web API) that your application needs access to
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Door machtigingsscopen op te `response_type=code+id_token`nemen `authorize` in de aanvraag en het gebruik, zorgt `scope` het eindpunt ervoor dat de gebruiker heeft ingestemd met de machtigingen die zijn aangegeven in de queryparameter en retourneert u uw app een autorisatiecode om in te wisselen voor een toegangstoken.

### <a name="successful-response"></a>Succesvolle reactie

Een succesvol antwoord, `redirect_uri` verzonden naar het gebruik `response_mode=form_post`, ziet eruit als volgt:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parameter | Beschrijving |
| --- | --- |
| id_token |De `id_token` app gevraagd. U `id_token` de gebruiker gebruiken om de identiteit van de gebruiker te verifiëren en een sessie met de gebruiker te beginnen. |
| code |De authorization_code die de app heeft aangevraagd. De app kan de autorisatiecode gebruiken om een toegangstoken voor de doelbron aan te vragen. Authorization_codes zijn van korte duur, en meestal verlopen na ongeveer 10 minuten. |
| state |Als een parameter in de status in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weergegeven. De app moet controleren of de statuswaarden in de aanvraag en het antwoord identiek zijn. |

### <a name="error-response"></a>Foutreactie

Foutreacties kunnen ook naar `redirect_uri` de app worden verzonden, zodat de app ze op de juiste manier kan afhandelen:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschrijving |
| --- | --- |
| error |Een foutcodetekenreeks die kan worden gebruikt om typen fouten die zich voordoen te classificeren en kan worden gebruikt om op fouten te reageren. |
| error_description |Een specifiek foutbericht waarmee een ontwikkelaar de hoofdoorzaak van een verificatiefout kan identificeren. |

Zie [Foutcodes voor eindpuntfouten](#error-codes-for-authorization-endpoint-errors)voor autorisatievoor een beschrijving van de mogelijke foutcodes en de aanbevolen clientactie.

Zodra u een autorisatie `code` en `id_token`een hebt gekregen, u de gebruiker aanmelden en namens hen [toegangstokens](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) ontvangen. Als u de gebruiker wilt `id_token` aanmelden, moet u de precies zoals hierboven beschreven valideren. Om toegangstokens te ontvangen, u de stappen volgen die zijn beschreven in het gedeelte 'Gebruik de autorisatiecode om een toegangstoken aan te vragen' van onze [OAuth-codestroomdocumentatie.](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [toegangstokens](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
* Meer informatie [ `id_token` ](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)over de claims en claims .
