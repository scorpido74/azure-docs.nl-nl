---
title: Azure API Management-paginabesturingselementen | Microsoft Documenten
description: Meer informatie over de paginabesturingselementen die beschikbaar zijn voor gebruik in ontwikkelaarsportalsjablonen in Azure API-beheer.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: ff608cac9786ebc0d3908ce4d88fb501e621be6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244015"
---
# <a name="azure-api-management-page-controls"></a>Paginabesturingselementen voor Azure API-beheer
Azure API Management biedt de volgende besturingselementen voor gebruik in de ontwikkelaarsportalsjablonen.  
  
Als u een besturingselement wilt gebruiken, plaatst u deze op de gewenste locatie in de sjabloon voor de ontwikkelaarsportal. Sommige besturingselementen, zoals het besturingselement [voor app-acties,](#app-actions) hebben parameters, zoals in het volgende voorbeeld wordt weergegeven:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
De waarden voor de parameters worden doorgegeven als onderdeel van het gegevensmodel voor de sjabloon. In de meeste gevallen u gewoon plakken in het meegeleverde voorbeeld voor elk besturingselement voor het correct te laten werken. Voor meer informatie over de parameterwaarden ziet u de sectie gegevensmodel voor elke sjabloon waarin een besturingselement kan worden gebruikt.  

Zie [De API Management-ontwikkelaarsportal aanpassen met sjablonen](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)voor meer informatie over het werken met sjablonen.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Besturingselementen voor sjabloonpagina voor ontwikkelaarsportalen  
  
-   [app-acties](#app-actions)  
-   [basis-signin](#basic-signin)  
-   [paging-control](#paging-control)  
-   [Providers](#providers)  
-   [zoekbesturingselement](#search-control)  
-   [Aanmelden](#sign-up)  
-   [knop abonneren](#subscribe-button)  
-   [abonnement-annuleren](#subscription-cancel)  
  
##  <a name="app-actions"></a><a name="app-actions"></a>app-acties  
 Het `app-actions` besturingselement biedt een gebruikersinterface voor interactie met toepassingen op de gebruikersprofielpagina in de ontwikkelaarsportal.  
  
 ![besturingselement voor&#45;van apps](./media/api-management-page-controls/APIM-app-actions-control.png "APIM-app-actiesbesturingselement")  
  
### <a name="usage"></a>Gebruik  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parameters  
  
|Parameter|Beschrijving|  
|---------------|-----------------|  
|appId|De ID van de aanvraag.|  
  
### <a name="developer-portal-templates"></a>Ontwikkelaarsportalsjablonen  
 Het `app-actions` besturingselement kan worden gebruikt in de volgende sjablonen voor ontwikkelaarsportalen:  
  
-   [Toepassingen](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a><a name="basic-signin"></a>basis-signin  
 Het `basic-signin` besturingselement biedt een besturingselement voor het verzamelen van aanmeldingsgegevens van gebruikers op de aanmeldingspagina in de ontwikkelaarsportal.  
  
 ![basis&#45;signin control](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM basis-signin control")  
  
### <a name="usage"></a>Gebruik  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parameters  
 Geen.  
  
### <a name="developer-portal-templates"></a>Ontwikkelaarsportalsjablonen  
 Het `basic-signin` besturingselement kan worden gebruikt in de volgende sjablonen voor ontwikkelaarsportalen:  
  
-   [Aanmelden](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a><a name="paging-control"></a>paging-control  
 De `paging-control` biedt paging-functionaliteit op pagina's van ontwikkelaarsportalen die een lijst met items weergeven.  
  
 ![paging controle](./media/api-management-page-controls/APIM-paging-control.png "APIM-paging-besturingselement")  
  
### <a name="usage"></a>Gebruik  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parameters  
 Geen.  
  
### <a name="developer-portal-templates"></a>Ontwikkelaarsportalsjablonen  
 Het `paging-control` besturingselement kan worden gebruikt in de volgende sjablonen voor ontwikkelaarsportalen:  
  
-   [API-lijst](api-management-api-templates.md#APIList)  
  
-   [Lijst met problemen](api-management-issue-templates.md#IssueList)  
  
-   [Productlijst](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a><a name="providers"></a>Providers  
 Het `providers` besturingselement biedt een controle voor de selectie van verificatieproviders in de aanmeldingspagina in de ontwikkelaarsportal.  
  
 ![providers controleren](./media/api-management-page-controls/APIM-providers-control.png "APIM-providers beheren")  
  
### <a name="usage"></a>Gebruik  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parameters  
 Geen.  
  
### <a name="developer-portal-templates"></a>Ontwikkelaarsportalsjablonen  
 Het `providers` besturingselement kan worden gebruikt in de volgende sjablonen voor ontwikkelaarsportalen:  
  
-   [Aanmelden](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a><a name="search-control"></a>zoekbesturingselement  
 De `search-control` biedt zoekfunctionaliteit op pagina's van ontwikkelaarsportalen die een lijst met items weergeven.  
  
 ![zoekbesturingselement](./media/api-management-page-controls/APIM-search-control.png "APIM-zoekbesturingselement")  
  
### <a name="usage"></a>Gebruik  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parameters  
 Geen.  
  
### <a name="developer-portal-templates"></a>Ontwikkelaarsportalsjablonen  
 Het `search-control` besturingselement kan worden gebruikt in de volgende sjablonen voor ontwikkelaarsportalen:  
  
-   [API-lijst](api-management-api-templates.md#APIList)  
  
-   [Productlijst](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a><a name="sign-up"></a>Aanmelden  
 Het `sign-up` besturingselement biedt een controle voor het verzamelen van gebruikersprofielinformatie op de aanmeldingspagina in de ontwikkelaarsportal.  
  
 ![aanmelden&#45;](./media/api-management-page-controls/APIM-sign-up-control.png "Besturingselement APIM-aanmelding")  
  
### <a name="usage"></a>Gebruik  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parameters  
 Geen.  
  
### <a name="developer-portal-templates"></a>Ontwikkelaarsportalsjablonen  
 Het `sign-up` besturingselement kan worden gebruikt in de volgende sjablonen voor ontwikkelaarsportalen:  
  
-   [Aanmelden](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a><a name="subscribe-button"></a>knop abonneren  
 Het `subscribe-button` biedt een controle voor het abonneren van een gebruiker op een product.  
  
 ![u abonneren&#45;knopbesturingselement](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM-besturingselement voor abonneren en knoppen")  
  
### <a name="usage"></a>Gebruik  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parameters  
 Geen.  
  
### <a name="developer-portal-templates"></a>Ontwikkelaarsportalsjablonen  
 Het `subscribe-button` besturingselement kan worden gebruikt in de volgende sjablonen voor ontwikkelaarsportalen:  
  
-   [Product](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a><a name="subscription-cancel"></a>abonnement-annuleren  
 Het `subscription-cancel` besturingselement biedt een controle voor het annuleren van een abonnement op een product op de gebruikersprofielpagina in de ontwikkelaarsportal.  
  
 ![abonnement&#45;het besturingselement opzeggen](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM-abonnement opzeggen")  
  
### <a name="usage"></a>Gebruik  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parameters  
  
|Parameter|Beschrijving|  
|---------------|-----------------|  
|subscriptionId|De ID van het abonnement op te zeggen.|  
|cancelUrl|Het abonnement annuleert URL.|  
  
### <a name="developer-portal-templates"></a>Ontwikkelaarsportalsjablonen  
 Het `subscription-cancel` besturingselement kan worden gebruikt in de volgende sjablonen voor ontwikkelaarsportalen:  
  
-   [Product](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Volgende stappen
Zie [De API Management-ontwikkelaarsportal aanpassen met sjablonen](api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.
