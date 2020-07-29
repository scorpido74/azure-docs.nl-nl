---
title: Azure Monitor resource logboeken ondersteunde services en categorieën
description: Verwijzing van Azure Monitor inzicht krijgen in de ondersteunde services en het gebeurtenis schema voor Azure-resource Logboeken.
ms.subservice: logs
ms.topic: reference
ms.date: 06/03/2020
ms.openlocfilehash: 81f79b81c03e7996d7f6d45b002d8160740c3c14
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318297"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Ondersteunde categorieën voor Azure-resource logboeken

> [!NOTE]
> Bron logboeken zijn voorheen bekend als Diagnostische logboeken. De naam is in oktober 2019 gewijzigd, omdat de typen logboeken die door Azure Monitor zijn verzameld, meer dan alleen de Azure-resource bevatten.

[Azure monitor bron logboeken](./platform-logs-overview.md) worden logboeken gegenereerd door Azure-Services waarmee de werking van deze services of bronnen wordt beschreven. Alle bron logboeken die beschikbaar zijn via Azure Monitor, delen een gemeen schappelijk schema op het hoogste niveau, met flexibiliteit voor elke service om unieke eigenschappen voor hun eigen gebeurtenissen te verzenden.

Een combi natie van het resource type (beschikbaar in de `resourceId` eigenschap) en de `category` unieke identificatie van een schema. Er is een gemeen schappelijk schema voor alle resource logboeken met servicespecifieke velden en vervolgens toegevoegd voor verschillende logboek categorieën. Zie [common en service-specifiek schema voor Azure-resource logboeken]() voor meer informatie.

## <a name="supported-log-categories-per-resource-type"></a>Ondersteunde logboek categorieën per resource type

Hieronder volgt een lijst met de typen logboeken die beschikbaar zijn voor elk resource type. 

Sommige categorieën worden mogelijk alleen ondersteund voor specifieke typen resources. Raadpleeg de resource-specifieke documentatie als u denkt dat er een resource ontbreekt. Bijvoorbeeld: categorieën van micro soft. SQL/servers/data bases zijn niet beschikbaar voor alle typen data bases. Zie [informatie over SQL database diagnostische logboek registratie](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)voor meer informatie. 

Als er nog steeds iets ontbreekt, kunt u onder aan dit artikel een GitHub-opmerking openen.

## <a name="microsoftaadiamtenants"></a>micro soft. aadiam/tenants

|Categorie|Weergave naam categorie|
|---|---|
|Aanmelding|Aanmelding|


## <a name="microsoftanalysisservicesservers"></a>Micro soft. AnalysisServices/servers

|Categorie|Weergave naam categorie|
|---|---|
|Engine|Engine|
|Service|Service|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Categorie|Weergave naam categorie|
|---|---|
|Gateway logs|Logboeken gerelateerd aan de ApiManagement-gateway|


## <a name="microsoftappplatformspring"></a>Micro soft. AppPlatform/lente

|Categorie|Weergave naam categorie|
|---|---|
|ApplicationConsole|Toepassings console|
|SystemLogs|Systeem logboeken|


## <a name="microsoftautomationautomationaccounts"></a>Micro soft. Automation/automationAccounts

|Categorie|Weergave naam categorie|
|---|---|
|JobLogs|Taak logboeken|
|JobStreams|Taak stromen|
|DscNodeStatus|DSC-knooppunt status|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Bat-CH/batchAccounts

|Categorie|Weergave naam categorie|
|---|---|
|ServiceLog|Service logboeken|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/werk ruimten

|Categorie|Weergave naam categorie|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Categorie|Weergave naam categorie|
|---|---|
|BlockchainApplication|Block Chain-toepassing|
|Proxy|Proxy|


## <a name="microsoftblockchaincordamembers"></a>Micro soft. Block Chain/cordaMembers

|Categorie|Weergave naam categorie|
|---|---|
|BlockchainApplication|Block Chain-toepassing|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Micro soft. CDN/cdnwebapplicationfirewallpolicies

|Categorie|Weergave naam categorie|
|---|---|
|WebApplicationFirewallLogs|Web Application firewall-logboeken|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|Categorie|Weergave naam categorie|
|---|---|
|AzureCdnAccessLog|Azure CDN-toegangs logboek|


## <a name="microsoftcdnprofilesendpoints"></a>Micro soft. CDN/profielen/eind punten

|Categorie|Weergave naam categorie|
|---|---|
|CoreAnalytics|Hiermee worden de metrische gegevens van het eind punt opgehaald, bijvoorbeeld band breedte, uitgaand verkeer enzovoort.|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Micro soft. ClassicNetwork/networksecuritygroups

|Categorie|Weergave naam categorie|
|---|---|
|Stroom gebeurtenis van regel voor netwerk beveiligings groep|Stroom gebeurtenis van regel voor netwerk beveiligings groep|


## <a name="microsoftcognitiveservicesaccounts"></a>Micro soft. CognitiveServices/accounts

|Categorie|Weergave naam categorie|
|---|---|
|Controleren|Auditlogboeken|
|RequestResponse|Aanvraag-en antwoord logboeken|


## <a name="microsoftcontainerregistryregistries"></a>Micro soft. ContainerRegistry/registers

|Categorie|Weergave naam categorie|
|---|---|
|ContainerRegistryLoginEvents|Aanmeldings gebeurtenissen|
|ContainerRegistryRepositoryEvents|RepositoryEvent-logboeken|


## <a name="microsoftcontainerservicemanagedclusters"></a>Micro soft. container service/managedClusters

|Categorie|Weergave naam categorie|
|---|---|
|cluster-automatisch schalen|Kubernetes-cluster automatisch schalen|
|uitvoeren-apiserver|Kubernetes-API-server|
|uitvoeren-audit|Kubernetes-controle|
|uitvoeren-Controller-Manager|Kubernetes-controller beheer|
|uitvoeren-scheduler|Kubernetes scheduler|


## <a name="microsoftcustomprovidersresourceproviders"></a>Micro soft. CustomProviders/resourceproviders

|Categorie|Weergave naam categorie|
|---|---|
|Audit logs bevat|Controle logboeken voor MiniRP-aanroepen|


## <a name="microsoftdatabricksworkspaces"></a>Micro soft. Databricks/werk ruimten

|Categorie|Weergave naam categorie|
|---|---|
|accounts|Databricks-accounts|
|clusters|Databricks-clusters|
|dbfs|Databricks-bestandssysteem|
|instancePools|Instantie groepen|
|functies|Databricks-taken|
|notitieblok|Databricks Notebook|
|geheimen|Databricks geheimen|
|sqlPermissions|Databricks SQLPermissions|
|SSH|Databricks SSH|
|werkruimte|Databricks-werk ruimte|


## <a name="microsoftdatacatalogdatacatalogs"></a>Micro soft. DataCatalog/datacatalogs

|Categorie|Weergave naam categorie|
|---|---|
|ScanStatusLogEvent|ScanStatus|


## <a name="microsoftdatafactoryfactories"></a>Micro soft. DataFactory/fabrieken

|Categorie|Weergave naam categorie|
|---|---|
|ActivityRuns|Logboek voor uitvoering van pijplijn activiteit|
|PipelineRuns|Logboek voor uitvoering van pijp lijn|
|TriggerRuns|Trigger uitvoeringen logboek|


## <a name="microsoftdatalakestoreaccounts"></a>Micro soft. data Lake Store/accounts

|Categorie|Weergave naam categorie|
|---|---|
|Controleren|Auditlogboeken|
|Aanvragen|Logboeken aanvragen|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Categorie|Weergave naam categorie|
|---|---|
|MySqlAuditLogs|Controle logboeken voor MariaDB|
|MySqlSlowLogs|MariaDB-server logboeken|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Categorie|Weergave naam categorie|
|---|---|
|MySqlAuditLogs|MySQL-controle logboeken|
|MySqlSlowLogs|MySQL-server logboeken|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Categorie|Weergave naam categorie|
|---|---|
|PostgreSQLLogs|PostgreSQL-server logboeken|
|QueryStoreRuntimeStatistics|Runtime statistieken voor PostgreSQL query Store|
|QueryStoreWaitStatistics|Wacht statistieken voor PostgreSQL query Store|


