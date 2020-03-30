---
title: Ondersteunde services en schema's voor Azure Resource Logs
description: Inzicht in het programma voor ondersteunde services en gebeurtenissen voor Azure-bronlogboeken.
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: de102c5dc4104aafc44b87b14aeea0b30cb7c083
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248812"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Ondersteunde services, schema's en categorieën voor Azure Resource Logs

> [!NOTE]
> Bronlogboeken stonden voorheen bekend als diagnostische logboeken.

[Azure Monitor-bronlogboeken](../../azure-monitor/platform/platform-logs-overview.md) zijn logboeken die worden uitgestoten door Azure-services die de werking van deze services of resources beschrijven. Alle bronlogboeken die beschikbaar zijn via Azure Monitor delen een algemeen schema op het hoogste niveau, met flexibiliteit voor elke service om unieke eigenschappen voor hun eigen gebeurtenissen uit te zenden.

Een combinatie van het resourcetype `resourceId` (beschikbaar `category` in de eigenschap) en het unieke identificeren van een schema. In dit artikel wordt het schema op het hoogste niveau beschreven voor bronlogboeken en koppelingen naar de schemata voor elke service.

## <a name="top-level-resource-logs-schema"></a>Schema resourcelogboeken op het hoogste niveau

| Name | Vereist/optioneel | Beschrijving |
|---|---|---|
| tijd | Vereist | De tijdstempel (UTC) van de gebeurtenis. |
| resourceId | Vereist | De resource-id van de resource die de gebeurtenis heeft uitgezonden. Voor tenantservices is dit van het formulier /tenants/tenant-id/providers/provider-name. |
| tenantId | Vereist voor tenantlogboeken | De tenant-id van de Active Directory-tenant waaraan deze gebeurtenis is gekoppeld. Deze eigenschap wordt alleen gebruikt voor logboeken op tenantniveau, deze wordt niet weergegeven in logboeken op resourceniveau. |
| operationName | Vereist | De naam van de bewerking die door deze gebeurtenis wordt vertegenwoordigd. Als de gebeurtenis een RBAC-bewerking vertegenwoordigt, is dit de naam rbac-bewerking (bijv. Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Meestal gemodelleerd in de vorm van een Resource Manager-bewerking, zelfs als`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`het niet de werkelijke gedocumenteerde Resource Manager-bewerkingen zijn ( ) |
| operationVersion | Optioneel | De api-versie die aan de bewerking is gekoppeld, als de operationName is uitgevoerd met behulp van een API (bijv. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Als er geen API is die overeenkomt met deze bewerking, vertegenwoordigt de versie de versie van die bewerking voor het geval de eigenschappen die zijn gekoppeld aan de bewerking in de toekomst worden gewijzigd. |
| category | Vereist | De logcategorie van het evenement. Categorie is de granulariteit waarop u logboeken op een bepaalde resource in- of uitschakelen. De eigenschappen die worden weergegeven in de eigenschappenblob van een gebeurtenis zijn hetzelfde binnen een bepaalde logboekcategorie en resourcetype. Typische logcategorieën zijn 'Audit' 'Operationeel' 'Uitvoering' en 'Aanvragen'. |
| resultType | Optioneel | De status van het evenement. Typische waarden zijn Gestart, In uitvoering, Geslaagd, Mislukt, Actief en Opgelost. |
| resultSignature | Optioneel | De substatus van de gebeurtenis. Als deze bewerking overeenkomt met een REST API-aanroep, is dit de HTTP-statuscode van de bijbehorende REST-aanroep. |
| resultDescription | Optioneel | De statische tekstbeschrijving van deze bewerking, bijv. "Haal opslagbestand." |
| durationMs | Optioneel | De duur van de bewerking in milliseconden. |
| callerIpAddress | Optioneel | Het IP-adres van de beller, als de bewerking overeenkomt met een API-aanroep die afkomstig zou zijn van een entiteit met een openbaar beschikbaar IP-adres. |
| correlationId | Optioneel | Een GUID wordt gebruikt om een reeks gerelateerde gebeurtenissen samen te voegen. Als twee gebeurtenissen meestal dezelfde operationName hebben, maar twee verschillende statussen (bijv. 'Gestart' en 'Geslaagd', ze delen dezelfde correlatie-ID. Dit kan ook andere relaties tussen gebeurtenissen vertegenwoordigen. |
| identity | Optioneel | Een JSON-blob die de identiteit beschrijft van de gebruiker of toepassing die de bewerking heeft uitgevoerd. Meestal omvat dit de autorisatie en claims / JWT-token van active directory. |
| Niveau | Optioneel | Het ernstniveau van de gebeurtenis. Moet een van informatief, waarschuwing, fout of kritiek zijn. |
| location | Optioneel | De regio van de bron die de gebeurtenis uitzendt, bijv. "Oost-VS" of "Frankrijk Zuid" |
| properties | Optioneel | Uitgebreide eigenschappen met betrekking tot deze specifieke categorie gebeurtenissen. Alle aangepaste/unieke eigenschappen moeten in dit "Deel B" van het schema worden geplaatst. |

## <a name="service-specific-schemas-for-resource-logs"></a>Servicespecifieke schema's voor resourcelogboeken
Het schema voor diagnostische logboeken voor bronnen is afhankelijk van de resource- en logboekcategorie. In deze lijst worden alle services weergegeven die bronlogboeken en koppelingen beschikbaar stellen aan de service en categoriespecifiek schema, indien beschikbaar.

| Service | Schema & Documenten |
| --- | --- |
| Azure Active Directory | [Overzicht](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [Controlelogboekschema](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) en [Aanmeldingsschema](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API-beheerbronlogboeken](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Toepassingsgateways |[Logboekregistratie voor toepassingsgateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Logboekanalyses voor Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch-logboekregistratie](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database voor Diagnostische logboeken van MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database voor PostgreSQL-logboeken](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure Data Explorer | [Azure Data Explorer-logboeken](../../data-explorer/using-diagnostic-logs.md) |
| Cognitive Services | [Logboekregistratie voor Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Logboekregistratie voor Azure Container Registry](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| CDN (Content Delivery Network) | [Azure-logboeken voor CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB-logboekregistratie](../../cosmos-db/logging.md) |
| Data Factory | [Gegevensfabrieken bewaken met Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Logboeken openen voor Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Logboeken openen voor Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure Event Hubs-logboeken](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schema niet beschikbaar. |
| Azure Firewall | Schema niet beschikbaar. |
| IoT Hub | [IoT Hub-bewerkingen](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Logboekregistratie van Azure Key Vault](../../key-vault/key-vault-logging.md) |
| Kubernetes Service |[Azure Kubernetes-logboekregistratie](../../aks/view-master-logs.md#log-event-schema) |
| Load balancer |[Logboekanalyse voor Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Aangepast Logic Apps B2B-volgschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Netwerkbeveiligingsgroepen |[Logboekanalyses voor netwerkbeveiligingsgroepen (NSG's)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS Protection | [Azure DDoS-beveiligingsstandaard beheren](../../virtual-network/manage-ddos-protection.md) |
| Power BI Dedicated | [Logboekregistratie voor Power BI-ingesloten in Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Gegevensmodel voor Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Search |[Search Traffic Analytics inschakelen en gebruiken](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus-logboeken](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL-databaselogboekregistratie](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Taaklogboeken](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Logboekschema verkeersbeheerbeheer](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuele netwerken | Schema niet beschikbaar. |
| Virtuele netwerkgateways | Schema niet beschikbaar. |

## <a name="supported-log-categories-per-resource-type"></a>Ondersteunde logboekcategorieën per resourcetype

Sommige categorieën kunnen alleen worden ondersteund voor specifieke soorten resources. Dit is een lijst van alles wat beschikbaar zijn in een bepaalde vorm.  Microsoft.Sql/servers/databases categorieën zijn bijvoorbeeld niet beschikbaar voor alle soorten databases. Zie [informatie over diagnostische logboekregistratie van SQL Database](../../sql-database/sql-database-metrics-diag-logging.md)voor meer informatie. 

|Resourcetype|Categorie|Weergavenaam categorie|
|---|---|---|
|Microsoft.AAD/domainServices|Systeembeveiliging|Systeembeveiliging|
|Microsoft.AAD/domainServices|AccountManagement AccountManagement|AccountManagement AccountManagement|
|Microsoft.AAD/domainServices|Aanmeldingslogboeken|Aanmeldingslogboeken|
|Microsoft.AAD/domainServices|ObjectAccess|ObjectAccess|
|Microsoft.AAD/domainServices|Beleidswijziging|Beleidswijziging|
|Microsoft.AAD/domainServices|PrivilegeGebruik|PrivilegeGebruik|
|Microsoft.AAD/domainServices|Detailtracking|Detailtracking|
|Microsoft.AAD/domainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Microsoft.AAD/domainServices|AccountLogon|AccountLogon|
|microsoft.aadiam/huurders|Aanmelden|Aanmelden|
|Microsoft.AnalysisServices/servers|Engine|Engine|
|Microsoft.AnalysisServices/servers|Service|Service|
|Microsoft.ApiManagement/service|Gatewaylogs|Logboeken met betrekking tot ApiManagement Gateway|
|Microsoft.AppPlatform/Spring|ApplicationConsole|Toepassingsconsole|
|Microsoft.Automation/automationAccounts|JobLogs JobLogs|Taaklogboeken|
|Microsoft.Automation/automationAccounts|JobStreams|Taakstromen|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Dsc-knooppuntstatus|
|Microsoft.Batch/batchAccounts|ServiceLog (ServiceLog)|Servicelogboeken|
|Microsoft.BatchAI/werkruimten|BaiClusterEvent (BaiClusterEvent)|BaiClusterEvent (BaiClusterEvent)|
|Microsoft.BatchAI/werkruimten|BaiClusterNodeEvent BaiClusterNodeEvent|BaiClusterNodeEvent BaiClusterNodeEvent|
|Microsoft.BatchAI/werkruimten|BaiJobEvent BaiJobEvent|BaiJobEvent BaiJobEvent|
|Microsoft.Blockchain/blockchainLeden|BlockchainToepassing|Blockchain-toepassing|
|Microsoft.Blockchain/blockchainLeden|Proxy|Proxy|
|Microsoft.Cdn/profielen/eindpunten|CoreAnalytics (CoreAnalytics)|Krijgt de statistieken van het eindpunt, bijvoorbeeld bandbreedte, uitgang, enz.|
|Microsoft.ClassicNetwork/networksecuritygroups|Regelstroom van netwerkbeveiligingsgroep|Regelstroom van netwerkbeveiligingsgroep|
|Microsoft.CognitiveServices/accounts|Controleren|Auditlogboeken|
|Microsoft.CognitiveServices/accounts|Reactie op aanvragen|Logboeken voor aanvragen en antwoorden|
|Microsoft.ContainerRegistry/registers|ContainerRegistryRepositoryEvents|RepositoryEvent-logboeken (voorbeeld)|
|Microsoft.ContainerRegistry/registers|ContainerRegistryLoginEvents|Aanmeldingsgebeurtenissen (voorbeeld)|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Kubernetes API-server|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Kubernetes Controller Manager|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Kubernetes Scheduler|
|Microsoft.ContainerService/managedClusters|kube-audit|Kubernetes-audit|
|Microsoft.ContainerService/managedClusters|clusterautoscaler|Kubernetes Cluster Autoscaler|
|Microsoft.Databricks/werkruimten|dbfs dbfs|Databricks-bestandssysteem|
|Microsoft.Databricks/werkruimten|Clusters|Databricks-clusters|
|Microsoft.Databricks/werkruimten|accounts|Databricks-accounts|
|Microsoft.Databricks/werkruimten|Banen|Databricks-taken|
|Microsoft.Databricks/werkruimten|Notebook|Databricks Notebook|
|Microsoft.Databricks/werkruimten|Ssh|Databricks SSH|
|Microsoft.Databricks/werkruimten|werkruimte|Databricks-werkruimte|
|Microsoft.Databricks/werkruimten|geheimen|Databricks Geheimen|
|Microsoft.Databricks/werkruimten|sqlPermissions|Gegevensbricks SQLPermissions|
|Microsoft.Databricks/werkruimten|instantiePools|Instantiegroepen|
|Microsoft.DataCatalog/datacatalogi|ScanStatusLogEvent|Scanstatus|
|Microsoft.DataFactory/fabrieken|ActivityRuns ActivityRuns ActivityRuns ActivityRuns|Pijplijnactiviteit voert logboek uit|
|Microsoft.DataFactory/fabrieken|Pijplijnruns|Logboek met pijplijnuitvoeringen|
|Microsoft.DataFactory/fabrieken|TriggerRuns|Trigger voert logboek uit|
|Microsoft.DataLakeAnalytics/accounts|Controleren|Auditlogboeken|
|Microsoft.DataLakeAnalytics/accounts|Aanvragen|Logboeken aanvragen|
|Microsoft.DataLakeStore/accounts|Controleren|Auditlogboeken|
|Microsoft.DataLakeStore/accounts|Aanvragen|Logboeken aanvragen|
|Microsoft.DataShare/accounts|Shares|Shares|
|Microsoft.DataShare/accounts|Abonnementen delen|Abonnementen delen|
|Microsoft.DataShare/accounts|SentShareSnapshots|Verzonden momentopnamen delen|
|Microsoft.DataShare/accounts|ReceivedShareSnapshots|Momentopnamen voor ontvangen delen|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|MySQL Server-logboeken|
|Microsoft.DBforMySQL/servers|MySqlAuditLogs|MySQL-controlelogboeken|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL-serverlogboeken|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistieken|Runtime-statistieken van PostgreSQL-queryopslag|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistieken|Wachtstatistieken van PostgreSQL-queryarchief|
|Microsoft.DBforPostgreSQL/serversv2|PostgreSQLLogs|PostgreSQL-serverlogboeken|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistieken|Runtime-statistieken van PostgreSQL-queryopslag|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreWaitStatistieken|Wachtstatistieken van PostgreSQL-queryarchief|
|Microsoft.DesktopVirtualisatie/werkruimten|Controlepunt|Controlepunt|
|Microsoft.DesktopVirtualisatie/werkruimten|Fout|Fout|
|Microsoft.DesktopVirtualisatie/werkruimten|Beheer|Beheer|
|Microsoft.DesktopVirtualisatie/werkruimten|Feed|Feed|
|Microsoft.DesktopVirtualization/applicationGroups|Controlepunt|Controlepunt|
|Microsoft.DesktopVirtualization/applicationGroups|Fout|Fout|
|Microsoft.DesktopVirtualization/applicationGroups|Beheer|Beheer|
|Microsoft.DesktopVirtualization/hostPools|Controlepunt|Controlepunt|
|Microsoft.DesktopVirtualization/hostPools|Fout|Fout|
|Microsoft.DesktopVirtualization/hostPools|Beheer|Beheer|
|Microsoft.DesktopVirtualization/hostPools|Verbinding|Verbinding|
|Microsoft.DesktopVirtualization/hostPools|HostRegistratie|HostRegistratie|
|Microsoft.Devices/IotHubs|Verbindingen|Verbindingen|
|Microsoft.Devices/IotHubs|ApparaatTelemetrie|Apparaattelemetrie|
|Microsoft.Devices/IotHubs|C2D-opdrachten|C2D-opdrachten|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations DeviceIdentityOperations|Apparaatidentiteitsbewerkingen|
|Microsoft.Devices/IotHubs|Bestandsuploadbewerkingen|Bestandsuploadbewerkingen|
|Microsoft.Devices/IotHubs|Routes|Routes|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D Twin Operations|
|Microsoft.Devices/IotHubs|TwinQueries|Dubbele query's|
|Microsoft.Devices/IotHubs|Vacatures|Taken|
|Microsoft.Devices/IotHubs|DirectMethods|Directe methoden|
|Microsoft.Devices/IotHubs|GedistribueerdTraceering|Gedistribueerde tracering (voorbeeld)|
|Microsoft.Devices/IotHubs|Configuraties|Configuraties|
|Microsoft.Devices/IotHubs|Apparaatstreams|Apparaatstreams (voorbeeld)|
|Microsoft.Devices/provisioningServices|Apparaatbewerkingen|Apparaatbewerkingen|
|Microsoft.Devices/provisioningServices|ServiceBewerkingen|Servicebewerkingen|
|Microsoft.DocumentDB/databaseAccounts|Gegevensvliegtuigverzoeken|Gegevensvliegtuigverzoeken|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistieken|QueryRuntimeStatistieken|
|Microsoft.DocumentDB/databaseAccounts|PartitionKeyStatistieken|PartitionKeyStatistieken|
|Microsoft.DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|Microsoft.EnterpriseKnowledgeGraph/services|AuditEvent|Logboek AuditEvent|
|Microsoft.EnterpriseKnowledgeGraph/services|Gegevensprobleem|Logboek Gegevensuitgifte|
|Microsoft.EnterpriseKnowledgeGraph/services|Aanvragen|Configuratielogboek|
|Microsoft.EventHub/naamruimten|Archieflogboeken|Archieflogboeken|
|Microsoft.EventHub/naamruimten|OperationalLogs (OperationalLogs)|Operationele logboeken|
|Microsoft.EventHub/naamruimten|AutoScaleLogs|Logboeken automatisch schalen|
|Microsoft.EventHub/naamruimten|KafkaCoordinatorLogs|Kafka Coördinator Logs|
|Microsoft.EventHub/naamruimten|KafkaUserErrorLogs|Meldingslogboeken van Kafka-gebruiker|
|Microsoft.EventHub/naamruimten|EventHubVNetConnectionEvent|VNet/IP-filterverbindingen|
|Microsoft.EventHub/naamruimten|CustomerManagedKeyUserLogs|Door de klant beheerde sleutellogboeken|
|Microsoft.HealthcareApis/services|Controlelogboeken|Auditlogboeken|
|Microsoft.Insights/Instellingen voor automatisch schalen|AutoscaleEvaluaties|Evaluaties van autoschaal|
|Microsoft.Insights/Instellingen voor automatisch schalen|Acties voor automatisch schalen|Acties voor schaalautomatisch|
|Microsoft.ioTSpaces/Graph|Tracering|Tracering|
|Microsoft.ioTSpaces/Graph|Operationele|Operationele|
|Microsoft.ioTSpaces/Graph|Controleren|Controleren|
|Microsoft.ioTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.ioTSpaces/Graph|Inkomend verkeer|Inkomend verkeer|
|Microsoft.ioTSpaces/Graph|Uitgaand verkeer|Uitgaand verkeer|
|Microsoft.KeyVault/kluizen|AuditEvent|Auditlogboeken|
|Microsoft.Kusto/Clusters|SucceededIngestion|Succesvolle inname operaties|
|Microsoft.Kusto/Clusters|FailedIngestion|Mislukte innamebewerkingen|
|Microsoft.Logic/werkstromen|WerkstroomRuntime|Diagnostische gebeurtenissen voor werkstroomruntime|
|Microsoft.Logic/integrationAccounts|IntegratieAccounttrackingEvents|Integratieaccount bijhouden gebeurtenissen|
|Microsoft.MachineLearningServices/werkruimten|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft.MachineLearningServices/werkruimten|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft.MachineLearningServices/werkruimten|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft.Media/mediaservices|KeyDeliveryRequests|Belangrijkste leveringsverzoeken|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Gebeurtenis netwerkbeveiligingsgroep|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Regelteller netwerkbeveiliginggroep|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Regelstroom van netwerkbeveiligingsgroep|
|Microsoft.Network/publicIPAddresses|DDoSProtectionMeldingen|DDoS-beveiligingsmeldingen|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Stroomlogboeken van DDoS-mitigatiebeslissingen|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Rapporten van DDoS-oplossingen|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM-beveiligingswaarschuwingen|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Toepassingsgatewaytoegangslogboek|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Prestatielogboek toepassingsgateway|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Toepassingsgatewayfirewalllogboek|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Azure Firewall-toepassingsregel|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Azure Firewall-netwerkregel|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Diagnostische logboeken voor gateway's|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Logboeken voor tunneldiagnose|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnoslog|Diagnostische logboeken routeren|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE Diagnostische logboeken|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2S Diagnostische logboeken|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Resultaten van de statusvan de verkeersmanager probe, gebeurtenis|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Logboeken van routelogboeken voor peering|
|Microsoft.Network/vpnGateways|GatewayDiagnosticLog|Diagnostische logboeken voor gateway's|
|Microsoft.Network/vpnGateways|TunnelDiagnosticLog|Logboeken voor tunneldiagnose|
|Microsoft.Network/vpnGateways|RouteDiagnoslog|Diagnostische logboeken routeren|
|Microsoft.Network/vpnGateways|IKEDiagnosticLog|IKE Diagnostische logboeken|
|Microsoft.Network/voordeuren|FrontdoorAccessLog|Frontdoor Access-logboek|
|Microsoft.Network/voordeuren|FrontdoorWebApplicationFirewallLog|Frontdoor-firewalllogboek voor webtoepassingen|
|Microsoft.Network/p2sVpnGateways|GatewayDiagnosticLog|Diagnostische logboeken voor gateway's|
|Microsoft.Network/p2sVpnGateways|IKEDiagnosticLog|IKE Diagnostische logboeken|
|Microsoft.Network/p2sVpnGateways|P2SDiagnosticLog|P2S Diagnostische logboeken|
|Microsoft.Network/bastionHosts|BastionAuditLogs|Bastion-controlelogboeken|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Waarschuwingsgebeurtenissen load balancer|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Status van load Balancer Probe|
|Microsoft.PowerBIDedicated/capaciteiten|Engine|Engine|
|Microsoft.RecoveryServices/Kluizen|AzureBackupReport|Rapportagegegevens van Azure Backup|
|Microsoft.RecoveryServices/Kluizen|CoreAzureBackup|Kernazure-back-upgegevens|
|Microsoft.RecoveryServices/Kluizen|AddonAzureBackupJobs|Addon Azure Backup-taakgegevens|
|Microsoft.RecoveryServices/Kluizen|AddonAzureBackupAlerts|Azure-waarschuwingsgegevens voor back-ups toevoegen|
|Microsoft.RecoveryServices/Kluizen|AddonAzureBackupPolicy|Addon Azure Backup Policy Data|
|Microsoft.RecoveryServices/Kluizen|AddonAzureBackupStorage|Opslaggegevens van Azure Backup Storage toevoegen|
|Microsoft.RecoveryServices/Kluizen|AddonAzureBackupProtectedInstance|Addon Azure Backup Protected Instance-gegevens|
|Microsoft.RecoveryServices/Kluizen|AzureSiteRecoveryTaken|Azure-sitehersteltaken|
|Microsoft.RecoveryServices/Kluizen|AzureSiteRecoverygebeurtenissen|Gebeurtenissen voor azure-siteherstel|
|Microsoft.RecoveryServices/Kluizen|AzureSiteRecovery-gerepliceerde items|Gerepliceerde items voor Azure-siteherstel|
|Microsoft.RecoveryServices/Kluizen|AzureSiteRecoveryReplicatiestatistieken|Replicatiestatistieken azure-siteherstel|
|Microsoft.RecoveryServices/Kluizen|AzureSiteRecoveryPoints|Herstelpunten voor Azure-siteherstel|
|Microsoft.RecoveryServices/Kluizen|AzureSiteRecoveryReplicatieDataUploadRate|Uploadsnelheid voor azure-siteherstelreplicatie|
|Microsoft.RecoveryServices/Kluizen|AzureSiteRecoveryProtectedDiskDataChurn|Azure-siteherstelbeveiligde schijfgegevensverloop|
|Microsoft.Search/searchServices|OperationLogs (OperationLogs)|Bewerkingslogboeken|
|Microsoft.ServiceBus/naamruimten|OperationalLogs (OperationalLogs)|Operationele logboeken|
|Microsoft.Sql/servers/databases|SQLInsights|SQL-inzichten|
|Microsoft.Sql/servers/databases|Automatisch afstemmen|Automatisch instellen|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistieken|Runtime-statistieken voor queryopslag|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistieken|Wachtstatistieken van queryopslag|
|Microsoft.Sql/servers/databases|Fouten|Fouten|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistieken|Database wachtstatistieken|
|Microsoft.Sql/servers/databases|Time-outs|Time-outs|
|Microsoft.Sql/servers/databases|Blokken|Blokken|
|Microsoft.Sql/servers/databases|Deadlocks|Deadlocks|
|Microsoft.Sql/servers/databases|Controleren|Auditlogboeken|
|Microsoft.Sql/servers/databases|SQLSecurityAuditGebeurtenissen|SQL Security Audit-gebeurtenis|
|Microsoft.Sql/servers/databases|DmsWorkers DmsWorkers|Dms Werknemers|
|Microsoft.Sql/servers/databases|ExecRequests|Exec-verzoeken|
|Microsoft.Sql/servers/databases|Stappen aanvragen|Stappen aanvragen|
|Microsoft.Sql/servers/databases|SqlRequests|Sql-aanvragen|
|Microsoft.Sql/servers/databases|Wacht|Wacht|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Statistieken over resourcegebruik|
|Microsoft.Sql/managedInstances|SQLSecurityAuditGebeurtenissen|SQL Security Audit-gebeurtenis|
|Microsoft.Sql/managedInstances/databases|SQLInsights|SQL-inzichten|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistieken|Runtime-statistieken voor queryopslag|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistieken|Wachtstatistieken van queryopslag|
|Microsoft.Sql/managedInstances/databases|Fouten|Fouten|
|Microsoft.Storage/storageAccounts/tableServices|StorageRead (StorageRead)|StorageRead (StorageRead)|
|Microsoft.Storage/storageAccounts/tableServices|StorageWrite (StorageWrite)|StorageWrite (StorageWrite)|
|Microsoft.Storage/storageAccounts/tableServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/blobServices|StorageRead (StorageRead)|StorageRead (StorageRead)|
|Microsoft.Storage/storageAccounts/blobServices|StorageWrite (StorageWrite)|StorageWrite (StorageWrite)|
|Microsoft.Storage/storageAccounts/blobServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/fileServices|StorageRead (StorageRead)|StorageRead (StorageRead)|
|Microsoft.Storage/storageAccounts/fileServices|StorageWrite (StorageWrite)|StorageWrite (StorageWrite)|
|Microsoft.Storage/storageAccounts/fileServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/queueServices|StorageRead (StorageRead)|StorageRead (StorageRead)|
|Microsoft.Storage/storageAccounts/queueServices|StorageWrite (StorageWrite)|StorageWrite (StorageWrite)|
|Microsoft.Storage/storageAccounts/queueServices|StorageDelete|StorageDelete|
|Microsoft.StreamAnalytics/streamingjobs|Uitvoering|Uitvoering|
|Microsoft.StreamAnalytics/streamingjobs|Ontwerpen|Ontwerpen|
|microsoft.web/hostingomgevingen|AppServiceEnvironmentPlatformLogs|Logboeken van het App-serviceomgevingplatform|
|microsoft.web/sites|FunctieAppLogs|Functietoepassingslogboeken|
|microsoft.web/sites|AppServiceHTTPLogs|HTTP-logboeken|
|microsoft.web/sites|AppServiceConsoleLogs|Logboeken van app-serviceconsole|
|microsoft.web/sites|AppServiceAppLogs AppServiceAppLogs|App-servicetoepassingslogboeken|
|microsoft.web/sites|AppServiceFileAuditLogs|Controlelogboeken voor site-inhoud wijzigen|
|microsoft.web/sites|AppServiceAuditLogs|Controlelogboeken openen|
|microsoft.web/sites/sleuven|FunctieAppLogs|Functietoepassingslogboeken|
|microsoft.web/sites/sleuven|AppServiceHTTPLogs|HTTP-logboeken|
|microsoft.web/sites/sleuven|AppServiceConsoleLogs|Consolelogboeken|
|microsoft.web/sites/sleuven|AppServiceAppLogs AppServiceAppLogs|Toepassingslogboeken|
|microsoft.web/sites/sleuven|AppServiceFileAuditLogs|Controlelogboeken voor site-inhoud wijzigen|
|microsoft.web/sites/sleuven|AppServiceAuditLogs|Controlelogboeken openen|

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over bronlogboeken](../../azure-monitor/platform/platform-logs-overview.md)
* [Bronbronlogboeken streamen naar **gebeurtenishubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Diagnostische instellingen voor bronlogboeken wijzigen met de Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Logboeken analyseren vanuit Azure-opslag met Logboekanalyse](../../azure-monitor/platform/collect-azure-metrics-logs.md)
