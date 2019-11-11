---
title: Trainings handleiding voor app-registraties voor Azure Portal (verouderd)-Azure
description: Een inleiding tot de nieuwe ervaring voor toepassings registratie in het micro soft Identity-platform.
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbdb6a2e17ab867b4938e94ae5a20a95cc352daa
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905392"
---
# <a name="training-guide-app-registrations-in-the-azure-portal-legacy"></a>Trainings gids: App-registraties in het Azure Portal (verouderd)

In de Azure Portal vindt u een groot aantal verbeteringen in de nieuwe [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring. Als u vertrouwd bent met de verouderde ervaring, kunt u deze hand leiding gebruiken om aan de slag te gaan met de nieuwe ervaring.

In Azure Active Directory is de nieuwe ervaring voor registratie van toepassingen die hier wordt beschreven, algemeen beschikbaar (GA). In Azure Active Directory B2C (Azure AD B2C) is deze ervaring een preview-versie.

## <a name="key-changes"></a>Sleutel wijzigingen

- App-registraties zijn niet beperkt tot een **Web-app/API** of een **systeem eigen** app. U kunt dezelfde app-registratie voor al deze gegevens gebruiken door de respectieve omleidings-Uri's te registreren.
- De verouderde ervaring ondersteunt apps die alleen worden aangemeld bij organisatie-en Azure AD-accounts. Apps zijn geregistreerd als single-tenant (ondersteunt alleen organisatie accounts van de directory waarin de app is geregistreerd) en kunnen worden gewijzigd in multi tenant (ondersteunt alle organisatie accounts). Met de nieuwe ervaring kunt u apps registreren die beide opties kunnen ondersteunen, evenals een derde optie: alle organisatie accounts en persoonlijke micro soft-accounts.
- De verouderde ervaring is alleen beschikbaar wanneer u bent aangemeld bij de Azure Portal met een organisatie account. Met de nieuwe ervaring kunt u persoonlijke micro soft-accounts gebruiken die niet zijn gekoppeld aan een map.

## <a name="list-of-applications"></a>Lijst met toepassingen

- In de lijst met nieuwe apps worden de toepassingen weer gegeven die zijn geregistreerd via de ervaring voor de verouderde app-registraties in de Azure Portal (apps die zich aanmelden bij Azure AD-accounts) en de apps die zijn geregistreerd bij de [Portal voor toepassings registratie](https://apps.dev.microsoft.com/) (apps die zich aanmelden Azure AD en persoonlijke micro soft-accounts).
- De nieuwe app-lijst heeft geen kolom **toepassings type** (aangezien een registratie van één app verschillende typen kan zijn) en twee extra kolommen heeft: een kolom **gemaakt in** en een lijst met **Certificaten & geheimen** waarin de status wordt weer gegeven (actueel, verlopen binnenkort of verlopen) van referenties die zijn geregistreerd op de app.

## <a name="new-app-registration"></a>Nieuwe app-registratie

In de verouderde ervaring moet u een app registreren die u moet opgeven: **naam**, **toepassings type**en **URL/omleidings-URI voor aanmelden**. De apps die zijn gemaakt, zijn alleen Azure AD-toepassingen met één Tenant, wat betekent dat alleen organisatie accounts worden ondersteund uit de map waarin de app is geregistreerd.

In de nieuwe ervaring moet u een **naam** opgeven voor de app en de **ondersteunde account typen**kiezen. U kunt desgewenst een **omleidings-URI**opgeven. Als u een omleidings-URI opgeeft, moet u opgeven of deze Web/public (mobiel en desktop) is. Zie [een app registreren bij het micro soft-identiteits platform](quickstart-register-app.md)voor meer informatie over het registreren van een app met behulp van de nieuwe app-registraties. Zie [een toepassing registreren in azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md)voor Azure AD B2C.

## <a name="the-legacy-properties-page"></a>De pagina verouderde eigenschappen

De oude ervaring had een **Eigenschappen** pagina die de nieuwe ervaring niet heeft. De Blade **Eigenschappen** bevat de volgende velden: **naam**, **object-id**, **toepassings-id**, **App-ID-URI**, **logo**, **URL van start pagina**, **afmeldings-URL**, **voor waarden van service-URL**, **privacyverklaring URL**, **toepassings type**en **multi tenant.**

Hier kunt u de equivalente functionaliteit in de nieuwe ervaring vinden:

- De **naam**, het **logo**, de URL van de **Start pagina**, de URL **van de service voorwaarden**en de URL van de **privacyverklaring** bevindt zich nu op de **huismerk** pagina van de app.
- De **object-id** en **toepassings-id (client)** bevindt zich op de pagina **overzicht** .
- De functionaliteit die wordt beheerd door de **multi-tenant** wissel knop in de verouderde ervaring is vervangen door de **ondersteunde account typen** op de **verificatie** pagina. Zie [deze Quick](quickstart-modify-supported-accounts.md)start voor meer informatie over hoe multi tenants wordt toegewezen aan de ondersteunde account type opties.
- De **Afmeldings-URL** is nu op de **verificatie** pagina.
- Het **toepassings type** is niet langer een geldig veld. Omleidings-Uri's (die u op de pagina **verificatie** kunt vinden) bepalen in plaats daarvan welke app-typen worden ondersteund.
- De URI van de **App-ID** heet nu URI voor de **toepassings-id** . u kunt dit vinden op de Blade **API beschikbaar** maken. In de verouderde ervaring is deze eigenschap automatisch geregistreerd met de volgende indeling: `https://{tenantdomain}/{appID}` (bijvoorbeeld `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). De nieuwe ervaring wordt automatisch gegenereerd als `api://{appID}`, maar moet expliciet worden opgeslagen. In Azure AD B2C-tenants wordt de `https://{tenantdomain}/{appID}`-indeling nog steeds gebruikt.

## <a name="reply-urlsredirect-urls"></a>Antwoord Url's/omleidings-Url's

In de verouderde ervaring had een app een pagina **antwoord-url's** . In de nieuwe ervaring zijn antwoord-Url's te vinden in de sectie **verificatie** van een app. Daarnaast worden deze **omleidings-uri's**genoemd. Daarnaast is de indeling voor omleidings-Uri's gewijzigd. Ze moeten worden gekoppeld aan een app-type (Web of openbaar). Om veiligheids redenen worden joker tekens en http://-schema's bovendien niet ondersteund (met uitzonde ring van http://localhost).

## <a name="keyscertificates--secrets"></a>Sleutels/certificaten & geheimen

Een app bevat een pagina met **sleutels** voor de oude ervaring. In de nieuwe ervaring is de naam gewijzigd in **certificaten & geheimen**. Daarnaast worden **open bare sleutels** aangeduid als **certificaten** en **wacht woorden** worden aangeduid als **client geheimen**.

## <a name="required-permissionsapi-permissions"></a>Vereiste machtigingen/API-machtigingen

- In de verouderde ervaring beschikt een app over een **vereiste machtigingen** pagina. In de nieuwe ervaring is de naam gewijzigd in **API-machtigingen**.
- Wanneer u in de verouderde ervaring een API selecteert, kunt u kiezen uit een kleine lijst met micro soft-Api's of door service-principals in de Tenant te zoeken. In de nieuwe ervaring kunt u kiezen uit meerdere tabbladen: **micro soft api's**, **api's mijn organisatie gebruikt**of **mijn api's**. De zoek balk op **api's mijn organisatie** gebruikt tabbladen zoeken via service-principals in de Tenant.

   > [!NOTE]
   > Dit tabblad wordt niet weer geven als uw toepassing niet is gekoppeld aan een Tenant. Zie [deze Quick](quickstart-configure-app-access-web-apis.md)start voor meer informatie over het aanvragen van machtigingen met de nieuwe ervaring.

- Boven aan de pagina **aangevraagde machtigingen** heeft de verouderde ervaring de knop **machtigingen verlenen** . In de nieuwe ervaring is er een **toekennings toestemmings** sectie met de knop **toestemming van beheerder verlenen** aan de sectie **API-machtigingen** van een app. Daarnaast zijn er enkele verschillen in de manieren waarop de knoppen worden gebruikt:
   - In de verouderde ervaring varieerden de logica, afhankelijk van de aangemelde gebruiker en de aangevraagde machtigingen. De logica is:
      - Als er alleen toestemming van de gebruiker is aangevraagd en de aangemelde gebruiker geen beheerder was, kan de gebruiker toestemming geven voor de aangevraagde machtigingen.
      - Als ten minste één machtiging is aangevraagd en de aangemelde gebruiker geen beheerder is, is er een fout opgetreden bij het verlenen van toestemming door de gebruiker.
      - Als de aangemelde gebruiker een beheerder was, is de beheerder toestemming verleend voor alle aangevraagde machtigingen.
   - In de nieuwe ervaring kan alleen een beheerder toestemming verlenen. Wanneer een beheerder de knop **toestemming van beheerder verlenen** selecteert, wordt toestemming van de beheerder verleend aan alle aangevraagde machtigingen.

## <a name="deleting-an-app-registration"></a>Een app-registratie verwijderen

In de verouderde ervaring moest een app één Tenant zijn om te worden verwijderd. De knop verwijderen is uitgeschakeld voor apps met meerdere tenants. In de nieuwe ervaring kunnen apps in elke status worden verwijderd, maar u moet de actie bevestigen. Zie [deze Quick](quickstart-remove-app.md)start voor meer informatie over het verwijderen van app-registraties.

## <a name="application-manifest"></a>Manifest van de toepassing

De oude en nieuwe ervaringen gebruiken verschillende versies voor de indeling van de JSON in de manifest editor. Zie het [toepassings manifest](reference-app-manifest.md)voor meer informatie.

## <a name="new-ui"></a>Nieuwe gebruikers interface

Er is een nieuwe gebruikers interface voor eigenschappen die voorheen alleen kunnen worden ingesteld met behulp van de manifest editor of de API, of die niet bestaat.

- **Impliciete toekennings stroom** (oauth2AllowImplicitFlow) vindt u op de **verificatie** pagina. In tegens telling tot in de verouderde ervaring kunt u **toegangs tokens** of **id-tokens**inschakelen, of beide.
- **Bereiken die zijn gedefinieerd door deze API** (oauth2Permissions) en **geautoriseerde client toepassingen** (preAuthorizedApplications) kunnen worden geconfigureerd via de pagina **een API beschikbaar** maken. Zie [deze Quick](quickstart-configure-app-expose-web-apis.md)start voor meer informatie over het configureren van een app om een web-API te zijn en machtigingen/bereiken beschikbaar te maken.
- Het **Uitgever domein** (dat wordt weer gegeven voor gebruikers in de [toestemming vragen](application-consent-experience.md)van de toepassing) kunt u vinden op de **Blade pagina huisstijl** . Zie voor meer informatie over het configureren van een uitgever domein [deze instructies](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Beperkingen

De nieuwe ervaring heeft de volgende beperkingen:

- De indeling van client geheimen (app-wacht woorden) verschilt van die van de verouderde ervaring en kan de CLI verstoren.
- Het wijzigen van de waarde voor ondersteunde accounts wordt niet ondersteund in de gebruikers interface. U moet het app-manifest gebruiken tenzij u overschakelt tussen Azure AD en multi tenant.