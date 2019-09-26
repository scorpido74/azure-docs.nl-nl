---
title: Door Azure Diagnostische logboeken ondersteunde services en schema's
description: Meer informatie over de ondersteunde services en het gebeurtenis schema voor Azure Diagnostic-Logboeken.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/11/2018
ms.author: robb
ms.subservice: logs
ms.openlocfilehash: fdcfcbaf99d48a345d2be4da297be1c9139da15c
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71308117"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Ondersteunde services, schema's en categorieën voor Azure Diagnostische logboeken

[Azure monitor Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md) worden logboeken gegenereerd door Azure-Services waarmee de werking van deze services of bronnen wordt beschreven. Alle Diagnostische logboeken die beschikbaar zijn via Azure Monitor, delen een gemeen schappelijk schema op het hoogste niveau, met flexibiliteit voor elke service om unieke eigenschappen voor hun eigen gebeurtenissen te verzenden.

Een combi natie van het resource type (beschikbaar in `resourceId` de eigenschap) en `category` de unieke identificatie van een schema. In dit artikel wordt het schema op het hoogste niveau voor Diagnostische logboeken en koppelingen naar de schema's voor elke service beschreven.

## <a name="top-level-diagnostic-logs-schema"></a>Schema voor Diagnostische logboeken op het hoogste niveau

