---
title: Gegevens van Azure Application Insights-app weergeven | Microsoft Documenten
description: U de oplossing Application Insights Connector gebruiken om prestatieproblemen te diagnosticeren en te begrijpen wat gebruikers met uw app doen wanneer deze worden gecontroleerd met Application Insights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c143d8aa24d3479f4619ea2c220d4a0c593f9cb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665150"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Application Insights Connector-beheeroplossing (afgeschaft)

![Toepassingsinzichtensymbool](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Met de ondersteuning van [cross-resource query's](../../azure-monitor/log-query/cross-workspace-query.md)is de Application Insights Connector-beheeroplossing niet langer nodig. Het is afgeschaft en verwijderd van Azure Marketplace, samen met de OMS-portal die officieel is afgeschaft op 15 januari 2019 voor azure-commerciële cloud. Het wordt op 30 maart 2019 teruggetrokken voor azure us government cloud.
>
>Bestaande verbindingen blijven werken tot 30 juni 2019.  Met OMS-portalafschaffing is er geen manier om bestaande verbindingen van de portal te configureren en te verwijderen. Zie [De connector met PowerShell](#removing-the-connector-with-powershell) hieronder verwijderen voor een script over het gebruik van PowerShell om bestaande verbindingen te verwijderen.
>
>Zie [Meerdere Azure Monitor Application Insights-bronnen verenigen](../log-query/unify-app-resource-data.md)voor richtlijnen voor het opvragen van loggegevens van Application Insights voor meerdere toepassingen. Zie OMS-portal verplaatsen naar Azure voor meer informatie over de afkoopsom van de [OMS-portal.](../../azure-monitor/platform/oms-portal-transition.md)
>
> 

De Oplossing voor Applications Insights Connector helpt u prestatieproblemen te diagnosticeren en te begrijpen wat gebruikers met uw app doen wanneer deze wordt gecontroleerd met [Application Insights.](../../azure-monitor/app/app-insights-overview.md) Weergaven van dezelfde toepassingstelemetrie die ontwikkelaars zien in Application Insights zijn beschikbaar in Log Analytics. Wanneer u uw Application Insights-apps echter integreert met Log Analytics, wordt de zichtbaarheid van uw toepassingen vergroot door dat de bedrijfs- en toepassingsgegevens op één plaats zijn. Als u dezelfde weergaven hebt, u samenwerken met uw app-ontwikkelaars. De gemeenschappelijke weergaven kunnen helpen de tijd te verkorten om zowel problemen met toepassingen als platforms op te sporen en op te lossen.

Wanneer u de oplossing gebruikt, u:

- Alle apps voor Application Insights op één plek weergeven, zelfs als ze zich in verschillende Azure-abonnementen bevinden
- Infrastructuurgegevens correleren met toepassingsgegevens
- Toepassingsgegevens visualiseren met perspectieven in logboekzoekopdrachten
- Draai van Log Analytics-gegevens naar de app Application Insights in de Azure-portal


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Verbonden bronnen

In tegenstelling tot de meeste andere Log Analytics-oplossingen worden gegevens niet verzameld voor de Application Insights Connector door agents. Alle gegevens die door de oplossing worden gebruikt, komen rechtstreeks uit Azure.

| Verbonden bron | Ondersteund | Beschrijving |
| --- | --- | --- |
| [Windows-agents](../../azure-monitor/platform/agent-windows.md) | Nee | De oplossing verzamelt geen informatie van Windows-agents. |
| [Linux-agents](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nee | De oplossing verzamelt geen informatie van Linux-agents. |
| [SCOM-managementgroep](../../azure-monitor/platform/om-agents.md) | Nee | De oplossing verzamelt geen informatie van agenten in een verbonden SCOM-beheergroep. |
| [Azure-opslagaccount](collect-azure-metrics-logs.md) | Nee | De oplossing haalt geen informatie uit Azure-opslag. |

## <a name="prerequisites"></a>Vereisten

- Als u toegang wilt krijgen tot de informatie van Application Insights Connector, moet u een Azure-abonnement hebben
- U moet ten minste één geconfigureerde Application Insights-bron hebben.
- U moet de eigenaar of bijdrager zijn van de application insights-bron.

## <a name="configuration"></a>Configuratie

1. Schakel de Azure Web Apps Analytics-oplossing in vanuit de [Azure-marktplaats](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) of met behulp van het proces dat is beschreven in [Log Analytics-oplossingen toevoegen vanuit de Galerie Oplossingen.](../../azure-monitor/insights/solutions.md)
2. Blader naar [Azure Portal](https://portal.azure.com). Selecteer **Alle services** om Application Insights te openen. Zoek vervolgens naar Application Insights. 
3. Selecteer **onder Abonnementen**een abonnement met toepassingsinzichten en selecteer vervolgens onder **Naam**een of meer toepassingen.
4. Klik op **Opslaan**.

In ongeveer 30 minuten worden gegevens beschikbaar en wordt de tegel Application Insights bijgewerkt met gegevens, zoals de volgende afbeelding:

![Tegel Toepassingsinzichten](./media/app-insights-connector/app-insights-tile.png)

Andere punten om in gedachten te houden:

- U application insights-apps alleen koppelen aan één Log Analytics-werkruimte.
- U [basis- of Enterprise Application Insights-bronnen](https://azure.microsoft.com/pricing/details/application-insights) alleen koppelen aan Log Analytics. U echter wel de gratis laag Log Analytics gebruiken.

## <a name="management-packs"></a>Management packs

Deze oplossing installeert geen beheerpakketten in verbonden beheergroepen.

## <a name="use-the-solution"></a>Gebruik de oplossing

In de volgende secties wordt beschreven hoe u de bladen gebruiken die in het dashboard Application Insights worden weergegeven om gegevens uit uw apps weer te geven en ermee te communiceren.

### <a name="view-application-insights-connector-information"></a>Informatie over application insights-connector weergeven

Klik op de tegel **Toepassingsinzichten** om het dashboard **Application Insights** te openen om de volgende blades te bekijken.

![Application Insights-dashboard](./media/app-insights-connector/app-insights-dash01.png)

![Application Insights-dashboard](./media/app-insights-connector/app-insights-dash02.png)

Het dashboard bevat de bladen in de tabel. Elke blade bevat maximaal tien items die overeenkomen met de criteria voor het opgegeven bereik en de duur van die blade. U een logboekzoekopdracht uitvoeren die alle records retourneert wanneer u op Alles onder aan het blad **weergeven** klikt of wanneer u op de bladekop klikt.


| **Kolom** | **Beschrijving** |
| --- | --- |
| Aanvragen - Aantal aanvragen | Hiermee wordt het aantal toepassingen weergegeven in toepassingsbronnen. Vermeldt ook toepassingsnamen en voor elk, het aantal toepassingsverslagen. Klik op het nummer om een logboekzoekopdracht uit te voeren<code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Klik op een toepassingsnaam om een logboekzoekopdracht uit te voeren voor de toepassing die toepassingsrecords per host, records op telemetrietype en alle gegevens op type (op basis van de laatste dag) weergeeft. |
| Gegevensvolume – Hosts die gegevens verzenden | Toont het aantal computerhosts dat gegevens verzendt. Bevat ook computerhosts en recordtelling voor elke host. Klik op het nummer om een logboekzoekopdracht uit te voeren<code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Klik op een computernaam om een logboekzoekopdracht uit te voeren naar de host die toepassingsrecords per host, records op telemetrietype en alle gegevens op type (op basis van de laatste dag) weergeeft. |
| Beschikbaarheid – Webtestresultaten | Hiermee wordt een ringdiagram weergegeven voor webtestresultaten, die de pas of fail aangeven. Klik op de grafiek om een logboekzoekopdracht uit te voeren<code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> De resultaten tonen het aantal passen en fouten voor alle tests. Het toont alle Web Apps met verkeer voor de laatste minuut. Klik op een toepassingsnaam om een logboekzoekopdracht weer te geven met details van mislukte webtests. |
| Serveraanvragen – Aanvragen per uur | Toont een lijndiagram van de serveraanvragen per uur voor verschillende toepassingen. Plaats de plaats boven een lijn in de grafiek om de top 3-toepassingen te zien die aanvragen voor een tijdstip ontvangen. Bevat ook een lijst met de aanvragen die aanvragen ontvangen en het aantal aanvragen voor de geselecteerde periode. <br><br>Klik op de grafiek om <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> een logboekzoekopdracht uit te voeren die een gedetailleerder lijndiagram van de serveraanvragen per uur voor verschillende toepassingen weergeeft. <br><br> Klik op een toepassing in de <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> lijst om een logboekzoekopdracht uit te voeren waarin een lijst met aanvragen, grafieken voor aanvragen in de loop van de tijd en de duur van het verzoek en een lijst met antwoordcodes voor aanvragen wordt weergegeven.   |
| Fouten : Mislukte aanvragen per uur | Toont een lijndiagram met mislukte toepassingsaanvragen per uur. Plaats de plaats in de grafiek om de top 3-toepassingen met mislukte aanvragen voor een tijdstip te bekijken. Toont ook een lijst met toepassingen met het aantal mislukte aanvragen voor elk. Klik op de grafiek om <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> een logboekzoekopdracht uit te voeren met een meer gedetailleerde lijngrafiek met mislukte toepassingsaanvragen. <br><br>Klik op een item in de <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> lijst om een logboekzoekopdracht uit te voeren waarin mislukte aanvragen, grafieken voor mislukte aanvragen in de loop van de tijd en de duur van het verzoek worden weergegeven en een lijst met mislukte antwoordcodes voor aanvragen. |
| Uitzonderingen – Uitzonderingen per uur | Toont een lijndiagram met uitzonderingen per uur. Plaats de plaats in de grafiek om de top 3-toepassingen met uitzonderingen voor een punt in de tijd te zien. Toont ook een lijst van toepassingen met het aantal uitzonderingen voor elk. Klik op de grafiek om <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> een logboekzoekopdracht uit te voeren met een meer gedetailleerde koppelingsgrafiek met uitzonderingen. <br><br>Klik op een item in de <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> lijst om een logboekzoekopdracht uit te voeren waarin een lijst met uitzonderingen, grafieken voor uitzonderingen in de loop van de tijd en mislukte aanvragen en een lijst met uitzonderingstypen wordt weergegeven.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Het perspectief Toepassingsinzichten weergeven met logboekzoekopdrachten

Wanneer u op een item in het dashboard klikt, ziet u een perspectief toepassingsstatistieken dat in de zoekresultaten wordt weergegeven. Het perspectief biedt een uitgebreide visualisatie, gebaseerd op het telemetrietype dat is geselecteerd. Dus, visualisatie inhoud verandert voor verschillende telemetrie types.

Wanneer u ergens in het mes Toepassingen klikt, ziet u het standaardperspectief **Toepassingen.**

![Perspectief toepassingen voor toepassingsinzichten](./media/app-insights-connector/applications-blade-drill-search.png)

Het perspectief toont een overzicht van de toepassing die u hebt geselecteerd.

Het **blade Beschikbaarheid** toont een andere perspectiefweergave waarin u webtestresultaten en gerelateerde mislukte aanvragen zien.

![Perspectief beschikbaarheid van toepassingsinzichten](./media/app-insights-connector/availability-blade-drill-search.png)

Wanneer u ergens klikt in de **blades Serverrequests** of **Failures,** worden de perspectiefcomponenten gewijzigd om u een visualisatie te geven die betrekking heeft op aanvragen.

![Het blade van toepassingsinzichten mislukt](./media/app-insights-connector/server-requests-failures-drill-search.png)

Wanneer u ergens in het blad **Uitzonderingen** klikt, ziet u een visualisatie die is afgestemd op uitzonderingen.

![Application Insights Exceptions blade](./media/app-insights-connector/exceptions-blade-drill-search.png)

Ongeacht of u op iets klikt in het **dashboard Application Insights Connector,** op de **zoekpagina** zelf, toont elke query terugkerende Application Insights-gegevens het perspectief van Application Insights. Als u bijvoorbeeld application insights-gegevens bekijkt, wordt in een **&#42;** query ook het perspectieftabblad weergegeven, zoals de volgende afbeelding:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Perspectiefcomponenten worden bijgewerkt, afhankelijk van de zoekopdracht. Dit betekent dat u de resultaten filteren met behulp van elk zoekveld waarmee u de gegevens zien van:

- Al uw toepassingen
- Eén geselecteerde toepassing
- Een groep aanvragen

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Draaien naar een app in de Azure-portal

Application Insights Connector-blades zijn zo ontworpen dat u draaien naar de geselecteerde App Application Insights *wanneer u de Azure-portal gebruikt.* U de oplossing gebruiken als een monitoringplatform op hoog niveau dat u helpt een app op te lossen. Wanneer u een mogelijk probleem ziet in een van uw verbonden toepassingen, u er in log Analytics-zoekopdracht ininzoomen of rechtstreeks naar de App Application Insights draaien.

Als u wilt draaien, klikt u op de ellips (**... )** die aan het einde van elke regel wordt weergegeven en selecteert u Openen **in Toepassingsinzichten**.

>[!NOTE]
>**Openen in Application Insights** is niet beschikbaar in de Azure-portal.

![Open in Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Door het voorbeeld gecorrigeerde gegevens

Application Insights biedt *[steekproeven om](../../azure-monitor/app/sampling.md)* telemetrieverkeer te verminderen. Wanneer u sampling inschakelt in uw App Voor Toepassingsinzichten, wordt een verminderd aantal vermeldingen opgeslagen, zowel in Application Insights als in Log Analytics. Hoewel de consistentie van gegevens behouden blijft in de pagina **Application Insights Connector** en -perspectieven, moet u de voorbeeldgegevens voor uw aangepaste query's handmatig corrigeren.

Hier is een voorbeeld van bemonsteringscorrectie in een zoekopdracht voor logboekzoekopdrachten:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

Het veld **Voorbeeld tellen** is aanwezig in alle vermeldingen en geeft het aantal gegevenspunten weer dat de vermelding vertegenwoordigt. Als u steekproeven inschakelt voor uw App Voor Inzichten in toepassingen, is **het aantal gesamplede gegevens** groter dan 1. Als u het werkelijke aantal items wilt tellen dat uw toepassing genereert, somt u de velden **Voorbeeldaantal op.**

De bemonstering is alleen van invloed op het totale aantal vermeldingen dat uw toepassing genereert. U hoeft de bemonstering voor metrische velden zoals **Aanvraagduur** of **Beschikbaarheidsduur** niet te corrigeren, omdat in deze velden het gemiddelde voor vertegenwoordigde items wordt weergegeven.

## <a name="input-data"></a>Invoergegevens

De oplossing ontvangt de volgende telemetrietypen van gegevens van uw verbonden Application Insights-apps:

- Beschikbaarheid
- Uitzonderingen
- Aanvragen
- Paginaweergaven : als uw werkruimte paginaweergaven wilt ontvangen, moet u uw apps configureren om die informatie te verzamelen. Zie [PageViews](../../azure-monitor/app/api-custom-events-metrics.md#page-views)voor meer informatie.
- Aangepaste gebeurtenissen : als uw werkruimte aangepaste gebeurtenissen wilt ontvangen, moet u uw apps configureren om die informatie te verzamelen. Zie [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)voor meer informatie.

Gegevens worden door Log Analytics ontvangen van Application Insights zodra deze beschikbaar zijn.

## <a name="output-data"></a>Uitvoergegevens

Voor elk type invoergegevens wordt een record gemaakt met een *type* *ApplicationInsights.* ApplicationInsights-records hebben eigenschappen die worden weergegeven in de volgende secties:

### <a name="generic-fields"></a>Algemene velden

| Eigenschap | Beschrijving |
| --- | --- |
| Type | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Tijd van de record |
| ApplicationId | Instrumentatiesleutel van de App Application Insights |
| ApplicationName | Naam van de app Application Insights |
| Rolinstantie | ID van serverhost |
| DeviceType | Clientapparaat |
| Schermresolutie |   |
| Continent | Continent waar het verzoek vandaan komt |
| Land | Land/regio waar het verzoek vandaan komt |
| Provincie | Provincie, staat of plaats waar het verzoek vandaan komt |
| Plaats | Stad of stad waar het verzoek vandaan komt |
| isSynthetisch | Geeft aan of de aanvraag is gemaakt door een gebruiker of met een geautomatiseerde methode. True = geautomatiseerde methode of false = user generated True = automated method or false = user generated True = automated method or false = user generated True |
| Bemonsteringsfrequentie | Percentage telemetrie gegenereerd door de SDK dat naar portal wordt verzonden. Bereik 0,0-100,0. |
| Voorbeeldaantal | 100/(SamplingRate). Bijvoorbeeld, 4&gt; = 25% |
| IsAuthenticated | Waar of onwaar |
| OperationID | Items met dezelfde bewerkings-id worden weergegeven als gerelateerde items in de portal. Meestal is de aanvraag-ID |
| ParentOperationID | ID van de bovenliggende bewerking |
| OperationName |   |
| Sessionid | GUID om de sessie op unieke wijze te identificeren waar de aanvraag is gemaakt |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Beschikbaarheidsspecifieke velden

| Eigenschap | Beschrijving |
| --- | --- |
| TelemetrieType | Beschikbaarheid |
| BeschikbaarheidTestName | Naam van de webtest |
| BeschikbaarheidRunLocatie | Geografische bron van http-aanvraag |
| BeschikbaarheidResultaat | Geeft het succesresultaat van de webtest aan |
| BeschikbaarheidMessage | Het bericht dat is gekoppeld aan de webtest |
| BeschikbaarheidAantal | 100/(Bemonsteringspercentage). Bijvoorbeeld, 4&gt; = 25% |
| DataSizeMetricValue | 1.0 of 0.0 |
| DataSizeMetricCount | 100/(Bemonsteringspercentage). Bijvoorbeeld, 4&gt; = 25% |
| BeschikbaarheidDuur | Tijd, in milliseconden, van de webtestduur |
| BeschikbaarheidDurationCount | 100/(Bemonsteringspercentage). Bijvoorbeeld, 4&gt; = 25% |
| BeschikbaarheidWaarde |   |
| AvailabilityMetricCount |   |
| BeschikbaarheidTestId | Unieke GUID voor de webtest |
| BeschikbaarheidTimestamp | Nauwkeurige tijdstempel van de beschikbaarheidstest |
| BeschikbaarheidDurationMin | Voor gesamplede records toont dit veld de minimale webtestduur (milliseconden) voor de weergegeven gegevenspunten |
| BeschikbaarheidDuurMax | Voor gesamplede records toont dit veld de maximale webtestduur (milliseconden) voor de weergegeven gegevenspunten |
| BeschikbaarheidDurationStdDev | Voor gesamplede records toont dit veld de standaarddeviatie tussen alle webtestduur (milliseconden) voor de weergegeven gegevenspunten |
| BeschikbaarheidMin |   |
| BeschikbaarheidMax |   |
| BeschikbaarheidStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Uitzonderingsspecifieke velden

| Type | ApplicationInsights |
| --- | --- |
| TelemetrieType | Uitzondering |
| ExceptionType | Type uitzondering |
| UitzonderingSmethode | De methode die de uitzondering maakt |
| UitzonderingMontage | Assembly omvat het framework en de versie, evenals de public key token |
| UitzonderingGroep | Type uitzondering |
| Exceptionhandledat | Geeft het niveau aan dat de uitzondering heeft verwerkt |
| Aantal uitzonderingen | 100/(Bemonsteringspercentage). Bijvoorbeeld, 4&gt; = 25% |
| ExceptionMessage | Bericht van de uitzondering |
| Uitzonderingsstapel | Volledige stapel van de uitzondering |
| ExceptionHasStack ExceptionHasStack ExceptionHasStack ExceptionHas | True, als uitzondering een stapel heeft |



### <a name="request-specific-fields"></a>Aanvraagspecifieke velden

| Eigenschap | Beschrijving |
| --- | --- |
| Type | ApplicationInsights |
| TelemetrieType | Aanvraag |
| ResponseCode | HTTP-antwoord verzonden naar client |
| VerzoekSucces | Geeft succes of mislukking aan. Waar of onwaar. |
| RequestID | ID om het verzoek uniek te identificeren |
| RequestName | GET/POST + URL-basis |
| AanvraagDuur | Tijd, in seconden, van de aanvraagduur |
| URL | URL van het verzoek, inclusief host |
| Host | Webserverhost |
| URLBase | Volledige URL van het verzoek |
| ApplicationProtocol | Type protocol dat door de toepassing wordt gebruikt |
| Aantal aanvragen | 100/(Bemonsteringspercentage). Bijvoorbeeld, 4&gt; = 25% |
| AanvraagDuuraantal | 100/(Bemonsteringspercentage). Bijvoorbeeld, 4&gt; = 25% |
| AanvraagDurationMin | Voor gesamplede records wordt in dit veld de minimale aanvraagduur (milliseconden) voor de weergegeven gegevenspunten weergegeven. |
| AanvraagDuurMax | Voor gesamplede records wordt in dit veld de maximale aanvraagduur (milliseconden) voor de weergegeven gegevenspunten weergegeven |
| AanvraagDurationStdDev | Voor gesamplede records toont dit veld de standaarddeviatie tussen alle aanvraagduur (milliseconden) voor de weergegeven gegevenspunten |

## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken

Deze oplossing heeft geen set zoekopdrachten in het voorbeeldlogboek op het dashboard. Voorbeeldlogboekquery's met beschrijvingen worden echter weergegeven in de [sectie Informatie van de View Application Insights Connector.](#view-application-insights-connector-information)

## <a name="removing-the-connector-with-powershell"></a>De connector verwijderen met PowerShell
Met OMS-portalafschaffing is er geen manier om bestaande verbindingen van de portal te configureren en te verwijderen. U bestaande verbindingen met het volgende PowerShell-script verwijderen. U moet de eigenaar of bijdrager van de werkruimte en lezer van de Bron Application Insights zijn om deze bewerking uit te voeren.

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

U een lijst met toepassingen ophalen met behulp van het volgende PowerShell-script dat een REST API-aanroep aanroept. 

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
Dit script vereist een verificatietoken voor dragers voor verificatie tegen Azure Active Directory. Een manier om dit token op te halen is door een artikel in de [REST API-documentatiesite](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate)te gebruiken. Klik **op Proberen** en meld u aan bij uw Azure-abonnement. U het token aan toonder kopiëren vanuit het **voorvertoning aanvragen** zoals weergegeven in de volgende afbeelding.


![Token aan toonder](media/app-insights-connector/bearer-token.png)


U ook een lijst met toepassingen ophalen met een logboekquery:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Volgende stappen

- Gebruik [Logboekzoeken](../../azure-monitor/log-query/log-query-overview.md) om gedetailleerde informatie voor uw Application Insights-apps weer te geven.