## <a name="microsoftdbforpostgresqlserversv2"></a>Micro soft. DBforPostgreSQL/serversv2

|Categorie|Weergave naam categorie|
|---|---|
|PostgreSQLLogs|PostgreSQL-server logboeken|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Micro soft. DBforPostgreSQL/singleservers

|Categorie|Weergave naam categorie|
|---|---|
|PostgreSQLLogs|PostgreSQL-server logboeken|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Micro soft. DesktopVirtualization/applicationgroups

|Categorie|Weergave naam categorie|
|---|---|
|Controlepunt|Controlepunt|
|Fout|Fout|
|Beheer|Beheer|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Micro soft. DesktopVirtualization/hostpools

|Categorie|Weergave naam categorie|
|---|---|
|Controlepunt|Controlepunt|
|Verbinding|Verbinding|
|Fout|Fout|
|HostRegistration|HostRegistration|
|Beheer|Beheer|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Micro soft. DesktopVirtualization/werk ruimten

|Categorie|Weergave naam categorie|
|---|---|
|Controlepunt|Controlepunt|
|Fout|Fout|
|Feed|Feed|
|Beheer|Beheer|


## <a name="microsoftdevicesiothubs"></a>Micro soft. devices/IotHubs

|Categorie|Weergave naam categorie|
|---|---|
|C2DCommands|C2D-opdrachten|
|C2DTwinOperations|Dubbele C2D-bewerkingen|
|Configuraties|Configuraties|
|Verbindingen|Verbindingen|
|D2CTwinOperations|D2CTwinOperations|
|DeviceIdentityOperations|Bewerkingen voor apparaat-Id's|
|DeviceStreams|Apparaatversleuteling (preview-versie)|
|DeviceTelemetry|Apparaattelemetrie|
|DirectMethods|Directe methoden|
|DistributedTracing|Gedistribueerde tracering (preview-versie)|
|FileUploadOperations|Bewerkingen voor het uploaden van bestanden|
|JobsOperations|Taak bewerkingen|
|Routes|Routes|
|TwinQueries|Dubbele Query's|


## <a name="microsoftdevicesprovisioningservices"></a>Micro soft. devices/provisioningServices

|Categorie|Weergave naam categorie|
|---|---|
|DeviceOperations|Bewerkingen voor apparaten|
|ServiceOperations|Service bewerkingen|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Categorie|Weergave naam categorie|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsoftenterpriseknowledgegraphservices"></a>Micro soft. EnterpriseKnowledgeGraph/Services

|Categorie|Weergave naam categorie|
|---|---|
|Audit event|Audit event-logboek|
|DataIssue|DataIssue-logboek|
|Aanvragen|Configuratie logboek|

## <a name="microsofteventgriddomains"></a>Micro soft. EventGrid/domeinen

|Categorie|Weergave naam categorie|
|---|---|
|DeliveryFailures|Fout logboeken voor bezorging|
|PublishFailures|Fout logboeken publiceren|


## <a name="microsofteventgridsystemtopics"></a>Micro soft. EventGrid/systemTopics

|Categorie|Weergave naam categorie|
|---|---|
|DeliveryFailures|Fout logboeken voor bezorging|


## <a name="microsofteventgridtopics"></a>Micro soft. EventGrid/topics

|Categorie|Weergave naam categorie|
|---|---|
|DeliveryFailures|Fout logboeken voor bezorging|
|PublishFailures|Fout logboeken publiceren|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Categorie|Weergave naam categorie|
|---|---|
|ArchiveLogs|Archief logboeken|
|AutoScaleLogs|Logboeken automatisch schalen|
|CustomerManagedKeyUserLogs|Door de klant beheerde sleutel logboeken|
|EventHubVNetConnectionEvent|Logboeken voor VNet/IP-filtering verbindingen|
|KafkaCoordinatorLogs|Kafka Coordinator-logboeken|
|KafkaUserErrorLogs|Fout logboeken van Kafka-gebruikers|
|OperationalLogs|Operationele logboeken|


