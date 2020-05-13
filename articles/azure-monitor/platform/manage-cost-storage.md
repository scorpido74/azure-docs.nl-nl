---
title: Het gebruik en de kosten voor Azure Monitor logboeken beheren
description: Meer informatie over het wijzigen van het prijs plan en het beheren van het gegevens volume en het Bewaar beleid voor uw Log Analytics-werk ruimte in Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: ea289dbdf22f76c8ea716acf87b0b1a2da6ef0f9
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196599"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gebruik en kosten beheren met Azure Monitor-logboeken

> [!NOTE]
> In dit artikel wordt beschreven hoe u uw kosten voor Azure Monitor-logboeken begrijpt en beheert. Een verwant artikel, het [bewaken van het gebruik en de geschatte kosten](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) , beschrijft het weer geven van het gebruik en de geschatte kosten in meerdere Azure-bewakings functies voor verschillende prijs modellen. Alle prijzen en kosten die in dit artikel worden weer gegeven, zijn alleen bedoeld als voor beeld. 

Azure Monitor-Logboeken is ontworpen voor het schalen en ondersteunen van het verzamelen, indexeren en opslaan van enorme hoeveel heden gegevens per dag vanuit elke bron in uw onderneming of die in Azure is geïmplementeerd.  Hoewel dit een primair stuur programma voor uw organisatie is, is de kosten efficiëntie uiteindelijk het onderliggende stuur programma. Daarom is het belang rijk te weten dat de kosten van een Log Analytics werk ruimte alleen gebaseerd zijn op het volume van verzamelde gegevens, dat ook afhankelijk is van het geselecteerde plan en hoe lang u ervoor hebt gekozen om gegevens op te slaan die zijn gegenereerd op basis van uw verbonden bronnen.  

In dit artikel wordt uitgelegd hoe u opgenomen gegevens volume en opslag groei proactief kunt controleren en hoe u limieten definieert om deze kosten te beheren. 

## <a name="pricing-model"></a>Prijsmodel

De standaard prijs voor Log Analytics is een model voor **betalen naar gebruik** op basis van het gegevens volume dat is opgenomen en optioneel voor langere gegevens retentie. Het gegevens volume wordt gemeten als de grootte van de gegevens die worden opgeslagen. Elke Log Analytics-werk ruimte wordt in rekening gebracht als een afzonderlijke service en draagt bij aan de factuur voor uw Azure-abonnement. De hoeveelheid gegevens opname kan aanzienlijk zijn, afhankelijk van de volgende factoren: 

  - Aantal ingeschakelde beheer oplossingen en hun configuratie
  - Aantal bewaakte Vm's
  - Type gegevens die worden verzameld van elke bewaakte VM 
  
