---
title: Nieuwe ervaring voor app-registratie in de Azure-portal
titleSuffix: Microsoft identity platform
description: Een inleiding tot de nieuwe app-registratie-ervaring in de Azure-portal
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 50c88dd1785bd9177219054fed3800ca725a5274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154590"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>De nieuwe registratie-ervaring voor Azure-portal-apps

Er zijn veel verbeteringen in de nieuwe [ervaring met app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) in de Azure-portal. Als u meer bekend bent met de ervaring van de toepassingsregistratieportal (apps.dev.microsoft.com) voor het registreren of beheren van geconvergeerde toepassingen, ook wel de oude ervaring genoemd, helpt deze trainingsgids u op weg met behulp van de nieuwe ervaring.

## <a name="whats-not-changing"></a>Wat verandert er niet?

- Uw toepassingen en gerelateerde configuraties zijn te vinden zoals deze is in de nieuwe ervaring. U hoeft de aanvragen niet opnieuw te registreren en gebruikers van uw toepassingen hoeven zich niet opnieuw aan te melden.

    > [!NOTE]
    > U moet zich aanmelden bij het account dat u hebt gebruikt om toepassingen te registreren om ze te vinden in de Azure-portal. We raden u aan de aangemelde gebruiker in de Azure-portal te controleren die overeenkomt met de gebruiker die is aangemeld bij de toepassingsregistratieportal door het e-mailadres van uw profiel te vergelijken.
    >
    > In sommige gevallen, vooral wanneer u zich aanmeldt met persoonlijke Microsoft-accounts (bijvoorbeeld Outlook, Live, Xbox, enz.) met een Azure AD-e-mailadres, kwamen we erachter dat wanneer u vanuit de oude ervaring naar de Azure-portal gaat, u wordt aangemeld voor een ander account met hetzelfde e-mail in uw Azure AD-tenant. Als u nog steeds denkt dat uw aanvragen ontbreken, meldt u zich af en meldt u zich aan met het juiste account.

- Live SDK-apps die zijn gemaakt met persoonlijke Microsoft-accounts worden nog niet ondersteund in de Azure-portal en blijven in de nabije toekomst in de oude ervaring.

## <a name="key-changes"></a>Belangrijkste wijzigingen

-   In de oude ervaring werden apps standaard geregistreerd als geconvergeerde apps die alle organisatieaccounts (multitenant) en persoonlijke Microsoft-accounts ondersteunen. Dit kan niet worden gewijzigd door de oude ervaring, waardoor het moeilijk is om apps te maken die alleen organisatorische accounts ondersteunen (multitenant of enkele tenant).
    Met de nieuwe ervaring u apps registreren die al deze opties ondersteunen. [Meer informatie over app-typen](active-directory-v2-registration-portal.md).

-   In de nieuwe ervaring, als uw persoonlijke Microsoft-account ook in een Azure AD-tenant is, ziet u drie tabbladen- alle toepassingen in de tenant, eigen toepassingen in de tenant en toepassingen van uw persoonlijke account. Als u dus van mening bent dat apps die zijn geregistreerd bij uw persoonlijke Microsoft-account ontbreken, controleert u het tabblad Toepassingen op het tabblad **Persoonlijke account.**

-   In de nieuwe ervaring u eenvoudig schakelen tussen huurders door naar uw profiel te navigeren en switch directory te kiezen.

## <a name="list-of-applications"></a>Lijst van aanvragen

