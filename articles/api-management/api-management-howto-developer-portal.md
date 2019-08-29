---
title: De nieuwe ontwikkelaars Portal openen en aanpassen-Azure API Management | Microsoft Docs
description: Meer informatie over het gebruik van de nieuwe ontwikkelaars Portal in API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: da75ca43a2576e3214d4b67f9eb61c7bad3bd5cc
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073517"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>De nieuwe ontwikkelaars Portal openen en aanpassen in azure API Management

In dit artikel wordt beschreven hoe u toegang krijgt tot de nieuwe Azure API Management-ontwikkelaars Portal. U wordt begeleid bij het uitvoeren van de Visual Editor-ervaring: het toevoegen en bewerken van inhoud, en het aanpassen van het uiterlijk van de website.

![Nieuwe API Management ontwikkelaars Portal](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a> Vereisten

- Voltooi de volgende quickstart: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
- Importeer en publiceer een Azure API Management-exemplaar. Zie [importeren en publiceren](import-and-publish.md)voor meer informatie.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> De nieuwe ontwikkelaars Portal is momenteel beschikbaar als preview-versie.

## <a name="managed-vs-self-hosted"></a>Beheerde en zelf-hostende versies

U kunt uw ontwikkelaars Portal op twee manieren bouwen:

- **Beheerde versie** : door de portal te bewerken en aan te passen, die in uw API Management-exemplaar is ingebouwd en toegankelijk `<your-api-management-instance-name>.developer.azure-api.net`is via de URL.
- **Zelf-hostende versie** : door uw portal buiten een API Management-exemplaar te implementeren en zelf te hosten. Met deze aanpak kunt u de code base van de portal bewerken en de meegeleverde kern functionaliteit uitbreiden. Raadpleeg de [github-opslag plaats met de bron code van de portal][1]voor meer informatie en instructies.

## <a name="managed-access"></a>Toegang tot de beheerde versie van de portal

Volg de onderstaande stappen om toegang te krijgen tot de beheerde versie van de portal.

1. Ga naar uw API Management service-exemplaar in de Azure Portal.
1. Klik op de knop **Nieuw ontwikkelaars Portal (preview)** in de bovenste navigatie balk. Er wordt een nieuw browser tabblad met een beheer versie van de portal geopend. Als u de portal voor de eerste keer opent, wordt de standaard inhoud automatisch ingericht.

## <a name="managed-tutorial"></a>De beheerde versie van de portal bewerken en aanpassen

In de onderstaande video laten we zien hoe u de inhoud van de portal kunt bewerken, het uiterlijk van de website kunt aanpassen en de wijzigingen publiceert.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="faq"></a>Veelgestelde vragen

In deze sectie wordt geantwoord op veelgestelde vragen over de nieuwe ontwikkelaars Portal, die algemeen zijn. Raadpleeg [de sectie wiki van de GitHub-opslag plaats](https://github.com/Azure/api-management-developer-portal/wiki)voor vragen die specifiek zijn voor de zelf-hostende versie.

### <a name="how-can-i-migrate-content-from-the-old-developer-portal-to-the-new-one"></a>Hoe kan ik inhoud migreren van de oude ontwikkelaars Portal naar de nieuwe?

U kunt dit niet doen. De portals zijn incompatibel.

### <a name="when-will-the-portal-become-generally-available"></a>Wanneer gaat de portal algemeen beschikbaar worden?

De portal is momenteel beschikbaar als preview-versie en is algemeen verkrijgbaar aan het einde van het kalender jaar (2019). De preview-versie mag niet worden gebruikt voor productie doeleinden.

### <a name="will-the-old-portal-be-deprecated"></a>Wordt de oude Portal afgeschaft?

Ja, het wordt afgeschaft nadat het nieuwe item algemeen beschikbaar wordt. Als u problemen hebt, kunt u ze verhogen [in een speciaal github-probleem](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Beschikt de nieuwe portal over alle functies van de oude Portal?

Het doel van algemene Beschik baarheid is een functie pariteit op basis van scenario's te bieden met de oude Portal. Tot dat kan de preview-versie mogelijk geen geselecteerde onderdelen bevatten.

De uitzonde ringen zijn de *toepassingen* en *problemen* van de oude Portal, die niet beschikbaar zijn in de nieuwe portal. Als u *problemen* in de oude Portal gebruikt en deze nodig hebt, plaatst u een opmerking in [een specifiek github-probleem](https://github.com/Azure/api-management-developer-portal/issues/122).

### <a name="ive-found-bugs-andor-id-like-to-request-a-feature"></a>Ik heb fouten gevonden en/of ik wil een functie aanvragen.

Goed gedaan. U kunt ons feedback geven, een functie aanvraag indienen of een fouten rapport verzenden via [de sectie problemen van de GitHub-opslag plaats](https://github.com/Azure/api-management-developer-portal/issues). U bent er ook zeker van dat u feedback hebt over de problemen die met het `community` label zijn gemarkeerd.

### <a name="i-want-to-move-the-content-of-the-new-portal-between-environments-how-can-i-do-that-and-do-i-need-to-go-with-the-self-hosted-version"></a>Ik wil de inhoud van de nieuwe portal verplaatsen tussen omgevingen. Hoe kan ik dat doen en moet ik met de zelf-hostende versie gaan?

U kunt dit doen in beide Portal versies, beheerd en zelf-hosten. De nieuwe ontwikkelaars Portal biedt ondersteuning voor het extra heren van inhoud via de beheer-API van uw API Management service. De Api's worden beschreven [in de sectie wiki van de GitHub-opslag plaats](https://github.com/Azure/api-management-developer-portal/wiki/). We hebben ook [een script](https://github.com/Azure/api-management-developer-portal/blob/master/scripts/migrate.bat)geschreven. Dit kan u helpen om aan de slag te gaan.

We werken nog steeds aan het uitlijnen van dit proces met de API Management DevOps Resource Kit.

### <a name="how-can-i-select-a-layout-when-creating-a-new-page"></a>Hoe kan ik een *indeling* selecteren bij het maken van een nieuwe *pagina*?

Een *indeling* wordt toegepast op een pagina door te voldoen aan de URL-sjabloon van de *pagina* . *Indeling* met een URL-sjabloon van `/wiki/*` wordt bijvoorbeeld toegepast op elke *pagina* met `/wiki/` het segment: `/wiki/getting-started`, `/wiki/styles`, enzovoort.

### <a name="why-doesnt-the-interactive-developer-console-work"></a>Waarom werkt de interactieve ontwikkelaars console niet?

Het is waarschijnlijk gerelateerd aan CORS. De interactieve console maakt een API aan de client zijde via de browser. U kunt het CORS-probleem oplossen door [een CORS-beleid](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) toe te voegen aan uw API (s). U kunt alle para meters hand matig opgeven (bijvoorbeeld oorsprong als https://contoso.com) of een Joker teken `*` waarde gebruiken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de nieuwe ontwikkelaars portal:

- [GitHub-opslag plaats met de bron code][1]
- [Instructies voor het zelf hosten van de portal][2]
- [Openbaar schema van het project][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects