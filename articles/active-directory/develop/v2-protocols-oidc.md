---
title: OpenID Connect-protocol - Microsoft-identiteitsplatform | Azure
description: Bouw webapplicaties met behulp van het Microsoft-identiteitsplatform dat wordt geïmplementeerd voor het OpenID Connect-verificatieprotocol.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0ed1cb6a080a35fa81c6a859f88d987020c8504c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262293"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft-identiteitsplatform en OpenID Connect-protocol

OpenID Connect is een verificatieprotocol dat is gebaseerd op OAuth 2.0 dat u gebruiken om een gebruiker veilig aan te melden bij een webtoepassing. Wanneer u de implementatie van OpenID Connect van het Microsoft-identiteitsplatform gebruikt, u aanmeldings- en API-toegang toevoegen aan uw webgebaseerde apps. In dit artikel ziet u hoe u dit onafhankelijk van de taal doen en wordt beschreven hoe u HTTP-berichten verzendt en ontvangt zonder gebruik te maken van open-sourcebibliotheken van Microsoft.

> [!NOTE]
> Het eindpunt van het Microsoft-identiteitsplatform biedt geen ondersteuning voor alle Azure Active Directory-scenario's en -functies (Azure AD). Als u wilt bepalen of u het eindpunt van het Microsoft-identiteitsplatform moet gebruiken, leest u over [de beperkingen van het Microsoft-identiteitsplatform.](active-directory-v2-limitations.md)

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) breidt het OAuth 2.0-autorisatieprotocol uit om te gebruiken als *authorization* *verificatieprotocol,* zodat u één aanmelding doen met OAuth. OpenID Connect introduceert het concept van een *ID-token,* een beveiligingstoken waarmee de client de identiteit van de gebruiker kan verifiëren. Het ID-token krijgt ook basisprofielinformatie over de gebruiker. Omdat OpenID Connect OAuth 2.0 uitbreidt, kunnen apps veilig *toegangstokens*verkrijgen, die kunnen worden gebruikt om toegang te krijgen tot bronnen die zijn beveiligd door een [autorisatieserver.](active-directory-v2-protocols.md#the-basics) Met het eindpunt van het Microsoft-identiteitsplatform kunnen apps van derden die zijn geregistreerd bij Azure AD ook toegangstokens uitgeven voor beveiligde bronnen zoals web-API's. Zie Een app registreren bij het eindpunt van [het Microsoft-identiteitsplatform](quickstart-register-app.md)voor meer informatie over het instellen van een toepassing voor het uitgeven van toegangstokens. We raden u aan OpenID Connect te gebruiken als u een [webtoepassing](v2-app-types.md#web-apps) bouwt die op een server wordt gehost en via een browser wordt geopend.

## <a name="protocol-diagram-sign-in"></a>Protocoldiagram: Aanmelden

De meest elementaire aanmeldingsstroom heeft de stappen in het volgende diagram. Elke stap wordt in detail beschreven in dit artikel.

![OpenID Connect-protocol: aanmelden](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Het metagegevensdocument van OpenID Connect ophalen

OpenID Connect beschrijft een metagegevensdocument dat de meeste informatie bevat die nodig is voor een app om zich aan te melden. Dit omvat informatie zoals de URL's die moeten worden gebruikt en de locatie van de openbare ondertekeningssleutels van de service. Voor het eindpunt van het Microsoft-identiteitsplatform is dit het Metagegevensdocument van OpenID Connect dat u moet gebruiken:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Probeer het nu! Klik [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) hier `common` om de configuratie van de tenants te bekijken.

Het `{tenant}` kan een van de vier waarden:

| Waarde | Beschrijving |
| --- | --- |
| `common` |Gebruikers met zowel een persoonlijk Microsoft-account als een werk- of schoolaccount van Azure AD kunnen zich aanmelden bij de toepassing. |
| `organizations` |Alleen gebruikers met werk- of schoolaccounts van Azure AD kunnen zich aanmelden bij de toepassing. |
| `consumers` |Alleen gebruikers met een persoonlijk Microsoft-account kunnen zich aanmelden bij de toepassing. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` of `contoso.onmicrosoft.com` | Alleen gebruikers van een specifieke Azure AD-tenant (of ze nu lid zijn in de directory met een werk- of schoolaccount, of ze zijn gasten in de directory met een persoonlijk Microsoft-account) kunnen zich aanmelden bij de toepassing. De vriendelijke domeinnaam van de Azure AD-tenant of de GUID-id van de tenant kan worden gebruikt. U ook gebruik `9188040d-6c67-4c5b-b112-36a304b66dad`maken van de `consumers` consument huurder, in plaats van de huurder.  |

De metagegevens zijn een eenvoudig Json-document (JavaScript Object Notation). Zie het volgende fragment voor een voorbeeld. De inhoud van het fragment wordt volledig beschreven in de [OpenID Connect-specificatie.](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2)

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/{tenant}\/discovery\/v2.0\/keys",

  ...

}
```

Als uw app aangepaste ondertekeningssleutels heeft als gevolg van het gebruik `appid` van de functie voor het toewijzen `jwks_uri` van [claims,](active-directory-claims-mapping.md) moet u een queryparameter toevoegen die de app-id bevat om een aanmelding naar de ondertekeningssleutelgegevens van uw app te krijgen. Bijvoorbeeld: `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` bevat `jwks_uri` een `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`van .

Doorgaans gebruikt u dit metagegevensdocument om een OpenID Connect-bibliotheek of SDK te configureren. de bibliotheek zou de metagegevens gebruiken om zijn werk te doen. Als u echter geen vooraf gebouwde OpenID Connect-bibliotheek gebruikt, u de stappen in de rest van dit artikel volgen om u aan te melden in een web-app met behulp van het eindpunt van het Microsoft-identiteitsplatform.

## <a name="send-the-sign-in-request"></a>De aanmeldingsaanvraag verzenden

Wanneer uw web-app de gebruiker moet verifiëren, `/authorize` kan deze de gebruiker naar het eindpunt leiden. Deze aanvraag is vergelijkbaar met het eerste deel van de [OAuth 2.0 autorisatie code stroom,](v2-oauth2-auth-code-flow.md)met deze belangrijke verschillen:

* De aanvraag moet `openid` het `scope` bereik in de parameter bevatten.
* De `response_type` parameter `id_token`moet omvatten .
* De aanvraag moet `nonce` de parameter bevatten.

> [!IMPORTANT]
> Om een ID-token met succes aan te vragen bij het /authorization-eindpunt, moet de app-registratie in het `oauth2AllowIdTokenImplicitFlow` [registratieportaal](https://portal.azure.com) de impliciete toekenning van id_tokens ingeschakeld hebben op het tabblad Verificatie (waarin de vlag in het [toepassingsmanifest wordt ingesteld](reference-app-manifest.md) op `true`). Als deze niet is ingeschakeld, wordt een `unsupported_response` fout geretourneerd: "De opgegeven waarde voor de invoerparameter 'response_type' is niet toegestaan voor deze client. Verwachte waarde is 'code'"

Bijvoorbeeld:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> Klik op de volgende link om dit verzoek uit te voeren. Nadat u zich hebt aangemeld, wordt `https://localhost/myapp/`uw browser omgeleid naar , met een ID-token in de adresbalk. Houd er rekening `response_mode=fragment` mee dat deze aanvraag wordt gebruikt (alleen voor demonstratiedoeleinden). Wij raden u `response_mode=form_post`aan .
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parameter | Voorwaarde | Beschrijving |
| --- | --- | --- |
| `tenant` | Vereist | U `{tenant}` de waarde in het pad van de aanvraag gebruiken om te bepalen wie zich bij de toepassing kan aanmelden. De toegestane waarden `common` `organizations`zijn `consumers`, , en tenant-id's. Zie [de basisprincipes van het protocol voor](active-directory-v2-protocols.md#endpoints)meer informatie. |
| `client_id` | Vereist | De **toepassings-id (client)** die de [Azure-portal – App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring toegewezen aan uw app. |
| `response_type` | Vereist | Moet `id_token` voor OpenID Connect aanmelden. Het kan ook `response_type` andere waarden `code`bevatten, zoals . |
| `redirect_uri` | Aanbevolen | De omleiding uri van uw app, waar authenticatie reacties kunnen worden verzonden en ontvangen door uw app. Het moet precies overeenkomen met een van de omleiding URI's die u geregistreerd in de portal, behalve dat het moet worden URL gecodeerd. Als dit niet aanwezig is, kiest het eindpunt willekeurig een geregistreerde redirect_uri om de gebruiker terug te sturen. |
| `scope` | Vereist | Een ruimte-gescheiden lijst met scopes. Voor OpenID Connect moet het `openid`bereik worden opgenomen, wat zich vertaalt naar de machtiging 'Aanmelden' in de gebruikersinterface voor toestemming. U ook andere scopes opnemen in dit verzoek om toestemming te vragen. |
| `nonce` | Vereist | Een waarde die is opgenomen in het verzoek, gegenereerd door de app, die wordt opgenomen in de resulterende id_token waarde als een claim. De app kan deze waarde verifiëren om token replay-aanvallen te beperken. De waarde is meestal een gerandomiseerde, unieke tekenreeks die kan worden gebruikt om de oorsprong van het verzoek te identificeren. |
| `response_mode` | Aanbevolen | Hiermee geeft u de methode op die moet worden gebruikt om de resulterende autorisatiecode terug te sturen naar uw app. Deze waarde kan `form_post` of `fragment` zijn. Voor webapplicaties raden `response_mode=form_post`we u aan om de veiligste overdracht van tokens naar uw toepassing te garanderen. |
| `state` | Aanbevolen | Een waarde die is opgenomen in het verzoek en die ook wordt geretourneerd in het tokenantwoord. Het kan een reeks van elke inhoud die u wilt. Een willekeurig gegenereerde unieke waarde wordt meestal gebruikt om [cross-site request vervalsing aanvallen](https://tools.ietf.org/html/rfc6749#section-10.12)te voorkomen. De status wordt ook gebruikt om informatie over de status van de gebruiker in de app te coderen voordat de verificatieaanvraag plaatsvond, zoals de pagina of de weergave waarop de gebruiker zich bevond. |
| `prompt` | Optioneel | Geeft het type gebruikersinteractie aan dat vereist is. De enige geldige waarden `login`op `none`dit `consent`moment zijn , en . De `prompt=login` claim dwingt de gebruiker om hun referenties in te voeren op dat verzoek, die eenmalige aanmelding ontkent. De `prompt=none` vordering is het tegenovergestelde. Deze claim zorgt ervoor dat de gebruiker geen interactieve prompt krijgt. Als de aanvraag niet in stilte kan worden voltooid via eenmalige aanmelding, retourneert het eindpunt van het Microsoft-identiteitsplatform een fout. De `prompt=consent` claim activeert het dialoogvenster OAuth-toestemming nadat de gebruiker zich heeft aanmeldt. In het dialoogvenster wordt de gebruiker gevraagd machtigingen toe te kennen aan de app. |
| `login_hint` | Optioneel | U deze parameter gebruiken om het gebruikersnaam- en e-mailadresveld van de aanmeldingspagina voor de gebruiker vooraf in te vullen, als u de gebruikersnaam van tevoren kent. Vaak gebruiken apps deze parameter tijdens herverificatie, nadat ze de gebruikersnaam al `preferred_username` uit een eerdere aanmelding hebben gehaald met behulp van de claim. |
| `domain_hint` | Optioneel | Het rijk van de gebruiker in een federatieve directory.  Dit slaat het op e-mail gebaseerde detectieproces over dat de gebruiker op de aanmeldingspagina doorloopt, voor een iets gestroomlijndere gebruikerservaring. Voor huurders die via een on-premises directory zoals AD FS worden gefedereerd, resulteert dit vaak in een naadloze aanmelding vanwege de bestaande inlogsessie. |

Op dit punt wordt de gebruiker gevraagd om zijn referenties in te voeren en de verificatie te voltooien. Het eindpunt van het Microsoft-identiteitsplatform controleert of de gebruiker `scope` heeft ingestemd met de machtigingen die zijn aangegeven in de queryparameter. Als de gebruiker niet heeft ingestemd met een van deze machtigingen, vraagt het eindpunt van het Microsoft-identiteitsplatform de gebruiker om in te stemmen met de vereiste machtigingen. U meer lezen over [machtigingen, toestemming en apps met meerdere huurders.](v2-permissions-and-consent.md)

Nadat de gebruiker is geverifieerd en toestemming heeft gegeven, retourneert het eindpunt van het Microsoft-identiteitsplatform een antwoord op uw app op de aangegeven omleidinguri met behulp van de methode die in de `response_mode` parameter is opgegeven.

### <a name="successful-response"></a>Succesvolle reactie

Een succesvol antwoord `response_mode=form_post` wanneer u er als volgt uitziet:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parameter | Beschrijving |
| --- | --- |
| `id_token` | Het ID-token dat de app heeft aangevraagd. U `id_token` de parameter gebruiken om de identiteit van de gebruiker te verifiëren en een sessie met de gebruiker te beginnen. Zie de [ `id_tokens` referentie](id-tokens.md)voor meer informatie over ID-tokens en de inhoud ervan. |
| `state` | Als `state` een parameter in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weergegeven. De app moet controleren of de statuswaarden in de aanvraag en het antwoord identiek zijn. |

### <a name="error-response"></a>Foutreactie

Foutreacties kunnen ook worden verzonden naar de omleiding URI, zodat de app ze kan verwerken. Een foutreactie ziet er als volgt uit:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschrijving |
| --- | --- |
| `error` | Een foutcodetekenreeks die u gebruiken om typen fouten die optreden te classificeren en om te reageren op fouten. |
| `error_description` | Een specifiek foutbericht waarmee u de hoofdoorzaak van een verificatiefout identificeren. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Foutcodes voor eindpuntfouten voor autorisatie

In de volgende tabel worden foutcodes `error` beschreven die kunnen worden geretourneerd in de parameter van de foutrespons:

| Foutcode | Beschrijving | Clientactie |
| --- | --- | --- |
| `invalid_request` | Protocolfout, zoals een ontbrekende, vereiste parameter. |De aanvraag herstellen en opnieuw indienen. Dit is een ontwikkelingsfout die meestal wordt gevangen tijdens de eerste tests. |
| `unauthorized_client` | De clienttoepassing kan geen autorisatiecode aanvragen. |Dit gebeurt meestal wanneer de clienttoepassing niet is geregistreerd in Azure AD of niet wordt toegevoegd aan de Azure AD-tenant van de gebruiker. De toepassing kan de gebruiker instructies vragen om de toepassing te installeren en toe te voegen aan Azure AD. |
| `access_denied` | De eigenaar van de resource heeft geen toestemming geweigerd. |De clienttoepassing kan de gebruiker laten weten dat deze niet door kan gaan, tenzij de gebruiker hiermee instemt. |
| `unsupported_response_type` |De autorisatieserver ondersteunt het antwoordtype in de aanvraag niet. |De aanvraag herstellen en opnieuw indienen. Dit is een ontwikkelingsfout die meestal wordt gevangen tijdens de eerste tests. |
| `server_error` | Er is een onverwachte fout opgetreden op de server. |Probeer het verzoek opnieuw. Deze fouten kunnen het gevolg zijn van tijdelijke omstandigheden. De clienttoepassing kan de gebruiker uitleggen dat de reactie is vertraagd vanwege een tijdelijke fout. |
| `temporarily_unavailable` | De server is tijdelijk te druk om de aanvraag af te handelen. |Probeer het verzoek opnieuw. De clienttoepassing kan de gebruiker uitleggen dat de reactie is vertraagd vanwege een tijdelijke aandoening. |
| `invalid_resource` | De doelbron is ongeldig omdat deze niet bestaat, Azure AD deze niet kan vinden of niet correct is geconfigureerd. |Dit geeft aan dat de resource, als deze bestaat, niet is geconfigureerd in de tenant. De toepassing kan de gebruiker instructies geven voor het installeren van de toepassing en het toevoegen aan Azure AD. |

## <a name="validate-the-id-token"></a>Het ID-token valideren

Alleen het ontvangen van een id_token is niet voldoende om de gebruiker te verifiëren; u moet de handtekening van de id_token valideren en de claims in het token verifiëren volgens de vereisten van uw app. Het eindpunt van het Microsoft-identiteitsplatform gebruikt [JSON Web Tokens (JWT's)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) en public key cryptography om tokens te ondertekenen en te controleren of ze geldig zijn.

U ervoor `id_token` kiezen om de in-clientcode te `id_token` valideren, maar een gangbare praktijk is om de backendserver naar een backendserver te verzenden en daar de validatie uit te brengen. Zodra u de handtekening van de id_token hebt gevalideerd, zijn er een paar claims die u moet verifiëren. Zie [ `id_token` ](id-tokens.md) de referentie voor meer informatie, waaronder [het valideren van tokens](id-tokens.md#validating-an-id_token) en belangrijke informatie over het ondertekenen van [sleutelrollover.](active-directory-signing-key-rollover.md) We raden u aan gebruik te maken van een bibliotheek voor het ontleden en valideren van tokens - er is er ten minste één beschikbaar voor de meeste talen en platforms.

U ook aanvullende claims valideren, afhankelijk van uw scenario. Enkele veelvoorkomende validaties zijn:

* Ervoor zorgen dat de gebruiker/organisatie zich heeft aangemeld voor de app.
* Ervoor zorgen dat de gebruiker over de juiste autorisatie/bevoegdheden beschikt
* Er is een bepaalde sterkte van verificatie opgetreden, zoals meervoudige verificatie.

Zodra u de id_token hebt gevalideerd, u een sessie met de gebruiker beginnen en de claims in de id_token gebruiken om informatie over de gebruiker in uw app te verkrijgen. Deze informatie kan worden gebruikt voor weergave, records, personalisatie, enz.

## <a name="send-a-sign-out-request"></a>Een afmeldingsverzoek verzenden

Wanneer u de gebruiker vanuit uw app wilt afmelden, volstaat het niet om de cookies van uw app te wissen of anderszins de sessie van de gebruiker te beëindigen. U moet de gebruiker ook doorverwijzen naar het eindpunt van het Microsoft-identiteitsplatform om u af te melden. Als u dit niet doet, verifieert de gebruiker opnieuw naar uw app zonder zijn of haar referenties opnieuw in te voeren, omdat er een geldige aanmeldingssessie is met het eindpunt van het Microsoft-identiteitsplatform.

U de gebruiker `end_session_endpoint` doorverwijzen naar de lijst in het metagegevensdocument van OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | Voorwaarde | Beschrijving |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Aanbevolen | De URL waarnaar de gebruiker wordt doorgestuurd nadat hij zich met succes heeft afmelden. Als de parameter niet is opgenomen, krijgt de gebruiker een algemeen bericht te zien dat wordt gegenereerd door het eindpunt van het Microsoft-identiteitsplatform. Deze URL moet overeenkomen met een van de omleidings-URI's die zijn geregistreerd voor uw toepassing in de app-registratieportal. |

## <a name="single-sign-out"></a>Eenmalige afmelding

Wanneer u de gebruiker `end_session_endpoint`doorverwijst naar het eindpunt van het Microsoft-identiteitsplatform, wordt de sessie van de gebruiker gewist uit de browser. De gebruiker kan echter nog steeds zijn aangemeld bij andere toepassingen die Microsoft-accounts gebruiken voor verificatie. Om deze toepassingen in staat te stellen de gebruiker tegelijkertijd uit te loggen, `LogoutUrl` stuurt het eindpunt van het Microsoft-identiteitsplatform een HTTP GET-verzoek naar de geregistreerde van alle toepassingen waarop de gebruiker momenteel is aangemeld. Toepassingen moeten op dit verzoek reageren door elke sessie `200` te wissen die de gebruiker identificeert en een antwoord terug te sturen. Als u eenmalige afmelding in uw aanvraag wilt `LogoutUrl` ondersteunen, moet u een dergelijke in de code van uw toepassing implementeren. U `LogoutUrl` de app-registratieportal instellen.

## <a name="protocol-diagram-access-token-acquisition"></a>Protocoldiagram: Acquisitie van Access-token

Veel webapps moeten niet alleen de gebruiker aanmelden, maar ook toegang krijgen tot een webservice namens de gebruiker met OAuth. Dit scenario combineert OpenID Connect voor gebruikersverificatie en krijgt tegelijkertijd een autorisatiecode die u gebruiken om toegangstokens te krijgen als u de OAuth-autorisatiecodestroom gebruikt.

De volledige aanmeldings- en tokenacquisitiestroom van OpenID Connect lijkt op het volgende diagram. We beschrijven elke stap in detail in de volgende secties van het artikel.

![OpenID Connect-protocol: tokenacquisitie](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Toegangstokens ontvangen
Als u toegangstokens wilt verkrijgen, wijzigt u de aanmeldingsaanvraag:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Klik op de volgende link om dit verzoek uit te voeren. Nadat u zich hebt aangemeld, `https://localhost/myapp/`wordt uw browser doorgestuurd naar , met een ID-token en een code in de adresbalk. Houd er rekening `response_mode=fragment` mee dat dit verzoek alleen wordt gebruikt voor demonstratiedoeleinden. Wij raden u `response_mode=form_post`aan .
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Door machtigingsscopes op te `response_type=id_token code`nemen in de aanvraag en door het gebruik van het eindpunt `scope` van het Microsoft-identiteitsplatform, zorgt het eindpunt van het Microsoft-identiteitsplatform ervoor dat de gebruiker heeft ingestemd met de machtigingen die in de queryparameter zijn vermeld. Het retourneert een autorisatiecode naar uw app om in te wisselen voor een toegangstoken.

### <a name="successful-response"></a>Succesvolle reactie

Een succesvol antwoord `response_mode=form_post` van het gebruik ziet er als volgt uit:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parameter | Beschrijving |
| --- | --- |
| `id_token` | Het ID-token dat de app heeft aangevraagd. U het ID-token gebruiken om de identiteit van de gebruiker te verifiëren en een sessie met de gebruiker te beginnen. Meer informatie over ID-tokens en de inhoud ervan vindt u in de [ `id_tokens` referentie.](id-tokens.md) |
| `code` | De autorisatiecode die de app heeft aangevraagd. De app kan de autorisatiecode gebruiken om een toegangstoken voor de doelbron aan te vragen. Een autorisatiecode is van korte duur. Normaal gesproken verloopt een autorisatiecode binnen ongeveer 10 minuten. |
| `state` | Als een parameter in de status in de aanvraag is opgenomen, moet dezelfde waarde in het antwoord worden weergegeven. De app moet controleren of de statuswaarden in de aanvraag en het antwoord identiek zijn. |

### <a name="error-response"></a>Foutreactie

Foutreacties kunnen ook worden verzonden naar de omleiding URI, zodat de app ze op de juiste manier kan verwerken. Een foutreactie ziet er als volgt uit:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschrijving |
| --- | --- |
| `error` | Een foutcodetekenreeks die u gebruiken om typen fouten die optreden te classificeren en om te reageren op fouten. |
| `error_description` | Een specifiek foutbericht waarmee u de hoofdoorzaak van een verificatiefout identificeren. |

Zie [Foutcodes voor eindpuntfouten voor autorisatievoor](#error-codes-for-authorization-endpoint-errors)een beschrijving van mogelijke foutcodes en aanbevolen clientreacties.

Wanneer u een autorisatiecode en een ID-token hebt, u de gebruiker aanmelden en namens hen toegangstokens ontvangen. Als u de gebruiker wilt aanmelden, moet u het ID-token [precies zoals beschreven](id-tokens.md#validating-an-id_token)valideren. Volg de stappen die zijn beschreven in [oAuth-codestroomdocumentatie](v2-oauth2-auth-code-flow.md#request-an-access-token)om toegangstokens te ontvangen.
