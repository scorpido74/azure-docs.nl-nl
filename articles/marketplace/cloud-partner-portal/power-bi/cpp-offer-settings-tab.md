---
title: Aanbiedings instellingen voor een Power BI app-aanbieding | Azure Marketplace
description: Configureer aanbiedings instellingen voor een Power BI app-aanbieding voor de Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: ddedc15b41b2b163f85f5443275daf9fbb56bac8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143032"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Tabblad instellingen van Power BI-apps-aanbod

>[!Important]
>Vanaf 13 april 2020 gaan we het beheer van uw Power BI app-aanbiedingen naar het partner centrum verplaatsen. Na de migratie maakt en beheert u uw aanbiedingen in partner centrum. Volg de instructies in [Power bi overzicht](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) van het maken van apps voor het beheren van uw gemigreerde aanbiedingen.

Wanneer u de **nieuwe aanbiedings** pagina voor service-apps opent, ziet u eerst het tabblad Instellingen voor de **aanbieding** . U geeft de primaire id's en de naam voor uw aanbieding op dit tabblad. Een asterisk (*) geeft een vereist veld aan.

![Tabblad voor aanbiedingsinstellingen](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Velden voor aanbiedings instellingen 

Op het tabblad Instellingen voor de **aanbieding** moet u gegevens invoeren in de volgende vereiste velden. Vereiste velden worden gedicteerd door een asterisk (*).

|  Veld        |  Beschrijving                                                               |
|---------------|----------------------------------------------------------------------------|
| **Aanbiedings-id\***  | Een unieke id (binnen een Publisher-profiel) voor de aanbieding. Deze id is zichtbaar in product-Url's, Azure Resource Manager sjablonen en facturerings rapporten. De maximale lengte is 50 tekens. De naam mag alleen kleine letters en streepjes (-) bevatten. Het mag niet eindigen met een streepje. Deze id kan niet worden gewijzigd nadat een aanbieding Live is. Als contoso een aanbieding met een aanbiedings `sample-SvcApp`-id publiceert, wordt de APPSOURCE- `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`URL toegewezen aan de aanbieding.      |
| **Uitgever\*** | De unieke id van uw organisatie in [AppSource](https://appsource.microsoft.com). Al uw aanbiedingen moeten worden gekoppeld aan uw uitgevers-ID. Deze waarde kan niet worden gewijzigd nadat de aanbieding is opgeslagen.                         |
| **Naam\***      | Een weergave naam voor uw aanbieding. Deze naam wordt weer gegeven in AppSource en op de Cloud Partner-portal. De maximale lengte is 50 tekens. Gebruik een merk naam die herkenbaar is voor uw product. Neem hier de naam van uw organisatie niet op, tenzij de app met die naam in de handel is. Als u deze aanbieding op andere websites en publicaties levert, gebruikt u dezelfde naam in alle publicaties.    <br/>Als u een aanbieding vrijgeeft tijdens de preview-periode voor Power BI-apps, `(Preview)` voegt u de teken reeks toe aan het einde van de naam `Sample Scv App (Preview)`van uw toepassing, zoals:. |
|     |     |


## <a name="next-steps"></a>Volgende stappen

Op het volgende tabblad kunt u [technische gegevens](./cpp-technical-info-tab.md) voor uw aanbieding opgeven.
