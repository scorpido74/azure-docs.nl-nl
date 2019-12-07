---
title: Meldt zich aan Azure Monitor | Microsoft Docs
description: Hierin worden de logboeken in Azure Monitor beschreven die worden gebruikt voor geavanceerde analyse van bewakings gegevens.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 269744d5e9552d87c3fa619f33e02c833b3841be
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894161"
---
# <a name="logs-in-azure-monitor"></a>Logboeken in Azure Monitor

> [!NOTE]
> Alle gegevens die worden verzameld door Azure Monitor, komen in een van de volgende twee fundamentele typen, metrische gegevens en Logboeken. In dit artikel worden Logboeken beschreven. Raadpleeg de [para meters in azure monitor](data-platform-metrics.md) voor een gedetailleerde beschrijving van metrische gegevens en voor het [bewaken van door Azure monitor verzamelde informatie](data-platform.md) voor een vergelijking van de twee.

Logboeken in Azure Monitor zijn vooral handig voor het uitvoeren van complexe analyses op basis van gegevens uit verschillende bronnen. In dit artikel wordt beschreven hoe logboeken worden gestructureerd in Azure Monitor, wat u met de gegevens kunt doen en hoe verschillende gegevens bronnen worden geïdentificeerd waarmee gegevens in Logboeken worden opgeslagen.

> [!NOTE]
> Het is belang rijk om onderscheid te maken tussen Azure Monitor logboeken en bronnen van logboek gegevens in Azure. Gebeurtenissen op abonnements niveau in Azure worden bijvoorbeeld geschreven naar een [activiteiten logboek](activity-logs-overview.md) dat u kunt weer geven in het menu Azure monitor. De meeste resources schrijven operationele informatie naar een [bron logboek](resource-logs-overview.md) dat u kunt door sturen naar verschillende locaties. Azure Monitor-Logboeken is een platform voor gegevens registratie dat activiteiten logboeken en resource logboeken verzamelt samen met andere bewakings gegevens om een diepe analyse te bieden in uw hele set resources.

## <a name="what-are-azure-monitor-logs"></a>Wat zijn Azure Monitor logboeken?

Logboeken in Azure Monitor bevatten verschillende soorten gegevens die zijn ingedeeld in records met verschillende sets eigenschappen voor elk type. Logboeken kunnen numerieke waarden bevatten, zoals Azure Monitor metrische gegevens, maar bevatten normaal gesp roken tekst met gedetailleerde beschrijvingen. Ze verschillen nog verder van metrische gegevens, omdat ze in hun structuur variëren en vaak met regel matige tussen pozen niet worden verzameld. Telemetrie, zoals gebeurtenissen en traceringen, wordt opgeslagen Azure Monitor logboeken naast prestatie gegevens, zodat deze allemaal kunnen worden gecombineerd voor analyse.

Een gemeen schappelijk type logboek vermelding is een gebeurtenis die sporadisch wordt verzameld. Gebeurtenissen worden gemaakt door een toepassing of service en bevatten doorgaans voldoende informatie om hun eigen volledige context te bieden. Een gebeurtenis kan bijvoorbeeld duiden dat een bepaalde resource is gemaakt of gewijzigd, een nieuwe host gestart als reactie op toegenomen verkeer, of er is een fout gedetecteerd in een toepassing.

 Omdat de indeling van de gegevens variëren kan, kunnen toepassingen aangepaste logboeken kunnen maken met behulp van de structuur die ze nodig hebben. Metrische gegevens kunnen zelfs worden opgeslagen in Logboeken om ze te combi neren met andere bewakings gegevens voor trending en andere gegevens analyse.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Wat kunt u doen met Azure Monitor-logboeken?
De volgende tabel geeft een lijst van de verschillende manieren waarop u Logboeken in Azure Monitor kunt gebruiken.


