---
title: Nieuwe ervaring voor app-registratie in de Azure-portal
titleSuffix: Microsoft identity platform
description: Een inleiding tot de nieuwe app-registratie ervaring in de Azure Portal
services: active-directory
author: archieag
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: aragra
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 835924c92d0c5768c1125b3b1d26ee72cf2cec70
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698268"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>De nieuwe app registratie-ervaring voor Azure Portal

Er zijn veel verbeteringen aangebracht in de nieuwe [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring in de Azure Portal. Als u bekend bent met de apps.dev.microsoft.com-ervaring (Application Registration Portal) voor het registreren of beheren van geconvergeerde toepassingen, krijgt u in deze trainings gids de mogelijkheid om aan de slag te gaan met de nieuwe ervaring.

## <a name="whats-not-changing"></a>Wat wordt er niet gewijzigd?

- Uw toepassingen en gerelateerde configuraties kunnen worden gevonden in de nieuwe ervaring. U hoeft de toepassingen niet opnieuw te registreren, en gebruikers van uw toepassingen hoeven zich niet opnieuw aan te melden.

    > [!NOTE]
    > U moet zich aanmelden met het account dat u hebt gebruikt om toepassingen te registreren om ze te vinden in de Azure Portal. We raden u aan de aangemelde gebruiker te controleren in de Azure Portal overeenkomt met de gebruiker die is aangemeld bij de portal voor toepassings registratie door het e-mail adres te vergelijken met uw profiel.
    > 
    > In sommige gevallen, met name wanneer u zich aanmeldt met persoonlijke micro soft-accounts (bijvoorbeeld Outlook, Live, Xbox, enzovoort) met een Azure AD-e-mail adres, hebben we ontdekt dat wanneer u naar de Azure Portal van de oude ervaring gaat, u zich aanmeldt bij een ander account met dezelfde e-mail in uw Azure AD-Tenant. Als u nog steeds denkt dat uw toepassingen ontbreken, meldt u zich af en meldt u zich aan met het juiste account.

- Live SDK-apps die zijn gemaakt met persoonlijke micro soft-accounts worden nog niet ondersteund in de Azure Portal en blijven in de nabije toekomst in de oude ervaring.

## <a name="key-changes"></a>Sleutel wijzigingen

-   In de oude ervaring waren apps standaard geregistreerd als geconvergeerde apps die alle organisatie accounts (multi tenant) ondersteunen, evenals persoonlijke micro soft-accounts. Dit kan niet worden gewijzigd met de oude ervaring, waardoor het lastig is om apps te maken die alleen ondersteuning bieden voor organisatie accounts (multi tenant of één Tenant).
    Met de nieuwe ervaring kunt u apps registreren die al deze opties ondersteunen. Meer [informatie over app-typen](active-directory-v2-registration-portal.md).

-   Als uw persoonlijke Microsoft-account zich ook in een Azure AD-Tenant bevindt, ziet u in de nieuwe ervaring drie tabbladen: alle toepassingen in de Tenant, toepassingen in eigendom in de Tenant en toepassingen uit uw persoonlijke account. Als u denkt dat apps die zijn geregistreerd bij uw persoonlijke Microsoft-account ontbreken, controleert u het tabblad **toepassingen op uw persoonlijke account** .

-   In de nieuwe ervaring kunt u eenvoudig scha kelen tussen tenants door te navigeren naar uw profiel en Directory switch te kiezen.

## <a name="list-of-applications"></a>Lijst met toepassingen

-   In de nieuwe app-lijst worden de toepassingen weer gegeven die zijn geregistreerd via de ervaring voor de verouderde app-registraties in de Azure Portal (apps die alleen worden aangemeld bij Azure AD-accounts), evenals de apps die zijn geregistreerd bij de [Portal voor toepassings registratie](https://apps.dev.microsoft.com/) (apps die zich aanmelden bij zowel Azure AD als persoonlijke micro soft-accounts).

-   De lijst met nieuwe apps bevat twee extra kolommen: **gemaakt op** kolom en **Certificaten & geheimen** kolom waarin de status wordt weer gegeven (actueel, verloopt binnenkort of verlopen) van referenties die zijn geregistreerd voor de app.

## <a name="new-app-registration"></a>Nieuwe app-registratie

In de oude ervaring moest u een geconvergeerde app registreren om een naam op te geven. De apps die zijn gemaakt, zijn geregistreerd als geconvergeerde apps die alle organisatorische mappen (multi tenant) ondersteunen, evenals persoonlijke micro soft-accounts.  Dit kan niet worden gewijzigd met de oude ervaring, waardoor het lastig is om apps te maken die alleen ondersteuning bieden voor organisatie accounts (multi tenant of één Tenant). [Meer informatie over ondersteunde account typen](v2-supported-account-types.md)

In de nieuwe ervaring moet u een naam opgeven voor de app en de ondersteunde account typen kiezen. U kunt desgewenst een omleidings-URI opgeven.
Als u een omleidings-URI opgeeft, moet u opgeven of deze Web/public (systeem eigen/mobiel en bureau blad) is. Zie [deze Snelstartgids](quickstart-register-app.md)voor meer informatie over het registreren van een app met behulp van de nieuwe app-registraties.

## <a name="app-management-page"></a>Pagina app-beheer

De oude ervaring had één app-beheer pagina voor geconvergeerde apps met de volgende secties: eigenschappen, toepassings geheimen, platforms, eigen aren, Microsoft Graph machtigingen, profiel en geavanceerde opties.

De nieuwe ervaring in het Azure Portal vertegenwoordigt deze functies in afzonderlijke pagina's. Hier kunt u de equivalente functionaliteit vinden:

-   Eigenschappen: naam en toepassings-ID bevindt zich op de pagina overzicht.

-   Toepassings geheimen bevindt zich op de pagina certificaten & geheimen

-   De configuratie van platforms bevindt zich op de verificatie pagina

-   Microsoft Graph machtigingen vindt u op de pagina API-machtigingen samen met andere machtigingen

-   Profiel is op huisstijl pagina

-   Geavanceerde optie: ondersteuning voor Live SDK vindt u op de pagina verificatie.

## <a name="application-secretscertificates--secrets"></a>Toepassings geheimen/certificaten & geheimen

In de nieuwe ervaring zijn de namen van de **toepassings geheimen** gewijzigd in **Certificaten & geheimen**. Daarnaast worden **open bare sleutels** aangeduid als **certificaten** en **wacht woorden** worden aangeduid als **client geheimen**. We hebben ervoor gekozen deze functionaliteit niet meer in de nieuwe ervaring te plaatsen, dus u kunt geen nieuw sleutel paar meer genereren.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Platformen/authenticatie: antwoord-Url's/omleidings-Uri's
In de oude ervaring bevat een app platformen voor web, native en Web API voor het configureren van omleidings-Url's, afmeldings-URL en impliciete stroom.

In de nieuwe ervaring zijn antwoord-Url's te vinden in de sectie app-\'s-verificatie. Daarnaast worden ze omleidings-Uri's genoemd en is de indeling voor omleidings-Uri's gewijzigd. Ze moeten worden gekoppeld aan een app-type (Web of open bare client-mobiel en desktop). [Meer informatie](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

Web-Api's worden geconfigureerd in een API-pagina beschikbaar maken.

> [!NOTE] 
> Probeer de nieuwe ervaring voor verificatie-instellingen uit, waar u instellingen voor uw toepassing kunt configureren op basis van het platform of apparaat dat u wilt instellen. [Meer informatie](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Microsoft Graph machtigingen/API-machtigingen

-   Wanneer u een API in de oude ervaring selecteert, kunt u kiezen uit Microsoft Graph-Api's. In de nieuwe ervaring kunt u kiezen uit veel Api's van micro soft, waaronder Microsoft Graph, Api's van uw organisatie en uw Api's. dit wordt weer gegeven op drie tabbladen: micro soft Api's, Api's mijn organisatie gebruikt of mijn Api's. De zoek balk op Api's mijn organisatie gebruikt tabbladen zoeken via service-principals in de Tenant.
    
    > [!NOTE] 
    > Dit tabblad wordt niet weer geven als uw toepassing niet is gekoppeld aan een Tenant. Zie [deze Quick](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md)start voor meer informatie over het aanvragen van machtigingen met de nieuwe ervaring.

-   De oude ervaring heeft geen knop **machtigingen verlenen** . In de nieuwe ervaring is er een toekennings toestemmings sectie met de knop **toestemming van beheerder verlenen** aan de sectie API-machtigingen van een app. Alleen een beheerder kan toestemming geven en deze knop is alleen voor beheerders ingeschakeld. Wanneer een beheerder de knop **toestemming van beheerder verlenen** selecteert, wordt toestemming van de beheerder verleend aan alle aangevraagde machtigingen.

## <a name="profile"></a>Profiel
In de oude ervaring heeft het profiel het logo, de URL van de start pagina, de service-URL en de URL-configuratie voor de privacyverklaring. In de nieuwe ervaring vindt u deze op de pagina huis stijl.

## <a name="application-manifest"></a>Manifest van de toepassing
In de nieuwe ervaring kunt u met manifest pagina de kenmerken van de app bewerken en bijwerken. Zie het [toepassings manifest](reference-app-manifest.md)voor meer informatie.

## <a name="new-ui"></a>Nieuwe gebruikers interface
Er is een nieuwe gebruikers interface voor eigenschappen die voorheen alleen kunnen worden ingesteld met behulp van de manifest editor of de API, of die niet bestaat.

-   Impliciete toekennings stroom (oauth2AllowImplicitFlow) vindt u op de verificatie pagina. In tegens telling tot de oude ervaring kunt u toegangs tokens of ID-tokens inschakelen, of beide.

-   Bereiken die zijn gedefinieerd door deze API (oauth2Permissions) en geautoriseerde client toepassingen (preAuthorizedApplications) kunnen worden geconfigureerd via de pagina een API beschikbaar maken. Zie [deze Quick](quickstart-configure-app-expose-web-apis.md)start voor meer informatie over het configureren van een app om een web-API te zijn en machtigingen/bereiken beschikbaar te maken.

-   Het Publisher-domein (dat wordt weer gegeven voor gebruikers op de [\'toestemming](application-consent-experience.md)om de aanvraag uit te geven), vindt u op de pagina huisstijl Blade. Zie voor meer informatie over het configureren van een uitgever domein [deze instructies](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Beperkingen

De nieuwe ervaring heeft de volgende beperkingen:

-   De nieuwe ervaring biedt nog geen ondersteuning voor App-registraties voor Azure AD B2C-tenants.

-   De nieuwe ervaring biedt nog geen ondersteuning voor Live SDK-apps die zijn gemaakt met persoonlijke micro soft-accounts.

-   Het wijzigen van de waarde voor ondersteunde accounts wordt niet ondersteund in de gebruikers interface. U moet het app-manifest gebruiken tenzij u\'wisselen tussen Azure AD en meerdere tenants.

   > [!NOTE]
   > Als u een persoonlijke Microsoft-account gebruiker bent in de Azure AD-Tenant en de Tenant beheerder beperkte toegang heeft tot Azure Portal, wordt de toegang mogelijk geweigerd. Als u echter de snelkoppeling doorloopt door App-registraties op de zoek balk te typen of deze vast te maken, hebt u toegang tot de nieuwe ervaring.
