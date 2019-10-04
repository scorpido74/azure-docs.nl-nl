---
title: Gebruik en geschatte kosten in Azure Monitor bewaken
description: Overzicht van het proces van het gebruik van de pagina Azure Monitor-gebruik en geschatte kosten
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 787618b59cd18dd4c38892ddf0861808211671cb
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936629"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Gebruik en geschatte kosten in Azure Monitor bewaken

> [!NOTE]
> In dit artikel wordt beschreven hoe u het gebruik en de geschatte kosten kunt weer geven in meerdere Azure-bewakings functies voor verschillende prijs modellen. Verwante artikelen voor specifieke onderdelen van Azure Monitor zijn onder andere:
> - [Gebruik en kosten beheren met Azure monitor logboeken](manage-cost-storage.md) beschrijft hoe u uw kosten kunt bepalen door de Bewaar periode van uw gegevens te wijzigen en hoe u uw gegevens gebruik kunt analyseren en waarschuwen.
> - [Gebruik en kosten beheren voor Application Insights](../../azure-monitor/app/pricing.md) beschrijft hoe u het gebruik van gegevens in Application Insights kunt analyseren.

In de hub Monitor van het Azure-portal, de **gebruik en geraamde kosten** pagina leest u het gebruik van voor kernbewaking van functies zoals [waarschuwingen, metrische gegevens, meldingen](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics ](https://azure.microsoft.com/pricing/details/log-analytics/), en [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Dit omvat ook gebruik van logboekanalyse die zijn aangeschaft via de inzichten en analyses bieden voor klanten op de abonnementen beschikbaar voor April 2018.

Op deze pagina kunnen gebruikers bekijken hun Resourcegebruik voor de afgelopen 31 dagen, bij elkaar opgeteld per abonnement. Drill-modules weergeven trends in gebruik gedurende de periode van 31 dagen. Grote hoeveelheden gegevens, moeten afkomstig zijn samen voor deze schatting, dus mogelijk als de pagina wordt geladen.

In dit voorbeeld ziet u bewaking wordt gebruikt en een schatting van de resulterende kosten:

![Gebruik en geschatte kosten van het portal-schermafbeelding](./media/usage-estimated-costs/001.png)

Selecteer de koppeling in de maandelijkse gebruik kolom openen van een diagram met trends in gebruik gedurende de afgelopen 31 dagen per periode:

![Opgenomen per knooppunt balken grafieken schermafbeelding](./media/usage-estimated-costs/002.png)

Hier is een andere soortgelijke gebruik en kosten samenvatting. In dit voorbeeld ziet u het abonnement in de nieuwe April 2018 verbruik gebaseerde prijsmodel. Houd er rekening mee het ontbreken van een facturering op basis van een knooppunt. Gegevensopname en retentie voor Log Analytics en Application Insights worden nu voor een nieuwe meter met algemene gerapporteerd.

![Gebruik en geschatte kosten van het portal schermafbeelding - prijzen voor April 2018](./media/usage-estimated-costs/003.png)

## <a name="pricing-model"></a>Prijsmodel

In April 2018, een [nieuwe bewakingsscenario prijsmodel werd uitgebracht](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Deze functie biedt een Cloud vriendelijke prijs op basis van verbruik (' betalen naar gebruik '). U betaalt alleen voor wat u, zonder verbintenissen op basis van een knooppunt gebruikt. Details van het nieuwe prijsmodel zijn beschikbaar voor [waarschuwingen, metrische gegevens, meldingen](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) en [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Naast het model voor betalen naar gebruik, hebben we in september 2019 capaciteits reserveringen toegevoegd voor Log Analytics waarmee u Maxi maal 25% kunt besparen op basis van de betalen naar gebruik-prijs. Met de prijzen voor capaciteits reservering kunt u een reserve ring kopen vanaf 100 GB per dag. Elk gebruik boven het reserverings niveau wordt gefactureerd op basis van het betalen naar gebruik-tarief. Meer [informatie](https://azure.microsoft.com/pricing/details/monitor/) over de prijzen voor capaciteits reservering.

Voor onboarding van klanten naar Log Analytics of Application Insights na 2 April 2018, is het nieuwe prijsmodel de enige optie. Voor klanten die al gebruikmaken van deze services, verplaatsen naar het nieuwe prijsmodel is optioneel.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Beoordeling van de impact van het nieuwe prijsmodel
Het nieuwe prijsmodel, andere invloed heeft op elke klant op basis van hun bewaking gebruikspatronen. Voor klanten die Log Analytics of Application Insights voor 2 April 2018, zijn de **gebruik en geschatte kosten** pagina in Azure Monitor maakt een schatting van eventuele wijzigingen in de kosten als ze worden verplaatst naar het nieuwe prijsmodel. Het biedt de manier waarop een abonnement verplaatsen naar het nieuwe model. Voor de meeste klanten is het nieuwe prijsmodel voorkeur. Voor klanten met gebruikspatronen vooral hoog gegevens of in de regio's voor hogere kosten, kan dit niet het geval zijn.

Om te zien van een schatting van uw kosten voor de abonnementen die u hebt gekozen op de **gebruik en geraamde kosten** pagina, selecteert u de blauwe voortgangsbalk aan de bovenkant van de pagina. Het is raadzaam te doen dit een abonnement op een tijdstip, omdat die het niveau waarop het nieuwe prijsmodel kan worden vastgesteld.

![Controle op het gebruik en geschatte kosten in de nieuwe prijzen model-schermafbeelding](./media/usage-estimated-costs/004.png)

De nieuwe pagina ziet u een vergelijkbare versie van de vorige pagina met een groene banner:

![Controle op het gebruik en geschatte kosten in de huidige prijzen model-schermafbeelding](./media/usage-estimated-costs/005.png)

De pagina bevat ook een andere set meters die met het nieuwe prijsmodel overeenkomen. Deze lijst is een voorbeeld:

- Inzicht en Analytics\Overage per knooppunt
- Inzicht en Analytics\Included per knooppunt
- Toepassing Insights\Basic overschrijding gegevens
- Toepassingsgegevens Insights\Included

Het nieuwe prijsmodel geen toewijzing voor inbegrepen gegevens op basis van een knooppunt. Daarom deze gegevensopname meters worden gecombineerd tot een nieuwe algemene gegevensopname meter met de naam **gedeeld Services\Data opname**. 

Er is een andere wijziging aan gegevens die zijn opgenomen in Log Analytics of Application Insights in regio's met een hogere kosten. Gegevens voor deze regio's van hoge kosten worden weergegeven met de nieuwe regionale meters. Een voorbeeld is **gegevensopname (VS-West-Centraal)** .

> [!NOTE]
> De geschatte kosten doen geen factor in het accountniveau per knooppunt rechten van de Operations Management Suite (OMS)-abonnement per abonnement. Raadpleeg in dat geval vertegenwoordiger voor uw account voor een uitgebreidere bespreking van het nieuwe prijsmodel.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nieuwe prijzen model en de rechten voor Operations Management Suite-abonnement

Klanten die hebben gekocht van Microsoft Operations Management Suite E1 en E2 komen in aanmerking voor per knooppunt gegevensopname rechten voor [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) en [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Voor het ontvangen van deze rechten voor Log Analytics-werkruimten of Application Insights-resources in een bepaald abonnement: 

- Het prijsmodel van het abonnement moet blijven in het model pre-April 2018.
- Log Analytics-werkruimten moeten de "Per knooppunt (OMS)" prijscategorie gebruiken.
- Application Insights-resources, moeten het prijsplan 'bedrijf' gebruiken.

Afhankelijk van het aantal knooppunten van het pakket die uw organisatie heeft aangeschaft, verplaatsen van sommige abonnementen op het nieuwe prijsmodel zijn mogelijk nuttig, maar dit vereist een zorgvuldige afweging. In het algemeen is het raadzaam dat u gewoon blijven in de pre-April 2018-model, zoals hierboven beschreven.

> [!WARNING]
> Als uw organisatie heeft aangeschaft, de Microsoft Operations Management Suite E1 en E2, is het beste om te houden van uw abonnementen in het prijsmodel van pre-April 2018. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Verandert wanneer u naar het nieuwe prijsmodel overstapt

Het nieuwe prijsmodel vereenvoudigt de Log Analytics en Application Insights prijsopties voor slechts één laag (of -abonnement). Een abonnement verplaatsen naar de nieuwe prijzen model wordt:

- Wijzig de prijscategorie voor elke Log Analytics naar een nieuwe Per GB-laag (met de naam 'pergb2018' in Azure Resource Manager)
- Een Application Insights-resources in het Enterprise-plan is gewijzigd in de Basic-abonnement.

De schatting van de kosten worden de effecten van deze wijzigingen.

> [!WARNING]
> Belangrijke opmerking als u Azure Resource Manager of PowerShell gebruiken om te implementeren hier [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) of [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) in een abonnement dat u hebt verplaatst naar het nieuwe prijsmodel. Als u een laag/prijsschema dan de 'pergb2018' voor Log Analytics of 'Basic' voor Application Insights opgeeft, in plaats van mislukt de implementatie vanwege een ongeldige laag/prijsplan, op te geven deze zal slagen **, maar alleen de geldige wordt gebruikt laag/het prijsplan** (dit geldt niet voor de Log Analytics gratis laag waar een ongeldig pricing tier-bericht is gegenereerd).
>

## <a name="moving-to-the-new-pricing-model"></a>Verplaatsen naar het nieuwe prijsmodel

Als u hebt besloten om het nieuwe prijs model voor een bepaald abonnement te nemen, gaat u naar elke Application Insights resource, opent u het **gebruik en de geschatte kosten** en zorgt u ervoor dat deze zich in de prijs categorie Basic bevindt, en gaat u naar elke log Analytics-werk ruimte, open elke  **Pagina prijs categorie** en wijzig de prijs categorie **per GB (2018)** . 

> [!NOTE]
> De vereiste dat alle Application Insights resources en Log Analytics werk ruimten binnen een bepaald abonnement het nieuwste prijs model aannemen, is nu verwijderd, waardoor er meer flexibele en eenvoudiger configuratie kan worden geconfigureerd. 

## <a name="automate-moving-to-the-new-pricing-model"></a>Verplaatsen naar het nieuwe prijsmodel automatiseren

Zoals hierboven vermeld, is het niet meer nodig om alle bewakings resources in een abonnement op hetzelfde moment te verplaatsen naar het nieuwe prijs model. de ``migratetonewpricingmodel``-actie heeft dus geen effect meer. U kunt nu Application Insights resources en Log Analytics werk ruimten afzonderlijk verplaatsen naar de nieuwste prijs categorieën.  

Het automatiseren van deze wijziging wordt beschreven voor Application Insights met behulp van [set-AzureRmApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/set-azurermapplicationinsightspricingplan) met ``-PricingPlan "Basic"`` en log Analytics met behulp van [set-AzureRmOperationalInsightsWorkspace](https://docs.microsoft.com/powershell/module/AzureRM.OperationalInsights/Set-AzureRmOperationalInsightsWorkspace) met ``-sku "PerGB2018"``. 
