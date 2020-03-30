---
title: Richtlijnen voor Azure AD Conditional Access-ontwikkelaars
description: Richtlijnen voor ontwikkelaars en scenario's voor voorwaardelijke toegang tot Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ROBOTS: NOINDEX
ms.openlocfilehash: 92acb1a475fbd41bfb7351d73c61db866ce2bbc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154930"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Richtlijnen voor ontwikkelaars voor voorwaardelijke toegang voor Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

De functie Voorwaardelijke toegang in Azure Active Directory (Azure AD) biedt een van de verschillende manieren waarop u uw app beveiligen en een service beveiligen. Conditional Access stelt ontwikkelaars en zakelijke klanten in staat om services op verschillende manieren te beschermen, waaronder:

* Multi-Factor Authentication
* Alleen intune-ingeschreven apparaten toegang geven tot specifieke services
* Gebruikerslocaties en IP-bereiken beperken

Zie [Voorwaardelijke toegang in Azure Active Directory](../active-directory-conditional-access-azure-portal.md)voor meer informatie over de volledige mogelijkheden van voorwaardelijke toegang.

Voor ontwikkelaars die apps voor Azure AD bouwen, wordt in dit artikel weergegeven hoe u Voorwaardelijke toegang gebruiken en u leert ook over de impact van toegang tot bronnen waarover u geen controle hebt waarop mogelijk beleid voor voorwaardelijke toegang is toegepast. Het artikel onderzoekt ook de implicaties van voorwaardelijke toegang in de on-behalf-of-flow, web-apps, toegang tot Microsoft Graph en het aanroepen van API's.

Kennis van apps met [één en meerdere huurders](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) en algemene [verificatiepatronen](v1-authentication-scenarios.md) wordt verondersteld.

## <a name="how-does-conditional-access-impact-an-app"></a>Welke invloed heeft Voorwaardelijke toegang op een app?

### <a name="app-types-impacted"></a>Invloed op app-typen

In de meeste voorkomende gevallen verandert Voorwaardelijke toegang het gedrag van een app niet of zijn er wijzigingen van de ontwikkelaar vereist.Alleen in bepaalde gevallen waarin een app indirect of in stilte een token voor een service aanvraagt, vereist een app codewijzigingen om "uitdagingen" voor voorwaardelijke toegang af te handelen.Het kan net zo eenvoudig zijn als het uitvoeren van een interactieve aanmeldingsaanvraag.

In het bijzonder vereisen de volgende scenario's code om "uitdagingen voor voorwaardelijke toegang" af te handelen:

* Apps die de on-behalf-of-flow uitvoeren
* Apps die toegang hebben tot meerdere services/resources
* Apps met één pagina met ADAL.js
* Web Apps die een bron aanroepen

Beleid voor voorwaardelijke toegang kan worden toegepast op de app, maar kan ook worden toegepast op een web-API die uw app opent. Zie [Snelstart: MFA vereisen voor specifieke apps met Voorwaardelijke toegang met Azure Active Directory Conditional Access](../conditional-access/app-based-mfa.md)voor meer informatie over het configureren van een beleid voor voorwaardelijke toegang.

Afhankelijk van het scenario kan een zakelijke klant op elk gewenst moment beleid voor voorwaardelijke toegang toepassen en verwijderen. Als uw app kan blijven functioneren wanneer een nieuw beleid wordt toegepast, moet u de "uitdaging" afhandelen implementeren. De volgende voorbeelden illustreren challenge handling.

### <a name="conditional-access-examples"></a>Voorbeelden van voorwaardelijke toegang

Voor sommige scenario's is codewijzigingen vereist om voorwaardelijke toegang te verwerken, terwijl andere werken zoals het is. Hier volgen een paar scenario's met voorwaardelijke toegang om multi-factor authenticatie uit te brengen die enig inzicht geeft in het verschil.

