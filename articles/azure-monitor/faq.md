---
title: Azure Monitor Veelgestelde vragen | Microsoft Docs
description: Antwoorden op veelgestelde vragen over Azure Monitor.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/23/2020
ms.openlocfilehash: 169cc3f6a195a7339ab33eb9f00d9937451a93e0
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620440"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Veelgestelde vragen over Azure Monitor

Deze veelgestelde vragen over micro soft vindt u een lijst met veel gestelde antwoorden over Azure Monitor.

## <a name="general"></a>Algemeen

### <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?
[Azure monitor](overview.md) is een service in azure die prestatie-en beschikbaarheids bewaking biedt voor toepassingen en services in azure, andere Cloud omgevingen of on-premises. Azure Monitor verzamelt gegevens uit meerdere bronnen in een gemeen schappelijk gegevens platform waar het kan worden geanalyseerd op trends en afwijkingen. Met uitgebreide functies in Azure Monitor kunt u snel kritieke situaties identificeren en erop reageren die van invloed kunnen zijn op uw toepassing.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Wat is het verschil tussen Azure Monitor, Log Analytics en Application Insights?
In september 2018, micro soft gecombineerd Azure Monitor, Log Analytics en Application Insights in één service om een krachtige end-to-end bewaking te bieden van uw toepassingen en de onderdelen waarop ze zijn gebaseerd. Functies in Log Analytics en Application Insights zijn niet gewijzigd, maar sommige functies zijn opnieuw ingesteld op Azure Monitor om de nieuwe scope beter weer te geven. De logboek gegevens engine en query taal van Log Analytics worden nu Azure Monitor-logboeken genoemd. Zie [Azure monitor terminologie-updates](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Wat betekent Azure Monitor kosten?
Functies van Azure Monitor die automatisch worden ingeschakeld, zoals het verzamelen van metrische gegevens en activiteiten logboeken, zijn gratis. Er zijn kosten verbonden aan andere functies, zoals logboek query's en waarschuwingen. Zie de [pagina met prijzen voor Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) voor gedetailleerde prijs informatie.

### <a name="how-do-i-enable-azure-monitor"></a>Azure Monitor Hoe kan ik inschakelen?
Azure Monitor is ingeschakeld wanneer u een nieuw Azure-abonnement maakt, het [activiteiten logboek](platform/activity-logs-overview.md) en de [metrische gegevens](platform/data-platform-metrics.md) van het platform worden automatisch verzameld. Maak [Diagnostische instellingen](platform/diagnostic-settings.md) voor het verzamelen van meer gedetailleerde informatie over de werking van uw Azure-resources en voeg [bewakings oplossingen](insights/solutions.md) en [inzichten](insights/insights-overview.md) toe om extra analyses te bieden op verzamelde gegevens voor bepaalde services. 

### <a name="how-do-i-access-azure-monitor"></a>Hoe kan ik toegang tot Azure Monitor?
Toegang tot alle Azure Monitor-functies en-gegevens via het menu **monitor** in de Azure Portal. De sectie **bewaking** van het menu voor verschillende Azure-Services biedt toegang tot dezelfde hulpprogram ma's als gegevens die zijn gefilterd op een bepaalde resource. Azure Monitor gegevens zijn ook toegankelijk voor diverse scenario's met behulp van CLI, Power shell en een REST API.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Is er een on-premises versie van Azure Monitor?
Nee. Azure Monitor is een schaal bare Cloud service die grote hoeveel heden gegevens verwerkt en opslaat, hoewel Azure Monitor resources kan bewaken die on-premises en in andere Clouds zijn.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Kan Azure Monitor on-premises resources controleren?
Ja, naast het verzamelen van bewakings gegevens van Azure-resources, kunnen Azure Monitor gegevens verzamelen van virtuele machines en toepassingen in andere Clouds en on-premises. Zie [bronnen van bewakings gegevens voor Azure monitor](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Is Azure Monitor geïntegreerd met System Center Operations Manager?
U kunt uw bestaande System Center Operations Manager-beheer groep verbinden met Azure Monitor om gegevens van agents te verzamelen in Azure Monitor Logboeken. Zo kunt u logboek query's en oplossingen gebruiken om gegevens te analyseren die zijn verzameld door agents. U kunt ook bestaande System Center Operations Manager agenten zo configureren dat gegevens rechtstreeks naar Azure Monitor worden verzonden. Zie [Operations Manager verbinding maken met Azure monitor](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Welke IP-adressen Azure Monitor gebruiken?
Zie [IP-adressen die worden gebruikt door Application Insights en log Analytics](app/ip-addresses.md) voor een lijst met de IP-adressen en poorten die vereist zijn voor agents en andere externe bronnen voor toegang tot Azure monitor. 

## <a name="monitoring-data"></a>Bewakingsgegevens

### <a name="where-does-azure-monitor-get-its-data"></a>Waar Azure Monitor de gegevens ophalen?
Azure Monitor verzamelt gegevens uit verschillende bronnen, waaronder Logboeken en metrieken van Azure-platform en-resources, aangepaste toepassingen en agents die op virtuele machines worden uitgevoerd. Andere services, zoals Azure Security Center, en Network Watcher het verzamelen van gegevens in een Log Analytics-werk ruimte, zodat deze kunnen worden geanalyseerd met Azure Monitor gegevens. U kunt ook aangepaste gegevens naar Azure Monitor verzenden met behulp van de REST API voor Logboeken of metrieken. Zie [bronnen van bewakings gegevens voor Azure monitor](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Welke gegevens worden er door Azure Monitor verzameld? 
Azure Monitor verzamelt gegevens uit een verscheidenheid aan bronnen in [Logboeken](platform/data-platform-logs.md) of [metrieken](platform/data-platform-metrics.md). Elk type gegevens heeft zijn eigen relatieve voor delen, en alle typen bieden ondersteuning voor een bepaalde set functies in Azure Monitor. Er is één metrische Data Base voor elk Azure-abonnement, terwijl u meerdere Log Analytics-werk ruimten kunt maken voor het verzamelen van Logboeken, afhankelijk van uw vereisten. Zie [Azure monitor data platform](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Is er een maximale hoeveelheid gegevens die ik kan verzamelen in Azure Monitor?
Er is geen limiet voor de hoeveelheid metrische gegevens die u kunt verzamelen, maar deze gegevens worden Maxi maal 93 dagen opgeslagen. Zie het [bewaren van metrische gegevens](platform/data-platform-metrics.md#retention-of-metrics). Er is geen limiet voor het aantal logboek gegevens dat u kunt verzamelen, maar dit kan worden beïnvloed door de prijs categorie die u kiest voor de Log Analytics-werk ruimte. Zie de [prijs informatie](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Hoe kan ik toegang tot gegevens die door Azure Monitor zijn verzameld?
Inzichten en oplossingen bieden een aangepaste ervaring voor het werken met gegevens die zijn opgeslagen in Azure Monitor. U kunt rechtstreeks met logboek gegevens werken met behulp van een logboek query die is geschreven in Kusto query language (KQL). In de Azure Portal kunt u query's schrijven en uitvoeren en gegevens interactief analyseren met behulp van Log Analytics. Analyseer de metrische gegevens in de Azure Portal met de Metrics Explorer. Zie [logboek gegevens analyseren in azure monitor](log-query/log-query-overview.md) en [aan de slag met Azure Metrics Explorer](platform/metrics-getting-started.md).





## <a name="solutions-and-insights"></a>Oplossingen en inzichten

### <a name="what-is-an-insight-in-azure-monitor"></a>Wat is een inzicht in Azure Monitor?
Inzichten bieden een aangepaste bewakings ervaring voor bepaalde Azure-Services. Ze gebruiken dezelfde metrische gegevens en Logboeken als andere functies in Azure Monitor, maar kunnen er ook extra informatie verzamelen en een unieke ervaring bieden in de Azure Portal. Zie [inzichten in azure monitor](insights/insights-overview.md).

Als u inzichten wilt weer geven in de Azure Portal, raadpleegt u de sectie **inzichten** in het menu **monitor** of in het gedeelte **bewaking** van het menu van de service.

### <a name="what-is-a-solution-in-azure-monitor"></a>Wat is een oplossing in Azure Monitor?
Bewakings oplossingen zijn verpakte sets van logica voor het bewaken van een bepaalde toepassing of service op basis van Azure Monitor-functies. Ze verzamelen logboek gegevens in Azure Monitor en bieden logboek query's en-weer gaven voor hun analyse met behulp van een algemene ervaring in de Azure Portal. Zie [oplossingen controleren in azure monitor](insights/solutions.md).

Als u oplossingen wilt weer geven in de Azure Portal, klikt u op **meer** in het gedeelte **inzichten** van het menu **monitor** . Klik op **toevoegen** om aanvullende oplossingen aan de werk ruimte toe te voegen.






## <a name="logs"></a>Logboeken

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Wat is het verschil tussen Azure Monitor logboeken en Azure Data Explorer?
Azure Data Explorer is een snelle en zeer schaalbare service om gegevens in logboeken en telemetriegegevens te verkennen. Azure Monitor-Logboeken is gebaseerd op Azure Data Explorer en maakt gebruik van dezelfde Kusto query language (KQL) met enkele kleine verschillen. Zie [Azure monitor taal verschillen in de logboek query](log-query/data-explorer-difference.md).

### <a name="how-do-i-retrieve-log-data"></a>Hoe kan ik logboek gegevens ophalen?
Alle gegevens worden opgehaald uit een Log Analytics-werk ruimte met behulp van een logboek query die is geschreven met Kusto query language (KQL). U kunt uw eigen query's schrijven of oplossingen en inzichten gebruiken die logboek query's bevatten voor een bepaalde toepassing of service. Zie [overzicht van logboek query's in azure monitor](log-query/log-query-overview.md).

### <a name="what-is-a-log-analytics-workspace"></a>Wat is een Log Analytics-werk ruimte?
Alle door Azure Monitor verzamelde logboek gegevens worden opgeslagen in een Log Analytics-werk ruimte. Een werk ruimte is in feite een container waarin logboek gegevens worden verzameld uit verschillende bronnen. Mogelijk hebt u een enkele Log Analytics-werk ruimte voor al uw bewakings gegevens of hebt u vereisten voor meerdere werk ruimten. Zie [de implementatie van uw Azure monitor-logboeken ontwerpen](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Kunt u een bestaande Log Analytics-werk ruimte verplaatsen naar een ander Azure-abonnement?
U kunt een werk ruimte verplaatsen tussen resource groepen of abonnementen, maar niet naar een andere regio. Zie [een log Analytics-werk ruimte verplaatsen naar een ander abonnement of een andere resource groep](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Waarom kan ik query Explorer niet zien en knoppen Opslaan in Log Analytics?

De knoppen **query Verkenner**, **Opslaan** en **nieuwe waarschuwings regel** zijn niet beschikbaar wanneer het [query bereik](log-query/scope.md) is ingesteld op een specifieke resource. Als u waarschuwingen wilt maken, een query wilt opslaan of laden, moet Log Analytics bereik zijn van een werk ruimte. Als u Log Analytics in de werkruimte context wilt openen, selecteert u **Logboeken** in het menu **Azure monitor** . De laatst gebruikte werkruimte is geselecteerd, maar u kunt een andere werkruimte selecteren. Zie de [logboek query bereik en het tijds bereik in Azure Monitor Log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Waarom krijg ik de fout: ' micro soft. Insights van resource provider registreren voor dit abonnement om deze query in te scha kelen ' bij het openen van Log Analytics van een VM? 
Veel resource providers worden automatisch geregistreerd, maar u moet mogelijk bepaalde resource providers hand matig registreren. Het bereik voor de registratie is altijd het abonnement. Zie [Resourceproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) voor meer informatie.

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Waarom krijg ik geen toegangs fout bericht bij het openen van Log Analytics vanaf een virtuele machine? 
Als u VM-logboeken wilt weer geven, moet u beschikken over de machtiging lezen voor de werk ruimten waarin de VM-logboeken worden opgeslagen. In dergelijke gevallen moet de beheerder u verlenen machtigingen in Azure.




## <a name="alerts"></a>Waarschuwingen

### <a name="what-is-an-alert-in-azure-monitor"></a>Wat is een waarschuwing in Azure Monitor?
Waarschuwingen geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. Hiermee kunt u problemen identificeren en verhelpen voordat de gebruikers van uw systeem ze merken. Er zijn meerdere soorten waarschuwingen:

- Metrische waarde voor metriek overschrijdt drempel.
- Logboek query-resultaten van een logboek query die overeenkomt met gedefinieerde criteria.
- Activiteiten logboek-activiteiten logboek gebeurtenis komt overeen met gedefinieerde criteria.
- Webtest: resultaten van de beschikbaarheids test voldoen aan de criteria die zijn gedefinieerd.


Zie [overzicht van waarschuwingen in Microsoft Azure](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Wat is een actie groep?
Een actie groep is een verzameling van meldingen en acties die kunnen worden geactiveerd door een waarschuwing. Meerdere waarschuwingen kunnen één actie groep gebruiken, zodat u algemene sets van meldingen en acties kunt benutten. Zie [actie groepen maken en beheren in de Azure Portal](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>Wat is een actie regel?
Met een actie regel kunt u het gedrag wijzigen van een set waarschuwingen die overeenkomen met een bepaald criterium. Zo kunt u dergelijke vereisten uitvoeren als waarschuwings acties uitschakelen tijdens een onderhouds venster. U kunt ook een actie groep Toep assen op een set waarschuwingen in plaats van deze rechtstreeks toe te passen op de waarschuwings regels. Zie [actie regels](platform/alerts-action-rules.md).


## <a name="agents"></a>Agents

### <a name="does-azure-monitor-require-an-agent"></a>Is Azure Monitor een agent nodig?
Een agent is alleen vereist voor het verzamelen van gegevens van het besturings systeem en workloads op virtuele machines. De virtuele machines kunnen zich in azure, een andere cloud omgeving of on-premises bevinden. Zie [overzicht van de Azure monitor agents](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Wat is het verschil tussen de Azure Monitor agents?
Diagnostische Azure-extensie is voor virtuele machines van Azure en verzamelt gegevens voor Azure Monitor metrieken, Azure Storage en Azure Event Hubs. De Log Analytics-agent is voor virtuele machines in azure, een andere cloud omgeving of on-premises en verzamelt gegevens voor Azure Monitor-Logboeken. De afhankelijkheids agent vereist de Log Analytics agent en de verzamelde proces gegevens en-afhankelijkheden. Zie [overzicht van de Azure monitor agents](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Maakt mijn agent verkeer gebruik van mijn ExpressRoute-verbinding?
Verkeer naar Azure Monitor maakt gebruik van het micro soft peering ExpressRoute-circuit. Raadpleeg de [ExpressRoute-documentatie](../expressroute/expressroute-faqs.md#supported-services) voor een beschrijving van de verschillende soorten ExpressRoute-verkeer. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Hoe kan ik controleren of de Log Analytics-agent met Azure Monitor kan communiceren?
Selecteer in het configuratie scherm op de computer van de agent **beveiliging & instellingen**, **micro soft Monitoring Agent** . Op het tabblad **Azure log Analytics (OMS)** wordt met een groen vinkje bevestigd dat de agent kan communiceren met Azure monitor. Een geel waarschuwings pictogram geeft aan dat de agent problemen ondervindt. Een veelvoorkomende reden is dat de service **micro soft Monitoring Agent** is gestopt. Servicebesturingsbeheer gebruiken de service opnieuw te starten.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Hoe kan ik Log Analytics agent niet meer communiceren met Azure Monitor?
Voor agents die rechtstreeks met Log Analytics zijn verbonden, opent u het configuratie scherm en selecteert u **instellingen voor beveiliging &** , **micro soft Monitoring Agent**. Verwijder op het tabblad **Azure log Analytics (OMS)** alle weer gegeven werk ruimten. In System Center Operations Manager verwijdert u de computer uit de lijst Log Analytics beheerde computers. Operations Manager werkt u de configuratie van de agent niet langer een rapport naar Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Hoeveel gegevens worden verzonden per agent?
Afhankelijk van de hoeveelheid gegevens die per agent worden verzonden:

* De oplossingen die u hebt ingeschakeld
* Het aantal logboeken en prestatiemeteritems worden verzameld
* De hoeveelheid gegevens in de logboeken

Zie [gebruik en kosten beheren met Azure monitor logboeken](platform/manage-cost-storage.md) voor meer informatie.

Voor computers die kunnen worden uitgevoerd van de agent WireData, gebruikt u de volgende query uit om te zien hoeveel gegevens er worden verzonden:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Hoeveel netwerk bandbreedte wordt gebruikt door de micro soft Management Agent (MMA) bij het verzenden van gegevens naar Azure Monitor?
Bandbreedte is een functie voor de hoeveelheid gegevens die worden verzonden. Gegevens worden gecomprimeerd wanneer deze worden verzonden via het netwerk.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Hoe kan ik een melding ontvangen wanneer het verzamelen van gegevens van de Log Analytics agent stopt?

Gebruik de stappen die worden beschreven in [een waarschuwing bij het maken van een nieuw logboek om een](platform/alerts-metric.md) melding te ontvangen wanneer het verzamelen van gegevens wordt gestopt. Gebruik de volgende instellingen voor de waarschuwings regel:

- **Waarschuwings voorwaarde definiëren**: geef uw log Analytics-werk ruimte op als bron doel.
- **Waarschuwings criteria** 
   - **Signaal naam**: *aangepaste zoek opdracht in Logboeken*
   - **Zoek query**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Waarschuwings logica**: **gebaseerd op** het *aantal resultaten*, **waarde** *groter dan*, **drempel waarde** *0*
   - **Geëvalueerd op basis van**: **periode (in minuten)** *30*, **frequentie (in minuten)** *10*
- **Waarschuwings Details definiëren** 
   - **Naam**: *gegevens verzameling gestopt*
   - **Ernst**: *waarschuwing*

Geef een bestaande of nieuwe [actie groep](platform/action-groups.md) op, zodat wanneer de logboek waarschuwing overeenkomt met criteria, u een melding ontvangt als er al meer dan 15 minuten een heartbeat ontbreekt.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Wat zijn de firewall vereisten voor Azure Monitor agents?
Zie [netwerk firewall vereisten](platform/log-analytics-agent.md#network-firewall-requirements)voor meer informatie over Firewall vereisten.


## <a name="visualizations"></a>Visualisaties

### <a name="why-cant-i-cant-see-view-designer"></a>Waarom kan ik de weer gave Designer niet zien?

De weer gave Designer is alleen beschikbaar voor gebruikers die zijn toegewezen met Inzender machtigingen of een hoger in de Log Analytics-werk ruimte.


## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Configuratie problemen
*Ik ondervind problemen bij het instellen van mijn:*

* [.NET app](app/asp-net-troubleshoot-no-data.md)
* [Een app die al wordt uitgevoerd bewaken](app/monitor-performance-live-website-now.md#troubleshoot)
* [Diagnostische gegevens van Azure](platform/diagnostics-extension-to-application-insights.md)
* [Java-web-app](app/java-troubleshoot.md)

*Ik krijg geen gegevens van mijn server*

* [Firewall-uitzonde ringen instellen](app/ip-addresses.md)
* [Een ASP.NET-Server instellen](app/monitor-performance-live-website-now.md)
* [Een Java-Server instellen](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>Kan ik Application Insights gebruiken met...?

* [Web-apps op een IIS-server in een Azure VM-of Azure virtual machine-schaalset](app/azure-vm-vmss-apps.md)
* [Web-apps op een IIS-server, on-premises of in een VM](app/asp-net.md)
* [Java-Web-apps](app/java-get-started.md)
* [Node.js-apps](app/nodejs.md)
* [Web-apps op Azure](app/azure-web-apps.md)
* [Cloud Services op Azure](app/cloudservices.md)
* [App-servers die worden uitgevoerd in docker](app/docker.md)
* [Web-apps met één pagina](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows-bureau blad-app](app/windows-desktop.md)
* [Andere platforms](app/platforms.md)

### <a name="is-it-free"></a>Is dit gratis?

Ja, voor experimenteel gebruik. In het Basic-prijs plan kan uw toepassing elke maand gratis een bepaalde hoeveelheid gegevens verzenden. De gratis limiet is groot genoeg voor ontwikkeling en het publiceren van een app voor een klein aantal gebruikers. U kunt een limiet instellen om te voor komen dat er meer dan een opgegeven hoeveelheid gegevens wordt verwerkt.

Grotere hoeveel heden telemetrie worden in rekening gebracht op basis van de GB. We bieden enkele tips over het [beperken van uw kosten](app/pricing.md).

In het Enter prise-plan worden kosten in rekening gebracht voor elke dag dat elk webserver knooppunt telemetrie verzendt. Het is geschikt als u doorlopend exporteren wilt gebruiken op een grote schaal.

[Lees het prijs plan](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Wat kost het?

* Open de **pagina gebruik en geschatte kosten** in een Application Insights resource. Er is een grafiek van recent gebruik. Als u wilt, kunt u een limiet voor gegevens volumes instellen.
* Open de [Blade Azure-facturering](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) om uw rekeningen voor alle resources weer te geven.

### <a name="q14"></a>Wat doet Application Insights wijzigen in mijn project?
De details zijn afhankelijk van het type project. Voor een webtoepassing:

* Voegt deze bestanden toe aan uw project:
  * ApplicationInsights.config
  * ai.js
* Installeert deze NuGet-pakketten:
  * *Application Insights-API* : de core-API
  * *Application Insights-API voor webtoepassingen* : wordt gebruikt voor het verzenden van telemetrie van de server
  * *Application Insights-API voor Java script-toepassingen* : wordt gebruikt voor het verzenden van telemetrie van de client
* De pakketten bevatten de volgende assembly's:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Hiermee worden items ingevoegd in:
  * Web.config
  * packages.config
* (Alleen nieuwe projecten: als u [Application Insights toevoegt aan een bestaand project][start], moet u dit hand matig doen.) Hiermee worden fragmenten ingevoegd in de client-en server code om ze te initialiseren met de Application Insights Resource-ID. In een MVC-app wordt bijvoorbeeld code ingevoegd in de basis pagina weergaven/gedeelde/\_indeling. cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hoe kan ik upgrade van oudere SDK-versies?
Zie de [release opmerkingen](app/release-notes.md) voor de SDK die geschikt is voor uw type toepassing.

### <a name="update"></a>Hoe kan ik wijzigen met welke Azure-resource mijn project gegevens verzendt?
Klik in Solution Explorer met de rechter muisknop op `ApplicationInsights.config` en kies vervolgens **Update Application Insights**. U kunt de gegevens verzenden naar een bestaande of nieuwe resource in Azure. De update wizard wijzigt de instrumentatie sleutel in ApplicationInsights. config, waarmee wordt bepaald waar de server-SDK uw gegevens verzendt. Tenzij u ' Alles bijwerken ' uitschakelt, wordt ook de sleutel gewijzigd waar deze wordt weer gegeven op uw webpagina's.

### <a name="what-is-status-monitor"></a>Wat is Status Monitor?

Een bureau blad-app die u kunt gebruiken op uw IIS-webserver om Application Insights te configureren in web-apps. Er wordt geen telemetrie verzameld: u kunt deze stoppen wanneer u geen app configureert. 

[Meer informatie](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Welke telemetrie wordt door Application Insights verzameld?

Van server web apps:

* HTTP-aanvragen
* [Afhankelijkheden](app/asp-net-dependencies.md). Aanroepen naar: SQL data bases; HTTP-aanroepen naar externe services; Azure Cosmos DB, tabel, Blob Storage en wachtrij. 
* [Uitzonde ringen](app/asp-net-exceptions.md) en stack traceringen.
* [Prestatie meter items](app/performance-counters.md) : als u [status monitor](app/monitor-performance-live-website-now.md)gebruikt, wordt [Azure monitoring voor app Services](app/azure-web-apps.md), [Azure monitoring voor VM of virtual machine Scale set](app/azure-vm-vmss-apps.md)of de [Application Insights verzamelde Writer](app/java-collectd.md).
* [Aangepaste gebeurtenissen en metrische gegevens](app/api-custom-events-metrics.md) die u codeert.
* [Traceer logboeken](app/asp-net-trace-logs.md) als u de juiste Collector configureert.

Van [client webpagina's](app/javascript.md):

* [Aantal pagina weergaven](app/usage-overview.md)
* [Ajax-aanroepen](app/asp-net-dependencies.md) Aanvragen die afkomstig zijn van een script dat wordt uitgevoerd.
* Laad gegevens pagina weergave
* Aantal gebruikers en sessies
* [Geverifieerde gebruikers-Id's](app/api-custom-events-metrics.md#authenticated-users)

Van andere bronnen, als u deze configureert:

* [Diagnostische gegevens van Azure](platform/diagnostics-extension-to-application-insights.md)
* [Importeren naar Analytics](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kan ik een telemetrie uitfilteren of wijzigen?

Ja, op de server kunt u het volgende schrijven:

* Telemetrie-processor om eigenschappen te filteren of toe te voegen aan geselecteerde telemetriegegevens voordat ze vanuit uw app worden verzonden.
* De initialisatie functie voor telemetrie om eigenschappen toe te voegen aan alle items van telemetrie.

Meer informatie voor [ASP.net](app/api-filtering-sampling.md) of [Java](app/java-filter-telemetry.md).

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Hoe worden plaats, land/regio en andere geografische locatie gegevens berekend?

We zoeken het IP-adres (IPv4 of IPv6) van de webclient met behulp van [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Browser-telemetrie: we verzamelen het IP-adres van de afzender.
* Server-telemetrie: de Application Insights-module verzamelt het client-IP-adres. Het wordt niet verzameld als `X-Forwarded-For` is ingesteld.
* Raadpleeg dit [artikel](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection)voor meer informatie over hoe IP-adres en geolocatie gegevens worden verzameld in Application Insights.


U kunt de `ClientIpHeaderTelemetryInitializer` configureren om het IP-adres van een andere header te halen. In sommige systemen wordt het bijvoorbeeld verplaatst met een proxy, load balancer of CDN naar `X-Originating-IP`. [Meer informatie](https://apmtips.com/blog/2016/07/05/client-ip-address/).

U kunt [Power bi gebruiken](app/export-power-bi.md ) om uw aanvraag-telemetrie weer te geven op een kaart.


### <a name="data"></a>Hoe lang worden gegevens in de portal bewaard? Is het veilig?
Bekijk de [gegevens retentie en privacy][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Wat gebeurt er met de telemetrie van Application Insight wanneer een server of apparaat geen verbinding meer met Azure heeft?

Al onze Sdk's, inclusief de Web-SDK, bevatten ' reliable Trans Port ' of ' Robust Trans Port '. Wanneer de server of het apparaat verbinding met Azure verliest, wordt telemetrie [lokaal opgeslagen op het bestands systeem](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage) (Server-sdk's) of in HTML5-sessie opslag (Web-SDK). De SDK zal regel matig proberen deze telemetrie te verzenden totdat de opname service de ' verouderde ' (48-uur voor de logboeken, 30 minuten voor metrische gegevens) beschouwt. Verlopen telemetrie wordt verwijderd. In sommige gevallen, bijvoorbeeld wanneer lokale opslag vol is, wordt er geen nieuwe poging gedaan.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Kunnen persoons gegevens in de telemetrie worden verzonden?

Dit is mogelijk als uw code dergelijke gegevens verzendt. Dit kan ook gebeuren als variabelen in stack-traceringen persoons gegevens bevatten. Uw ontwikkel team moet risico beoordelingen uitvoeren om ervoor te zorgen dat persoons gegevens op de juiste wijze worden afgehandeld. Meer [informatie over gegevens retentie en privacy](app/data-retention-privacy.md).

**Alle** octetten van het webadres van de client zijn altijd ingesteld op 0 nadat de geografische locatie kenmerken zijn opgezocht.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Mijn instrumentatie sleutel is zichtbaar in de bron van de webpagina. 

* Dit is gebruikelijk bij het controleren van oplossingen.
* Het kan niet worden gebruikt om uw gegevens te stelen.
* Het kan worden gebruikt om uw gegevens te scheef stellen of waarschuwingen te activeren.
* We hebben niet gehoord dat een klant dergelijke problemen heeft gehad.

U kunt het volgende doen:

* Gebruik twee afzonderlijke instrumentatie sleutels (afzonderlijke Application Insights resources) voor client-en Server gegevens. of
* Schrijf een proxy die op uw server wordt uitgevoerd en laat de webclient gegevens verzenden via die proxy.

### <a name="post"></a>Hoe kan ik raadpleegt u POST gegevens in diagnostische Zoek opdrachten?
Er worden geen POST gegevens automatisch geregistreerd, maar u kunt een TrackTrace-aanroep gebruiken: plaats de gegevens in de bericht parameter. Dit heeft een maximale grootte die groter is dan de limieten voor teken reeks eigenschappen, maar u kunt er niet op filteren.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Moet ik een of meerdere Application Insights bronnen gebruiken?

Gebruik één resource voor alle onderdelen of rollen in één bedrijfs systeem. Gebruik afzonderlijke resources voor ontwikkelings-, test-en release versies en voor onafhankelijke toepassingen.

* [Bekijk de discussie hier](app/separate-resources.md)
* [Voor beeld-Cloud service met werk nemers en webrollen](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Hoe kan ik de instrumentatie sleutel dynamisch wijzigen?

* [Discussie hier](app/separate-resources.md)
* [Voor beeld-Cloud service met werk nemers en webrollen](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Wat zijn de gebruikers-en sessie aantallen?

* De Java script-SDK stelt een gebruikers cookie op de webclient in om terugkerende gebruikers te identificeren en een sessie cookie om activiteiten te groeperen.
* Als er geen script aan de client zijde is, kunt u [cookies instellen op de server](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Als een echte gebruiker uw site in verschillende browsers gebruikt of als u in-private/incognito Browse of verschillende computers gebruikt, worden deze meerdere keren geteld.
* Als u een aangemelde gebruiker op verschillende computers en browsers wilt identificeren, voegt u een aanroep toe aan [setAuthenticatedUserContext ()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="q17"></a>Heb ik alles in Application Insights ingeschakeld?
| Wat u moet zien | Hoe kan ik het downloaden? | Waarom u wilt |
| --- | --- | --- |
| Beschikbaarheids grafieken |[Webtests](app/monitor-web-app-availability.md) |Weet u zeker dat uw web-app actief is |
| Server app-prestaties: reactie tijden,... |[Application Insights toevoegen aan uw project](app/asp-net.md) of [AI-status monitor op server installeren](app/monitor-performance-live-website-now.md) (of uw eigen code schrijven om [afhankelijkheden bij te houden](app/api-custom-events-metrics.md#trackdependency)) |Prestatie problemen detecteren |
| Telemetrie van afhankelijkheid |[AI-Status Monitor op server installeren](app/monitor-performance-live-website-now.md) |Problemen met data bases of andere externe onderdelen vaststellen |
| Stack traceringen ophalen van uitzonde ringen |[TrackException-aanroepen invoegen in uw code](app/asp-net-exceptions.md) (maar sommige worden automatisch gerapporteerd) |Uitzonde ringen detecteren en diagnosticeren |
| Logboek traceringen zoeken |[Een logboek registratie adapter toevoegen](app/asp-net-trace-logs.md) |Diagnose uitzonde ringen, prestatie problemen |
| Basis beginselen van client gebruik: pagina weergaven, sessies,... |[Java script-initialisatie functie in webpagina's](app/javascript.md) |Gebruiksanalyse |
| Aangepaste metrische gegevens van client |[Aanroepen bijhouden op webpagina's](app/api-custom-events-metrics.md) |Gebruikers ervaring verbeteren |
| Aangepaste metrische gegevens voor de server |[Tracerings aanroepen op server](app/api-custom-events-metrics.md) |Business intelligence |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Waarom zijn de aantallen in zoek-en metrische grafieken niet gelijk?

Door [steek proeven](app/sampling.md) wordt het aantal telemetriegegevens (aanvragen, aangepaste gebeurtenissen, enzovoort) verminderd die daad werkelijk vanuit uw app naar de portal worden verzonden. In de zoek opdracht ziet u het aantal items dat daad werkelijk is ontvangen. In metrische grafieken die een aantal gebeurtenissen weer geven, ziet u het aantal oorspronkelijke gebeurtenissen dat heeft plaatsgevonden. 

Elk item dat wordt verzonden, bevat een `itemCount`-eigenschap die laat zien hoeveel oorspronkelijke gebeurtenissen een item vertegenwoordigt. Als u de bemonsterings bewerking wilt observeren, kunt u deze query uitvoeren in Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Automatisering

#### <a name="configuring-application-insights"></a>Application Insights configureren

U kunt [Power shell-scripts](app/powershell.md) met Azure Broncontrole schrijven om het volgende te doen:

* Application Insights resources maken en bijwerken.
* Stel het prijs plan in.
* Haal de instrumentatie sleutel op.
* Een waarschuwing voor metrische gegevens toevoegen.
* Voeg een beschikbaarheids test toe.

U kunt geen metrisch Explorer-rapport instellen of continue export instellen.

#### <a name="querying-the-telemetry"></a>Query's uitvoeren op de telemetrie

Gebruik de [rest API](https://dev.applicationinsights.io/) om [Analytics](app/analytics.md) -query's uit te voeren.

### <a name="how-can-i-set-an-alert-on-an-event"></a>Hoe kan ik een waarschuwing instellen voor een gebeurtenis?

Azure-waarschuwingen zijn alleen op metrische gegevens. Maak een aangepaste metriek die een drempel waarde voor waarden kruist wanneer uw gebeurtenis zich voordoet. Stel vervolgens een waarschuwing in voor de metrische gegevens. U ontvangt een melding wanneer de metriek de drempel waarde in een van beide richtingen overschrijdt. u krijgt pas een melding als de eerste doorhaling, ongeacht of de aanvankelijke waarde hoog of laag is. Er is altijd een latentie van een paar minuten.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Zijn er kosten voor gegevens overdracht tussen een Azure-web-app en Application Insights?

* Als uw Azure-web-app wordt gehost in een Data Center waar zich een Application Insights verzamelings eindpunt bevindt, worden er geen kosten in rekening gebracht. 
* Als er geen eind punt van een verzameling in uw host Data Center is, worden door de telemetrie van uw app [uitgaande Azure-kosten](https://azure.microsoft.com/pricing/details/bandwidth/)in rekening gebracht.

Dit is niet afhankelijk van waar uw Application Insights-bron wordt gehost. Het hangt gewoon af van de distributie van onze eind punten.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Kan ik telemetrie verzenden naar de Application Insights Portal?

We raden u aan om onze Sdk's te gebruiken en de [SDK API](app/api-custom-events-metrics.md)te gebruiken. Er zijn varianten van de SDK voor verschillende [platforms](app/platforms.md). Deze Sdk's behandelen buffering, compressie, beperking, nieuwe pogingen, enzovoort. Het [opname schema](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) en het [eindpunt protocol](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) zijn echter openbaar.

### <a name="can-i-monitor-an-intranet-web-server"></a>Kan ik een intranet webserver bewaken?

Ja, maar u moet het verkeer naar onze services toestaan door Firewall-uitzonde ringen of proxy omleidingen.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Bekijk [hier](app/ip-addresses.md)onze volledige lijst met Services en IP-adressen.

#### <a name="firewall-exception"></a>Firewall-uitzonde ring

Hiermee staat u toe dat uw webserver telemetrie naar onze eind punten verzendt. 

#### <a name="gateway-redirect"></a>Gateway omleiden

Verkeer van uw server naar een gateway op uw intranet routeren door eind punten in uw configuratie te overschrijven. Als deze eigenschappen van het eind punt niet aanwezig zijn in uw configuratie, gebruiken deze klassen de standaard waarden die hieronder worden weer gegeven in het voor beeld ApplicationInsights. config. 

Uw gateway moet verkeer routeren naar het basis adres van het eind punt. Vervang in uw configuratie de standaard waarden door `http://<your.gateway.address>/<relative path>`.


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Voor beeld van ApplicationInsights. config met standaard eindpunten:
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
> ApplicationIdProvider is beschikbaar vanaf v 2.6.0.



#### <a name="proxy-passthrough"></a>Passthrough van proxy

Passthrough van de proxy kan worden bereikt door een computer niveau of proxy op toepassings niveau te configureren.
Zie voor meer informatie het artikel van DOTNET op [DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Voor beeld van web. config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Kan ik beschik baarheid-webtests op een intranet server uitvoeren?

Onze [webtests](app/monitor-web-app-availability.md) worden uitgevoerd op de aanwezigheids punten die over de hele wereld worden gedistribueerd. Er zijn twee oplossingen:

* Firewall deur: Hiermee worden aanvragen van de server met [de lange en de lijst met webtest agents](app/ip-addresses.md)toegestaan.
* Schrijf uw eigen code voor het verzenden van periodieke aanvragen naar uw server vanuit uw intranet. U kunt Visual Studio-webtests voor dit doel uitvoeren. De tester kan de resultaten naar Application Insights verzenden met behulp van de API TrackAvailability ().

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Hoe lang duurt het voordat telemetrie is verzameld?

De meeste Application Insights gegevens hebben een latentie van minder dan vijf minuten. Sommige gegevens kunnen langer duren. meestal grotere logboek bestanden. Zie de [Application INSIGHTS Sla](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/)voor meer informatie.



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md



## <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers

Deze veelgestelde vragen over micro soft is een lijst met veelgestelde vragen over Azure Monitor voor containers. Als u aanvullende vragen over de oplossing hebt, gaat u naar het [discussie forum](https://feedback.azure.com/forums/34192--general-feedback) en plaatst u uw vragen. Wanneer u een vraag is vaak wordt gevraagd, toevoegen we deze aan dit artikel zodat snel en eenvoudig kunnen worden gevonden.

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Ik zie geen afbeeldings-en naam eigenschap waarden die worden ingevuld wanneer ik de tabel ContainerLog zoek.

Voor Agent versie ciprod12042019 en hoger worden deze twee eigenschappen standaard niet ingevuld voor elke logboek regel om de kosten te minimaliseren voor logboek gegevens die worden verzameld. Er zijn twee opties voor het opvragen van de tabel die deze eigenschappen bevat met hun waarden:

#### <a name="option-1"></a>optie 1 

Neem deel aan andere tabellen om deze eigenschaps waarden in de resultaten op te nemen.

Wijzig uw query's zodat de eigenschappen van de afbeelding en ImageTag worden toegevoegd aan de tabel ```ContainerInventory``` door lid te worden van de eigenschap ContainerID. U kunt de eigenschap name (zoals deze eerder in de tabel ```ContainerLog```) in het veld ContaineName van KubepodInventory tabel toevoegen door lid te worden van de eigenschap ContainerID. Dit is de aanbevolen optie.

In het volgende voor beeld wordt uitgelegd hoe u deze veld waarden ophaalt met samen voegingen.

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

Schakel de verzameling voor deze eigenschappen voor elke container logboek regel opnieuw in.

Als de eerste optie niet handig is als gevolg van wijzigingen in de query, kunt u het verzamelen van deze velden opnieuw inschakelen door de instellings ```log_collection_settings.enrich_container_logs``` in te scha kelen in de [configuratie-instellingen](insights/container-insights-agent-config.md)van de agent.

> [!NOTE]
> De tweede optie wordt niet aanbevolen voor grote clusters met meer dan 50 knoop punten, omdat hiermee de API-server aanroepen van elk knoop punt in het cluster worden gegenereerd om deze verrijking uit te voeren. Met deze optie wordt ook de gegevens grootte voor elke verzamelde logboek regel verhoogd.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Kan ik de metrische gegevens weer geven die zijn verzameld in Grafana?

Azure Monitor voor containers biedt ondersteuning voor het weer geven van gegevens die zijn opgeslagen in uw Log Analytics-werk ruimte in Grafana-Dash boards. We hebben een sjabloon die u kunt downloaden uit de Grafana van het [dash board](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) van micro soft om aan de slag te gaan en te verwijzen naar informatie over het zoeken naar extra gegevens van uw bewaakte clusters om te visualiseren in aangepaste Grafana-Dash boards. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Kan ik mijn AKS-engine-cluster bewaken met Azure Monitor voor containers?

Azure Monitor voor containers ondersteunt bewakings werkbelastingen die zijn geïmplementeerd op de AKS-Engine (voorheen bekend als ACS-Engine) cluster (s) die worden gehost op Azure. Zie voor meer informatie en een overzicht van de stappen die nodig zijn om bewaking in te scha kelen voor dit scenario Raadpleeg [Azure monitor gebruiken voor containers voor AKS-engine](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Waarom zie ik geen gegevens in mijn Log Analytics-werk ruimte?

Als u op een bepaald moment dagelijks geen gegevens in de Log Analytics-werk ruimte kunt zien, hebt u mogelijk de standaard limiet van 500 MB bereikt of is de daglimiet opgegeven om de hoeveelheid gegevens die dagelijks moet worden verzameld, te bepalen. Wanneer de limiet voor de dag wordt bereikt, stopt het verzamelen van gegevens en wordt deze alleen op de volgende dag hervat. Zie [logboek gegevens gebruiken en kosten](platform/manage-cost-storage.md)voor meer informatie over het controleren van uw gegevens gebruik en het bijwerken van een andere prijs categorie op basis van uw verwachte gebruiks patronen. 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Wat zijn de container statussen die zijn opgegeven in de tabel ContainerInventory?

De tabel ContainerInventory bevat informatie over zowel gestopte als actieve containers. De tabel wordt gevuld met een werk stroom in de agent die de docker doorzoekt voor alle containers (actief en gestopt), en stuurt die gegevens door naar de Log Analytics-werk ruimte.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Hoe kan ik oplossen van *ontbrekende registratie* fout van abonnement?

Als u de fout melding **abonnements registratie voor micro soft. OperationsManagement**ontvangt, kunt u deze oplossen door de resource provider **micro soft. OperationsManagement** te registreren in het abonnement waarin de werk ruimte is gedefinieerd. De documentatie voor hoe u dit doet, vindt u [hier](../azure-resource-manager/templates/error-register-resource-provider.md).

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Is er ondersteuning voor AKS-clusters met RBAC ingeschakeld?

De container bewakings oplossing biedt geen ondersteuning voor RBAC, maar wordt wel ondersteund met Azure Monitor voor containers. Op de pagina oplossings Details wordt mogelijk niet de juiste informatie weer gegeven op de Blades waarin de gegevens voor deze clusters worden weer gegeven.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Hoe kan ik logboek verzameling voor containers in de uitvoeren-naam ruimte inschakelen via helm?

De logboek verzameling van containers in de uitvoeren-naam ruimte is standaard uitgeschakeld. Logboek verzameling kan worden ingeschakeld door een omgevings variabele in te stellen op de omsagent. Zie de pagina [Azure monitor voor containers](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) github voor meer informatie. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Hoe kan ik de omsagent bij naar de meest recente versie?

Zie [agent beheer](insights/container-insights-manage-agent.md)voor meer informatie over het bijwerken van de agent.

### <a name="how-do-i-enable-multi-line-logging"></a>Hoe kan ik logboek registratie met meerdere regels inschakelen?

Momenteel Azure Monitor voor containers geen ondersteuning voor logboek registratie in meerdere regels, maar er zijn tijdelijke oplossingen beschikbaar. U kunt alle services zo configureren dat ze worden geschreven in JSON-indeling en vervolgens docker/Moby als één regel schrijft.

U kunt uw logboek bijvoorbeeld als een JSON-object laten teruglopen, zoals wordt weer gegeven in het onderstaande voor beeld voor een node. js-toepassing voor een voor beeld:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Deze gegevens zien eruit als in het volgende voor beeld in Azure Monitor voor Logboeken wanneer u er een query op uitvoert:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Raadpleeg de volgende [github-koppeling](https://github.com/moby/moby/issues/22920)voor een gedetailleerde weer gave van het probleem.

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Hoe kan ik Azure AD-fouten oplossen wanneer ik live-logboeken inschakel? 

Mogelijk wordt de volgende fout weer **gegeven: de antwoord-URL die in de aanvraag is opgegeven, komt niet overeen met de antwoord-url's die zijn geconfigureerd voor de toepassing: ' <-toepassings-ID\>'** . De oplossing voor het oplossen hiervan vindt u in het artikel [informatie over container gegevens in realtime weer geven met Azure monitor voor containers](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Waarom kan ik het cluster niet upgraden na het onboarden?

Als u Azure Monitor voor containers voor een AKS-cluster hebt ingeschakeld, verwijdert u de Log Analytics werk ruimte waarnaar de gegevens naar het cluster zijn verzonden bij het upgraden van het cluster. U kunt dit probleem omzeilen door de bewaking uit te scha kelen en vervolgens weer in te scha kelen naar een andere geldige werk ruimte in uw abonnement. Wanneer u de cluster upgrade opnieuw probeert uit te voeren, moet het proces worden uitgevoerd en voltooid.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Welke poorten en domeinen heb ik nodig om de agent te openen/white list?

Zie de [netwerk firewall vereisten](insights/container-insights-onboard.md#network-firewall-requirements) voor de proxy-en firewall configuratie-informatie die is vereist voor de container agent met Azure, Azure US Government en Azure China 21vianet-Clouds.

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor voor VM's (preview-versie)
Deze veelgestelde vragen over micro soft vindt u een lijst met veel gestelde antwoorden over Azure Monitor voor VM's. Als u aanvullende vragen over de oplossing hebt, gaat u naar het [discussie forum](https://feedback.azure.com/forums/34192--general-feedback) en plaatst u uw vragen. Wanneer u een vraag is vaak wordt gevraagd, toevoegen we deze aan dit artikel zodat snel en eenvoudig kunnen worden gevonden.

### <a name="can-i-onboard-to-an-existing-workspace"></a>Kan ik onboarding uitvoeren op een bestaande werk ruimte?
Als uw virtuele machines al zijn verbonden met een Log Analytics-werk ruimte, kunt u deze werk ruimte blijven gebruiken bij onboarding naar Azure Monitor voor VM's, mits deze zich in een van de ondersteunde regio's bevindt die [hier](insights/vminsights-enable-overview.md#prerequisites)worden vermeld.

Als er een onboarding wordt uitgevoerd, worden er prestatie meter items voor de werk ruimte geconfigureerd die ervoor zorgen dat alle Vm's rapport gegevens naar de werk ruimte worden verzameld om deze informatie te verzamelen voor weer gave en analyse in Azure Monitor voor VM's.  Als gevolg hiervan worden er prestatie gegevens weer gegeven van alle Vm's die zijn verbonden met de geselecteerde werk ruimte.  De functies status en kaart worden alleen ingeschakeld voor de virtuele machines die u hebt opgegeven voor onboarding.

Raadpleeg het artikel [overzicht inschakelen](insights/vminsights-enable-overview.md#performance-counters-enabled) voor meer informatie over welke prestatie meter items zijn ingeschakeld.

### <a name="can-i-onboard-to-a-new-workspace"></a>Kan ik onboarding uitvoeren op een nieuwe werk ruimte? 
Als uw Vm's momenteel niet zijn verbonden met een bestaande Log Analytics-werk ruimte, moet u een nieuwe werk ruimte maken om uw gegevens op te slaan. Het maken van een nieuwe standaardwerk ruimte wordt automatisch uitgevoerd als u een enkele Azure-VM voor Azure Monitor voor VM's configureert via de Azure Portal.

Als u ervoor kiest de op scripts gebaseerde methode te gebruiken, worden deze stappen behandeld in het artikel [enable Azure monitor voor VM's (preview) met Azure PowerShell of Resource Manager-sjabloon](insights/vminsights-enable-at-scale-powershell.md) . 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Wat moet ik doen als mijn VM al rapporteert aan een bestaande werk ruimte?
Als u al gegevens van uw virtuele machines verzamelt, is het mogelijk dat u deze al hebt geconfigureerd om gegevens te rapporteren aan een bestaande Log Analytics-werk ruimte.  Als die werk ruimte zich in een van de ondersteunde regio's bevindt, kunt u Azure Monitor voor VM's inschakelen voor die bestaande werk ruimte.  Als de werk ruimte die u al gebruikt, zich niet in een van de ondersteunde regio's bevindt, kunt u op dit moment niet op de Azure Monitor voor VM's.  We werken actief ter ondersteuning van extra regio's.

>[!NOTE]
>We configureren prestatie meter items voor de werk ruimte die van invloed zijn op alle Vm's die rapporteren aan de werk ruimte, ongeacht of u hebt gekozen om ze te Azure Monitor voor VM's. Raadpleeg onze [documentatie](platform/data-sources-performance-counters.md)voor meer informatie over de configuratie van prestatie meter items voor de werk ruimte. Raadpleeg het artikel [enable Azure monitor voor VM's](insights/vminsights-enable-overview.md#performance-counters-enabled) voor informatie over de tellers die zijn geconfigureerd voor Azure monitor voor VM's.  

### <a name="why-did-my-vm-fail-to-onboard"></a>Waarom is mijn VM niet op de onboarding uitgevoerd?
Wanneer een virtuele machine van Azure wordt geboardd vanuit de Azure Portal, gebeurt het volgende:

* Een standaard Log Analytics-werkruimte is gemaakt, als die optie hebt geselecteerd.
* De prestatie meter items zijn geconfigureerd voor de geselecteerde werk ruimte. Als deze stap mislukt, ziet u dat sommige prestatie grafieken en-tabellen geen gegevens weer geven voor de virtuele machine die u hebt opgevolgd. U kunt dit probleem oplossen door het Power shell-script uit te voeren dat [hier](insights/vminsights-enable-at-scale-powershell.md#enable-performance-counters)wordt beschreven.
* De Log Analytics-agent wordt op virtuele machines van Azure geïnstalleerd met behulp van een VM-extensie, indien nodig.  
* De Azure Monitor voor VM's kaart dependency agent wordt geïnstalleerd op virtuele machines van Azure met behulp van een uitbrei ding, indien bepaald dat deze is vereist.  
* Azure Monitor onderdelen die ondersteuning bieden voor de status functie worden zo nodig geconfigureerd en de virtuele machine is geconfigureerd om status gegevens te rapporteren.

Tijdens het onboarding-proces wordt gecontroleerd op de status van elk van de bovenstaande om een meldings status te retour neren in de portal. Configuratie van de werkruimte en de installatie van de agent duurt doorgaans 5 tot 10 minuten. Het weer geven van bewakings-en status gegevens in de portal duurt 5 tot 10 minuten.  

Als u onboarding hebt gestart en berichten ziet met de melding dat de virtuele machine moet worden uitgevoerd, kunt u Maxi maal 30 minuten voor de virtuele machine het proces volt ooien. 

### <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Ik heb alleen Azure Monitor voor VM's ingeschakeld, waarom kan ik al mijn Vm's zien die worden bewaakt door de status functie?
De status functie is ingeschakeld voor alle Vm's die zijn verbonden met de Log Analytics-werk ruimte, zelfs wanneer de actie voor één virtuele machine wordt gestart.

### <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Kan ik de planning wijzigen voor wanneer de status criteria een voor waarde evalueren?
Nee, de tijds periode en frequentie van de status criteria kunnen niet worden gewijzigd in deze release. 

### <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Kan ik status criteria uitschakelen voor een voor waarde die ik niet hoeft te controleren?
Status criteria kunnen niet worden uitgeschakeld in deze versie.

### <a name="are-the-health-alert-severities-configurable"></a>Zijn de ernst van de status waarschuwing configureerbaar?  
Ernst van status waarschuwing kan niet worden gewijzigd. deze kunnen alleen worden ingeschakeld of uitgeschakeld. Daarnaast worden enkele waarschuwings Ernst bijgewerkt op basis van de status criteria. 

### <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Als ik de instellingen van een bepaald status criterium opnieuw Configureer, kan de scope worden toegepast op een specifiek exemplaar?  
Als u een instelling van een status criterium instantie wijzigt, worden alle status criterium exemplaren van hetzelfde type op de Azure-VM gewijzigd. Bijvoorbeeld, als de drempelwaarde van de status van de vrije ruimte criterium schijfexemplaar die overeenkomt met de logische schijf C: wordt gewijzigd, geldt deze drempelwaarde voor alle andere logische schijven die zijn gedetecteerd en bewaakt voor dezelfde virtuele machine.

### <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>Bewaakt de status functie logische processors en kernen?
Nee, de status van een afzonderlijke processor en een logisch processor niveau is niet opgenomen voor een Windows. alleen het totale CPU-gebruik wordt standaard gecontroleerd om de CPU-belasting effectief te beoordelen op basis van het totale aantal logische Cpu's dat beschikbaar is voor de virtuele machine van Azure. 

### <a name="are-all-health-criteria-thresholds-configurable"></a>Zijn alle drempel waarden voor status criteria geconfigureerd?  
Drempel waarden voor de status criteria die zijn gericht op een Windows-VM, kunnen niet worden gewijzigd, omdat hun status wordt ingesteld op *actief* of *beschikbaar*. Wanneer u een query uitvoert op de status van de [API voor workload-bewaking](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components), wordt de *ComparisonOperator* -waarde **LessThan** of **GreaterThan** met een *drempel* waarde van **4** voor de service of entiteit weer gegeven als:
   - DNS-Client-Service Health-Service niet actief is. 
   - DHCP-client-service health-Service niet actief is. 
   - RPC-Service Health-Service niet actief is. 
   - Windows firewall-service health-Service niet actief is.
   - Windows-gebeurtenislogboek service health-Service niet actief is. 
   - Server-service health-Service niet actief is. 
   - Windows remote management-service health-Service wordt niet uitgevoerd. 
   - Bestandssysteemfout of beschadiging – logische schijf is niet beschikbaar.

Drempel waarden voor de volgende status criteria van Linux kunnen niet worden gewijzigd, omdat hun status al is ingesteld op *waar*. In de status wordt de *comparisonOperator* met de waarde **LessThan** en de *drempel* waarde **1** weer gegeven wanneer er een query wordt uitgevoerd vanuit de API voor het bewaken van workloads voor de entiteit, afhankelijk van de context:
   - Status van logische schijf – logische schijf is niet online / beschikbaar
   - De Status van de schijf-schijf is niet online / beschikbaar
   - Status van Network Adapter - netwerkadapter is uitgeschakeld

### <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Hoe kan ik wijzigen van waarschuwingen die zijn opgenomen in de status functie?
Regels voor waarschuwingen die zijn gedefinieerd voor elk criterium status worden niet weergegeven in de Azure-portal. U kunt een waarschuwings regel voor de status alleen in-of uitschakelen in de [API voor workload-bewaking](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). U kunt ook geen [Azure monitor actie groep](platform/action-groups.md) toewijzen voor status waarschuwingen in de Azure Portal. U kunt de API voor meldings instellingen alleen gebruiken om een actie groep te configureren die wordt geactiveerd wanneer een status waarschuwing wordt geactiveerd. Op dit moment kunt u actie groepen toewijzen aan een virtuele machine, zodat alle *status waarschuwingen* voor de virtuele machine dezelfde actie groepen activeren. In tegenstelling tot traditionele Azure-waarschuwingen is er geen concept van een aparte actiegroep voor elke status waarschuwingsregel. Bovendien worden alleen actiegroepen die zijn geconfigureerd voor e-mailadres of SMS-berichten ondersteund wanneer de gezondheid van waarschuwingen worden geactiveerd. 

### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Ik zie niet sommige of alle gegevens in de prestatiegrafieken voor mijn virtuele machine
Onze prestatie grafieken zijn bijgewerkt voor het gebruik van gegevens die zijn opgeslagen in de tabel *InsightsMetrics* .  Als u gegevens in deze grafieken wilt weer geven, moet u een upgrade uitvoeren om de nieuwe VM Insights-oplossing te gebruiken.  Raadpleeg onze Ga naar de [Veelgestelde vragen](insights/vminsights-ga-release-faq.md) voor meer informatie.

Als u geen prestatie gegevens in de tabel schijf of in een aantal prestatie grafieken ziet, zijn de prestatie meter items mogelijk niet geconfigureerd in de werk ruimte. Voer het volgende [Power shell-script](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell)uit om het probleem op te lossen.


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Hoe verschilt Azure Monitor voor VM's kaart functie van Servicetoewijzing?
De functie Azure Monitor voor VM's map is gebaseerd op Servicetoewijzing, maar heeft de volgende verschillen:

* De kaart weergave is toegankelijk via de VM-Blade en uit Azure Monitor voor VM's onder Azure Monitor.
* De verbindingen in de kaart kunnen nu worden geklikt en weer geven van de verbindings gegevens in het deel venster voor de geselecteerde verbinding.
* Er is een nieuwe API die wordt gebruikt om de kaarten te maken voor betere ondersteuning van complexere kaarten.
* Bewaakte Vm's zijn nu opgenomen in het knoop punt client groep en het ring diagram toont het aandeel van de bewaakte en niet-bewaakte virtuele machines in de groep.  Het kan ook worden gebruikt voor het filteren van de lijst met computers wanneer de groep wordt uitgevouwen.
* Bewaakte virtuele machines zijn nu opgenomen in de server poort groep knoop punten en het ring diagram toont het aandeel van de bewaakte en niet-bewaakte computers in de groep.  Het kan ook worden gebruikt voor het filteren van de lijst met computers wanneer de groep wordt uitgevouwen.
* De kaart stijl is bijgewerkt zodat deze consistent is met de app-toewijzing van Application Insights.
* De side panels zijn bijgewerkt en hebben niet de volledige set integraties die worden ondersteund in Servicetoewijzing-Updatebeheer, Wijzigingen bijhouden, beveiliging en Service Desk. 
* De optie voor het kiezen van groepen en machines die u wilt toewijzen, is bijgewerkt en ondersteunt nu abonnementen, resource groepen, Azure-schaal sets voor virtuele machines en Cloud Services.
* U kunt geen nieuwe Servicetoewijzing machine groepen maken in de functie Azure Monitor voor VM's kaart.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Waarom kunnen mijn prestatiegrafieken stippellijnen weergeven?
Dit kan een paar oorzaken hebben.  Als er sprake is van een onderbreking in het verzamelen van gegevens, worden de lijnen weer gegeven als gestippeld.  Als u de gegevens sampling frequentie hebt gewijzigd voor de prestatie meter items ingeschakeld (de standaard instelling is het verzamelen van gegevens elke 60 seconden), kunt u stippel lijnen in de grafiek zien als u een beperkt tijds bereik voor de grafiek kiest en de sampling frequentie kleiner is dan de Bucket grootte die in de grafiek wordt gebruikt (bijvoorbeeld de sampling frequentie is elke 10 minuten en elke Bucket in de grafiek is 5 minuten).  Als u een breder tijds bereik kiest voor weer gave, moeten de grafiek lijnen worden weer gegeven als ononderbroken lijnen in plaats van punten in dit geval.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Worden groepen ondersteund met Azure Monitor voor virtuele machines?
Ja, nadat u de afhankelijkheids agent hebt geïnstalleerd, verzamelen we informatie van de virtuele machines om groepen weer te geven op basis van het abonnement, de resource groep, de schaal sets voor virtuele machines en Cloud Services.  Als u Servicetoewijzing hebt gebruikt en computer groepen hebt gemaakt, worden deze ook weer gegeven.  Computer groepen worden ook weer gegeven in het filter groepen als u deze hebt gemaakt voor de werk ruimte die u bekijkt. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hoe kan ik raadpleegt u de details van wat is de 95e percentiel regel in de samengevoegde prestatie grafieken?
Standaard wordt de lijst gesorteerd om u de virtuele machines weer te geven die de hoogste waarde voor het 95e percentiel hebben voor de geselecteerde metriek, met uitzonde ring van de beschik bare geheugen grafiek, waarin de computers met de laagste waarde van het vijfde percentiel worden weer gegeven.  Als u op de grafiek klikt, wordt de **bovenste N lijst** weergave geopend met de juiste metriek geselecteerd.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Hoe verwerken de kaart functie dubbele IP-adressen over verschillende vnets en subnetten?
Als u IP-bereiken dupliceert met Vm's of virtuele-machine schaal sets van Azure in subnetten en vnets, kan dit ertoe leiden dat Azure Monitor voor VM's kaart onjuiste gegevens weergeeft. Dit is een bekend probleem en we onderzoeken de opties om deze ervaring te verbeteren.

### <a name="does-map-feature-support-ipv6"></a>Ondersteunt de functie voor het toewijzen van IPv6?
De toewijzings functie ondersteunt momenteel alleen IPv4 en we onderzoeken de ondersteuning voor IPv6. We bieden ook ondersteuning voor IPv4 die in IPv6 is getunneld.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Wanneer ik een kaart voor een resource groep of een andere grote groep laad, kan de kaart moeilijk worden weer gegeven
We hebben verbeteringen aangebracht in de toewijzing van het verwerken van grote en complexe configuraties, maar het is ook mogelijk dat een kaart een groot aantal knoop punten, verbindingen en een knoop punt als een cluster kan hebben.  We zijn om verder te verbeteren van ondersteuning voor meer schaalbaarheid.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Waarom ziet de netwerk grafiek op het tabblad prestaties er anders uit dan de netwerk grafiek op de overzichts pagina van de Azure VM?

Op de overzichts pagina voor een Azure-VM worden grafieken weer gegeven op basis van de meting van de activiteit van de host in de gast-VM.  Voor de netwerk grafiek in het overzicht van de Azure-VM wordt alleen het netwerk verkeer weer gegeven dat wordt gefactureerd.  Dit omvat niet het verkeer tussen virtuele netwerken.  De gegevens en grafieken die voor Azure Monitor voor VM's worden weer gegeven, zijn gebaseerd op gegevens van de gast-VM en de netwerk grafiek bevat alle TCP/IP-verkeer dat binnenkomend en uitgaand is voor die VM, inclusief intervirtueel netwerk.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Hoe wordt de reactie tijd gemeten voor gegevens die zijn opgeslagen in VMConnection en worden weer gegeven in het deel venster verbinding en in werkmappen?

De reactie tijd is een benadering. Omdat we de code van de toepassing niet instrumenteren, weten we niet echt wanneer een aanvraag begint en wanneer het antwoord wordt ontvangen. In plaats daarvan zien we dat er gegevens worden verzonden via een verbinding en dat de gegevens vervolgens weer worden teruggestuurd op deze verbinding. Onze agent houdt deze verzenden en ontvangen en probeert deze te koppelen: een volg orde van verzen dingen, gevolgd door een reeks ontvangen, wordt geïnterpreteerd als een aanvraag/antwoord-paar. De tijds duur tussen deze bewerkingen is de reactie tijd. Deze bevat de netwerk latentie en de verwerkings tijd van de server.

Deze benadering werkt goed voor protocollen die op aanvraag/antwoord zijn gebaseerd: een enkele aanvraag verloopt op de verbinding en er wordt één antwoord ontvangen. Dit is het geval voor HTTP (S) (zonder pipeline), maar niet aan andere protocollen is voldaan.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Zijn er beperkingen als ik aan het gratis prijs plan van Log Analytics?
Als u Azure Monitor hebt geconfigureerd met een Log Analytics-werk ruimte met behulp van de *gratis* prijs categorie, worden in azure monitor voor VM's kaart-functie alleen vijf verbonden computers ondersteund die zijn verbonden met de werk ruimte. Als u vijf Vm's hebt verbonden met een gratis werk ruimte, verbreekt u de verbinding van een van de virtuele machines en maakt u later een nieuwe virtuele machine, de nieuwe virtuele machine wordt niet bewaakt en wordt niet weer gegeven op de pagina overzicht.  

In dit voor beeld wordt u gevraagd de optie **nu proberen** te kiezen wanneer u de virtuele machine opent en **inzichten (preview)** selecteert in het linkerdeel venster, zelfs nadat het al op de virtuele machine is geïnstalleerd.  U wordt echter niet gevraagd naar opties die normaal gesp roken optreden als deze virtuele machine niet is opvoerd aan Azure Monitor voor VM's. 


## <a name="next-steps"></a>Volgende stappen
Als uw vraag hier niet wordt beantwoord, kunt u de volgende forums raadplegen voor aanvullende vragen en antwoorden.

- [Log Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

Voor algemene feedback over Azure Monitor gaat u naar het [Feedback forum](https://feedback.azure.com/forums/34192--general-feedback).
