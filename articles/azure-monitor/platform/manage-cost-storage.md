---
title: Gebruik en kosten voor Azure Monitor-logboeken beheren | Microsoft Documenten
description: Meer informatie over het wijzigen van het prijsplan en het beheren van het gegevensvolume- en bewaarbeleid voor uw Log Analytics-werkruimte in Azure Monitor.
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
ms.date: 04/08/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: d03b053f2aa5de4a6f7874dbf4e6ccb3a305a964
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992076"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gebruik en kosten beheren met Azure Monitor-logboeken

> [!NOTE]
> In dit artikel wordt beschreven hoe u uw kosten voor Azure Monitor-logboeken begrijpt en beheert. In een gerelateerd artikel worden [het gebruik en de geschatte kosten controleren](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) beschreven hoe u het gebruik en de geschatte kosten voor meerdere Azure-bewakingsfuncties voor verschillende prijsmodellen weergeven. Alle prijzen en kosten in dit artikel zijn bijvoorbeeld alleen doeleinden. 

Azure Monitor Logs is ontworpen voor het schalen en ondersteunen van het verzamelen, indexeren en opslaan van enorme hoeveelheden gegevens per dag vanuit elke bron in uw onderneming of geïmplementeerd in Azure.  Hoewel dit een primaire drijfveer voor uw organisatie kan zijn, is kostenefficiëntie uiteindelijk de onderliggende driver. Daarom is het belangrijk om te begrijpen dat de kosten van een Log Analytics-werkruimte niet alleen gebaseerd zijn op het volume van de verzamelde gegevens, maar ook afhankelijk zijn van het geselecteerde abonnement en hoe lang u ervoor hebt gekozen om gegevens op te slaan die zijn gegenereerd uit uw verbonden bronnen.  

In dit artikel bekijken we hoe u proactief de toename van het gegevensvolume en de opslagkunt monitoren en limieten definiëren om de bijbehorende kosten te beheersen. 

## <a name="pricing-model"></a>Prijsmodel