-   De lijst met nieuwe apps toont toepassingen die zijn geregistreerd via de ervaring met oudere app-registraties in de Azure-portal (apps die alleen in Azure AD-accounts worden aangemeld) en apps die zijn geregistreerd via de [toepassingsregistratieportal](https://apps.dev.microsoft.com/) (apps die zowel in Azure AD- als in persoonlijke Microsoft-accounts worden aangemeld).

-   De nieuwe app-lijst bevat twee extra kolommen: **Gemaakt op** kolom en certificaten **& secrets** kolom die de status (huidige, verlopen binnenkort, of verlopen) van referenties die zijn geregistreerd op de app toont.

## <a name="new-app-registration"></a>Nieuwe app-registratie

In de oude ervaring, om een geconvergeerde app te registreren, hoefde u alleen een naam op te geven. De apps die zijn gemaakt, zijn geregistreerd als geconvergeerde apps ter ondersteuning van alle organisatiemap (multitenant) en persoonlijke Microsoft-accounts.  Dit kan niet worden gewijzigd door de oude ervaring, waardoor het moeilijk is om apps te maken die alleen organisatorische accounts ondersteunen (multitenant of enkele tenant). [Meer informatie over ondersteunde accounttypen](v2-supported-account-types.md)

In de nieuwe ervaring moet u een naam voor de app opgeven en de typen ondersteunde accountkiezen. U optioneel een omleiding uri.
Als u een omleidingsURI opgeeft, moet u opgeven of het web/openbaar is (native/mobile en desktop). Zie [deze quickstart](quickstart-register-app.md)voor meer informatie over het registreren van een app met behulp van de nieuwe ervaring met app-registraties.

## <a name="app-management-page"></a>Pagina App-beheer

De oude ervaring had één app-beheerpagina voor geconvergeerde apps met de volgende secties: Eigenschappen, Toepassingsgeheimen, Platforms, Eigenaren, Microsoft Graph-machtigingen, profiel en geavanceerde opties.

De nieuwe ervaring in de Azure-portal vertegenwoordigt deze functies in afzonderlijke pagina's. Hier vindt u de gelijkwaardige functionaliteit:

-   Eigenschappen - Naam- en toepassings-id staan op de pagina Overzicht.

-   Application Secrets staat op de pagina Certificaten & geheimen

-   De configuratie van platforms staat op de pagina Verificatie

-   Microsoft Graph-machtigingen staan op de pagina MET API-machtigingen, samen met andere machtigingen

-   Profiel staat op de pagina Branding

-   Geavanceerde optie - Live SDK-ondersteuning bevindt zich op de pagina Verificatie.

## <a name="application-secretscertificates--secrets"></a>Toepassingsgeheimen/certificaten & geheimen

In de nieuwe ervaring, **Application secrets** zijn omgedoopt tot Certificaten **& geheimen**. Bovendien worden **openbare sleutels** aangeduid als **certificaten** en **wachtwoorden** worden aangeduid als **Client geheimen**. We hebben ervoor gekozen om deze functionaliteit niet mee te nemen in de nieuwe ervaring om veiligheidsredenen, vandaar dat je niet langer een nieuw sleutelpaar genereren.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Platforms/verificatie: URL's beantwoorden/omleiden VAN URL's
In de oude ervaring had een app de sectie Platforms voor web-, native- en web-API om omleidings-URL's, URL-logboeken en impliciete stroom te configureren.

In de nieuwe ervaring zijn antwoord-URL's te vinden in een sectie Verificatie van apps.\' Bovendien worden ze aangeduid als omleiding URI's en de indeling voor omleiding URI's is veranderd. Ze moeten worden gekoppeld aan een app-type (web of openbare client - mobiel en desktop). [Meer informatie](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

Web-API's zijn geconfigureerd in Een API-pagina blootleggen.

> [!NOTE]
> Probeer de nieuwe verificatie-instellingen-ervaring uit waarbij u instellingen voor uw toepassing configureren op basis van het platform of apparaat dat u wilt targeten. [Meer informatie](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Machtigingen/API-machtigingen voor Microsoft Graph

-   Wanneer u een API in de oude ervaring selecteert, u alleen kiezen uit Microsoft Graph API's. In de nieuwe ervaring u kiezen uit veel Microsoft API's, waaronder Microsoft Graph, API's van uw organisatie en uw API's, dit wordt gepresenteerd in drie tabbladen: Microsoft API's, API's die mijn organisatie gebruikt of Mijn API's. De zoekbalk op API's die mijn organisatie gebruikt, zoekt op tabbladen via serviceprincipals in de tenant.

    > [!NOTE]
    > U ziet dit tabblad niet als uw toepassing niet is gekoppeld aan een tenant. Zie [deze snelstart](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md)voor meer informatie over het aanvragen van machtigingen met behulp van de nieuwe ervaring.

-   De oude ervaring had geen knop **subsidiemachtigingen.** In de nieuwe ervaring is er een sectie Toestemming voor subsidies met een **toestemmingsknop voor beheerders van een** app in de sectie API-machtigingen van een app. Alleen een beheerder kan toestemming verlenen en deze knop is alleen ingeschakeld voor beheerders. Wanneer een beheerder de **toestemmingsknop Voor beheerders** verleent, wordt toestemming van de beheerder verleend voor alle gevraagde machtigingen.

## <a name="profile"></a>Profiel
In de oude ervaring had Profiel Logo, URL van de startpagina, URL van servicevoorwaarden en URL-configuratie van de privacyverklaring. In de nieuwe ervaring zijn deze te vinden op de pagina Branding.

## <a name="application-manifest"></a>Manifest van de toepassing
In de nieuwe ervaring u met manifestpagina de kenmerken van apps bewerken en bijwerken. Zie [Toepassingsmanifest](reference-app-manifest.md)voor meer informatie .

## <a name="new-ui"></a>Nieuwe gebruikersinterface
Er is een nieuwe gebruikersinterface voor eigenschappen die voorheen alleen konden worden ingesteld met de manifesteditor of de API of die niet bestonden.

-   Impliciete subsidiestroom (oauth2AllowImplicitFlow) is te vinden op de pagina Verificatie. In tegenstelling tot de oude ervaring, u toegangstokens of ID-tokens inschakelen, of beide.

-   Scopes die zijn gedefinieerd door deze API (oauth2Permissions) en Geautoriseerde clienttoepassingen (preAuthorizedApplications) kunnen worden geconfigureerd via de pagina Een API blootleggen. Zie [deze snelstart](quickstart-configure-app-expose-web-apis.md)voor meer informatie over het configureren van een app als web-API en het blootleggen van machtigingen/scopes.

-   Publisher-domein (dat wordt weergegeven aan gebruikers op de [toestemmingsprompt\'van](application-consent-experience.md)de toepassing) is te vinden op de pagina Branding blade. Zie [deze how-to](howto-configure-publisher-domain.md)voor meer informatie over het configureren van een uitgeversdomein.

## <a name="limitations"></a>Beperkingen

De nieuwe ervaring heeft de volgende beperkingen:

-   De nieuwe ervaring biedt nog geen ondersteuning voor app-registraties voor Azure AD B2C-tenants.

-   De nieuwe ervaring biedt nog geen ondersteuning voor Live SDK-apps die zijn gemaakt met persoonlijke Microsoft-accounts.

-   Het wijzigen van de waarde voor ondersteunde accounts wordt niet ondersteund in de gebruikersinterface. U moet het app-manifest\'gebruiken, tenzij u opnieuw schakelt tussen Azure AD single-tenant en multi-tenant.

   > [!NOTE]
   > Als u een persoonlijke Microsoft-accountgebruiker bent in de Azure AD-tenant en de tenantbeheerder de toegang tot de Azure-portal heeft beperkt, u toegang geweigerd krijgen. Als u echter via de snelkoppeling komt door app-registraties in de zoekbalk te typen of vast te maken, hebt u toegang tot de nieuwe ervaring.
