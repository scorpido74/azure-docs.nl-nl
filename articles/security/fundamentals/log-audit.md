---
title: Logboek registratie en controle van Azure | Microsoft Docs
description: Meer informatie over hoe u logboek gegevens kunt gebruiken om grondige inzichten over uw toepassing te krijgen.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: TomSh
ms.openlocfilehash: d64cdce34127b066aedc8a5fcd6ec3a891b38c5e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262852"
---
# <a name="azure-logging-and-auditing"></a>Logboekregistratie en bewaking in Azure

Azure biedt een breed scala aan Configureer bare opties voor beveiligings controle en logboek registratie waarmee u hiaten in uw beveiligings beleid en-mechanismen kunt identificeren. In dit artikel vindt u informatie over het genereren, verzamelen en analyseren van beveiligings logboeken van services die worden gehost op Azure.

> [!Note]
> Bepaalde aanbevelingen in dit artikel kunnen leiden tot groter gegevens-, netwerk-of COMPUTE-gebruik en uw licentie-of abonnements kosten verhogen.

## <a name="types-of-logs-in-azure"></a>Typen logboeken in azure

Cloud toepassingen zijn complex, met veel bewegende onderdelen. Logboeken bieden gegevens om uw toepassingen up-to-date te houden. Logboeken helpen u bij het oplossen van problemen met het verleden of het voor komen van mogelijke oorzaken. En ze kunnen helpen bij het verbeteren van de prestaties of het onderhoud van toepassingen, of het automatiseren van acties waarvoor anders hand matige interventie nodig zou zijn.

Azure-logboeken zijn ingedeeld in de volgende typen:
* **Control/Management-logboeken** bevatten informatie over Azure Resource Manager Create-, update-en delete-bewerkingen. Zie activiteiten logboeken van [Azure](../../azure-monitor/platform/activity-logs-overview.md)voor meer informatie.

* **Gegevens vlak logboeken** bevatten informatie over gebeurtenissen die worden gegenereerd als onderdeel Azure-resource gebruik. Voor beelden van dit type logboek zijn het Windows-gebeurtenis systeem, de beveiliging en toepassings Logboeken in een virtuele machine (VM) [](../../azure-monitor/platform/resource-logs-overview.md) en de diagnostische logboeken die zijn geconfigureerd via Azure monitor.

* **Verwerkte gebeurtenissen** bieden informatie over geanalyseerde gebeurtenissen/waarschuwingen die namens u zijn verwerkt. Voor beelden van dit type zijn [Azure Security Center waarschuwingen](../../security-center/security-center-managing-and-responding-alerts.md) waarbij [Azure Security Center](../../security-center/security-center-intro.md) uw abonnement heeft verwerkt en geanalyseerd en beknopte beveiligings waarschuwingen biedt.

De volgende tabel bevat de belangrijkste typen logboeken die beschikbaar zijn in Azure:

