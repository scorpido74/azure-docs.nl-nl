---
title: '&-Cache-tokens verkrijgen met MSAL | Azure'
titleSuffix: Microsoft identity platform
description: Meer informatie over het verkrijgen en caching van tokens met behulp van de micro soft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c1f1cbf85b96aade745cc4248aed4bc89e41b450
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085167"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Tokens verkrijgen en in de cache opslaan met behulp van de micro soft Authentication Library (MSAL)

Met [toegangs tokens](access-tokens.md) kunnen clients veilig Web-api's aanroepen die worden beveiligd door Azure. Er zijn veel manieren om een token te verkrijgen met behulp van micro soft Authentication Library (MSAL). Voor sommige manieren is interactie van de gebruiker vereist via een webbrowser. Voor sommige gebruikers interacties is geen interactie van de gebruiker vereist. Over het algemeen is het mogelijk om een token te verkrijgen, afhankelijk van of de toepassing een open bare-client toepassing (desktop-of mobiele app) of een vertrouwelijke client toepassing (Web-app, Web-API of daemon-toepassing, zoals een Windows-service) is.

MSAL slaat een token op in de cache nadat het is verkregen.  Toepassings code moet eerst proberen een token op de achtergrond (uit de cache) te krijgen voordat een token op een andere manier wordt verkregen.

U kunt ook de token cache wissen. dit wordt bereikt door de accounts uit de cache te verwijderen. Hiermee wordt de sessie cookie die zich in de browser bevindt, echter niet verwijderd.

## <a name="scopes-when-acquiring-tokens"></a>Bereiken bij het ophalen van tokens

[Scopes](v2-permissions-and-consent.md) zijn de machtigingen die een web-API beschikbaar maakt voor client toepassingen om toegang aan te vragen. Client toepassingen vragen de toestemming van de gebruiker voor deze bereiken bij het maken van verificatie aanvragen voor het verkrijgen van tokens voor toegang tot de Web-Api's. Met MSAL kunt u tokens ophalen om toegang te krijgen tot Azure AD voor ontwikkel aars (v 1.0) en micro soft Identity platform (v 2.0) Api's. v 2.0-protocol gebruikt scopes in plaats van resource in de aanvragen. Lees de [vergelijking van v 1.0 en v 2.0](active-directory-v2-compare.md)voor meer informatie. Op basis van de configuratie van de token die door de Web-API wordt geaccepteerd, retourneert het v 2.0-eind punt het toegangs token voor MSAL.

Voor een aantal MSAL-Acquire-token methoden is een *bereik* parameter vereist. Deze para meter is een eenvoudige lijst met teken reeksen die de gewenste machtigingen en benodigde bronnen declareren. Bekende bereiken zijn de [Microsoft Graph machtigingen](/graph/permissions-reference).