## <a name="microsofthealthcareapisservices"></a>Micro soft. HealthcareApis/Services

|Categorie|Weergave naam categorie|
|---|---|
|Audit logs bevat|Auditlogboeken|


## <a name="microsoftinsightsautoscalesettings"></a>Micro soft. Insights/AutoscaleSettings

|Categorie|Weergave naam categorie|
|---|---|
|AutoscaleEvaluations|Evaluaties automatisch schalen|
|AutoscaleScaleActions|Schaal acties automatisch schalen|


## <a name="microsoftinsightscomponents"></a>Micro soft. Insights/onderdelen

|Categorie|Weergave naam categorie|
|---|---|
|AppAvailabilityResults|Beschikbaarheids resultaten|
|AppBrowserTimings|Browser timing|
|AppDependencies|Afhankelijkheden|
|AppEvents|Gebeurtenissen|
|AppExceptions|Uitzonderingen|
|AppMetrics|Metrische gegevens|
|AppPageViews|Pagina weergaven|
|AppPerformanceCounters|Prestatiemeteritems|
|AppRequests|Aanvragen|
|AppSystemEvents|Systeem gebeurtenissen|
|AppTraces|Traceringen|


## <a name="microsoftiotspacesgraph"></a>Micro soft. IoTSpaces/Graph

|Categorie|Weergave naam categorie|
|---|---|
|Controleren|Controleren|
|Uitgaand verkeer|Uitgaand verkeer|
|Inkomend verkeer|Inkomend verkeer|
|Functioneren|Functioneren|
|Tracering|Tracering|
|UserDefinedFunction|UserDefinedFunction|


## <a name="microsoftkeyvaultvaults"></a>Micro soft.-sleutel kluis/-kluizen

|Categorie|Weergave naam categorie|
|---|---|
|Audit event|Auditlogboeken|


## <a name="microsoftkustoclusters"></a>Micro soft. Kusto/clusters

|Categorie|Weergave naam categorie|
|---|---|
|FailedIngestion|Mislukte opname bewerkingen|
|SucceededIngestion|Geslaagde opname bewerkingen|


## <a name="microsoftlogicintegrationaccounts"></a>Micro soft. Logic/integrationAccounts

|Categorie|Weergave naam categorie|
|---|---|
|IntegrationAccountTrackingEvents|Spoor gebeurtenissen voor integratie account|


## <a name="microsoftlogicworkflows"></a>Micro soft. Logic/werk stromen

|Categorie|Weergave naam categorie|
|---|---|
|WorkflowRuntime|Diagnostische gebeurtenissen voor workflowruntime|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Micro soft. MachineLearningServices/werk ruimten

|Categorie|Weergave naam categorie|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Micro soft. Media/Media Services

|Categorie|Weergave naam categorie|
|---|---|
|KeyDeliveryRequests|Aanvragen voor sleutel levering|


## <a name="microsoftnetworkapplicationgateways"></a>Micro soft. Network/applicationGateways

|Categorie|Weergave naam categorie|
|---|---|
|ApplicationGatewayAccessLog|Application Gateway Access-logboek|
|ApplicationGatewayFirewallLog|Application Gateway firewall-logboek|
|ApplicationGatewayPerformanceLog|Prestatie logboek Application Gateway|


## <a name="microsoftnetworkazurefirewalls"></a>Micro soft. Network/azurefirewalls

|Categorie|Weergave naam categorie|
|---|---|
|AzureFirewallApplicationRule|Toepassings regel Azure Firewall|
|AzureFirewallNetworkRule|Azure Firewall netwerk regel|


## <a name="microsoftnetworkbastionhosts"></a>Micro soft. Network/bastionHosts

|Categorie|Weergave naam categorie|
|---|---|
|BastionAuditLogs|Controle logboeken voor Bastion|


## <a name="microsoftnetworkexpressroutecircuits"></a>Micro soft. Network/expressRouteCircuits

|Categorie|Weergave naam categorie|
|---|---|
|PeeringRouteLog|Logboeken voor peering route tabel|


## <a name="microsoftnetworkfrontdoors"></a>Micro soft. Network/frontdoors

