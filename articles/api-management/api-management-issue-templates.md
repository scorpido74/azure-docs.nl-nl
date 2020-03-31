---
title: Probleemsjablonen in Azure API-beheer | Microsoft Documenten
description: Meer informatie over het aanpassen van de inhoud van de uitgiftepagina's in de ontwikkelaarsportal in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 47da4bb2-426e-4e53-8fa7-214ee2e3ab37
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 1dac90053797caf66af79e458b9dbb95b682cd17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249579"
---
# <a name="issue-templates-in-azure-api-management"></a>Probleemsjablonen in Azure API-beheer
Azure API Management biedt u de mogelijkheid om de inhoud van ontwikkelaarsportalpagina's aan te passen met behulp van een reeks sjablonen die de inhoud ervan configureren. Met behulp van [DotLiquid](http://dotliquidmarkup.org/) syntaxis en de editor van uw keuze, zoals [DotLiquid voor ontwerpers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), en een meegeleverde set van gelokaliseerde [string bronnen,](api-management-template-resources.md#strings) [Glyph middelen,](api-management-template-resources.md#glyphs)en [Pagina besturingselementen](api-management-page-controls.md), hebt u grote flexibiliteit om de inhoud van de pagina's te configureren zoals u dat wilt met behulp van deze sjablonen.  
  
 Met de sjablonen in deze sectie u de inhoud van de uitgiftepagina's aanpassen in de ontwikkelaarsportal.  
  
-   [Lijst met problemen](#IssueList)  
  
> [!NOTE]
>  Standaardsjablonen voor voorbeelden zijn opgenomen in de volgende documentatie, maar kunnen worden gewijzigd als gevolg van continue verbeteringen. U de standaardstandaardsjablonen in de ontwikkelaarsportal bekijken door naar de gewenste afzonderlijke sjablonen te navigeren. Zie [De API Management-ontwikkelaarsportal aanpassen met sjablonen](api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="issue-list"></a><a name="IssueList"></a>Lijst met problemen  
 Met de sjabloon **Probleemlijst** u de hoofdtekst van de pagina met de uitgiftelijst aanpassen in de ontwikkelaarsportal.  
  
 ![Ontwikkelaarsportal voor probleemlijst](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "APIM-lijst-ontwikkelaarsportal")  
  
### <a name="default-template"></a>Standaardsjabloon  
  
```xml
<div class="row">
  <div class="col-md-9">
    <h2>{% localized "IssuesStrings|WebIssuesIndexTitle" %}</h2>
  </div>
</div>
<div class="row">
  <div class="col-md-12">
    {% if issues.size > 0 %}
    <ul class="list-unstyled">
      {% capture reportedBy %}{% localized "IssuesStrings|WebIssuesStatusReportedBy" %}{% endcapture %}
      {% assign replaceString0 = '{0}' %}
      {% assign replaceString1 = '{1}' %}
      {% for issue in issues %}
      <li>
        <h3>
          <a href="/issues/{{issue.id}}">{{issue.title}}</a>
        </h3>
        <p>{{issue.description}}</p>
        <em>
          {% capture state %}{{issue.issueState}}{% endcapture %}
          {% capture devName %}{{issue.subscriptionDeveloperName}}{% endcapture %}
          {% capture str1 %}{{ reportedBy | replace : replaceString0, state }}{% endcapture %}
          {{ str1 | replace : replaceString1, devName }}
          <span class="UtcDateElement">{{ issue.reportedOn | date: "r" }}</span>
        </em>
      </li>
      {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    {% if canReportIssue %}
    <a class="btn btn-primary" id="createIssue" href="/Issues/Create">{% localized "IssuesStrings|WebIssuesReportIssueButton" %}</a>
    {% elsif isAuthenticated %}
    <hr />
    <p>{% localized "IssuesStrings|WebIssuesNoActiveSubscriptions" %}</p>
    {% else %}
    <hr />
    <p>
      {% capture signIntext %}{% localized "IssuesStrings|WebIssuesNotSignin" %}{% endcapture %}
      {% capture link %}<a href="/signin">{% localized "IssuesStrings|WebIssuesSignIn" %}</a>{% endcapture %}
      {{ signIntext | replace : replaceString0, link }}
    </p>
    {% endif %}
  </div>
</div>
```
  
### <a name="controls"></a>Besturingselementen  
 De `Issue list` sjabloon kan de volgende [paginabesturingselementen](api-management-page-controls.md)gebruiken .  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Gegevensmodel  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`Issues`|Verzameling van [entiteiten voor uitgifte.](api-management-template-data-model-reference.md#Issue)|De problemen zichtbaar voor de huidige gebruiker.|  
|`Paging`|[Paging](api-management-template-data-model-reference.md#Paging) entiteit.|De paging informatie voor de aanvragen verzamelen.|  
|`IsAuthenticated`|booleaans|Of de huidige gebruiker is aangemeld bij de ontwikkelaarsportal.|  
|`CanReportIssues`|booleaans|Of de huidige gebruiker machtigingen heeft om een probleem in te dienen.|  
|`Search`|tekenreeks|Deze eigenschap is afgeschaft en mag niet worden gebruikt.|  
  
### <a name="sample-template-data"></a>Voorbeeldsjabloongegevens  
  
```json
{
    "Issues": [
        {
            "Id": "5702b68bb16653124c8f9ba7",
            "ApiId": "570275f1b16653124c8f9ba3",
            "Title": "I couldn't figure out how to connect my application to the API",
            "Description": "I'm having trouble connecting my application to the backend API.",
            "SubscriptionDeveloperName": "Clayton",
            "IssueState": "Proposed",
            "ReportedOn": "2016-04-04T18:46:35.64",
            "Comments": null,
            "Attachments": null,
            "Services": null
        }
    ],
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 1,
        "ShowAll": false,
        "PageCount": 1
    },
    "IsAuthenticated": true,
    "CanReportIssue": true,
    "Search": null
}
```

## <a name="next-steps"></a>Volgende stappen
Zie [De API Management-ontwikkelaarsportal aanpassen met sjablonen](api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.
