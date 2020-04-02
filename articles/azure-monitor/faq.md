---
title: Veelgestelde vragen over Azure Monitor | Microsoft Documenten
description: Antwoorden op veelgestelde vragen over Azure Monitor.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2020
ms.openlocfilehash: 306d847c2bc5af72d37dbf8bf472a5bae63e9fd5
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528506"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Veelgestelde vragen over Azure-monitor

Deze veelgestelde vragen van Microsoft is een lijst met veelgestelde vragen over Azure Monitor.

## <a name="general"></a>Algemeen

### <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?
[Azure Monitor](overview.md) is een service in Azure die prestatie- en beschikbaarheidsbewaking biedt voor toepassingen en services in Azure, andere cloudomgevingen of on-premises. Azure Monitor verzamelt gegevens uit meerdere bronnen in een gemeenschappelijk gegevensplatform waar deze kunnen worden geanalyseerd op trends en afwijkingen. Uitgebreide functies in Azure Monitor helpen u bij het snel identificeren en reageren op kritieke situaties die van invloed kunnen zijn op uw toepassing.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Wat is het verschil tussen Azure Monitor, Log Analytics en Application Insights?
In september 2018 heeft Microsoft Azure Monitor, Log Analytics en Application Insights gecombineerd tot één service om krachtige end-to-end monitoring van uw toepassingen en de onderdelen waarop ze vertrouwen te bieden. Functies in Log Analytics en Application Insights zijn niet gewijzigd, hoewel sommige functies zijn omgedoopt tot Azure Monitor om hun nieuwe scope beter weer te geven. De loggegevensengine en querytaal van Log Analytics worden nu Azure Monitor Logs genoemd. Zie [Azure Monitor-terminologieupdates](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Wat kost Azure Monitor?
Functies van Azure Monitor die automatisch zijn ingeschakeld, zoals het verzamelen van statistieken en activiteitslogboeken, worden gratis geleverd. Er zijn kosten verbonden aan andere functies, zoals logboekquery's en waarschuwingen. Zie de [prijspagina azure monitor](https://azure.microsoft.com/pricing/details/monitor/) voor gedetailleerde prijsinformatie.

### <a name="how-do-i-enable-azure-monitor"></a>Hoe schakel ik Azure Monitor in?
Azure Monitor is ingeschakeld op het moment dat u een nieuw Azure-abonnement maakt en [activiteitslogboek-](platform/activity-logs-overview.md) en [platformstatistieken](platform/data-platform-metrics.md) worden automatisch verzameld. Maak [diagnostische instellingen](platform/diagnostic-settings.md) om meer gedetailleerde informatie over de werking van uw Azure-resources te verzamelen en voeg [bewakingsoplossingen](insights/solutions.md) en [-inzichten](insights/insights-overview.md) toe om aanvullende analyses te bieden over verzamelde gegevens voor bepaalde services. 

### <a name="how-do-i-access-azure-monitor"></a>Hoe krijg ik toegang tot Azure Monitor?
Toegang tot alle Azure Monitor-functies en -gegevens in het menu **Monitor** in de Azure-portal. De sectie **Controle** van het menu voor verschillende Azure-services biedt toegang tot dezelfde hulpprogramma's met gegevens die naar een bepaalde bron zijn gefilterd. Azure Monitor-gegevens zijn ook toegankelijk voor verschillende scenario's met CLI, PowerShell en een REST-API.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Is er een on-premises versie van Azure Monitor?
Nee. Azure Monitor is een schaalbare cloudservice die grote hoeveelheden gegevens verwerkt en opslaat, hoewel Azure Monitor resources kan controleren die on-premises en in andere clouds zijn.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Kan Azure Monitor on-premises resources controleren?
Ja, azure monitor kan niet alleen bewakingsgegevens uit Azure-bronnen verzamelen, maar ook gegevens verzamelen van virtuele machines en toepassingen in andere clouds en on-premises. Zie [Bronnen van bewakingsgegevens voor Azure Monitor](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Integreert Azure Monitor met System Center Operations Manager?
U uw bestaande beheergroep System Center Operations Manager verbinden met Azure Monitor om gegevens van agents te verzamelen in Azure Monitor-logboeken. Hiermee u logboekquery's en oplossingen gebruiken om gegevens die van agents zijn verzameld, te analyseren. U ook bestaande System Center Operations Manager-agents configureren om gegevens rechtstreeks naar Azure Monitor te verzenden. Zie [Operations Manager verbinden met Azure Monitor](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Welke IP-adressen gebruikt Azure Monitor?
Zie [IP-adressen die worden gebruikt door Application Insights en Log Analytics](app/ip-addresses.md) voor een lijst met de IP-adressen en poorten die nodig zijn voor agents en andere externe bronnen om toegang te krijgen tot Azure Monitor. 

## <a name="monitoring-data"></a>Bewakingsgegevens

### <a name="where-does-azure-monitor-get-its-data"></a>Waar haalt Azure Monitor zijn gegevens vandaan?
Azure Monitor verzamelt gegevens uit verschillende bronnen, waaronder logboeken en statistieken van Azure-platform en -bronnen, aangepaste toepassingen en agents die op virtuele machines worden uitgevoerd. Andere services zoals Azure Security Center en Network Watcher verzamelen gegevens in een Log Analytics-werkruimte, zodat deze kunnen worden geanalyseerd met Azure Monitor-gegevens. U ook aangepaste gegevens naar Azure Monitor verzenden met behulp van de REST-API voor logboeken of statistieken. Zie [Bronnen van bewakingsgegevens voor Azure Monitor](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Welke gegevens worden verzameld door Azure Monitor? 
Azure Monitor verzamelt gegevens uit verschillende bronnen in [logboeken](platform/data-platform-logs.md) of [statistieken.](platform/data-platform-metrics.md) Elk type gegevens heeft zijn eigen relatieve voordelen en elk ondersteunt een bepaalde set functies in Azure Monitor. Er is één metrische database voor elk Azure-abonnement, terwijl u meerdere Log Analytics-werkruimten maken om logboeken te verzamelen, afhankelijk van uw vereisten. Zie [Azure Monitor-gegevensplatform](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Is er een maximale hoeveelheid gegevens die ik kan verzamelen in Azure Monitor?
Er is geen limiet aan de hoeveelheid metrische gegevens die u verzamelen, maar deze gegevens worden maximaal 93 dagen opgeslagen. Zie [Behoud van statistieken](platform/data-platform-metrics.md#retention-of-metrics). Er is geen limiet aan de hoeveelheid logboekgegevens die u verzamelen, maar dit kan worden beïnvloed door de prijscategorie die u kiest voor de werkruimte Log Analytics. Zie [prijsdetails](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Hoe krijg ik toegang tot gegevens die zijn verzameld door Azure Monitor?
Inzichten en oplossingen bieden een aangepaste ervaring voor het werken met gegevens die zijn opgeslagen in Azure Monitor. U rechtstreeks met loggegevens werken met behulp van een logboekquery geschreven in Kusto Query Language (KQL). In de Azure-portal u query's schrijven en uitvoeren en gegevens interactief analyseren met Behulp van Log Analytics. Analyseer statistieken in de Azure-portal met de Metrics Explorer. Zie [Logboekgegevens analyseren in Azure Monitor](log-query/log-query-overview.md) en Aan de slag met Azure Metrics [Explorer](platform/metrics-getting-started.md).

## <a name="solutions-and-insights"></a>Oplossingen en inzichten

### <a name="what-is-an-insight-in-azure-monitor"></a>Wat is inzicht in Azure Monitor?
Inzichten bieden een aangepaste monitoringervaring voor bepaalde Azure-services. Ze gebruiken dezelfde statistieken en logboeken als andere functies in Azure Monitor, maar kunnen extra gegevens verzamelen en een unieke ervaring bieden in de Azure-portal. Zie [Insights in Azure Monitor](insights/insights-overview.md).

Zie het gedeelte **Inzichten** van het menu **Monitor** of **Het gedeelte Controle** van het menu van de service om inzichten in de Azure-portal weer te geven.

### <a name="what-is-a-solution-in-azure-monitor"></a>Wat is een oplossing in Azure Monitor?
Monitoringoplossingen zijn verpakte sets logica voor het bewaken van een bepaalde toepassing of service op basis van Azure Monitor-functies. Ze verzamelen logboekgegevens in Azure Monitor en bieden logboekquery's en -weergaven voor hun analyse met behulp van een algemene ervaring in de Azure-portal. Zie [Bewakingsoplossingen in Azure Monitor](insights/solutions.md).

Als u oplossingen in de Azure-portal wilt weergeven, klikt u op **Meer** in het gedeelte **Inzichten** van het menu **Monitor.** Klik **op Toevoegen** om extra oplossingen aan de werkruimte toe te voegen.

## <a name="logs"></a>Logboeken

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Wat is het verschil tussen Azure Monitor Logs en Azure Data Explorer?
Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. Azure Monitor Logs is gebouwd bovenop Azure Data Explorer en gebruikt dezelfde Kusto Query Language (KQL) met enkele kleine verschillen. Zie [taalverschillen in querytaal van Azure Monitor- logboekquery .](log-query/data-explorer-difference.md)

### <a name="how-do-i-retrieve-log-data"></a>Hoe kan ik loggegevens ophalen?
Alle gegevens worden opgehaald uit een Log Analytics-werkruimte met behulp van een logboekquery die is geschreven met Kusto Query Language (KQL). U uw eigen query's schrijven of oplossingen en inzichten gebruiken die logboekquery's voor een bepaalde toepassing of service bevatten. Zie [Overzicht van logboekquery's in Azure Monitor](log-query/log-query-overview.md).

### <a name="what-is-a-log-analytics-workspace"></a>Wat is een Log Analytics-werkruimte?
Alle logboekgegevens die door Azure Monitor worden verzameld, worden opgeslagen in een werkruimte log Analytics. Een werkruimte is in wezen een container waar loggegevens worden verzameld uit verschillende bronnen. U hebt mogelijk één Log Analytics-werkruimte voor al uw bewakingsgegevens of hebt mogelijk vereisten voor meerdere werkruimten. Zie [Het ontwerpen van de implementatie van Azure Monitor Logs](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>u een bestaande Log Analytics-werkruimte verplaatsen naar een ander Azure-abonnement?
U een werkruimte verplaatsen tussen resourcegroepen of abonnementen, maar niet naar een andere regio. Zie [Een werkruimte logboekanalyse verplaatsen naar een ander abonnement of resourcegroep](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Waarom kan ik queryverkenneren en opslaanknoppen niet zien in Log Analytics?

**Queryverkenner**, **Opslaan** en **Nieuwe waarschuwingsregelknoppen** zijn niet beschikbaar wanneer het [querybereik](log-query/scope.md) is ingesteld op een specifieke bron. Als u waarschuwingen wilt maken, een query wilt opslaan of laden, moet Log Analytics worden uitgevoerd in een werkruimte. Als u Log Analytics wilt openen in de context van de werkruimte, selecteert u **Logboeken** in het menu **Azure Monitor.** De laatst gebruikte werkruimte is geselecteerd, maar u een andere werkruimte selecteren. Zie [Querybereik en -tijdsbereik van logboeken in Azure Monitor Log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Waarom krijg ik de foutmelding: 'Registreer resourceprovider 'Microsoft.Insights' voor dit abonnement om deze query in te schakelen bij het openen van Log Analytics vanaf een VM? 
Veel resourceproviders worden automatisch geregistreerd, maar het kan nodig zijn om sommige resourceproviders handmatig te registreren. De ruimte voor registratie is altijd het abonnement. Zie [Resourceproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) voor meer informatie.

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Waarom krijg ik geen foutbericht voor toegang wanneer ik Log Analytics van een vm open? 
Als u VM-logboeken wilt weergeven, moet u met leestoestemming worden verleend aan de werkruimten die de VM-logboeken opslaan. In deze gevallen moet uw beheerder u toestemming geven voor machtigingen in Azure.

## <a name="alerts"></a>Waarschuwingen

### <a name="what-is-an-alert-in-azure-monitor"></a>Wat is een waarschuwing in Azure Monitor?
Waarschuwingen stellen u proactief op de hoogte wanneer belangrijke voorwaarden worden gevonden in uw bewakingsgegevens. Hiermee u problemen identificeren en aanpakken voordat de gebruikers van uw systeem deze opmerken. Er zijn meerdere soorten waarschuwingen:

- Metrische waarde - Metrische waarde overschrijdt een drempelwaarde.
- Logboekquery - Resultaten van een logboekquery komen overeen met gedefinieerde criteria.
- Activiteitenlogboek - Gebeurtenis activiteitenlogboek komt overeen met gedefinieerde criteria.
- Webtest - Resultaten van beschikbaarheidstest komen overeen met gedefinieerde criteria.


Zie [Overzicht van waarschuwingen in Microsoft Azure](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Wat is een actiegroep?
Een actiegroep is een verzameling meldingen en acties die kunnen worden geactiveerd door een waarschuwing. Meerdere waarschuwingen kunnen één actiegroep gebruiken waarmee u gebruik maken van algemene sets meldingen en acties. Zie [Actiegroepen maken en beheren in de Azure-portal](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>Wat is een actieregel?
Met een actieregel u het gedrag wijzigen van een reeks waarschuwingen die overeenkomen met een bepaalde criteria. Hiermee u dergelijke vereisten uitvoeren, zoals het uitschakelen van waarschuwingsacties tijdens een onderhoudsvenster. U ook een actiegroep toepassen op een reeks waarschuwingen in plaats van deze rechtstreeks toe te passen op de waarschuwingsregels. Zie [Actieregels](platform/alerts-action-rules.md).

## <a name="agents"></a>Agents

### <a name="does-azure-monitor-require-an-agent"></a>Heeft Azure Monitor een agent nodig?
Een agent is alleen nodig om gegevens van het besturingssysteem en workloads in virtuele machines te verzamelen. De virtuele machines kunnen zich in Azure, een andere cloudomgeving of on-premises bevinden. Zie [Overzicht van de Azure Monitor-agents](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Wat is het verschil tussen de Azure Monitor-agents?
Azure Diagnostic-extensie is voor virtuele Azure-machines en verzamelt gegevens naar Azure Monitor Metrics, Azure Storage en Azure Event Hubs. De log-analyse-agent is bedoeld voor virtuele machines in Azure, een andere cloudomgeving of on-premises en verzamelt gegevens naar Azure Monitor-logboeken. De afhankelijkheidsagent vereist de Log Analytics-agent en verzamelde procesdetails en afhankelijkheden. Zie [Overzicht van de Azure Monitor-agents](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Maakt mijn agentverkeer gebruik van mijn ExpressRoute-verbinding?
Verkeer naar Azure Monitor maakt gebruik van het ExpressRoute-circuit van Microsoft- peering. Zie [ExpressRoute documentatie](../expressroute/expressroute-faqs.md#supported-services) voor een beschrijving van de verschillende soorten ExpressRoute verkeer. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Hoe kan ik bevestigen dat de Log Analytics-agent in staat is om te communiceren met Azure Monitor?
Selecteer in het Configuratiescherm op de agentcomputer de optie **Beveiliging & Instellingen**, Microsoft Monitoring **Agent** . Onder het tabblad **Azure Log Analytics (OMS)** bevestigt een groen vinkje dat de agent in staat is om te communiceren met Azure Monitor. Een geel waarschuwingspictogram betekent dat de agent problemen ondervindt. Een veel voorkomende reden is dat de **Microsoft Monitoring Agent-service** is gestopt. Gebruik servicebeheerbeheer om de service opnieuw te starten.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Hoe voorkom ik dat de Log Analytics-agent communiceert met Azure Monitor?
Open het Configuratiescherm en selecteer **&-instellingen ,** **Microsoft Monitoring Agent,** voor agents die rechtstreeks met Log Analytics zijn verbonden. Verwijder onder het tabblad **Azure Log Analytics (OMS)** alle aangeboden werkruimten. Verwijder de computer in Systeemcentrum Operations Manager uit de lijst Met beheerde computers van Log Analytics. Operations Manager werkt de configuratie van de agent bij om niet langer te rapporteren aan Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Hoeveel gegevens worden er per agent verzonden?
De hoeveelheid gegevens die per agent wordt verzonden, is afhankelijk van:

* De oplossingen die u hebt ingeschakeld
* Het aantal logboeken en prestatietellers dat wordt verzameld
* Het volume van de gegevens in de logboeken

Zie [Gebruik en kosten beheren met Azure Monitor-logboeken](platform/manage-cost-storage.md) voor meer informatie.

Voor computers die de WireData-agent kunnen uitvoeren, gebruikt u de volgende query om te zien hoeveel gegevens worden verzonden:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Hoeveel netwerkbandbreedte wordt gebruikt door de Microsoft Management Agent (MMA) bij het verzenden van gegevens naar Azure Monitor?
Bandbreedte is een functie voor de hoeveelheid verzonden gegevens. Gegevens worden gecomprimeerd wanneer deze via het netwerk worden verzonden.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Hoe kan ik een melding krijgen wanneer het verzamelen van gegevens van de Log Analytics-agent stopt?

Gebruik de stappen die zijn beschreven in [het maken van een nieuwe logboekwaarschuwing](platform/alerts-metric.md) om op de hoogte te worden gesteld wanneer de gegevensverzameling stopt. Gebruik de volgende instellingen voor de waarschuwingsregel:

- **Waarschuwingsvoorwaarde definiëren:** geef uw logboekanalysewerkruimte op als resourcedoel.
- **Waarschuwingscriteria** 
   - **Signaalnaam:** *Zoeken naar aangepaste logboeken*
   - **Zoekopdracht:**`Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Waarschuwingslogica**: **Op basis** van het *aantal resultaten,* **voorwaarde** *groter dan* **, drempelwaarde** *0*
   - **Geëvalueerd op basis van**: **Periode (in minuten)** *30*, **Frequentie (in minuten)** *10*
- **Waarschuwingsdetails definiëren** 
   - **Naam**: *Gegevensverzameling gestopt*
   - **Ernst**: *Waarschuwing*

Geef een bestaande of nieuwe [actiegroep](platform/action-groups.md) op, zodat wanneer de logboekwaarschuwing overeenkomt met de criteria, u een melding krijgt als u een hartslag langer dan 15 minuten mist.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Wat zijn de firewallvereisten voor Azure Monitor-agents?
Zie [Netwerkfirewallvereisten](platform/log-analytics-agent.md#firewall-requirements)voor details over firewallvereisten.


## <a name="visualizations"></a>Visualisaties

### <a name="why-cant-i-see-view-designer"></a>Waarom kan ik View Designer niet zien?

View Designer is alleen beschikbaar voor gebruikers die zijn toegewezen met inzendermachtigingen of hoger in de werkruimte Log Analytics.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Configuratieproblemen
*Ik heb problemen met het opzetten van mijn:*

* [.NET app](app/asp-net-troubleshoot-no-data.md)
* [Een reeds lopende app controleren](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure-diagnose](platform/diagnostics-extension-to-application-insights.md)
* [Java-web-app](app/java-troubleshoot.md)

*Ik krijg geen gegevens van mijn server*

* [Uitzonderingen op firewall instellen](app/ip-addresses.md)
* [Een ASP.NET server instellen](app/monitor-performance-live-website-now.md)
* [Een Java-server instellen](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>Kan ik Application Insights gebruiken met ...?

* [Web-apps op een IIS-server in Azure VM- of Azure-set voor virtuele machines](app/azure-vm-vmss-apps.md)
* [Web-apps op een IIS-server - on-premises of in een vm](app/asp-net.md)
* [Java-web-apps](app/java-get-started.md)
* [Node.js-apps](app/nodejs.md)
* [Web-apps op Azure](app/azure-web-apps.md)
* [Cloudservices op Azure](app/cloudservices.md)
* [App-servers die worden uitgevoerd in Docker](app/docker.md)
* [Web-apps met één pagina](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows-bureaublad-app](app/windows-desktop.md)
* [Andere platforms](app/platforms.md)

### <a name="is-it-free"></a>Is het gratis?

Ja, voor experimenteel gebruik. In het basisprijsplan kan uw aanvraag elke maand gratis een bepaalde hoeveelheid gegevens verzenden. De gratis toelage is groot genoeg om de ontwikkeling te dekken, en het publiceren van een app voor een klein aantal gebruikers. U een limiet instellen om te voorkomen dat meer dan een bepaalde hoeveelheid gegevens wordt verwerkt.

Grotere volumes telemetrie worden in rekening gebracht door de Gb. We geven enkele tips over hoe [u uw kosten beperken.](app/pricing.md)

In het Enterprise-abonnement worden kosten in rekening gebracht voor elke dag waarop elk webserverknooppunt telemetrie verzendt. Het is geschikt als u Continuous Export op grote schaal wilt gebruiken.

[Lees het prijsplan](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Hoeveel kost het?

* Open de **pagina Gebruik en geschatte kosten** in een toepassingsstatistiekenbron. Er is een grafiek van recent gebruik. U desgevraagd een limiet voor het gegevensvolume instellen.
* Open het [Azure Billing-blad](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) om uw facturen in alle bronnen te bekijken.

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Wat wijzigt Application Insights in mijn project?
De details zijn afhankelijk van het type project. Voor een webtoepassing:

* Hiermee voegt u deze bestanden toe aan uw project:
  * ApplicationInsights.config
  * ai.js
* Installeert deze NuGet-pakketten:
  * *Application Insights API* - de belangrijkste API
  * *Application Insights API voor webtoepassingen* - gebruikt om telemetrie vanaf de server te verzenden
  * *Application Insights API voor JavaScript-toepassingen* - gebruikt om telemetrie van de client te verzenden
* De pakketten omvatten deze samenstellingen:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Hiermee voegt u items in:
  * Web.config
  * packages.config
* (Alleen nieuwe projecten - als u [Application Insights toevoegt aan een bestaand project,][start]moet u dit handmatig doen.) Hiermee voegt u fragmenten in de client- en servercode in om ze te initialiseren met de bron-id van Application Insights. In een MVC-app wordt code bijvoorbeeld ingevoegd in de\_stramienpagina Weergaven/Gedeeld/Layout.cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hoe kan ik upgraden van oudere SDK-versies?
Zie de [releasenotes](app/release-notes.md) voor de SDK die geschikt zijn voor uw type toepassing.

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Hoe kan ik wijzigen naar welke Azure-bron mijn project gegevens verzendt?
Klik in `ApplicationInsights.config` Solution Explorer met de rechtermuisknop en kies **Toepassingsinzichten bijwerken**. U de gegevens verzenden naar een bestaande of nieuwe bron in Azure. De updatewizard wijzigt de instrumentatiesleutel in ApplicationInsights.config, waarmee wordt bepaald waar de serverSDK uw gegevens verzendt. Tenzij u de selectie 'Alles bijwerken' deselecteert, wordt ook de sleutel gewijzigd waar deze in uw webpagina's wordt weergegeven.

### <a name="what-is-status-monitor"></a>Wat is Status Monitor?

Een bureaublad-app die u gebruiken in uw IIS-webserver om Application Insights in web-apps te configureren. Het verzamelt geen telemetrie: u het stoppen wanneer u een app niet configureert. 

[Meer informatie](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Welke telemetrie wordt verzameld door Application Insights?

Van serverweb-apps:

* HTTP-aanvragen
* [Afhankelijkheden](app/asp-net-dependencies.md). Oproepen naar: SQL-databases; HTTP-oproepen naar externe diensten; Azure Cosmos DB, tabel, blobopslag en wachtrij. 
* [Uitzonderingen](app/asp-net-exceptions.md) en stapelsporen.
* [Prestatiemeteritems](app/performance-counters.md) - Als u [Statusmonitor](app/monitor-performance-live-website-now.md), [Azure-bewaking voor App Services,](app/azure-web-apps.md) [Azure-controle voor vm- of virtuele machineschaalset](app/azure-vm-vmss-apps.md)of de [verzamelde schrijver van Application Insights](app/java-collectd.md)gebruikt.
* [Aangepaste gebeurtenissen en statistieken](app/api-custom-events-metrics.md) die u codeert.
* [Traceer logboeken](app/asp-net-trace-logs.md) als u de juiste verzamelprogramma configureert.

Van [client webpagina's:](app/javascript.md)

* [Aantal paginaweergaven](app/usage-overview.md)
* [AJAX belt](app/asp-net-dependencies.md) Aanvragen vanuit een lopend script.
* Belastinggegevens voor paginaweergave
* Aantal gebruikers- en sessies
* [Geverifieerde gebruikersnamen](app/api-custom-events-metrics.md#authenticated-users)

Uit andere bronnen, als u ze configureert:

* [Azure-diagnose](platform/diagnostics-extension-to-application-insights.md)
* [Importeren in Analytics](platform/data-collector-api.md)
* [Logboekanalyse](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kan ik wat telemetrie filteren of wijzigen?

Ja, in de server u schrijven:

* Telemetrieprocessor om eigenschappen te filteren of toe te voegen aan geselecteerde telemetrie-items voordat ze vanuit uw app worden verzonden.
* Telemetrie Initializer om eigenschappen toe te voegen aan alle items van telemetrie.

Meer informatie voor [ASP.NET](app/api-filtering-sampling.md) of [Java](app/java-filter-telemetry.md).

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Hoe worden gegevens over stad, land/regio en andere geolocatiegegevens berekend?

We zoeken het IP-adres (IPv4 of IPv6) van de webclient op met Behulp van [GeoLite2.](https://dev.maxmind.com/geoip/geoip2/geolite2/)

* Browser telemetrie: We verzamelen het IP-adres van de afzender.
* Servertelemetrie: de module Application Insights verzamelt het IP-adres van de client. Het wordt niet `X-Forwarded-For` verzameld als is ingesteld.
* Voor meer informatie over hoe IP-adres- en geolocatiegegevens worden verzameld in Application Insights verwijzen naar dit [artikel](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection).


U `ClientIpHeaderTelemetryInitializer` de om het IP-adres te nemen van een andere koptekst configureren. In sommige systemen wordt het bijvoorbeeld verplaatst door een proxy, load `X-Originating-IP`balancer of CDN naar . [Meer informatie](https://apmtips.com/blog/2016/07/05/client-ip-address/).

U [Power BI gebruiken](app/export-power-bi.md ) om uw aanvraagtelemetrie weer te geven op een kaart.


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Hoe lang worden gegevens bewaard in de portal? Is het veilig?
Neem een kijkje op [het bewaren van gegevens en privacy][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Wat gebeurt er met de telemetrie van Application Insight wanneer een server of apparaat de verbinding met Azure verliest?

Al onze SDK's, inclusief de web SDK, omvatten "betrouwbaar transport" of "robuust transport". Wanneer de server of het apparaat de verbinding met Azure verliest, wordt telemetrie [lokaal opgeslagen op het bestandssysteem](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage) (Server SDK's) of in HTML5 Session Storage (Web SDK). De SDK zal periodiek opnieuw proberen om deze telemetrie te verzenden totdat onze inname service beschouwt het "muf" (48-uur voor logs, 30 minuten voor metrische gegevens). Verouderde telemetrie zal worden geschrapt. In sommige gevallen, zoals wanneer de lokale opslag vol is, zal opnieuw proberen niet plaatsvinden.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Kunnen persoonlijke gegevens worden verzonden in de telemetrie?

Dit is mogelijk als uw code dergelijke gegevens verzendt. Het kan ook gebeuren als variabelen in stacksporen persoonlijke gegevens bevatten. Uw ontwikkelingsteam moet risicobeoordelingen uitvoeren om ervoor te zorgen dat persoonsgegevens correct worden verwerkt. [Meer informatie over gegevensbehoud en privacy](app/data-retention-privacy.md).

**Alle** octetten van het webadres van de client worden altijd ingesteld op 0 nadat de geolocatiekenmerken zijn opgezocht.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Mijn instrumentatiesleutel is zichtbaar in mijn webpaginabron. 

* Dit is gebruikelijk bij het monitoren van oplossingen.
* Het kan niet worden gebruikt om uw gegevens te stelen.
* Het kan worden gebruikt om uw gegevens scheef te trekken of waarschuwingen te activeren.
* We hebben niet gehoord dat een klant dergelijke problemen heeft gehad.

U kunt de volgende stappen uitvoeren:

* Gebruik twee afzonderlijke instrumentatiesleutels (afzonderlijke toepassingsinzichten), voor client- en servergegevens. of
* Schrijf een proxy die in uw server wordt uitgevoerd en laat de webclient gegevens verzenden via die proxy.

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Hoe zie ik POST-gegevens in Diagnostisch zoeken?
We loggen postgegevens niet automatisch in, maar u wel een TrackTrace-oproep gebruiken: plaats de gegevens in de berichtparameter. Dit heeft een langere groottelimiet dan de limieten voor tekenreekseigenschappen, hoewel u er niet op filteren.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Moet ik één of meerdere Application Insights-bronnen gebruiken?

Gebruik één resource voor alle onderdelen of rollen in één bedrijfssysteem. Gebruik afzonderlijke bronnen voor de ontwikkeling, test en release versies, en voor onafhankelijke toepassingen.

* [Bekijk hier de discussie](app/separate-resources.md)
* [Voorbeeld - cloudservice met werknemer- en webrollen](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Hoe verander ik dynamisch de instrumentatietoets?

* [Discussie hier](app/separate-resources.md)
* [Voorbeeld - cloudservice met werknemer- en webrollen](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Wat zijn de aantal gebruikers en sessies?

* De JavaScript SDK stelt een gebruikerscookie in op de webclient, om terugkerende gebruikers te identificeren en een sessiecookie om activiteiten te groeperen.
* Als er geen script aan de clientzijde is, u [cookies instellen op de server.](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/)
* Als een echte gebruiker uw site in verschillende browsers gebruikt, of in-private/incognito browsen, of verschillende machines gebruikt, dan worden ze meer dan één keer geteld.
* Als u een ingelogde gebruiker wilt identificeren in machines en browsers, voegt u een oproep toe aan [de setAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a>Heb ik alles ingeschakeld in Application Insights?
| Wat je moet zien | Hoe het te krijgen | Waarom je het wilt |
| --- | --- | --- |
| Beschikbaarheidsgrafieken |[Webtests](app/monitor-web-app-availability.md) |Weet dat uw web-app is up |
| Server app perf: responstijden, ... |[Toepassingsinzichten toevoegen aan uw project](app/asp-net.md) of [AI-statusmonitor installeren op de server](app/monitor-performance-live-website-now.md) (of uw eigen code schrijven om afhankelijkheden bij te [houden)](app/api-custom-events-metrics.md#trackdependency) |Perf-problemen detecteren |
| Telemetrie van afhankelijkheid |[AI-statusmonitor op server installeren](app/monitor-performance-live-website-now.md) |Problemen met databases of andere externe componenten diagnosticeren |
| Stack-sporen ophalen van uitzonderingen |[TrackException-aanroepen in uw code invoegen](app/asp-net-exceptions.md) (maar sommige worden automatisch gerapporteerd) |Uitzonderingen detecteren en diagnosticeren |
| Logsporen zoeken |[Een logboekadapter toevoegen](app/asp-net-trace-logs.md) |Uitzonderingen, perf-problemen diagnosticeren |
| Basisprincipes van klantgebruik: paginaweergaven, sessies, ... |[JavaScript-initialisator in webpagina's](app/javascript.md) |Gebruiksanalyse |
| Aangepaste statistieken voor client |[Oproepen op webpagina's bijhouden](app/api-custom-events-metrics.md) |Gebruikerservaring verbeteren |
| Aangepaste serverstatistieken |[Oproepen bijhouden in de server](app/api-custom-events-metrics.md) |Business intelligence |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Waarom zijn de tellingen in grafieken met zoekresultaten en statistieken ongelijk?

[Sampling](app/sampling.md) vermindert het aantal telemetrie-items (aanvragen, aangepaste gebeurtenissen, enzovoort) die daadwerkelijk vanuit uw app naar de portal worden verzonden. In Zoeken ziet u het aantal objecten dat daadwerkelijk is ontvangen. In metrische grafieken met een aantal gebeurtenissen ziet u het aantal oorspronkelijke gebeurtenissen dat heeft plaatsgevonden. 

Elk item dat wordt verzonden, bevat een `itemCount` eigenschap die aangeeft hoeveel oorspronkelijke gebeurtenissen dat item vertegenwoordigt. Als u de bemonstering in werking wilt observeren, u deze query uitvoeren in Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Automation

#### <a name="configuring-application-insights"></a>Toepassingsinzichten configureren

U [PowerShell-scripts schrijven](app/powershell.md) met Azure Resource Monitor om:

* Resources voor Application Insights maken en bijwerken.
* Stel het prijsplan in.
* Pak de instrumentatiesleutel.
* Voeg een metrische waarschuwing toe.
* Voeg een beschikbaarheidstest toe.

U geen rapport van Metric Explorer instellen of continue export instellen.

#### <a name="querying-the-telemetry"></a>De telemetrie opvragen

Gebruik de [REST API](https://dev.applicationinsights.io/) om [Analytics-query's](app/analytics.md) uit te voeren.

### <a name="how-can-i-set-an-alert-on-an-event"></a>Hoe kan ik een waarschuwing instellen voor een gebeurtenis?

Azure-waarschuwingen zijn alleen gebaseerd op metrische gegevens. Maak een aangepaste statistiek die een waardedrempel overschrijdt wanneer uw gebeurtenis plaatsvindt. Stel vervolgens een waarschuwing in op de statistiek. U krijgt een melding wanneer de statistiek de drempel in beide richtingen overschrijdt; u krijgt pas bij de eerste overschrijding een melding, ongeacht of de initiële waarde hoog of laag is; er is altijd een latentie van een paar minuten.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Zijn er kosten voor gegevensoverdracht tussen een Azure-web-app en Application Insights?

* Als uw Azure-webapp wordt gehost in een datacenter waar een eindpunt voor de verzameling Application Insights is, worden er geen kosten in rekening gebracht. 
* Als er geen verzameleindpunt in uw hostdatacenter is, worden in de telemetrie van uw app uitgaande kosten in rekening gebracht voor [Azure.](https://azure.microsoft.com/pricing/details/bandwidth/)

Dit hangt niet af van waar uw Application Insights-bron wordt gehost. Het hangt gewoon af van de verdeling van onze eindpunten.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Kan ik telemetrie naar de Application Insights-portal sturen?

We raden u aan onze SDK's te gebruiken en de [SDK API](app/api-custom-events-metrics.md)te gebruiken. Er zijn varianten van de SDK voor verschillende [platforms.](app/platforms.md) Deze SDK's verwerken buffering, compressie, beperking, nieuwe pogingen, enzovoort. Het [innameschema](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) en [het eindpuntprotocol](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) zijn echter openbaar.

### <a name="can-i-monitor-an-intranet-web-server"></a>Kan ik een intranetwebserver controleren?

Ja, maar u moet verkeer naar onze services toestaan door firewalluitzonderingen of proxyomleidingen.
- QuickPulse (QuickPulse)`https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider`https://dc.services.visualstudio.com:443` 
- Telemetriekanaal`https://dc.services.visualstudio.com:443` 


Bekijk [hier](app/ip-addresses.md)onze volledige lijst met diensten en IP-adressen.

#### <a name="firewall-exception"></a>Uitzondering op firewall

Laat uw webserver telemetrie naar onze eindpunten verzenden. 

#### <a name="gateway-redirect"></a>Gateway omleiding

Stuur verkeer van uw server naar een gateway op uw intranet door endpoints in uw configuratie te overschrijven. Als deze eigenschappen 'Eindpunt' niet aanwezig zijn in uw config, gebruiken deze klassen de onderstaande standaardwaarden in het voorbeeld ApplicationInsights.config. 

Uw gateway moet het verkeer naar het basisadres van ons eindpunt leiden. Vervang in uw configuratie de `http://<your.gateway.address>/<relative path>`standaardwaarden door .


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Voorbeeld ApplicationInsights.config met standaardeindpunten:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> ApplicationIdProvider is beschikbaar vanaf v2.6.0.



#### <a name="proxy-passthrough"></a>Proxy-doorgeefwerking

Proxy passthrough kan worden bereikt door het configureren van een machine niveau of applicatie niveau proxy.
Zie voor meer informatie het artikel van Dotnet op [DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Voorbeeld Web.config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Kan ik webtests beschikbaarheid uitvoeren op een intranetserver?

Onze [webtests](app/monitor-web-app-availability.md) worden uitgevoerd op aanwezigheidspunten die over de hele wereld worden verspreid. Er zijn twee oplossingen:

* Firewalldeur - Sta aanvragen toe aan uw server vanuit [de lange en veranderlijke lijst van webtestagents.](app/ip-addresses.md)
* Schrijf uw eigen code om periodieke verzoeken naar uw server te verzenden vanuit uw intranet. U hiervoor webtests van Visual Studio uitvoeren. De tester kan de resultaten naar Application Insights sturen met behulp van de TrackAvailability() API.

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Hoe lang duurt het voordat telemetrie wordt verzameld?

De meeste Application Insights-gegevens hebben een latentie van minder dan 5 minuten. Sommige gegevens kunnen langer duren; doorgaans grotere logbestanden. Zie de SLA [Applicatie-inzichten voor](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/)meer informatie.



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md


## <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers

Deze veelgestelde vragen van Microsoft is een lijst met veelgestelde vragen over Azure Monitor voor containers. Als u aanvullende vragen over de oplossing, ga naar de [discussie forum](https://feedback.azure.com/forums/34192--general-feedback) en post uw vragen. Wanneer een vraag vaak wordt gesteld, voegen we deze toe aan dit artikel, zodat deze snel en gemakkelijk kan worden gevonden.

### <a name="what-does-other-processes-represent-under-the-node-view"></a>Wat vertegenwoordigen *andere processen* onder de weergave Knooppunt?

**Andere processen** zijn bedoeld om u te helpen de oorzaak van het hoge resourcegebruik op uw knooppunt duidelijk te begrijpen. Hiermee u onderscheid maken tussen het gebruik tussen gecontaineriseerde processen versus niet-gecontaineriseerde processen.

Wat zijn deze **andere processen?** 

Dit zijn niet-gecontaineriseerde processen die op uw knooppunt worden uitgevoerd.  

Hoe berekenen we dit?

**Andere processen** = *Totaal gebruik van CAdvisor-gebruik* - van*containerized proces*

De **andere processen** omvatten:

- Zelfbeheerde of beheerde Kubernetes niet-gecontaineriseerde processen 

- Run-time-processen voor containers  

- Kubelet  

- Systeemprocessen die op uw knooppunt worden uitgevoerd 

- Andere niet-Kubernetes-workloads die worden uitgevoerd op nodehardware of VM 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Ik zie geen waarden van de eigenschap Afbeelding en naam ingevuld wanneer ik de containerlog-tabel opvraag.

Voor agentversie ciprod12042019 en later worden deze twee eigenschappen standaard niet ingevuld voor elke logboekregel om de kosten voor verzamelde logboekgegevens te minimaliseren. Er zijn twee opties om de tabel op te vragen die deze eigenschappen met hun waarden bevatten:

#### <a name="option-1"></a>Mogelijkheid 1 

Voeg andere tabellen toe om deze eigenschapswaarden in de resultaten op te nemen.

Wijzig uw query's om eigenschappen van ```ContainerInventory``` ImageTag uit de tabel op te nemen door deel te nemen aan de eigenschap ContainerID. U de eigenschap Name (zoals ```ContainerLog``` deze eerder in de tabel is weergegeven) opnemen in het containeName-veld van de KubepodInventory-tabel door deel te nemen aan de eigenschap ContainerID. Dit is de aanbevolen optie.

Het volgende voorbeeld is een voorbeeld van gedetailleerde query waarmee wordt uitgelegd hoe u deze veldwaarden krijgen met joins.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>Optie 2

Voor elke containerlogboeklijn opnieuw ophalen voor deze eigenschappen.

Als de eerste optie niet handig is vanwege querywijzigingen, u het ```log_collection_settings.enrich_container_logs``` verzamelen van deze velden opnieuw inschakelen door de instelling in de agentconfig-kaart in te schakelen zoals beschreven in de [configuratie-instellingen voor gegevensverzameling.](insights/container-insights-agent-config.md)

> [!NOTE]
> De tweede optie wordt niet aanbevolen met grote clusters met meer dan 50 knooppunten omdat het API-serveraanroepen genereert van elk knooppunt in het cluster om deze verrijking uit te voeren. Deze optie verhoogt ook de gegevensgrootte voor elke verzamelde logboekregel.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Kan ik statistieken bekijken die zijn verzameld in Grafana?

Azure Monitor voor containers ondersteunt weergavestatistieken die zijn opgeslagen in uw Log Analytics-werkruimte in Grafana-dashboards. We hebben een sjabloon beschikbaar gesteld die u downloaden uit de [dashboardopslagplaats](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) van Grafana om u op weg te helpen en te verwijzen om u te helpen leren hoe u aanvullende gegevens uit uw bewaakte clusters opvragen om te visualiseren in aangepaste Grafana-dashboards. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Kan ik mijn AKS-engine cluster met Azure Monitor controleren voor containers?

Azure Monitor voor containers ondersteunt monitoring containerworkloads die zijn geïmplementeerd op AKS-engine (voorheen ACS-engine) cluster(s) gehost op Azure. Zie [Azure Monitor gebruiken voor containers voor AKS-engine voor](https://github.com/microsoft/OMS-docker/tree/aks-engine)meer informatie en een overzicht van de stappen die nodig zijn om monitoring voor dit scenario mogelijk te maken.

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Waarom zie ik geen gegevens in mijn Log Analytics-werkruimte?

Als u dagelijks geen gegevens in de werkruimte Log Analytics op een bepaald tijdstip zien, hebt u mogelijk de standaardlimiet van 500 MB of de dagelijkse limiet bereikt om de hoeveelheid gegevens te bepalen die dagelijks moet worden verzameld. Wanneer de limiet voor de dag wordt bereikt, stopt en wordt het verzamelen van gegevens alleen op de volgende dag hervat. Zie [Gegevensgebruik en kosten](platform/manage-cost-storage.md)van logboekgegevens als u uw gegevensgebruik wilt bekijken en bijwerken naar een andere prijscategorie op basis van uw verwachte gebruikspatronen. 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Wat zijn de containerstatussen die zijn opgegeven in de tabel Containervoorraad?

De tabel ContainerInventory bevat informatie over zowel gestopte als lopende containers. De tabel wordt ingevuld door een werkstroom in de agent die de docker opvraagt voor alle containers (uitgevoerd en gestopt) en doorstuurt die gegevens de werkruimte Log Analytics.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Hoe los ik *registratiefout bij ontbrekend abonnement op?*

Als u de **registratie voor ontbrekende abonnementen voor Microsoft.OperationsManagement**ontvangt, u deze oplossen door de bronprovider **Microsoft.OperationsManagement** te registreren in het abonnement waarin de werkruimte is gedefinieerd. De documentatie voor hoe dit te doen is [hier](../azure-resource-manager/templates/error-register-resource-provider.md)te vinden.

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Is er ondersteuning voor RBAC-enabled AKS-clusters?

De Container Monitoring-oplossing biedt geen ondersteuning voor RBAC, maar wordt ondersteund met Azure Monitor for Containers. De pagina met oplossingsdetails bevat mogelijk niet de juiste informatie in de blades die gegevens voor deze clusters weergeven.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Hoe schakel ik logboekverzameling in voor containers in de naamruimte van het Kube-systeem via Helm?

De logboekverzameling uit containers in de naamruimte van het kube-systeem is standaard uitgeschakeld. Logboekverzameling kan worden ingeschakeld door een omgevingsvariabele in te stellen op de omsagent. Zie de [GitHub-pagina Azure Monitor voor containers voor](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) meer informatie. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Hoe werk ik de omsagent bij naar de nieuwste versie?

Zie [Agentbeheer](insights/container-insights-manage-agent.md)voor meer informatie over het upgraden van de agent.

### <a name="how-do-i-enable-multi-line-logging"></a>Hoe schakel ik multi-line logging in?

Momenteel biedt Azure Monitor voor containers geen ondersteuning voor multiline-logboekregistratie, maar er zijn oplossingen beschikbaar. U alle services configureren om te schrijven in JSON-indeling en vervolgens schrijft Docker/Moby ze als één regel.

U uw logboek bijvoorbeeld omwikkelen als een JSON-object zoals in het onderstaande voorbeeld voor een voorbeeldnode.js-toepassing:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Deze gegevens zien eruit als het volgende voorbeeld in Azure Monitor voor logboeken wanneer u er om vraagt:

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Voor een gedetailleerde blik op het probleem, bekijk de volgende [GitHub link](https://github.com/moby/moby/issues/22920).

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Hoe los ik Azure AD-fouten op wanneer ik live logboeken inschakel? 

Mogelijk ziet u de volgende fout: **de in de aanvraag opgegeven antwoord-url komt niet\>overeen met de voor de toepassing geconfigureerde antwoordurl's: '<application ID '**. De oplossing om deze op te lossen is te vinden in het artikel [Hoe containergegevens in realtime te bekijken met Azure Monitor voor containers.](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication) 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Waarom kan ik het cluster niet upgraden na het instappen?

Als u Azure Monitor voor containers voor een AKS-cluster hebt ingeschakeld, verwijdert u de Log Analytics-werkruimte waarnaar het cluster zijn gegevens heeft verzonden, wanneer u het cluster probeert te upgraden. Om dit mogelijk te maken, moet u de bewaking uitschakelen en vervolgens opnieuw verwijzen naar een andere geldige werkruimte in uw abonnement. Wanneer u de clusterupgrade opnieuw probeert uit te voeren, moet deze worden verwerkt en voltooid.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Welke poorten en domeinen moet ik openen/whitelisten voor de agent?

Zie de vereisten voor [netwerkfirewalls](insights/container-insights-onboard.md#network-firewall-requirements) voor de proxy- en firewallconfiguratiegegevens die vereist zijn voor de containeragent met Azure, Azure US Government en Azure China 21Vianet-clouds.

## <a name="azure-monitor-for-vms"></a>Azure Monitor voor virtuele machines
Deze veelgestelde vragen van Microsoft is een lijst met veelgestelde vragen over Azure Monitor voor VM's. Als u aanvullende vragen over de oplossing, ga naar de [discussie forum](https://feedback.azure.com/forums/34192--general-feedback) en post uw vragen. Wanneer een vraag vaak wordt gesteld, voegen we deze toe aan dit artikel, zodat deze snel en gemakkelijk kan worden gevonden.

### <a name="can-i-onboard-to-an-existing-workspace"></a>Kan ik aan boord gaan naar een bestaande werkruimte?
Als uw virtuele machines al zijn verbonden met een Log Analytics-werkruimte, u die werkruimte blijven gebruiken bij het instappen naar Azure Monitor voor VM's, op voorwaarde dat deze zich in een van de ondersteunde regio's [bevindt](insights/vminsights-enable-overview.md#prerequisites)die hier worden vermeld.


### <a name="can-i-onboard-to-a-new-workspace"></a>Kan ik aan boord gaan naar een nieuwe werkruimte? 
Als uw VM's momenteel niet zijn verbonden met een bestaande Log Analytics-werkruimte, moet u een nieuwe werkruimte maken om uw gegevens op te slaan. Het maken van een nieuwe standaardwerkruimte gebeurt automatisch als u één Azure VM voor Azure Monitor voor VM's configureert via de Azure-portal.

Als u ervoor kiest de op scripts gebaseerde methode te gebruiken, worden deze stappen behandeld in de [Azure Monitor for VM's inschakelen met Azure PowerShell of resourcebeheersjabloonartikel.](insights/vminsights-enable-at-scale-powershell.md) 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Wat moet ik doen als mijn vm al rapporteert aan een bestaande werkruimte?
Als u al gegevens van uw virtuele machines verzamelt, hebt u deze mogelijk al geconfigureerd om gegevens te rapporteren aan een bestaande Log Analytics-werkruimte.  Zolang die werkruimte zich in een van onze ondersteunde regio's bevindt, u Azure Monitor voor VM's inschakelen voor die reeds bestaande werkruimte.  Als de werkruimte die u al gebruikt zich niet in een van onze ondersteunde regio's bevindt, u op dit moment niet aan boord gaan van Azure Monitor voor VM's.  We zijn actief bezig om extra regio's te ondersteunen.


### <a name="why-did-my-vm-fail-to-onboard"></a>Waarom is mijn VM niet aan boord gekomen?
Bij het inwerken van een Azure VM vanuit de Azure-portal worden de volgende stappen uitgevoerd:

* Er wordt een standaard werkruimte Log Analytics gemaakt als die optie is geselecteerd.
* De log-analyse-agent is geïnstalleerd op Azure VM's met behulp van een VM-extensie, indien bepaald dat dit vereist is.  
* De Azure Monitor for VMs Map Dependency-agent is geïnstalleerd op Azure VM's met behulp van een extensie, indien bepaald. 

Tijdens het proces aan boord controleren we op status op elk van de bovenstaande om een meldingsstatus aan u terug te geven in de portal. De configuratie van de werkruimte en de installatie van de agent duurt doorgaans 5 tot 10 minuten. Het bekijken van bewakingsgegevens in de portal duurt 5 tot 10 minuten extra.  

Als u onboarding hebt gestart en berichten ziet die aangeven dat de VM aan boord moet worden genomen, u de VM maximaal 30 minuten de tijd geven om het proces te voltooien. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Ik zie geen gegevens of gegevens in de prestatiegrafieken voor mijn VM
Onze prestatiegrafieken zijn bijgewerkt om gegevens te gebruiken die zijn opgeslagen in de tabel *InsightsMetrics.*  Als u gegevens in deze grafieken wilt zien, moet u upgraden om de nieuwe VM Insights-oplossing te gebruiken.  Raadpleeg onze [GA FAQ](insights/vminsights-ga-release-faq.md) voor meer informatie.

Als u prestatiegegevens niet ziet in de schijftabel of in sommige prestatiegrafieken, worden de prestatiemeteritems mogelijk niet geconfigureerd in de werkruimte. Voer het volgende [PowerShell-script](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell)uit om het volgende PowerShell-script uit te voeren.


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Waarin verschilt de functie Azure Monitor for VM's Map van Service Map?
De functie Kaart van Azure Monitor voor VM's is gebaseerd op servicekaart, maar heeft de volgende verschillen:

* De kaartweergave is toegankelijk vanaf het VM-blad en vanuit Azure Monitor voor VM's onder Azure Monitor.
* De verbindingen in de kaart zijn nu klikbaar en geven een weergave weer van de verbindingsmetrische gegevens in het zijpaneel voor de geselecteerde verbinding.
* Er is een nieuwe API die wordt gebruikt om de kaarten te maken om complexere kaarten beter te ondersteunen.
* Bewaakte VM's zijn nu opgenomen in het clientgroepsknooppunt en de donutgrafiek toont het aandeel bewaakte versus niet-bewaakte virtuele machines in de groep.  Het kan ook worden gebruikt om de lijst met machines te filteren wanneer de groep wordt uitgebreid.
* Bewaakte virtuele machines zijn nu opgenomen in de knooppunten van de serverpoortgroep en de donutgrafiek toont het aandeel bewaakte versus niet-bewaakte machines in de groep.  Het kan ook worden gebruikt om de lijst met machines te filteren wanneer de groep wordt uitgebreid.
* De kaartstijl is bijgewerkt om meer in overeenstemming te zijn met app-kaart van toepassingsinzichten.
* De zijpanelen zijn bijgewerkt en hebben niet de volledige set integratie's die werden ondersteund in Service Map - Update Management, Change Tracking, Security en Service Desk. 
* De optie voor het kiezen van groepen en machines om in kaart te brengen is bijgewerkt en ondersteunt nu abonnementen, resourcegroepen, azure-eenvoudigmaquettesets voor virtuele machines en Cloudservices.
* U geen nieuwe machinegroepen voor servicetoewijzingmaken in de functie Azure Monitor for VM's-kaart.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Waarom worden in mijn prestatiegrafieken stippellijnen weergegeven?
Dit kan om een paar redenen gebeuren.  In gevallen waarin er een gat in het verzamelen van gegevens zijn, geven we de lijnen af als gestippeld.  Als u de frequentie van de gegevensbemonstering hebt gewijzigd voor de ingeschakelde prestatiemeteritems (de standaardinstelling is om elke 60 seconden gegevens te verzamelen), u stippellijnen in de grafiek zien als u een smal tijdsbereik voor de grafiek kiest en uw bemonsteringsfrequentie kleiner is dan de emmergrootte die in de grafiek wordt gebruikt (bijvoorbeeld de bemonsteringsfrequentie is elke 10 minuten en elke emmer in de grafiek is 5 minuten).  Als u een breder tijdsbereik kiest om te bekijken, worden de grafieklijnen in dit geval weergegeven als effen lijnen in plaats van puntjes.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Worden groepen ondersteund met Azure Monitor voor VM's?
Ja, zodra u de afhankelijkheidsagent installeert, verzamelen we informatie uit de VM's om groepen weer te geven op basis van abonnement, resourcegroep, virtuele machineschaalsets en cloudservices.  Als u Service Map hebt gebruikt en machinegroepen hebt gemaakt, worden deze ook weergegeven.  Computergroepen worden ook weergegeven in het groepenfilter als u ze hebt gemaakt voor de werkruimte die u bekijkt. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hoe zie ik de details voor wat de 95e percentiellijn in de totale prestatiegrafieken aandrijft?
Standaard wordt de lijst gesorteerd om u de VM's te laten zien die de hoogste waarde hebben voor het 95e percentiel voor de geselecteerde statistiek, met uitzondering van het diagram Beschikbaar geheugen, waarin de machines met de laagste waarde van het 5e percentiel worden weergegeven.  Als u op de grafiek klikt, opent u de **toplijstweergave** met de juiste statistiek geselecteerd.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Hoe verwerkt de functie Kaart dubbele IP's over verschillende videonetten en subnetten?
Als u IP-bereiken dupliceert met VM's of Azure-schaalsets voor virtuele machines voor subnetten en vnets, kan azure-monitor voor VM's-toewijzing onjuiste informatie weergeven. Dit is een bekend probleem en we onderzoeken opties om deze ervaring te verbeteren.

### <a name="does-map-feature-support-ipv6"></a>Ondersteunt kaartfunctie IPv6?
Kaartfunctie ondersteunt momenteel alleen IPv4 en we onderzoeken ondersteuning voor IPv6. We ondersteunen ook IPv4 dat is getunneld in IPv6.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Wanneer ik een kaart laad voor een resourcegroep of andere grote groep, is de kaart moeilijk te bekijken
Hoewel we verbeteringen hebben aangebracht in Map om grote en complexe configuraties te verwerken, realiseren we ons dat een kaart veel knooppunten, verbindingen en knooppunt kan hebben die als cluster werken.  We doen er alles aan om de ondersteuning te blijven verbeteren om de schaalbaarheid te vergroten.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Waarom ziet de netwerkgrafiek op het tabblad Prestaties er anders uit dan de netwerkgrafiek op de pagina Azure VM-overzicht?

Op de overzichtspagina voor een Azure VM worden grafieken weergegeven op basis van de meting van de activiteit van de host in de gast-VM.  Voor de netwerkgrafiek in het Azure VM-overzicht wordt alleen netwerkverkeer weergegeven dat wordt gefactureerd.  Dit geldt niet voor intervirtueel netwerkverkeer.  De gegevens en grafieken die voor Azure Monitor voor VM's worden weergegeven, zijn gebaseerd op gegevens van de gast-VM en in het netwerkdiagram worden al het TCP/IP-verkeer weergegeven dat binnen- en uitgaand is naar die VM, inclusief intervirtueel netwerk.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Hoe wordt de responstijd gemeten voor gegevens die zijn opgeslagen in VMConnection en worden weergegeven in het verbindingspaneel en de werkmappen?

Reactietijd is een benadering. Aangezien we de code van de toepassing niet instrumenteren, weten we niet echt wanneer een aanvraag begint en wanneer het antwoord binnenkomt. In plaats daarvan zien we dat gegevens worden verzonden op een verbinding en vervolgens gegevens die terugkomen op die verbinding. Onze agent houdt deze verzendingen en ontvangt en pogingen bij om ze te koppelen: een reeks verzendingen, gevolgd door een reeks ontvangsten wordt geïnterpreteerd als een verzoek/antwoordpaar. De timing tussen deze bewerkingen is de reactietijd. Het omvat de netwerklatentie en de serververwerkingstijd.

Deze benadering werkt goed voor protocollen die op aanvraag/antwoord zijn gebaseerd: er wordt één verzoek op de verbinding uitgevoerd en er komt één antwoord binnen. Dit is het geval voor HTTP(S) (zonder pipelining), maar niet tevreden voor andere protocollen.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Zijn hun beperkingen als ik op de Log Analytics Gratis prijsplan?
Als u Azure Monitor hebt geconfigureerd met een Log Analytics-werkruimte met de *gratis* prijscategorie, biedt azure monitor voor vms-kaartfunctie slechts ondersteuning voor vijf verbonden machines die zijn aangesloten op de werkruimte. Als u vijf VM's hebt aangesloten op een vrije werkruimte, koppelt u een van de VM's los en sluit u later een nieuwe vm aan, wordt de nieuwe vm niet gecontroleerd en weergegeven op de kaartpagina.  

Onder deze voorwaarde wordt u gevraagd met de optie **Nu proberen** wanneer u de virtuele machine opent en **Inzichten** selecteert in het linkerdeelvenster, zelfs nadat deze al op de vm is geïnstalleerd.  U wordt echter niet gevraagd met opties zoals normaal gesproken zou gebeuren als deze VM niet was aangesloten op Azure Monitor voor VM's. 


## <a name="next-steps"></a>Volgende stappen
Als uw vraag hier niet wordt beantwoord, u naar de volgende forums verwijzen naar aanvullende vragen en antwoorden.

- [Logboekanalyse](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

Ga voor algemene feedback over Azure Monitor naar het [feedbackforum.](https://feedback.azure.com/forums/34192--general-feedback)
