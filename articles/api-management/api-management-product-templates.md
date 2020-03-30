---
title: Productsjablonen in Azure API Management | Microsoft Documenten
description: Meer informatie over het aanpassen van de inhoud van de productpagina's in de Azure API Management-ontwikkelaarsportal.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 393563427e936e07315cd44b78cb793d4292b352
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243924"
---
# <a name="product-templates-in-azure-api-management"></a>Productsjablonen in Azure API-beheer

Azure API Management biedt u de mogelijkheid om de inhoud van ontwikkelaarsportalpagina's aan te passen met behulp van een reeks sjablonen die de inhoud ervan configureren. Met behulp van [DotLiquid](http://dotliquidmarkup.org/) syntaxis en de editor van uw keuze, zoals [DotLiquid voor ontwerpers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), en een meegeleverde set van gelokaliseerde [string bronnen,](api-management-template-resources.md#strings) [Glyph middelen,](api-management-template-resources.md#glyphs)en [Pagina besturingselementen](api-management-page-controls.md), hebt u grote flexibiliteit om de inhoud van de pagina's te configureren zoals u dat wilt met behulp van deze sjablonen.  
  
 Met de sjablonen in deze sectie u de inhoud van de productpagina's aanpassen in de ontwikkelaarsportal.  
  
-   [Productlijst](#ProductList)  
  
-   [Product](#Product)  
  
> [!NOTE]
>  Standaardsjablonen voor voorbeelden zijn opgenomen in de volgende documentatie, maar kunnen worden gewijzigd als gevolg van continue verbeteringen. U de standaardstandaardsjablonen in de ontwikkelaarsportal bekijken door naar de gewenste afzonderlijke sjablonen te navigeren. Zie [De API Management-ontwikkelaarsportal aanpassen met sjablonen](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)voor meer informatie over het werken met sjablonen.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="product-list"></a><a name="ProductList"></a>Productlijst  
 Met de sjabloon **Productlijst** u de hoofdtekst van de productlijstpagina aanpassen in de ontwikkelaarsportal.  
  
 ![Productenlijst](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>Standaardsjabloon  
  
```xml  
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
  
### <a name="controls"></a>Besturingselementen  
 De `Product list` sjabloon kan de volgende [paginabesturingselementen](api-management-page-controls.md)gebruiken .  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
-   [zoekbesturingselement](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Gegevensmodel  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|Zoekresultaten oproepen|[Paging](api-management-template-data-model-reference.md#Paging) entiteit.|De paging informatie voor de producten collectie.|  
|Filteren|[Entiteit filteren.](api-management-template-data-model-reference.md#Filtering)|De filtergegevens voor de lijstpagina met producten.|  
|Producten|Verzameling [Product](api-management-template-data-model-reference.md#Product) van productentiteiten.|De producten zichtbaar voor de huidige gebruiker.|  
  
### <a name="sample-template-data"></a>Voorbeeldsjabloongegevens  
  
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
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
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
  
##  <a name="product"></a><a name="Product"></a>Product  
 Met **de productsjabloon** u de hoofdtekst van de productpagina aanpassen in de ontwikkelaarsportal.  
  
 ![Productpagina van ontwikkelaarsportal](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>Standaardsjabloon  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>Besturingselementen  
 De `Product list` sjabloon kan de volgende [paginabesturingselementen](api-management-page-controls.md)gebruiken .  
  
-   [knop abonneren](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Gegevensmodel  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|Product|[Product](api-management-template-data-model-reference.md#Product)|Het opgegeven product.|  
|IsDeveloperSubscribed|booleaans|Of de huidige gebruiker is geabonneerd op dit product.|  
|AbonnementStaat|getal|De staat van het abonnement. Mogelijke staten zijn:<br /><br /> -   `0 - suspended`– het abonnement is geblokkeerd en de abonnee kan geen API's van het product bellen.<br />-   `1 - active`– het abonnement actief is.<br />-   `2 - expired`– het abonnement de vervaldatum heeft bereikt en is gedeactiveerd.<br />-   `3 - submitted`– de abonnementsaanvraag is gedaan door de ontwikkelaar, maar is nog niet goedgekeurd of afgewezen.<br />-   `4 - rejected`– het abonnementsverzoek is geweigerd door een beheerder.<br />-   `5 - cancelled`– het abonnement is opgezegd door de ontwikkelaar of beheerder.|  
|Limieten|matrix|Deze eigenschap is afgeschaft en mag niet worden gebruikt.|  
|GedelegeerdAbonnementingeschakeld|booleaans|Of [delegatie](https://azure.microsoft.com/documentation/articles/api-management-howto-setup-delegation/) is ingeschakeld voor dit abonnement.|  
|GedelegeerdabonnementUrl|tekenreeks|Als de overdracht is ingeschakeld, wordt de URL van het gedelegeerdabonnement weergegeven.|  
|Isovereengekomen|booleaans|Als het product voorwaarden heeft, of de huidige gebruiker heeft ingestemd met de voorwaarden.|  
|Abonnementen|Verzameling van overzichtsentiteiten [voor](api-management-template-data-model-reference.md#SubscriptionSummary) abonnementen.|De abonnementen op het product.|  
|Apis|Verzameling van [API-entiteiten.](api-management-template-data-model-reference.md#API)|De API's in dit product.|  
|CannotAddBecauseAbonnementnumberlimitbereikt|booleaans|Of de huidige gebruiker in aanmerking komt om zich te abonneren op dit product met betrekking tot de abonnementslimiet.|  
|CannotAddBecauseMultipleSubscriptionsNotA toegestaan|booleaans|Of de huidige gebruiker in aanmerking komt om zich te abonneren op dit product met betrekking tot meerdere abonnementen worden toegestaan of niet.|  
  
### <a name="sample-template-data"></a>Voorbeeldsjabloongegevens  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>Volgende stappen
Zie [De API Management-ontwikkelaarsportal aanpassen met sjablonen](api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.
