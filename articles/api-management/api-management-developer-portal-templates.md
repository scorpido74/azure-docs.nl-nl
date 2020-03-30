---
title: De ontwikkelaarsportal API-beheer aanpassen met behulp van sjablonen
titleSuffix: Azure API Management
description: Meer informatie over het aanpassen van de Azure API Management-ontwikkelaarsportal met behulp van sjablonen.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 7a8c348340be143f7059ce7e64a1c66b66074a45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430777"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>De azure API Management-ontwikkelaarsportal aanpassen met behulp van sjablonen

Er zijn die manieren waarop u de ontwikkelaarsportal in Azure API Management kunt aanpassen:

* [De inhoud van statische pagina's en pagina-indelingselementen bewerken][modify-content-layout]
* [De stijlen bijwerken die worden gebruikt voor pagina-elementen in de ontwikkelaarsportal][customize-styles]
* [De sjablonen wijzigen die worden gebruikt voor pagina's die door de portal worden gegenereerd][portal-templates] (uitgelegd in deze handleiding)

Sjablonen worden gebruikt om de inhoud van door het systeem gegenereerde ontwikkelaarsportalpagina's aan te passen (bijvoorbeeld API-documenten, producten, gebruikersverificatie, enz.). Met de syntaxis van [DotLiquid](http://dotliquidmarkup.org/) en een set gelokaliseerde tekenreeksbronnen, pictogrammen en paginabesturingselementen hebt u een grote flexibiliteit om de inhoud van de pagina's naar eigen inzicht te configureren.

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Overzicht van ontwikkelaarsportalsjablonen

Het bewerken van sjablonen wordt gedaan vanuit de **portal Voor ontwikkelaars** terwijl ze zijn aangemeld als beheerder. Als u er wilt komen, opent u eerst de Azure-portal en klikt u op **De portal voor ontwikkelaars** vanaf de servicewerkbalk van uw API-beheerexemplaar.

Als u toegang wilt krijgen tot de sjablonen voor ontwikkelaarsportalen, klikt u op het pictogram aanpassen aan de linkerkant om het aanpassingsmenu weer te geven en klikt u op **Sjablonen**.

![Ontwikkelaarsportalsjablonen][api-management-customize-menu]

In de lijst met sjablonen worden verschillende categorieën sjablonen weergegeven die de verschillende pagina's in de ontwikkelaarsportal bestrijken. Elke sjabloon is anders, maar de stappen om ze te bewerken en de wijzigingen te publiceren zijn hetzelfde. Als u een sjabloon wilt bewerken, klikt u op de naam van de sjabloon.

![Ontwikkelaarsportalsjablonen][api-management-templates-menu]

Als u op een sjabloon klikt, gaat u naar de pagina van de ontwikkelaarsportal die door die sjabloon is aanpasbaar. In dit voorbeeld wordt de sjabloon **Productlijst** weergegeven. De sjabloon **Productlijst** bepaalt het gebied van het scherm dat wordt aangegeven door de rode rechthoek.

![Sjabloon voor de lijst met producten][api-management-developer-portal-templates-overview]

Sommige sjablonen, zoals de **sjablonen voor gebruikersprofielen,** passen verschillende delen van dezelfde pagina aan.

![Gebruikersprofielsjablonen][api-management-user-profile-templates]

De editor voor elke sjabloon voor ontwikkelaarsportal heeft twee secties onder aan de pagina weergegeven. Aan de linkerkant wordt het bewerkingsvenster voor de sjabloon weergegeven en rechts wordt het gegevensmodel voor de sjabloon weergegeven.

Het deelvenster Sjabloonbewerking bevat de markering die de weergave en het gedrag van de bijbehorende pagina in de ontwikkelaarsportal regelt. De markering in de sjabloon gebruikt de syntaxis [Van DotLiquid.](http://dotliquidmarkup.org/) Een populaire editor voor DotLiquid is [DotLiquid voor ontwerpers.](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers) Wijzigingen die tijdens het bewerken in de sjabloon zijn aangebracht, worden in realtime weergegeven in de browser, maar zijn pas zichtbaar voor uw klanten als u de sjabloon [opslaat](#to-save-a-template) en [publiceert.](#to-publish-a-template)

![Sjabloonmarkering][api-management-template]

Het **deelvenster Sjabloongegevens** biedt een handleiding voor het gegevensmodel voor de entiteiten die beschikbaar zijn voor gebruik in een bepaalde sjabloon. Het biedt deze handleiding door het weergeven van de live gegevens die momenteel worden weergegeven in de ontwikkelaar portal. U de sjabloondeelvensters uitbreiden door op de rechthoek in de rechterbovenhoek van het **deelvenster Sjabloongegevens** te klikken.

![Sjabloongegevensmodel][api-management-template-data]

In het vorige voorbeeld worden twee producten weergegeven in de ontwikkelaarsportal die zijn opgehaald uit de gegevens die worden weergegeven in het deelvenster **Sjabloongegevens,** zoals in het volgende voorbeeld wordt weergegeven:

```json
{
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 2,
        "ShowAll": false,
        "PageCount": 1
    },
    "Filtering": {
        "Pattern": null,
        "Placeholder": "Search products"
    },
    "Products": [
        {
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
            "Title": "Unlimited",
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
            "Terms": null,
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        }
    ]
}
```

De opmaak in de **productlijstsjabloon** verwerkt de gegevens om de gewenste uitvoer te leveren door door het verzamelen van producten te herhalen om informatie weer te geven en een link naar elk afzonderlijk product. Let `<search-control>` op `<page-control>` de elementen en elementen in de opmaak. Deze regelen de weergave van de besturingselementen voor zoeken en paging op de pagina. `ProductsStrings|PageTitleProducts`is een gelokaliseerde tekenreeksverwijzing `h2` die de koptekst voor de pagina bevat. Zie [naslaginformatie api-portalsjablonen](api-management-developer-portal-templates-reference.md)voor ontwikkelaarsportalen voor een lijst met tekenreeksbronnen, paginabesturingselementen en pictogrammen die beschikbaar zijn voor gebruik in ontwikkelaarsportalsjablonen.

```html
<search-control></search-control>
<div class="row">
    <div class="col-md-9">
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
    </div>
</div>
<div class="row">
    <div class="col-md-12">
    {% if products.size > 0 %}
    <ul class="list-unstyled">
    {% for product in products %}
        <li>
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
            {{product.description}}
        </li>
    {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    </div>
</div>
```

## <a name="to-save-a-template"></a>Een sjabloon opslaan
Als u een sjabloon wilt opslaan, klikt u op Opslaan in de sjablooneditor.

![Sjabloon opslaan][api-management-save-template]

Opgeslagen wijzigingen worden pas live in de ontwikkelaarsportal doorgevoerd als ze zijn gepubliceerd.

## <a name="to-publish-a-template"></a>Een sjabloon publiceren
Opgeslagen sjablonen kunnen afzonderlijk of allemaal samen worden gepubliceerd. Als u een afzonderlijke sjabloon wilt publiceren, klikt u op Publiceren in de sjablooneditor.

![Sjabloon publiceren][api-management-publish-template]

Klik **op Ja** om de sjabloon live te bevestigen en te maken op de ontwikkelaarsportal.

![Publicatie bevestigen][api-management-publish-template-confirm]

Als u alle momenteel niet-gepubliceerde sjabloonversies wilt publiceren, klikt u op **Publiceren** in de lijst met sjablonen. Niet-gepubliceerde sjablonen worden aangeduid met een sterretje naar aanleiding van de naam van de sjabloon. In dit voorbeeld worden de **productlijst** en **productsjablonen** gepubliceerd.

![Sjablonen publiceren][api-management-publish-templates]

Klik **op Aanpassingen publiceren** om te bevestigen.

![Publicatie bevestigen][api-management-publish-customizations]

Nieuw gepubliceerde sjablonen zijn onmiddellijk van kracht in de ontwikkelaarsportal.

## <a name="to-revert-a-template-to-the-previous-version"></a>Een sjabloon terugzetten naar de vorige versie
Als u een sjabloon wilt terugzetten naar de vorige gepubliceerde versie, klikt u op Terugzetten in de sjablooneditor.

![Sjabloon terugdraaien][api-management-revert-template]

Klik op **Ja** om te bevestigen.

![Bevestigen][api-management-revert-template-confirm]

De eerder gepubliceerde versie van een sjabloon is live in de ontwikkelaarsportal zodra de bewerking opnieuw worden teruggedraaid.

## <a name="to-restore-a-template-to-the-default-version"></a>Een sjabloon herstellen naar de standaardversie
Het herstellen van sjablonen naar de standaardversie is een proces in twee stappen. Eerst moeten de sjablonen worden hersteld en vervolgens moeten de herstelde versies worden gepubliceerd.

Als u één sjabloon wilt herstellen naar de standaardversie, klikt u op Herstellen in de sjablooneditor.

![Sjabloon terugdraaien][api-management-reset-template]

Klik op **Ja** om te bevestigen.

![Bevestigen][api-management-reset-template-confirm]

Als u alle sjablonen wilt herstellen naar de standaardversies, klikt u op **Standaardsjablonen herstellen** in de sjabloonlijst.

![Sjablonen herstellen][api-management-restore-templates]

De herstelde sjablonen moeten vervolgens afzonderlijk of in één keer worden gepubliceerd door de stappen te volgen in [Een sjabloon publiceren](#to-publish-a-template).

## <a name="next-steps"></a>Volgende stappen
Zie naslaginformatie voor ontwikkelaarsportalsjablonen, tekenreeksbronnen, pictogrammen en paginabesturingselementen voor [referentievoor referentievoor ontwerpportalsjablonen](api-management-developer-portal-templates-reference.md)voor ontwikkelaarsportalsjablonen.

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-management-console]: ./media/api-management-developer-portal-templates/api-management-management-console.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png