* U bouwt één iOS-app met één tenant en past een beleid voor voorwaardelijke toegang toe. De app meldt zich aan bij een gebruiker en vraagt geen toegang tot een API. Wanneer de gebruiker zich aanmeldt, wordt het beleid automatisch aangeroepen en moet de gebruiker multi-factor authenticatie (MFA) uitvoeren. 
* U bouwt een native app die een middle tier-service gebruikt om toegang te krijgen tot een downstream-API. Een zakelijke klant bij het bedrijf dat deze app gebruikt, past een beleid toe op de downstream-API. Wanneer een eindgebruiker zich aanmeldt, vraagt de native app toegang tot de middelste laag en stuurt het token. De middelste laag voert namens de stroom uit om toegang tot de downstream-API aan te vragen. Op dit punt, een vorderingen "uitdaging" wordt gepresenteerd aan de middelste laag. De middelste laag stuurt de uitdaging terug naar de native app, die moet voldoen aan het beleid voor voorwaardelijke toegang.

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph heeft speciale overwegingen bij het bouwen van apps in voorwaardelijke toegangsomgevingen. Over het algemeen gedragen de mechanica van Voorwaardelijke toegang zich hetzelfde, maar het beleid dat uw gebruikers zien, is gebaseerd op de onderliggende gegevens die uw app in de grafiek opvraagt. 

In het bijzonder vertegenwoordigen alle Microsoft Graph-scopes een gegevensset waarmee afzonderlijk beleid kan worden toegepast. Aangezien aan beleid voor voorwaardelijke toegang de specifieke gegevenssets zijn toegewezen, dwingt Azure AD beleid voor voorwaardelijke toegang af op basis van de gegevens achter Graph - in plaats van Graph zelf.

Als een app bijvoorbeeld de volgende Microsoft Graph-scopes opvraagt,

```
scopes="Bookings.Read.All Mail.Read"
```

Een app kan van hun gebruikers verwachten dat ze voldoen aan alle beleidsregels die zijn ingesteld op Boekingen en Exchange. Sommige scopes kunnen worden toegewezen aan meerdere gegevenssets als deze toegang verlenen. 

### <a name="complying-with-a-conditional-access-policy"></a>Voldoen aan een beleid voor voorwaardelijke toegang

Voor verschillende app-topologieën wordt een beleid voor voorwaardelijke toegang geëvalueerd wanneer de sessie wordt ingesteld. Aangezien een beleid voor voorwaardelijke toegang werkt op de granulariteit van apps en services, is het punt waarop het wordt aangeroepen sterk afhankelijk van het scenario dat u probeert te bereiken.

Wanneer uw app probeert toegang te krijgen tot een service met een beleid voor voorwaardelijke toegang, kan deze worden geconfronteerd met een uitdaging voor voorwaardelijke toegang. Deze uitdaging is gecodeerd `claims` in de parameter die wordt geleverd in een reactie van Azure AD. Hier volgt een voorbeeld van deze uitdagingsparameter: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Ontwikkelaars kunnen deze uitdaging aangaan en toevoegen aan een nieuw verzoek aan Azure AD. Als u deze status doorgeeft, wordt de eindgebruiker gevraagd alle actie uit te voeren die nodig is om te voldoen aan het beleid voor voorwaardelijke toegang. In de volgende scenario's worden details van de fout en hoe de parameter moet worden geëxtraheerd.

## <a name="scenarios"></a>Scenario's

### <a name="prerequisites"></a>Vereisten