Het is ook mogelijk in MSAL om toegang te krijgen tot v 1.0-resources. Lees voor meer informatie [bereiken voor een v 1.0-toepassing](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Specifieke bereiken voor een web-API aanvragen

Als uw toepassing tokens met specifieke machtigingen voor een resource-API moet aanvragen, moet u de scopes met de App-ID-URI van de API door geven in de onderstaande notatie: *&lt;App-ID-uri&gt;/&lt;bereik&gt;*

Bijvoorbeeld bereiken voor Microsoft Graph-API: `https://graph.microsoft.com/User.Read`

Of bijvoorbeeld bereiken voor een aangepaste web-API: `api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

Voor de Microsoft Graph-API wordt alleen een bereik waarde `user.read` toegewezen aan `https://graph.microsoft.com/User.Read` indeling en kan door elkaar worden gebruikt.

> [!NOTE]
> Bepaalde web-Api's, zoals Azure Resource Manager-API (https://management.core.windows.net/) verwachten een navolgende '/' in de claim van de doel groep (AUD) van het toegangs token. In dit geval is het belang rijk om het bereik door te geven als https://management.core.windows.net//user_impersonation (Let op de dubbele slash), zodat het token geldig is in de API.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Dynamische bereiken aanvragen voor incrementele toestemming

Wanneer u toepassingen bouwt met behulp van v 1.0, moest u de volledige set machtigingen (statische bereiken) registreren die de gebruiker nodig heeft om toestemming te geven op het moment van de aanmelding. In v 2.0 kunt u indien nodig aanvullende machtigingen aanvragen met de para meter bereik. Deze worden dynamische bereiken genoemd en bieden de gebruiker de mogelijkheid om incrementele toestemming te bieden voor scopes.

U kunt de gebruiker bijvoorbeeld eerst aanmelden en de toegang weigeren. Later kunt u hen de mogelijkheid geven om de agenda van de gebruiker te lezen door het kalender bereik aan te vragen bij de methoden voor het verkrijgen van een token en de toestemming van de gebruiker te verkrijgen.

Bijvoorbeeld: `https://graph.microsoft.com/User.Read` en `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Tokens op de achtergrond verkrijgen (vanuit de cache)

MSAL onderhoudt een token cache (of twee caches voor vertrouwelijke client toepassingen) en slaat een token op in de cache nadat het is verkregen.  In veel gevallen probeert het op de achtergrond ophalen van een token een andere token te verkrijgen met meer scopes op basis van een token in de cache. Het is ook mogelijk om een token te vernieuwen wanneer het bijna is verlopen (omdat de token cache ook een vernieuwings token bevat).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Aanbevolen aanroep patroon voor open bare client toepassingen

De toepassings code moet eerst proberen een token op de achtergrond te verkrijgen (vanuit de cache).  Als de aanroep van de methode de fout of uitzonde ring ' gebruikers interface vereist ' retourneert, kunt u een token op een andere manier ophalen. 

Er zijn echter twee stromen waarvoor u **niet moet** proberen om een token op de achtergrond te verkrijgen:

- [client referenties flow](msal-authentication-flows.md#client-credentials), die geen gebruik maakt van de token cache van de gebruiker, maar een toepassings token cache. Deze methode zorgt ervoor dat deze toepassings token cache wordt gecontroleerd voordat een aanvraag naar de STS wordt verzonden.
- de [autorisatie code stroom](msal-authentication-flows.md#authorization-code) in web apps, omdat er een code wordt ingewisseld die de toepassing heeft ontvangen door zich aan te melden bij de gebruiker en die toestemming heeft voor meer bereiken. Omdat een code wordt door gegeven als een para meter en geen account, kan de methode niet in de cache zoeken voordat de code wordt ingewisseld, waardoor er nog een aanroep van de service nodig is.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Aanbevolen aanroep patroon in Web Apps met behulp van de autorisatie code stroom

Voor webtoepassingen die gebruikmaken van de [OpenID Connect Connect-autorisatie code stroom](v2-protocols-oidc.md), is het aanbevolen patroon in de controllers:

- Een vertrouwelijke client toepassing instantiëren met een token cache met aangepaste serialisatie. 
- Het token verkrijgen met de autorisatie code stroom

## <a name="acquiring-tokens"></a>Tokens ophalen

Over het algemeen is de methode voor het verkrijgen van een token afhankelijk van of het een open bare client of een vertrouwelijke client toepassing is.

### <a name="public-client-applications"></a>Open bare client toepassingen

Voor open bare client toepassingen (desktop of mobiele app) gaat u als volgt te werk:
- Vaak tokens interactief verkrijgen, waarbij de gebruiker zich aanmeldt via een gebruikers interface of pop-upvenster.
- Kan [een token op de achtergrond ontvangen voor de aangemelde gebruiker](msal-authentication-flows.md#integrated-windows-authentication) die gebruikmaakt van geïntegreerde Windows-authenticatie (IWA/Kerberos) als de bureaublad toepassing wordt uitgevoerd op een Windows-computer die is toegevoegd aan een domein of aan Azure.
- Kan [een token verkrijgen met een gebruikers naam en wacht woord](msal-authentication-flows.md#usernamepassword) in .NET Framework desktop client-toepassingen, maar dit wordt niet aanbevolen. Gebruik geen gebruikers naam/wacht woord in vertrouwelijke client toepassingen.
- Kan een token verkrijgen via de [code stroom](msal-authentication-flows.md#device-code) van het apparaat in toepassingen die worden uitgevoerd op apparaten die geen webbrowser hebben. De gebruiker wordt voorzien van een URL en een code, die vervolgens naar een webbrowser op een ander apparaat gaat en de code invoert en zich aanmeldt.  Azure AD stuurt vervolgens een token terug naar het browser-less-apparaat.

### <a name="confidential-client-applications"></a>Vertrouwelijke client toepassingen

Voor vertrouwelijke client toepassingen (Web-app, Web-API of daemon-toepassing, zoals een Windows-service), gaat u als volgt te werk:
- Tokens ophalen **voor de toepassing zelf** en niet voor een gebruiker, met behulp van de [client referenties stroom](msal-authentication-flows.md#client-credentials). Dit kan worden gebruikt voor het synchroniseren van hulpprogram ma's of hulpprogram ma's die gebruikers in het algemeen en niet een specifieke gebruiker verwerken. 
- Gebruik de [namens-stroom](msal-authentication-flows.md#on-behalf-of) voor een web-API om een API namens de gebruiker aan te roepen. De toepassing wordt aangeduid met client referenties om een token te verkrijgen op basis van een gebruikers bevestiging (SAML-voor beeld of een JWT-token). Deze stroom wordt gebruikt door toepassingen die toegang moeten hebben tot bronnen van een bepaalde gebruiker in service-to-service-aanroepen.
- Tokens verkrijgen met de [autorisatie code stroom](msal-authentication-flows.md#authorization-code) in web apps nadat de gebruiker zich heeft aangemeld via de URL van de verificatie aanvraag. OpenID Connect Connect Application gebruikt dit mechanisme doorgaans, waarmee de gebruiker zich kan aanmelden met open ID Connect en vervolgens toegang tot Web-Api's namens de gebruiker kan krijgen.

## <a name="authentication-results"></a>Verificatie resultaten

Als uw client een toegangs token aanvraagt, retourneert Azure AD ook een verificatie resultaat dat enkele meta gegevens bevat over het toegangs token. Deze informatie omvat de verloop tijd van het toegangs token en de scopes waarvoor deze geldig is. Met deze gegevens kan uw app intelligente caching van toegangs tokens uitvoeren zonder dat het toegangs token zelf moet worden geparseerd.  Het verificatie resultaat is beschikbaar:

- Het [toegangs token](access-tokens.md) voor de Web-API voor toegang tot bronnen. Dit is een teken reeks, meestal een met base64 gecodeerde JWT, maar de client mag nooit in het toegangs token kijken. De indeling is niet gegarandeerd stabiel en kan worden versleuteld voor de resource. Personen die code schrijven, afhankelijk van de inhoud van het toegangs token op de client, is een van de grootste bronnen van fouten en client logica-onderbrekingen.
- Het [id-token](id-tokens.md) voor de gebruiker (dit is een JWT).
- Het verval van het token, waarmee de datum/tijd wordt aangegeven waarop het token verloopt.
- De Tenant-ID bevat de Tenant waarin de gebruiker is gevonden. Voor gast gebruikers (Azure AD B2B-scenario's) is de Tenant-ID de gast-Tenant, niet de unieke Tenant. Wanneer het token wordt geleverd met de naam van een gebruiker, bevat het verificatie resultaat ook informatie over deze gebruiker. Voor vertrouwelijke client stromen waarbij tokens worden aangevraagd zonder gebruiker (voor de toepassing), is deze gebruikers informatie null.
- De bereiken waarvoor het token is uitgegeven.
- De unieke ID voor de gebruiker.

## <a name="next-steps"></a>Volgende stappen

Als u MSAL voor Java gebruikt, kunt u meer informatie vinden over [aangepaste serialisatie van de token cache in MSAL voor Java](msal-java-token-cache-serialization.md).

Meer informatie over het [afhandelen van fouten en uitzonde ringen](msal-handling-exceptions.md).
