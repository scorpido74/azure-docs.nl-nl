---
title: Trainings handleiding voor het overstappen van App-registraties (verouderd) naar de nieuwe App-registraties ervaring in de Azure Portal
description: Een inleiding tot de nieuwe app-registratie ervaring in de Azure Portal
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
ms.date: 10/25/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01d543262a9eb358643c0860b24ac4306d2c5edf
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927100"
---
# <a name="transitioning-from-app-registrations-legacy-to-the-new-app-registrations-experience-in-the-azure-portal"></a>Overstappen van App-registraties (verouderd) naar de nieuwe App-registraties ervaring in de Azure Portal

U kunt een groot aantal verbeteringen vinden in de nieuwe [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) -ervaring in de Azure Portal. Als u bekend bent met de App-registraties (verouderde) ervaring in de Azure Portal, gebruikt u deze hand leiding om aan de slag te gaan met de nieuwe ervaring.

In Azure Active Directory is de nieuwe ervaring voor registratie van toepassingen die hier wordt beschreven, algemeen beschikbaar (GA). In Azure Active Directory B2C (Azure AD B2C) is deze ervaring een preview-versie.

## <a name="key-changes"></a>Sleutel wijzigingen

- App-registraties zijn niet beperkt tot een *Web-app/API* of een *systeem eigen* app. U kunt dezelfde app-registratie voor al deze apps gebruiken door de respectieve omleidings-Uri's te registreren.

- De oudere ervaring ondersteunt apps die zich alleen aanmelden met behulp van organisatorische (Azure AD)-accounts. Apps zijn geregistreerd als één Tenant. Apps worden alleen ondersteund voor organisatie-accounts uit de map waarin de app is geregistreerd. Apps kunnen worden gewijzigd in multi tenant en ondersteunen alle organisatie accounts. Met de nieuwe ervaring kunt u apps registreren die beide opties kunnen ondersteunen, evenals een derde optie: alle organisatie accounts en persoonlijke micro soft-accounts.

- De verouderde ervaring is alleen beschikbaar wanneer u bent aangemeld bij de Azure Portal met een organisatie account. Met de nieuwe ervaring kunt u persoonlijke micro soft-accounts gebruiken die niet zijn gekoppeld aan een map.

## <a name="list-of-applications"></a>Lijst met toepassingen

In de lijst met nieuwe apps worden de toepassingen weer gegeven die zijn geregistreerd via de ervaring voor de verouderde app-registraties in de Azure Portal. Met deze apps meldt u zich aan met Azure AD-accounts. In de nieuwe app-lijst worden ook de apps weer gegeven die zijn geregistreerd bij de portal voor toepassings registratie. Met deze apps meldt u zich aan met Azure AD en persoonlijke micro soft-accounts.

>[!NOTE]
>De portal voor toepassings registratie is afgeschaft.

De nieuwe app-lijst heeft geen kolom **toepassings type** omdat een registratie van één app verschillende typen kan zijn. De lijst bevat twee extra kolommen: **gemaakt op** en **Certificaten & geheimen**. **Certificaten & geheimen** toont de status van de referenties die zijn geregistreerd op de app. Statussen zijn **actueel**, **binnenkort verlopen**en **verlopen**.

## <a name="new-app-registration"></a>Nieuwe app-registratie

In de verouderde ervaring moet u een app registreren die u moet opgeven: **naam**, **toepassings type**en **URL/omleidings-URI voor aanmelden**. De apps die zijn gemaakt, zijn alleen Azure AD-toepassingen met één Tenant. Er worden alleen organisatie accounts ondersteund uit de map waarin de app is geregistreerd.

In de nieuwe ervaring moet u een **naam** opgeven voor de app en de **ondersteunde account typen**kiezen. U kunt desgewenst een **omleidings-URI**opgeven. Als u een omleidings-URI opgeeft, moet u opgeven of deze Web/public (mobiel en desktop) is. Zie [Quick Start: een toepassing registreren bij het micro soft Identity-platform](quickstart-register-app.md)voor meer informatie. Zie [een toepassing registreren in azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md)voor Azure AD B2C.

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Verschillen tussen de portal voor toepassings registratie en de App-registraties pagina

