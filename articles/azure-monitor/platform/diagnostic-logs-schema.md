---
title: Ondersteunde services en schema's voor Azure resource logs
description: Meer informatie over de ondersteunde services en het gebeurtenis schema voor Azure-resource Logboeken.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
author: rboucher
ms.author: robb
ms.openlocfilehash: e744cdde298054de3631adb96b56bbc808f36a38
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750952"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Ondersteunde services, schema's en categorieën voor Azure-resource logboeken

> [!NOTE]
> Bron logboeken zijn voorheen bekend als Diagnostische logboeken.

[Azure monitor bron logboeken](../../azure-monitor/platform/platform-logs-overview.md) worden logboeken gegenereerd door Azure-Services waarmee de werking van deze services of bronnen wordt beschreven. Alle bron logboeken die beschikbaar zijn via Azure Monitor, delen een gemeen schappelijk schema op het hoogste niveau, met flexibiliteit voor elke service om unieke eigenschappen voor hun eigen gebeurtenissen te verzenden.

Een combi natie van het resource type (beschikbaar in de eigenschap `resourceId`) en de `category` unieke identificatie van een schema. In dit artikel wordt het schema op het hoogste niveau voor resource logboeken en koppelingen naar de schema's voor elke service beschreven.

## <a name="top-level-resource-logs-schema"></a>Schema voor resource logboeken op het hoogste niveau