| Logboekcategorie | Logboektype | Gebruik | Integratie |
| ------------ | -------- | ------ | ----------- |
|[Activiteiten logboeken](../../azure-monitor/platform/activity-logs-overview.md)|Gebeurtenissen op het vlak van het besturings element op Azure Resource Manager resources|  Biedt inzicht in de bewerkingen die zijn uitgevoerd voor de resources in uw abonnement.|    Rest API, [Azure Monitor](../../azure-monitor/platform/activity-logs-overview.md)|
|[Diagnostische logboeken van Azure](../../azure-monitor/platform/resource-logs-overview.md)|Frequente gegevens over de werking van Azure Resource Manager resources in het abonnement|    Biedt inzicht in de bewerkingen die uw resource zelf heeft uitgevoerd.| Azure Monitor, [Stream](../../azure-monitor/platform/resource-logs-overview.md)|
|[Azure AD-rapportage](../../active-directory/reports-monitoring/overview-reports.md)|Logboeken en rapporten | Rapporteert aanmeldings activiteiten van gebruikers en informatie over systeem activiteiten over gebruikers en groeps beheer.|[Graph API](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[Virtuele machines en Cloud Services](../../azure-monitor/learn/quick-collect-azurevm.md)|Windows Event Log-service en Linux syslog|  Legt systeem gegevens vast en registreert gegevens op de virtuele machines en brengt deze gegevens over naar een opslag account van uw keuze.|   Windows (met behulp van Windows Azure Diagnostics [[wad](../../monitoring-and-diagnostics/azure-diagnostics.md)]-opslag) en Linux in azure monitor|
|[Azure Opslaganalyse](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Met opslag logboek registratie kunt u metrische gegevens voor een opslag account|Biedt inzicht in Trace-aanvragen, analyse van gebruiks trends en het vaststellen van problemen met uw opslag account.|   REST API of de [client bibliotheek](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Stroom logboeken voor netwerk beveiligings groepen (NSG)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|JSON-indeling, geeft uitgaande en inkomende stromen per regel weer|Geeft informatie weer over binnenkomend en uitgaand IP-verkeer via een netwerk beveiligings groep.|[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Toepassings inzicht](../../azure-monitor/app/app-insights-overview.md)|Logboeken, uitzonde ringen en aangepaste diagnostische gegevens|  Biedt een service voor het controleren van de prestaties van toepassingen (APM) voor webontwikkelaars op meerdere platforms.| REST API, [Power bi](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Gegevens/beveiligings waarschuwingen verwerken|    Waarschuwingen voor Azure Security Center, Azure Monitor logboeken|    Geeft beveiligings informatie en waarschuwingen.|  REST Api's, JSON|

### <a name="activity-logs"></a>Activiteitenlogboeken

[Azure-activiteiten logboeken](../../azure-monitor/platform/activity-logs-overview.md) bieden inzicht in de bewerkingen die zijn uitgevoerd voor de resources in uw abonnement. Activiteiten logboeken waren voorheen bekend als "audit logs" of "Operational logs", omdat ze besturingselementgebeurtenissen op het [vlak](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) van uw abonnementen rapporteren. 

Met activiteiten Logboeken kunt u het bepalen van ' wat, wie en wanneer ' voor schrijf bewerkingen (dat wil zeggen, plaatsen, posten of verwijderen). Met activiteiten Logboeken kunt u ook de status van de bewerking en andere relevante eigenschappen begrijpen. Activiteiten logboeken bevatten geen lees bewerkingen (GET).

In dit artikel plaatst, boekt en verwijderen verwijzen naar alle schrijf bewerkingen die een activiteiten logboek op de resources bevat. U kunt bijvoorbeeld de activiteiten Logboeken gebruiken om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

![Diagram activiteiten logboek](./media/log-audit/azure-log-audit-fig1.png)

U kunt gebeurtenissen uit een activiteiten logboek ophalen met behulp van de Azure Portal, [Azure cli](../../storage/common/storage-azure-cli.md), Power shell-cmdlets en [Azure monitor rest API](../../azure-monitor/platform/rest-api-walkthrough.md). Activiteiten logboeken hebben een gegevens Bewaar periode van 90 dagen.

Integratie scenario's voor een activiteiten logboek gebeurtenis:

* [Maak een e-mail-of webhook-waarschuwing die wordt geactiveerd door een gebeurtenis in het activiteiten logboek](../../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md).

* [Stream het naar een event hub](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) voor opname door een service van derden of een aangepaste analyse oplossing zoals PowerBI.

* Analyseer deze in Power bi met behulp van het [Power bi-inhouds pakket](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Sla het bestand op in een opslag account voor archivering of hand matige inspectie](../../azure-monitor/platform/archive-activity-log.md). U kunt de Bewaar periode (in dagen) opgeven door gebruik te maken van logboek profielen.

* Query's en weer geven in de Azure Portal.

* Zoek het via een Power shell-cmdlet, Azure CLI of REST API.

* Exporteer het activiteiten logboek met logboek profielen om [Logboeken te Azure monitor](../../log-analytics/log-analytics-queries.md).

U kunt een opslag account of [Event hub naam ruimte](../../event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-capture.md) gebruiken die zich niet in hetzelfde abonnement bevindt als de locatie waarmee het logboek wordt gegenereerd. Degene die de instelling configureert, moet beschikken over de juiste RBAC-toegang [(op rollen gebaseerd toegangs beheer)](../../role-based-access-control/role-assignments-portal.md) voor beide abonnementen.

### <a name="azure-diagnostics-logs"></a>Azure Diagnostics-logboeken

Diagnostische logboeken van Azure worden verzonden door een resource die rijke, frequente gegevens bevat over de werking van die resource. Resourcetype is afhankelijk van de inhoud van deze logboeken. [Windows-gebeurtenis systeem logboeken](../../azure-monitor/platform/data-sources-windows-events.md) zijn bijvoorbeeld een categorie Diagnostische logboeken voor vm's, en [blob-, tabel-en wachtrij logboeken](../../storage/common/storage-monitor-storage-account.md) zijn categorieën met Diagnostische logboeken voor opslag accounts. Diagnostische logboeken verschillen van activiteiten logboeken, die inzicht bieden in de bewerkingen die zijn uitgevoerd op resources in uw abonnement.

![Diagrammen van Azure Diagnostics-logboeken](./media/log-audit/azure-log-audit-fig2.png)

Diagnostische logboeken van Azure bieden meerdere configuratie opties, zoals de Azure Portal, Power shell, Azure CLI en de REST API.

**Integratie scenario's**

* Sla ze op in een [opslag account](../../azure-monitor/platform/archive-diagnostic-logs.md) voor controle of hand matige inspectie. U kunt de Bewaar periode (in dagen) opgeven met behulp van de diagnostische instellingen.

* [U kunt ze streamen naar Event hubs](../../azure-monitor/platform/resource-logs-stream-event-hubs.md) voor opname door een service van derden of een aangepaste analyse oplossing, zoals [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Analyseer ze met [Azure monitor](../../log-analytics/log-analytics-queries.md)-Logboeken.

**Ondersteunde services, schema voor Diagnostische logboeken en ondersteunde logboek categorieën per resource type**


| Service | Schema en documentatie | Resourcetype | Category |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Azure Monitor logboeken voor Load Balancer (preview-versie)](../../load-balancer/load-balancer-monitor-log.md)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Netwerkbeveiligingsgroepen|[Azure Monitor logboeken voor netwerk beveiligings groepen](../../virtual-network/virtual-network-nsg-manage-log.md)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Diagnostische logboek registratie voor Application Gateway](../../application-gateway/application-gateway-diagnostics.md)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Key Vault logboeken](../../key-vault/key-vault-logging.md)|Microsoft.KeyVault/vaults|Audit event|
|Azure Search|[Zoek Traffic Analytics inschakelen en gebruiken](../../search/search-traffic-analytics.md)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Diagnostische logboeken openen voor Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Controleren<br>Aanvragen|
|Azure Data Lake Analytics|[Diagnostische logboeken openen voor Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Controleren<br>Aanvragen|
|Azure Logic Apps|[Aangepast Logic Apps B2B-volgschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Diagnostische logboeken Azure Batch](../../batch/batch-diagnostics.md)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Azure Monitor logboeken voor Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Diagnostische logboeken Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[Taak diagnose logboeken](../../stream-analytics/stream-analytics-job-diagnostic-logs.md)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Kan worden uitgevoerd<br>Ontwerpen|
|Azure Service Bus|[Diagnostische logboeken van Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Azure Active Directory-rapportage

Azure Active Directory (Azure AD) bevat beveiligings-, activiteiten-en controle rapporten voor de directory van een gebruiker. Het [Azure AD-controle rapport](../../active-directory/active-directory-reporting-azure-portal.md) helpt u bij het identificeren van geprivilegieerde acties die zijn opgetreden in de Azure AD-instantie van de gebruiker. Geprivilegieerde acties omvatten verhogings wijzigingen (bijvoorbeeld het maken van functies of het opnieuw instellen van wacht woorden), het wijzigen van beleids configuraties (bijvoorbeeld wachtwoord beleid) of wijzigingen in de Directory-configuratie (bijvoorbeeld wijzigingen in de instellingen van het domein Federatie).

De rapporten bevatten de controle record voor de gebeurtenis naam, de gebruiker die de actie heeft uitgevoerd, de doel resource die wordt beïnvloed door de wijziging en de datum en tijd (in UTC). Gebruikers kunnen de lijst met controle gebeurtenissen voor Azure AD ophalen via de [Azure Portal](https://portal.azure.com/), zoals wordt beschreven in [uw audit logboeken weer geven](../../active-directory/reports-monitoring/overview-reports.md). 

De opgenomen rapporten worden weer gegeven in de volgende tabel:

| Beveiligingsrapporten | Activiteitsrapporten | Controlerapporten |
| :--------------- | :--------------- | :------------ |
|Aanmeldingen van onbekende bronnen| Toepassingsgebruik: samenvatting| Directorycontrolerapport|
|Aanmeldingen na meerdere mislukte pogingen|  Toepassingsgebruik: gedetailleerd||
|Aanmeldingen vanuit meerdere locaties|    Toepassingsdashboard||
|Aanmeldingen van IP-adressen met verdachte activiteit|   Fouten bij het inrichten van een account||
|Onregelmatige aanmeldingsactiviteiten|    Afzonderlijke gebruikersapparaten||
|Aanmeldingen vanaf mogelijk geïnfecteerde apparaten|   Afzonderlijke gebruikers activiteit||
|Gebruikers met afwijkende aanmeldingsactiviteiten| Rapport met groepsactiviteiten||
||Rapport activiteit registratie van wacht woord opnieuw instellen||
||Activiteit voor wachtwoord opnieuw instellen||

De gegevens in deze rapporten kunnen nuttig zijn voor uw toepassingen, zoals Security Information and Event Management-systemen (SIEM), controle en business intelligence-hulpprogram ma's. De API's van Azure AD Reporting bieden toegang tot de gegevens op programmeerniveau via een set op REST-gebaseerde API's. U kunt deze [api's](../../active-directory/active-directory-reporting-api-getting-started-azure-portal.md) aanroepen vanuit verschillende programmeer talen en hulpprogram ma's.

De Bewaar periode van een gebeurtenis in het Azure AD-controle rapport varieert tussen 7-90 dagen, afhankelijk van het type licentie dat aan de Tenant is gekoppeld. 

> [!Note]
> Zie het [Bewaar beleid voor Azure AD-rapporten](../../active-directory/reports-monitoring/reference-reports-data-retention.md)voor meer informatie over het bewaren van rapporten.

Als u wilt dat uw controle gebeurtenissen langer worden bewaard, gebruikt u de rapportage-API om [controle gebeurtenissen](../../active-directory/active-directory-reporting-activity-audit-logs.md) regel matig in een afzonderlijk gegevens archief in te trekken.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Logboeken voor virtuele machines die gebruikmaken van Azure Diagnostics

[Azure Diagnostics](../../monitoring-and-diagnostics/azure-diagnostics.md) is de mogelijkheid binnen Azure om het verzamelen van diagnostische gegevens op een geïmplementeerde toepassing mogelijk te maken. U kunt de diagnostische uitbrei ding van een van de verschillende bronnen gebruiken. Momenteel wordt de [Azure Cloud service-Web-en-werk rollen](../../cloud-services/cloud-services-choose-me.md)ondersteund.

![Logboeken voor virtuele machines die gebruikmaken van Azure Diagnostics](./media/log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineslearnpathsdeploy-a-website-with-azure-virtual-machines-that-are-running-microsoft-windows-and-service-fabricservice-fabricservice-fabric-overviewmd"></a>[Virtuele Azure-machines](/learn/paths/deploy-a-website-with-azure-virtual-machines/) met micro soft Windows en [service Fabric](../../service-fabric/service-fabric-overview.md)

U kunt Azure Diagnostics op een virtuele machine inschakelen door een van de volgende handelingen uit te voeren:

* [Visual Studio gebruiken om virtuele machines van Azure te traceren](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Azure Diagnostics op afstand instellen op een virtuele machine van Azure](../../virtual-machines/virtual-machines-dotnet-diagnostics.md)

* [Power shell gebruiken om diagnostische gegevens in te stellen op virtuele machines van Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Een virtuele Windows-machine met controle en diagnostische gegevens maken met behulp van een Azure Resource Manager sjabloon](../../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Storage Analytics

[Azure Opslaganalyse](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) logboeken en levert metrische gegevens voor een opslag account. Deze gegevens kunt u gebruiken om aanvragen te traceren, gebruikstrends te analyseren en een diagnose uit te voeren voor problemen met uw opslagaccount. Opslaganalyse logboek registratie is beschikbaar voor de [Azure-Blob, Azure Queue en Azure Table Storage-services](../../storage/common/storage-introduction.md). Opslaganalyse registreert gedetailleerde informatie over geslaagde en mislukte aanvragen voor een opslag service.

U kunt deze informatie gebruiken om afzonderlijke aanvragen te bewaken en problemen met een opslag service te diagnosticeren. Aanvragen worden op de beste basis geregistreerd. Logboek vermeldingen worden alleen gemaakt als er aanvragen worden gedaan voor het service-eind punt. Als een opslag account bijvoorbeeld activiteit heeft in het BLOB-eind punt, maar niet in de tabel-of wachtrij-eind punten, worden alleen logboeken gemaakt die betrekking hebben op de Blob Storage-service.

Als u Opslaganalyse wilt gebruiken, schakelt u deze afzonderlijk in voor elke service die u wilt bewaken. U kunt deze functie inschakelen in de [Azure Portal](https://portal.azure.com/). Zie [een opslag account bewaken in de Azure Portal](../../storage/common/storage-monitor-storage-account.md)voor meer informatie. U kunt Opslaganalyse ook via een programma inschakelen via de REST API of de client bibliotheek. Gebruik de bewerking service-eigenschappen instellen om Opslaganalyse afzonderlijk in te scha kelen voor elke service.

De geaggregeerde gegevens worden opgeslagen in een bekende BLOB (voor logboek registratie) en in bekende tabellen (voor metrieken), die u kunt openen met behulp van de Blob Storage-service en de Table Storage-service-Api's.

Opslaganalyse heeft een limiet van 20 terabyte (TB) voor de hoeveelheid opgeslagen gegevens die onafhankelijk is van de totale limiet voor uw opslag account. Alle logboeken worden opgeslagen in [blok](../../storage/common/storage-analytics.md) -blobs in een container met de naam $Logs, die automatisch wordt gemaakt wanneer u Opslaganalyse voor een opslag account inschakelt.

> [!Note]
> * Zie [Opslaganalyse en facturering](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)voor meer informatie over het beleid voor facturering en het bewaren van gegevens.
> * Zie [Azure Storage schaal baarheid en prestatie doelen](../../storage/common/storage-scalability-targets.md)voor meer informatie over limieten voor opslag accounts.

Opslaganalyse worden de volgende typen geverifieerde en anonieme aanvragen geregistreerd:

| Geverifieerd  | Anoniem|
| :------------- | :-------------|
| Geslaagde aanvragen | Geslaagde aanvragen |
|Mislukte aanvragen, inclusief time-out, beperking, netwerk, autorisatie en andere fouten | Aanvragen met behulp van een hand tekening voor gedeelde toegang, waaronder mislukte en geslaagde aanvragen |
| Aanvragen met behulp van een hand tekening voor gedeelde toegang, waaronder mislukte en geslaagde aanvragen |Time-outfouten voor zowel de client als de server |
|   Aanvragen voor Analytics-gegevens |    Mislukte GET-aanvragen met de fout code 304 (niet gewijzigd) |
| Aanvragen die worden gedaan door Opslaganalyse zichzelf, zoals het maken of verwijderen van een logboek, worden niet geregistreerd. Een volledige lijst met de geregistreerde gegevens wordt gedocumenteerd in [Opslaganalyse geregistreerde bewerkingen en status berichten](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) en [Opslaganalyse logboek indeling](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Alle andere mislukte anonieme aanvragen worden niet geregistreerd. Een volledige lijst met de geregistreerde gegevens wordt gedocumenteerd in [Opslaganalyse geregistreerde bewerkingen en status berichten](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) en [Opslaganalyse logboek indeling](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Azure-netwerk logboeken

Netwerk logboek registratie en bewaking in Azure is uitgebreid en omvat twee algemene categorieën:

* [Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md): Op scenario's gebaseerde netwerk bewaking wordt meegeleverd met de functies in Network Watcher. Deze service omvat pakket opname, volgende hop, IP-stroom controleren, beveiligings groep weer geven, NSG stroom Logboeken. Bewaking op scenario niveau biedt een end-to-end weer gave van netwerk bronnen in tegens telling tot afzonderlijke netwerk bron bewaking.

* [Bron bewaking](../../network-watcher/network-watcher-monitoring-overview.md): Bewaking op resource niveau bestaat uit vier functies, Diagnostische logboeken, metrische gegevens, probleem oplossing en resource status. Al deze functies zijn gebaseerd op het netwerk resource niveau.

![Azure-netwerk logboeken](./media/log-audit/azure-log-audit-fig4.png)

Network Watcher is een regionale service waarmee u voor waarden kunt controleren en diagnosticeren op het niveau van een netwerk scenario in, naar en Azure. Diagnostische en visualisatie hulpprogramma's voor netwerken die beschikbaar zijn met Network Watcher helpen u bij het begrijpen, diagnosticeren en verkrijgen van inzicht in uw netwerk in Azure.

### <a name="network-security-group-flow-logging"></a>Logboek registratie van stroom van netwerk beveiligings groep

[NSG-stroom logboeken](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) zijn een functie van Network Watcher die u kunt gebruiken om informatie weer te geven over binnenkomend en IP-verkeer via een NSG. Deze stroom logboeken worden geschreven in JSON-indeling en weer gegeven:
* Uitgaande en inkomende stromen per regel.
* De NIC waarop de stroom van toepassing is.
* 5-tuple informatie over de stroom: de bron-of doel-IP, de bron-of doel poort en het protocol.
* Of het verkeer is toegestaan of geweigerd.

Hoewel stroom logboeken doel-Nsg's worden, worden ze niet op dezelfde manier weer gegeven als de andere logboeken. Stroom logboeken worden alleen opgeslagen in een opslag account.

Hetzelfde Bewaar beleid dat in andere logboeken wordt weer gegeven, is van toepassing op stroom Logboeken. Logboeken hebben een Bewaar beleid dat u van 1 dag tot 365 dagen kunt instellen. Als geen bewaarbeleid is ingesteld, worden de logboeken voor altijd bewaard.

**Diagnostische logboeken**

Periodieke en spontane gebeurtenissen worden gemaakt door netwerk bronnen en worden aangemeld bij opslag accounts en verzonden naar een Event Hub-of Azure Monitor-Logboeken. De logboeken bieden inzicht in de status van een resource. Ze kunnen worden weer gegeven in hulpprogram ma's als Power BI en Azure Monitor-Logboeken. Zie [Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md)-logboeken voor meer informatie over het weer geven van Diagnostische logboeken.

![Logboeken met diagnostische gegevens](./media/log-audit/azure-log-audit-fig5.png)

Diagnostische logboeken zijn beschikbaar voor [Load Balancer](../../load-balancer/load-balancer-monitor-log.md), [netwerk beveiligings groepen](../../virtual-network/virtual-network-nsg-manage-log.md), routes en [Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

Network Watcher biedt een weer gave Diagnostische logboeken. Deze weer gave bevat alle netwerk bronnen die ondersteuning bieden voor diagnostische logboek registratie. In deze weer gave kunt u netwerk bronnen gemakkelijk en snel en eenvoudig inschakelen en uitschakelen.


Naast de eerder genoemde logboek functies heeft Network Watcher momenteel de volgende mogelijkheden:
- [Topologie](../../network-watcher/view-network-topology.md): Biedt een weer gave op netwerk niveau waarin de verschillende interconnects en koppelingen tussen netwerk bronnen in een resource groep worden weer gegeven.

- [Vastleggen van variabele pakketten](../../network-watcher/network-watcher-packet-capture-overview.md): Hiermee worden pakket gegevens in en uit een virtuele machine vastgelegd. Geavanceerde filter opties en besturings elementen voor nauw keurigheid, zoals instellingen voor tijd en grootte-beperking, bieden veelzijdigheid. De pakket gegevens kunnen worden opgeslagen in een BLOB-archief of op de lokale schijf in de bestands indeling *. Cap* .

- [IP-stroom verificatie](../../network-watcher/network-watcher-ip-flow-verify-overview.md): Hiermee wordt gecontroleerd of een pakket wordt toegestaan of geweigerd op basis van de stroom informatie 5-tuple-pakket parameters (dat wil zeggen, doel-IP, bron-IP, doel poort, bron poort en Protocol). Als het pakket wordt geweigerd door een beveiligings groep, wordt de regel en groep die het pakket heeft geweigerd, geretourneerd.

- [Volgende hop](../../network-watcher/network-watcher-next-hop-overview.md): Bepaalt de volgende hop voor pakketten die worden gerouteerd in de Azure-netwerk infrastructuur, zodat u eventuele onjuist geconfigureerde, door de gebruiker gedefinieerde routes kunt vaststellen.

- [Weer gave van beveiligings groep](../../network-watcher/network-watcher-security-group-view-overview.md): Hiermee worden de effectief en toegepaste beveiligings regels opgehaald die worden toegepast op een virtuele machine.

- [Probleem oplossing voor virtuele netwerk gateway en verbinding](../../network-watcher/network-watcher-troubleshoot-manage-rest.md): Helpt u bij het oplossen van problemen met virtuele netwerk gateways en verbindingen.

- [Limieten voor netwerk abonnementen](../../network-watcher/network-watcher-monitoring-overview.md): Hiermee kunt u het gebruik van netwerk bronnen weer geven op basis van limieten.

### <a name="application-insights"></a>Application Insights

[Azure-toepassing Insights](../../azure-monitor/app/app-insights-overview.md) is een UITBREID bare apm-service voor webontwikkelaars op meerdere platforms. Gebruik het om live webtoepassingen te bewaken. Er worden automatisch prestatie afwijkingen gedetecteerd. De service bevat krachtige analysehulpmiddelen om u te helpen bij het vaststellen van problemen en te begrijpen wat gebruikers daadwerkelijk doen met uw app.

Application Insights is ontworpen om u te helpen de prestaties en bruikbaarheid continu te verbeteren.

Het werkt voor apps op een groot aantal verschillende platformen, waaronder .NET, node. js en Java EE, ongeacht of deze on-premises of in de cloud worden gehost. Het wordt geïntegreerd met uw DevOps-proces en heeft verbindings punten met verschillende ontwikkel hulpprogramma's.

![Application Insights diagram](./media/log-audit/azure-log-audit-fig6.png)

Application Insights is bedoeld voor het ontwikkelingsteam en helpt u om te begrijpen hoe de app presteert en op welke manier de app wordt gebruikt. Met deze service kunt u het volgende controleren:

* **Aanvraag tarieven, reactie tijden en fout percentages**: Ontdek welke pagina's het populairst zijn, op welk tijdstip van de dag en waar uw gebruikers zich bevinden. Ontdek welke pagina's het beste presteren. Als uw reactie tijden en fout tarieven hoog zijn wanneer er meer aanvragen zijn, hebt u mogelijk een probleem met het oplossen van problemen.

* **Afhankelijkheids tarieven, reactie tijden en fout percentages**: Ga na of externe services worden vertraagd.

* **Uitzonde ringen**: Analyseer de geaggregeerde statistieken of Kies specifieke instanties en zoom in op de stack tracering en gerelateerde aanvragen. Zowel server- als browseruitzonderingen worden gerapporteerd.

* **Pagina weergaven en de prestaties**van de belasting: Rapporten ophalen van de browsers van uw gebruikers.

* **Ajax**-aanroepen: De tarieven, reactie tijden en fout percentages van een webpagina ophalen.

* **Aantal gebruikers en sessies**.

* **Prestatie meter items**: Gegevens ophalen van uw Windows-of Linux-Server computers, zoals CPU, geheugen en netwerk gebruik.

* **Diagnostische gegevens van host**: Gegevens ophalen uit docker of Azure.

* **Traceer logboeken voor diagnostische gegevens**: Gegevens ophalen uit uw app, zodat u tracerings gebeurtenissen met aanvragen kunt correleren.

* **Aangepaste gebeurtenissen en metrische gegevens**: Gegevens ophalen die u zelf schrijft in de client-of server code, voor het bijhouden van zakelijke gebeurtenissen, zoals verkochte objecten of spellen die zijn gewonnen.

De volgende tabel bevat een overzicht en een beschrijving van integratie scenario's:

| Integratie scenario | Description |
| --------------------- | :---------- |
|[Toepassings overzicht](../../azure-monitor/app/app-map.md)|Bekijk de onderdelen van uw app met belangrijke metrische gegevens en waarschuwingen.|
|[Diagnostische gegevens voor het zoeken naar exemplaren](../../azure-monitor/app/diagnostic-search.md)| U kunt zoeken naar gebeurtenissen, zoals aanvragen, uitzonderingen, afhankelijkheidsaanroepen, logboektraceringen en paginaweergaven en deze gegevens ook filteren.|
|[Metrics Explorer voor geaggregeerde gegevens](../../azure-monitor/app/metrics-explorer.md)|Verken, filter en segmenteer cumulatieve gegevens, zoals aantallen aanvragen, fouten en uitzonderingen, reactietijden en paginalaadtijden.|
|[Dashboards](../../azure-monitor/app/overview-dashboard.md)|Combineer gegevens van meerdere resources tot een mash-up en deel deze met anderen. Ideaal voor toepassingen met meerdere onderdelen en om continu weer te geven in de teamkamer.|
|[Live Metrics Stream](../../azure-monitor/app/live-stream.md)|Wanneer u een nieuwe build implementeert, kunt u kijken naar deze 'near-realtime' prestatie-indicatoren om te controleren of dat alles naar verwachting werkt.|
|[Analytische gegevens](../../azure-monitor/app/analytics.md)|Beantwoord moeilijke vragen over de prestaties en het gebruik van uw app met behulp van deze krachtige querytaal.|
|[Automatische en hand matige waarschuwingen](../../azure-monitor/app/alerts.md)|Automatische waarschuwingen worden aangepast aan de normale patronen van de telemetrie van uw app en worden geactiveerd wanneer er iets buiten het gebruikelijke patroon is. U kunt ook waarschuwingen instellen voor bepaalde niveaus van aangepaste functies of standaardfuncties voor het verzamelen van metrische gegevens.|
|[Visual Studio](../../azure-monitor/app/visual-studio.md)|Prestatie gegevens weer geven in de code. Ga naar de code vanuit stack-traces.|
|[Power BI](../../azure-monitor/app/export-power-bi.md)|Integreer metrische gegevens over het gebruik van de toepassing met andere business intelligence.|
|[REST API](https://dev.applicationinsights.io/)|Schrijf code voor het uitvoeren van query's op uw functies voor het verzamelen van metrische gegevens en op onbewerkte gegevens.|
|[Continue export](../../azure-monitor/app/export-telemetry.md)|Bulksgewijs exporteren van onbewerkte gegevens naar opslag wanneer deze arriveert.|

### <a name="azure-security-center-alerts"></a>Azure Security Center waarschuwingen

Azure Security Center detectie van bedreigingen werkt door automatisch beveiligings gegevens van uw Azure-resources, het netwerk en verbonden partner oplossingen te verzamelen. Deze informatie wordt door Security Center geanalyseerd, waarbij vaak informatie uit meerdere bronnen wordt samengebracht om bedreigingen te analyseren. Beveiligingswaarschuwingen krijgen in Security Center een prioriteit, evenals aanbevelingen voor het oplossen van de bedreiging. Zie [Azure Security Center](../../security-center/security-center-intro.md)voor meer informatie.

![Azure Security Center diagram](./media/log-audit/azure-log-audit-fig7.png)

Security Center maakt gebruik van geavanceerde beveiligingsanalyses die veel verder gaan dan op handtekeningen gebaseerde benaderingen. Dit past door braak in grote gegevens en [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technologieën om gebeurtenissen in de gehele Cloud infrastructuur te evalueren. Op deze manier detecteert de IT bedreigingen die onmogelijk zouden kunnen worden geïdentificeerd door hand matige benaderingen te gebruiken en de ontwikkeling van aanvallen te voors pellen. Deze beveiligingsanalyses omvatten:

* **Geïntegreerde bedreigings informatie**: Zoekt naar bekende beschadigde actors door wereld wijde bedreigings informatie van micro soft-producten en-services toe te passen, de micro soft Digital misdrijven-eenheid (DCU), micro soft Security Response Center (MSRC) en externe feeds.

* **Gedrags analyse**: Past bekende patronen toe om schadelijk gedrag te ontdekken.

* **Afwijkings detectie**: Gebruikt statistische profilering om een historische basis lijn te maken. Er wordt een waarschuwing gegeven bij afwijkingen van vastgestelde basislijnen die aan een mogelijke aanvalsvector voldoen.

Veel beveiligings bewerkingen en reactie teams op incidenten vertrouwen op een SIEM-oplossing als uitgangs punt voor het uitsorteren en onderzoeken van beveiligings waarschuwingen. Met Azure Log Integration kunt u Security Center waarschuwingen en beveiligings gebeurtenissen voor virtuele machines die zijn verzameld door Azure Diagnostics en audit logs, synchroniseren met uw Azure Monitor Logboeken of SIEM-oplossing in bijna realtime.

## <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

Azure Monitor-Logboeken is een service in azure waarmee u gegevens kunt verzamelen en analyseren die worden gegenereerd door resources in uw Cloud-en on-premises omgevingen. Het biedt u real-time inzichten met behulp van geïntegreerde Zoek-en aangepaste Dash boards waarmee u miljoenen records in al uw workloads en servers eenvoudig kunt analyseren, ongeacht hun fysieke locatie.

![Diagram van Azure Monitor logboeken](./media/log-audit/azure-log-audit-fig8.png)

In het midden van Azure Monitor logboeken bevindt zich de Log Analytics-werk ruimte, die wordt gehost in Azure. Met Azure Monitor logboeken worden gegevens in de werk ruimte verzameld van verbonden bronnen door gegevens bronnen te configureren en oplossingen toe te voegen aan uw abonnement. Gegevens bronnen en oplossingen maken elk verschillende record typen, elk met een eigen set eigenschappen. Maar bronnen en oplossingen kunnen nog steeds samen worden geanalyseerd in query's naar de werk ruimte. Met deze mogelijkheid kunt u dezelfde hulpprogram ma's en methoden gebruiken om te werken met diverse gegevens die worden verzameld door verschillende bronnen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Verbonden bronnen zijn de computers en andere resources die de gegevens genereren die worden verzameld door Azure Monitor Logboeken. Bronnen kunnen agents zijn die zijn geïnstalleerd op [Windows](../../log-analytics/log-analytics-agent-windows.md) -en [Linux](../../log-analytics/log-analytics-quick-collect-linux-computer.md) -computers die rechtstreeks verbinding maken, of agents in [een verbonden System Center Operations Manager-beheer groep](../../azure-monitor/platform/om-agents.md). Azure Monitor logboeken kunnen ook gegevens verzamelen van een [Azure-opslag account](../../azure-monitor/platform/resource-logs-collect-storage.md).

[Gegevens bronnen](../../azure-monitor/platform/agent-data-sources.md) zijn de verschillende soorten gegevens die van elke verbonden bron worden verzameld. Bronnen bevatten gebeurtenissen en [prestatie gegevens](../../azure-monitor/platform/data-sources-performance-counters.md) van [Windows](../../azure-monitor/platform/data-sources-windows-events.md) -en Linux-agents, naast bronnen zoals [IIS](../../azure-monitor/platform/data-sources-iis-logs.md) -logboeken en [aangepaste tekst logboeken](../../azure-monitor/platform/data-sources-custom-logs.md). U configureert elke gegevensbron die u wenst te verzamelen en de configuratie wordt automatisch doorgegeven aan elke verbonden bron.

Er zijn vier manieren voor het [verzamelen van Logboeken en metrische gegevens voor Azure-Services](../../azure-monitor/platform/resource-logs-collect-storage.md):

* Azure Diagnostics direct naar Azure Monitor-Logboeken (**Diagnostische gegevens** in de volgende tabel)

* Azure Diagnostics Azure-opslag naar Azure Monitor-Logboeken (**opslag** in de volgende tabel)

* Connectors voor Azure-Services (**connector** in de volgende tabel)

* Scripts voor het verzamelen en vervolgens plaatsen van gegevens in Azure Monitor Logboeken (lege cellen in de volgende tabel en voor services die niet worden vermeld)

| Service | Resourcetype | Logboeken | Metrische gegevens | Oplossing |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  Diagnostiek|Diagnostiek|    [Azure-toepassing](../../azure-monitor/insights/azure-networking-analytics.md) [Gateway-analyse](../../azure-monitor/insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor)|
|Application Insights||     Connector|  Connector|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [Connector (preview-versie)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Azure Automation accounts| Microsoft.Automation/<br>AutomationAccounts|    Diagnostiek||       [Meer informatie](../../automation/automation-manage-send-joblogs-log-analytics.md)|
|Azure Batch accounts|  Microsoft.Batch/<br>batchAccounts|  Diagnostiek|    Diagnostiek||
|Klassieke cloudservices||       Storage||       [Meer informatie](../../azure-monitor/platform/azure-storage-iis-table.md)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>accounts|       Diagnostiek|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>accounts|   Diagnostiek|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>accounts|   Diagnostiek|||
|Azure Event hub-naam ruimte| Microsoft.EventHub/<br>naamruimten|  Diagnostiek|    Diagnostiek||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     Diagnostiek||
|Azure Key Vault|   Microsoft.KeyVault/<br>kluizen|  Diagnostiek  || [Key Vault-analyse](../../azure-monitor/insights/azure-key-vault.md)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    Diagnostiek|||
|Azure Logic Apps|  Microsoft.Logic/<br>stroom|  Diagnostiek|    Diagnostiek||
||Microsoft.Logic/<br>integrationAccounts||||
|Netwerkbeveiligingsgroepen|   Microsoft.Network/<br>networksecuritygroups|Diagnostiek||   [Analyse van Azure-netwerk beveiligings groep](../../azure-monitor/insights/azure-networking-analytics.md#azure-application-gateway-and-network-security-group-analytics)|
|Recovery-kluizen|   Microsoft.RecoveryServices/<br>kluizen|||[Azure Recovery Services-analyse (Preview)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Services zoeken|   Microsoft.Search/<br>searchServices|    Diagnostiek|    Diagnostiek||
|Service Bus-naamruimte| Microsoft.ServiceBus/<br>naamruimten|    Diagnostiek|Diagnostiek|    [Service Bus-analyse (Preview)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Storage||    [Service Fabric-analyse (Preview)](../../service-fabric/service-fabric-diagnostics-oms-setup.md)|
|SQL (v12)| Microsoft.Sql/<br>Server<br>databases||       Diagnostiek||
||Microsoft.Sql/<br>Server<br>elasticPools||||
|Storage|||         Script| [Azure Storage Analytics (Preview)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Azure Virtual Machines|    Microsoft.Compute/<br>Informatie|  Toestelnummer|  Toestelnummer||
||||Diagnostiek||
|Virtual Machine Scale Sets|    Microsoft.Compute/<br>Informatie    ||Diagnostiek||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>Informatie||||
|Webserver farms|Microsoft.Web/<br>server farms||   Diagnostiek
|Websites|  Microsoft.Web/<br>sites ||      Diagnostiek|    [Meer informatie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites/<br>sleuf||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Integratie met on-premises SIEM-systemen vastleggen

Met Azure Log Integration kunt u onbewerkte logboeken integreren met uw Azure-resources met uw on-premises SIEM systeem (beveiligings informatie en Event Management System). AzLog-down loads zijn op 27 juni 2018 uitgeschakeld. Raadpleeg voor meer informatie over wat u verder kunt doen met [Azure monitor om te integreren met Siem-hulpprogram ma's](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

![Diagram voor logboek integratie](./media/log-audit/azure-log-audit-fig9.png)

Met logboek integratie worden Azure Diagnostics verzameld van uw virtuele Windows-machines, Azure-activiteiten logboeken, Azure Security Center waarschuwingen en logboeken van de Azure-resource provider. Deze integratie biedt een gecombineerd dash board voor al uw assets, ongeacht of deze zich on-premises of in de cloud bevinden, zodat u beveiligings gebeurtenissen kunt samen voegen, correleren, analyseren en waarschuwen.

Logboek integratie biedt momenteel ondersteuning voor de integratie van Azure-activiteiten logboeken, Windows-gebeurtenis logboeken van virtuele Windows-machines met uw Azure-abonnement, Azure Security Center waarschuwingen, Azure Diagnostics-logboeken en Azure AD-audit Logboeken.

| Logboektype | Azure Monitor logboeken die JSON ondersteunen (Splunk, ArcSight en IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Audit logboeken van Azure AD|   Ja|
|Activiteitenlogboeken| Ja|
|Security Center waarschuwingen |Ja|
|Diagnostische logboeken (resource Logboeken)|  Ja|
|VM-logboeken|   Ja, via doorgestuurde gebeurtenissen en niet via JSON|

[Aan de slag met Azure log Integration](azure-log-integration-get-started.md): In deze zelf studie wordt uitgelegd hoe u Azure Log Integration installeert en de logboeken integreert vanuit Azure Storage, Azure-activiteiten logboeken, Azure Security Center waarschuwingen en Azure AD-audit Logboeken.

Integratie scenario's voor SIEM:

* [Stappen](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/)voor de configuratie van de partner: In dit blog bericht ziet u hoe u Azure Log Integration kunt configureren voor gebruik met partner oplossingen Splunk, HP ArcSight en IBM QRadar.

* [Azure log Integration Veelgestelde vragen](azure-log-integration-faq.md): In dit artikel vindt u antwoorden op vragen over Azure Log Integration.

* [Security Center-waarschuwingen integreren met Azure log Integration](../../security-center/security-center-export-data-to-siem.md): In dit artikel wordt beschreven hoe u Security Center-waarschuwingen, beveiligings gebeurtenissen voor virtuele machines die worden verzameld door Azure Diagnostics-logboeken en Azure-controle Logboeken kunt synchroniseren met uw Azure Monitor-Logboeken of SIEM-oplossing.

## <a name="next-steps"></a>Volgende stappen

- [Controle en logboek registratie](management-monitoring-overview.md): Beveilig gegevens door zicht baarheid te hand haven en snel te reageren op tijdige beveiligings waarschuwingen.

- [Beveiligings logboeken en audit-logboek verzameling in azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): U kunt deze instellingen afdwingen om ervoor te zorgen dat uw Azure-instanties de juiste beveiligings-en audit logboeken verzamelen.

- [Controle-instellingen voor een site verzameling configureren](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Als u beheerder van een site verzameling bent, haalt u de geschiedenis op van de acties van afzonderlijke gebruikers en de geschiedenis van de acties die zijn uitgevoerd tijdens een bepaald datum bereik. 

- [Het controle logboek zoeken in het Office 365 beveiligings-en compliancecentrum](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Gebruik Office 365 Beveiligings-en compliancecentrum om in het uniforme controle logboek te zoeken en de activiteiten van gebruikers en beheerders in uw Office 365-organisatie weer te geven.