|  |  |
|:---|:---|
| Analyseren | Gebruik [log Analytics](../log-query/get-started-portal.md) in de Azure Portal om [logboek query's](../log-query/log-query-overview.md) te schrijven en logboek gegevens interactief te analyseren met behulp van de krachtige Data Explorer Analysis-engine.<br>Gebruik de [Application Insights Analytics-console](../app/analytics.md) in de Azure Portal om logboek query's te schrijven en logboek gegevens interactief van Application Insights te analyseren. |
| Visualiseren | Query resultaten weer geven als tabellen of grafieken aan een [Azure-dash board](../../azure-portal/azure-portal-dashboards.md).<br>Een [werkmap](../app/usage-workbooks.md) maken om te combi neren met meerdere gegevens sets in een interactief rapport. <br>De resultaten van een query voor het exporteren [Power BI](powerbi.md) verschillende visualisaties gebruiken en delen met gebruikers buiten Azure.<br>De resultaten van een query exporteren naar [Grafana](grafana-plugin.md) om gebruik te maken van Dash boards en combi neren met andere gegevens bronnen.|
| Waarschuwing | Configureren van een [waarschuwingsregel](alerts-log.md) die duurt of verzendt een melding [automatische actie](action-groups.md) wanneer de resultaten van de query overeenkomt met een bepaalde resultaat.<br>Een [regel voor metrische waarschuwingen](alerts-metric-logs.md) configureren voor bepaalde logboek gegevens logboeken die zijn geëxtraheerd als metriek. |
| Ophalen | Toegang tot logboek query resultaten van een opdracht regel met behulp van [Azure cli](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Access-logboek query resultaten van een opdracht regel met behulp van [Power shell-cmdlets](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Toegang tot logboek query resultaten van een aangepaste toepassing met behulp van [rest API](https://dev.loganalytics.io/). |
| Exporteren | Maak een werk stroom om logboek gegevens op te halen en kopieer deze naar een externe locatie met behulp van [Logic apps](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Hoe worden gegevens in Azure Monitor-logboeken gestructureerd?
Gegevens die worden verzameld door Azure Monitor logboeken worden opgeslagen in een [log Analytics-werk ruimte](../platform/design-logs-deployment.md). Elke werk ruimte bevat meerdere tabellen die elke gegevens van een bepaalde bron opslaan. Hoewel alle tabellen [enkele algemene eigenschappen](log-standard-properties.md)delen, heeft elk een unieke set eigenschappen, afhankelijk van het type gegevens dat wordt opgeslagen. Een nieuwe werk ruimte bevat standaard sets tabellen en er worden meer tabellen toegevoegd door verschillende bewakings oplossingen en andere services die naar de werk ruimte schrijven.

Logboek gegevens van Application Insights gebruiken dezelfde Log Analytics engine als werk ruimten, maar worden afzonderlijk opgeslagen voor elke bewaakte toepassing. Elke toepassing heeft een standaardset tabellen voor het opslaan van gegevens zoals toepassings aanvragen, uitzonde ringen en pagina weergaven.

In logboek query's worden gegevens uit een Log Analytics-werk ruimte of een Application Insights toepassing gebruikt. U kunt een query voor meerdere [bronnen](../log-query/cross-workspace-query.md) gebruiken om toepassings gegevens samen met andere logboek gegevens te analyseren of om query's te maken, inclusief meerdere werk ruimten of toepassingen.

![Werkruimten](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Logboekquery's
Gegevens in Azure Monitor logboeken worden opgehaald met een [logboek query](../log-query/log-query-overview.md) die is geschreven met de [Kusto-query taal](../log-query/get-started-queries.md), waarmee u snel verzamelde gegevens kunt ophalen, consolideren en analyseren. Gebruik [log Analytics](../log-query/portals.md) om logboek query's te schrijven en te testen in de Azure Portal. Hiermee kunt u interactief met de resultaten werken of deze vastmaken aan een dash board om ze te bekijken met andere visualisaties.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Open [log Analytics van Application Insights](../app/analytics.md) om Application Insights gegevens te analyseren.

![Application Insights Analytics](media/data-platform-logs/app-insights-analytics.png)

U kunt ook logboek gegevens ophalen met behulp van de [log Analytics-API](https://dev.loganalytics.io/documentation/overview) en de [Application Insights rest API](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Bronnen van Azure Monitor-logboeken
Azure Monitor kunt logboek gegevens verzamelen van verschillende bronnen in zowel Azure als on-premises resources. De volgende tabellen geven een lijst van de verschillende gegevens bronnen die beschikbaar zijn vanuit verschillende bronnen die gegevens schrijven naar Azure Monitor Logboeken. Elk bevat een koppeling naar informatie over de vereiste configuratie.

### <a name="azure-tenant-and-subscription"></a>Azure-Tenant en-abonnement

| Gegevens | Beschrijving |
|:---|:---|
| Controle logboeken Azure Active Directory | Geconfigureerd via Diagnostische instellingen voor elke directory. Zie [Azure AD-logboeken integreren met Azure monitor-logboeken](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Activiteitenlogboeken | Wordt standaard afzonderlijk opgeslagen en kan worden gebruikt voor bijna realtime-waarschuwingen. Installeer de oplossing voor activiteiten logboek analyse om naar Log Analytics-werk ruimte te schrijven. Zie [Azure-activiteiten logboeken verzamelen en analyseren in log Analytics](activity-log-collect.md). |

### <a name="azure-resources"></a>Azure-bronnen

| Gegevens | Beschrijving |
|:---|:---|
| Diagnostische gegevens over resources | Diagnostische instellingen configureren om te schrijven naar diagnostische gegevens, met inbegrip van de waarden voor een Log Analytics-werk ruimte. Zie [Azure-resource logboeken streamen om te log Analytics](resource-logs-collect-storage.md). |
| Bewakingsoplossingen | Bewakings oplossingen schrijven gegevens die ze verzamelen naar hun Log Analytics-werk ruimte. Zie [Details over het verzamelen van gegevens voor beheer oplossingen in azure](../insights/solutions-inventory.md) voor een lijst met oplossingen. Zie [oplossingen controleren in azure monitor](../insights/solutions.md) voor meer informatie over het installeren en gebruiken van oplossingen. |
| Metrische gegevens | Verstuur de platform metrieken voor Azure Monitor resources naar een Log Analytics werk ruimte om logboek gegevens langere tijd te bewaren en om complexe analyses uit te voeren met andere gegevens typen met behulp van de [Kusto-query taal](/azure/kusto/query/). Zie [Azure-resource logboeken streamen om te log Analytics](resource-logs-collect-storage.md). |
| Azure-tabelopslag | Gegevens verzamelen uit Azure Storage waar sommige Azure-resources bewakings gegevens schrijven. Zie [Azure Blob Storage gebruiken voor IIS en Azure-tabel opslag voor gebeurtenissen met log Analytics](azure-storage-iis-table.md). |

### <a name="virtual-machines"></a>Virtual Machines

| Gegevens | Beschrijving |
|:---|:---|
|  Agent-gegevensbronnen | Gegevens bronnen die worden verzameld van [Windows](agent-windows.md) -en [Linux](../learn/quick-collect-linux-computer.md) -agents, bevatten gebeurtenissen, prestatie gegevens en aangepaste Logboeken. Raadpleeg [agent gegevens bronnen in azure monitor](data-sources.md) voor een lijst met gegevens bronnen en Details over de configuratie. |
| Bewakingsoplossingen | Bewakings oplossingen schrijven gegevens die ze verzamelen van agents naar hun Log Analytics-werk ruimte. Zie [Details over het verzamelen van gegevens voor beheer oplossingen in azure](../insights/solutions-inventory.md) voor een lijst met oplossingen. Zie [oplossingen controleren in azure monitor](../insights/solutions.md) voor meer informatie over het installeren en gebruiken van oplossingen. |
| System Center Operations Manager | Operations Manager beheer groep verbinden met Azure Monitor om gebeurtenis-en prestatie gegevens van on-premises agents in Logboeken te verzamelen. Zie [verbinding maken tussen Operations Manager en log Analytics](om-agents.md) voor meer informatie over deze configuratie. |


### <a name="applications"></a>Applicaties

| Gegevens | Beschrijving |
|:---|:---|
| Aanvragen en uitzonde ringen | Gedetailleerde gegevens over toepassings aanvragen en uitzonde ringen vindt u in de tabellen _aanvragen_, _page views_en _uitzonde ringen_ . Aanroepen naar [externe onderdelen](../app/asp-net-dependencies.md) bevinden zich in de tabel _afhankelijkheden_ . |
| Gebruik en prestaties | De prestaties van de toepassing zijn beschikbaar in de tabellen _aanvragen_, _browserTimings_ en _Performance Counters_ . De gegevens voor [aangepaste metrieken](../app/api-custom-events-metrics.md#trackevent) bevindt zich in de tabel _customMetrics_ .|
| Gegevens traceren | De resultaten van [gedistribueerde tracering](../app/distributed-tracing.md) worden opgeslagen in de tabel _traces_ . |
| Beschikbaarheidstests | Samenvattings gegevens van [beschikbaarheids testen](../app/monitor-web-app-availability.md) worden opgeslagen in de tabel _availabilityResults_ . Gedetailleerde gegevens van deze tests bevinden zich in afzonderlijke opslag en zijn toegankelijk via Application Insights in de Azure Portal. |

### <a name="insights"></a>Inzichten

| Gegevens | Beschrijving |
|:---|:---|
| Azure Monitor voor containers | Inventaris-en prestatie gegevens die worden verzameld door [Azure monitor voor containers](../insights/container-insights-overview.md). Raadpleeg [container gegevens verzamelings Details](../insights/container-insights-log-search.md#container-records) voor een lijst met tabellen. |
| Azure Monitor voor virtuele machines | Toewijzings-en prestatie gegevens die door [Azure monitor voor VM's](../insights/vminsights-overview.md)zijn verzameld. Zie [Logboeken van Azure monitor voor VM's opvragen](../insights/vminsights-log-search.md) voor meer informatie over het uitvoeren van query's op deze gegevens. |

### <a name="custom"></a>Aangepast 

| Gegevens | Beschrijving |
|:---|:---|
| REST-API | Gegevens schrijven naar een Log Analytics-werk ruimte vanuit een wille keurige REST-client. Zie [logboek gegevens naar Azure monitor verzenden met de HTTP-gegevens verzamelaar-API](data-collector-api.md) voor meer informatie.
| Logische app | Gegevens schrijven naar een Log Analytics-werk ruimte vanuit een werk stroom van een logische app met de actie **Azure log Analytics gegevens verzamelaar** . |

### <a name="security"></a>Beveiliging

| Gegevens | Beschrijving |
|:---|:---|
| Azure Beveiligingscentrum | [Azure Security Center](/azure/security-center/) worden gegevens opgeslagen die in een log Analytics-werk ruimte worden verzameld, waar ze kunnen worden geanalyseerd met andere logboek gegevens. Zie [gegevens verzameling in azure Security Center](../../security-center/security-center-enable-data-collection.md) voor meer informatie over werkruimte configuratie. |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) slaat gegevens van gegevens bronnen op in een log Analytics-werk ruimte. Zie [verbinding maken met gegevens bronnen](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [Azure monitor-gegevens platform](data-platform.md).
- Meer informatie over [metrische gegevens in azure monitor](data-platform-metrics.md).
- Meer informatie over de [bewakingsgegevens beschikbaar](data-sources.md) voor verschillende resources in Azure.