De standaardprijzen voor Log Analytics zijn een **Pay-As-You-Go-model** op basis van het ingenomen gegevensvolume en optioneel voor een langere gegevensbewaring. Het gegevensvolume wordt gemeten als de grootte van de gegevens die worden opgeslagen. Elke Log Analytics-werkruimte wordt in rekening gebracht als een afzonderlijke service en draagt bij aan de factuur voor uw Azure-abonnement. De hoeveelheid gegevensopname kan aanzienlijk zijn, afhankelijk van de volgende factoren: 

  - Aantal ingeschakelde beheeroplossingen en hun configuratie (bijv. 
  - Aantal gecontroleerde VM's
  - Type gegevens verzameld van elke bewaakte VM 
  
Naast het Pay-As-You-Go-model heeft Log Analytics **capaciteitsreserveringsniveaus** waarmee u maar liefst 25% besparen in vergelijking met de pay-as-you-go-prijs. Met de capaciteitsreserveringsprijzen u een reservering kopen vanaf 100 GB/dag. Elk gebruik boven het reserveringsniveau wordt in rekening gebracht tegen het pay-as-you-go-tarief. De capaciteitsreserveringsniveaus hebben een verbintenisperiode van 31 dagen. Tijdens de verbintenisperiode u overgaan naar een niveau capaciteitsreserveringsniveau op een hoger niveau (waarmee de verbintenisperiode van 31 dagen wordt hervat), maar u niet teruggaan naar Pay-As-You-Go of naar een niveau voor een lagere capaciteitsreservering totdat de verbintenisperiode is voltooid. 
[Meer informatie](https://azure.microsoft.com/pricing/details/monitor/) over de prijzen voor betalen per gebruik en capaciteitsreservering van Log Analytics. 

In alle prijsniveaus wordt het gegevensvolume berekend op basis van een tekenreeksweergave van de gegevens die bereid zijn op te slaan. Verschillende [eigenschappen die alle gegevenstypen gemeen](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) hebben, worden niet `_ResourceId` `_ItemId`opgenomen `_IsBillable` in de berekening van de gebeurtenisgrootte, waaronder , en `_BilledSize`.

Houd er ook rekening mee dat sommige oplossingen, zoals [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) en Azure [Sentinel,](https://azure.microsoft.com/pricing/details/azure-sentinel/)hun eigen prijsmodel hebben. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Het schatten van de kosten voor het beheer van uw omgeving 

Als u Azure Monitor Logs nog niet gebruikt, u de [prijscalculator azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) gebruiken om de kosten van het gebruik van Logboekanalyse te schatten. Begin met het invoeren van 'Azure Monitor' in het vak Zoeken en klik op de resulterende Azure Monitor-tegel. Schuif omlaag naar Azure Monitor en selecteer Logboekanalyse in de vervolgkeuzelijst Type.  Hier u het aantal VM's en de GB aan gegevens invoeren die u van elke VM verwacht te verzamelen. Meestal wordt 1 tot 3 GB gegevensmaand ingenomen van een typische Azure VM. Als u Azure Monitor Logs al evalueert, u uw gegevensstatistieken uit uw eigen omgeving gebruiken. Zie hieronder hoe u het [aantal bewaakte VM's](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) en het volume van de [gegevens die uw werkruimte inneemt,](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)bepalen. 

## <a name="understand-your-usage-and-estimate-costs"></a>Inzicht in uw gebruik en raming van de kosten

Als u azure-monitorlogboeken nu gebruikt, is het gemakkelijk om te begrijpen wat de kosten waarschijnlijk zijn gebaseerd op recente gebruikspatronen. Gebruik hiervoor **het gebruik van Log Analytics en geschatte kosten** om het gegevensgebruik te controleren en te analyseren. Dit geeft aan hoeveel gegevens door elke oplossing worden verzameld, hoeveel gegevens worden bewaard en een schatting van uw kosten op basis van de hoeveelheid ingenomen gegevens en eventuele extra retentie boven het inbegrepen bedrag.

![Gebruik en geraamde kosten](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Als u uw gegevens in meer detail wilt bekijken, klikt u op het pictogram rechtsboven in een van de grafieken op de pagina **Gebruik en Geschatte kosten.** Nu u met deze query werken om meer details over uw gebruik te bekijken.  

![Logboekenweergave](media/manage-cost-storage/logs.png)

Op de pagina **Gebruik en Geschatte kosten** u uw gegevensvolume voor de maand bekijken. Dit omvat alle gegevens die zijn ontvangen en bewaard in uw Log Analytics-werkruimte.  Klik boven aan de pagina op **Gebruiksgegevens** om het gebruiksdashboard weer te geven met informatie over trends in het gegevensvolume per bron, computers en aanbieden. Als u een dagelijkse limiet wilt weergeven en instellen of de bewaarperiode wilt wijzigen, klikt u op **Gegevensvolumebeheer**.
 
Log Analytics-kosten worden toegevoegd aan uw Azure-factuur. U details van uw Azure-factuur bekijken onder het gedeelte Facturering van de Azure-portal of in de [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Log Analytics-gebruik weergeven op uw Azure-factuur 

Azure biedt veel nuttige functionaliteit in de hub [Azure Cost Management + Billing.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Met de functionaliteit 'Kostenanalyse' u bijvoorbeeld uw uitgaven voor Azure-resources weergeven. Als u een filter toevoegt op resourcetype (naar microsoft.operationalinsights/workspace voor Log Analytics) u uw uitgaven bijhouden.

Meer inzicht in uw gebruik kan worden verkregen door [uw gebruik te downloaden van de Azure-portal.](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal) In de gedownloade spreadsheet u het gebruik per Azure-bron (bijvoorbeeld Logboekanalysewerkruimte) per dag zien. In deze Excel-spreadsheet u het gebruik van uw Log Analytics-werkruimten vinden door eerst te filteren op de kolom 'Metercategorie' om 'Inzichten en analyses' (gebruikt door enkele van de oudere prijsniveaus) en 'Log Analytics' weer te geven en vervolgens een filter toe te voegen aan de kolom 'Instantie-id' die 'werkruimte bevat'. Het gebruik wordt weergegeven in de kolom 'Verbruikt aantal' en de eenheid voor elk item wordt weergegeven in de kolom 'Maateenheid'.  Meer details zijn beschikbaar om u inzicht te [geven in uw Microsoft Azure-factuur.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) 

## <a name="changing-pricing-tier"></a>Prijslaag wijzigen

Als u de prijscategorie Log Analytics van uw werkruimte wilt wijzigen, 

1. Open in de Azure-portal **gebruiks- en geschatte kosten** vanuit uw werkruimte, waar u een lijst ziet met elk van de prijsniveaus die beschikbaar zijn voor deze werkruimte.

2. Bekijk de geschatte kosten voor elk van de prijsniveaus. Deze schatting is gebaseerd op de laatste 31 dagen van gebruik, dus deze kostenraming is afhankelijk van de laatste 31 dagen die representatief zijn voor uw typische gebruik. In het onderstaande voorbeeld u zien hoe deze werkruimte, op basis van de gegevenspatronen van de afgelopen 31 dagen, minder zou kosten in de laag Betalen per gebruik (#1) in vergelijking met de capaciteitsreserveringslaag van 100 GB/dag (#2).  

    ![Prijscategorieën](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Nadat u de geschatte kosten hebt gecontroleerd op basis van de laatste 31 gebruiksdagen, klikt u op **Selecteren**als u besluit de prijscategorie te wijzigen.  

U [de prijscategorie](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) ook instellen `sku` via`pricingTier` Azure Resource Manager met behulp van de parameter (in de sjabloon Azure Resource Manager). 

## <a name="legacy-pricing-tiers"></a>Oude prijsniveaus

Abonnementen die vóór 2 april 2018 een Log Analytics-werkruimte of Application Insights-bron hadden of die zijn gekoppeld aan een Enterprise Agreement die vóór 1 februari 2019 is gestart, blijven toegang hebben tot de oudere prijsniveaus: **Gratis**, **Standalone (Per GB)** en **Per knooppunt (OMS).**  Werkruimten in de prijscategorie Gratis hebben de dagelijkse gegevensopname beperkt tot 500 MB (met uitzondering van beveiligingsgegevensdie zijn verzameld door Azure Security Center) en de gegevensbewaring is beperkt tot 7 dagen. De prijscategorie Gratis is alleen bedoeld voor evaluatiedoeleinden. Werkruimten in de prijzenvan zelfstandige of per knooppunt hebben een gebruikersconfigureerbare retentie van 30 tot 730 dagen.

De prijstarieftarieftarieven per knooppunt per bewaakte VM (knooppunt) op een uur granulariteit. Voor elk bewaakt knooppunt krijgt de werkruimte 500 MB aan gegevens per dag die niet worden gefactureerd. Deze toewijzing wordt geaggregeerd op werkruimteniveau. Gegevens die boven de geaggregeerde dagelijkse gegevenstoewijzing worden ingenomen, worden per GB gefactureerd als gegevensoverschrijding. Houd er rekening mee dat de service op uw factuur **inzicht en analyse** voor het gebruik van Logboekanalyse is als de werkruimte zich in de prijslaag Per knooppunt bevindt. 

> [!TIP]
> Als uw werkruimte toegang heeft tot de prijslaag **per knooppunt,** maar u zich afvraagt of deze minder kosten zou kosten in een pay-as-you-go-laag, u [de onderstaande query gebruiken](#evaluating-the-legacy-per-node-pricing-tier) om gemakkelijk een aanbeveling te krijgen. 

Werkruimten die vóór april 2016 zijn gemaakt, hebben ook toegang tot de oorspronkelijke **standaard-** en **Premium-prijsniveaus** met vaste gegevensbewaring van respectievelijk 30 en 365 dagen. Nieuwe werkruimten kunnen niet worden gemaakt in de prijzenlagen **Standaard** of **Premium** en als een werkruimte uit deze lagen wordt verplaatst, kan deze niet worden verplaatst. 

Meer details over de beperkingen van de prijslaag zijn [hier](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)beschikbaar.

> [!NOTE]
> Als u de rechten wilt gebruiken die afkomstig zijn van de aankoop van OMS E1 Suite, OMS E2 Suite of OMS Add-On for System Center, kiest u de prijscategorie Log Analytics *Per knooppunt.*

## <a name="change-the-data-retention-period"></a>De gegevensretentieperiode wijzigen

In de volgende stappen wordt beschreven hoe u configureren hoe lang logboekgegevens worden bewaard in uw werkruimte. Het bewaren van gegevens kan worden geconfigureerd van 30 tot 730 dagen (2 jaar) voor alle werkruimten, tenzij ze de verouderde gratis prijscategorie gebruiken. 

### <a name="default-retention"></a>Standaardretentie

Als u de standaardretentie voor uw werkruimte wilt instellen, 
 
1. Selecteer in de Azure-portal vanuit uw werkruimte **Gebruiks- en geschatte kosten** in het linkerdeelvenster.
2. Klik op bovenaan de pagina **Gebruik en geschatte kosten** op **Gegevensvolumebeheer**.
3. Verplaats de schuifregelaar in het deelvenster om het aantal dagen te verhogen of te verkleinen en klik vervolgens op **OK**.  Als u zich op de *gratis* laag bevindt, u de bewaarperiode voor gegevens niet wijzigen en moet u upgraden naar de betaalde laag om deze instelling te beheren.

    ![Instelling voor het bewaren van werkruimtegegevens wijzigen](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
De retentie kan ook worden ingesteld `retentionInDays` via Azure Resource [Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) met behulp van de parameter. Als u de gegevensbewaring instelt op 30 dagen, u bovendien `immediatePurgeDataOn30Days` een onmiddellijke zuivering van oudere gegevens activeren met behulp van de parameter, wat handig kan zijn voor nalevingsgerelateerde scenario's. Deze functionaliteit wordt alleen zichtbaar via Azure Resource Manager. 

Twee gegevenstypen `Usage` - `AzureActivity` en - worden standaard 90 dagen bewaard en er zijn geen kosten verbonden aan deze retentie van 90 dagen. Deze gegevenstypen zijn ook vrij van kosten voor het innemen van gegevens. 

### <a name="retention-by-data-type"></a>Bewaren op gegevenstype

Het is ook mogelijk om verschillende bewaarinstellingen voor afzonderlijke gegevenstypen van 30 tot 730 dagen op te geven (met uitzondering van werkruimten in de verouderde gratis prijscategorie). Elk gegevenstype is een subbron van de werkruimte. De tabel SecurityEvent kan bijvoorbeeld worden aangepakt in [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) als:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Houd er rekening mee dat het gegevenstype (tabel) hoofdlettergevoelig is.  Gebruik:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Als u de huidige bewaarinstellingen per gegevenstype wilt krijgen voor alle gegevenstypen in uw werkruimte, laat u het specifieke gegevenstype weg, bijvoorbeeld:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Als u het bewaren van een bepaald gegevenstype (in dit voorbeeld SecurityEvent) wilt instellen op 730 dagen, moet u

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Geldige waarden `retentionInDays` voor zijn 30 tot en met 730.

De `Usage` `AzureActivity` gegevenstypen en gegevens kunnen niet worden ingesteld met aangepaste bewaring. Ze nemen het maximum van de standaard werkruimteretentie of 90 dagen aan. 

Een geweldig hulpmiddel om rechtstreeks verbinding te maken met Azure Resource Manager om retentie in te stellen op gegevenstype is de [ARM-client](https://github.com/projectkudu/ARMClient)van de OSS-tool.  Meer informatie over ARMclient uit artikelen van [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) en [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Hier is een voorbeeld met ARMClient, waarbij SecurityEvent-gegevens worden ingesteld op een retentie van 730 dagen:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> Het instellen van retentie op afzonderlijke gegevenstypen kan worden gebruikt om uw kosten voor het bewaren van gegevens te verlagen.  Voor gegevens die vanaf oktober 2019 zijn verzameld (toen deze functie werd uitgebracht), kan het verminderen van de retentie voor sommige gegevenstypen uw bewaarkosten in de loop van de tijd verminderen.  Voor eerder verzamelde gegevens heeft het instellen van een lagere retentie voor een individueel type geen invloed op uw bewaarkosten.  

## <a name="manage-your-maximum-daily-data-volume"></a>Uw maximale dagelijkse gegevensvolume beheren

U een dagelijkse limiet configureren en de dagelijkse inname voor uw werkruimte beperken, maar zorg gebruiken omdat uw doel niet zou moeten zijn om de dagelijkse limiet te bereiken.  Anders verliest u gegevens voor de rest van de dag, wat van invloed kan zijn op andere Azure-services en -oplossingen waarvan de functionaliteit kan afhangen van de beschikbaarheid van up-to-date gegevens in de werkruimte.  Als gevolg hiervan u waarschuwingen observeren en ontvangen wanneer de gezondheidsomstandigheden van resources die IT-services ondersteunen, worden beïnvloed.  De dagelijkse limiet is bedoeld om te worden gebruikt als een manier om de onverwachte toename van het gegevensvolume van uw beheerde resources te beheren en binnen uw limiet te blijven, of wanneer u ongeplande kosten voor uw werkruimte wilt beperken.  

Kort nadat de dagelijkse limiet is bereikt, stopt het verzamelen van factureerbare gegevenstypen voor de rest van de dag. (Latentie die inherent is aan het toepassen van de dagelijkse dop kan betekenen dat de dop niet wordt toegepast als precies het opgegeven dagelijkse dopniveau.) Boven aan de pagina wordt een waarschuwingsbanner weergegeven voor de geselecteerde werkruimte Log Analytics en wordt een *bewerkingsgebeurtenis* verzonden naar de bewerkingstabel onder de categorie **LogManagement.** Het verzamelen van gegevens wordt hervat nadat de resettijd die is gedefinieerd onder *de dagelijkse limiet wordt ingesteld op*. We raden u aan een waarschuwingsregel te definiëren op basis van deze bewerkingsgebeurtenis, die is geconfigureerd om te melden wanneer de dagelijkse gegevenslimiet is bereikt. 

> [!WARNING]
> De dagelijkse limiet stopt niet met het verzamelen van gegevens uit Azure Security Center, behalve voor werkruimten waarin Azure Security Center vóór 19 juni 2017 is geïnstalleerd. 

### <a name="identify-what-daily-data-limit-to-define"></a>Bepalen welke dagelijkse gegevenslimiet u wilt definiëren

Bekijk [het gebruik van Log Analytics en de geschatte kosten](usage-estimated-costs.md) om inzicht te krijgen in de trend van gegevensopname en wat de dagelijkse volumelimiet is om te definiëren. Het moet met zorg worden overwogen, omdat u uw resources niet controleren nadat de limiet is bereikt. 

### <a name="set-the-daily-cap"></a>Stel de dagelijkse limiet in

In de volgende stappen wordt beschreven hoe u een limiet configureert om het volume van de gegevens te beheren dat de werkruimte log Analytics per dag inneemt.  

1. Selecteer in de werkruimte in het linkerdeelvenster **Gebruik en geschatte kosten**.
2. Klik **op** de pagina Gebruik en geschatte kosten voor de geselecteerde werkruimte op **Gegevensvolumebeheer** vanaf de bovenkant van de pagina. 
3. Dagelijkse limiet is **standaard UIT** klik **op AAN** om deze in te schakelen en stel vervolgens de limiet voor het gegevensvolume in GB/dag in.

    ![Logboekanalyse configureren gegevenslimiet](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Alert wanneer Daily Cap bereikt

Hoewel we een visuele aanwijzing presenteren in de Azure-portal wanneer de drempelwaarde voor gegevenslimiet wordt bereikt, sluit dit gedrag niet noodzakelijkerwijs aan bij de manier waarop u operationele problemen beheert die onmiddellijke aandacht vereisen.  Als u een waarschuwingsmelding wilt ontvangen, u een nieuwe waarschuwingsregel maken in Azure Monitor.  Zie voor meer informatie [hoe u waarschuwingen maakt, bekijkt en beheert.](alerts-metric.md)

Om u op weg te helpen, zijn hier de aanbevolen instellingen voor de waarschuwing:

- Doel: selecteer uw Log Analytics-bron
- Criteria: 
   - Signaalnaam: Aangepaste logboekzoekopdracht
   - Zoekopdracht: Bewerking | waar Detail 'OverQuota' heeft
   - Gebaseerd op: Aantal resultaten
   - Voorwaarde: Groter dan
   - Drempel: 0
   - Periode: 5 (minuten)
   - Frequentie: 5 (minuten)
- Naam van waarschuwingsregel: dagelijkse gegevenslimiet bereikt
- Ernst: Waarschuwing (Sev 1)

Zodra de waarschuwing is gedefinieerd en de limiet is bereikt, wordt een waarschuwing geactiveerd en wordt het antwoord uitgevoerd dat is gedefinieerd in de actiegroep. Het kan uw team op de hoogte stellen via e-mail en tekstberichten, of acties automatiseren met webhooks, Automation runbooks of [integreren met een externe ITSM-oplossing.](itsmc-overview.md#create-itsm-work-items-from-azure-alerts) 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Het oplossen van problemen met een hoger gebruik dan verwacht

Hoger gebruik wordt veroorzaakt door een of beide volgende oorzaken:
- Meer knooppunten dan verwacht die gegevens verzenden naar log Analytics-werkruimte
- Meer gegevens dan verwacht worden verzonden naar Log Analytics-werkruimte (mogelijk als gevolg van het gebruik van een nieuwe oplossing of een configuratiewijziging in een bestaande oplossing)

## <a name="understanding-nodes-sending-data"></a>Inzicht in knooppunten die gegevens verzenden

Als u wilt begrijpen hoeveel knooppunten heartbeats van de agent elke dag in de afgelopen maand melden, gebruikt u

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Als u een aantal knooppunten krijgt die gegevens in de afgelopen 24 uur hebben verzonden, wordt de query gebruikt: 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Om een lijst met knooppunten te krijgen die gegevens verzenden (en de hoeveelheid gegevens die door elk van de gegevens worden verzonden), kan de volgquery worden gebruikt:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> Gebruik `union withsource = tt *` deze query's spaarzaam als scans over gegevenstypen zijn duur uit te voeren. Deze query vervangt de oude manier van opvragen per computer met het gegevenstype Gebruik.  

## <a name="understanding-ingested-data-volume"></a>Inzicht in het ingenomen gegevensvolume

Op de pagina **Gebruik en geschatte kosten** geeft de grafiek *Gegevensopname per oplossing* het totale aantal verzonden gegevens weer en hoeveel er door elke oplossing wordt verzonden. Hiermee u trends bepalen, zoals of het totale gegevensgebruik (of het gebruik door een bepaalde oplossing) groeit, stabiel blijft of afneemt. 

### <a name="data-volume-for-specific-events"></a>Gegevensvolume voor specifieke gebeurtenissen

Als u wilt kijken naar de grootte van ingenomen gegevens voor een bepaalde `Event`set gebeurtenissen, u de specifieke tabel (in dit voorbeeld) opvragen en de query vervolgens beperken tot de gebeurtenissen die van belang zijn (in dit voorbeeld gebeurtenis-ID 5145 of 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Houd er `where IsBillable = true` rekening mee dat de clausule gegevenstypen filtert uit bepaalde oplossingen waarvoor geen innamekosten zijn. 

### <a name="data-volume-by-solution"></a>Gegevensvolume per oplossing

De query die wordt gebruikt om het factureerbare gegevensvolume per oplossing van de afgelopen maand te bekijken (met uitzondering van de laatste gedeeltelijke dag) is:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

De clausule `TimeGenerated` met is alleen om ervoor te zorgen dat de query-ervaring in de Azure-portal zal terugkijken dan de standaard 24 uur. Wanneer u het gegevenstype `EndTime` Gebruik gebruikt en `StartTime` de tijdbuckets weergeeft waarvoor de resultaten worden weergegeven. 

### <a name="data-volume-by-type"></a>Gegevensvolume per type

U verder inzoomen om gegevenstrends te bekijken op gegevenstype:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Of om een tabel te zien op oplossing en type voor de afgelopen maand,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Gegevensvolume per computer

Het `Usage` gegevenstype bevat geen informatie op het completere niveau. Als u de **grootte** van de ingenomen gegevens per computer wilt zien, gebruikt u de `_BilledSize` [eigenschap](log-standard-properties.md#_billedsize), die de grootte in bytes biedt:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

De `_IsBillable` [eigenschap](log-standard-properties.md#_isbillable) geeft aan of de ingenomen gegevens kosten in rekening brengen.

Als u het **aantal** factureerbare gebeurtenissen per computer wilt zien, gebruikt u 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Gegevensvolume per Azure-bron, brongroep of abonnement

Voor gegevens van knooppunten die in Azure worden gehost, u de **grootte** van ingenomen gegevens __per computer__ophalen, de [eigenschap](log-standard-properties.md#_resourceid)_ResourceId gebruiken, die het volledige pad naar de bron biedt:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Voor gegevens van knooppunten die in Azure worden gehost, u de `_ResourceId` **grootte** van ingenomen gegevens per __Azure-abonnement__krijgen, de eigenschap ontleedt als:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Als `subscriptionId` `resourceGroup` u overstapt op, wordt het factureerbare gegevensvolume per Azure-brongroep weergegeven. 

> [!WARNING]
> Sommige velden van het gegevenstype Gebruik, terwijl ze nog in het schema staan, zijn afgeschaft en zullen hun waarden niet meer worden ingevuld. Dit zijn **computer-** en velden met betrekking tot inname (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** en **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Query's voor veelvoorkomende gegevenstypen

Om dieper in de bron van gegevens voor een bepaald gegevenstype te graven, volgen hier enkele nuttige voorbeeldquery's:

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

### <a name="tips-for-reducing-data-volume"></a>Tips voor het verminderen van het gegevensvolume

Enkele suggesties voor het verminderen van het volume van de verzamelde logboeken zijn:

| Bron van hoog gegevensvolume | Het gegevensvolume verminderen |
| -------------------------- | ------------------------- |
| Beveiligingsgebeurtenissen            | Selecteer [normale of minimale beveiligingsgebeurtenissen](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> Wijzig het beleid voor beveiligingscontrole zodat alleen de gewenste gebeurtenissen worden verzameld. Controleer vooral de noodzaak voor het verzamelen van gebeurtenissen voor <br> - [filterplatform controleren](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [register controleren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [bestandssysteem controleren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [kernel-object controleren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [greepbewerking controleren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - controle van verwisselbare opslag |
| Prestatiemeteritems       | Wijzig de [Prestatiemeteritemconfiguratie](data-sources-performance-counters.md) in: <br> - Frequentie van het verzamelen van gegevens beperken <br> - Aantal prestatiemeteritems beperken |
| Gebeurtenislogboeken                 | Wijzig [Configuratie van gebeurtenislogboek](data-sources-windows-events.md) in: <br> - Aantal verzamelde gebeurtenislogboeken beperken <br> - Alleen vereiste gebeurtenisniveaus verzamelen. Bijvoorbeeld, gebeurtenissen op *informatie*niveau niet verzamelen |
| Syslog                     | Wijzig de [syslog-configuratie](data-sources-syslog.md) in: <br> - Aantal verzamelde installaties beperken <br> - Alleen vereiste gebeurtenisniveaus verzamelen. Bijvoorbeeld, gebeurtenissen op *informatie*- en *foutopsporings*niveau niet verzamelen |
| AzureDiagnostics           | Wijzig de resourcelogboekverzameling om: <br> - Het aantal resources dat logboeken naar Log Analytics verzendt te verkleinen <br> - Alleen vereiste logboeken te verzamelen |
| Oplossingsgegevens van computers die de oplossing niet nodig hebben | Gebruik [oplossingstargeting](../insights/solution-targeting.md) om gegevens te verzamelen van alleen vereiste groepen computers. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Knooppunten krijgen zoals gefactureerd in de prijslaag per knooppunt

Als u een lijst met computers wilt krijgen die als knooppunten worden gefactureerd als de werkruimte zich in de oudere prijscategorie Per knooppunt bevindt, zoekt u naar knooppunten die **gefactureerde gegevenstypen** verzenden (sommige gegevenstypen zijn gratis). Gebruik hiervoor de `_IsBillable` [eigenschap](log-standard-properties.md#_isbillable) en gebruik het meest linkse veld van de volledig gekwalificeerde domeinnaam. Dit geeft het aantal computers met gefactureerde gegevens per uur (dat is de granulariteit waarop knooppunten worden geteld en gefactureerd):

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Het aantal gegevens voor het aantal gegevens over beveiliging en automatisering

Als u op de prijslaag 'Per knooppunt (OMS)' staat, worden er kosten in rekening gebracht op basis van het aantal knooppunten en oplossingen dat u gebruikt, het aantal Insights- en Analytics-knooppunten waarvoor u wordt gefactureerd, wordt weergegeven in de tabel op de pagina **Gebruik en Geschatte kosten.**  

Als u het aantal afzonderlijke beveiligingsknooppunten wilt zien, u de query gebruiken:

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

Als u het aantal verschillende automatiseringsknooppunten wilt zien, gebruikt u de query:

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

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>De prijscategorie legacy Per Node evalueren

De beslissing of werkruimten met toegang tot de oudere prijscategorie **Per knooppunt** beter af zijn in die laag of in een huidige laag betalen **per gebruik** of **capaciteitsreservering,** is vaak moeilijk te beoordelen voor klanten.  Dit houdt in dat u inzicht heeft in de afweging tussen de vaste kosten per bewaakt knooppunt in de prijscategorie Per knooppunt en de inbegrepen gegevenstoewijzing van 500 MB/node/dag en de kosten van het betalen voor ingenomen gegevens in de laag Pay-As-You-Go (Per GB). 

Om deze beoordeling te vergemakkelijken, kan de volgende query worden gebruikt om een aanbeveling te doen voor de optimale prijscategorie op basis van de gebruikspatronen van een werkruimte.  In deze query wordt gekeken naar de bewaakte knooppunten en gegevens die in de afgelopen 7 dagen in een werkruimte zijn opgenomen en wordt voor elke dag geëvalueerd welke prijscategorie optimaal zou zijn geweest. Als u de query wilt gebruiken, moet u opgeven `workspaceHasSecurityCenter` of `true` `false`de werkruimte Azure Security Center gebruikt door de prijzen Per knooppunt en per GB die uw organizaiton ontvangt, in te stellen of vervolgens (optioneel) bij te werken. 

```kusto
// Set these paramaters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your price per node / month 
let PerGBPrice = 2.30; // Enter your price per GB 
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
union withsource = tt * 
| where TimeGenerated >= startofday(now(-7d)) and TimeGenerated < startofday(now())
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join (
    Usage 
    | where TimeGenerated > ago(8d)
    | where StartTime >= startofday(now(-7d)) and EndTime < startofday(now())
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend PerGBDailyCost = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*nodesPerDay, 0.)) * PerGBPrice,
             DataGB * PerGBPrice)
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 1.0*nodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerGBPrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
| project day, Recommendation // Comment this line to see details
| sort by day asc
```

## <a name="create-an-alert-when-data-collection-is-high"></a>Een waarschuwing maken wanneer de gegevensverzameling hoog is

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

## <a name="data-transfer-charges-using-log-analytics"></a>Kosten voor gegevensoverdracht met Log Analytics

Het verzenden van gegevens naar Log Analytics kan leiden tot kosten voor gegevensbandbreedte. Zoals beschreven op de [prijspagina azure bandbreedte](https://azure.microsoft.com/pricing/details/bandwidth/), wordt gegevensoverdracht tussen Azure-services in twee regio's in rekening gebracht als uitgaande gegevensoverdracht tegen de normale snelheid. Inkomende gegevensoverdracht is gratis. Deze heffing is echter zeer gering (weinig %) vergeleken met de kosten voor het innemen van Log Analytics-gegevens. Daarom moet het beheersen van de kosten voor Log Analytics zich richten op uw ingenomen gegevensvolume, en we hebben richtlijnen om dat [hier](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)te begrijpen.   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Problemen oplossen waarom Log Analytics geen gegevens meer verzamelt

Als u zich op de oudere gratis prijscategorie bevindt en meer dan 500 MB aan gegevens op een dag hebt verzonden, wordt het verzamelen van gegevens voor de rest van de dag gestopt. Het bereiken van de dagelijkse limiet is een veel voorkomende reden dat Log Analytics stopt met het verzamelen van gegevens of gegevens lijken te ontbreken.  Log Analytics maakt een gebeurtenis van het type Operation wanneer het verzamelen van gegevens begint en stopt. Voer de volgende zoekopdracht uit in zoekopdrachten om te controleren of u de dagelijkse limiet en ontbrekende gegevens bereikt: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Wanneer het verzamelen van gegevens stopt, is de OperationStatus **Waarschuwing**. Wanneer het verzamelen van gegevens wordt gestart, wordt de OperationStatus **uitgevoerd**. In de volgende tabel worden redenen beschreven waarom het verzamelen van gegevens stopt en een voorgestelde actie om het verzamelen van gegevens te hervatten:  

|Reden collectie stopt| Oplossing| 
|-----------------------|---------|
|Dagelijkse limiet van legacy Gratis prijscategorie bereikt |Wacht tot de volgende dag voordat de verzameling automatisch opnieuw wordt opgestart of ga u over op een betaalde prijscategorie.|
|Dagelijkse limiet van uw werkruimte is bereikt|Wacht tot verzameling automatisch opnieuw wordt opgestart of verhoog de dagelijkse gegevensvolumelimiet die wordt beschreven in het beheren van het maximale dagelijkse gegevensvolume. De dagelijkse limietresettijd wordt weergegeven op de pagina **Gegevensvolumebeheer.** |
|Azure-abonnement is in een opgeschorte status als gevolg van:<br> Gratis proefperiode beëindigd<br> Azure-pas verlopen<br> Maandelijkse bestedingslimiet bereikt (bijvoorbeeld op een MSDN- of Visual Studio-abonnement)|Overstappen op een betaald abonnement<br> Limiet verwijderen of wachten tot de limiet wordt gereset|

Als u een melding wilt ontvangen wanneer het verzamelen van gegevens stopt, gebruikt u de stappen die zijn beschreven in *Waarschuwing voor dagelijkse gegevenslimiet maken* om op de hoogte te worden gesteld wanneer het verzamelen van gegevens stopt. Gebruik de stappen die zijn beschreven in [het maken van een actiegroep](action-groups.md) om een e-mail-, webhook- of runbook-actie voor de waarschuwingsregel te configureren. 

## <a name="limits-summary"></a>Overzicht van limieten

Er zijn een aantal aanvullende Log Analytics-limieten, waarvan sommige afhankelijk zijn van de prijscategorie Log Analytics. Deze zijn [hier](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)gedocumenteerd.


## <a name="next-steps"></a>Volgende stappen

- Zie [Zoeken in Azure-monitorlogboeken voor](../log-query/log-query-overview.md) meer informatie over het gebruik van de zoektaal. U kunt zoekquery’s gebruiken om aanvullende analyses uit te voeren op de gebruiksgegevens.
- Gebruik de stappen in [Een nieuwe logboekwaarschuwing maken](alerts-metric.md) om een melding te krijgen wanneer aan een zoekcriterium wordt voldaan.
- Gebruik [oplossingstargeting](../insights/solution-targeting.md) om gegevens te verzamelen van alleen vereiste groepen computers.
- Als u een effectief beleid voor het verzamelen van gebeurtenissen wilt configureren, controleert u [het filterbeleid van Azure Security Center](../../security-center/security-center-enable-data-collection.md).
- Wijzig de [prestatiemeteritemconfiguratie](data-sources-performance-counters.md).
- Als u de instellingen voor het verzamelen van gebeurtenissen wilt wijzigen, controleert u [de configuratie van het gebeurtenislogboek](data-sources-windows-events.md).
- Als u de instellingen van uw syslog-verzameling wilt wijzigen, controleert u [de syslog-configuratie](data-sources-syslog.md).