### <a name="the-legacy-properties-page"></a>De pagina verouderde eigenschappen

De verouderde ervaring had een **Eigenschappen** pagina. **Eigenschappen** hebben de volgende velden:

- **Naam**
- **Object-ID**
- **Toepassings-ID**
- **App-id-URI**
- **Logo**
- **URL van start pagina**
- **Afmeldings-URL**
- **URL voor de service voorwaarden**
- **URL van privacyverklaring**
- **Toepassingstype**
- **Multi tenant**

Deze pagina is niet in de nieuwe ervaring. Hier kunt u de equivalente functionaliteit vinden:

- De **naam**, het **logo**, de URL van de **Start pagina**, de URL **van de service voorwaarden**en de URL van de **privacyverklaring** van de app bevinden zich nu op de **huisstijl** pagina.
- De **object-id** en **toepassings-id (client)** bevinden zich op de pagina **overzicht** .
- De functionaliteit die wordt beheerd door de **multi-tenant** wissel knop in de verouderde ervaring is vervangen door de **ondersteunde account typen** op de **verificatie** pagina. Zie [Quick Start: de accounts wijzigen die door een toepassing worden ondersteund](quickstart-modify-supported-accounts.md)voor meer informatie.
- De **Afmeldings-URL** is nu op de **verificatie** pagina.
- Het **toepassings type** is niet langer een geldig veld. In plaats daarvan omleidings-Uri's, die u op de pagina **verificatie** kunt vinden, bepaalt u welke typen apps worden ondersteund.
- De URI van de **App-ID** heet nu URI voor de **toepassings-id** en u kunt deze vinden op **een API beschikbaar**maken. In de verouderde ervaring is deze eigenschap auto geregistreerd met de volgende indeling: `https://{tenantdomain}/{appID}`bijvoorbeeld `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`. In de nieuwe ervaring wordt het automatisch gegenereerd als `api://{appID}`, maar moet het expliciet worden opgeslagen. In Azure AD B2C-tenants wordt de `https://{tenantdomain}/{appID}`-indeling nog steeds gebruikt.

### <a name="reply-urlsredirect-urls"></a>Antwoord Url's/omleidings-Url's

In de verouderde ervaring had een app een pagina **antwoord-url's** . In de nieuwe ervaring kunnen antwoord-Url's worden gevonden op de **verificatie** pagina van de app. Ze worden nu **omleidings-uri's**genoemd.

De indeling voor omleidings-Uri's is gewijzigd. Ze moeten worden gekoppeld aan een app-type, hetzij web of openbaar. Uit veiligheids overwegingen worden joker tekens en `http://`-schema's niet ondersteund, met uitzonde ring van *http://localhost* .

### <a name="keyscertificates--secrets"></a>Sleutels/certificaten & geheimen

Een app bevat een pagina met **sleutels** voor de oude ervaring. In de nieuwe ervaring is de naam gewijzigd in **certificaten & geheimen**.

**Open bare sleutels** worden nu **certificaten**genoemd. **Wacht woorden** worden nu **client geheimen**genoemd.

### <a name="required-permissionsapi-permissions"></a>Vereiste machtigingen/API-machtigingen

In de verouderde ervaring beschikt een app over een **vereiste machtigingen** pagina. In de nieuwe ervaring is de naam gewijzigd in **API-machtigingen**.

Wanneer u in de verouderde ervaring een API hebt geselecteerd, kunt u kiezen uit een kleine lijst met micro soft-Api's. U kunt ook zoeken in service-principals in de Tenant. In de nieuwe ervaring kunt u kiezen uit meerdere tabbladen: **micro soft api's**, **api's mijn organisatie gebruikt**of **mijn api's**. De zoek balk op **api's mijn organisatie** gebruikt tabbladen zoeken via service-principals in de Tenant.

