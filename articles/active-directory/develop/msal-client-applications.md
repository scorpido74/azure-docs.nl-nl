---
title: Openbare en vertrouwelijke clientapps (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over openbare client- en vertrouwelijke clienttoepassingen in de Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 9c3292a31e5f750c16933acf94509e0ad226080a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534309"
---
# <a name="public-client-and-confidential-client-applications"></a>Openbare client- en vertrouwelijke clienttoepassingen
Microsoft Authentication Library (MSAL) definieert twee soorten clients: openbare clients en vertrouwelijke clients. De twee clienttypen onderscheiden zich door hun vermogen om veilig te verifiëren met de autorisatieserver en de vertrouwelijkheid van hun clientreferenties te behouden. Azure AD Authentication Library (ADAL) gebruikt daarentegen de zogenaamde *verificatiecontext* (een verbinding met Azure AD).

- **Vertrouwelijke clienttoepassingen** zijn apps die worden uitgevoerd op servers (web-apps, web-API-apps of zelfs service/daemon-apps). Ze worden beschouwd als moeilijk toegankelijk, en om die reden in staat om een aanvraag geheim te houden. Vertrouwelijke clients kunnen configuratie-tijdgeheimen bevatten. Elk exemplaar van de client heeft een aparte configuratie (inclusief client-ID en clientgeheim). Deze waarden zijn moeilijk te extraheren voor eindgebruikers. Een web-app is de meest voorkomende vertrouwelijke client. De client-ID wordt blootgesteld via de webbrowser, maar het geheim wordt alleen doorgegeven in het achterkanaal en nooit direct blootgesteld.

    Vertrouwelijke client-apps: <BR>
    ![Web app](media/msal-client-applications/web-app.png) ![Web API](media/msal-client-applications/web-api.png) ![Daemon/service](media/msal-client-applications/daemon-service.png)

- **Openbare clienttoepassingen** zijn apps die worden uitgevoerd op apparaten of desktopcomputers of in een webbrowser. Ze worden niet vertrouwd om veilig toepassingsgeheimen te bewaren, dus ze hebben alleen toegang tot web-API's namens de gebruiker. (Ze ondersteunen alleen openbare clientstromen.) Openbare clients kunnen geen geheimen voor configuratie-tijd hebben, zodat ze geen klantgeheimen hebben.

    Openbare client-apps: <BR>
    ![](media/msal-client-applications/desktop-app.png) ![Desktop-app Browserless API](media/msal-client-applications/browserless-app.png) ![Mobile-app](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> In MSAL.js is er geen scheiding van openbare en vertrouwelijke clientapps.  MSAL.js vertegenwoordigt client-apps als user agent-gebaseerde apps, openbare clients waarin de clientcode wordt uitgevoerd in een user agent zoals een webbrowser. Deze clients slaan geen geheimen op omdat de browsercontext openlijk toegankelijk is.

## <a name="comparing-the-client-types"></a>De clienttypen vergelijken
Hier volgen enkele overeenkomsten en verschillen tussen openbare client- en vertrouwelijke client-apps:

- Beide soorten apps onderhouden een gebruikerstokencache en kunnen een token in stilte verkrijgen (wanneer het token al in de tokencache staat). Vertrouwelijke client-apps hebben ook een app-tokencache voor tokens die voor de app zelf zijn.
- Beide typen apps beheren gebruikersaccounts en kunnen een account ophalen uit de cache van gebruikerstoken, een account ophalen via de id of een account verwijderen.
- Openbare client-apps hebben vier manieren om een token te verkrijgen (vier verificatiestromen). Vertrouwelijke client-apps hebben drie manieren om een token te verkrijgen (en één manier om de URL van het eindpunt van de identiteitsprovider te berekenen). Zie [Tokens werven](msal-acquire-cache-tokens.md)voor meer informatie.

Als u ADAL hebt gebruikt, u merken dat in de context van ADAL de client-ID (ook wel de *toepassings-id* of *app-id*genoemd) eenmaal wordt doorgegeven bij de constructie van de toepassing. Het hoeft niet opnieuw te worden doorgegeven wanneer de app een token verwerft. Dit geldt voor zowel openbare als vertrouwelijke clientapps. Constructors van vertrouwelijke client apps worden ook doorgegeven client referenties: het geheim dat ze delen met de identiteit provider.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over:
- [Configuratieopties voor clienttoepassingen](msal-client-application-configuration.md)
- [Clienttoepassingen instantiëring met behulp van MSAL.NET](msal-net-initializing-client-applications.md)
- [Clienttoepassingen instantiëring met MSAL.js](msal-js-initializing-client-applications.md)
