---
title: Toegang tot de beheerde ontwikkelaarsportal openen en aanpassen - Azure API Management | Microsoft Documenten
description: Meer informatie over het gebruik van de beheerde versie van de ontwikkelaarsportal in API-beheer.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
ms.openlocfilehash: af7c995c11322a538dd9e27a905f1ddbc723e8ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244093"
---
# <a name="access-and-customize-developer-portal"></a>Ontwikkelaarsportal openen en aanpassen

Developer portal is een automatisch gegenereerde, volledig aanpasbare website met de documentatie van uw API's. Het is waar API-consumenten uw API's kunnen ontdekken, leren hoe ze ze kunnen gebruiken en toegang kunnen aanvragen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Toegang tot de beheerde versie van de ontwikkelaarsportal
> * Navigeren in de beheerinterface
> * De inhoud aanpassen
> * De wijzigingen publiceren
> * Bekijk de gepubliceerde portal

Meer informatie over de ontwikkelaarsportal vindt u in het overzicht van de [Azure API Management developer portal.](api-management-howto-developer-portal.md)

![API Management developer portal - admin mode](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>Vereisten

- De volgende quickstart voltooien: [een azure API-beheerexemplaar maken](get-started-create-service-instance.md)
- Een Azure API Management-exemplaar importeren en publiceren. Zie [Importeren en publiceren](import-and-publish.md) voor meer informatie

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Toegang tot de portal als beheerder

Volg de onderstaande stappen om toegang te krijgen tot de beheerde versie van de portal.

1. Ga naar de instantie API Management-service in de Azure-portal.
1. Klik op de knop **Ontwikkelaarsportal** in de bovenste navigatiebalk. Er wordt een nieuw browsertabblad geopend met een administratieve versie van de portal.

## <a name="understand-the-portals-administrative-interface"></a>De administratieve interface van de portal begrijpen

### <a name="default-content"></a>Standaardinhoud 

Als u de portal voor de eerste keer opent, wordt de standaardinhoud automatisch op de achtergrond ingericht. Standaardinhoud is ontworpen om de mogelijkheden van portalen te demonstreren en de hoeveelheid aanpassingen die nodig zijn om uw portal te personaliseren, te minimaliseren. Meer informatie over wat er is opgenomen in de portalinhoud in het overzicht van de [Azure API Management-ontwikkelaarsportal.](api-management-howto-developer-portal.md)

### <a name="visual-editor"></a>Visual editor

U de inhoud van de portal aanpassen met de visuele editor. Met de menusecties aan de linkerkant u pagina's, media, lay-outs, menu's, stijlen of website-instellingen maken of wijzigen. Met de menu-items onderaan u schakelen tussen viewports (bijvoorbeeld mobiel of desktop), de elementen van de portal zichtbaar maken voor geverifieerde of anonieme gebruikers of acties opslaan of ongedaan maken.

U rijen aan een pagina toevoegen door op een blauw pictogram met een plusteken te klikken. Widgets (bijvoorbeeld tekst, afbeeldingen of API's lijst) kunnen worden toegevoegd door op een grijs pictogram met een plusteken te drukken. U items op een pagina opnieuw rangschikken met de interactie slepen en neerzetten. 

### <a name="layouts-and-pages"></a>Indelingen en pagina's

![Pagina's en lay-outs](media/api-management-howto-developer-portal-customize/pages-layouts.png)

Indelingen bepalen hoe pagina's worden weergegeven. In de standaardinhoud zijn er bijvoorbeeld twee indelingen: de ene is van toepassing op de startpagina en de andere op alle resterende pagina's.

Een lay-out wordt toegepast op een pagina door de URL-sjabloon af te koppelen aan de URL van de pagina. De lay-out met een `/wiki/*` URL-sjabloon wordt bijvoorbeeld `/wiki/` toegepast op `/wiki/getting-started`elke `/wiki/styles`pagina met het segment in de URL: , enz.

In de afbeelding hierboven wordt de inhoud van de indeling blauw gemarkeerd, terwijl de pagina rood is gemarkeerd. De menusecties zijn respectievelijk gemarkeerd.

### <a name="styling-guide"></a>Styling gids

![Styling gids](media/api-management-howto-developer-portal-customize/styling-guide.png)

Styling gids is een paneel gemaakt met ontwerpers in het achterhoofd. Het zorgt voor het toezicht op en styling alle visuele elementen in uw portal. De styling is hiërarchisch - veel elementen erven eigenschappen van andere elementen. Knopelementen gebruiken bijvoorbeeld kleuren voor tekst en achtergrond. Als u de kleur van een knop wilt wijzigen, moet u de oorspronkelijke kleurvariant wijzigen.

Als u een variant wilt bewerken, klikt u erop en selecteert u het potloodpictogram dat erbovenop wordt weergegeven. Zodra u de wijzigingen in het pop-upvenster aanbrengt, sluit u het.

### <a name="save-button"></a>De knop Opslaan

![De knop Opslaan](media/api-management-howto-developer-portal-customize/save-button.png)

Wanneer u een wijziging aanbrengt in de portal, moet u deze handmatig opslaan door op de knop **Opslaan** in het menu onderaan te drukken. Wanneer u uw wijzigingen opslaat, wordt de gewijzigde inhoud automatisch geüpload naar uw API Management-service.

## <a name="customize-the-portals-content"></a>De inhoud van de portal aanpassen

Voordat u uw portal beschikbaar stelt aan de bezoekers, moet u de automatisch gegenereerde inhoud personaliseren. Aanbevolen wijzigingen zijn de lay-outs, stijlen en de inhoud van de startpagina.

> [!NOTE]
> Vanwege integratieoverwegingen kunnen de volgende pagina's niet worden `/404`verwijderd `/500` `/captcha`of `/change-password` `/config.json`verplaatst `/confirm/invitation` `/confirm-v2/identities/basic/signup`onder `/confirm-v2/password` `/internal-status-0123456789abcdef`een `/publish` `/signin`andere `/signin-sso` `/signup`URL: , , , , , , , , , , , , , , .

### <a name="home-page"></a>Startpagina

De **standaardstartpagina** is gevuld met dummy-inhoud. U de hele secties met de inhoud verwijderen of de structuur behouden en de elementen één voor één aanpassen. Vervang de gegenereerde tekst en afbeeldingen door die van u en zorg ervoor dat de koppelingen naar de gewenste locaties verwijzen.

### <a name="layouts"></a>Indelingen

Vervang het automatisch gegenereerde logo in de navigatiebalk door uw eigen afbeelding.

### <a name="styling"></a>Styling

Hoewel u geen stijlen hoeft aan te passen, u overwegen bepaalde elementen aan te passen. Wijzig bijvoorbeeld de primaire kleur die overeenkomt met de kleur van uw merk.

### <a name="customization-example"></a>Voorbeeld van aanpassing

In de onderstaande video laten we zien hoe je de inhoud van de portal bewerken, het uiterlijk van de website aanpassen en de wijzigingen publiceren.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>De portal publiceren

Om uw portal en de nieuwste wijzigingen beschikbaar te maken voor bezoekers, moet u deze publiceren.

1. Zorg ervoor dat u de wijzigingen hebt opgeslagen door op het pictogram **Opslaan te** klikken.
1. Klik op **De website publiceren** in het gedeelte **Bewerkingen** van het menu. Deze bewerking kan enkele minuten duren.  
    ![Portal publiceren](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> De portal moet opnieuw worden gepubliceerd nadat api-beheerserviceconfiguratieisswijzigingen zijn gewijzigd, zoals het toewijzen van een aangepast domein, het bijwerken van de identiteitsproviders, het instellen van delegatie, het opgeven van aanmeldings- en productvoorwaarden en meer.

## <a name="visit-the-published-portal"></a>Bezoek het gepubliceerde portaal

Nadat u de portal hebt gepubliceerd, u deze openen `https://contoso-api.developer.azure-api.net`op dezelfde URL als bijvoorbeeld het beheerderspaneel. Bekijk het in een aparte browsersessie (incognito / private browsing mode) als een externe bezoeker.

## <a name="apply-the-cors-policy-on-apis"></a>Het CORS-beleid toepassen op API's

U moet CORS (cross-origin resource sharing) inschakelen op uw API's om de bezoekers van uw portal de API's te laten testen via de ingebouwde interactieve console. Raadpleeg [dit documentatieartikel](api-management-howto-developer-portal.md#cors) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de ontwikkelaarsportal:

- [Overzicht van azure API Management-ontwikkelaarsportal](api-management-howto-developer-portal.md)
