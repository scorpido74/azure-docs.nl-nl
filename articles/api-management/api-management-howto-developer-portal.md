---
title: Overzicht van Azure API Management-ontwikkelaars Portal-Azure API Management | Microsoft Docs
description: Meer informatie over de ontwikkelaars Portal in API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: eb5e2c2e2eeb0f29eb74b3727ecf14d70d2381f6
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176688"
---
# <a name="azure-api-management-developer-portal-overview"></a>Overzicht van Azure API Management-ontwikkelaars Portal

Ontwikkelaars Portal is een automatisch gegenereerde, volledig aanpas bare website met de documentatie van uw Api's. Het is waar API-consumers uw Api's kunnen detecteren, meer informatie over het gebruik ervan moeten gebruiken, toegang te vragen en uit te proberen.

In dit artikel worden de verschillen beschreven tussen zelf-hostende en beheerde versies van de ontwikkelaars Portal in API Management. Ook wordt de architectuur uitgelegd en vindt u antwoorden op veelgestelde vragen.

> [!IMPORTANT]
> [Meer informatie over het migreren van de preview-versie naar de algemeen beschik bare versie](#preview-to-ga) van de ontwikkelaars Portal.

![API Management ontwikkelaars Portal](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>Beheerde en zelf-hostende versies

U kunt uw ontwikkelaars Portal op twee manieren bouwen:

- **Beheerde versie** : door de portal te bewerken en aan te passen, die in uw API Management-exemplaar is ingebouwd en toegankelijk is via de URL `<your-api-management-instance-name>.developer.azure-api.net`. Raadpleeg [Dit documentatie artikel](api-management-howto-developer-portal-customize.md) voor meer informatie over het openen en aanpassen van de beheerde Portal.
- **Zelf-hostende versie** : door uw portal buiten een API Management-exemplaar te implementeren en zelf te hosten. Met deze aanpak kunt u de code base van de portal bewerken en de meegeleverde kern functionaliteit uitbreiden. U moet ook zelf een upgrade van de portal naar de nieuwste versie uitvoeren. Raadpleeg de [github-opslag plaats met de bron code van de portal][1]voor meer informatie en instructies. In de [zelf studie voor de beheerde versie](api-management-howto-developer-portal-customize.md) wordt het beheer paneel van de portal beschreven. dit wordt ook aanbevolen in de zelf-hostende versie.

## <a name="portal-architectural-concepts"></a>Concepten van de portal architectuur

De portal onderdelen kunnen logisch worden onderverdeeld in twee categorieën: *code* en *inhoud*.

De *code* wordt beheerd in [de GitHub-opslag plaats][1] en omvat:

- Widgets: deze vertegenwoordigen visuele elementen en combi neer HTML, java script, de mogelijkheden, instellingen en inhouds toewijzing. Voor beelden zijn een afbeelding, een tekst alinea, een formulier, een lijst met Api's, enzovoort.
- Stijl definities: Hiermee geeft u op hoe widgets kunnen worden opgemaakt
- Engine: Hiermee worden statische webpagina's gegenereerd op basis van de Portal-inhoud en geschreven in Java script
- Visuele editor, die in de browser aanpassing en ontwerp ervaring toestaat

*Inhoud* is onderverdeeld in twee subcategorieën: inhoud van de *Portal* en *API Management inhoud*.

De *Portal-inhoud* is specifiek voor de portal en omvat:

- Pagina's: bijvoorbeeld landings pagina, API-zelf studies, blog berichten
- Media-afbeeldingen, animaties en andere op bestanden gebaseerde inhoud
- Indelingen: sjablonen, die overeenkomen met een URL en bepalen hoe pagina's worden weer gegeven
- Stijlen-waarden voor stijl definities, zoals letter typen, kleuren, randen
- Instellingen-configuratie, bijvoorbeeld favicon, meta gegevens van website

*Portal inhoud*, met uitzonde ring van media, wordt uitgedrukt als JSON-documenten.

*API Management inhoud* bevat entiteiten zoals Api's, bewerkingen, producten, abonnementen.

De portal is gebaseerd op een aangepaste Fork van het [Paperbits-Framework](https://paperbits.io/). De oorspronkelijke Paperbits-functionaliteit is uitgebreid om API Management specifieke widgets te bieden (bijvoorbeeld een lijst met Api's, een lijst met producten) en een connector voor API Management-service voor het opslaan en ophalen van inhoud.

## <a name="faq"></a>Veelgestelde vragen

In deze sectie beantwoorden we veelgestelde vragen over de nieuwe ontwikkelaars Portal, die algemeen zijn. Raadpleeg [de sectie wiki van de GitHub-opslag plaats](https://github.com/Azure/api-management-developer-portal/wiki)voor vragen die specifiek zijn voor de zelf-hostende versie.

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> hoe kan ik migreren vanuit de preview-versie van de portal?

Door de preview-versie van de ontwikkelaars portal te gebruiken, hebt u de preview-inhoud in uw API Management-service ingericht. De standaard inhoud is aanzienlijk gewijzigd in de algemeen beschik bare versie voor betere gebruikers ervaring. Het omvat ook nieuwe widgets.

Als u de beheerde versie gebruikt, moet u de inhoud van de portal opnieuw instellen door te klikken op **inhoud opnieuw instellen** in de sectie **bewerkingen** menu. Als u deze bewerking bevestigt, wordt de inhoud van de portal verwijderd en wordt de nieuwe standaard inhoud ingericht. De engine van de portal is automatisch bijgewerkt in uw API Management-service.

![Portal-inhoud opnieuw instellen](media/api-management-howto-developer-portal/reset-content.png)

Als u de zelf-hostende versie gebruikt, gebruikt u de `scripts/cleanup.bat` en `scripts/generate.bat` uit de opslag plaats GitHub om bestaande inhoud te verwijderen en nieuwe inhoud in te richten. Zorg ervoor dat u de code van uw portal van tevoren bijwerkt naar de nieuwste versie van de GitHub-opslag plaats.

Als u de inhoud van de portal niet opnieuw wilt instellen, kunt u eventueel pas beschik bare widgets gebruiken in uw pagina's. Bestaande widgets zijn automatisch bijgewerkt naar de nieuwste versie.

Als uw portal is ingericht na de aankondiging van de algemene Beschik baarheid, moet deze al de nieuwe standaard inhoud gebruiken. Aan uw zijde is geen actie vereist.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-new-developer-portal"></a>Hoe kan ik migreren van de oude ontwikkelaars Portal naar de nieuwe ontwikkelaars Portal?

Portals zijn incompatibel en u moet de inhoud hand matig migreren.

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Beschikt de nieuwe portal over alle functies van de oude Portal?

De nieuwe ontwikkelaars Portal biedt geen ondersteuning voor *toepassingen* en *problemen*. Als u *problemen* hebt gebruikt in de oude Portal en deze nodig hebt, plaatst u een opmerking in [een specifiek github-probleem](https://github.com/Azure/api-management-developer-portal/issues/122).

### <a name="has-the-old-portal-been-deprecated"></a>Is de oude Portal afgeschaft?

De oude portals voor ontwikkel aars en uitgevers zijn nu *verouderde* functies. ze ontvangen alleen beveiligings updates. Nieuwe functies worden alleen geïmplementeerd in de nieuwe ontwikkelaars Portal.

Afschaffing van de verouderde portals wordt afzonderlijk aangekondigd. Als u vragen of opmerkingen hebt, kunt u deze [in een specifiek github-probleem](https://github.com/Azure/api-management-developer-portal/issues/121)opheffen.

### <a name="how-can-i-automate-portal-deployments"></a>Hoe kan ik Portal implementaties automatiseren?

U kunt via de REST API programmatisch toegang krijgen tot de inhoud van de ontwikkelaars Portal en deze beheren, ongeacht of u een beheerde of zelf-hostende versie gebruikt.

De API wordt beschreven in [de sectie wiki van de GitHub-opslag plaats][2]. Het kan ook worden gebruikt voor het automatiseren van de migratie van Portal-inhoud tussen omgevingen, bijvoorbeeld vanuit een test omgeving naar de productie omgeving. Meer informatie over dit proces vindt u [in dit documentatie artikel](https://aka.ms/apimdocs/migrateportal) op github.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Ondersteunt de portal Azure Resource Manager sjablonen en/of is deze compatibel met API Management DevOps Resource Kit?

Nee.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Moet ik extra VNET-connectiviteit inschakelen voor de afhankelijkheden van de beheerde Portal?

Nee.

### <a name="im-getting-a-cors-error-when-using-the-interactive-console-what-should-i-do"></a>Ik krijg een CORS-fout bij het gebruik van de interactieve console. Wat zal ik doen?

De interactieve console maakt een API aan de client zijde via de browser. U kunt het CORS-probleem oplossen door [een CORS-beleid](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) toe te voegen aan uw API (s). U kunt alle para meters hand matig opgeven (bijvoorbeeld Origin als https://contoso.com) of een Joker `*` waarde gebruiken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de nieuwe ontwikkelaars portal:

- [De beheerde ontwikkelaars Portal openen en aanpassen](api-management-howto-developer-portal-customize.md)
- [Zelf-hostende versie van de Portal instellen][2]

Bladeren door andere resources:

- [GitHub-opslag plaats met de bron code][1]
- [Openbaar schema van het project][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects