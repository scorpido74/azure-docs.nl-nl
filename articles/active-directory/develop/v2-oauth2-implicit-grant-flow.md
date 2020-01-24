---
title: OAuth 2,0 impliciete toekennings stroom-micro soft Identity-platform | Azure
description: Beveilig apps met één pagina met een impliciete stroom van micro soft Identity platform.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 42d315b44a76e79d6f1db48e5024094099564a98
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700478"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Micro soft Identity-platform en impliciete toekennings stroom

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Met het micro soft Identity platform-eind punt kunt u gebruikers aan uw apps met één pagina ondertekenen met zowel privé-als werk-of school accounts van micro soft. Eén pagina en andere Java script-apps die voornamelijk worden uitgevoerd in een browser, hebben een paar interessante uitdagingen wanneer de verificatie is:

* De beveiligings kenmerken van deze apps zijn aanzienlijk anders dan traditionele webtoepassingen op de server.
* Veel autorisatie servers en id-providers bieden geen ondersteuning voor CORS-aanvragen.
* De volledige pagina browser omleidingen van de app worden met name invasief voor de gebruikers ervaring.

Voor deze toepassingen (AngularJS, wordt. js, reageren. js, enzovoort) ondersteunt het micro soft Identity-platform de OAuth 2,0 impliciete toekennings stroom. De impliciete stroom wordt beschreven in de [OAuth 2,0-specificatie](https://tools.ietf.org/html/rfc6749#section-4.2). Het belangrijkste voor deel is dat de app tokens van het micro soft Identity-platform kan ophalen zonder een back-end-server referentie-uitwisseling uit te voeren. Hiermee kan de app zich aanmelden bij de gebruiker, de sessie beheren en tokens ophalen voor andere web-Api's in de Java script-code van de client. Er zijn enkele belang rijke beveiligings overwegingen waarbij rekening moet worden gehouden bij het gebruik van de impliciete stroom om de [client](https://tools.ietf.org/html/rfc6749#section-10.3) -en [gebruikers imitatie](https://tools.ietf.org/html/rfc6749#section-10.3)te gebruiken.

In dit artikel wordt beschreven hoe u direct kunt Program meren met het protocol in uw toepassing.  Als dat mogelijk is, kunt u het beste de ondersteunde micro soft-verificatie bibliotheken (MSAL) gebruiken in plaats van [tokens te verkrijgen en beveiligde web-api's](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)aan te roepen.  Bekijk ook de voor beeld- [apps die gebruikmaken van MSAL](sample-v2-code.md).

Als u echter liever geen bibliotheek gebruikt in een app met één pagina en zelf protocol berichten verzendt, volgt u de algemene stappen hieronder.

> [!NOTE]
> Niet alle Azure Active Directory-scenario's en-functies (Azure AD) worden ondersteund door het micro soft Identity platform-eind punt. Lees over [micro soft Identity platform-beperkingen](active-directory-v2-limitations.md)om te bepalen of u het micro soft Identity platform-eind punt moet gebruiken.

## <a name="protocol-diagram"></a>Protocol diagram

In het volgende diagram ziet u hoe de volledige impliciete aanmeldings stroom eruit ziet en de secties die volgen elke stap in meer detail beschrijven.

![Diagram waarin de impliciete aanmeldings stroom wordt weer gegeven](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>De aanmeldings aanvraag verzenden

Als u de gebruiker in de app wilt ondertekenen, kunt u een aanvraag voor [OpenID Connect Connect](v2-protocols-oidc.md) -verificatie verzenden en een `id_token` ophalen van het micro soft Identity platform-eind punt.

> [!IMPORTANT]
> Als u een ID-token en/of een toegangs token wilt aanvragen, moet de registratie van de app op de [Azure Portal-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) pagina de bijbehorende impliciete toekennings stroom hebben ingeschakeld, door **id-tokens** en. of **toegangs tokens** te selecteren onder de sectie **impliciete toekenning** . Als deze niet is ingeschakeld, wordt er een `unsupported_response` fout geretourneerd: **de opgegeven waarde voor de invoer parameter response_type is niet toegestaan voor deze client. De verwachte waarde is ' code '**

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
> Als u het aanmelden wilt testen met behulp van de impliciete stroom, klikt u op <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Nadat u zich hebt aangemeld, moet uw browser worden omgeleid naar `https://localhost/myapp/` met een `id_token` in de adres balk.
>

| Parameter |  | Beschrijving |
| --- | --- | --- |
| `tenant` | vereist |De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich kan aanmelden bij de toepassing. De toegestane waarden zijn `common`, `organizations`, `consumers`en Tenant-id's. Zie [basis beginselen van protocollen](active-directory-v2-protocols.md#endpoints)voor meer informatie. |
| `client_id` | vereist | De ID van de toepassing (client) die de [Azure Portal-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) pagina die aan uw app is toegewezen. |
| `response_type` | vereist |Moet `id_token` voor OpenID Connect Connect-aanmelding bevatten. Het kan ook de response_type `token`omvatten. Als u `token` hier gebruikt, kan uw app direct vanuit het eind punt voor toestemming een toegangs token ontvangen zonder dat hiervoor een tweede aanvraag moet worden verzonden naar het toestemming eind punt. Als u de `token`-response_type gebruikt, moet de `scope`-para meter een bereik bevatten dat aangeeft voor welke resource het token moet worden uitgegeven (bijvoorbeeld gebruiker. Read op Microsoft Graph).  |
| `redirect_uri` | aanbevolen |De redirect_uri van uw app, waar verificatie reacties kunnen worden verzonden en ontvangen door uw app. Het moet exact overeenkomen met een van de redirect_uris die u in de portal hebt geregistreerd, behalve het moet een URL-code ring zijn. |
| `scope` | vereist |Een lijst met door spaties gescheiden [bereiken](v2-permissions-and-consent.md). Voor OpenID Connect Connect (id_tokens) moet deze het bereik `openid`bevatten, dat wordt omgezet in de machtiging ' Meld u aan ' in de gebruikers interface van de toestemming. U kunt eventueel ook de `email` en `profile` bereiken voor het verkrijgen van toegang tot aanvullende gebruikers gegevens toevoegen. Als er een toegangs token wordt aangevraagd, kunt u ook andere bereiken in deze aanvraag voor het aanvragen van een toestemming voor verschillende bronnen toevoegen. |
| `response_mode` | optioneel |Hiermee geeft u de methode op die moet worden gebruikt om het resulterende token terug naar uw app te verzenden. De standaard instelling is een query alleen voor een toegangs token, maar fragment als de aanvraag een id_token bevat. |
| `state` | aanbevolen |Een waarde die in de aanvraag is opgenomen en die ook wordt geretourneerd in de token reactie. Dit kan een teken reeks zijn van alle inhoud die u wilt. Een wille keurig gegenereerde unieke waarde wordt doorgaans gebruikt om [vervalsing van aanvragen op meerdere sites](https://tools.ietf.org/html/rfc6749#section-10.12)te voor komen. De status wordt ook gebruikt voor het coderen van informatie over de status van de gebruiker in de app voordat de verificatie aanvraag is uitgevoerd, zoals de pagina of weer gave waarin ze zich bevonden. |
| `nonce` | vereist |Een waarde die is opgenomen in de aanvraag, gegenereerd door de app, die wordt opgenomen in de resulterende id_token als een claim. De app kan vervolgens deze waarde verifiëren om token replay-aanvallen te verhelpen. De waarde is doorgaans een wille keurige, unieke teken reeks die kan worden gebruikt om de oorsprong van de aanvraag te identificeren. Alleen vereist wanneer een id_token wordt aangevraagd. |
| `prompt` | optioneel |Hiermee wordt het type gebruikers interactie aangegeven dat vereist is. De enige geldige waarden op dit moment zijn ' aanmelding, ' none ', ' select_account ' en ' instemming '. `prompt=login` dwingt de gebruiker de referenties op die aanvraag in te voeren, waarbij eenmalige aanmelding wordt genegeerd. `prompt=none` het tegenovergestelde is, zorgt er dan voor dat de gebruiker niet wordt weer gegeven met een interactieve prompt. Als de aanvraag niet op de achtergrond kan worden voltooid via eenmalige aanmelding, wordt door het micro soft Identity platform-eind punt een fout geretourneerd. `prompt=select_account` stuurt de gebruiker naar een account kiezer waarbij alle accounts die in de sessie zijn onthouden, worden weer gegeven. `prompt=consent` wordt het OAuth-dialoog venster voor toestemming geactiveerd nadat de gebruiker zich heeft aangemeld, waarin de gebruiker wordt gevraagd om machtigingen te verlenen aan de app. |
| `login_hint`  |optioneel |Kan worden gebruikt om het veld gebruikers naam/e-mail adres vooraf in te vullen op de aanmeldings pagina voor de gebruiker als u de gebruikers naam van tevoren kent. Vaak gebruiken apps deze para meter tijdens het opnieuw verifiëren, waarbij de gebruikers naam al is geëxtraheerd van een eerdere aanmelding met behulp van de `preferred_username` claim.|
| `domain_hint` | optioneel |Indien opgenomen, wordt het op e-mail gebaseerde detectie proces overs Laan dat de gebruiker op de aanmeldings pagina doorloopt, waardoor er iets meer gestroomlijnde gebruikers ervaring is. Dit wordt vaak gebruikt voor line-of-Business-Apps die worden uitgevoerd in één Tenant, waar ze een domein naam binnen een bepaalde Tenant opgeven.  Hiermee wordt de gebruiker doorgestuurd naar de Federation-provider voor die Tenant.  Houd er rekening mee dat de gast gebruikers zich niet kunnen aanmelden bij deze toepassing.  |

Op dit moment wordt de gebruiker gevraagd om hun referenties in te voeren en de verificatie te volt ooien. Het micro soft Identity platform-eind punt zorgt er ook voor dat de gebruiker heeft ingestemd met de machtigingen die in de `scope` query parameter worden aangegeven. Als de gebruiker heeft ingestemd met **geen** van deze machtigingen, wordt de gebruiker gevraagd om toestemming te geven voor de vereiste machtigingen. Zie [machtigingen, toestemming en multi tenant-apps](v2-permissions-and-consent.md)voor meer informatie.

Zodra de gebruiker zich heeft geverifieerd en toestemming verleent, stuurt het micro soft Identity platform-eind punt een reactie op uw app bij de aangegeven `redirect_uri`, met de methode die is opgegeven in de para meter `response_mode`.

#### <a name="successful-response"></a>Geslaagde reactie

Een geslaagde reactie met `response_mode=fragment` en `response_type=id_token+token` ziet er als volgt uit (met regel einden voor de Lees baarheid):

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parameter | Beschrijving |
| --- | --- |
| `access_token` |Opgenomen als `response_type` `token`bevat. Het toegangs token dat door de app is aangevraagd. Het toegangs token mag niet worden gedecodeerd of anderszins worden geïnspecteerd, maar moet worden beschouwd als een ondoorzichtige teken reeks. |
| `token_type` |Opgenomen als `response_type` `token`bevat. Wordt altijd `Bearer`. |
| `expires_in`|Opgenomen als `response_type` `token`bevat. Hiermee wordt het aantal seconden aangegeven dat het token geldig is, voor caching-doel einden. |
| `scope` |Opgenomen als `response_type` `token`bevat. Hiermee worden de scope (s) aangegeven waarvoor de access_token geldig is. Mag niet alle aangevraagde bereiken omvatten als deze niet van toepassing zijn op de gebruiker (in het geval van alleen Azure AD-scopes die worden aangevraagd wanneer een persoonlijk account wordt gebruikt om zich aan te melden). |
| `id_token` | Een ondertekende JSON Web Token (JWT). De app kan de segmenten van deze token decoderen om informatie aan te vragen over de gebruiker die zich heeft aangemeld. De app kan de waarden in de cache opslaan en weer geven, maar deze hoeft niet te worden gebaseerd op autorisatie-of beveiligings grenzen. Zie de [`id_token reference`](id-tokens.md)voor meer informatie over id_tokens. <br> **Opmerking:** Alleen opgegeven als `openid` bereik is aangevraagd. |
| `state` |Als een para meter State is opgenomen in de aanvraag, moet dezelfde waarde in het antwoord worden weer gegeven. De app moet controleren of de status waarden in de aanvraag en het antwoord identiek zijn. |

#### <a name="error-response"></a>Fout bericht

Fout reacties kunnen ook worden verzonden naar de `redirect_uri` zodat de app deze op de juiste wijze kan afhandelen:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschrijving |
| --- | --- |
| `error` |Een teken reeks voor fout codes die kan worden gebruikt voor het classificeren van typen fouten die optreden en kunnen worden gebruikt om te reageren op fouten. |
| `error_description` |Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Toegangs tokens op de achtergrond ontvangen

Nu u de gebruiker hebt ondertekend in een app met één pagina, kunt u op de achtergrond toegangs tokens ontvangen voor het aanroepen van web-Api's die zijn beveiligd met micro soft Identity platform, zoals de [Microsoft Graph](https://developer.microsoft.com/graph). Zelfs als u al een token hebt ontvangen met behulp van de `token` response_type, kunt u deze methode gebruiken om tokens te verkrijgen aan aanvullende bronnen zonder dat de gebruiker opnieuw moet worden omgeleid om zich opnieuw aan te melden.

In de normale OpenID Connect Connect-verbinding/OAuth-stroom kunt u dit doen door een aanvraag te doen voor het micro soft Identity platform `/token`-eind punt. Het micro soft Identity platform-eind punt biedt echter geen ondersteuning voor CORS-aanvragen, dus het maken van AJAX-aanroepen voor het verkrijgen en vernieuwen van tokens is niet in de vraag. In plaats daarvan kunt u de impliciete stroom in een verborgen iframe gebruiken om nieuwe tokens voor andere web-Api's op te halen: 

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

Zie [de aanmeldings aanvraag verzenden](#send-the-sign-in-request)voor meer informatie over de query parameters in de URL.

> [!TIP]
> Kopieer & de onderstaande aanvraag op een browser tabblad te plakken. (Vergeet niet om de `login_hint` waarden te vervangen door de juiste waarde voor uw gebruiker)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

Dankzij de para meter `prompt=none`, kan deze aanvraag onmiddellijk slagen of mislukken en terugkeren naar uw toepassing. Er wordt een geslaagde reactie verzonden naar uw app op de aangegeven `redirect_uri`, met behulp van de methode die is opgegeven in de para meter `response_mode`.

#### <a name="successful-response"></a>Geslaagde reactie

Een geslaagde reactie met `response_mode=fragment` ziet er als volgt uit:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parameter | Beschrijving |
| --- | --- |
| `access_token` |Opgenomen als `response_type` `token`bevat. Het toegangs token dat de app heeft aangevraagd, in dit geval voor de Microsoft Graph. Het toegangs token mag niet worden gedecodeerd of anderszins worden geïnspecteerd, maar moet worden beschouwd als een ondoorzichtige teken reeks. |
| `token_type` | Wordt altijd `Bearer`. |
| `expires_in` | Hiermee wordt het aantal seconden aangegeven dat het token geldig is, voor caching-doel einden. |
| `scope` | Hiermee worden de scope (s) aangegeven waarvoor de access_token geldig is. Mag niet alle aangevraagde bereiken omvatten als deze niet van toepassing zijn op de gebruiker (in het geval van alleen Azure AD-scopes die worden aangevraagd wanneer een persoonlijk account wordt gebruikt om zich aan te melden). |
| `id_token` | Een ondertekende JSON Web Token (JWT). Opgenomen als `response_type` `id_token`bevat. De app kan de segmenten van deze token decoderen om informatie aan te vragen over de gebruiker die zich heeft aangemeld. De app kan de waarden in de cache opslaan en weer geven, maar deze hoeft niet te worden gebaseerd op autorisatie-of beveiligings grenzen. Zie voor meer informatie over id_tokens de [`id_token` verwijzing](id-tokens.md). <br> **Opmerking:** Alleen opgegeven als `openid` bereik is aangevraagd. |
| `state` |Als een para meter State is opgenomen in de aanvraag, moet dezelfde waarde in het antwoord worden weer gegeven. De app moet controleren of de status waarden in de aanvraag en het antwoord identiek zijn. |

#### <a name="error-response"></a>Fout bericht

Fout reacties kunnen ook worden verzonden naar de `redirect_uri` zodat de app deze op de juiste wijze kan afhandelen. In het geval van `prompt=none`is een verwachte fout:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beschrijving |
| --- | --- |
| `error` |Een teken reeks voor fout codes die kan worden gebruikt voor het classificeren van typen fouten die optreden en kunnen worden gebruikt om te reageren op fouten. |
| `error_description` |Een specifiek fout bericht dat een ontwikkelaar kan helpen bij het identificeren van de hoofd oorzaak van een verificatie fout. |

Als u deze fout in de iframe-aanvraag ontvangt, moet de gebruiker zich opnieuw aanmelden om een nieuw token op te halen. U kunt ervoor kiezen om deze kwestie op een wille keurige manier af te handelen voor uw toepassing.

## <a name="refreshing-tokens"></a>Tokens vernieuwen

De impliciete toekenning biedt geen vernieuwings tokens. Zowel `id_token`s als `access_token`s verlopen na een korte periode, dus uw app moet worden voor bereid om deze tokens regel matig te vernieuwen. Als u een van beide typen tokens wilt vernieuwen, kunt u dezelfde verborgen iframe-aanvraag van boven uitvoeren met behulp van de para meter `prompt=none` om het gedrag van het identiteits platform te bepalen. Als u een nieuw `id_token`wilt ontvangen, moet u `id_token` gebruiken in de `response_type` en `scope=openid`, evenals een `nonce`-para meter.

## <a name="send-a-sign-out-request"></a>Een aanvraag voor een afmelding verzenden

Met de OpenID Connect Connect `end_session_endpoint` kan uw app een aanvraag verzenden naar het micro soft Identity platform-eind punt om de sessie van een gebruiker te beëindigen en cookies te wissen die zijn ingesteld door het micro soft Identity platform-eind punt. Als u een gebruiker volledig wilt ondertekenen vanuit een webtoepassing, moet uw app een eigen sessie met de gebruiker beëindigen (meestal door het wissen van een token cache of het verwijderen van cookies), en vervolgens de browser om te leiden naar:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parameter |  | Beschrijving |
| --- | --- | --- |
| `tenant` |vereist |De `{tenant}` waarde in het pad van de aanvraag kan worden gebruikt om te bepalen wie zich kan aanmelden bij de toepassing. De toegestane waarden zijn `common`, `organizations`, `consumers`en Tenant-id's. Zie [basis beginselen van protocollen](active-directory-v2-protocols.md#endpoints)voor meer informatie. |
| `post_logout_redirect_uri` | aanbevolen | De URL waarnaar de gebruiker moet worden geretourneerd nadat de afmelding is voltooid. Deze waarde moet overeenkomen met een van de omleidings-Uri's die voor de toepassing zijn geregistreerd. Als dat niet het geval is, wordt de gebruiker een algemeen bericht weer gegeven door het micro soft Identity platform-eind punt. |

## <a name="next-steps"></a>Volgende stappen

* Ga naar de [MSAL js](sample-v2-code.md) -voor beelden om te beginnen met de code ring.
