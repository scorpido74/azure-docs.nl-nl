---
title: Het gebruik en de kosten voor Azure-toepassing inzichten beheren | Microsoft Docs
description: Telemetrie-volumes beheren en de kosten controleren in Application Insights.
services: application-insights
documentationcenter: ''
author: DaleKoetke
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/22/2019
ms.author: dalek
ms.openlocfilehash: 45a8f8a7ee4d887503aeaf8e0e285c45a21c4bcc
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982620"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Het gebruik en de kosten voor Application Insights beheren

> [!NOTE]
> In dit artikel wordt beschreven hoe u Application Insights voor gegevens gebruik kunt analyseren.  Raadpleeg de volgende artikelen voor meer informatie.
> - [Gebruik en geschatte kosten bewaken](../../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) wordt beschreven hoe u gebruik en geschatte kosten voor meerdere Azure-bewakingsfuncties voor verschillende prijsmodellen. Ook wordt beschreven hoe u kunt wijzigen van het prijsmodel.

Als u vragen hebt over de werking van prijzen voor Application Insights, kunt u een vraag stellen op ons [forum](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights).

## <a name="pricing-model"></a>Prijsmodel

Prijzen voor [Azure-toepassing Insights][start] is gebaseerd op gegevens volume opgenomen. Elke Application Insights resource wordt in rekening gebracht als een afzonderlijke service en draagt bij aan de factuur voor uw Azure-abonnement.

### <a name="data-volume-details"></a>Details van gegevens volume

* Gegevens volume is het aantal ontvangen telemetrie-bytes door Application Insights. Gegevens volume wordt gemeten als de grootte van het niet-gecomprimeerde JSON-gegevens pakket dat door Application Insights van uw toepassing wordt ontvangen. Voor [tabellaire gegevens die naar Analytics worden geïmporteerd](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), wordt gegevens volume gemeten als de niet-gecomprimeerde grootte van bestanden die naar Application Insights worden verzonden.
* De kosten voor gegevens volumes van uw toepassing worden nu gerapporteerd op een nieuwe facturerings meter met de naam **gegevens opname** vanaf april 2018. Deze nieuwe meter wordt gedeeld door bewakings technologieën zoals toepassingen Insights en Log Analytics en is momenteel beschikbaar onder de service naam **log Analytics**. 
* [Live Metrics stream](../../azure-monitor/app/live-stream.md) gegevens worden niet geteld voor prijs doeleinden.

> [!NOTE]
> Alle prijzen die in de scherm afbeeldingen in dit artikel worden weer gegeven, zijn alleen bedoeld als voor beeld. Zie [Application Insights prijzen][pricing]voor actuele prijzen in uw valuta en regio.

### <a name="multi-step-web-tests"></a>Webtests met meerdere stappen

Voor webtests [met meerdere stappen](../../azure-monitor/app/availability-multistep.md) worden extra kosten in rekening gebracht. Webtests met meerdere stappen zijn webtests die een reeks acties uitvoeren.

Er worden geen afzonderlijke kosten in rekening gebracht voor *ping-tests* van één pagina. Telemetrie van ping-tests en tests met meerdere stappen wordt in rekening gebracht op hetzelfde als andere telemetrie van uw app.

## <a name="review-usage-and-estimate-costs"></a>Gebruik controleren en kosten ramen

Application Insights maakt het eenvoudig om te begrijpen wat uw kosten waarschijnlijk zijn op basis van recente gebruiks patronen. Ga naar de pagina **gebruik en geschatte kosten** in het Azure Portal om aan de slag te gaan voor de Application Insights resource:

![Prijzen kiezen](./media/pricing/pricing-001.png)

A. Controleer uw gegevens volume voor de maand. Dit omvat alle gegevens die worden ontvangen en bewaard (na [steek proeven](../../azure-monitor/app/sampling.md)) van de server en client-apps, en van beschikbaarheids testen.  
B. Er worden afzonderlijke kosten in rekening gebracht voor webtests [met meerdere stappen](../../azure-monitor/app/availability-multistep.md). (Dit omvat geen eenvoudige beschikbaarheids testen, die zijn opgenomen in de kosten van het gegevens volume.)  
C. Bekijk trends in gegevens volumes voor de afgelopen maand.  
D. [Steek proeven](../../azure-monitor/app/sampling.md)voor gegevens opname inschakelen.   
E. Stel de dagelijkse gegevens volume limiet in.  

