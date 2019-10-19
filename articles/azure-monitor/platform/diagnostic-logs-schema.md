---
title: Door Azure Diagnostische logboeken ondersteunde services en schema's
description: Meer informatie over de ondersteunde services en het gebeurtenis schema voor Azure Diagnostic-Logboeken.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/11/2018
ms.openlocfilehash: f2d71972fa8acc930800a70193f688246ee7415c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555573"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Ondersteunde services, schema's en categorieën voor Azure Diagnostische logboeken

[Azure monitor Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md) worden logboeken gegenereerd door Azure-Services waarmee de werking van deze services of bronnen wordt beschreven. Alle Diagnostische logboeken die beschikbaar zijn via Azure Monitor, delen een gemeen schappelijk schema op het hoogste niveau, met flexibiliteit voor elke service om unieke eigenschappen voor hun eigen gebeurtenissen te verzenden.

Een combi natie van het resource type (beschikbaar in de eigenschap `resourceId`) en de `category` unieke identificatie van een schema. In dit artikel wordt het schema op het hoogste niveau voor Diagnostische logboeken en koppelingen naar de schema's voor elke service beschreven.

## <a name="top-level-diagnostic-logs-schema"></a>Schema voor Diagnostische logboeken op het hoogste niveau

| Naam | Vereist/optioneel | Beschrijving |
|---|---|---|
| tijd | Verplicht | De tijds tempel (UTC) van de gebeurtenis. |
| resourceId | Verplicht | De resource-ID van de resource die de gebeurtenis heeft verzonden. Voor Tenant Services is dit de vorm/tenants/Tenant-id/providers/provider-name. |
| tenantId | Vereist voor Tenant logboeken | De Tenant-ID van de Active Directory-Tenant waaraan deze gebeurtenis is gekoppeld. Deze eigenschap wordt alleen gebruikt voor logboeken op Tenant niveau, maar wordt niet weer gegeven in Logboeken op resource niveau. |
| operationName | Verplicht | De naam van de bewerking die door deze gebeurtenis wordt vertegenwoordigd. Als de gebeurtenis een RBAC-bewerking vertegenwoordigt, is dit de naam van de RBAC-bewerking (bijvoorbeeld Micro soft. Storage/Storage accounts/blobServices/blobs/lezen). Normaal gesp roken gemodelleerd in de vorm van een resource manager-bewerking, zelfs als ze niet de werkelijke gedocumenteerde Resource Manager-bewerkingen (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Optioneel | De API-versie die aan de bewerking is gekoppeld als de bewerking is uitgevoerd met een API (bijvoorbeeld `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Als er geen API is die overeenkomt met deze bewerking, vertegenwoordigt de versie de versie van die bewerking in het geval dat de eigenschappen die aan de bewerking zijn gekoppeld in de toekomst worden gewijzigd. |
| category | Verplicht | De logboek categorie van de gebeurtenis. Categorie is de granulariteit waarmee u Logboeken voor een bepaalde resource kunt in-of uitschakelen. De eigenschappen die worden weer gegeven in de BLOB eigenschappen van een gebeurtenis zijn hetzelfde binnen een bepaalde logboek categorie en hetzelfde resource type. Veelvoorkomende logboek categorieën zijn ' Auditing ' ' Execution ' en ' request '. |
| resultType | Optioneel | De status van de gebeurtenis. Typische waarden zijn gestart, in uitvoering, geslaagd, mislukt, actief en opgelost. |
| resultSignature | Optioneel | De substatus van de gebeurtenis. Als deze bewerking overeenkomt met een REST API-aanroep, is dit de HTTP-status code van de bijbehorende REST-aanroep. |
| resultDescription | Optioneel | De statische tekst beschrijving van deze bewerking, bijvoorbeeld. Opslag bestand ophalen. |
| durationMs | Optioneel | De duur van de bewerking in milliseconden. |
| callerIpAddress | Optioneel | Het IP-adres van de beller, als de bewerking overeenkomt met een API-aanroep die afkomstig zou zijn van een entiteit met een openbaar beschikbaar IP-adres. |
| correlationId | Optioneel | Een GUID die wordt gebruikt om een set gerelateerde gebeurtenissen samen te voegen. Normaal gesp roken, als twee gebeurtenissen dezelfde operationname hebben, maar twee verschillende statussen hebben (bijvoorbeeld ' Started ' en ' geslaagd '), delen ze dezelfde correlatie-ID. Dit kan ook andere relaties tussen gebeurtenissen vertegenwoordigen. |
| identiteit | Optioneel | Een JSON-BLOB waarmee de identiteit wordt beschreven van de gebruiker of toepassing die de bewerking heeft uitgevoerd. Dit omvat meestal de autorisatie en claims/JWT-token van Active Directory. |
| Niveau | Optioneel | Het Ernst niveau van de gebeurtenis. Moet een van de volgende informatie hebben: waarschuwing, fout of kritiek. |
| location | Optioneel | De regio van de resource waarmee de gebeurtenis wordt verzonden, bijvoorbeeld. "VS-Oost" of "Frankrijk-zuid" |
| properties | Optioneel | Alle uitgebreide eigenschappen die betrekking hebben op deze specifieke gebeurtenis categorie. Alle aangepaste/unieke eigenschappen moeten in dit deel B van het schema worden geplaatst. |

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>Servicespecifieke schema's voor Diagnostische logboeken voor bronnen
Het schema voor de diagnostische logboeken voor bronnen varieert afhankelijk van de categorie resource en logboek. Deze lijst bevat alle services die beschik bare Diagnostische logboeken en koppelingen naar het service-en categorie-specifieke schema maken, indien beschikbaar.

| Service | & Documenten voor schema |
| --- | --- |
| Azure Active Directory | [Overzicht](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), schema voor [audit logboeken](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) en [aanmeldingen](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [Diagnostische logboeken API Management](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Toepassingsgateways |[Diagnostische logboek registratie voor Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics voor Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Diagnostische logboek registratie Azure Batch](../../batch/batch-diagnostics.md) |
| Azure Database voor MySQL | [Diagnostische logboeken Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database voor PostgreSQL | [Diagnostische logboeken Azure Database for PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure Data Explorer | [Diagnostische logboeken van Azure Data Explorer](../../data-explorer/using-diagnostic-logs.md) |
| Cognitive Services | [Diagnostische logboek registratie voor Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| CDN (Content Delivery Network) | [Diagnostische logboeken van Azure voor CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB logboek registratie](../../cosmos-db/logging.md) |
| Data Factory | [Gegevens fabrieken bewaken met behulp van Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Diagnostische logboeken openen voor Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Diagnostische logboeken openen voor Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Diagnostische logboeken van Azure Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Het schema is niet beschikbaar. |
| Azure Firewall | Het schema is niet beschikbaar. |
| IoT Hub | [IoT Hub bewerkingen](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Logboekregistratie van Azure Key Vault](../../key-vault/key-vault-logging.md) |
| Kubernetes Service |[Logboek registratie voor Azure Kubernetes](../../aks/view-master-logs.md#log-event-schema) |
| Load balancer |[Logboekanalyse voor Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logische apps |[Aangepast Logic Apps B2B-volgschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Netwerkbeveiligingsgroepen |[Logboekanalyses voor netwerkbeveiligingsgroepen (NSG's)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS Protection | [Azure DDoS Protection Standard beheren](../../virtual-network/manage-ddos-protection.md) |
| Power BI Dedicated | [Diagnostische logboek registratie voor Power BI Embedded in azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Gegevens model voor Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Search |[Zoek Traffic Analytics inschakelen en gebruiken](../../search/search-traffic-analytics.md) |
| Service Bus |[Diagnostische logboeken Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Diagnostische logboek registratie Azure SQL Database](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Diagnostische logboeken voor taken](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager-logboek schema](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuele netwerken | Het schema is niet beschikbaar. |
| Virtuele-netwerkgateways | Het schema is niet beschikbaar. |

## <a name="supported-log-categories-per-resource-type"></a>Ondersteunde logboek categorieën per resource type
|Resourcetype|Category|Weergave naam categorie|
|---|---|---|
|Micro soft. AnalysisServices/servers|Mechanisme|Mechanisme|
|Micro soft. AnalysisServices/servers|Service|Service|
|Microsoft.ApiManagement/service|Gateway logs|Logboeken gerelateerd aan de ApiManagement-gateway|
|Micro soft. Automation/automationAccounts|JobLogs|Taak logboeken|
|Micro soft. Automation/automationAccounts|JobStreams|Taak stromen|
|Micro soft. Automation/automationAccounts|DscNodeStatus|DSC-knooppunt status|
|Micro soft. batch/batchAccounts|ServiceLog|Service logboeken|
|Micro soft. CDN/profielen/eind punten|CoreAnalytics|Hiermee worden de metrische gegevens van het eind punt opgehaald, bijvoorbeeld band breedte, uitgaand verkeer enzovoort.|
|Micro soft. ClassicNetwork/networksecuritygroups|Stroom gebeurtenis van regel voor netwerk beveiligings groep|Stroom gebeurtenis van regel voor netwerk beveiligings groep|
|Micro soft. CognitiveServices/accounts|Controleren|Auditlogboeken|
|Micro soft. CognitiveServices/accounts|RequestResponse|Aanvraag-en antwoord logboeken|
|Micro soft. container service/managedClusters|uitvoeren-apiserver|Kubernetes-API-server|
|Micro soft. container service/managedClusters|uitvoeren-Controller-Manager|Kubernetes-controller beheer|
|Micro soft. container service/managedClusters|cluster-automatisch schalen|Kubernetes-cluster automatisch schalen|
|Micro soft. container service/managedClusters|uitvoeren-scheduler|Kubernetes scheduler|
|Micro soft. container service/managedClusters|komen|Verificatie-webhook|
|Micro soft. CustomerInsights/hubs|AuditEvents|AuditEvents|
|Micro soft. DataFactory/fabrieken|ActivityRuns|Logboek voor uitvoering van pijplijn activiteit|
|Micro soft. DataFactory/fabrieken|PipelineRuns|Logboek voor uitvoering van pijp lijn|
|Micro soft. DataFactory/fabrieken|TriggerRuns|Trigger uitvoeringen logboek|
|Micro soft. DataLakeAnalytics/accounts|Controleren|Auditlogboeken|
|Micro soft. DataLakeAnalytics/accounts|Aanvragen|Logboeken aanvragen|
|Micro soft. data Lake Store/accounts|Controleren|Auditlogboeken|
|Micro soft. data Lake Store/accounts|Aanvragen|Logboeken aanvragen|
|Micro soft. DBforMySQL/servers|MySqlSlowLogs|MySQL-server logboeken|
|Micro soft. DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL-server logboeken|
|Micro soft. devices/IotHubs|Verbindingen|Verbindingen|
|Micro soft. devices/IotHubs|DeviceTelemetry|Apparaattelemetrie|
|Micro soft. devices/IotHubs|C2DCommands|C2D-opdrachten|
|Micro soft. devices/IotHubs|DeviceIdentityOperations|Bewerkingen voor apparaat-Id's|
|Micro soft. devices/IotHubs|FileUploadOperations|Bewerkingen voor het uploaden van bestanden|
|Micro soft. devices/IotHubs|Routes|Routes|
|Micro soft. devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Micro soft. devices/IotHubs|C2DTwinOperations|Dubbele C2D-bewerkingen|
|Micro soft. devices/IotHubs|TwinQueries|Dubbele Query's|
|Micro soft. devices/IotHubs|JobsOperations|Taak bewerkingen|
|Micro soft. devices/IotHubs|DirectMethods|Directe methoden|
|Micro soft. devices/IotHubs|E2EDiagnostics|E2E diagnostische gegevens (preview-versie)|
|Micro soft. devices/IotHubs|Configuraties|Configuraties|
|Micro soft. devices/provisioningServices|DeviceOperations|Bewerkingen voor apparaten|
|Micro soft. devices/provisioningServices|ServiceOperations|Service bewerkingen|
|Micro soft. DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Micro soft. DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Micro soft. DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Micro soft. EventHub/naam ruimten|ArchiveLogs|Archief logboeken|
|Micro soft. EventHub/naam ruimten|OperationalLogs|Operationele logboeken|
|Micro soft. EventHub/naam ruimten|AutoScaleLogs|Logboeken automatisch schalen|
|Micro soft. Insights/AutoscaleSettings|AutoscaleEvaluations|Evaluaties automatisch schalen|
|Micro soft. Insights/AutoscaleSettings|AutoscaleScaleActions|Schaal acties automatisch schalen|
|Micro soft. IoTSpaces/Graph|Tracering|Tracering|
|Micro soft. IoTSpaces/Graph|Functioneren|Functioneren|
|Micro soft. IoTSpaces/Graph|Controleren|Controleren|
|Micro soft. IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Micro soft. IoTSpaces/Graph|Binnenkomend|Binnenkomend|
|Micro soft. IoTSpaces/Graph|Uitgaand verkeer|Uitgaand verkeer|
|Micro soft.-sleutel kluis/-kluizen|Audit event|Auditlogboeken|
|Micro soft. Logic/werk stromen|WorkflowRuntime|Diagnostische gebeurtenissen voor workflowruntime|
|Micro soft. Logic/integrationAccounts|IntegrationAccountTrackingEvents|Spoor gebeurtenissen voor integratie account|
|Micro soft. Network/networksecuritygroups|NetworkSecurityGroupEvent|Gebeurtenis netwerk beveiligings groep|
|Micro soft. Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Teller van regel voor netwerk beveiligings groep|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Load Balancer waarschuwings gebeurtenissen|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Load Balancer test status|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS-beveiligings meldingen|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Stroom logboeken van DDoS-oplossings beslissingen|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Rapporten met DDoS-oplossingen|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Waarschuwingen voor VM-beveiliging|
|Micro soft. Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway Access-logboek|
|Micro soft. Network/applicationGateways|ApplicationGatewayPerformanceLog|Prestatie logboek Application Gateway|
|Micro soft. Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway firewall-logboek|
|Micro soft. Network/securegateways|AzureFirewallApplicationRule|Toepassings regel Azure Firewall|
|Micro soft. Network/securegateways|AzureFirewallNetworkRule|Azure Firewall netwerk regel|
|Micro soft. Network/azurefirewalls|AzureFirewallApplicationRule|Toepassings regel Azure Firewall|
|Micro soft. Network/azurefirewalls|AzureFirewallNetworkRule|Azure Firewall netwerk regel|
|Micro soft. Network/virtualNetworkGateways|GatewayDiagnosticLog|Diagnostische logboeken van de gateway|
|Micro soft. Network/virtualNetworkGateways|TunnelDiagnosticLog|Diagnostische logboeken voor tunnel|
|Micro soft. Network/virtualNetworkGateways|RouteDiagnosticLog|Diagnostische logboeken voor route ring|
|Micro soft. Network/virtualNetworkGateways|IKEDiagnosticLog|Diagnostische logboeken voor IKE|
|Micro soft. Network/virtualNetworkGateways|P2SDiagnosticLog|Diagnostische logboeken van P2S|
|Micro soft. Network/trafficManagerProfiles|ProbeHealthStatusEvents|Gebeurtenis met resultaten van Traffic Manager test status|
|Micro soft. Network/expressRouteCircuits|PeeringRouteLog|Logboeken voor peering route tabel|
|Micro soft. Network/frontdoors|FrontdoorAccessLog|-Ingang-toegangs logboek|
|Micro soft. Network/frontdoors|FrontdoorWebApplicationFirewallLog|-Ingang Web Application firewall-logboek|
|Micro soft. PowerBIDedicated/capaciteiten|Mechanisme|Mechanisme|
|Micro soft. Recovery Services/kluizen|AzureBackupReport|Azure Backup rapportage gegevens|
|Micro soft. Recovery Services/kluizen|AzureSiteRecoveryJobs|Azure Site Recovery taken|
|Micro soft. Recovery Services/kluizen|AzureSiteRecoveryEvents|Azure Site Recovery gebeurtenissen|
|Micro soft. Recovery Services/kluizen|AzureSiteRecoveryReplicatedItems|Gerepliceerde items Azure Site Recovery|
|Micro soft. Recovery Services/kluizen|AzureSiteRecoveryReplicationStats|Replicatie statistieken Azure Site Recovery|
|Micro soft. Recovery Services/kluizen|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery herstel punten|
|Micro soft. Recovery Services/kluizen|AzureSiteRecoveryReplicationDataUploadRate|Upload frequentie van Azure Site Recovery replicatie gegevens|
|Micro soft. Recovery Services/kluizen|AzureSiteRecoveryProtectedDiskDataChurn|Gegevens verloop van beveiligde schijf Azure Site Recovery|
|Micro soft. Search/searchServices|OperationLogs|Bewerkings logboeken|
|Micro soft. ServiceBus/naam ruimten|OperationalLogs|Operationele logboeken|
|Micro soft. SQL/servers/data bases|SQLInsights|SQL Insights|
|Micro soft. SQL/servers/data bases|AutomaticTuning|Automatisch instellen|
|Micro soft. SQL/servers/data bases|QueryStoreRuntimeStatistics|Runtime statistieken voor query Store|
|Micro soft. SQL/servers/data bases|QueryStoreWaitStatistics|Wacht statistieken voor query Store|
|Micro soft. SQL/servers/data bases|Fouten|Fouten|
|Micro soft. SQL/servers/data bases|DatabaseWaitStatistics|Data base-wacht statistieken|
|Micro soft. SQL/servers/data bases|Time-outs|Time-outs|
|Micro soft. SQL/servers/data bases|Block|Block|
|Micro soft. SQL/servers/data bases|Impassen|Impassen|
|Micro soft. SQL/servers/data bases|Controleren|Auditlogboeken|
|Micro soft. SQL/servers/data bases|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|
|Micro soft. SQL/servers/data bases|DmsWorkers|DMS-werk rollen|
|Micro soft. SQL/servers/data bases|ExecRequests|Exec-aanvragen|
|Micro soft. SQL/servers/data bases|RequestSteps|Stappen voor aanvragen|
|Micro soft. SQL/servers/data bases|SqlRequests|SQL-aanvragen|
|Micro soft. SQL/servers/data bases|Wacht|Wacht|
|Micro soft. SQL/managedInstances|ResourceUsageStats|Resource gebruiks statistieken|
|Micro soft. SQL/managedInstances|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|
|Micro soft. SQL/managedInstances/data bases|SQLInsights|SQL Insights|
|Micro soft. SQL/managedInstances/data bases|QueryStoreRuntimeStatistics|Runtime statistieken voor query Store|
|Micro soft. SQL/managedInstances/data bases|QueryStoreWaitStatistics|Wacht statistieken voor query Store|
|Micro soft. SQL/managedInstances/data bases|Fouten|Fouten|
|Micro soft. StreamAnalytics/streamingjobs|Uitvoering|Uitvoering|
|Micro soft. StreamAnalytics/streamingjobs|Ontwerpen|Ontwerpen|
|micro soft. web/sites|FunctionExecutionLogs|Uitvoer logboeken voor functies|
|micro soft. web/sites/sleuven|FunctionExecutionLogs|Uitvoer logboeken voor functies|

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md)
* [Diagnostische logboeken voor bronnen streamen naar **Event hubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Diagnostische instellingen voor bronnen wijzigen met behulp van de Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Logboeken analyseren vanuit Azure Storage met Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