|Categorie|Weergave naam categorie|
|---|---|
|FrontdoorAccessLog|-Ingang-toegangs logboek|
|FrontdoorWebApplicationFirewallLog|-Ingang Web Application firewall-logboek|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Categorie|Weergave naam categorie|
|---|---|
|LoadBalancerAlertEvent|Load Balancer waarschuwings gebeurtenissen|
|LoadBalancerProbeHealthStatus|Load Balancer test status|


## <a name="microsoftnetworknetworksecuritygroups"></a>Micro soft. Network/networksecuritygroups

|Categorie|Weergave naam categorie|
|---|---|
|NetworkSecurityGroupEvent|Gebeurtenis netwerk beveiligings groep|
|NetworkSecurityGroupFlowEvent|Stroom gebeurtenis van regel voor netwerk beveiligings groep|
|NetworkSecurityGroupRuleCounter|Teller van regel voor netwerk beveiligings groep|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Categorie|Weergave naam categorie|
|---|---|
|DDoSMitigationFlowLogs|Stroom logboeken van DDoS-oplossings beslissingen|
|DDoSMitigationReports|Rapporten met DDoS-oplossingen|
|DDoSProtectionNotifications|DDoS-beveiligings meldingen|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Micro soft. Network/trafficManagerProfiles

|Categorie|Weergave naam categorie|
|---|---|
|ProbeHealthStatusEvents|Gebeurtenis met resultaten van Traffic Manager test status|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Categorie|Weergave naam categorie|
|---|---|
|GatewayDiagnosticLog|Diagnostische logboeken van de gateway|
|IKEDiagnosticLog|Diagnostische logboeken voor IKE|
|P2SDiagnosticLog|Diagnostische logboeken van P2S|
|RouteDiagnosticLog|Diagnostische logboeken voor route ring|
|TunnelDiagnosticLog|Diagnostische logboeken voor tunnel|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Categorie|Weergave naam categorie|
|---|---|
|VMProtectionAlerts|Waarschuwingen voor VM-beveiliging|


## <a name="microsoftpowerbidedicatedcapacities"></a>Micro soft. PowerBIDedicated/capaciteiten

|Categorie|Weergave naam categorie|
|---|---|
|Engine|Engine|


## <a name="microsoftrecoveryservicesvaults"></a>Micro soft. Recovery Services/kluizen

|Categorie|Weergave naam categorie|
|---|---|
|AddonAzureBackupAlerts|Azure Backup waarschuwings gegevens van invoeg toepassing|
|AddonAzureBackupJobs|Taak gegevens van invoeg toepassing Azure Backup|
|AddonAzureBackupPolicy|Azure Backup beleids gegevens van invoeg toepassing|
|AddonAzureBackupProtectedInstance|Invoeg toepassing Azure Backup beveiligde instantie gegevens|
|AddonAzureBackupStorage|Invoeg Azure Backup opslag gegevens|
|AzureBackupReport|Azure Backup rapportage gegevens|
|AzureSiteRecoveryEvents|Azure Site Recovery gebeurtenissen|
|AzureSiteRecoveryJobs|Azure Site Recovery taken|
|AzureSiteRecoveryProtectedDiskDataChurn|Gegevens verloop van beveiligde schijf Azure Site Recovery|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery herstel punten|
|AzureSiteRecoveryReplicatedItems|Gerepliceerde items Azure Site Recovery|
|AzureSiteRecoveryReplicationDataUploadRate|Upload frequentie van Azure Site Recovery replicatie gegevens|
|AzureSiteRecoveryReplicationStats|Replicatie statistieken Azure Site Recovery|
|CoreAzureBackup|Kern Azure Backup gegevens|


## <a name="microsoftrelaynamespaces"></a>Micro soft. relay/naam ruimten

|Categorie|Weergave naam categorie|
|---|---|
|HybridConnectionsEvent|HybridConnections-gebeurtenissen|


## <a name="microsoftsearchsearchservices"></a>Micro soft. Search/searchServices

|Categorie|Weergave naam categorie|
|---|---|
|OperationLogs|Bewerkings logboeken|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Categorie|Weergave naam categorie|
|---|---|
|OperationalLogs|Operationele logboeken|


