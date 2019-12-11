---
title: Micro soft Identity platform & OpenID Connect Connect protocol | Azure
description: Bouw webtoepassingen met behulp van de micro soft Identity platform-implementatie van het OpenID Connect Connect-verificatie protocol.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc3778f31cb5dd68d3f3f49ed3cddf574b1cc3bd
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966738"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Micro soft Identity platform en OpenID Connect Connect protocol

OpenID Connect Connect is een verificatie protocol dat is gebaseerd op OAuth 2,0 en waarmee u een gebruiker veilig kunt aanmelden bij een webtoepassing. Wanneer u de implementatie van OpenID Connect Connect van het micro soft Identity platform-eind punt gebruikt, kunt u aanmelden en API-toegang toevoegen aan uw web-apps. In dit artikel wordt uitgelegd hoe u dit onafhankelijk van taal kunt doen en wordt beschreven hoe u HTTP-berichten verzendt en ontvangt zonder enige micro soft open source-bibliotheken te gebruiken.

> [!NOTE]
> Het micro soft Identity platform-eind punt biedt geen ondersteuning voor alle scenario's en functies van Azure Active Directory (Azure AD). Lees over [micro soft Identity platform-beperkingen](active-directory-v2-limitations.md)om te bepalen of u het micro soft Identity platform-eind punt moet gebruiken.

[OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html) breidt het OAuth 2,0- *autorisatie* protocol uit voor gebruik als een *verificatie* protocol, zodat u eenmalige aanmelding kunt uitvoeren met behulp van OAuth. OpenID Connect Connect introduceert het concept van een *id-token*. Dit is een beveiligings token waarmee de client de identiteit van de gebruiker kan verifiëren. Het ID-token krijgt ook basis profiel informatie over de gebruiker. Omdat OpenID Connect Connect OAuth 2,0 uitbreidt, kunnen apps veilig *toegangs tokens*verkrijgen die kunnen worden gebruikt voor toegang tot bronnen die worden beveiligd door een [autorisatie server](active-directory-v2-protocols.md#the-basics). Met het micro soft Identity platform-eind punt kunnen apps van derden die zijn geregistreerd bij Azure AD, toegangs tokens uitgeven voor beveiligde bronnen zoals web-Api's. Zie [een app registreren met het micro soft Identity platform-eind punt](quickstart-register-app.md)voor meer informatie over het instellen van een toepassing voor het uitgeven van toegangs tokens. U wordt aangeraden OpenID Connect Connect te gebruiken als u een [Webtoepassing](v2-app-types.md#web-apps) bouwt die wordt gehost op een server en toegankelijk is via een browser.

## <a name="protocol-diagram-sign-in"></a>Protocol diagram: aanmelden

De meest eenvoudige aanmeldings stroom bevat de stappen die in het volgende diagram worden weer gegeven. Elke stap wordt gedetailleerd beschreven in dit artikel.

![OpenID Connect Connect Protocol: aanmelden](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Het OpenID Connect Connect meta data-document ophalen

OpenID Connect Connect beschrijft een meta gegevens document met de meeste informatie die vereist is voor een app om u aan te melden. Dit omvat informatie zoals de Url's die moeten worden gebruikt en de locatie van de open bare handtekeningen sleutels van de service. Voor het micro soft Identity platform-eind punt is dit het OpenID Connect Connect meta data-document dat u moet gebruiken:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Probeer het nu! Klik op [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) om de configuratie van de `common` tenants weer te geven.

Het `{tenant}` kan een van de vier volgende waarden hebben:

| Waarde | Beschrijving |
| --- | --- |
| `common` |Gebruikers met een persoonlijk Microsoft-account en een werk-of school account van Azure AD kunnen zich aanmelden bij de toepassing. |
| `organizations` |Alleen gebruikers met werk-of school accounts van Azure AD kunnen zich aanmelden bij de toepassing. |
| `consumers` |Alleen gebruikers met een persoonlijke Microsoft-account kunnen zich aanmelden bij de toepassing. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` of `contoso.onmicrosoft.com` | Alleen gebruikers van een specifieke Azure AD-Tenant (ongeacht of ze lid zijn van de directory met een werk-of school account, of gasten in de directory met een persoonlijk Microsoft-account) kunnen zich aanmelden bij de toepassing. De beschrijvende domein naam van de Azure AD-Tenant of de GUID-id van de Tenant kan worden gebruikt. U kunt ook de Tenant van de consument, `9188040d-6c67-4c5b-b112-36a304b66dad`, gebruiken in plaats van de `consumers` Tenant.  |

De meta gegevens zijn een eenvoudig JavaScript Object Notation (JSON)-document. Raadpleeg het volgende fragment voor een voor beeld. De inhoud van het fragment wordt volledig beschreven in de [OpenID Connect Connect-specificatie](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

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

Als uw app aangepaste handtekening sleutels heeft als gevolg van het gebruik van de functie voor het [toewijzen van claims](active-directory-claims-mapping.md) , moet u een `appid` query parameter met de app-id toevoegen om een `jwks_uri` te krijgen met de handtekening sleutel gegevens van uw app. Bijvoorbeeld: `https://login.microsoftonline.com/{tenant}/.well-known/v2.0/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` bevat een `jwks_uri` van `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

Normaal gesp roken gebruikt u dit meta gegevens document voor het configureren van een OpenID Connect Connect-bibliotheek of-SDK. de-bibliotheek gebruikt de meta gegevens om het werk uit te voeren. Als u echter geen vooraf gemaakte OpenID Connect Connect-bibliotheek gebruikt, kunt u de stappen in de rest van dit artikel volgen om u aan te melden in een web-app met behulp van het micro soft Identity platform-eind punt.

## <a name="send-the-sign-in-request"></a>De aanmeldings aanvraag verzenden

Als uw web-app de gebruiker moet verifiëren, kan deze de gebruiker naar het `/authorize`-eind punt sturen. Deze aanvraag is vergelijkbaar met de eerste poot van de [OAuth 2,0-autorisatie code stroom](v2-oauth2-auth-code-flow.md), met de volgende belang rijke verschillen:

* De aanvraag moet het `openid` bereik bevatten in de para meter `scope`.
* De para meter `response_type` moet `id_token`bevatten.
* De aanvraag moet de para meter `nonce` bevatten.

> [!IMPORTANT]
> Als u een ID-token wilt aanvragen vanuit het/Authorization-eind punt, moet de app-registratie in de [registratie Portal](https://portal.azure.com) de impliciete toekenning van id_tokens op het tabblad verificatie hebben ingeschakeld (Hiermee stelt u de `oauth2AllowIdTokenImplicitFlow`-vlag in het [toepassings manifest](reference-app-manifest.md) in op `true`). Als deze niet is ingeschakeld, wordt er een `unsupported_response` fout geretourneerd: de opgegeven waarde voor de invoer parameter response_type is niet toegestaan voor deze client. De verwachte waarde is ' code '

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
> Klik op de volgende koppeling om deze aanvraag uit te voeren. Nadat u zich hebt aangemeld, wordt uw browser omgeleid naar `https://localhost/myapp/`, met een ID-token in de adres balk. Houd er rekening mee dat deze aanvraag gebruikmaakt van `response_mode=fragment` (alleen voor demonstratie doeleinden). U wordt aangeraden `response_mode=form_post`te gebruiken.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parameter | Voorwaarde | Beschrijving |
| --- | --- | --- |
| `tenant` | Verplicht | U kunt de `{tenant}` waarde in het pad van de aanvraag gebruiken om te bepalen wie zich kan aanmelden bij de toepassing. De toegestane waarden zijn `common`, `organizations`, `consumers`en Tenant-id's. Zie [basis beginselen van protocollen](active-directory-v2-protocols.md#endpoints)voor meer informatie. |
| `client_id` | Verplicht | De **client-id** van de toepassing die de [Azure Portal – app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring die aan uw app is toegewezen. |
| `response_type` | Verplicht | Moet `id_token` voor OpenID Connect Connect-aanmelding bevatten. Het kan ook andere `response_type` waarden bevatten, zoals `code`. |
| `redirect_uri` | Aanbevolen | De omleidings-URI van uw app, waar verificatie reacties kunnen worden verzonden en ontvangen door uw app. De waarde moet exact overeenkomen met een van de omleidings-Uri's die u in de portal hebt geregistreerd, behalve dat deze URL moet worden gecodeerd. Als deze niet aanwezig is, kiest het eind punt één geregistreerde redirect_uri wille keurig om de gebruiker terug naar te sturen. |
| `scope` | Verplicht | Een lijst met door spaties gescheiden bereiken. Voor OpenID Connect Connect moet het bereik `openid`bevatten, dat wordt omgezet in de machtiging ' Meld u aan ' in de gebruikers interface van de toestemming. U kunt ook andere bereiken in deze aanvraag toevoegen om toestemming te vragen. |
| `nonce` | Verplicht | Een waarde die is opgenomen in de aanvraag, gegenereerd door de app, die wordt opgenomen in de resulterende id_token waarde als een claim. De app kan deze waarde verifiëren om token replay-aanvallen te verhelpen. De waarde is doorgaans een wille keurige, unieke teken reeks die kan worden gebruikt om de oorsprong van de aanvraag te identificeren. |
| `response_mode` | Aanbevolen | Hiermee geeft u de methode op die moet worden gebruikt om de resulterende autorisatie code terug te sturen naar uw app. Deze waarde kan `form_post` of `fragment` zijn. Voor webtoepassingen kunt u het beste `response_mode=form_post`gebruiken om ervoor te zorgen dat de tokens veilig worden overgedragen naar uw toepassing. |
| `state` | Aanbevolen | Een waarde die in de aanvraag is opgenomen en die ook wordt geretourneerd in het token antwoord. Dit kan een teken reeks zijn van elke gewenste inhoud. Een wille keurig gegenereerde unieke waarde wordt doorgaans gebruikt om [vervalsing van aanvragen op meerdere sites te voor komen](https://tools.ietf.org/html/rfc6749#section-10.12). De status wordt ook gebruikt om informatie over de status van de gebruiker in de app te coderen voordat de verificatie aanvraag is uitgevoerd, zoals de pagina of weer gave waarin de gebruiker zich bevond. |
| `prompt` | Optioneel | Hiermee wordt het type gebruikers interactie aangegeven dat vereist is. De enige geldige waarden op dit moment zijn `login`, `none`en `consent`. Met de `prompt=login` claim wordt de gebruiker gedwongen hun referenties in te voeren voor die aanvraag, waardoor eenmalige aanmelding wordt genegeerd. De `prompt=none` claim is het tegenovergestelde. Met deze claim zorgt u ervoor dat de gebruiker niet wordt aangeboden met een interactieve prompt op. Als de aanvraag niet op de achtergrond kan worden voltooid via eenmalige aanmelding, wordt een fout geretourneerd door het micro soft Identity platform-eind punt. Met de `prompt=consent` claim wordt het dialoog venster OAuth-toestemming geactiveerd nadat de gebruiker zich heeft aangemeld. In het dialoog venster wordt de gebruiker gevraagd om machtigingen te verlenen aan de app. |
| `login_hint` | Optioneel | U kunt deze para meter gebruiken om het veld gebruikers naam en e-mail adres vooraf in te vullen op de aanmeldings pagina voor de gebruiker, als u de gebruikers naam van tevoren kent. Apps gebruiken deze para meter vaak tijdens het opnieuw verifiëren, nadat u de gebruikers naam al hebt geëxtraheerd van een eerdere aanmelding met behulp van de `preferred_username` claim. |
| `domain_hint` | Optioneel | De realm van de gebruiker in een federatieve Directory.  Hiermee wordt het detectie proces op basis van e-mail overs Laan dat de gebruiker op de aanmeldings pagina doorloopt, voor een iets meer gestroomlijnde gebruikers ervaring. Voor tenants die zich bevinden in een on-premises Directory als AD FS, resulteert dit vaak in een naadloze aanmelding vanwege de bestaande aanmeldings sessie. |

Op dit moment wordt de gebruiker gevraagd om hun referenties in te voeren en de verificatie te volt ooien. Het micro soft Identity platform-eind punt controleert of de gebruiker heeft ingestemd met de machtigingen die zijn opgegeven in de `scope` query-para meter. Als de gebruiker niet heeft ingestemd met een van deze machtigingen, wordt de gebruiker door het micro soft Identity platform-eind punt gevraagd om toestemming te geven voor de vereiste machtigingen. U kunt meer lezen over [machtigingen, toestemming en apps met meerdere tenants](v2-permissions-and-consent.md).

Nadat de gebruiker zich heeft geverifieerd en toestemming verleent, retourneert het micro soft Identity platform-eind punt een antwoord op uw app bij de aangegeven omleidings-URI met behulp van de methode die is opgegeven in de para meter `response_mode`.

### <a name="successful-response"></a>Geslaagde reactie

Een geslaagde reactie wanneer u `response_mode=form_post` ziet er als volgt uit:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parameter | Beschrijving |
| --- | --- |
| `id_token` | Het ID-token dat de app heeft aangevraagd. U kunt de para meter `id_token` gebruiken om de identiteit van de gebruiker te verifiëren en een sessie met de gebruiker te starten. Zie de [`id_tokens`-verwijzing](id-tokens.md)voor meer informatie over id-tokens en de inhoud ervan. |
| `state` | Als de aanvraag een `state`-para meter bevat, moet dezelfde waarde in het antwoord worden weer gegeven. De app moet controleren of de status waarden in de aanvraag en het antwoord identiek zijn. |

### <a name="error-response"></a>Fout bericht

Fout reacties kunnen ook worden verzonden naar de omleidings-URI, zodat de app deze kan verwerken. Een fout bericht ziet er als volgt uit:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschrijving |
| --- | --- |
| `error` | Een teken reeks voor de fout code die u kunt gebruiken om typen fouten te classificeren die optreden en om te reageren op fouten. |
| `error_description` | Een specifiek fout bericht die u kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Fout codes voor verificatie eindpunt fouten

In de volgende tabel worden fout codes beschreven die kunnen worden geretourneerd in de para meter `error` van het fout bericht:

| Foutcode | Beschrijving | Client actie |
| --- | --- | --- |
| `invalid_request` | Protocol fout, zoals een ontbrekende, vereiste para meter. |Corrigeer en verzend de aanvraag opnieuw. Dit is een ontwikkelings fout die normaal gesp roken tijdens de eerste test wordt gedetecteerd. |
| `unauthorized_client` | De client toepassing kan geen autorisatie code aanvragen. |Dit gebeurt meestal wanneer de client toepassing niet is geregistreerd in azure AD of niet is toegevoegd aan de Azure AD-Tenant van de gebruiker. De toepassing kan de gebruiker vragen om de toepassing te installeren en deze toe te voegen aan Azure AD. |
| `access_denied` | De resource-eigenaar heeft toestemming geweigerd. |De client toepassing kan de gebruiker hiervan op de hoogte stellen dat deze niet kan door gaan, tenzij de gebruiker ermee akkoord gaat. |
| `unsupported_response_type` |De autorisatie server biedt geen ondersteuning voor het antwoord type in de aanvraag. |Corrigeer en verzend de aanvraag opnieuw. Dit is een ontwikkelings fout die normaal gesp roken tijdens de eerste test wordt gedetecteerd. |
| `server_error` | Er is een onverwachte fout opgetreden op de server. |Voer de aanvraag opnieuw uit. Deze fouten kunnen worden veroorzaakt door tijdelijke voor waarden. De client toepassing kan de gebruiker uitleggen dat de reactie is vertraagd vanwege een tijdelijke fout. |
| `temporarily_unavailable` | De server is tijdelijk niet actief om de aanvraag af te handelen. |Voer de aanvraag opnieuw uit. De client toepassing kan bijvoorbeeld verklaren dat de reactie van de gebruiker is vertraagd vanwege een tijdelijke voor waarde. |
| `invalid_resource` | De doel resource is ongeldig omdat deze niet bestaat, Azure AD niet kan worden gevonden of niet juist is geconfigureerd. |Dit geeft aan dat de resource, indien aanwezig, niet is geconfigureerd in de Tenant. De toepassing kan de gebruiker vragen om instructies voor het installeren van de toepassing en het toevoegen aan Azure AD. |

## <a name="validate-the-id-token"></a>Het ID-token valideren

Alleen de ontvangst van een id_token is voldoende om de gebruiker te verifiëren. u moet de hand tekening van de id_token valideren en de claims in het token verifiëren volgens de vereisten van uw app. Het micro soft Identity platform-eind punt maakt gebruik van [JSON Web tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) en open bare-sleutel cryptografie om tokens te ondertekenen en te controleren of ze geldig zijn.

U kunt ervoor kiezen om de `id_token` in client code te valideren, maar een veelvoorkomende procedure is het verzenden van de `id_token` naar een back-end-server en de validatie. Nadat u de hand tekening van de id_token hebt gevalideerd, moeten er enkele claims worden gecontroleerd. Zie de [`id_token` referentie](id-tokens.md) voor meer informatie, waaronder het [valideren van tokens](id-tokens.md#validating-an-id_token) en [belang rijke informatie over de rollover van de handtekening sleutel](active-directory-signing-key-rollover.md). We raden u aan om een bibliotheek te gebruiken voor het parseren en valideren van tokens. er is ten minste één beschikbaar voor de meeste talen en platformen.

Het is ook mogelijk dat u aanvullende claims wilt valideren, afhankelijk van uw scenario. Enkele veelvoorkomende validaties zijn:

* Controleren of de gebruiker/organisatie zich heeft geregistreerd voor de app.
* Controleren of de gebruiker de juiste autorisatie/bevoegdheden heeft
* Er is een zekere mate van verificatie uitgevoerd, zoals multi-factor Authentication.

Zodra u de id_token hebt gevalideerd, kunt u een sessie met de gebruiker starten en de claims in de id_token gebruiken om informatie te verkrijgen over de gebruiker in uw app. Deze informatie kan worden gebruikt voor weer gave, records, persoonlijke instellingen, enzovoort.

## <a name="send-a-sign-out-request"></a>Een afmeldings aanvraag verzenden

Wanneer u de gebruiker wilt afmelden bij uw app, is het niet voldoende om de cookies van uw app te wissen of de sessie van de gebruiker anderszins te beëindigen. U moet ook de gebruiker omleiden naar het micro soft Identity platform-eind punt om u af te melden. Als u dit niet doet, wordt de gebruiker opnieuw geverifieerd bij uw app zonder dat ze hun referenties opnieuw in te voeren, omdat ze een geldige sessie voor eenmalige aanmelding met het micro soft Identity platform-eind punt hebben.

U kunt de gebruiker omleiden naar de `end_session_endpoint` die wordt weer gegeven in het OpenID Connect Connect meta data-document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | Voorwaarde | Beschrijving |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Aanbevolen | De URL waarnaar de gebruiker wordt omgeleid na het afmelden. Als de para meter niet is opgenomen, wordt de gebruiker een algemeen bericht weer gegeven dat wordt gegenereerd door het micro soft Identity platform-eind punt. Deze URL moet overeenkomen met een van de omleidings-Uri's die zijn geregistreerd voor uw toepassing in de app-registratie Portal. |

## <a name="single-sign-out"></a>Eenmalige afmelding

Wanneer u de gebruiker omleidt naar de `end_session_endpoint`, wist het micro soft Identity platform-eind punt de sessie van de gebruiker uit de browser. De gebruiker kan echter nog steeds zijn aangemeld bij andere toepassingen die gebruikmaken van micro soft-accounts voor authenticatie. Om ervoor te zorgen dat deze toepassingen de gebruiker gelijktijdig kunnen ondertekenen, stuurt het micro soft Identity platform-eind punt een HTTP GET-aanvraag naar de geregistreerde `LogoutUrl` van alle toepassingen waarbij de gebruiker momenteel is aangemeld. Toepassingen moeten reageren op deze aanvraag door een wille keurige sessie te wissen waarmee de gebruiker wordt geïdentificeerd en een `200`-antwoord wordt geretourneerd. Als u eenmalige afmelding wilt ondersteunen in uw toepassing, moet u dergelijke `LogoutUrl` implementeren in de code van uw toepassing. U kunt de `LogoutUrl` instellen vanuit de portal voor app-registratie.

## <a name="protocol-diagram-access-token-acquisition"></a>Protocol diagram: verkrijgen van toegangs token

Veel web-apps moeten de gebruiker niet alleen kunnen ondertekenen in, maar ook toegang hebben tot een webservice namens de gebruiker met behulp van OAuth. Dit scenario bevat een combi natie van OpenID Connect Connect voor gebruikers verificatie terwijl u tegelijkertijd een autorisatie code krijgt die u kunt gebruiken om toegangs tokens op te halen als u de OAuth-autorisatie code stroom gebruikt.

De volledige OpenID connect-verbinding voor het aanmelden en de token verwervings stroom ziet er ongeveer uit als in het volgende diagram. In de volgende gedeelten van het artikel worden elke stap uitvoerig beschreven.

![OpenID Connect Connect Protocol: verwerving van tokens](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Toegangs tokens ophalen
Als u toegangs tokens wilt verkrijgen, wijzigt u de aanmeldings aanvraag:

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
> Klik op de volgende koppeling om deze aanvraag uit te voeren. Nadat u zich hebt aangemeld, wordt uw browser omgeleid naar `https://localhost/myapp/`, met een ID-token en een code in de adres balk. Houd er rekening mee dat deze aanvraag alleen `response_mode=fragment` gebruikt voor demonstratie doeleinden. U wordt aangeraden `response_mode=form_post`te gebruiken.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Door de machtigings bereiken in de aanvraag in te nemen en door gebruik te maken van `response_type=id_token code`, zorgt het micro soft Identity platform-eind punt ervoor dat de gebruiker heeft ingestemd met de machtigingen die zijn aangegeven in de `scope` query-para meter. Er wordt een autorisatie code voor de app naar Exchange geretourneerd voor een toegangs token.

### <a name="successful-response"></a>Geslaagde reactie

Een geslaagde reactie van het gebruik van `response_mode=form_post` ziet er als volgt uit:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parameter | Beschrijving |
| --- | --- |
| `id_token` | Het ID-token dat de app heeft aangevraagd. U kunt het ID-token gebruiken om de identiteit van de gebruiker te verifiëren en een sessie met de gebruiker te starten. Meer informatie over ID-tokens en de inhoud ervan vindt u in de [referentie`id_tokens`](id-tokens.md). |
| `code` | De autorisatie code die door de app is aangevraagd. De app kan de autorisatie code gebruiken om een toegangs token aan te vragen voor de doel bron. Een autorisatie code is korte duur. Een autorisatie code verloopt doorgaans in ongeveer 10 minuten. |
| `state` | Als een para meter State is opgenomen in de aanvraag, moet dezelfde waarde in het antwoord worden weer gegeven. De app moet controleren of de status waarden in de aanvraag en het antwoord identiek zijn. |

### <a name="error-response"></a>Fout bericht

Fout reacties kunnen ook worden verzonden naar de omleidings-URI, zodat de app deze op de juiste wijze kan afhandelen. Een fout bericht ziet er als volgt uit:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschrijving |
| --- | --- |
| `error` | Een teken reeks voor de fout code die u kunt gebruiken om typen fouten te classificeren die optreden en om te reageren op fouten. |
| `error_description` | Een specifiek fout bericht die u kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |

Zie voor een beschrijving van mogelijke fout codes en aanbevolen client reacties [fout codes voor verificatie eindpunt fouten](#error-codes-for-authorization-endpoint-errors).

Wanneer u een autorisatie code en een ID-token hebt, kunt u de gebruiker ondertekenen in en toegangs tokens voor hun naam krijgen. Als u de gebruiker wilt ondertekenen in, moet u de ID-token [precies zo valideren als beschreven](id-tokens.md#validating-an-id_token). Volg de stappen die worden beschreven in de documentatie van de [OAuth-code stroom](v2-oauth2-auth-code-flow.md#request-an-access-token)om toegangs tokens te verkrijgen.