Naast het betalen naar gebruik-model is Log Analytics **capaciteits reserverings** lagen waarmee u Maxi maal 25% kunt besparen op basis van de betalen naar gebruik-prijs. Met de prijzen voor capaciteits reservering kunt u een reserve ring kopen vanaf 100 GB per dag. Elk gebruik boven het reserverings niveau wordt gefactureerd op basis van het betalen naar gebruik-tarief. De lagen voor capaciteits reservering hebben een toezeggings periode van 31 dagen. Tijdens de toezeggings periode kunt u overschakelen naar een reserverings tier op een hoger niveau (waardoor de dag van 31 dagen opnieuw wordt opgestart), maar u kunt niet teruggaan naar betalen naar gebruik of naar een reserverings laag met een lagere capaciteit totdat de toezeggings periode is voltooid. Facturering voor de reserverings lagen voor capaciteit wordt dagelijks uitgevoerd. Meer [informatie](https://azure.microsoft.com/pricing/details/monitor/) over log Analytics prijzen voor betalen per gebruik en capaciteits reservering. 

In alle prijs categorieën wordt het gegevens volume berekend op basis van een teken reeks representatie van de gegevens die worden voor bereid om te worden opgeslagen. Diverse [eigenschappen die voor alle gegevens typen gelden](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) , worden niet opgenomen in de berekening van de gebeurtenis grootte, met inbegrip van `_ResourceId` , `_ItemId` , `_IsBillable` en `_BilledSize` .

Houd er ook rekening mee dat sommige oplossingen, zoals [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/), [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) en [Configuration Management](https://azure.microsoft.com/pricing/details/automation/) hun eigen prijs modellen hebben. 

### <a name="log-analytics-dedicated-clusters"></a>Log Analytics toegewezen clusters

Log Analytics toegewezen clusters zijn verzamelingen van werk ruimten in één beheerd Azure Data Explorer-cluster ter ondersteuning van geavanceerde scenario's zoals door de [klant beheerde sleutels](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys).  Log Analytics toegewezen clusters bieden alleen ondersteuning voor een prijs model voor capaciteits reservering, te beginnen bij 1000 GB/dag met een korting van 25% ten opzichte van de prijzen voor betalen per gebruik. Elk gebruik boven het reserverings niveau wordt gefactureerd op basis van het betalen naar gebruik-tarief. De reserve ring van de cluster capaciteit heeft een toezeggings periode van 31 dagen nadat het reserverings niveau is verhoogd. Tijdens de toezeggings periode kan het capaciteits reserverings niveau niet worden verminderd, maar het kan op elk gewenst moment worden verhoogd. Meer informatie over het [maken van een log Analytics clusters](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#create-cluster-resource) en [het koppelen van werk ruimten aan het](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#workspace-association-to-cluster-resource)cluster.  

Het reserverings niveau voor cluster capaciteit wordt geconfigureerd via programmatisch met Azure Resource Manager met behulp van de `Capacity` para meter onder `Sku` . De `Capacity` is opgegeven in eenheden van GB en kan waarden hebben van 1000 GB/dag of meer in stappen van 100 GB/dag. Dit wordt [hier](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#create-cluster-resource)beschreven. Als voor uw cluster een reserve ring nodig is die hoger is dan 2000 GB/dag, neemt u contact met ons op [LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com) .

Omdat de facturering van opgenomen gegevens op het cluster niveau wordt uitgevoerd, hebben werk ruimten die zijn gekoppeld aan een cluster niet langer een prijs categorie. De opgenomen gegevens aantallen uit elke werk ruimte die aan een cluster is gekoppeld, worden geaggregeerd voor het berekenen van de dagelijkse factuur voor het cluster. Houd er rekening mee dat toewijzingen per knoop punt van [Azure Security Center](https://docs.microsoft.com/azure/security-center/) worden toegepast op het niveau van de werk ruimte vóór deze aggregatie van geaggregeerde gegevens in alle werk ruimten in het cluster. Het bewaren van gegevens wordt nog steeds gefactureerd op het niveau van de werk ruimte. Houd er rekening mee dat het factureren van het cluster begint wanneer het cluster wordt gemaakt, ongeacht of er werk ruimten aan het cluster zijn gekoppeld. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Schatting van de kosten voor het beheren van uw omgeving 

Als u Azure Monitor-logboeken nog niet gebruikt, kunt u de [Azure monitor prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=monitor) gebruiken om de kosten van het gebruik van log Analytics te schatten. Begin met het invoeren van ' Azure Monitor ' in het zoekvak en klik op de resulterende Azure Monitor tegel. Schuif omlaag in de pagina naar Azure Monitor en selecteer Log Analytics in de vervolg keuzelijst Type.  Hier kunt u het aantal Vm's en de GB aan gegevens opgeven die u naar verwachting van elke VM wilt verzamelen. Doorgaans is 1 tot 3 GB gegevens maand opgenomen van een typische Azure-VM. Als u Azure Monitor logboeken al evalueert, kunt u uw gegevens statistieken uit uw eigen omgeving gebruiken. Hieronder vindt u informatie over het bepalen [van het aantal bewaakte vm's](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) en het [volume van de gegevens die in uw werk ruimte worden](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)opgenomen. 

## <a name="understand-your-usage-and-estimate-costs"></a>Inzicht in uw gebruik en geschatte kosten

Als u Azure Monitor-logboeken nu gebruikt, is het eenvoudig om te begrijpen wat de kosten waarschijnlijk zijn op basis van recente gebruiks patronen. Gebruik hiervoor **log Analytics gebruik en geschatte kosten** om het gegevens gebruik te controleren en analyseren. Hier ziet u hoeveel gegevens worden verzameld door elke oplossing, hoeveel gegevens er worden bewaard en een schatting van uw kosten op basis van de hoeveelheid gegevens die wordt opgenomen en een extra Bewaar periode van meer dan het inbegrepen bedrag.

![Gebruik en geraamde kosten](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Als u uw gegevens gedetailleerder wilt bekijken, klikt u op het pictogram aan de rechter bovenhoek van een van de grafieken op de pagina **gebruik en geschatte kosten** . Nu kunt u met deze query werken om meer details van uw gebruik te bekijken.  

![Logboeken weer geven](media/manage-cost-storage/logs.png)

Op de pagina **gebruik en geschatte kosten** kunt u uw gegevens volume voor de maand controleren. Dit omvat alle gegevens die in uw Log Analytics-werk ruimte worden ontvangen en bewaard.  Klik op **gebruiks gegevens** boven aan de pagina om het gebruiks dashboard weer te geven met informatie over trends op gegevens volume per bron, computers en aanbieding. Als u een daglimiet wilt weer geven en instellen of als u de retentie periode wilt wijzigen, klikt u op **gegevens volume beheer**.
 
Log Analytics kosten worden toegevoegd aan uw Azure-factuur. U kunt de details van uw Azure-factuur bekijken onder het gedeelte Facturering van de Azure Portal of in de [Azure billing-Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Log Analytics gebruik op uw Azure-factuur weer geven 

Azure biedt een groot aantal handige functies in de hub [Azure Cost Management en facturering](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) . Zo kunt u met de functionaliteit ' cost analysis ' uw uitgaven voor Azure-resources weer geven. Voeg eerst een filter toe op resource type (aan micro soft. operationalinsights/Workspace voor Log Analytics en micro soft. operationalinsights/Workspace voor Log Analytics-clusters) zodat u uw Log Analytics uitgaven kunt volgen. Selecteer vervolgens ' groeperen op ' om ' meter categorie ' of ' meter ' te selecteren.  Houd er rekening mee dat andere services, zoals Azure Security Center en Azure-Sentinel, hun gebruik ook voor Log Analytics werkruimte resources kunnen factureren. Als u de toewijzing aan service naam wilt zien, kunt u de tabel weergave selecteren in plaats van een grafiek. 

Meer informatie over uw gebruik kan worden verkregen door het [gebruik van het Azure portal te downloaden](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). In het gedownloade werk blad kunt u het gebruik per Azure-resource (bijvoorbeeld Log Analytics-werk ruimte) per dag bekijken. In dit Excel-werk blad kunt u het gebruik van uw Log Analytics-werk ruimten vinden door eerst te filteren op de kolom meter categorie om ' Log Analytics ' weer te geven. inzichten en analyses ' (gebruikt door enkele van de verouderde prijs categorieën) en ' Azure Monitor ' (gebruikt door prijs categorieën voor capaciteits reservering) en voegt vervolgens een filter toe aan de kolom ' instance ID ' die is ingesteld op ' bevat werk ruimte ' of ' contains cluster ' (de laatste voor het toevoegen van Log Analytics cluster gebruik). Het gebruik wordt weer gegeven in de kolom verbruikte hoeveelheid en de eenheid voor elk item wordt weer gegeven in de kolom eenheid.  Meer informatie is beschikbaar om u te helpen [uw Microsoft Azure factuur te begrijpen](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="changing-pricing-tier"></a>Prijs categorie wijzigen

Als u de Log Analytics prijs categorie van uw werk ruimte wilt wijzigen, 

1. In de Azure Portal opent u het **gebruik en de geschatte kosten** van uw werk ruimte waar u een lijst ziet van elk van de prijs categorieën die beschikbaar zijn voor deze werk ruimte.

2. Bekijk de geschatte kosten voor elk van de prijs categorieën. Deze schatting is gebaseerd op het gebruik van de afgelopen 31 dagen, dus deze kosten raming is afhankelijk van de laatste 31 dagen die representatief is voor uw typische gebruik. In het onderstaande voor beeld ziet u hoe, op basis van de gegevens patronen van de afgelopen 31 dagen, deze werk ruimte minder kost in de betalen naar gebruik-laag (#1) vergeleken met de 100 GB/dagen capaciteit reserverings tier (#2).  

    ![Prijscategorieën](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Als u de prijs categorie hebt gecontroleerd op basis van de laatste 31 dagen van gebruik, klikt u op **selecteren**.  

U kunt [de prijs categorie ook instellen via Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) met behulp `sku` van de para meter ( `pricingTier` in de Azure Resource Manager sjabloon). 

## <a name="legacy-pricing-tiers"></a>Verouderde prijs Categorieën

Abonnementen die een Log Analytics werk ruimte hebben of Application Insights resource vóór 2 april 2018 zijn gekoppeld aan een Enterprise Agreement die zijn gestart vóór 1 februari 2019, blijven toegang hebben tot de verouderde prijs Categorieën: **gratis**, **zelfstandig (per GB)** en **per knoop punt (OMS)**.  Voor werk ruimten in de gratis prijs categorie geldt een dagelijkse gegevens opname van 500 MB (met uitzonde ring van beveiligings gegevens typen die worden verzameld door Azure Security Center) en de Bewaar periode van gegevens is beperkt tot 7 dagen. De gratis prijs categorie is alleen bedoeld voor evaluatie doeleinden. Werk ruimten in de zelfstandige of per knooppunt prijs categorie hebben een door de gebruiker Configureer bare Bewaar periode van 30 tot 730 dagen.

De prijs categorie per knoop punt kosten per bewaakte VM (knoop punt) op een granulatie van het uur. Voor elk bewaakt knoop punt wordt voor de werk ruimte 500 MB aan gegevens toegewezen per dag die niet wordt gefactureerd. Deze toewijzing wordt geaggregeerd op het niveau van de werk ruimte. Gegevens die boven de cumulatieve dagelijkse gegevens toewijzing zijn opgenomen, worden per GB in rekening gebracht als gegevens overschrijding. Op uw factuur wordt de service **Insight and Analytics** voor log Analytics gebruik als de werk ruimte zich in de prijs categorie per knoop punt bevindt. 

> [!TIP]
> Als uw werk ruimte toegang heeft tot de prijs categorie **per knoop punt** , maar u vraagt zich af of deze kosten lager zijn in een betalen per gebruik-laag, kunt u [de onderstaande query gebruiken](#evaluating-the-legacy-per-node-pricing-tier) om eenvoudig een aanbeveling te krijgen. 

Werk ruimten die zijn gemaakt vóór 2016 april hebben ook toegang tot de oorspronkelijke **Standard** -en **Premium** -prijs categorieën die respectievelijk 30 en 365 dagen zijn bewaard. Nieuwe werk ruimten kunnen niet worden gemaakt in de prijs categorie **Standard** of **Premium** , en als een werk ruimte uit deze lagen wordt verplaatst, kan deze niet meer worden teruggezet. 

[Hier](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)vindt u meer informatie over de beperkingen van de prijs categorie.

> [!NOTE]
> Als u de rechten wilt gebruiken die afkomstig zijn van het aanschaffen van OMS E1 Suite, OMS E2-Suite of OMS-invoeg toepassing voor System Center, kiest u de prijs categorie Log Analytics *per knoop punt* .

## <a name="change-the-data-retention-period"></a>De gegevensretentieperiode wijzigen

In de volgende stappen wordt beschreven hoe u kunt configureren hoe lang logboek gegevens worden bewaard in uw werk ruimte. Gegevens retentie kan worden geconfigureerd van 30 tot 730 dagen (2 jaar) voor alle werk ruimten, tenzij ze gebruikmaken van de verouderde gratis prijs categorie. Meer [informatie](https://azure.microsoft.com/pricing/details/monitor/) over prijzen voor langere retentie van gegevens. 

### <a name="default-retention"></a>Standaard retentie

Als u de standaard retentie voor uw werk ruimte wilt instellen, 
 
1. Selecteer in de Azure Portal in uw werk ruimte de optie **gebruik en geschatte kosten** in het linkerdeel venster.
2. Klik op bovenaan de pagina **Gebruik en geschatte kosten** op **Gegevensvolumebeheer**.
3. Verplaats in het deel venster de schuif regelaar om het aantal dagen te verhogen of te verlagen en klik vervolgens op **OK**.  Als u zich in de laag *gratis* bevindt, kunt u de Bewaar periode voor gegevens niet wijzigen en moet u upgraden naar de laag betaald om deze instelling te kunnen beheren.

    ![Instelling voor het bewaren van gegevens van de werk ruimte wijzigen](media/manage-cost-storage/manage-cost-change-retention-01.png)

Als de retentie is verlaagd, is er een respijt periode van een enkele dag voordat de oudste gegevens worden verwijderd. 
    
De retentie kan ook [via Azure Resource Manager worden ingesteld](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) met behulp van de `retentionInDays` para meter. Als u de gegevens retentie instelt op 30 dagen, kunt u ook een onmiddellijke opschoning van oudere gegevens activeren met behulp van de `immediatePurgeDataOn30Days` para meter, wat nuttig kan zijn voor nalevings scenario's. Deze functionaliteit is alleen beschikbaar via Azure Resource Manager. 


Twee gegevens typen- `Usage` en `AzureActivity` --worden standaard 90 dagen bewaard en er worden geen kosten in rekening gebracht voor deze Bewaar periode van 90 dagen. Deze gegevens typen zijn ook gratis van de kosten voor gegevens opname. 

Gegevens typen van Application Insights resources (,,,,,,,, en) op basis van een werk ruimte `AppAvailabilityResults` `AppBrowserTimings` `AppDependencies` `AppExceptions` `AppEvents` `AppMetrics` `AppPageViews` `AppPerformanceCounters` `AppRequests` `AppSystemEvents` `AppTraces` worden ook standaard 90 dagen bewaard en er worden geen kosten in rekening gebracht voor de Bewaar periode van 90 dagen. De retentie kan worden aangepast met de functionaliteit voor het bewaren van gegevens typen. 


### <a name="retention-by-data-type"></a>Bewaren op gegevens type

Het is ook mogelijk om verschillende Bewaar instellingen op te geven voor afzonderlijke gegevens typen van 30 tot 730 dagen (met uitzonde ring van werk ruimten in de prijs categorie verouderde gratis). Elk gegevens type is een subresource van de werk ruimte. De SecurityEvent-tabel kan bijvoorbeeld als volgt worden behandeld in [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) :

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Houd er rekening mee dat het gegevens type (tabel) hoofdletter gevoelig is.  Als u de huidige instellingen voor het bewaren van gegevens typen van een bepaald gegevens type (in dit voor beeld SecurityEvent) wilt ophalen, gebruikt u:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Als u de huidige instellingen voor het bewaren van gegevens type wilt ophalen voor alle gegevens typen in uw werk ruimte, laat u gewoon het specifieke gegevens type weg, bijvoorbeeld:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Ga als volgt te werk om de Bewaar periode van een bepaald gegevens type (in dit voor beeld SecurityEvent) in te stellen op 730 dagen.

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Geldige waarden voor `retentionInDays` zijn 30 tot en met 730.

De `Usage` `AzureActivity` gegevens typen en kunnen niet worden ingesteld met aangepaste retentie. Ze nemen het maximum van de standaard retentie van de werk ruimte of 90 dagen in beslag. 

Een uitstekend hulp programma om rechtstreeks verbinding te maken met Azure Resource Manager om Bewaar periode in te stellen op gegevens type is het OSS-hulp programma [ARMclient](https://github.com/projectkudu/ARMClient).  Meer informatie over ARMclient van artikelen op [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) en de [Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Hier volgt een voor beeld van het gebruik van ARMClient, waarbij SecurityEvent-gegevens worden ingesteld op een retentie van 730 dagen:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> Het instellen van de Bewaar periode voor afzonderlijke gegevens typen kan worden gebruikt om de kosten voor het bewaren van gegevens te verlagen.  Voor gegevens die vanaf oktober 2019 worden verzameld (als deze functie is uitgebracht) vermindert u de retentie voor sommige gegevens typen in de loop van de tijd om uw Bewaar kosten te verlagen.  Voor de gegevens die u eerder hebt verzameld, is het instellen van een lagere Bewaar periode voor een afzonderlijk type geen invloed op de kosten voor de Bewaar periode.  

## <a name="manage-your-maximum-daily-data-volume"></a>Uw maximale dagelijkse gegevens volume beheren

U kunt een dagelijks CAP configureren en de dagelijkse opname voor uw werk ruimte beperken, maar zorg ervoor dat uw doel niet kan worden bereikt door de dagelijkse limiet te gebruiken.  Anders verliest u gegevens voor de rest van de dag, wat van invloed kan zijn op andere Azure-Services en-oplossingen waarvan de functionaliteit afhankelijk is van actuele gegevens die beschikbaar zijn in de werk ruimte.  Als gevolg hiervan wordt de mogelijkheid geboden om waarschuwingen te bekijken en te ontvangen wanneer de status van bronnen met ondersteunende IT-Services worden beïnvloed.  Het dagelijks kapje is bedoeld om te worden gebruikt als een manier om de onverwachte toename van het gegevens volume van uw beheerde resources te beheren en binnen uw limiet te blijven, of wanneer u niet-geplande kosten voor uw werk ruimte wilt beperken.  

Zodra de dagelijkse limiet is bereikt, stopt het verzamelen van factureer bare gegevens typen voor de rest van de dag. (De latentie die inherent is aan het Toep assen van de daglimiet, kan betekenen dat het kapje niet zo precies het opgegeven niveau voor de dagelijkse Cap wordt toegepast.) Er wordt een waarschuwings banner boven aan de pagina weer gegeven voor de geselecteerde Log Analytics-werk ruimte en er wordt een bewerkings gebeurtenis verzonden naar de *bewerkings* tabel onder **LogManagement** categorie. Het verzamelen van gegevens wordt hervat nadat het tijdstip waarop de tijd opnieuw is ingesteld onder *dagelijkse limiet is ingesteld op*. We raden u aan een waarschuwings regel te definiëren op basis van deze bewerkings gebeurtenis, die is geconfigureerd om te worden gewaarschuwd wanneer de dagelijkse gegevens limiet is bereikt. 

> [!WARNING]
> Het dagelijks kapje stopt niet het verzamelen van gegevens uit Azure Security Center, met uitzonde ring van werk ruimten waarin Azure Security Center is geïnstalleerd vóór 19 juni 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificeren welke dagelijkse gegevens limiet moet worden gedefinieerd

Bekijk [log Analytics gebruik en de geschatte kosten](usage-estimated-costs.md) om inzicht te krijgen in de trend van de gegevens opname en wat het dagelijkse volume Cap is dat moet worden gedefinieerd. U moet er rekening mee houden, omdat u hebt gewonnen? d u kunt uw resources controleren nadat de limiet is bereikt. 

### <a name="set-the-daily-cap"></a>Het dagelijks kapje instellen

In de volgende stappen wordt beschreven hoe u een limiet kunt configureren voor het beheren van de hoeveelheid gegevens die Log Analytics werk ruimte wordt opgenomen per dag.  

1. Selecteer in de werkruimte in het linkerdeelvenster **Gebruik en geschatte kosten**.
2. Klik op het tabblad **gebruik en geschatte kosten** voor de geselecteerde werk ruimte op **gegevens volume beheer** vanaf de bovenkant van de pagina. 
3. Dagelijks Cap is standaard **uitgeschakeld** ? Klik op aan om deze **functie in te** scha kelen en stel de gegevens volume limiet in GB/dag in.

    ![Gegevens limiet Log Analytics configureren](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Waarschuwen wanneer de dagelijkse limiet is bereikt

Hoewel we een visuele hint presen teren in de Azure Portal als aan de drempel waarde voor gegevens limiet is voldaan, wordt dit gedrag niet noodzakelijkerwijs uitgelijnd op de manier waarop u operationele problemen beheert die onmiddellijke aandacht vereisen.  Als u een waarschuwings melding wilt ontvangen, kunt u in Azure Monitor een nieuwe waarschuwings regel maken.  Zie [waarschuwingen maken, weer geven en beheren](alerts-metric.md)voor meer informatie.

Om aan de slag te gaan, zijn dit de aanbevolen instellingen voor de waarschuwing:

- Doel: Selecteer uw Log Analytics resource
- Gezocht 
   - Signaal naam: aangepaste zoek opdracht in Logboeken
   - Zoek query: bewerking | Wat is een overschrijding van Details
   - Gebaseerd op: aantal resultaten
   - Voor waarde: groter dan
   - Drempel waarde: 0
   - Periode: 5 (minuten)
   - Frequentie: 5 (minuten)
- Naam van waarschuwings regel: dagelijkse gegevens limiet bereikt
- Ernst: waarschuwing (Ernst 1)

Zodra een waarschuwing is gedefinieerd en de limiet is bereikt, wordt er een waarschuwing geactiveerd en wordt de reactie uitgevoerd die is gedefinieerd in de actie groep. Dit kan uw team op de hoogte stellen via e-mail en SMS-berichten, of acties automatiseren met webhooks, Automation-runbooks of [integreren met een externe ITSM-oplossing](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Het oplossen van problemen met een hoger gebruik dan verwacht

Hoger gebruik wordt veroorzaakt door een of beide volgende oorzaken:
- Meer knoop punten dan de verwachte verzen ding van gegevens naar Log Analytics werk ruimte
- Er worden meer gegevens dan verwacht verzonden naar Log Analytics-werk ruimte (mogelijk als gevolg van het gebruik van een nieuwe oplossing of een configuratie wijziging naar een bestaande oplossing)

## <a name="understanding-nodes-sending-data"></a>Informatie over knoop punten die gegevens verzenden

Gebruik voor meer informatie over het aantal knoop punten dat elke dag in de afgelopen maand de heartbeats rapporteert van de agent.

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Het aantal knoop punten dat gegevens in de afgelopen 24 uur verzendt, gebruiken de query: 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

De follow-query kan worden gebruikt om een lijst met knoop punten te verkrijgen die gegevens verzenden (en de hoeveelheid gegevens die door elk van beide worden verzonden):

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> Gebruik deze `union *` query's spaarzaam als scans over gegevens typen [veel resources](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane) zijn om uit te voeren. Als u geen resultaten **per computer** nodig hebt, voert u een query uit op het type gebruiks gegevens (zie hieronder).

## <a name="understanding-ingested-data-volume"></a>Meer informatie over opgenomen gegevens volume

Op de pagina **gebruik en geschatte kosten** toont het diagram *gegevens opname per oplossing* het totale volume van de verzonden gegevens en hoeveel er door elke oplossing wordt verzonden. Zo kunt u trends bepalen, bijvoorbeeld of het algehele gegevens gebruik (of het gebruik door een bepaalde oplossing) groeit, stabieler of aflopend blijft. 

### <a name="data-volume-for-specific-events"></a>Gegevens volume voor specifieke gebeurtenissen

Als u de grootte van opgenomen gegevens voor een bepaalde reeks gebeurtenissen wilt bekijken, kunt u een query uitvoeren op de specifieke tabel (in dit voor beeld `Event` ) en de query vervolgens beperken tot de gebeurtenissen die van belang zijn (in dit voor beeld gebeurtenis-ID 5145 of 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Houd er rekening mee dat met de component `where IsBillable = true` gegevens typen worden gefilterd van bepaalde oplossingen waarvoor geen opname kosten worden berekend. 

### <a name="data-volume-by-solution"></a>Gegevensvolume per oplossing

De query die wordt gebruikt om het factureer bare gegevens volume per oplossing in de afgelopen maand te bekijken (met uitzonde ring van de laatste gedeeltelijke dag) is:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

De component with `TimeGenerated` is alleen om ervoor te zorgen dat de query-ervaring in het Azure Portal na de standaard 24 uur terugkeert. Wanneer u het gegevens type gebruik gebruikt `StartTime` en `EndTime` de tijds verzamelingen weergeeft waarvoor de resultaten worden weer gegeven. 

### <a name="data-volume-by-type"></a>Gegevens volume per type

U kunt verder inzoomen om gegevens trends weer te geven voor op gegevens type:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Of om een tabel weer te geven op oplossing en type voor de afgelopen maand,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Gegevens volume per computer

Het `Usage` gegevens type bevat geen informatie op computer niveau. Als u de **grootte** van opgenomen gegevens per computer wilt zien, gebruikt u de `_BilledSize` [eigenschap](log-standard-properties.md#_billedsize), die de grootte in bytes levert:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

De `_IsBillable` [eigenschap](log-standard-properties.md#_isbillable) geeft aan of de opgenomen gegevens kosten in rekening worden gebracht. 

Voor een overzicht van het **aantal** factureer bare gebeurtenissen dat per computer is opgenomen, gebruikt u 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

> [!TIP]
> Gebruik deze `union  *` query's spaarzaam als scans over gegevens typen [veel resources](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane) zijn om uit te voeren. Als u geen resultaten **per computer** nodig hebt, voert u een query uit op het gegevens type gebruik.

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Gegevens volume per Azure-resource, resource groep of abonnement

Voor gegevens van knoop punten die worden gehost in azure, kunt u de **grootte** van opgenomen gegevens __per computer__verkrijgen, met behulp van de [eigenschap](log-standard-properties.md#_resourceid)_ResourceId, die het volledige pad naar de bron bevat:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Voor gegevens van knoop punten die worden gehost in azure, kunt u de **grootte** van opgenomen gegevens __per Azure-abonnement__verkrijgen, waarbij u de eigenschap parseert `_ResourceId` als:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

`subscriptionId`Als u wijzigt in `resourceGroup` , wordt het factureer bare opgenomen gegevens volume per Azure-resource groep weer gegeven. 

> [!TIP]
> Gebruik deze `union  *` query's spaarzaam als scans over gegevens typen [veel resources](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane) zijn om uit te voeren. Als u geen resultaten per abonnement, resource groep of bron naam nodig hebt, voert u vervolgens een query uit op het gegevens type gebruik.

> [!WARNING]
> Sommige velden van het gegevens type gebruik, terwijl ze nog steeds in het schema zijn, zijn afgeschaft en hun waarden worden niet meer ingevuld. Dit zijn zowel **computers** als velden met betrekking tot opname (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** en **AverageProcessingTimeMs**.


### <a name="querying-for-common-data-types"></a>Query's uitvoeren voor algemene gegevens typen

Als u meer wilt weten over de gegevens bron voor een bepaald gegevens type, vindt u hier enkele nuttige voorbeeld query's:

+ **Application Insights resources op basis van een werk ruimte**
  - meer informatie [vindt u hier](https://docs.microsoft.com/azure/azure-monitor/app/pricing#data-volume-for-workspace-based-application-insights-resources)
+ **Beveiligingsoplossing**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **Logboekbeheeroplossing**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **Perf-gegevenstype**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **Gebeurtenisgegevenstype**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog-gegevenstype**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics**-gegevenstype
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Tips voor het verkleinen van het gegevens volume

Hieronder vindt u enkele suggesties voor het verkleinen van het aantal logboeken dat wordt verzameld:

| Bron van hoog gegevensvolume | Het gegevensvolume verminderen |
| -------------------------- | ------------------------- |
| Beveiligingsgebeurtenissen            | Selecteer [normale of minimale beveiligingsgebeurtenissen](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> Wijzig het beleid voor beveiligingscontrole zodat alleen de gewenste gebeurtenissen worden verzameld. Controleer vooral de noodzaak voor het verzamelen van gebeurtenissen voor <br> - [filterplatform controleren](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [register controleren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [bestandssysteem controleren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [kernel-object controleren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [greepbewerking controleren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> -Verwissel bare opslag controleren |
| Prestatiemeteritems       | Wijzig de [Prestatiemeteritemconfiguratie](data-sources-performance-counters.md) in: <br> - Frequentie van het verzamelen van gegevens beperken <br> - Aantal prestatiemeteritems beperken |
| Gebeurtenislogboeken                 | Wijzig [Configuratie van gebeurtenislogboek](data-sources-windows-events.md) in: <br> - Aantal verzamelde gebeurtenislogboeken beperken <br> - Alleen vereiste gebeurtenisniveaus verzamelen. Bijvoorbeeld, gebeurtenissen op *informatie*niveau niet verzamelen |
| Syslog                     | Wijzig de [syslog-configuratie](data-sources-syslog.md) in: <br> - Aantal verzamelde installaties beperken <br> - Alleen vereiste gebeurtenisniveaus verzamelen. Bijvoorbeeld, gebeurtenissen op *informatie*- en *foutopsporings*niveau niet verzamelen |
| AzureDiagnostics           | Wijzig de resourcelogboekverzameling om: <br> - Het aantal resources dat logboeken naar Log Analytics verzendt te verkleinen <br> - Alleen vereiste logboeken te verzamelen |
| Oplossingsgegevens van computers die de oplossing niet nodig hebben | Gebruik een [oplossing als doel](../insights/solution-targeting.md) voor het verzamelen van gegevens van alleen vereiste groepen computers. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Knoop punten ophalen als gefactureerd in de prijs categorie per knoop punt

Als u een lijst wilt ophalen met computers die worden gefactureerd als knoop punten als de werk ruimte zich in de prijs categorie verouderd per knoop punt bevindt, zoekt u naar knoop punten waarnaar **gefactureerde gegevens typen** worden verzonden (sommige gegevens typen zijn gratis). Als u dit wilt doen, gebruikt u de `_IsBillable` [eigenschap](log-standard-properties.md#_isbillable) en gebruikt u het meest linkse veld van de Fully Qualified Domain name. Hiermee wordt het aantal computers met gefactureerde gegevens per uur geretourneerd (de granulatie van de knoop punten die worden geteld en gefactureerd):

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Aantal knoop punten voor beveiliging en automatisering ophalen

Als u gebruikmaakt van de prijs categorie per knoop punt (OMS), worden de kosten berekend op basis van het aantal knoop punten en oplossingen dat u gebruikt. het aantal inzichten en analyse knooppunten waarvoor u wordt gefactureerd, wordt weer gegeven in de tabel op de pagina **gebruik en geschatte kosten** .  

U kunt de query gebruiken om het aantal afzonderlijke beveiligings knooppunten te bekijken:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Als u het aantal afzonderlijke automatiserings knooppunten wilt zien, gebruikt u de query:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>De prijs categorie verouderd per knoop punt evalueren

Het besluit of werk ruimten met toegang tot de verouderde prijs categorie **per knoop punt** beter zijn uitgeschakeld in die laag of in een huidige **betalen naar gebruik** -of **capaciteits reserverings** niveau, is het vaak moeilijk voor klanten te beoordelen.  Dit houdt in dat er rekening wordt gehouden met de afweging tussen de vaste kosten per bewaakt knoop punt in de prijs categorie per knoop punt en de opgenomen gegevens toewijzing van 500 MB/knoop punt/dag en de kosten voor het betalen van geconsumeerde gegevens in de laag betalen naar gebruik (per GB). 

Om deze evaluatie te vergemakkelijken, kan de volgende query worden gebruikt om een aanbeveling voor de optimale prijs categorie te maken op basis van de gebruiks patronen van een werk ruimte.  Met deze query wordt gekeken naar de bewaakte knoop punten en gegevens die in de afgelopen 7 dagen zijn opgenomen in een werk ruimte, en voor elke dag wordt geëvalueerd welke prijs categorie optimaal zou zijn. Als u de query wilt gebruiken, moet u opgeven

1. Hiermee wordt aangegeven of de werk ruimte Azure Security Center gebruikt door `workspaceHasSecurityCenter` in te stellen op `true` of `false` , 
2. werk de prijzen bij als u specifieke kortingen hebt en
3. Geef het aantal dagen op dat u wilt terugkijken en analyseren door in te stellen `daysToEvaluate` . Dit is handig als de query te lang duurt om 7 dagen aan gegevens te bekijken. 

Dit is de query voor de aanbeveling prijs categorie:

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let daysToEvaluate = 7; // Enter number of previous days look at (reduce if the query is taking too long)
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union withsource = tt * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend PerGBDailyCost = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)) * PerGBPrice,
             DataGB * PerGBPrice)
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Deze query is geen exacte replicatie van de manier waarop het gebruik wordt berekend, maar werkt in de meeste gevallen aan aanbevelingen voor de prijs categorie.  

> [!NOTE]
> Als u de rechten wilt gebruiken die afkomstig zijn van het aanschaffen van OMS E1 Suite, OMS E2-Suite of OMS-invoeg toepassing voor System Center, kiest u de prijs categorie Log Analytics *per knoop punt* .

## <a name="create-an-alert-when-data-collection-is-high"></a>Een waarschuwing maken wanneer het verzamelen van gegevens hoog is

In deze sectie wordt beschreven hoe u een waarschuwing instelt als:
- Het gegevensvolume groter is dan een opgegeven hoeveelheid.
- Het gegevensvolume naar verwachting een opgegeven hoeveelheid gaat overschrijden.

Azure-waarschuwingen bieden ondersteuning voor [logboekwaarschuwingen](alerts-unified-log.md) die gebruikmaken van zoekquery’s. 

De volgende query geeft een resultaat wanneer er meer dan 100 GB aan gegevens in de afgelopen 24 uur is verzameld:

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type 
| where DataGB > 100
```

De volgende query gebruikt een eenvoudige formule om te voorspellen wanneer meer dan 100 GB aan gegevens op een dag wordt verzonden: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type 
| where EstimatedGB > 100
```

Als u een waarschuwing wilt instellen bij een ander gegevensvolume, wijzigt u 100 in de query's in het aantal GB waarbij u een waarschuwing wilt.

Gebruik de stappen in [Een nieuwe logboekwaarschuwing maken](alerts-metric.md) om een melding te krijgen wanneer de hoeveelheid verzamelde gegevens groter is dan verwacht.

Bij het instellen van de waarschuwing voor de eerste query - wanneer er meer dan 100 GB aan gegevens in 24 uur, stelt u het volgende in:  

- **Waarschuwingsvoorwaarde definiëren** - geef uw Log Analytics-werkruimte op als het resourcedoel.
- **Waarschuwingscriteria** - geef het volgende op:
   - **Signaalnaam** - selecteer **Aangepast zoeken in logboeken**
   - **Zoekquery** op `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`
   - **Waarschuwingslogica** is **Gebaseerd op het ** *aantal resultaten*, en **Voorwaarde** is *Groter dan* een **Drempelwaarde** van *0*
   - **Tijdsperiode** van *1440* minuten en **Waarschuwingsfrequentie** van elke *60* minuten, omdat de gebruiksgegevens maar één keer per uur worden bijgewerkt.
- **Waarschuwingsdetails definiëren** - geef het volgende op:
   - **Naam** op *Gegevensvolume groter dan 100 GB in 24 uur*
   - **Ernst** op *Waarschuwing*

Maak een nieuwe [Actiegroep](action-groups.md) of geef een bestaande op, zodat u een melding ontvangt wanneer aan de criteria voor een logboekwaarschuwing wordt voldaan.

Bij het maken van de waarschuwing voor de tweede query - wanneer wordt voorspeld dat er meer dan 100 GB aan gegevens in 24 uur zal zijn, stelt u het volgende in:

- **Waarschuwingsvoorwaarde definiëren** - geef uw Log Analytics-werkruimte op als het resourcedoel.
- **Waarschuwingscriteria** - geef het volgende op:
   - **Signaalnaam** - selecteer **Aangepast zoeken in logboeken**
   - **Zoekquery** op `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`
   - **Waarschuwingslogica** is **Gebaseerd op het ** *aantal resultaten*, en **Voorwaarde** is *Groter dan* een **Drempelwaarde** van *0*
   - **Tijdsperiode** van *180* minuten en **Waarschuwingsfrequentie** van elke *60* minuten, omdat de gebruiksgegevens maar één keer per uur worden bijgewerkt.
- **Waarschuwingsdetails definiëren** - geef het volgende op:
   - **Naam** op *Gegevensvolume naar verwachting groter dan 100 GB in 24 uur*
   - **Ernst** op *Waarschuwing*

Maak een nieuwe [Actiegroep](action-groups.md) of geef een bestaande op, zodat u een melding ontvangt wanneer aan de criteria voor een logboekwaarschuwing wordt voldaan.

Wanneer u een waarschuwing ontvangt, gebruikt u de stappen in de volgende sectie om te bepalen waarom het-gebruik is hoger dan verwacht.

## <a name="data-transfer-charges-using-log-analytics"></a>Kosten voor gegevens overdracht met behulp van Log Analytics

Bij het verzenden van gegevens naar Log Analytics kunnen kosten voor de gegevens bandbreedte worden berekend. Zoals beschreven op de [pagina met prijzen voor Azure-band breedte](https://azure.microsoft.com/pricing/details/bandwidth/), wordt gegevens overdracht tussen Azure-Services in twee regio's in rekening gebracht als uitgaande gegevens overdracht tegen het normale tarief. Inkomende gegevens overdracht is gratis. Deze kosten zijn echter zeer klein (aantal%) vergeleken met de kosten voor Log Analytics gegevens opname. Als gevolg van het beheer van de kosten voor Log Analytics moet u zich richten op uw opgenomen gegevens volume, en wij hebben richt lijnen om die [hier](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)te helpen begrijpen.   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Problemen oplossen waarom Log Analytics geen gegevens meer verzamelt

Als u zich in de prijs categorie verouderde gratis bevindt en meer dan 500 MB aan gegevens op een dag hebt verzonden, stopt het verzamelen van gegevens voor de rest van de dag. Het bereiken van de dagelijkse limiet is een gemeen schappelijke reden dat Log Analytics het verzamelen van gegevens niet meer of gegevens lijkt te missen.  Log Analytics maakt een gebeurtenis van het type bewerking wanneer het verzamelen van gegevens wordt gestart en gestopt. Voer de volgende query uit in de zoek opdracht om te controleren of u de dagelijkse limiet bereikt en ontbrekende gegevens: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Wanneer het verzamelen van gegevens stopt, wordt de OperationStatus **waarschuwing**. Wanneer het verzamelen van gegevens wordt gestart, is de OperationStatus **voltooid**. De volgende tabel beschrijft de redenen waarom het verzamelen van gegevens wordt gestopt en een aanbevolen actie om het verzamelen van gegevens te hervatten:  

|Reden voor verzamelen stopt| Oplossing| 
|-----------------------|---------|
|De dagelijkse limiet van de verouderde gratis prijs categorie is bereikt |Wacht tot de volgende dag voor het automatisch opnieuw opstarten van de verzameling of de prijs categorie is gewijzigd.|
|Het dagelijkse kapje van uw werk ruimte is bereikt|Wacht tot de verzameling automatisch opnieuw wordt opgestart of verhoog de dagelijkse gegevens volume limiet die wordt beschreven in het maximale dagelijkse gegevens volume beheren. De tijd voor het opnieuw instellen van de dagelijkse limiet wordt weer gegeven op de pagina **gegevens volume beheer** . |
|Het Azure-abonnement bevindt zich in een onderbroken staat vanwege:<br> De gratis proef versie is beëindigd<br> Azure Pass is verlopen<br> De maandelijkse bestedings limiet is bereikt (bijvoorbeeld op een MSDN-of Visual Studio-abonnement)|Overstappen op een betaald abonnement<br> Limiet verwijderen of wachten tot de limiet is ingesteld|

Als u een melding wilt ontvangen wanneer het verzamelen van gegevens wordt gestopt, gebruikt u de stappen in de waarschuwing *dagelijkse gegevens Cap maken* om een melding te ontvangen wanneer het verzamelen van gegevens wordt gestopt. Gebruik de stappen die worden beschreven in [een actie groep maken](action-groups.md) om een e-mail, webhook of runbook-actie voor de waarschuwings regel te configureren. 

## <a name="limits-summary"></a>Limieten overzicht

Er zijn enkele aanvullende Log Analytics limieten, waarvan sommige afhankelijk zijn van de Log Analytics prijs categorie. Deze worden [hier](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)beschreven.


## <a name="next-steps"></a>Volgende stappen

- Zie [Zoek opdrachten in Logboeken in azure monitor logboeken](../log-query/log-query-overview.md) voor meer informatie over het gebruik van de Zoek taal. U kunt zoekquery’s gebruiken om aanvullende analyses uit te voeren op de gebruiksgegevens.
- Gebruik de stappen in [Een nieuwe logboekwaarschuwing maken](alerts-metric.md) om een melding te krijgen wanneer aan een zoekcriterium wordt voldaan.
- Gebruik een [oplossing als doel](../insights/solution-targeting.md) voor het verzamelen van gegevens van alleen vereiste groepen computers.
- Als u een effectief gebeurtenis verzamelings beleid wilt configureren, raadpleegt u [Azure Security Center filter beleid](../../security-center/security-center-enable-data-collection.md).
- Wijzig de [prestatiemeteritemconfiguratie](data-sources-performance-counters.md).
- Controleer de [configuratie van gebeurtenis logboeken](data-sources-windows-events.md)om de instellingen van uw gebeurtenis verzameling te wijzigen.
- Bekijk [syslog-configuratie](data-sources-syslog.md)om de instellingen van uw syslog-verzameling te wijzigen.