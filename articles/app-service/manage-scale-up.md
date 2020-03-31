---
title: Functies en capaciteiten opschalen
description: Meer informatie over het opschalen van een app in Azure App Service. Krijg meer CPU, geheugen, schijfruimte en extra functies.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: dfd9297e80836978b8a185df3fc4659676383802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659896"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Een app opschalen in Azure App Service

In dit artikel ziet u hoe u uw app schalen in Azure App Service. Er zijn twee werkstromen voor schalen, opschalen en uitschalen, en in dit artikel wordt de scale-upworkflow uitgelegd.

* [Opschalen:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)krijg meer CPU, geheugen, schijfruimte en extra functies zoals speciale virtuele machines (VM's), aangepaste domeinen en certificaten, tijdelijke sleuven, automatisch schalen en meer. U schaalt op door de prijscategorie van het App Service-abonnement waartoe uw app behoort te wijzigen.
* [Uitschalen:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)verhoog het aantal VM-exemplaren waarop uw app wordt uitgevoerd.
  U uitschalen naar maar liefst 30 exemplaren, afhankelijk van uw prijscategorie. [App-serviceomgevingen](environment/intro.md) in **geïsoleerde** laag verhogen het aantal scale-outs verder tot 100 exemplaren. Zie Het aantal gevallen [van een instantie handmatig of automatisch schalen](../monitoring-and-diagnostics/insights-how-to-scale.md)voor meer informatie over uitschalen. Daar kom je erachter hoe je automatisch schalen gebruiken, wat is om het aantal gevallen automatisch te schalen op basis van vooraf gedefinieerde regels en schema's.

Het duurt slechts enkele seconden voordat de schaalinstellingen zijn toegepast en alle apps in uw [App Service-abonnement](../app-service/overview-hosting-plans.md)toepassen.
U hoeft uw code niet te wijzigen of uw toepassing opnieuw te implementeren.

Zie Prijsdetails van [app-serviceprijzen](https://azure.microsoft.com/pricing/details/web-sites/)voor informatie over de prijzen en functies van afzonderlijke app-serviceplannen.  

> [!NOTE]
> Voordat u een App Service-abonnement van de laag **Gratis** overschakelt, moet u eerst de [geldende bestedingslimieten](https://azure.microsoft.com/pricing/spending-limits/) voor uw Azure-abonnement verwijderen. Zie [Microsoft Azure-abonnementen][azuresubscriptions]voor het weergeven of wijzigen van opties voor uw Microsoft Azure App Service-abonnement.
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Uw prijsniveau opschalen

> [!NOTE]
> Zie [PremiumV2-laag configureren voor App-service](app-service-configure-premium-tier.md)als u wilt schalen naar **PremiumV2-laag.**
>

1. Open [Azure Portal][portal] in uw browser.

1. Selecteer op de pagina App Service-app in het linkermenu De optie **Opschalen (App Service-abonnement)**.
   
3. Kies uw laag en selecteer **Vervolgens Toepassen**. Selecteer de verschillende categorieën (bijvoorbeeld **Productie)** en bekijk ook **extra opties** om meer lagen weer te geven.
   
    ![Navigeer om uw Azure-app op te schalen.][ChooseWHP]

    Wanneer de bewerking is voltooid, ziet u een meldingspop-up met een groen succesvinkje.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Gerelateerde resources schalen
Als uw app afhankelijk is van andere services, zoals Azure SQL Database of Azure Storage, u deze bronnen afzonderlijk opschalen. Deze resources worden niet beheerd door het App Service-abonnement.

1. Selecteer op de pagina **Overzicht** voor uw app de koppeling **Resourcegroep.**
   
    ![De gerelateerde resources van uw Azure-app opschalen](./media/web-sites-scale/RGEssentialsLink.png)

2. Selecteer **in** het gedeelte Samenvatting van de groep **Resource** een resource die u wilt schalen. De volgende schermafbeelding toont een SQL Database-bron.
   
    ![Navigeren naar de pagina resourcegroep om uw Azure-app op te schalen](./media/web-sites-scale/ResourceGroup.png)

    Zie de documentatie voor het specifieke resourcetype als u de bijbehorende resource wilt opschalen. Zie Bijvoorbeeld [één databaseresources schalen in Azure SQL Database](../sql-database/sql-database-single-database-scale.md)als u één SQL-database wilt opschalen. Zie [MySQL-bronnen schalen](../mysql/concepts-pricing-tiers.md#scale-resources)als u een Azure-database voor MySQL-bronnen wilt opschalen.

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Prijsniveaus vergelijken

Zie Prijsdetails van [app-serviceprijzen](https://azure.microsoft.com/pricing/details/app-service)voor gedetailleerde informatie, zoals VM-formaten voor elke prijscategorie.

Zie [App-servicelimieten](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)voor een tabel met servicelimieten, quota's en beperkingen en ondersteunde functies in elke laag.

<a name="Next Steps"></a>

## <a name="more-resources"></a>Meer bronnen

[Het aantal exemplaren handmatig of automatisch schalen](../monitoring-and-diagnostics/insights-how-to-scale.md)  
[PremiumV2-laag configureren voor App-service](app-service-configure-premium-tier.md)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