Als u uw Application Insights-gebruik dieper wilt onderzoeken, opent u de pagina **metrische** gegevens, voegt u de metriek toe met de naam ' data Point volume ' en selecteert u vervolgens de optie *splitsing Toep assen* om de gegevens te splitsen op het type telemetrie-item. 

Application Insights kosten worden toegevoegd aan uw Azure-factuur. U kunt de details van uw Azure-factuur bekijken in het gedeelte **facturering** van de Azure portal of in de [Azure-facturerings Portal](https://account.windowsazure.com/Subscriptions). 

![Selecteer in het menu links facturering](./media/pricing/02-billing.png)

## <a name="managing-your-data-volume"></a>Uw gegevens volume beheren 

Als u wilt weten hoeveel gegevens uw app verzendt, kunt u het volgende doen:

* Ga naar het deel venster **gebruik en geraamd kosten** om de grafiek met het dagelijkse gegevens volume weer te geven. 
* Voeg in Metrics Explorer een nieuwe grafiek toe. Voor de grafiek metriek selecteert u **gegevens punt volume**. Schakel **groeperen**in en groepeer vervolgens op **gegevens type**.
* Gebruik het `systemEvents` gegevens type. Als u bijvoorbeeld wilt zien welk gegevens volume in de laatste dag is opgenomen, zou de query het volgende zijn:

```kusto
systemEvents 
| where timestamp >= ago(1d)
| where type == "Billing" 
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Deze query kan worden gebruikt in een [Azure-logboek waarschuwing](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) om waarschuwingen op gegevens volumes in te stellen. 

Het volume van de gegevens dat u verzendt, kan op drie manieren worden beheerd:

* **Steek proeven**: U kunt steek proeven gebruiken om de hoeveelheid telemetrie te verminderen die wordt verzonden vanaf uw server-en client-apps, met minimale verstoring van metrische gegevens. Steek proeven zijn het primaire hulp programma dat u kunt gebruiken om de hoeveelheid gegevens die u verzendt af te stemmen. Meer informatie over [sampling-functies](../../azure-monitor/app/sampling.md).
 
* **Dagelijks Cap**: Wanneer u een Application Insights resource maakt in de Azure Portal, wordt het dagelijks kapje ingesteld op 100 GB per dag. Wanneer u een Application Insights resource maakt in Visual Studio, is de standaard klein (slechts 32,3 MB/dag). De standaard waarde voor dagelijks Cap is ingesteld om testen te vergemakkelijken. De bedoeling is dat de gebruiker de dagelijkse limiet verhoogt voordat de app in de productie omgeving wordt geïmplementeerd. 

    De maximale limiet is 1.000 GB/dag, tenzij u een hoger maximum voor een toepassing met een hoog verkeer opvraagt. 
    
    Waarschuwings-e-mails over de dagelijkse limiet worden verzonden naar accounts die lid zijn van deze rollen voor uw Application Insights-resource: "ServiceAdmin", "AccountAdmin", "coadmin", "eigenaar".

    Wees voorzichtig wanneer u het dagelijks kapje instelt. Het is uw bedoeling om *nooit het dagelijkse kapje*te bereiken. Als u de dagelijkse limiet bereikt, verliest u gegevens voor de rest van de dag en kunt u uw toepassing niet bewaken. Als u de dagelijkse limiet wilt wijzigen, gebruikt u de optie **dagelijks volume limiet** . U kunt deze optie gebruiken in het deel venster **gebruik en geraamde kosten** (dit wordt verderop in het artikel meer gedetailleerder beschreven).
    
    We hebben de beperking verwijderd voor sommige abonnements typen waarvoor een tegoed is dat niet kan worden gebruikt voor Application Insights. Als er eerder een bestedings limiet voor het abonnement is, is het dialoog venster voor de dagelijkse Cap instructies om de bestedings limiet te verwijderen en het dagelijks kapje te activeren dat groter is dan 32,3 MB/dag.
    
* **Beperking**: Met beperking wordt de gegevens snelheid beperkt tot 32.000 gebeurtenissen per seconde, gemiddeld meer dan 1 minuut per instrumentatie sleutel. Het gegevens volume dat door uw app wordt verzonden, wordt elke minuut beoordeeld. Als het per seconde gemiddeld rente overschrijdt gedurende de minuut, worden sommige aanvragen door de server geweigerd. De SDK buffert de gegevens en probeert deze opnieuw te verzenden. Er wordt een piek van enkele minuten gespreid. Als uw app op consistente wijze gegevens verzendt met meer dan de beperkings snelheid, worden sommige gegevens verwijderd. (De ASP.NET-, Java-en Java script-Sdk's proberen gegevens op deze manier opnieuw te verzenden; andere Sdk's kunnen gewoon vertraagde gegevens verwijderen.) Als er sprake is van beperking, wordt er een melding weer gegeven dat dit is gebeurd.

## <a name="reduce-your-data-volume"></a>Uw gegevens volume verminderen

Hier volgen enkele dingen die u kunt doen om uw gegevens volume te verminderen:

* Gebruik [steek proeven](../../azure-monitor/app/sampling.md). Deze technologie vermindert uw gegevens verhouding zonder dat u uw metrieke waarden hoeft te hellen. U verliest niet de mogelijkheid om te navigeren tussen verwante items in de zoek opdracht. In server-apps werkt de steek proef automatisch.
* [Beperk het aantal Ajax-aanroepen dat kan worden gerapporteerd](../../azure-monitor/app/javascript.md#configuration) in elke pagina weergave, of schakel de Ajax-rapportage uit.
* [Bewerk ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) om verzamelings modules die u niet nodig hebt, uit te scha kelen. U kunt bijvoorbeeld besluiten dat prestatie meter items of afhankelijkheids gegevens inessentieel zijn.
* Splits uw telemetrie over afzonderlijke instrumentatie sleutels. 
* Vooraf samengestelde metrische gegevens. Als u aanroepen naar TrackMetric in uw app opneemt, kunt u het verkeer verminderen door gebruik te maken van de overbelasting die uw berekening van de gemiddelde en standaard afwijking van een batch metingen accepteert. U kunt ook een pakket met [vooraf aggregatie](https://www.myget.org/gallery/applicationinsights-sdk-labs)gebruiken.

## <a name="manage-the-maximum-daily-data-volume"></a>Het maximale dagelijkse gegevensvolume beheren

U kunt het dagelijks volume kapje gebruiken om de verzamelde gegevens te beperken. Als de limiet is bereikt, wordt echter een verlies van alle telemetriegegevens verzonden vanuit uw toepassing voor de rest van de dag. Het is *niet raadzaam* om uw toepassing te laten overlopen op het dagelijkse kapje. U kunt de status en prestaties van uw toepassing niet volgen nadat u de dagelijkse limiet hebt bereikt.

In plaats van de dagelijkse volume limiet te gebruiken, gebruikt u [steek proeven](../../azure-monitor/app/sampling.md) om het gegevens volume op het gewenste niveau af te stemmen. Gebruik het dagelijks kapje alleen als een ' laatste redmiddel ' voor het geval uw toepassing een grote grotere hoeveelheid telemetrie verstuurt.

Als u het dagelijks kapje wilt wijzigen, selecteert u in de sectie **configureren** van uw Application Insights-resource op de pagina **gebruik en geschatte kosten** de optie **dagelijks Cap**.

![Het dagelijkse volume limiet voor telemetrie aanpassen](./media/pricing/pricing-003.png)

## <a name="sampling"></a>Steekproeven
[Steek proeven](../../azure-monitor/app/sampling.md) zijn een methode om de snelheid waarmee telemetrie wordt verzonden naar uw app te verminderen, terwijl u de mogelijkheid houdt om gerelateerde gebeurtenissen te vinden tijdens diagnostische Zoek opdrachten. U behoudt ook de juiste gebeurtenis aantallen.

Steek proeven zijn een efficiënte manier om kosten te verlagen en binnen uw maandelijkse quotum te blijven. De bemonsterings algoritme houdt gerelateerde items van telemetrie bij, zodat u bijvoorbeeld de aanvraag met betrekking tot een bepaalde uitzonde ring kunt vinden wanneer u zoekt. Het algoritme behoudt ook de juiste aantallen, zodat u de juiste waarden ziet in metrische Explorer voor aanvraag tarieven, uitzonderings snelheden en andere aantallen.

Er zijn verschillende soorten steek proeven.

* [Adaptieve steek proeven](../../azure-monitor/app/sampling.md) zijn de standaard waarde voor de ASP.NET-SDK. Adaptieve steek proeven worden automatisch aangepast aan het volume van de telemetrie dat uw app verzendt. Het wordt automatisch uitgevoerd in de SDK in uw web-app, zodat het telemetrie verkeer in het netwerk wordt gereduceerd. 
* Het nemen van *steek proeven* is een alternatief dat werkt op het punt waar telemetrie van uw app de Application Insights-service binnenkomt. De steek proef van opname heeft geen invloed op het volume van de telemetrie die wordt verzonden vanuit uw app, maar vermindert het volume dat door de service wordt bewaard. U kunt opname sampling gebruiken om het quotum te reduceren dat wordt gebruikt door telemetrie van browsers en andere Sdk's.

Ga naar het **prijs** venster om opname sampling in te stellen:

![Selecteer in het deel venster quotum en prijs de tegel voor beelden en selecteer vervolgens een steek proef Fractie](./media/pricing/pricing-004.png)

> [!WARNING]
> In het deel venster voor **gegevens steek proeven** wordt alleen de waarde van de steek proef van opname bepaald. Dit weerspiegelt niet de sampling frequentie die wordt toegepast door de Application Insights SDK in uw app. Als er al een voor beeld van de inkomende telemetrie in de SDK is, worden de steek proeven voor opname niet toegepast.
>

Gebruik een [Analytics-query](analytics.md)om de werkelijke sampling frequentie te ontdekken, ongeacht waar deze is toegepast. De query ziet er als volgt uit:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

In elk bewaard record `itemCount` geeft het aantal oorspronkelijke records aan dat het vertegenwoordigt. De waarde is gelijk aan 1 + het aantal eerder verwijderde records. 

## <a name="change-the-data-retention-period"></a>Wijzigen van de bewaartermijn voor gegevens

De standaard Bewaar periode voor Application Insights resources is 90 dagen. Voor elke Application Insights resource kunnen verschillende Bewaar perioden worden geselecteerd. De volledige set beschik bare Bewaar perioden is 30, 60, 90, 120, 180, 270, 365, 550 of 730 dagen. 

Als u de retentie wilt wijzigen Application Insights, gaat u naar de pagina **gebruik en geschatte kosten** en selecteert u de optie **gegevens** retentie:

![Het dagelijkse volume limiet voor telemetrie aanpassen](./media/pricing/pricing-005.png)

Wanneer facturering is ingeschakeld voor een langere retentie, worden gegevens die langer zijn dan 90 dagen in rekening gebracht als het tarief dat momenteel wordt gefactureerd voor Azure Log Analytics gegevens retentie. Meer informatie vindt u op de [pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/). Blijf op de hoogte van de voortgang van de herbewaaring van variabelen door te [stemmen op deze suggestie](https://feedback.azure.com/forums/357324-azure-monitor-application-insights/suggestions/17454031). 

## <a name="limits-summary"></a>Limieten overzicht

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Dagelijkse Cap-e-mails uitschakelen

Als u de dagelijkse e-mails voor volume limieten wilt uitschakelen, selecteert u in het deel venster **gebruik en geschatte kosten** de optie **dagelijks Cap**onder het gedeelte **configureren** van uw Application Insights-resource. Er zijn instellingen voor het verzenden van e-mail berichten wanneer de limiet is bereikt, en wanneer een aanpasbaar waarschuwings niveau is bereikt. Als u alle e-mail berichten met betrekking tot dagelijks Cap wilt uitschakelen, schakelt u beide vakjes uit.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Prijs categorie verouderde onderneming (per knoop punt)

Voor vroege toepassers van Azure-toepassing Insights zijn er nog twee mogelijke prijs Categorieën: Basic en Enter prise. De prijs categorie Basic is hetzelfde als hierboven beschreven en is de standaardlaag. Dit omvat alle functies van de Enter prise-laag, zonder extra kosten. De laag basis is vooral afhankelijk van het volume van de gegevens die worden opgenomen. 

> [!NOTE]
> De naam van deze verouderde prijs categorieën is gewijzigd. De Enter prise-prijs categorie wordt nu **per knoop punt** genoemd en de prijs categorie Basic wordt nu **per GB**aangeroepen. Deze nieuwe namen worden hieronder en in de Azure Portal gebruikt.  

De laag per knoop punt (voorheen onderneming) heeft een kosten per knoop punt en elk knoop punt ontvangt een dagelijks gegevens toelage. In de prijs categorie per knoop punt worden er kosten in rekening gebracht voor gegevens die boven de inbegrepen limiet zijn opgenomen. Als u Operations Management Suite gebruikt, kiest u de laag per knoop punt. 

Zie [Application Insights prijzen](https://azure.microsoft.com/pricing/details/application-insights/)voor actuele prijzen in uw valuta en regio.

> [!NOTE]
> In april 2018 hebben we een nieuw prijs model [geïntroduceerd](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) voor Azure-bewaking. In dit model wordt een eenvoudig ' pay-as-u-go-' model voor de volledige Port Folio van bewakings Services aangenomen. Meer informatie over het [nieuwe prijs model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), [het beoordelen van de impact van het verplaatsen naar dit model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) op basis van uw gebruiks patronen en [hoe u zich kunt aanmelden bij het nieuwe model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>De rechten van het abonnement per knooppunt laag en het Operations Management Suite-pakket

Klanten die Operations Management Suite E1 en E2 kopen, kunnen Application Insights per knoop punt ontvangen als extra onderdeel zonder extra kosten zoals [eerder aangekondigd](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). In het bijzonder bevat elke eenheid van Operations Management Suite E1 en E2 een recht op één knoop punt van de Application Insights per knooppunt laag. Elk Application Insights knoop punt bevat Maxi maal 200 MB aan gegevens die per dag (gescheiden van Log Analytics gegevens opname) worden opgenomen, met een gegevens Bewaar periode van 90 dagen zonder extra kosten. De laag wordt verderop in het artikel uitvoeriger beschreven. 

Omdat deze laag alleen van toepassing is op klanten met een Operations Management Suite-abonnement, zien klanten die geen Operations Management Suite-abonnement hebben geen optie om deze laag te selecteren.

> [!NOTE]
> Om ervoor te zorgen dat u dit recht krijgt, moeten uw Application Insights-resources zich in de prijs categorie per knoop punt bevinden. Dit recht is alleen van toepassing als knoop punten. Application Insights resources in de laag per GB hebben geen voor deel. Dit recht is niet zichtbaar in de geschatte kosten die worden weer gegeven in het deel venster **gebruik en geschatte kosten** . Als u een abonnement verplaatst naar het nieuwe Azure monitoring-prijs model in april 2018, is de laag per GB de enige laag die beschikbaar is. Het is niet raadzaam een abonnement te verplaatsen naar het nieuwe Azure monitoring-prijs model als u een Operations Management Suite-abonnement hebt.

### <a name="how-the-per-node-tier-works"></a>Hoe werkt de laag per knoop punt?

* U betaalt voor elk knoop punt dat telemetrie verzendt voor apps in de laag per knoop punt.
  * Een *knoop punt* is een fysieke of virtuele server machine of een platform-as-a-Service Role-exemplaar dat als host fungeert voor uw app.
  * Ontwikkel machines, client browsers en mobiele apparaten tellen niet als knoop punten.
  * Als uw app verschillende onderdelen heeft die telemetrie verzenden, zoals een webservice en een back-end-werk nemer, worden de onderdelen afzonderlijk geteld.
  * [Live Metrics stream](../../azure-monitor/app/live-stream.md) gegevens worden niet geteld voor prijs doeleinden. In een abonnement zijn uw kosten per knoop punt, niet per app. Als u vijf knoop punten hebt die telemetrie voor 12 apps verzenden, is de kosten voor vijf knoop punten.
* Hoewel kosten per maand worden genoteerd, worden er alleen kosten in rekening gebracht voor elk uur dat een knoop punt telemetrie vanuit een app verzendt. De kosten per uur zijn de maandelijkse kosten per maand, gedeeld door 744 (het aantal uren in een maand van 31 dagen).
* Er wordt een gegevensvolume toewijzing van 200 MB per dag gegeven voor elk gedetecteerd knoop punt (met granulatie per uur). Ongebruikte gegevens toewijzing wordt niet van de ene dag naar de volgende getransporteerd.
  * Als u de prijs categorie per knoop punt kiest, ontvangt elk abonnement een dagelijkse hoeveelheid gegevens op basis van het aantal knoop punten dat telemetrie verzendt naar de Application Insights resources in dat abonnement. Als u dus vijf knoop punten hebt die gegevens dagelijks verzenden, hebt u een gepoolde limiet van 1 GB toegepast op alle Application Insights resources in dat abonnement. Het maakt niet uit of bepaalde knoop punten meer gegevens verzenden dan andere knoop punten, omdat de opgenomen gegevens worden gedeeld op alle knoop punten. Als op een gegeven Application Insights dag meer gegevens worden ontvangen dan is opgenomen in de dagelijkse gegevens toewijzing voor dit abonnement, zijn de kosten voor overschrijding-gegevens per GB van toepassing. 
  * De dagelijkse gegevens limiet wordt berekend als het aantal uren van de dag (met behulp van UTC) dat elk knoop punt telemetriegegevens verzendt gedeeld door 24 vermenigvuldigd met 200 MB. Als u dus vier knoop punten hebt die een telemetrie verzenden gedurende 15 van de 24 uur per dag, worden de inbegrepen gegevens voor die dag ((4 &#215; 15)/24) &#215; 200 MB = 500 MB. Tegen de prijs van 2,30 USD per GB voor de overschrijding van gegevens, zijn de kosten 1,15 USD als de knoop punten 1 GB aan gegevens naar die dag verzenden.
  * De dagelijkse limiet per knoop punt laag wordt niet gedeeld met toepassingen waarvoor u de laag per GB hebt gekozen. Ongebruikte toelage wordt niet van dag tot dag getransporteerd. 

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Voor beelden van het bepalen van het aantal afzonderlijke knoop punten

| Scenario                               | Totaal aantal dagelijkse knoop punten |
|:---------------------------------------|:----------------:|
| 1 toepassing met 3 Azure App Service exemplaren en 1 virtuele server | 4 |
| 3 toepassingen die worden uitgevoerd op 2 Vm's; de Application Insights resources voor deze toepassingen bevinden zich in hetzelfde abonnement en in de laag per knoop punt | 2 | 
| 4 toepassingen waarvan de toepassingen Insights-resources zich in hetzelfde abonnement bevinden; elke toepassing die 2 instanties uitvoert gedurende 16-piek uren en 4 instanties gedurende 8 piek uren | 13.33 | 
| Cloud Services met één werknemersrol en één webrol, elk met 2 exemplaren | 4 | 
| Een Azure Service Fabric-cluster met 5 knoop punten met 50 micro Services; elke micro service die drie instanties uitvoert | 5|

* De nauw keurigheid van het knoop punt is afhankelijk van welke Application Insights SDK uw toepassing gebruikt. 
  * In SDK versie 2,2 en hoger rapporteert zowel de Application Insights [core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) als de [Web-SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) elke toepassingshost als een knoop punt. Voor beelden zijn de computer naam voor de fysieke server en VM-hosts of de naam van het exemplaar voor Cloud Services.  De enige uitzonde ring hierop is een toepassing die alleen de [.net core](https://dotnet.github.io/) -en de Application Insights core-SDK gebruikt. In dat geval wordt er slechts één knoop punt voor alle hosts gerapporteerd, omdat de hostnaam niet beschikbaar is. 
  * Voor eerdere versies van de SDK gedraagt de [Web-SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) zich als de nieuwere SDK-versies, maar de kern- [SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) rapporteert slechts één knoop punt, ongeacht het aantal hosts van de toepassing. 
  * Als uw toepassing de SDK gebruikt om **roleInstance** in te stellen op een aangepaste waarde, wordt de standaard waarde gebruikt voor het bepalen van het aantal knoop punten. 
  * Als u een nieuwe SDK-versie gebruikt met een app die wordt uitgevoerd vanaf client computers of mobiele apparaten, kan het aantal knoop punten resulteren in een zeer groot aantal (vanwege het grote aantal client computers of mobiele apparaten). 

## <a name="automation"></a>Automation

U kunt een script schrijven om de prijs categorie in te stellen met behulp van Azure-resource beheer. [Meer informatie](powershell.md#price).


## <a name="next-steps"></a>Volgende stappen

* [Steekproeven](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/