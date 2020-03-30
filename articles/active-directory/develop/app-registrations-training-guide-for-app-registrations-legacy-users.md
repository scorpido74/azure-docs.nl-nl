---
title: Nieuwe trainingshandleiding voor app-registratie in de Azure-portal
description: Introduceert de nieuwe Azure portal App-registratie-ervaring
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: marsma
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.openlocfilehash: a437d54dac50be7ddaad899a1cf0a3e93aade8f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154573"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>Nieuwe trainingshandleiding voor app-registratie in de Azure-portal

U veel verbeteringen vinden in de nieuwe [ervaring met app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) in de Azure-portal. Als u bekend bent met de ervaring van app-registraties (legacy) in de Azure-portal, gebruikt u deze trainingshandleiding om aan de slag te gaan met de nieuwe ervaring.

In Azure Active Directory is de nieuwe ervaring met toepassingsregistratie die hier wordt beschreven, algemeen beschikbaar (GA). In Azure Active Directory B2C (Azure AD B2C) bevindt deze ervaring zich in een voorbeeld.

## <a name="key-changes"></a>Belangrijkste wijzigingen

- App-registraties zijn niet beperkt tot een *web-app/API* of een *native* app. U dezelfde app-registratie voor al deze apps gebruiken door de respectievelijke omleidings-URI's te registreren.

- De verouderde ervaring ondersteunde apps die zich aanmelden met alleen Azure AD-accounts (Organizational AD). Apps zijn geregistreerd als single-tenant. Apps ondersteunden alleen organisatorische accounts uit de map waarin de app is geregistreerd. Apps kunnen worden gewijzigd om meerdere tenant te zijn en alle organisatieaccounts te ondersteunen. Met de nieuwe ervaring u apps registreren die zowel deze opties als een derde optie kunnen ondersteunen: alle organisatieaccounts en persoonlijke Microsoft-accounts.

- De verouderde ervaring was alleen beschikbaar wanneer deze is aangemeld bij de Azure-portal met behulp van een organisatieaccount. Met de nieuwe ervaring u persoonlijke Microsoft-accounts gebruiken die niet aan een map zijn gekoppeld.

## <a name="list-of-applications"></a>Lijst van aanvragen

De lijst met nieuwe apps toont toepassingen die zijn geregistreerd via de ervaring met oudere app-registraties in de Azure-portal. Deze apps loggen in met Azure AD-accounts. De nieuwe app-lijst toont ook apps geregistreerd via de Application Registration Portal. Deze apps loggen in met Azure AD en persoonlijke Microsoft-accounts.

>[!NOTE]
>Het Aanvraagregistratieportaal is afgeschaft.

De nieuwe app-lijst heeft geen kolom **toepassingstype** omdat één app-registratie meerdere typen kan zijn. De lijst bevat twee extra kolommen: **Gemaakt op** en Certificaten **& secrets**. **Certificaten & geheimen** toont de status van referenties die zijn geregistreerd in de app. Statussen omvatten **Huidige**, **Verlopen binnenkort**, en **verlopen**.

## <a name="new-app-registration"></a>Nieuwe app-registratie

In de legacy-ervaring moet u een app registreren die u moest opgeven: **Naam,** **Toepassingstype**en **Aanmeldings-URL/Redirect URI**. De apps die zijn gemaakt, zijn alleen Azure AD-toepassingen met één tenant. Ze ondersteunden alleen organisatorische accounts uit de directory waarin de app is geregistreerd.

In de nieuwe ervaring moet u een **naam** voor de app opgeven en de **typen ondersteunde accountkiezen.** U optioneel een **OmleidingsURI**verstrekken. Als u een omleidingsURI opgeeft, moet u opgeven of het web/openbaar is (mobiel en desktop). Zie [Snelstart: Een toepassing registreren bij het Microsoft-identiteitsplatform](quickstart-register-app.md)voor meer informatie. Zie Een toepassing registreren [in Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md)voor Azure AD B2C .

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Verschillen tussen de pagina Toepassingsregistratieportal en app-registraties