> [!NOTE]
> Dit tabblad wordt niet weer geven als uw toepassing niet is gekoppeld aan een Tenant. Voor meer informatie over het aanvragen van machtigingen raadpleegt u [Quick Start: een client toepassing configureren voor toegang tot Web-api's](quickstart-configure-app-access-web-apis.md).

Boven aan de pagina **aangevraagde machtigingen** heeft de verouderde ervaring de knop **machtigingen verlenen** . In de nieuwe ervaring is de pagina **granting toestemming** verleend aan de sectie **toestemming geven** voor het verlenen van machtigingen voor de **API** van een app. Er zijn ook enkele verschillen in de manier waarop de knoppen werken.

In de verouderde ervaring varieerden de logica, afhankelijk van de aangemelde gebruiker en de aangevraagde machtigingen. De logica is:

- Als er alleen toestemming van de gebruiker is aangevraagd en de aangemelde gebruiker geen beheerder is, kan de gebruiker toestemming geven voor de aangevraagde machtigingen.
- Als ten minste één machtiging is aangevraagd en de aangemelde gebruiker geen beheerder is, is er een fout opgetreden bij het verlenen van toestemming door de gebruiker.
- Als de aangemelde gebruiker een beheerder was, is de beheerder toestemming verleend voor alle aangevraagde machtigingen.

In de nieuwe ervaring kan alleen een beheerder toestemming verlenen. Wanneer een beheerder **toestemming geven**voor het verlenen van beheerders, wordt toestemming van de beheerder verleend aan alle aangevraagde machtigingen.

## <a name="deleting-an-app-registration"></a>Een app-registratie verwijderen

In de verouderde ervaring kunt u alleen apps met één Tenant verwijderen. De knop verwijderen is uitgeschakeld voor apps met meerdere tenants. In de nieuwe ervaring kunt u apps in elke status verwijderen, maar u moet de actie bevestigen. Zie [Quick Start: een toepassing verwijderen die is geregistreerd bij het micro soft Identity-platform](quickstart-remove-app.md)voor meer informatie.

## <a name="application-manifest"></a>Manifest van de toepassing

De oude en nieuwe ervaringen gebruiken verschillende versies voor de indeling van de JSON in de manifest editor. Zie [Azure Active Directory app-manifest](reference-app-manifest.md)voor meer informatie.

## <a name="new-ui"></a>Nieuwe gebruikers interface

De nieuwe ervaring voegt UI-besturings elementen toe voor de volgende eigenschappen:

- De **verificatie** pagina heeft **impliciete granting flow** (`oauth2AllowImplicitFlow`). In tegens telling tot in de verouderde ervaring kunt u **toegangs tokens** of **id-tokens**inschakelen, of beide.
- De pagina **een API beschikbaar** maken bevat **scopes die zijn gedefinieerd door deze API** (`oauth2Permissions`) en **geautoriseerde client toepassingen** (`preAuthorizedApplications`). Voor meer informatie over het configureren van een app voor een web-API en het beschikbaar maken van machtigingen/bereiken raadpleegt [u Quick Start: een toepassing configureren om Web-api's weer te](quickstart-configure-app-expose-web-apis.md)geven.
- De pagina **huisstijl** bevat het **domein**van de uitgever. Het domein van de uitgever wordt weer gegeven voor gebruikers op de [toestemming prompt van de toepassing](application-consent-experience.md). Zie [How to: configure a Application Publisher Domain](howto-configure-publisher-domain.md)(Engelstalig) voor meer informatie.

## <a name="limitations"></a>Beperkingen

De nieuwe ervaring heeft de volgende beperkingen:

- De indeling van client geheimen (app-wacht woorden) verschilt van die van de verouderde ervaring en kan de CLI verstoren.
- Het wijzigen van de waarde voor ondersteunde accounts wordt niet ondersteund in de gebruikers interface. U moet het app-manifest gebruiken tenzij u overschakelt tussen Azure AD en multi tenant.