| Name | Vereist/optioneel | Description |
|---|---|---|
| time | Vereist | De tijds tempel (UTC) van de gebeurtenis. |
| resourceId | Vereist | De resource-ID van de resource die de gebeurtenis heeft verzonden. Voor Tenant Services is dit de vorm/tenants/Tenant-id/providers/provider-name. |
| TenantId | Vereist voor Tenant logboeken | De Tenant-ID van de Active Directory-Tenant waaraan deze gebeurtenis is gekoppeld. Deze eigenschap wordt alleen gebruikt voor logboeken op Tenant niveau, maar wordt niet weer gegeven in Logboeken op resource niveau. |
| operationName | Vereist | De naam van de bewerking die door deze gebeurtenis wordt vertegenwoordigd. Als de gebeurtenis een RBAC-bewerking vertegenwoordigt, is dit de naam van de RBAC-bewerking (bijvoorbeeld Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Normaal gesp roken gemodelleerd in de vorm van een resource manager-bewerking, zelfs als ze niet de werkelijke gedocumenteerde Resource Manager-bewerkingen (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Optioneel | De API-versie die aan de bewerking is gekoppeld als de bewerking is uitgevoerd met een API (bijvoorbeeld `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Als er geen API is die overeenkomt met deze bewerking, vertegenwoordigt de versie de versie van die bewerking in het geval dat de eigenschappen die aan de bewerking zijn gekoppeld in de toekomst worden gewijzigd. |
| category | Vereist | De logboek categorie van de gebeurtenis. Categorie is de granulariteit waarmee u Logboeken voor een bepaalde resource kunt in-of uitschakelen. De eigenschappen die worden weer gegeven in de BLOB eigenschappen van een gebeurtenis zijn hetzelfde binnen een bepaalde logboek categorie en hetzelfde resource type. Veelvoorkomende logboek categorieën zijn ' Auditing ' ' Execution ' en ' request '. |
| resultType | Optioneel | De status van de gebeurtenis. Typische waarden zijn gestart, in uitvoering, geslaagd, mislukt, actief en opgelost. |
| resultSignature | Optioneel | De substatus van de gebeurtenis. Als deze bewerking overeenkomt met een REST API-aanroep, is dit de HTTP-status code van de bijbehorende REST-aanroep. |
| resultDescription | Optioneel | De statische tekst beschrijving van deze bewerking, bijvoorbeeld. Opslag bestand ophalen. |
| durationMs | Optioneel | De duur van de bewerking in milliseconden. |
| callerIpAddress | Optioneel | Het IP-adres van de beller, als de bewerking overeenkomt met een API-aanroep die afkomstig zou zijn van een entiteit met een openbaar beschikbaar IP-adres. |
| correlationId | Optioneel | Een GUID die wordt gebruikt om een set gerelateerde gebeurtenissen samen te voegen. Normaal gesp roken, als twee gebeurtenissen dezelfde operationname hebben, maar twee verschillende statussen hebben (bijvoorbeeld ' Started ' en ' geslaagd '), delen ze dezelfde correlatie-ID. Dit kan ook andere relaties tussen gebeurtenissen vertegenwoordigen. |
| identity | Optioneel | Een JSON-BLOB waarmee de identiteit wordt beschreven van de gebruiker of toepassing die de bewerking heeft uitgevoerd. Dit omvat meestal de autorisatie en claims/JWT-token van Active Directory. |
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
| Azure Database for MySQL | [Diagnostische logboeken Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Diagnostische logboeken Azure Database for PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
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
| Netwerktaakverdeler |[Logboekanalyse voor Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Aangepast Logic Apps B2B-volgschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
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
| Virtuele netwerkgateways | Het schema is niet beschikbaar. |

## <a name="supported-log-categories-per-resource-type"></a>Ondersteunde logboek categorieën per resource type
|Resourcetype|Category|Weergave naam categorie|
|---|---|---|
|Microsoft.AnalysisServices/servers|Engine|Engine|
|Microsoft.AnalysisServices/servers|Service|Service|
|Microsoft.ApiManagement/service|GatewayLogs|Logboeken gerelateerd aan de ApiManagement-gateway|
|Microsoft.Automation/automationAccounts|JobLogs|Taak logboeken|
|Microsoft.Automation/automationAccounts|JobStreams|Taak stromen|
|Microsoft.Automation/automationAccounts|DscNodeStatus|DSC-knooppunt status|
|Microsoft.Batch/batchAccounts|ServiceLog|Service logboeken|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Hiermee worden de metrische gegevens van het eind punt opgehaald, bijvoorbeeld band breedte, uitgaand verkeer enzovoort.|
|Microsoft.ClassicNetwork/networksecuritygroups|Stroom gebeurtenis van regel voor netwerk beveiligings groep|Stroom gebeurtenis van regel voor netwerk beveiligings groep|
|Microsoft.CognitiveServices/accounts|Controleren|Auditlogboeken|
|Microsoft.CognitiveServices/accounts|RequestResponse|Aanvraag-en antwoord logboeken|
|Microsoft.ContainerService/managedClusters|uitvoeren-apiserver|Kubernetes API-server|
|Microsoft.ContainerService/managedClusters|uitvoeren-Controller-Manager|Kubernetes Controller Manager|
|Microsoft.ContainerService/managedClusters|cluster-automatisch schalen|Automatische schaalaanpassing van Kubernetes-cluster|
|Microsoft.ContainerService/managedClusters|uitvoeren-scheduler|Kubernetes Scheduler|
|Microsoft.ContainerService/managedClusters|komen|Verificatiewebhook|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|Logboek voor uitvoering van pijplijn activiteit|
|Microsoft.DataFactory/factories|PipelineRuns|Logboek voor uitvoering van pijp lijn|
|Microsoft.DataFactory/factories|TriggerRuns|Trigger uitvoeringen logboek|
|Microsoft.DataLakeAnalytics/accounts|Controleren|Auditlogboeken|
|Microsoft.DataLakeAnalytics/accounts|Aanvragen|Logboeken aanvragen|
|Microsoft.DataLakeStore/accounts|Controleren|Auditlogboeken|
|Microsoft.DataLakeStore/accounts|Aanvragen|Logboeken aanvragen|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|MySQL-server logboeken|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL-server logboeken|
|Microsoft.Devices/IotHubs|Verbindingen|Verbindingen|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Apparaattelemetrie|
|Microsoft.Devices/IotHubs|C2DCommands|C2D-opdrachten|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Bewerkingen voor apparaat-Id's|
|Microsoft.Devices/IotHubs|FileUploadOperations|Bewerkingen voor het uploaden van bestanden|
|Microsoft.Devices/IotHubs|Routes|Routes|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|Dubbele C2D-bewerkingen|
|Microsoft.Devices/IotHubs|TwinQueries|Dubbele Query's|
|Microsoft.Devices/IotHubs|JobsOperations|Taak bewerkingen|
|Microsoft.Devices/IotHubs|DirectMethods|Directe methoden|
|Microsoft.Devices/IotHubs|E2EDiagnostics|E2E diagnostische gegevens (preview-versie)|
|Microsoft.Devices/IotHubs|Configuraties|Configuraties|
|Microsoft.Devices/provisioningServices|DeviceOperations|Apparaatbewerkingen|
|Microsoft.Devices/provisioningServices|ServiceOperations|Service bewerkingen|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|Archief logboeken|
|Microsoft.EventHub/namespaces|OperationalLogs|Operationele logboeken|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Logboeken automatisch schalen|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Automatische schaalaanpassing gebruiken voor evaluaties|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Automatische schaalaanpassing gebruiken voor schaalacties|
|Microsoft.IoTSpaces/Graph|Trace|Trace|
|Microsoft.IoTSpaces/Graph|Operationeel|Operationeel|
|Microsoft.IoTSpaces/Graph|Controleren|Controleren|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Inkomend verkeer|Inkomend verkeer|
|Microsoft.IoTSpaces/Graph|Uitgaand verkeer|Uitgaand verkeer|
|Microsoft.KeyVault/vaults|Audit event|Auditlogboeken|
|Microsoft.Logic/workflows|WorkflowRuntime|Diagnostische gebeurtenissen voor workflowruntime|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Spoor gebeurtenissen voor integratie account|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Gebeurtenis netwerk beveiligings groep|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Teller van regel voor netwerk beveiligings groep|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Load Balancer waarschuwings gebeurtenissen|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Load Balancer test status|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS-beveiligings meldingen|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Stroom logboeken van DDoS-oplossings beslissingen|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Rapporten met DDoS-oplossingen|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Waarschuwingen voor VM-beveiliging|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway Access-logboek|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Prestatie logboek Application Gateway|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway firewall-logboek|
|Microsoft.Network/securegateways|AzureFirewallApplicationRule|Toepassings regel Azure Firewall|
|Microsoft.Network/securegateways|AzureFirewallNetworkRule|Azure Firewall netwerk regel|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Toepassings regel Azure Firewall|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Azure Firewall netwerk regel|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Diagnostische logboeken van de gateway|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Diagnostische logboeken voor tunnel|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Diagnostische logboeken voor route ring|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Diagnostische logboeken voor IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Diagnostische logboeken van P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Gebeurtenis met resultaten van Traffic Manager test status|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Logboeken voor peering route tabel|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|-Ingang-toegangs logboek|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|-Ingang Web Application firewall-logboek|
|Micro soft. PowerBIDedicated/capaciteiten|Engine|Engine|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Rapportagegegevens voor Azure Backup|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery-taken|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery-gebeurtenissen|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Gerepliceerde Azure Site Recovery-items|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Gerepliceerde Azure Site Recovery-statistieken|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery-herstelpunten|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Upload frequentie van Azure Site Recovery replicatie gegevens|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Gegevens verloop van beveiligde schijf Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Bewerkings logboeken|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Operationele logboeken|
|Microsoft.Sql/servers/databases|SQLInsights|SQL-inzichten|
|Microsoft.Sql/servers/databases|AutomaticTuning|Automatisch afstemmen|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Runtime statistieken voor query Store|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Wacht statistieken voor query Store|
|Microsoft.Sql/servers/databases|Fouten|Fouten|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Data base-wacht statistieken|
|Microsoft.Sql/servers/databases|Time-outs|Time-outs|
|Microsoft.Sql/servers/databases|Block|Block|
|Microsoft.Sql/servers/databases|Deadlocks|Deadlocks|
|Microsoft.Sql/servers/databases|Controleren|Auditlogboeken|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|
|Microsoft.Sql/servers/databases|DmsWorkers|DMS-werk rollen|
|Microsoft.Sql/servers/databases|ExecRequests|Exec-aanvragen|
|Microsoft.Sql/servers/databases|RequestSteps|Stappen voor aanvragen|
|Microsoft.Sql/servers/databases|SqlRequests|SQL-aanvragen|
|Microsoft.Sql/servers/databases|Wacht|Wacht|
|Micro soft. SQL/managedInstances|ResourceUsageStats|Resource gebruiks statistieken|
|Micro soft. SQL/managedInstances|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|
|Microsoft.Sql/managedInstances/databases|SQLInsights|SQL-inzichten|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|Runtime statistieken voor query Store|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|Wacht statistieken voor query Store|
|Microsoft.Sql/managedInstances/databases|Fouten|Fouten|
|Microsoft.StreamAnalytics/streamingjobs|Kan worden uitgevoerd|Kan worden uitgevoerd|
|Microsoft.StreamAnalytics/streamingjobs|Ontwerpen|Ontwerpen|
|microsoft.web/sites|FunctionExecutionLogs|Uitvoer logboeken voor functies|
|microsoft.web/sites/slots|FunctionExecutionLogs|Uitvoer logboeken voor functies|

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md)
* [Diagnostische logboeken van de resource naar Stream **Event Hubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Diagnostische instellingen voor resources met behulp van de REST-API van Azure Monitor wijzigt](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Logboeken van Azure storage met Log Analytics analyseren](../../azure-monitor/platform/collect-azure-metrics-logs.md)