| Name | Vereist/optioneel | Beschrijving |
|---|---|---|
| tijd | Verplicht | De tijds tempel (UTC) van de gebeurtenis. |
| resourceId | Verplicht | De resource-ID van de resource die de gebeurtenis heeft verzonden. Voor Tenant Services is dit de vorm/tenants/Tenant-id/providers/provider-name. |
| tenantId | Vereist voor Tenant logboeken | De Tenant-ID van de Active Directory-Tenant waaraan deze gebeurtenis is gekoppeld. Deze eigenschap wordt alleen gebruikt voor logboeken op Tenant niveau, maar wordt niet weer gegeven in Logboeken op resource niveau. |
| operationName | Verplicht | De naam van de bewerking die door deze gebeurtenis wordt vertegenwoordigd. Als de gebeurtenis een RBAC-bewerking vertegenwoordigt, is dit de naam van de RBAC-bewerking (bijvoorbeeld Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Normaal gesp roken gemodelleerd in de vorm van een resource manager-bewerking, zelfs als ze niet de werkelijke gedocumenteerde Resource Manager-bewerkingen (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
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

## <a name="service-specific-schemas-for-resource-logs"></a>Servicespecifieke schema's voor resource logboeken
Het schema voor de diagnostische logboeken voor bronnen varieert afhankelijk van de categorie resource en logboek. Deze lijst bevat alle services die beschik bare resource logboeken en koppelingen naar de service en het specifieke schema voor de categorie maken, indien beschikbaar.

| Service | & Documenten voor schema |
| --- | --- |
| Azure Active Directory | [Overzicht](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), schema voor [audit logboeken](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) en [aanmeldingen](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API Management bron logboeken](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Toepassingsgateways |[Logboek registratie voor Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics voor Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch logboek registratie](../../batch/batch-diagnostics.md) |
| Azure Database voor MySQL | [Diagnostische logboeken Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database voor PostgreSQL | [Azure Database for PostgreSQL logboeken](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure Data Explorer | [Azure Data Explorer-logboeken](../../data-explorer/using-diagnostic-logs.md) |
| Cognitive Services | [Logboek registratie voor Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Logboek registratie voor Azure Container Registry](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| CDN (Content Delivery Network) | [Azure-logboeken voor CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB logboek registratie](../../cosmos-db/logging.md) |
| Data Factory | [Gegevens fabrieken bewaken met behulp van Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Toegang tot logboeken voor Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Toegang tot logboeken voor Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure Event Hubs-logboeken](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Het schema is niet beschikbaar. |
| Azure Firewall | Het schema is niet beschikbaar. |
| IoT Hub | [IoT Hub bewerkingen](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Logboekregistratie van Azure Key Vault](../../key-vault/key-vault-logging.md) |
| Kubernetes Service |[Logboek registratie voor Azure Kubernetes](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Logboekanalyse voor Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logische apps |[Aangepast Logic Apps B2B-volgschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Netwerkbeveiligingsgroepen |[Logboekanalyses voor netwerkbeveiligingsgroepen (NSG's)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS Protection | [Azure DDoS Protection Standard beheren](../../virtual-network/manage-ddos-protection.md) |
| Power BI Dedicated | [Logboek registratie voor Power BI Embedded in azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Gegevens model voor Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Search |[Zoek Traffic Analytics inschakelen en gebruiken](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus logboeken](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database logboek registratie](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Taak logboeken](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager-logboek schema](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuele netwerken | Het schema is niet beschikbaar. |
| Virtuele-netwerkgateways | Het schema is niet beschikbaar. |

## <a name="supported-log-categories-per-resource-type"></a>Ondersteunde logboek categorieën per resource type
|Resourcetype|Categorie|Weergave naam categorie|
|---|---|---|
|Micro soft. AAD/domainServices|SystemSecurity|SystemSecurity|
|Micro soft. AAD/domainServices|AccountManagement|AccountManagement|
|Micro soft. AAD/domainServices|LogonLogoff|LogonLogoff|
|Micro soft. AAD/domainServices|ObjectAccess|ObjectAccess|
|Micro soft. AAD/domainServices|PolicyChange|PolicyChange|
|Micro soft. AAD/domainServices|PrivilegeUse|PrivilegeUse|
|Micro soft. AAD/domainServices|DetailTracking|DetailTracking|
|Micro soft. AAD/domainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Micro soft. AAD/domainServices|AccountLogon|AccountLogon|
|micro soft. aadiam/tenants|Aanmelding|Aanmelding|
|Microsoft.AnalysisServices/servers|Engine|Engine|
|Microsoft.AnalysisServices/servers|Service|Service|
|Microsoft.ApiManagement/service|GatewayLogs|Logboeken gerelateerd aan de ApiManagement-gateway|
|Micro soft. AppPlatform/lente|ApplicationConsole|Toepassings console|
|Microsoft.Automation/automationAccounts|JobLogs|Taak logboeken|
|Microsoft.Automation/automationAccounts|JobStreams|Taak stromen|
|Microsoft.Automation/automationAccounts|DscNodeStatus|DSC-knooppunt status|
|Microsoft.Batch/batchAccounts|ServiceLog|Service logboeken|
|Micro soft. BatchAI/werk ruimten|BaiClusterEvent|BaiClusterEvent|
|Micro soft. BatchAI/werk ruimten|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Micro soft. BatchAI/werk ruimten|BaiJobEvent|BaiJobEvent|
|Microsoft.Blockchain/blockchainMembers|BlockchainApplication|Block Chain-toepassing|
|Microsoft.Blockchain/blockchainMembers|Proxy|Proxy|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Hiermee worden de metrische gegevens van het eind punt opgehaald, bijvoorbeeld band breedte, uitgaand verkeer enzovoort.|
|Microsoft.ClassicNetwork/networksecuritygroups|Stroom gebeurtenis van regel voor netwerk beveiligings groep|Stroom gebeurtenis van regel voor netwerk beveiligings groep|
|Microsoft.CognitiveServices/accounts|Controleren|Auditlogboeken|
|Microsoft.CognitiveServices/accounts|RequestResponse|Aanvraag-en antwoord logboeken|
|Micro soft. ContainerRegistry/registers|ContainerRegistryRepositoryEvents|RepositoryEvent-Logboeken (preview-versie)|
|Micro soft. ContainerRegistry/registers|ContainerRegistryLoginEvents|Aanmeldings gebeurtenissen (preview-versie)|
|Microsoft.ContainerService/managedClusters|uitvoeren-apiserver|Kubernetes-API-server|
|Microsoft.ContainerService/managedClusters|uitvoeren-Controller-Manager|Kubernetes-controller beheer|
|Microsoft.ContainerService/managedClusters|uitvoeren-scheduler|Kubernetes scheduler|
|Microsoft.ContainerService/managedClusters|uitvoeren-audit|Kubernetes-controle|
|Microsoft.ContainerService/managedClusters|cluster-automatisch schalen|Kubernetes-cluster automatisch schalen|
|Micro soft. Databricks/werk ruimten|dbfs|Databricks-bestandssysteem|
|Micro soft. Databricks/werk ruimten|clusters|Databricks-clusters|
|Micro soft. Databricks/werk ruimten|accounts|Databricks-accounts|
|Micro soft. Databricks/werk ruimten|functies|Databricks-taken|
|Micro soft. Databricks/werk ruimten|notitieblok|Databricks Notebook|
|Micro soft. Databricks/werk ruimten|SSH|Databricks SSH|
|Micro soft. Databricks/werk ruimten|werkruimte|Databricks-werkruimte|
|Micro soft. Databricks/werk ruimten|geheimen|Databricks geheimen|
|Micro soft. Databricks/werk ruimten|sqlPermissions|Databricks SQLPermissions|
|Micro soft. Databricks/werk ruimten|instancePools|Instantiegroepen|
|Micro soft. DataCatalog/datacatalogs|ScanStatusLogEvent|ScanStatus|
|Microsoft.DataFactory/factories|ActivityRuns|Logboek voor uitvoering van pijplijn activiteit|
|Microsoft.DataFactory/factories|PipelineRuns|Logboek voor uitvoering van pijp lijn|
|Microsoft.DataFactory/factories|TriggerRuns|Trigger uitvoeringen logboek|
|Microsoft.DataLakeAnalytics/accounts|Controleren|Auditlogboeken|
|Microsoft.DataLakeAnalytics/accounts|Aanvragen|Logboeken aanvragen|
|Microsoft.DataLakeStore/accounts|Controleren|Auditlogboeken|
|Microsoft.DataLakeStore/accounts|Aanvragen|Logboeken aanvragen|
|Micro soft. DataShare/accounts|Shares|Shares|
|Micro soft. DataShare/accounts|ShareSubscriptions|Abonnementen delen|
|Micro soft. DataShare/accounts|SentShareSnapshots|Verzonden moment opnamen van shares|
|Micro soft. DataShare/accounts|ReceivedShareSnapshots|Ontvangen moment opnamen van shares|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|MySQL-server logboeken|
|Microsoft.DBforMySQL/servers|MySqlAuditLogs|MySQL-controle logboeken|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL-server logboeken|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|Runtime statistieken voor PostgreSQL query Store|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistics|Wacht statistieken voor PostgreSQL query Store|
|Microsoft.DBforPostgreSQL/serversv2|PostgreSQLLogs|PostgreSQL-server logboeken|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|Runtime statistieken voor PostgreSQL query Store|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|Wacht statistieken voor PostgreSQL query Store|
|Micro soft. DesktopVirtualization/werk ruimten|Check Point|Check Point|
|Micro soft. DesktopVirtualization/werk ruimten|Fout|Fout|
|Micro soft. DesktopVirtualization/werk ruimten|Beheer|Beheer|
|Micro soft. DesktopVirtualization/werk ruimten|Feed|Feed|
|Micro soft. DesktopVirtualization/applicationGroups|Check Point|Check Point|
|Micro soft. DesktopVirtualization/applicationGroups|Fout|Fout|
|Micro soft. DesktopVirtualization/applicationGroups|Beheer|Beheer|
|Micro soft. DesktopVirtualization/hostPools|Check Point|Check Point|
|Micro soft. DesktopVirtualization/hostPools|Fout|Fout|
|Micro soft. DesktopVirtualization/hostPools|Beheer|Beheer|
|Micro soft. DesktopVirtualization/hostPools|Verbinding|Verbinding|
|Micro soft. DesktopVirtualization/hostPools|HostRegistration|HostRegistration|
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
|Microsoft.Devices/IotHubs|DistributedTracing|Gedistribueerde tracering (preview-versie)|
|Microsoft.Devices/IotHubs|Configuraties|Configuraties|
|Microsoft.Devices/IotHubs|DeviceStreams|Apparaatversleuteling (preview-versie)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Bewerkingen voor apparaten|
|Microsoft.Devices/provisioningServices|ServiceOperations|Service bewerkingen|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.DocumentDB/databaseAccounts|PartitionKeyStatistics|PartitionKeyStatistics|
|Microsoft.DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|Micro soft. EnterpriseKnowledgeGraph/Services|Audit event|Audit event-logboek|
|Micro soft. EnterpriseKnowledgeGraph/Services|DataIssue|DataIssue-logboek|
|Micro soft. EnterpriseKnowledgeGraph/Services|Aanvragen|Configuratie logboek|
|Microsoft.EventHub/namespaces|ArchiveLogs|Archief logboeken|
|Microsoft.EventHub/namespaces|OperationalLogs|Operationele logboeken|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Logboeken automatisch schalen|
|Microsoft.EventHub/namespaces|KafkaCoordinatorLogs|Kafka Coordinator-logboeken|
|Microsoft.EventHub/namespaces|KafkaUserErrorLogs|Fout logboeken van Kafka-gebruikers|
|Microsoft.EventHub/namespaces|EventHubVNetConnectionEvent|Logboeken voor VNet/IP-filtering verbindingen|
|Microsoft.EventHub/namespaces|CustomerManagedKeyUserLogs|Door de klant beheerde sleutel logboeken|
|Micro soft. HealthcareApis/Services|AuditLogs|Auditlogboeken|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Evaluaties automatisch schalen|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Schaal acties automatisch schalen|
|Microsoft.IoTSpaces/Graph|Tracering|Tracering|
|Microsoft.IoTSpaces/Graph|Functioneren|Functioneren|
|Microsoft.IoTSpaces/Graph|Controleren|Controleren|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Binnenkomend|Binnenkomend|
|Microsoft.IoTSpaces/Graph|Uitgaand verkeer|Uitgaand verkeer|
|Microsoft.KeyVault/vaults|Audit event|Auditlogboeken|
|Microsoft.Kusto/Clusters|SucceededIngestion|Geslaagde opname bewerkingen|
|Microsoft.Kusto/Clusters|FailedIngestion|Mislukte opname bewerkingen|
|Microsoft.Logic/workflows|WorkflowRuntime|Diagnostische gebeurtenissen voor workflowruntime|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Spoor gebeurtenissen voor integratie account|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeJobEvent|AmlComputeJobEvent|
|Micro soft. Media/Media Services|KeyDeliveryRequests|Aanvragen voor sleutel levering|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Gebeurtenis netwerk beveiligings groep|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Teller van regel voor netwerk beveiligings groep|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Stroom gebeurtenis van regel voor netwerk beveiligings groep|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS-beveiligings meldingen|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Stroom logboeken van DDoS-oplossings beslissingen|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Rapporten met DDoS-oplossingen|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Waarschuwingen voor VM-beveiliging|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway Access-logboek|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Prestatie logboek Application Gateway|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway firewall-logboek|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Toepassings regel Azure Firewall|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Azure Firewall netwerk regel|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Diagnostische logboeken van de gateway|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Diagnostische logboeken voor tunnel|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Diagnostische logboeken voor route ring|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Diagnostische logboeken voor IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Diagnostische logboeken van P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Gebeurtenis met resultaten van Traffic Manager test status|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Logboeken voor peering route tabel|
|Micro soft. Network/vpnGateways|GatewayDiagnosticLog|Diagnostische logboeken van de gateway|
|Micro soft. Network/vpnGateways|TunnelDiagnosticLog|Diagnostische logboeken voor tunnel|
|Micro soft. Network/vpnGateways|RouteDiagnosticLog|Diagnostische logboeken voor route ring|
|Micro soft. Network/vpnGateways|IKEDiagnosticLog|Diagnostische logboeken voor IKE|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|-Ingang-toegangs logboek|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|-Ingang Web Application firewall-logboek|
|Micro soft. Network/p2sVpnGateways|GatewayDiagnosticLog|Diagnostische logboeken van de gateway|
|Micro soft. Network/p2sVpnGateways|IKEDiagnosticLog|Diagnostische logboeken voor IKE|
|Micro soft. Network/p2sVpnGateways|P2SDiagnosticLog|Diagnostische logboeken van P2S|
|Micro soft. Network/bastionHosts|BastionAuditLogs|Controle logboeken voor Bastion|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Load Balancer waarschuwings gebeurtenissen|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Load Balancer test status|
|Micro soft. PowerBIDedicated/capaciteiten|Engine|Engine|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure Backup rapportage gegevens|
|Microsoft.RecoveryServices/Vaults|CoreAzureBackup|Kern Azure Backup gegevens|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupJobs|Taak gegevens van invoeg toepassing Azure Backup|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupAlerts|Azure Backup waarschuwings gegevens van invoeg toepassing|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupPolicy|Azure Backup beleids gegevens van invoeg toepassing|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupStorage|Invoeg Azure Backup opslag gegevens|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupProtectedInstance|Invoeg toepassing Azure Backup beveiligde instantie gegevens|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery taken|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery gebeurtenissen|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Gerepliceerde items Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Replicatie statistieken Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery herstel punten|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Upload frequentie van Azure Site Recovery replicatie gegevens|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Gegevens verloop van beveiligde schijf Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Bewerkings logboeken|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Operationele logboeken|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|AutomaticTuning|Automatisch instellen|
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
|Microsoft.Sql/managedInstances/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|Runtime statistieken voor query Store|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|Wacht statistieken voor query Store|
|Microsoft.Sql/managedInstances/databases|Fouten|Fouten|
|Microsoft.Storage/storageAccounts/tableServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/tableServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/tableServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/blobServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/blobServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/blobServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/fileServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/fileServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/fileServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/queueServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/queueServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/queueServices|StorageDelete|StorageDelete|
|Microsoft.StreamAnalytics/streamingjobs|Uitvoering|Uitvoering|
|Microsoft.StreamAnalytics/streamingjobs|Ontwerpen|Ontwerpen|
|micro soft. Web/hostingenvironments|AppServiceEnvironmentPlatformLogs|App Service Environment-platform logboeken|
|microsoft.web/sites|FunctionAppLogs|Toepassings logboeken van functie|
|microsoft.web/sites|AppServiceHTTPLogs|HTTP-logboeken|
|microsoft.web/sites|AppServiceConsoleLogs|App Service-console logboeken|
|microsoft.web/sites|AppServiceAppLogs|Toepassings logboeken App Service|
|microsoft.web/sites|AppServiceFileAuditLogs|Controle logboeken voor het wijzigen van de site-inhoud|
|microsoft.web/sites|AppServiceAuditLogs|Access-controle logboeken|
|microsoft.web/sites/slots|FunctionAppLogs|Toepassings logboeken van functie|
|microsoft.web/sites/slots|AppServiceHTTPLogs|HTTP-logboeken|
|microsoft.web/sites/slots|AppServiceConsoleLogs|Console logboeken|
|microsoft.web/sites/slots|AppServiceAppLogs|Toepassingslogboeken|
|microsoft.web/sites/slots|AppServiceFileAuditLogs|Controle logboeken voor het wijzigen van de site-inhoud|
|microsoft.web/sites/slots|AppServiceAuditLogs|Access-controle logboeken|

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over bron logboeken](../../azure-monitor/platform/platform-logs-overview.md)
* [Resource bron logboeken streamen naar **Event hubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Diagnostische instellingen voor bron logboek wijzigen met behulp van de Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Logboeken van Azure storage met Log Analytics analyseren](../../azure-monitor/platform/collect-azure-metrics-logs.md)
