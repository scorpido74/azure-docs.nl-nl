---
title: '& cachetokens verkrijgen met MSAL | Azure'
titleSuffix: Microsoft identity platform
description: Meer informatie over het werven en caching-tokens met behulp van de Microsoft Authentication Library (MSAL).
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
ms.openlocfilehash: 647dff9e6401322371ef795a25ca5ced2b517e9c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534581"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Tokens aanschaffen en cachen met behulp van de Microsoft-verificatiebibliotheek (MSAL)

[Met toegangstokens](access-tokens.md) kunnen clients veilig web-API's oproepen die door Azure worden beschermd. Er zijn veel manieren om een token aan te schaffen met Behulp van Microsoft Authentication Library (MSAL). Op sommige manieren zijn gebruikersinteracties via een webbrowser vereist. Sommige vereisen geen gebruikersinteracties. In het algemeen is de manier om een token te verkrijgen afhankelijk van of de toepassing een openbare clienttoepassing (desktop of mobiele app) of een vertrouwelijke clienttoepassing is (web-app, web-API of daemon-toepassing zoals een Windows-service).

MSAL caches een token nadat het is verworven.  Toepassingscode moet proberen om een token in stilte te krijgen (uit de cache), eerst, voordat u een token op een andere manier verwerft.

U ook de tokencache wissen, wat wordt bereikt door de accounts uit de cache te verwijderen. Deze browser wordt echter niet verwijderd door dat de cookie van de browser wordt gebruikt.

## <a name="scopes-when-acquiring-tokens"></a>Scopes bij het verkrijgen van tokens

[Scopes](v2-permissions-and-consent.md) zijn de machtigingen die een web-API blootstelt voor clienttoepassingen om toegang tot aan te vragen. Clienttoepassingen vragen de toestemming van de gebruiker voor deze scopes bij het indienen van verificatieverzoeken om tokens toegang te krijgen tot de web-API's. Met MSAL u tokens toegang geven tot Azure AD voor ontwikkelaars (v1.0) en Microsoft identity platform (v2.0) API's. v2.0-protocol gebruikt scopes in plaats van resource in de aanvragen. Voor meer informatie, lees [v1.0 en v2.0 vergelijking](active-directory-v2-compare.md). Op basis van de configuratie van de web-API van de tokenversie die het accepteert, retourneert het v2.0-eindpunt het toegangstoken naar MSAL.

Een aantal MSAL-methoden voor het verwerven van token vereisen een *scopeparameter.* Deze parameter is een eenvoudige lijst met tekenreeksen die de gewenste machtigingen en resources declareren die worden aangevraagd. Bekende scopes zijn de [Microsoft Graph-machtigingen.](/graph/permissions-reference)

Het is ook mogelijk in MSAL om toegang te krijgen tot v1.0-bronnen. Lees [scopes voor een v1.0-toepassing voor](msal-v1-app-scopes.md)meer informatie.

### <a name="request-specific-scopes-for-a-web-api"></a>Specifieke scopes aanvragen voor een web-API

Wanneer uw toepassing tokens met specifieke machtigingen voor een bron-API moet aanvragen, moet u de scopes passeren die de app-ID URI van de API in de onderstaande indeling bevatten: * &lt;app ID&gt;/&lt;URI-scope&gt;*

Bijvoorbeeld scopes voor Microsoft Graph API:`https://graph.microsoft.com/User.Read`

Of, bijvoorbeeld, scopes voor een aangepaste web-API:`api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

Alleen voor de Microsoft Graph API `user.read` wordt `https://graph.microsoft.com/User.Read` een scopewaarde opgemaakt en kan deze door elkaar worden gebruikt.

> [!NOTE]
> Bepaalde web-API's zoals Azurehttps://management.core.windows.net/) Resource Manager API (verwacht een trailing '/' in de doelgroepclaim (aud) van het toegangstoken. In dit geval is het belangrijk https://management.core.windows.net//user_impersonation om het bereik als (let op de dubbele slash), voor het token geldig te zijn in de API.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Dynamische scopes aanvragen voor incrementele toestemming