### <a name="the-legacy-properties-page"></a>De pagina Verouderde eigenschappen

De legacy-ervaring had een **eigenschappenpagina.** **Eigenschappen** hadden de volgende velden:

- **Naam**
- **Object-id**
- **Toepassings-id**
- **App-id-URI**
- **Logo**
- **URL van startpagina**
- **Afmeldings-URL**
- **Url van servicevoorwaarden**
- **URL voor de privacyverklaring**
- **Toepassingstype**
- **Multi-tenant**

De nieuwe ervaring heeft die pagina niet. Hier vindt u de gelijkwaardige functionaliteit:

- **Naam**, **Logo**, **URL van de startpagina**, URL van **servicevoorwaarden**en de **URL van de privacyverklaring** staan nu op de **pagina Branding** van de app.
- **Object-ID** en **toepassings-id (client)** staan op de pagina **Overzicht.**
- De functionaliteit die wordt beheerd door de **functie multitenant** in de verouderde ervaring is vervangen door **ondersteunde accounttypen** op de **pagina Verificatie.** Zie [Snelstart: De accounts wijzigen die door een toepassing worden ondersteund](quickstart-modify-supported-accounts.md)voor meer informatie.
- **De URL van afmelden** bevindt zich nu op de **pagina Verificatie.**
- **Toepassingstype** is niet langer een geldig veld. In plaats daarvan u URI's omleiden, die u op de **pagina Verificatie** vinden, bepalen welke apptypen worden ondersteund.
- **App ID URI** heet nu **Application ID URI** en je het vinden op Expose an **API**. In de legacy-ervaring is deze eigenschap automatisch `https://{tenantdomain}/{appID}`geregistreerd met `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`de volgende indeling: , bijvoorbeeld . In de nieuwe ervaring wordt het `api://{appID}`automatisch gegenereerd als , maar het moet expliciet worden opgeslagen. In Azure AD B2C-tenants wordt de `https://{tenantdomain}/{appID}` indeling nog steeds gebruikt.

### <a name="reply-urlsredirect-urls"></a>Url's beantwoorden/uheen verwijzen

In de legacy-ervaring had een app een **pagina met antwoord-URL's.** In de nieuwe ervaring zijn antwoord-URL's te vinden op **de verificatiepagina van** een app. Ze worden nu aangeduid als **Redirect URI's**.

De indeling voor omleidings-URI's is gewijzigd. Ze moeten worden gekoppeld aan een app-type, web of openbaar. Om veiligheidsredenen worden `http://` wildcards en schema's niet *http://localhost*ondersteund, behalve voor .

### <a name="keyscertificates--secrets"></a>Sleutels/certificaten & geheimen

In de legacy-ervaring had een app **keys-pagina.** In de nieuwe ervaring is het omgedoopt tot **Certificaten & geheimen.**

**Openbare sleutels** worden nu aangeduid als **Certificaten**. **Wachtwoorden** worden nu aangeduid als **Client geheimen**.

### <a name="required-permissionsapi-permissions"></a>Vereiste machtigingen/API-machtigingen

In de oudere ervaring had een app een pagina **vereiste machtigingen.** In de nieuwe ervaring is deze omgedoopt tot **API-machtigingen.**

Wanneer u een API in de legacy-ervaring hebt geselecteerd, u kiezen uit een kleine lijst met Microsoft-API's. U ook zoeken via serviceprincipals in de tenant. In de nieuwe ervaring u kiezen uit meerdere tabbladen: **Microsoft API's,** **API's die mijn organisatie gebruikt**of Mijn **API's**. De zoekbalk op **API's die mijn organisatie** gebruikt, zoekt op tabbladen via serviceprincipals in de tenant.