## <a name="microsoftsignalrservicesignalr"></a>Micro soft. SignalRService/Signa lering

|Categorie|Weergave naam categorie|
|---|---|
|AllLogs|Logboeken van de Azure signalerings service.|


## <a name="microsoftsqlmanagedinstances"></a>Micro soft. SQL/managedInstances

|Categorie|Weergave naam categorie|
|---|---|
|DevOpsOperationsAudit|Audit logboeken voor Devops-bewerkingen|
|ResourceUsageStats|Resource gebruiks statistieken|
|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Micro soft. SQL/managedInstances/data bases

|Categorie|Weergave naam categorie|
|---|---|
|Errors|Errors|
|QueryStoreRuntimeStatistics|Runtime statistieken voor query Store|
|QueryStoreWaitStatistics|Wacht statistieken voor query Store|
|SQLInsights|SQL Insights|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Categorie|Weergave naam categorie|
|---|---|
|AutomaticTuning|Automatisch instellen|
|Block|Block|
|DatabaseWaitStatistics|Data base-wacht statistieken|
|Impassen|Impassen|
|DevOpsOperationsAudit|Audit logboeken voor Devops-bewerkingen|
|DmsWorkers|DMS-werk rollen|
|Errors|Errors|
|ExecRequests|Exec-aanvragen|
|QueryStoreRuntimeStatistics|Runtime statistieken voor query Store|
|QueryStoreWaitStatistics|Wacht statistieken voor query Store|
|RequestSteps|Stappen voor aanvragen|
|SQLInsights|SQL Insights|
|SqlRequests|SQL-aanvragen|
|SQLSecurityAuditEvents|SQL-beveiligings controle gebeurtenis|
|Time-outs|Time-outs|
|Wacht|Wacht|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Micro soft. Storage/Storage accounts/blobServices

|Categorie|Weergave naam categorie|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Micro soft. Storage/Storage accounts/fileServices

|Categorie|Weergave naam categorie|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Micro soft. Storage/Storage accounts/queueServices

|Categorie|Weergave naam categorie|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Micro soft. Storage/Storage accounts/tableServices

|Categorie|Weergave naam categorie|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Micro soft. StreamAnalytics/streamingjobs

|Categorie|Weergave naam categorie|
|---|---|
|Ontwerpen|Ontwerpen|
|Uitvoering|Uitvoering|


## <a name="microsoftwebhostingenvironments"></a>micro soft. Web/hostingenvironments

|Categorie|Weergave naam categorie|
|---|---|
|AppServiceEnvironmentPlatformLogs|App Service Environment-platform logboeken|


## <a name="microsoftwebsites"></a>micro soft. web/sites

|Categorie|Weergave naam categorie|
|---|---|
|AppServiceAppLogs|Toepassings logboeken App Service|
|AppServiceAuditLogs|Access-controle logboeken|
|AppServiceConsoleLogs|App Service-console logboeken|
|AppServiceFileAuditLogs|Controle logboeken voor het wijzigen van de site-inhoud|
|AppServiceHTTPLogs|HTTP-logboeken|
|FunctionAppLogs|Toepassings logboeken van functie|
|ScanLogs|Virus scan logboeken|


## <a name="microsoftwebsitesslots"></a>micro soft. web/sites/sleuven


|Categorie|Weergave naam categorie|
|---|---|
|AppServiceAppLogs|Toepassings logboeken App Service|
|AppServiceAuditLogs|Access-controle logboeken|
|AppServiceConsoleLogs|App Service-console logboeken|
|AppServiceFileAuditLogs|Controle logboeken voor het wijzigen van de site-inhoud|
|AppServiceHTTPLogs|HTTP-logboeken|
|FunctionAppLogs|Toepassings logboeken van functie|
|ScanLogs|Virus scan logboeken|


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over bron logboeken](./platform-logs-overview.md)
* [Resource bron logboeken streamen naar **Event hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Diagnostische instellingen voor bron logboek wijzigen met behulp van de Azure Monitor REST API](/rest/api/monitor/diagnosticsettings)
* [Logboeken analyseren vanuit Azure Storage met Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

