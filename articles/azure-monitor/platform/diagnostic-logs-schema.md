---
title: Ondersteunde services en schema's voor Azure resource logs
description: Meer informatie over de ondersteunde services en het gebeurtenis schema voor Azure-resource Logboeken.
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: 7183c0b268342d08fe7c0ed79c7fa589e3e28afe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82128472"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Ondersteunde services, schema's en categorieën voor Azure-resource logboeken

> [!NOTE]
> Bron logboeken zijn voorheen bekend als Diagnostische logboeken.

[Azure monitor bron logboeken](../../azure-monitor/platform/platform-logs-overview.md) worden logboeken gegenereerd door Azure-Services waarmee de werking van deze services of bronnen wordt beschreven. Alle bron logboeken die beschikbaar zijn via Azure Monitor, delen een gemeen schappelijk schema op het hoogste niveau, met flexibiliteit voor elke service om unieke eigenschappen voor hun eigen gebeurtenissen te verzenden.

Een combi natie van het resource type (beschikbaar in `resourceId` de eigenschap) en `category` de unieke identificatie van een schema. In dit artikel wordt het schema op het hoogste niveau voor resource logboeken en koppelingen naar de schema's voor elke service beschreven.

## <a name="top-level-resource-logs-schema"></a>Schema voor resource logboeken op het hoogste niveau