> [!NOTE]
> U ziet dit tabblad niet als uw toepassing niet is gekoppeld aan een tenant. Zie [Snelstart: Een clienttoepassing configureren om toegang te krijgen tot web-API's](quickstart-configure-app-access-web-apis.md)voor meer informatie over het aanvragen van machtigingen.

De verouderde ervaring had een knop **Machtigingen verlenen** boven aan de pagina **Aangevraagde machtigingen.** In de nieuwe ervaring heeft de **toestemmingspagina grant** toestemming een **toestemmingsknop voor beheerders van** een app in de sectie **API-machtigingen** van een app. Er zijn ook enkele verschillen in de manieren waarop de knoppen functioneren.

In de legacy-ervaring varieerde de logica afhankelijk van de aangemelde gebruiker en de machtigingen die worden aangevraagd. De logica was:

- Als alleen toestemmingsmachtigingen voor gebruikers werden aangevraagd en de aangemelde gebruiker geen beheerder was, kan de gebruiker toestemming van de gebruiker verlenen voor de gevraagde machtigingen.
- Als er ten minste één toestemming is aangevraagd waarvoor toestemming van de beheerder is aangevraagd en de aangemelde gebruiker geen beheerder was, heeft de gebruiker een foutmelding gekregen bij een poging om toestemming te verlenen.
- Als de aangemelde gebruiker een beheerder was, is er toestemming van de beheerder verleend voor alle gevraagde machtigingen.

In de nieuwe ervaring kan alleen een beheerder toestemming verlenen. Wanneer een beheerder **toestemming voor de beheerder**verleent, wordt beheerderstoestemming verleend voor alle gevraagde machtigingen.

## <a name="deleting-an-app-registration"></a>Een app-registratie verwijderen

In de legacy-ervaring u alleen apps met één tenant verwijderen. De knop verwijderen is uitgeschakeld voor apps met meerdere tenants. In de nieuwe ervaring u apps in elke staat verwijderen, maar u moet de actie bevestigen. Zie [Snelstart: Een toepassing verwijderen die is geregistreerd bij het Microsoft-identiteitsplatform](quickstart-remove-app.md)voor meer informatie.

## <a name="application-manifest"></a>Manifest van de toepassing

De legacy en nieuwe ervaringen gebruiken verschillende versies voor het formaat van de JSON in de manifesteditor. Zie [Azure Active Directory-app manifest](reference-app-manifest.md)voor meer informatie .

## <a name="new-ui"></a>Nieuwe gebruikersinterface

De nieuwe ervaring voegt ui-besturingselementen toe voor de volgende eigenschappen:

- Op **de pagina Verificatie** heeft impliciete **subsidiestroom** (`oauth2AllowImplicitFlow`). In tegenstelling tot de legacy-ervaring kun je **Access-tokens** of **ID-tokens**of beide inschakelen.
- De **pagina Een API blootleggen** bevat **scopes die zijn gedefinieerd door deze API** (`oauth2Permissions`) en Geautoriseerde **clienttoepassingen** (`preAuthorizedApplications`). Zie [Quickstart: Een toepassing configureren om web-API's bloot te leggen](quickstart-configure-app-expose-web-apis.md)voor meer informatie over het configureren van een app als web-API en het blootleggen van machtigingen/scopes.
- De pagina **Branding** bevat het **publisher-domein**. Het uitgeversdomein wordt weergegeven aan gebruikers op de [toestemmingsprompt van](application-consent-experience.md)de toepassing. Zie [Hoe: Het uitgeversdomein van een toepassing configureren](howto-configure-publisher-domain.md)voor meer informatie.

## <a name="limitations"></a>Beperkingen

De nieuwe ervaring heeft de volgende beperkingen:

- Het formaat van klantgeheimen (app-wachtwoorden) is anders dan die van de legacy-ervaring en kan CLI breken.
- Het wijzigen van de waarde voor ondersteunde accounts wordt niet ondersteund in de gebruikersinterface. U moet het app-manifest gebruiken, tenzij u schakelt tussen Azure AD single-tenant en multi-tenant.