Voorwaardelijke toegang voor Azure AD is een functie die is opgenomen in [Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Meer informatie over licentievereisten vindt u in het [rapport voor gebruik zonder licentie.](../active-directory-conditional-access-unlicensed-usage-report.md) Ontwikkelaars kunnen lid worden van het [Microsoft Developer Network,](https://msdn.microsoft.com/dn308572.aspx)inclusief een gratis abonnement op de Enterprise Mobility Suite, waaronder Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Overwegingen voor specifieke scenario's

De volgende informatie is alleen van toepassing in deze scenario's voor voorwaardelijke toegang:

* Apps die de on-behalf-of-flow uitvoeren
* Apps die toegang hebben tot meerdere services/resources
* Apps met één pagina met ADAL.js

In de volgende secties worden veelvoorkomende scenario's besproken die complexer zijn. Het basisprincipe is Conditional Access-beleid worden geëvalueerd op het moment dat het token wordt aangevraagd voor de service waarvoor een beleid voor voorwaardelijke toegang is toegepast.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scenario: App die de stroom namens de stroom uitvoert

In dit scenario doorlopen we het geval waarin een native app een webservice/API aanroept. Op zijn beurt doet deze service de "on-behalf-of" stroom naar een downstream service bellen. In ons geval hebben we ons beleid voor voorwaardelijke toegang toegepast op de downstream-service (Web API 2) en gebruiken we een native app in plaats van een server/daemon-app. 

![App die het stroomdiagram namens de stroom uitvoert](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

De eerste tokenaanvraag voor Web API 1 vraagt de eindgebruiker niet om multi-factor authenticatie, omdat Web API 1 mogelijk niet altijd op de downstream-API raakt. Zodra Web API 1 probeert een token namens de gebruiker aan te vragen voor Web API 2, mislukt de aanvraag omdat de gebruiker zich niet heeft aangemeld met multi-factor authenticatie.

Azure AD retourneert een HTTP-antwoord met enkele interessante gegevens:

> [!NOTE]
> In dit geval is het een multi-factor authenticatie foutbeschrijving, `interaction_required` maar er is een breed scala van mogelijke met betrekking tot voorwaardelijke toegang.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

In Web API 1 vangen `error=interaction_required`we de `claims` fout en sturen we de uitdaging terug naar de desktop-app. Op dat moment kan de bureaublad-app een `acquireToken()` `claims`nieuw gesprek voeren en de uitdaging toevoegen als een extra querytekenreeksparameter. Deze nieuwe aanvraag vereist dat de gebruiker multi-factor authenticatie doet en dit nieuwe token vervolgens terugstuurt naar Web API 1 en de on-behalf-of-flow voltooit.

Zie ons [.NET-codevoorbeeld](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca)om dit scenario uit te proberen. Het laat zien hoe je de claimuitdaging teruggeeft van Web API 1 naar de native app en een nieuw verzoek maken in de client-app.

## <a name="scenario-app-accessing-multiple-services"></a>Scenario: App toegang tot meerdere services

In dit scenario doorlopen we de case waarin een web-app toegang heeft tot twee services waarvan er één een conditional access-beleid heeft toegewezen. Afhankelijk van de logica van uw app bestaat er mogelijk een pad waarin uw app geen toegang tot beide webservices vereist. In dit scenario speelt de volgorde waarin u een token aanvraagt een belangrijke rol in de gebruikerservaring.

Laten we aannemen dat we webservice A en B hebben en webservice B ons beleid voor voorwaardelijke toegang heeft toegepast. Hoewel de initiële interactieve auth-aanvraag toestemming vereist voor beide services, is het beleid voor voorwaardelijke toegang niet in alle gevallen vereist. Als de app een token voor webservice B aanvraagt, wordt het beleid aangeroepen en slagen latere aanvragen voor webservice A ook als volgt.

![App-stroomdiagram voor meerdere services](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Als de app in eerste instantie een token voor webservice A aanvraagt, beroept de eindgebruiker zich niet op het beleid voor voorwaardelijke toegang. Hierdoor kan de app-ontwikkelaar de eindgebruikerservaring beheren en het beleid voor voorwaardelijke toegang in alle gevallen niet dwingen. Het lastige geval is als de app vervolgens een token aanvraagt voor webservice B. Op dit moment moet de eindgebruiker voldoen aan het beleid voor voorwaardelijke toegang. Wanneer de app `acquireToken`probeert te , kan het genereren van de volgende fout (geïllustreerd in het volgende diagram):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![App die toegang krijgt tot meerdere services die een nieuw token aanvragen](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Als de app de ADAL-bibliotheek gebruikt, wordt het niet aanschaffen van het token altijd interactief opnieuw geprobeerd. Wanneer deze interactieve aanvraag plaatsvindt, heeft de eindgebruiker de mogelijkheid om te voldoen aan de voorwaardelijke toegang. Dit is waar, tenzij `AcquireTokenSilentAsync` `PromptBehavior.Never` het verzoek een of in ```AcquireToken``` welk geval de app moet een interactief verzoek uit te voeren om de eindgebruiker de mogelijkheid om te voldoen aan het beleid.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Scenario: Single-page app (SPA) met ADAL.js

In dit scenario lopen we door de aanvraag wanneer we een app met één pagina (SPA) hebben, waarbij we ADAL.js gebruiken om een voorwaardelijke toegangsbeveiligde web-API aan te roepen. Dit is een eenvoudige architectuur, maar heeft een aantal nuances waarmee rekening moet worden gehouden bij het ontwikkelen rond voorwaardelijke toegang.

In ADAL.js zijn er een paar `login()`functies `acquireToken(...)` `acquireTokenPopup(…)`die `acquireTokenRedirect(…)`tokens verkrijgen: , , en .

* `login()`verkrijgt een ID-token via een interactieve aanmeldingsaanvraag, maar verkrijgt geen toegangstokens voor een service (inclusief een voorwaardelijke toegangsbeveiligde web-API).
* `acquireToken(…)`kan vervolgens worden gebruikt om in stilte een toegangstoken te verkrijgen, wat betekent dat het ui in geen enkele omstandigheid weergeeft.
* `acquireTokenPopup(…)`en `acquireTokenRedirect(…)` zijn beide gewend om interactief een token aan te vragen voor een resource, wat betekent dat ze altijd aanmeldingsgebruikersinterface tonen.

Wanneer een app een toegangstoken nodig heeft om `acquireToken(…)`een web-API aan te roepen, wordt geprobeerd een . Als de tokensessie is verlopen of als we moeten voldoen aan een beleid voor `acquireTokenPopup()` `acquireTokenRedirect()`voorwaardelijke toegang, mislukt de *acquireToken-functie* en gebruikt de app of .

![App met één pagina met ADAL-stroomdiagram](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Laten we een voorbeeld doorlopen met ons scenario voor voorwaardelijke toegang. De eindgebruiker is net geland op de site en heeft geen sessie. We voeren `login()` een oproep uit, krijgen een ID-token zonder meervoudige verificatie. Vervolgens drukt de gebruiker op een knop waarvoor de app gegevens moet aanvragen uit een web-API. De app probeert `acquireToken()` een gesprek te voeren, maar mislukt omdat de gebruiker nog geen multi-factor authenticatie heeft uitgevoerd en moet voldoen aan het beleid voor voorwaardelijke toegang.

Azure AD stuurt het volgende HTTP-antwoord terug:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Onze app moet `error=interaction_required`de . De toepassing kan `acquireTokenPopup()` dan `acquireTokenRedirect()` een van beide of op dezelfde resource gebruiken. De gebruiker wordt gedwongen om een multi-factor authenticatie te doen. Nadat de gebruiker de multi-factor-verificatie heeft voltooid, krijgt de app een nieuw toegangstoken voor de gevraagde bron.

Om dit scenario uit te proberen, zie onze [JS SPA Namens code sample](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Deze codesteekproef maakt gebruik van het beleid voor voorwaardelijke toegang en de web-API die u eerder hebt geregistreerd bij een JS SPA om dit scenario aan te tonen. Het laat zien hoe je de claimuitdaging goed afhandelen en een toegangstoken krijgen dat kan worden gebruikt voor je Web API. U ook het algemene [Voorbeeld van de Angular.js-code](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) afrekenen voor begeleiding op een Hoekige SPA

## <a name="see-also"></a>Zie ook

* Zie [Voorwaardelijke toegang in Azure Active Directory](../active-directory-conditional-access-azure-portal.md)voor meer informatie over de mogelijkheden.
* Zie [GitHub repo van codevoorbeelden](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory)voor meer Azure AD-codevoorbeelden.
* Zie [bibliotheekhandleiding](active-directory-authentication-libraries.md)voor meer informatie over de ADAL SDK's en toegang tot de referentiedocumentatie.
* Zie Gebruikers aanmelden met het [patroon meerdere tenant's](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor meer informatie over scenario's met meerdere tenant.
