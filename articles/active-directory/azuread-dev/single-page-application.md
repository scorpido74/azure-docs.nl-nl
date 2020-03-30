---
title: Toepassingen met één pagina in Azure Active Directory
description: Beschrijft wat toepassingen (SB's) met één pagina zijn en wat de basisprincipes zijn voor de protocolstroom, registratie en tokenverloop voor dit app-type.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: adf3c5b5cd40a9ea3f07ba9c92cfc4544ca60f1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154743"
---
# <a name="single-page-applications"></a>Toepassingen met één pagina

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Toepassingen met één pagina (SB's) zijn doorgaans gestructureerd als een JavaScript-presentatielaag (front-end) die in de browser wordt uitgevoerd, en een web-API-back-end die op een server wordt uitgevoerd en de bedrijfslogica van de toepassing implementeert. Zie De impliciete subsidiestroom van [OAuth2 in Azure Active Directory begrijpen](v1-oauth2-implicit-grant-flow.md)voor meer informatie over de impliciete autorisatieverlening en u helpen beslissen of deze geschikt is voor uw toepassingsscenario.

In dit scenario gebruikt de JavaScript-front-end [active directory-verificatiebibliotheek voor JavaScript (ADAL) wanneer de gebruiker zich aanmeldt. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) en de impliciete autorisatieverlening voor het verkrijgen van een ID-token (id_token) van Azure AD. Het token wordt in de cache opgeslagen en de client hecht het aan het verzoek als het token aan toonder bij het maken van oproepen naar de web-API back-end, die is beveiligd met behulp van de OWIN middleware.

## <a name="diagram"></a>Diagram

![Toepassingsdiagram met één pagina](./media/authentication-scenarios/single-page-app.png)

## <a name="protocol-flow"></a>Protocolstroom

1. De gebruiker navigeert naar de webtoepassing.
1. De toepassing retourneert de JavaScript front-end (presentatielaag) naar de browser.
1. De gebruiker initieert zich aan te melden, bijvoorbeeld door op een aanmeldingslink te klikken. De browser stuurt een GET naar het eindpunt van de Azure AD-autorisatie om een ID-token aan te vragen. Deze aanvraag bevat de toepassings-ID en de antwoord-URL in de queryparameters.
1. Azure AD valideert de url van het antwoord op basis van de geregistreerde url van antwoord die is geconfigureerd in de Azure-portal.
1. De gebruiker meldt zich aan op de aanmeldingspagina.
1. Als de verificatie is geslaagd, maakt Azure AD een ID-token en retourneert het als een URL-fragment (#) naar de url van antwoord van de toepassing. Voor een productietoepassing moet deze URL van het antwoord HTTPS zijn. Het geretourneerde token bevat claims over de gebruiker en Azure AD die door de toepassing vereist zijn om het token te valideren.
1. De JavaScript-clientcode die in de browser wordt uitgevoerd, haalt het token uit het antwoord dat moet worden gebruikt bij het beveiligen van oproepen naar de web-API-back-end van de toepassing.
1. De browser roept de web-API-back-end van de toepassing aan met het ID-token in de autorisatiekoptekst. De Azure AD-verificatieservice geeft een ID-token uit dat kan worden gebruikt als een token aan toonder als de bron hetzelfde is als de client-id (in dit geval is dit het geval omdat de web-API de eigen backend van de app is).

## <a name="code-samples"></a>Codevoorbeelden

Zie de [codevoorbeelden voor toepassingsscenario's met één pagina](sample-v1-code.md#single-page-applications). Zorg ervoor dat u regelmatig terug te controleren als nieuwe monsters worden vaak toegevoegd.

## <a name="app-registration"></a>App-registratie

* Eén tenant - Als u een toepassing bouwt die alleen voor uw organisatie is, moet deze worden geregistreerd in de map van uw bedrijf met behulp van de Azure-portal.
* Multi-tenant - Als u een toepassing bouwt die kan worden gebruikt door gebruikers buiten uw organisatie, moet deze worden geregistreerd in de directory van uw bedrijf, maar moet deze ook worden geregistreerd in de directory van elke organisatie die de toepassing gebruikt. Om uw toepassing beschikbaar te maken in hun directory, u een aanmeldingsproces voor uw klanten opnemen waarmee ze toestemming kunnen geven voor uw toepassing. Wanneer ze zich aanmelden voor uw aanvraag, krijgen ze een dialoogvenster te zien met de machtigingen die de toepassing nodig heeft en vervolgens de optie om toestemming te geven. Afhankelijk van de vereiste machtigingen kan een beheerder in de andere organisatie worden verplicht om toestemming te geven. Wanneer de gebruiker of beheerder hiermee instemt, wordt de toepassing geregistreerd in zijn map.

Na het registreren van de toepassing moet deze zijn geconfigureerd om het impliciete subsidieprotocol van OAuth 2.0 te gebruiken. Standaard is dit protocol uitgeschakeld voor toepassingen. Als u het impliciete subsidieprotocol OAuth2 voor uw toepassing wilt inschakelen, bewerkt u het toepassingsmanifest van de Azure-portal en stelt u de waarde 'oauth2AllowImplicitFlow' in op true. Zie [Toepassingsmanifest](../develop/reference-app-manifest.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor meer informatie .

## <a name="token-expiration"></a>Token vervaldatum

Het gebruik van ADAL.js helpt bij:

* Een verlopen token vernieuwen
* Een toegangstoken aanvragen om een web-API-bron aan te roepen

Na een succesvolle verificatie schrijft Azure AD een cookie in de browser van de gebruiker om een sessie vast te stellen. Houd er rekening mee dat de sessie bestaat tussen de gebruiker en Azure AD (niet tussen de gebruiker en de webtoepassing). Wanneer een token verloopt, gebruikt ADAL.js deze sessie om in stilte een ander token te verkrijgen. ADAL.js gebruikt een verborgen iFrame om de aanvraag te verzenden en te ontvangen met behulp van het OAuth impliciete subsidieprotocol. ADAL.js kan dit zelfde mechanisme ook gebruiken om in stilte toegangstokens te verkrijgen voor andere web API-bronnen die de toepassing aanroept, zolang deze bronnen cross-origin resource sharing (CORS) ondersteunen, worden geregistreerd in de gebruikersmap en de vereiste toestemming is tijdens het aanmelden door de gebruiker.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [toepassingstypen en -scenario's](app-types.md)
* Meer informatie over de [basisprincipes](v1-authentication-scenarios.md) van Azure AD-verificatie
