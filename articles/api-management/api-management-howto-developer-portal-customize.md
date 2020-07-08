---
title: De beheerde ontwikkelaars Portal openen en aanpassen-Azure API Management | Microsoft Docs
description: Meer informatie over het gebruik van de beheerde versie van de ontwikkelaars Portal in API Management.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79244093"
---
# <a name="access-and-customize-developer-portal"></a>De ontwikkelaars Portal openen en aanpassen

Ontwikkelaars Portal is een automatisch gegenereerde, volledig aanpas bare website met de documentatie van uw Api's. Het is waar API-gebruikers uw Api's kunnen detecteren, meer informatie over het gebruik ervan en toegang vragen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Toegang tot de beheerde versie van de ontwikkelaars Portal
> * Navigeren door de beheer interface
> * De inhoud aanpassen
> * De wijzigingen publiceren
> * De gepubliceerde portal weer geven

Meer informatie over de ontwikkelaars Portal vindt u in het overzicht van de [Azure API Management-ontwikkelaars Portal](api-management-howto-developer-portal.md).

![API Management ontwikkelaars Portal-beheer modus](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>Vereisten

- Voer de volgende Snelstartgids uit: [een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
- Importeer en publiceer een Azure API Management-exemplaar. Zie voor meer informatie [importeren en publiceren](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Toegang krijgen tot de portal als beheerder

Volg de onderstaande stappen om toegang te krijgen tot de beheerde versie van de portal.

1. Ga naar uw API Management service-exemplaar in de Azure Portal.
1. Klik op de knop **ontwikkelaars Portal** in de bovenste navigatie balk. Er wordt een nieuw browser tabblad met een beheer versie van de portal geopend.

## <a name="understand-the-portals-administrative-interface"></a>Meer informatie over de beheer interface van de portal

### <a name="default-content"></a>Standaard inhoud 

Als u de portal voor de eerste keer opent, wordt de standaard inhoud automatisch op de achtergrond ingericht. Standaard inhoud is ontworpen om de mogelijkheden van de portal te presen teren en de hoeveelheid aanpassingen te minimaliseren die nodig zijn om uw portal aan te passen. Meer informatie over wat er is opgenomen in de Portal-inhoud, vindt u in het overzicht van de [Azure API Management-ontwikkelaars Portal](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Visuele editor

U kunt de inhoud van de portal aanpassen met de editor voor visuele elementen. Met de menu secties aan de linkerkant kunt u pagina's, media, indelingen, menu's, stijlen of website-instellingen maken of wijzigen. Met de menu opdrachten aan de onderkant kunt u scha kelen tussen View ports (bijvoorbeeld Mobile of desktop), de elementen van de portal weer geven die zichtbaar zijn voor geverifieerde of anonieme gebruikers of acties voor opslaan of ongedaan maken.

U kunt rijen toevoegen aan een pagina door te klikken op een blauw pictogram met een plus teken. Widgets (bijvoorbeeld tekst, afbeeldingen of Api's lijst) kunnen worden toegevoegd door op een grijs pictogram met een plus teken te drukken. U kunt items op een pagina opnieuw rangschikken met de interactie slepen en neerzetten. 

### <a name="layouts-and-pages"></a>Indelingen en pagina's

![Pagina's en indelingen](media/api-management-howto-developer-portal-customize/pages-layouts.png)

Indelingen bepalen hoe pagina's worden weer gegeven. In de standaard inhoud zijn er bijvoorbeeld twee indelingen: een is van toepassing op de start pagina en de andere op alle resterende pagina's.

Een indeling wordt toegepast op een pagina door te voldoen aan de URL-sjabloon van de pagina. Indeling met een URL-sjabloon van wordt bijvoorbeeld `/wiki/*` toegepast op elke pagina met het `/wiki/` segment in de URL: `/wiki/getting-started` , `/wiki/styles` enzovoort.

In de bovenstaande afbeelding wordt inhoud die deel uitmaakt van de lay-out blauw gemarkeerd, terwijl de pagina rood is gemarkeerd. De menu secties zijn respectievelijk gemarkeerd.

### <a name="styling-guide"></a>Stijl gids

![Stijl gids](media/api-management-howto-developer-portal-customize/styling-guide.png)

Stijl gids is een paneel dat is gemaakt met ontwerpers. Het maakt het mogelijk om alle visuele elementen in uw portal te overzien en te stijlen. De stijl is hiërarchisch: veel elementen nemen eigenschappen over van andere elementen. Knop elementen gebruiken bijvoorbeeld kleuren voor tekst en achtergrond. Als u de kleur van een knop wilt wijzigen, moet u de oorspronkelijke kleur variant wijzigen.

Als u een variant wilt bewerken, klikt u erop en selecteert u het potlood pictogram dat boven op de pagina wordt weer gegeven. Wanneer u de wijzigingen in het pop-upvenster hebt aangebracht, sluit u het.

### <a name="save-button"></a>De knop Opslaan

![De knop Opslaan](media/api-management-howto-developer-portal-customize/save-button.png)

Wanneer u een wijziging aanbrengt in de portal, moet u deze hand matig opslaan door op de knop **Opslaan** in het menu onderaan te drukken. Wanneer u uw wijzigingen opslaat, wordt de gewijzigde inhoud automatisch naar uw API Management-service geüpload.

## <a name="customize-the-portals-content"></a>De inhoud van de portal aanpassen

Voordat u uw portal voor de bezoekers beschikbaar maakt, moet u de automatisch gegenereerde inhoud personaliseren. De aanbevolen wijzigingen zijn de indelingen, stijlen en de inhoud van de start pagina.

> [!NOTE]
> Vanwege integratie overwegingen kunnen de volgende pagina's niet worden verwijderd of verplaatst onder een andere URL: `/404` , `/500` , `/captcha` ,,, `/change-password` `/config.json` `/confirm/invitation` , `/confirm-v2/identities/basic/signup` , `/confirm-v2/password` , `/internal-status-0123456789abcdef` , `/publish` , `/signin` , `/signin-sso` , `/signup` .

### <a name="home-page"></a>Startpagina

De standaard **Startpagina** wordt gevuld met een dummy-inhoud. U kunt de volledige secties met de inhoud verwijderen of de structuur hand haven en de elementen één voor één aanpassen. Vervang de gegenereerde tekst en afbeeldingen met uw eigen afbeelding en zorg ervoor dat de koppelingen naar de gewenste locaties verwijzen.

### <a name="layouts"></a>Indelingen

Vervang het automatisch gegenereerde logo in de navigatie balk door uw eigen afbeelding.

### <a name="styling"></a>Opmaak

Hoewel u geen stijlen hoeft aan te passen, kunt u overwegen bepaalde elementen aan te passen. Wijzig de primaire kleur bijvoorbeeld zodat deze overeenkomt met de kleur van uw merk.

### <a name="customization-example"></a>Voor beeld van aanpassing

In de onderstaande video laten we zien hoe u de inhoud van de portal kunt bewerken, het uiterlijk van de website kunt aanpassen en de wijzigingen publiceert.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>De portal publiceren

Als u uw portal en de meest recente wijzigingen beschikbaar wilt maken voor bezoekers, moet u deze publiceren.

1. Zorg ervoor dat u uw wijzigingen hebt opgeslagen door te klikken op het pictogram **Opslaan** .
1. Klik op **website publiceren** in het gedeelte **bewerkingen** van het menu. Deze bewerking kan enkele minuten duren.  
    ![Portal publiceren](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> De portal moet opnieuw worden gepubliceerd na API Management wijzigingen in de service configuratie, zoals het toewijzen van een aangepast domein, het bijwerken van de id-providers, het instellen van de overdracht, het opgeven van de aanmeldings-en product voorwaarden en nog veel meer.

## <a name="visit-the-published-portal"></a>Ga naar de gepubliceerde Portal

Nadat u de portal hebt gepubliceerd, kunt u deze openen op dezelfde URL als het deel venster beheer, bijvoorbeeld `https://contoso-api.developer.azure-api.net` . Bekijk het in een afzonderlijke browser sessie (incognito/private Browse mode) als een externe gebruiker.

## <a name="apply-the-cors-policy-on-apis"></a>Het CORS-beleid Toep assen op Api's

U moet CORS (cross-Origin Resource Sharing) inschakelen op uw Api's om de bezoekers van uw portal de Api's te laten testen via de ingebouwde interactieve console. Raadpleeg [Dit documentatie artikel](api-management-howto-developer-portal.md#cors) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de ontwikkelaars portal:

- [Overzicht van Azure API Management-ontwikkelaars Portal](api-management-howto-developer-portal.md)