| Naam | Vereist/optioneel | Beschrijving |
|---|---|---|
| tijd | Vereist | De tijds tempel (UTC) van de gebeurtenis. |
| resourceId | Vereist | De resource-ID van de resource die de gebeurtenis heeft verzonden. Voor Tenant Services is dit de vorm/tenants/Tenant-id/providers/provider-name. |
| tenantId | Vereist voor Tenant logboeken | De Tenant-ID van de Active Directory-Tenant waaraan deze gebeurtenis is gekoppeld. Deze eigenschap wordt alleen gebruikt voor logboeken op Tenant niveau, maar wordt niet weer gegeven in Logboeken op resource niveau. |
| operationName | Vereist | De naam van de bewerking die door deze gebeurtenis wordt vertegenwoordigd. Als de gebeurtenis een RBAC-bewerking vertegenwoordigt, is dit de naam van de RBAC-bewerking (bijvoorbeeld Micro soft. Storage/Storage accounts/blobServices/blobs/lezen). Normaal gesp roken gemodelleerd in de vorm van een resource manager-bewerking, zelfs als ze niet de werkelijke gedocumenteerde Resource Manager-bewerkingen (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
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

## <a name="service-specific-schemas-for-resource-logs"></a>Servicespecifieke schema's voor resource logboeken
Het schema voor de diagnostische logboeken voor bronnen varieert afhankelijk van de categorie resource en logboek. Deze lijst bevat alle services die beschik bare resource logboeken en koppelingen naar de service en het specifieke schema voor de categorie maken, indien beschikbaar.

| Service | & documenten voor schema |
| --- | --- |
| Azure Active Directory | [Overzicht](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), schema voor [audit logboeken](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) en [aanmeldingen](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API Management bron logboeken](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| Toepassingsgateways |[Logboek registratie voor Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics voor Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch logboek registratie](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Diagnostische logboeken Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL logboeken](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Data Explorer | [Azure Data Explorer-logboeken](/azure/data-explorer/using-diagnostic-logs) |
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
| Key Vault |[Azure Key Vault logboek registratie](../../key-vault/general/logging.md) |
| Kubernetes Service |[Logboek registratie voor Azure Kubernetes](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Logboekanalyse voor Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Aangepast Logic Apps B2B-volgschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Netwerkbeveiligingsgroepen |[Logboekanalyses voor netwerkbeveiligingsgroepen (NSG's)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS Protection | [Azure DDoS Protection Standard beheren](../../virtual-network/manage-ddos-protection.md) |
| Power BI Dedicated | [Logboek registratie voor Power BI Embedded in azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Gegevens model voor Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Search |[Zoek Traffic Analytics inschakelen en gebruiken](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus logboeken](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database logboek registratie](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Taaklogboeken](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager-logboek schema](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuele netwerken | Het schema is niet beschikbaar. |
| Virtuele netwerkgateways | Het schema is niet beschikbaar. |

## <a name="supported-log-categories-per-resource-type"></a>Ondersteunde logboek categorieën per resource type

Sommige categorieën worden mogelijk alleen ondersteund voor specifieke typen resources. Dit is een lijst van alle beschik bare in een formulier.  Bijvoorbeeld: categorieën van micro soft. SQL/servers/data bases zijn niet beschikbaar voor alle typen data bases. Zie [informatie over SQL database diagnostische logboek registratie](../../sql-database/sql-database-metrics-diag-logging.md)voor meer informatie. 

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
|Micro soft. AnalysisServices/servers|Engine|Engine|
|Micro soft. AnalysisServices/servers|Service|Service|
|Microsoft.ApiManagement/service|Gateway logs|Logboeken gerelateerd aan de ApiManagement-gateway|
|Micro soft. AppPlatform/lente|ApplicationConsole|Toepassings console|
|Micro soft. Automation/automationAccounts|JobLogs|Taak logboeken|
|Micro soft. Automation/automationAccounts|JobStreams|Taak stromen|
|Micro soft. Automation/automationAccounts|DscNodeStatus|DSC-knooppunt status|
|Micro soft. batch/batchAccounts|ServiceLog|Service logboeken|
|Micro soft. BatchAI/werk ruimten|BaiClusterEvent|BaiClusterEvent|
|Micro soft. BatchAI/werk ruimten|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Micro soft. BatchAI/werk ruimten|BaiJobEvent|BaiJobEvent|
|Micro soft. Block Chain/blockchainMembers|BlockchainApplication|Block Chain-toepassing|
|Micro soft. Block Chain/blockchainMembers|Proxy|Proxy|
|Micro soft. CDN/profielen/eind punten|CoreAnalytics|Hiermee worden de metrische gegevens van het eind punt opgehaald, bijvoorbeeld band breedte, uitgaand verkeer enzovoort.|
|Micro soft. ClassicNetwork/networksecuritygroups|Stroom gebeurtenis van regel voor netwerk beveiligings groep|Stroom gebeurtenis van regel voor netwerk beveiligings groep|
|Micro soft. CognitiveServices/accounts|Controleren|Auditlogboeken|
|Micro soft. CognitiveServices/accounts|RequestResponse|Aanvraag-en antwoord logboeken|
|Micro soft. ContainerRegistry/registers|ContainerRegistryRepositoryEvents|RepositoryEvent-Logboeken (preview-versie)|
|Micro soft. ContainerRegistry/registers|ContainerRegistryLoginEvents|Aanmeldings gebeurtenissen (preview-versie)|
|Micro soft. container service/managedClusters|uitvoeren-apiserver|Kubernetes-API-server|
|Micro soft. container service/managedClusters|uitvoeren-Controller-Manager|Kubernetes-controller beheer|
|Micro soft. container service/managedClusters|uitvoeren-scheduler|Kubernetes scheduler|
|Micro soft. container service/managedClusters|uitvoeren-audit|Kubernetes-controle|
|Micro soft. container service/managedClusters|cluster-automatisch schalen|Kubernetes-cluster automatisch schalen|
|Micro soft. Databricks/werk ruimten|dbfs|Databricks-bestandssysteem|
|Micro soft. Databricks/werk ruimten|clusters|Databricks-clusters|
|Micro soft. Databricks/werk ruimten|accounts|Databricks-accounts|
|Micro soft. Databricks/werk ruimten|functies|Databricks-taken|
|Micro soft. Databricks/werk ruimten|notitieblok|Databricks Notebook|
|Micro soft. Databricks/werk ruimten|SSH|Databricks SSH|
|Micro soft. Databricks/werk ruimten|werkruimte|Databricks-werk ruimte|
|Micro soft. Databricks/werk ruimten|geheimen|Databricks geheimen|
|Micro soft. Databricks/werk ruimten|sqlPermissions|Databricks SQLPermissions|
|Micro soft. Databricks/werk ruimten|instancePools|Instantie groepen|
|Micro soft. DataCatalog/datacatalogs|ScanStatusLogEvent|ScanStatus|
|Micro soft. DataFactory/fabrieken|ActivityRuns|Logboek voor uitvoering van pijplijn activiteit|
|Micro soft. DataFactory/fabrieken|PipelineRuns|Logboek voor uitvoering van pijp lijn|
|Micro soft. DataFactory/fabrieken|TriggerRuns|Trigger uitvoeringen logboek|
|Micro soft. DataLakeAnalytics/accounts|Controleren|Auditlogboeken|
|Micro soft. DataLakeAnalytics/accounts|Aanvragen|Logboeken aanvragen|
|Micro soft. data Lake Store/accounts|Controleren|Auditlogboeken|
|Micro soft. data Lake Store/accounts|Aanvragen|Logboeken aanvragen|
|Micro soft. DataShare/accounts|Shares|Shares|
|Micro soft. DataShare/accounts|ShareSubscriptions|Abonnementen delen|
|Micro soft. DataShare/accounts|SentShareSnapshots|Verzonden moment opnamen van shares|
|Micro soft. DataShare/accounts|ReceivedShareSnapshots|Ontvangen moment opnamen van shares|
|Micro soft. DBforMySQL/servers|MySqlSlowLogs|MySQL-server logboeken|
|Micro soft. DBforMySQL/servers|MySqlAuditLogs|MySQL-controle logboeken|
|Micro soft. DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL-server logboeken|
|Micro soft. DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|Runtime statistieken voor PostgreSQL query Store|
|Micro soft. DBforPostgreSQL/servers|QueryStoreWaitStatistics|Wacht statistieken voor PostgreSQL query Store|
|Micro soft. DBforPostgreSQL/serversv2|PostgreSQLLogs|PostgreSQL-server logboeken|
|Micro soft. DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|Runtime statistieken voor PostgreSQL query Store|
|Micro soft. DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|Wacht statistieken voor PostgreSQL query Store|
|Micro soft. DesktopVirtualization/werk ruimten|Controlepunt|Controlepunt|
|Micro soft. DesktopVirtualization/werk ruimten|Fout|Fout|
|Micro soft. DesktopVirtualization/werk ruimten|Beheer|Beheer|
|Micro soft. DesktopVirtualization/werk ruimten|Feed|Feed|
|Micro soft. DesktopVirtualization/applicationGroups|Controlepunt|Controlepunt|
|Micro soft. DesktopVirtualization/applicationGroups|Fout|Fout|
|Micro soft. DesktopVirtualization/applicationGroups|Beheer|Beheer|
|Micro soft. DesktopVirtualization/hostPools|Controlepunt|Controlepunt|
|Micro soft. DesktopVirtualization/hostPools|Fout|Fout|
|Micro soft. DesktopVirtualization/hostPools|Beheer|Beheer|
|Micro soft. DesktopVirtualization/hostPools|Verbinding|Verbinding|
|Micro soft. DesktopVirtualization/hostPools|HostRegistration|HostRegistration|
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
|Micro soft. devices/IotHubs|DistributedTracing|Gedistribueerde tracering (preview-versie)|
|Micro soft. devices/IotHubs|Configuraties|Configuraties|
|Micro soft. devices/IotHubs|DeviceStreams|Apparaatversleuteling (preview-versie)|
|Micro soft. devices/provisioningServices|DeviceOperations|Bewerkingen voor apparaten|
|Micro soft. devices/provisioningServices|ServiceOperations|Service bewerkingen|
|Micro soft. DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Micro soft. DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Micro soft. DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Micro soft. DocumentDB/databaseAccounts|PartitionKeyStatistics|PartitionKeyStatistics|
|Micro soft. DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|Micro soft. EnterpriseKnowledgeGraph/Services|Audit event|Audit event-logboek|
|Micro soft. EnterpriseKnowledgeGraph/Services|DataIssue|DataIssue-logboek|
|Micro soft. EnterpriseKnowledgeGraph/Services|Aanvragen|Configuratie logboek|
|Micro soft. EventHub/naam ruimten|ArchiveLogs|Archief logboeken|
|Micro soft. EventHub/naam ruimten|OperationalLogs|Operationele logboeken|
|Micro soft. EventHub/naam ruimten|AutoScaleLogs|Logboeken automatisch schalen|
|Micro soft. EventHub/naam ruimten|KafkaCoordinatorLogs|Kafka Coordinator-logboeken|
|Micro soft. EventHub/naam ruimten|KafkaUserErrorLogs|Fout logboeken van Kafka-gebruikers|
|Micro soft. EventHub/naam ruimten|EventHubVNetConnectionEvent|Logboeken voor VNet/IP-filtering verbindingen|
|Micro soft. EventHub/naam ruimten|CustomerManagedKeyUserLogs|Door de klant beheerde sleutel logboeken|
|Micro soft. HealthcareApis/Services|Audit logs bevat|Auditlogboeken|
|Micro soft. Insights/AutoscaleSettings|AutoscaleEvaluations|Evaluaties automatisch schalen|
|Micro soft. Insights/AutoscaleSettings|AutoscaleScaleActions|Schaal acties automatisch schalen|
|Micro soft. IoTSpaces/Graph|Tracering|Tracering|
|Micro soft. IoTSpaces/Graph|Functioneren|Functioneren|
|Micro soft. IoTSpaces/Graph|Controleren|Controleren|
|Micro soft. IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Micro soft. IoTSpaces/Graph|Inkomend verkeer|Inkomend verkeer|
|Micro soft. IoTSpaces/Graph|Uitgaand verkeer|Uitgaand verkeer|
|Micro soft.-sleutel kluis/-kluizen|Audit event|Auditlogboeken|
|Micro soft. Kusto/clusters|SucceededIngestion|Geslaagde opname bewerkingen|
|Micro soft. Kusto/clusters|FailedIngestion|Mislukte opname bewerkingen|
|Micro soft. Logic/werk stromen|WorkflowRuntime|Diagnostische gebeurtenissen voor workflowruntime|
|Micro soft. Logic/integrationAccounts|IntegrationAccountTrackingEvents|Spoor gebeurtenissen voor integratie account|
|Micro soft. MachineLearningServices/werk ruimten|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Micro soft. MachineLearningServices/werk ruimten|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Micro soft. MachineLearningServices/werk ruimten|AmlComputeJobEvent|AmlComputeJobEvent|
|Micro soft. Media/Media Services|KeyDeliveryRequests|Aanvragen voor sleutel levering|
|Micro soft. Network/networksecuritygroups|NetworkSecurityGroupEvent|Gebeurtenis netwerk beveiligings groep|
|Micro soft. Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Teller van regel voor netwerk beveiligings groep|
|Micro soft. Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Stroom gebeurtenis van regel voor netwerk beveiligings groep|
|Micro soft. Network/publicIPAddresses|DDoSProtectionNotifications|DDoS-beveiligings meldingen|
|Micro soft. Network/publicIPAddresses|DDoSMitigationFlowLogs|Stroom logboeken van DDoS-oplossings beslissingen|
|Micro soft. Network/publicIPAddresses|DDoSMitigationReports|Rapporten met DDoS-oplossingen|
|Micro soft. Network/virtualNetworks|VMProtectionAlerts|Waarschuwingen voor VM-beveiliging|
|Micro soft. Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway Access-logboek|
|Micro soft. Network/applicationGateways|ApplicationGatewayPerformanceLog|Prestatie logboek Application Gateway|
|Micro soft. Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway firewall-logboek|
|Micro soft. Network/azurefirewalls|AzureFirewallApplicationRule|Toepassings regel Azure Firewall|
|Micro soft. Network/azurefirewalls|AzureFirewallNetworkRule|Azure Firewall netwerk regel|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Diagnostische logboeken van de gateway|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Diagnostische logboeken voor tunnel|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Diagnostische logboeken voor route ring|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Diagnostische logboeken voor IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Diagnostische logboeken van P2S|
|Micro soft. Network/trafficManagerProfiles|ProbeHealthStatusEvents|Gebeurtenis met resultaten van Traffic Manager test status|
|Micro soft. Network/expressRouteCircuits|PeeringRouteLog|Logboeken voor peering route tabel|
|Micro soft. Network/vpnGateways|GatewayDiagnosticLog|Diagnostische logboeken van de gateway|
|Micro soft. Network/vpnGateways|TunnelDiagnosticLog|Diagnostische logboeken voor tunnel|
|Micro soft. Network/vpnGateways|RouteDiagnosticLog|Diagnostische logboeken voor route ring|
|Micro soft. Network/vpnGateways|IKEDiagnosticLog|Diagnostische logboeken voor IKE|
|Micro soft. Network/frontdoors|FrontdoorAccessLog|-Ingang-toegangs logboek|
|Micro soft. Network/frontdoors|FrontdoorWebApplicationFirewallLog|-Ingang Web Application firewall-logboek|
|Micro soft. Network/p2sVpnGateways|GatewayDiagnosticLog|Diagnostische logboeken van de gateway|
|Micro soft. Network/p2sVpnGateways|IKEDiagnosticLog|Diagnostische logboeken voor IKE|
|Micro soft. Network/p2sVpnGateways|P2SDiagnosticLog|Diagnostische logboeken van P2S|
|Micro soft. Network/bastionHosts|BastionAuditLogs|Controle logboeken voor Bastion|
|Micro soft. Network/loadBalancers|LoadBalancerAlertEvent|Load Balancer waarschuwings gebeurtenissen|
|Micro soft. Network/loadBalancers|LoadBalancerProbeHealthStatus|Load Balancer test status|
|Micro soft. PowerBIDedicated/capaciteiten|Engine|Engine|
|Micro soft. Recovery Services/kluizen|AzureBackupReport|Azure Backup rapportage gegevens|
|Micro soft. Recovery Services/kluizen|CoreAzureBackup|Kern Azure Backup gegevens|
|Micro soft. Recovery Services/kluizen|AddonAzureBackupJobs|Taak gegevens van invoeg toepassing Azure Backup|
|Micro soft. Recovery Services/kluizen|AddonAzureBackupAlerts|Azure Backup waarschuwings gegevens van invoeg toepassing|
|Micro soft. Recovery Services/kluizen|AddonAzureBackupPolicy|Azure Backup beleids gegevens van invoeg toepassing|
|Micro soft. Recovery Services/kluizen|AddonAzureBackupStorage|Invoeg Azure Backup opslag gegevens|
|Micro soft. Recovery Services/kluizen|AddonAzureBackupProtectedInstance|Invoeg toepassing Azure Backup beveiligde instantie gegevens|
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
|Micro soft. Storage/Storage accounts/tableServices|StorageRead|StorageRead|
|Micro soft. Storage/Storage accounts/tableServices|StorageWrite|StorageWrite|
|Micro soft. Storage/Storage accounts/tableServices|StorageDelete|StorageDelete|
|Micro soft. Storage/Storage accounts/blobServices|StorageRead|StorageRead|
|Micro soft. Storage/Storage accounts/blobServices|StorageWrite|StorageWrite|
|Micro soft. Storage/Storage accounts/blobServices|StorageDelete|StorageDelete|
|Micro soft. Storage/Storage accounts/fileServices|StorageRead|StorageRead|
|Micro soft. Storage/Storage accounts/fileServices|StorageWrite|StorageWrite|
|Micro soft. Storage/Storage accounts/fileServices|StorageDelete|StorageDelete|
|Micro soft. Storage/Storage accounts/queueServices|StorageRead|StorageRead|
|Micro soft. Storage/Storage accounts/queueServices|StorageWrite|StorageWrite|
|Micro soft. Storage/Storage accounts/queueServices|StorageDelete|StorageDelete|
|Micro soft. StreamAnalytics/streamingjobs|Uitvoering|Uitvoering|
|Micro soft. StreamAnalytics/streamingjobs|Ontwerpen|Ontwerpen|
|micro soft. Web/hostingenvironments|AppServiceEnvironmentPlatformLogs|App Service Environment-platform logboeken|
|micro soft. web/sites|FunctionAppLogs|Toepassings logboeken van functie|
|micro soft. web/sites|AppServiceHTTPLogs|HTTP-logboeken|
|micro soft. web/sites|AppServiceConsoleLogs|App Service-console logboeken|
|micro soft. web/sites|AppServiceAppLogs|Toepassings logboeken App Service|
|micro soft. web/sites|AppServiceFileAuditLogs|Controle logboeken voor het wijzigen van de site-inhoud|
|micro soft. web/sites|AppServiceAuditLogs|Access-controle logboeken|
|micro soft. web/sites/sleuven|FunctionAppLogs|Toepassings logboeken van functie|
|micro soft. web/sites/sleuven|AppServiceHTTPLogs|HTTP-logboeken|
|micro soft. web/sites/sleuven|AppServiceConsoleLogs|Console logboeken|
|micro soft. web/sites/sleuven|AppServiceAppLogs|Toepassingslogboeken|
|micro soft. web/sites/sleuven|AppServiceFileAuditLogs|Controle logboeken voor het wijzigen van de site-inhoud|
|micro soft. web/sites/sleuven|AppServiceAuditLogs|Access-controle logboeken|

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over bron logboeken](../../azure-monitor/platform/platform-logs-overview.md)
* [Resource bron logboeken streamen naar **Event hubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Diagnostische instellingen voor bron logboek wijzigen met behulp van de Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Logboeken analyseren vanuit Azure Storage met Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