Bij het bouwen van applicaties met behulp van v1.0, moest je de volledige set van machtigingen (statische scopes) vereist door de aanvraag voor de gebruiker om toestemming te geven op het moment van inloggen registreren. In v2.0 u indien nodig aanvullende machtigingen aanvragen met behulp van de scopeparameter. Deze worden dynamische scopes genoemd en stellen de gebruiker in staat om incrementele toestemming te geven voor scopes.

U zich bijvoorbeeld in eerste instantie aanmelden bij de gebruiker en deze elke vorm van toegang ontzeggen. Later u hen de mogelijkheid geven om de agenda van de gebruiker te lezen door het agendabereik in de methoden voor het verkrijgen van token aan te vragen en de toestemming van de gebruiker te verkrijgen.

Bijvoorbeeld: `https://graph.microsoft.com/User.Read` en`https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Tokens in stilte verkrijgen (uit de cache)

MSAL onderhoudt een tokencache (of twee caches voor vertrouwelijke clienttoepassingen) en caches een token nadat het is aangeschaft.  In veel gevallen krijgt een poging om in stilte een token te krijgen een ander token met meer scopes op basis van een token in de cache. Het is ook in staat om een token te vernieuwen wanneer het bijna verloopt (omdat de tokencache ook een vernieuwingstoken bevat).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Aanbevolen oproeppatroon voor openbare clienttoepassingen

Toepassingscode moet proberen om een token in stilte te krijgen (uit de cache), eerst.  Als de methodeaanroep een fout of uitzondering op 'UI vereist' retourneert, probeert u een token op een andere manier te verkrijgen.

Er zijn echter twee stromen waarvoor u **niet moet** proberen om in stilte een token te verwerven:

- [clientreferenties stromen,](msal-authentication-flows.md#client-credentials)die geen gebruik maakt van de cache van het gebruikerstoken, maar een toepassingstokencache. Deze methode zorgt ervoor dat deze tokencache van toepassingen wordt geverifieerd voordat u een aanvraag naar de STS verzendt.
- [autorisatiecodestroom](msal-authentication-flows.md#authorization-code) in web-apps, omdat deze een code inwisselt die de toepassing heeft gekregen door de gebruiker aan te melden en deze toestemming te geven voor meer scopes. Aangezien een code wordt doorgegeven als een parameter en niet als een account, kan de methode niet in de cache kijken voordat de code wordt ingewisseld, waarvoor in ieder geval een aanroep naar de service vereist is.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Aanbevolen oproeppatroon in web-apps met de machtigingscodestroom

Voor webtoepassingen die de [machtigingscodestroom van OpenID Connect](v2-protocols-oidc.md)gebruiken, is het aanbevolen patroon in de controllers:

- Instantiate een vertrouwelijke client applicatie met een token cache met aangepaste serialisatie.
- Het token verkrijgen met behulp van de autorisatiecodestroom

## <a name="acquiring-tokens"></a>Tokens verwerven

Over het algemeen is de methode voor het verkrijgen van een token afhankelijk van of het een openbare client of vertrouwelijke clienttoepassing is.

### <a name="public-client-applications"></a>Openbare clienttoepassingen

Voor openbare clienttoepassingen (desktop- of mobiele app) gaat u als:
- Vaak verwerven tokens interactief, met de gebruiker aanmelden via een ui of pop-up venster.
- Kan een token in stilte voor de aangemelde gebruiker krijgen met geïntegreerde Windows-verificatie (IWA/Kerberos) als de bureaubladtoepassing wordt uitgevoerd op een [Windows-computer](msal-authentication-flows.md#integrated-windows-authentication) die is verbonden met een domein of azure.
- Kan [een token met een gebruikersnaam en wachtwoord krijgen](msal-authentication-flows.md#usernamepassword) in .NET framework desktop client toepassingen, maar dit wordt niet aanbevolen. Gebruik geen gebruikersnaam/wachtwoord in vertrouwelijke clienttoepassingen.
- Kan een token verkrijgen via de [apparaatcodestroom](msal-authentication-flows.md#device-code) in toepassingen die worden uitgevoerd op apparaten die geen webbrowser hebben. De gebruiker krijgt een URL en een code, die vervolgens naar een webbrowser op een ander apparaat gaat en de code invoert en zich aanmeldt.  Azure AD stuurt vervolgens een token terug naar het browserloze apparaat.

### <a name="confidential-client-applications"></a>Vertrouwelijke clienttoepassingen

Voor vertrouwelijke clienttoepassingen (web-app, web-API of daemon-toepassing zoals een Windows-service) gaat u als volgt te werk:
- Tokens verkrijgen **voor de toepassing zelf** en niet voor een gebruiker, met behulp van de [clientreferenties stroom](msal-authentication-flows.md#client-credentials). Dit kan worden gebruikt voor het synchroniseren van tools, of tools die gebruikers in het algemeen verwerken en niet een specifieke gebruiker.
- Gebruik de [on-behalf-of-flow](msal-authentication-flows.md#on-behalf-of) voor een web-API om namens de gebruiker een API aan te roepen. De toepassing wordt geïdentificeerd met clientreferenties om een token te verkrijgen op basis van een gebruikersbewering (SAML bijvoorbeeld, of een JWT-token). Deze stroom wordt gebruikt door toepassingen die toegang moeten krijgen tot bronnen van een bepaalde gebruiker in service-to-service-aan-huisoproepen.
- Tokens verkrijgen met behulp van de [autorisatiecodestroom](msal-authentication-flows.md#authorization-code) in web-apps nadat de gebruiker zich heeft aanmeldt via de URL van het autorisatieverzoek. OpenID Connect-toepassing maakt doorgaans gebruik van dit mechanisme, waarmee de gebruiker zich kan aanmelden met Open ID-verbinding en vervolgens namens de gebruiker toegang heeft tot web-API's.

## <a name="authentication-results"></a>Verificatieresultaten

Wanneer uw client een toegangstoken aanvraagt, retourneert Azure AD ook een verificatieresultaat met enkele metagegevens over het toegangstoken. Deze informatie bevat de vervaldatum van het toegangstoken en de scopes waarvoor het geldig is. Met deze gegevens kan uw app intelligente caching van toegangstokens doen zonder het toegangstoken zelf te hoeven ontken.  Het verificatieresultaat legt bloot:

- Het [toegangstoken](access-tokens.md) voor de web-API om toegang te krijgen tot bronnen. Dit is een tekenreeks, meestal een base64 gecodeerde JWT, maar de client mag nooit in het toegangstoken kijken. Het formaat is niet gegarandeerd stabiel te blijven en het kan worden versleuteld voor de bron. Mensen die code schrijven afhankelijk van de inhoud van toegangstoken op de client is een van de grootste bronnen van fouten en fouten in de clientlogica.
- Het [ID-token](id-tokens.md) voor de gebruiker (dit is een JWT).
- Het token verloop, die de datum/tijd aangeeft wanneer het token verloopt.
- De tenant-id bevat de tenant waarin de gebruiker is gevonden. Voor gastgebruikers (Azure AD B2B-scenario's) is de tenant-id de gasttenant, niet de unieke tenant. Wanneer het token op naam van een gebruiker wordt geleverd, bevat het verificatieresultaat ook informatie over deze gebruiker. Voor vertrouwelijke clientstromen waarbij tokens worden aangevraagd zonder gebruiker (voor de toepassing), is deze gebruikersinformatie null.
- De scopes waarvoor het token is uitgegeven.
- De unieke ID voor de gebruiker.

## <a name="next-steps"></a>Volgende stappen

Als u MSAL voor Java gebruikt, leest u meer over [custom tokencache serialisatie in MSAL for Java.](msal-java-token-cache-serialization.md)

Meer informatie over [het afhandelen van fouten en uitzonderingen](msal-handling-exceptions.md).
