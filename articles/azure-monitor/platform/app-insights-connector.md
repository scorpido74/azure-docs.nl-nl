---
title: Azure Application Insights-app-gegevens weergeven | Microsoft Docs
description: Prestatieproblemen analyseren en inzicht krijgen wat gebruikers doen met uw app bij bewaakt met Application Insights kunt u de oplossing Application Insights-Connector.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c143d8aa24d3479f4619ea2c220d4a0c593f9cb1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665150"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Application Insights-connector-beheer oplossing (afgeschaft)

![Application Insights-symbool](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Met de ondersteuning van [query's voor meerdere bronnen](../../azure-monitor/log-query/cross-workspace-query.md)is de Application Insights-connector beheer oplossing niet meer nodig. Het is afgeschaft en verwijderd uit Azure Marketplace, samen met de OMS-portal die officieel werd afgeschaft op 15 januari 2019 voor de commerciële cloud van Azure. Deze wordt ingetrokken op 30 maart 2019 voor Azure US Government-Cloud.
>
>Bestaande verbindingen blijven werken tot en met 30 juni 2019.  In de OMS-Portal is het niet mogelijk om bestaande verbindingen te configureren en te verwijderen vanuit de portal. Zie [de onderstaande connector verwijderen met Power shell](#removing-the-connector-with-powershell) voor een script in Power shell gebruiken om bestaande verbindingen te verwijderen.
>
>Zie [meerdere Azure Monitor Application Insights resources](../log-query/unify-app-resource-data.md)samen voegen voor hulp bij het opvragen van Application Insights logboek gegevens voor meerdere toepassingen. Voor meer informatie over de afschaffing van de OMS-Portal raadpleegt [u OMS Portal verplaatsen naar Azure](../../azure-monitor/platform/oms-portal-transition.md).
>
> 

De oplossing van de Application Insights-connector helpt u bij het diagnosticeren van prestatie problemen en inzicht te krijgen in wat gebruikers met uw app doen wanneer deze worden bewaakt met [Application Insights](../../azure-monitor/app/app-insights-overview.md). Weergaven van dezelfde toepassingstelemetrie die ontwikkelaars wordt weergegeven in Application Insights zijn beschikbaar in Log Analytics. Wanneer u uw Application Insights-apps met Log Analytics integreert, is echter zichtbaarheid van uw toepassingen met operationele gegevens en uw toepassing op één plek met verhoogd. Met de dezelfde weergaven, kunt u samenwerken met uw app-ontwikkelaars. De algemene weergaven kunt de tijd om te detecteren en oplossen van toepassings- en platformproblemen te beperken.

Wanneer u de oplossing gebruikt, kunt u het volgende doen:

- Al uw Application Insights-apps op één plek, weergeven, zelfs als ze zich in verschillende Azure-abonnementen
- Correleren van gegevens van de infrastructuur met toepassingsgegevens
- Toepassingsgegevens visualiseren met perspectieven in zoeken in Logboeken
- Schakel over naar uw Application Insights-app in Azure portal van Log Analytics-gegevens


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Verbonden bronnen

Gegevens niet in tegenstelling tot de meeste andere Log Analytics-oplossingen, door agents verzameld voor de Application Insights-Connector. Alle gegevens die worden gebruikt door de oplossing wordt geleverd rechtstreeks vanuit Azure.

| Verbonden bron | Ondersteund | Beschrijving |
| --- | --- | --- |
| [Windows-agents](../../azure-monitor/platform/agent-windows.md) | Nee | De oplossing verzamelt geen informatie van Windows-agents. |
| [Linux-agents](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nee | De oplossing worden geen gegevens verzameld van Linux-agents. |
| [SCOM-beheer groep](../../azure-monitor/platform/om-agents.md) | Nee | De oplossing worden geen gegevens verzameld van agents in een verbonden SCOM-beheergroep. |
| [Azure Storage-account](collect-azure-metrics-logs.md) | Nee | De oplossing doet niet Verzamelingsgegevens van Azure storage. |

## <a name="prerequisites"></a>Vereisten

- Voor toegang tot gegevens van Application Insights-Connector, moet u een Azure-abonnement hebt
- U moet ten minste één geconfigureerde Application Insights-resource hebben.
- U moet de eigenaar of bijdrager van de Application Insights-resource.

## <a name="configuration"></a>Configuratie

1. Schakel de Azure Web Apps-analyse-oplossing in via de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) of via het proces dat wordt beschreven in [log Analytics oplossingen toevoegen van de Oplossingengalerie](../../azure-monitor/insights/solutions.md).
2. Blader naar de [Azure-portal](https://portal.azure.com). Selecteer **alle services** om Application Insights te openen. Zoek vervolgens naar Application Insights. 
3. Onder **abonnementen**selecteert u een abonnement met Application Insights resources en selecteert u vervolgens een of meer toepassingen onder **naam**.
4. Klik op **Opslaan**.

Gegevens beschikbaar in ongeveer 30 minuten, en de Application Insights-tegel wordt bijgewerkt met gegevens, zoals de volgende afbeelding:

![Application Insights-tegel](./media/app-insights-connector/app-insights-tile.png)

Andere punten moet rekening houden met:

- U kunt alleen Application Insights-apps koppelen aan een Log Analytics-werkruimte.
- U kunt alleen [basis-of ondernemings Application Insights resources](https://azure.microsoft.com/pricing/details/application-insights) koppelen aan log Analytics. U kunt echter de gratis laag van Log Analytics gebruiken.

## <a name="management-packs"></a>Management packs

Deze oplossing kan niet alle management packs geïnstalleerd in de verbonden beheergroepen.

## <a name="use-the-solution"></a>De oplossing gebruiken

De volgende secties wordt beschreven hoe u de blades wordt weergegeven in de Application Insights-dashboard kunnen zien en gebruiken met gegevens van uw apps kunt gebruiken.

### <a name="view-application-insights-connector-information"></a>Application Insights-Connector-informatie weergeven

Klik op de tegel **Application Insights** om het **Application Insights** dash board te openen om de volgende Blades weer te geven.

![Application Insights-dashboard](./media/app-insights-connector/app-insights-dash01.png)

![Application Insights-dashboard](./media/app-insights-connector/app-insights-dash02.png)

Het dashboard bevat de blades weergegeven in de tabel. Elke blade bevat maximaal tien items die overeenkomen met de criteria voor het opgegeven bereik en de duur van die blade. U kunt een zoek opdracht in Logboeken uitvoeren die alle records retourneert wanneer u op **alles weer geven** onder aan de Blade klikt of wanneer u op de Blade-kop klikt.


| **Kolominvoercel** | **Beschrijving** |
| --- | --- |
| Toepassingen - aantal toepassingen | Geeft het aantal toepassingen in Application-resources. Ook een lijst met toepassingsnamen en voor elk, het aantal toepassingsrecords. Klik op het nummer om een zoek opdracht voor logboeken uit te voeren voor <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Klik op de toepassingsnaam van een om uit te voeren een zoeken in Logboeken voor de toepassing die toepassingsrecords per host, de records op telemetrietype en alle gegevens op type wordt (op basis van de laatste dag). |
| Gegevensvolume – als host fungeert voor verzenden van gegevens | Geeft het aantal computer fungeert als host die gegevens worden verzonden. Bevat ook computer fungeert als host en het aantal records voor elke host. Klik op het nummer om een zoek opdracht voor logboeken uit te voeren voor <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Klik op de naam van een computer om uit te voeren een zoeken in Logboeken voor de host waarin toepassingsrecords per host, de records op telemetrietype en alle gegevens op type (op basis van de laatste dag). |
| Beschikbaarheid – webtestresultaten | Geeft een ringdiagram voor de testresultaten web, waarmee wordt aangegeven geslaagd of mislukt. Klik op de grafiek om een logboek zoekopdracht voor <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> uit te voeren <br><br> Resultaten staat dat het aantal fasen en fouten voor alle tests. Hierin staan alle Web-Apps met verkeer voor de laatste minuut. Klik op de toepassingsnaam van een om een van de details van mislukte webtests zoeken in logboeken weer te geven. |
| Serveraanvragen – aanvragen per uur | Geeft een lijndiagram van de serveraanvragen per uur voor verschillende toepassingen. Beweeg de muisaanwijzer over een regel in de grafiek voor het ontvangen van aanvragen voor een punt in tijd top 3-toepassingen. Ook wordt een lijst van de toepassingen die zijn ontvangen van aanvragen en het aantal aanvragen voor de geselecteerde periode weergegeven. <br><br>Klik op de grafiek om een logboek zoekopdracht uit te voeren voor <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> waarin een gedetailleerd lijn diagram wordt weer gegeven van de server aanvragen per uur voor verschillende toepassingen. <br><br> Klik op een toepassing in de lijst om een logboek zoekopdracht uit te voeren voor <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> waarin een lijst met aanvragen wordt weer gegeven, grafieken voor aanvragen in de loop van de tijd en de duur van aanvragen en een lijst met antwoord codes voor aanvragen.   |
| Fouten: mislukte aanvragen per uur | Geeft een lijndiagram van mislukte aanvragen per uur. Beweeg de muisaanwijzer over de grafiek om te zien van de eerste 3 toepassingen met mislukte aanvragen voor een punt in tijd. Ook ziet u een lijst met toepassingen met het aantal mislukte aanvragen voor elk. Klik op de grafiek om een logboek zoekopdracht uit te voeren voor <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> waarin een gedetailleerd lijn diagram van mislukte toepassings aanvragen wordt weer gegeven. <br><br>Klik op een item in de lijst om een logboek zoekopdracht uit te voeren voor <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> waarin mislukte aanvragen worden weer gegeven, grafieken voor mislukte aanvragen in de loop van de tijd en de duur van aanvragen en een lijst met mislukte antwoord codes voor aanvragen. |
| Uitzonderingen: uitzonderingen per uur | Geeft een lijndiagram van uitzonderingen per uur. Beweeg de muisaanwijzer over de grafiek om te zien van de eerste 3 toepassingen met uitzonderingen voor een punt in tijd. Ook ziet u een lijst met toepassingen met het aantal uitzonderingen voor elk. Klik op de grafiek om een logboek zoekopdracht uit te voeren voor <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> waarin een gedetailleerdere koppelings grafiek met uitzonde ringen wordt weer gegeven. <br><br>Klik op een item in de lijst om een logboek zoekopdracht uit te voeren voor <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> waarin een lijst met uitzonde ringen, grafieken voor uitzonde ringen in de loop van de tijd en mislukte aanvragen en een lijst met uitzonderings typen wordt weer gegeven.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Het perspectief van de Application Insights met zoeken in Logboeken weergeven

Wanneer u een item in het dashboard klikt, ziet u een Application Insights-perspectief wordt weergegeven in het zoekvak. Het perspectief biedt een uitgebreide visualisatie, op basis van het telemetrietype die geselecteerd. Dit het geval is, visualisatie inhoudswijzigingen voor verschillende telemetrietypen.

Wanneer u op een wille keurige plaats in de Blade toepassingen klikt, wordt het perspectief standaard **toepassingen** weer geven.

![Application Insights toepassingen perspectief](./media/app-insights-connector/applications-blade-drill-search.png)

Het perspectief bevat een overzicht van de toepassing die u hebt geselecteerd.

Op de Blade voor de **Beschik baarheid** ziet u een andere perspectief weergave waarin de resultaten van webtesten en gerelateerde mislukte aanvragen worden weer gegeven.

![Application Insights-beschikbaarheid perspectief](./media/app-insights-connector/availability-blade-drill-search.png)

Wanneer u op een wille keurige plaats in de Blade **server aanvragen** of **fouten** klikt, worden de perspectief onderdelen gewijzigd om u een visualisatie te geven die betrekking heeft op aanvragen.

![Blade voor Application Insights-fouten](./media/app-insights-connector/server-requests-failures-drill-search.png)

Wanneer u op een wille keurige plaats in de Blade **uitzonde ringen** klikt, ziet u een visualisatie die is afgestemd op uitzonde ringen.

![Blade voor Application Insights-uitzonderingen](./media/app-insights-connector/exceptions-blade-drill-search.png)

Ongeacht of u op een van de **Application Insights-connector** dash board klikt, binnen de **Zoek** pagina zelf, wordt met elke query die Application Insights gegevens retourneert, het Application Insights perspectief weer gegeven. Als u bijvoorbeeld Application Insights gegevens bekijkt, wordt in een **&#42;** query ook het tabblad perspectief weer gegeven, zoals in de volgende afbeelding:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Perspectief onderdelen zijn bijgewerkt, afhankelijk van de zoekquery. Dit betekent dat u kunt de resultaten filteren met behulp van een zoekveld waarmee u beschikt over de mogelijkheid om te zien van de gegevens uit:

- Al uw toepassingen
- Een enkele geselecteerde toepassing
- Een groep van toepassingen

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Schakel over naar een app in Azure portal

Application Insights-connector Blades zijn ontworpen om u in staat te stellen om naar de geselecteerde Application Insights-app te draaien *Wanneer u de Azure Portal gebruikt*. U kunt de oplossing gebruiken als een platform voor het controleren van hoog niveau waarmee u een app op te lossen. Als u een potentieel probleem in een van uw verbonden toepassingen ziet, kunt u een van beide Zoom in op deze in Log Analytics search of u kunt draaien rechtstreeks naar de Application Insights-app.

Als u wilt draaien, klikt u op de weglatings tekens ( **...** ) die aan het einde van elke regel worden weer gegeven en selecteert u **openen in Application Insights**.

>[!NOTE]
>**Openen in Application Insights** is niet beschikbaar in de Azure Portal.

![Openen in Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Voorbeeld gecorrigeerde gegevens

Application Insights biedt een *[steekproef correctie](../../azure-monitor/app/sampling.md)* om het telemetrische verkeer te verminderen. Als u sampling voor uw app in Application Insights inschakelt, krijgt u een kleiner aantal vermeldingen die zijn opgeslagen in Application Insights en Log Analytics. Hoewel gegevens consistentie wordt bewaard op de **Application Insights-connector** pagina en perspectieven, moet u de voorbeeld gegevens voor uw aangepaste query's hand matig corrigeren.

Hier volgt een voorbeeld van de correctie van steekproeven in een logboek zoekquery:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

Het veld aantal in de **steek proef** is aanwezig in alle vermeldingen en toont het aantal gegevens punten dat de vermelding vertegenwoordigt. Als u steek proeven voor uw Application Insights-app inschakelt, is het **aantal samples** groter dan 1. Als u het werkelijke aantal vermeldingen wilt tellen dat door de toepassing wordt gegenereerd, moet u de velden voor het **aantal samples** optellen.

Steekproeven is van invloed op het totale aantal vermeldingen dat uw toepassing genereert. U hoeft geen steek proeven te corrigeren voor metrische velden, zoals **RequestDuration** of **AvailabilityDuration** , omdat deze velden het gemiddelde voor weer gegeven vermeldingen tonen.

## <a name="input-data"></a>Invoergegevens

De oplossing ontvangt de volgende telemetrietypen van de gegevens van uw verbonden apps met Application Insights:

- Beschikbaarheid
- Uitzonderingen
- Aanvragen
- Paginaweergaven – voor uw werkruimte voor het ontvangen van paginaweergaven, moet u uw apps voor het verzamelen van gegevens configureren. Zie [page views](../../azure-monitor/app/api-custom-events-metrics.md#page-views)voor meer informatie.
- Aangepaste gebeurtenissen – voor uw werkruimte voor het ontvangen van aangepaste gebeurtenissen, moet u uw apps voor het verzamelen van gegevens configureren. Zie [track Event](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)voor meer informatie.

Gegevens worden ontvangen door Log Analytics van Application Insights zodra deze beschikbaar is.

## <a name="output-data"></a>Uitvoergegevens

Er wordt een record met een *type* *ApplicationInsights* gemaakt voor elk type invoer gegevens. Application Insights-records hebben eigenschappen die worden weergegeven in de volgende secties:

### <a name="generic-fields"></a>Algemene velden

| Eigenschap | Beschrijving |
| --- | --- |
| Type | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Tijd van de record |
| ApplicationId | De instrumentatiesleutel van de Application Insights-app |
| ApplicationName | Naam van de Application Insights app |
| RoleInstance | ID van server-host |
| Apparaattype | Client-apparaat |
| ScreenResolution |   |
| Continent | Continent waarvan de aanvraag afkomstig is |
| Land | Land/regio waar de aanvraag vandaan komt |
| Provincie | Provincie, provincie of land waar de aanvraag afkomstig is |
| Plaats | Stad of plaats waar de aanvraag afkomstig is |
| isSynthetic | Geeft aan of de aanvraag is gemaakt door een gebruiker of door de geautomatiseerde methode. True = geautomatiseerde methode of onwaar = door de gebruiker gegenereerd |
| SamplingRate | Percentage van de telemetrie die is gegenereerd door de SDK die wordt verzonden naar de portal. Het bereik 0,0 100,0. |
| SampledCount | 100/(SamplingRate). Bijvoorbeeld 4 =&gt; 25% |
| IsAuthenticated | Waar of onwaar |
| OperationID | Items die dezelfde bewerking ID als betrokken Items worden weergegeven in de portal. Meestal de aanvraag-ID |
| ParentOperationID | ID van de bovenliggende bewerking |
| OperationName |   |
| sessie-id | GUID voor het aanduiden van de sessie waarin de aanvraag is gemaakt |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Beschikbaarheid-specifieke velden

| Eigenschap | Beschrijving |
| --- | --- |
| TelemetryType | Beschikbaarheid |
| AvailabilityTestName | Naam van de WebTest |
| AvailabilityRunLocation | Geografische bron van http-aanvraag |
| AvailabilityResult | Geeft aan dat het resultaat van het succes van de WebTest |
| AvailabilityMessage | Het bericht dat is gekoppeld aan de WebTest |
| AvailabilityCount | 100 /(Sampling Rate). Bijvoorbeeld 4 =&gt; 25% |
| DataSizeMetricValue | 1.0 of 0,0 |
| DataSizeMetricCount | 100 /(Sampling Rate). Bijvoorbeeld 4 =&gt; 25% |
| AvailabilityDuration | Tijd in milliseconden, van de duur van de website |
| AvailabilityDurationCount | 100 /(Sampling Rate). Bijvoorbeeld 4 =&gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | De unieke GUID voor de WebTest |
| AvailabilityTimestamp | Exacte tijdstempel van de beschikbaarheidstest |
| AvailabilityDurationMin | Dit veld geeft voor sample records weer van de minimale web testduur (in milliseconden) voor de gegevenspunten weergegeven |
| AvailabilityDurationMax | Voor sample records bevat dit veld maximale duur van de (in milliseconden) voor de gegevenspunten weergegeven |
| AvailabilityDurationStdDev | Voor sample records wordt in dit veld de standaarddeviatie tussen alle web test duur (in milliseconden) voor de gegevenspunten weergegeven |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Uitzondering-specifieke velden

| Type | ApplicationInsights |
| --- | --- |
| TelemetryType | Uitzondering |
| ExceptionType | Type van de uitzondering |
| ExceptionMethod | De methode die wordt gemaakt van de uitzondering |
| ExceptionAssembly | Assembly bevat het framework en versie, evenals het openbare-sleuteltoken |
| ExceptionGroup | Type van de uitzondering |
| ExceptionHandledAt | Geeft het niveau aan dat de uitzondering wordt verwerkt |
| ExceptionCount | 100 /(Sampling Rate). Bijvoorbeeld 4 =&gt; 25% |
| ExceptionMessage | Bericht van uitzondering |
| ExceptionStack | Volledige stack van uitzondering |
| ExceptionHasStack | Waar, als uitzondering een stack heeft |



### <a name="request-specific-fields"></a>Aanvraag-specifieke velden

| Eigenschap | Beschrijving |
| --- | --- |
| Type | ApplicationInsights |
| TelemetryType | Aanvraag |
| ResponseCode | HTTP-antwoord verzonden naar client |
| RequestSuccess | Geeft aan of geslaagd of mislukt. Waar of ONWAAR. |
| RequestID | ID voor het aanduiden van de aanvraag |
| RequestName | GET/POST + URL-basis |
| RequestDuration | Tijd in seconden van de duur van de aanvraag |
| URL | URL van de aanvraag niet met inbegrip van host |
| Host | Web server-host |
| URLBase | Volledige URL van de aanvraag |
| ApplicationProtocol | Het type protocol dat wordt gebruikt door de toepassing |
| RequestCount | 100 /(Sampling Rate). Bijvoorbeeld 4 =&gt; 25% |
| RequestDurationCount | 100 /(Sampling Rate). Bijvoorbeeld 4 =&gt; 25% |
| RequestDurationMin | Dit veld geeft de duur van de minimale aanvraag (in milliseconden) voor sample records weer voor de gegevenspunten weergegeven. |
| RequestDurationMax | Dit veld geeft voor sample records, de duur van de maximale aanvraag (in milliseconden) weer voor de gegevenspunten weergegeven |
| RequestDurationStdDev | Dit veld geeft voor sample records, de standaardafwijking tussen alle aanvraag de duur (in milliseconden) weer voor de gegevenspunten weergegeven |

## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken

Deze oplossing beschikt niet over een aantal voorbeelden van zoekopdrachten op het dashboard weergegeven. Voor beelden van zoek opdrachten in Logboeken met beschrijvingen worden echter weer gegeven in de sectie [Application Insights-connector gegevens weer geven](#view-application-insights-connector-information) .

## <a name="removing-the-connector-with-powershell"></a>De connector verwijderen met Power shell
In de OMS-Portal is het niet mogelijk om bestaande verbindingen te configureren en te verwijderen vanuit de portal. U kunt bestaande verbindingen met het volgende Power shell-script verwijderen. U moet de eigenaar of bijdrager zijn van de werk ruimte en de lezer van Application Insights resource om deze bewerking uit te voeren.

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

U kunt een lijst met toepassingen ophalen met behulp van het volgende Power shell-script dat een REST API aanroep aanroept. 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
Voor dit script is een Bearer-verificatie token vereist voor verificatie op basis van Azure Active Directory. Een manier om dit token op te halen, is het gebruik van een artikel op de [Documentatie site van rest API](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate). Klik op **Probeer het** en meld u aan bij uw Azure-abonnement. U kunt het Bearer-token kopiëren uit de preview van de **aanvraag** , zoals wordt weer gegeven in de volgende afbeelding.


![Bearer-token](media/app-insights-connector/bearer-token.png)


U kunt ook een lijst met toepassingen ophalen een logboek query gebruiken:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Volgende stappen

- Gebruik [Zoeken in Logboeken](../../azure-monitor/log-query/log-query-overview.md) om gedetailleerde informatie weer te geven voor uw Application Insights-apps.
