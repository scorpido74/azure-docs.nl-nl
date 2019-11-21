---
title: Tag support for resources
description: Shows which Azure resource types support tags. Provides details for all Azure services.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 3a6fae9e0f85f7767b8d2d0b7f1364dbd83a81db
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230254"
---
# <a name="tag-support-for-azure-resources"></a>Tag support for Azure resources
This article describes whether a resource type supports [tags](resource-group-using-tags.md). The column labeled **Supports tags** indicates whether the resource type has a property for the tag. The column labeled **Tag in cost report** indicates whether that resource type passes the tag to the cost report.

To get the same data as a file of comma-separated values, download [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Jump to a resource provider namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DomainServices | Ja | Ja |
> | DomainServices / oucontainer | Nee | Nee |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | supportProviders | Nee | Nee |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Nee | Nee |
> | addsservices | Nee | Nee |
> | agents | Nee | Nee |
> | anonymousapiusers | Nee | Nee |
> | configuratie | Nee | Nee |
> | logboeken | Nee | Nee |
> | reports | Nee | Nee |
> | servicehealthmetrics | Nee | Nee |
> | services | Nee | Nee |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurations | Nee | Nee |
> | generateRecommendations | Nee | Nee |
> | metagegevens | Nee | Nee |
> | recommendations | Nee | Nee |
> | suppressions | Nee | Nee |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | actionRules | Ja | Ja |
> | waarschuwingen | Nee | Nee |
> | alertsList | Nee | Nee |
> | alertsMetaData | Nee | Nee |
> | alertsSummary | Nee | Nee |
> | alertsSummaryList | Nee | Nee |
> | feedback | Nee | Nee |
> | smartDetectorAlertRules | Ja | Ja |
> | smartDetectorRuntimeEnvironments | Nee | Nee |
> | smartGroups | Nee | Nee |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | servers | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Nee | Nee |
> | service | Ja | Ja |
> | validateServiceName | Nee | Nee |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Ja |
> | configurationStores / eventGridFilters | Nee | Nee |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Spring | Ja | Ja |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Nee | Nee |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Nee | Nee |
> | dataAliases | Nee | Nee |
> | denyAssignments | Nee | Nee |
> | elevateAccess | Nee | Nee |
> | findOrphanRoleAssignments | Nee | Nee |
> | locks | Nee | Nee |
> | permissions | Nee | Nee |
> | policyAssignments | Nee | Nee |
> | policyDefinitions | Nee | Nee |
> | policySetDefinitions | Nee | Nee |
> | providerOperations | Nee | Nee |
> | roleAssignments | Nee | Nee |
> | roleDefinitions | Nee | Nee |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Ja | Ja |
> | automationAccounts / configurations | Ja | Ja |
> | automationAccounts / jobs | Nee | Nee |
> | automationAccounts / runbooks | Ja | Ja |
> | automationAccounts / softwareUpdateConfigurations | Nee | Nee |
> | automationAccounts / webhooks | Nee | Nee |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Ja |
> | configurationStores / eventGridFilters | Nee | Nee |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | Nee | Nee |
> | environments / accounts | Nee | Nee |
> | environments / accounts / namespaces | Nee | Nee |
> | environments / accounts / namespaces / configurations | Nee | Nee |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Ja | Nee |
> | b2ctenants | Nee | Nee |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Ja | Ja |
> | postgresInstances | Ja | Ja |
> | sqlBigDataClusters | Ja | Ja |
> | sqlInstances | Ja | Ja |
> | sqlServerRegistrations | Ja | Ja |
> | sqlServerRegistrations / sqlServers | Nee | Nee |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registrations | Ja | Ja |
> | registrations / customerSubscriptions | Nee | Nee |
> | registrations / products | Nee | Nee |
> | verificationKeys | Nee | Nee |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Ja | Ja |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Nee | Nee |
> | billingAccounts / agreements | Nee | Nee |
> | billingAccounts / billingPermissions | Nee | Nee |
> | billingAccounts / billingProfiles | Nee | Nee |
> | billingAccounts / billingProfiles / billingPermissions | Nee | Nee |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nee | Nee |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nee | Nee |
> | billingAccounts / billingProfiles / billingSubscriptions | Nee | Nee |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nee | Nee |
> | billingAccounts / billingProfiles / customers | Nee | Nee |
> | billingAccounts / billingProfiles / invoices | Nee | Nee |
> | billingAccounts / billingProfiles / invoices / pricesheet | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / products | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Nee | Nee |
> | billingAccounts / BillingProfiles / patchOperations | Nee | Nee |
> | billingAccounts / billingProfiles / paymentMethods | Nee | Nee |
> | billingAccounts / billingProfiles / policies | Nee | Nee |
> | billingAccounts / billingProfiles / pricesheet | Nee | Nee |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nee | Nee |
> | billingAccounts / billingProfiles / products | Nee | Nee |
> | billingAccounts / billingProfiles / transactions | Nee | Nee |
> | billingAccounts / billingRoleAssignments | Nee | Nee |
> | billingAccounts / billingRoleDefinitions | Nee | Nee |
> | billingAccounts / billingSubscriptions | Nee | Nee |
> | billingAccounts / billingSubscriptions / invoices | Nee | Nee |
> | billingAccounts / createBillingRoleAssignment | Nee | Nee |
> | billingAccounts / createInvoiceSectionOperations | Nee | Nee |
> | billingAccounts / customers | Nee | Nee |
> | billingAccounts / customers / billingPermissions | Nee | Nee |
> | billingAccounts / customers / billingSubscriptions | Nee | Nee |
> | billingAccounts / customers / initiateTransfer | Nee | Nee |
> | billingAccounts / customers / policies | Nee | Nee |
> | billingAccounts / customers / products | Nee | Nee |
> | billingAccounts / customers / transactions | Nee | Nee |
> | billingAccounts / customers / transfers | Nee | Nee |
> | billingAccounts / departments | Nee | Nee |
> | billingAccounts / enrollmentAccounts | Nee | Nee |
> | billingAccounts / invoices | Nee | Nee |
> | billingAccounts / invoiceSections | Nee | Nee |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nee | Nee |
> | billingAccounts / invoiceSections / billingSubscriptions | Nee | Nee |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Nee | Nee |
> | billingAccounts / invoiceSections / elevate | Nee | Nee |
> | billingAccounts / invoiceSections / initiateTransfer | Nee | Nee |
> | billingAccounts / invoiceSections / patchOperations | Nee | Nee |
> | billingAccounts / invoiceSections / productMoveOperations | Nee | Nee |
> | billingAccounts / invoiceSections / products | Nee | Nee |
> | billingAccounts / invoiceSections / products / transfer | Nee | Nee |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Nee | Nee |
> | billingAccounts / invoiceSections / transactions | Nee | Nee |
> | billingAccounts / invoiceSections / transfers | Nee | Nee |
> | billingAccounts / lineOfCredit | Nee | Nee |
> | billingAccounts / patchOperations | Nee | Nee |
> | billingAccounts / paymentMethods | Nee | Nee |
> | billingAccounts / products | Nee | Nee |
> | billingAccounts / transactions | Nee | Nee |
> | billingPeriods | Nee | Nee |
> | billingPermissions | Nee | Nee |
> | billingProperty | Nee | Nee |
> | billingRoleAssignments | Nee | Nee |
> | billingRoleDefinitions | Nee | Nee |
> | createBillingRoleAssignment | Nee | Nee |
> | departments | Nee | Nee |
> | enrollmentAccounts | Nee | Nee |
> | invoices | Nee | Nee |
> | transfers | Nee | Nee |
> | transfers / acceptTransfer | Nee | Nee |
> | transfers / declineTransfer | Nee | Nee |
> | transfers / operationStatus | Nee | Nee |
> | transfers / validateTransfer | Nee | Nee |
> | validateAddress | Nee | Nee |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mapApis | Ja | Ja |
> | updateCommunicationPreference | Nee | Nee |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Ja | Ja |
> | cordaMembers | Ja | Ja |
> | kijkers | Ja | Ja |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Nee | Nee |
> | blueprintAssignments / assignmentOperations | Nee | Nee |
> | blueprintAssignments / operations | Nee | Nee |
> | blueprints | Nee | Nee |
> | blueprints / artifacts | Nee | Nee |
> | blueprints / versions | Nee | Nee |
> | blueprints / versions / artifacts | Nee | Nee |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | botServices | Ja | Ja |
> | botServices / channels | Nee | Nee |
> | botServices / connections | Nee | Nee |
> | languages | Nee | Nee |
> | sjablonen | Nee | Nee |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Redis | Ja | Ja |
> | RedisConfigDefinition | Nee | Nee |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Nee | Nee |
> | calculateExchange | Nee | Nee |
> | calculatePrice | Nee | Nee |
> | calculatePurchasePrice | Nee | Nee |
> | catalogs | Nee | Nee |
> | commercialReservationOrders | Nee | Nee |
> | exchange | Nee | Nee |
> | placePurchaseOrder | Nee | Nee |
> | reservationOrders | Nee | Nee |
> | reservationOrders / calculateRefund | Nee | Nee |
> | reservationOrders / merge | Nee | Nee |
> | reservationOrders / reservations | Nee | Nee |
> | reservationOrders / reservations / revisions | Nee | Nee |
> | reservationOrders / return | Nee | Nee |
> | reservationOrders / split | Nee | Nee |
> | reservationOrders / swap | Nee | Nee |
> | reservations | Nee | Nee |
> | resources | Nee | Nee |
> | validateReservationOrder | Nee | Nee |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nee | Nee |
> | CdnWebApplicationFirewallPolicies | Ja | Ja |
> | edgenodes | Nee | Nee |
> | profiles | Ja | Ja |
> | profiles / endpoints | Ja | Ja |
> | profiles / endpoints / customdomains | Nee | Nee |
> | profiles / endpoints / origins | Nee | Nee |
> | validateProbe | Nee | Nee |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Ja | Ja |
> | certificateOrders / certificates | Nee | Nee |
> | validateCertificateRegistrationInformation | Nee | Nee |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Nee | Nee |
> | domainNames | Nee | Nee |
> | domainNames / capabilities | Nee | Nee |
> | domainNames / internalLoadBalancers | Nee | Nee |
> | domainNames / serviceCertificates | Nee | Nee |
> | domainNames / slots | Nee | Nee |
> | domainNames / slots / roles | Nee | Nee |
> | domainNames / slots / roles / metricDefinitions | Nee | Nee |
> | domainNames / slots / roles / metrics | Nee | Nee |
> | moveSubscriptionResources | Nee | Nee |
> | operatingSystemFamilies | Nee | Nee |
> | operatingSystems | Nee | Nee |
> | quotas | Nee | Nee |
> | resourceTypes | Nee | Nee |
> | validateSubscriptionMoveAvailability | Nee | Nee |
> | virtualMachines | Nee | Nee |
> | virtualMachines / diagnosticSettings | Nee | Nee |
> | virtualMachines / metricDefinitions | Nee | Nee |
> | virtualMachines / metrics | Nee | Nee |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Nee | Nee |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Nee | Nee |
> | expressRouteCrossConnections | Nee | Nee |
> | expressRouteCrossConnections / peerings | Nee | Nee |
> | gatewaySupportedDevices | Nee | Nee |
> | networkSecurityGroups | Nee | Nee |
> | quotas | Nee | Nee |
> | reservedIps | Nee | Nee |
> | virtualNetworks | Nee | Nee |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Nee | Nee |
> | virtualNetworks / virtualNetworkPeerings | Nee | Nee |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Nee | Nee |
> | disks | Nee | Nee |
> | images | Nee | Nee |
> | osImages | Nee | Nee |
> | osPlatformImages | Nee | Nee |
> | publicImages | Nee | Nee |
> | quotas | Nee | Nee |
> | storageAccounts | Nee | Nee |
> | storageAccounts / blobServices | Nee | Nee |
> | storageAccounts / fileServices | Nee | Nee |
> | storageAccounts / metricDefinitions | Nee | Nee |
> | storageAccounts / metrics | Nee | Nee |
> | storageAccounts / queueServices | Nee | Nee |
> | storageAccounts / services | Nee | Nee |
> | storageAccounts / services / diagnosticSettings | Nee | Nee |
> | storageAccounts / services / metricDefinitions | Nee | Nee |
> | storageAccounts / services / metrics | Nee | Nee |
> | storageAccounts / tableServices | Nee | Nee |
> | storageAccounts / vmImages | Nee | Nee |
> | vmImages | Nee | Nee |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | RateCard | Nee | Nee |
> | UsageAggregates | Nee | Nee |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Ja | Ja |
> | diskEncryptionSets | Ja | Ja |
> | disks | Ja | Ja |
> | galleries | Ja | Ja |
> | galleries / applications | Nee | Nee |
> | galleries / applications / versions | Nee | Nee |
> | galleries / images | Nee | Nee |
> | galleries / images / versions | Nee | Nee |
> | hostGroups | Ja | Ja |
> | hostGroups / hosts | Ja | Ja |
> | images | Ja | Ja |
> | proximityPlacementGroups | Ja | Ja |
> | restorePointCollections | Ja | Ja |
> | restorePointCollections / restorePoints | Nee | Nee |
> | sharedVMImages | Ja | Ja |
> | sharedVMImages / versions | Nee | Nee |
> | momentopnamen | Ja | Ja |
> | virtualMachines | Ja | Ja |
> | virtualMachines / extensions | Ja | Ja |
> | virtualMachines / metricDefinitions | Nee | Nee |
> | virtualMachineScaleSets | Ja | Ja |
> | virtualMachineScaleSets / extensions | Nee | Nee |
> | virtualMachineScaleSets / networkInterfaces | Nee | Nee |
> | virtualMachineScaleSets / publicIPAddresses | Nee | Nee |
> | virtualMachineScaleSets / virtualMachines | Nee | Nee |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Nee | Nee |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Nee | Nee |
> | Tegoeden | Nee | Nee |
> | Budgetten | Nee | Nee |
> | Kosten | Nee | Nee |
> | CostTags | Nee | Nee |
> | credits | Nee | Nee |
> | events | Nee | Nee |
> | Prognoses | Nee | Nee |
> | lots | Nee | Nee |
> | Marketplaces | Nee | Nee |
> | Pricesheets | Nee | Nee |
> | products | Nee | Nee |
> | ReservationDetails | Nee | Nee |
> | ReservationRecommendations | Nee | Nee |
> | ReservationSummaries | Nee | Nee |
> | ReservationTransactions | Nee | Nee |
> | Tags | Nee | Nee |
> | tenants | Nee | Nee |
> | Voorwaarden | Nee | Nee |
> | UsageDetails | Nee | Nee |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerGroups | Ja | Ja |
> | serviceAssociationLinks | Nee | Nee |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registries | Ja | Ja |
> | registries / builds | Nee | Nee |
> | registries / builds / cancel | Nee | Nee |
> | registries / builds / getLogLink | Nee | Nee |
> | registries / buildTasks | Ja | Ja |
> | registries / buildTasks / steps | Nee | Nee |
> | registries / eventGridFilters | Nee | Nee |
> | registries / generateCredentials | Nee | Nee |
> | registries / getBuildSourceUploadUrl | Nee | Nee |
> | registries / GetCredentials | Nee | Nee |
> | registries / importImage | Nee | Nee |
> | registries / queueBuild | Nee | Nee |
> | registries / regenerateCredential | Nee | Nee |
> | registries / regenerateCredentials | Nee | Nee |
> | registries / replications | Ja | Ja |
> | registries / runs | Nee | Nee |
> | registries / runs / cancel | Nee | Nee |
> | registries / scheduleRun | Nee | Nee |
> | registries / scopeMaps | Nee | Nee |
> | registries / taskRuns | Ja | Ja |
> | registries / tasks | Ja | Ja |
> | registries / tokens | Nee | Nee |
> | registries / updatePolicies | Nee | Nee |
> | registries / webhooks | Ja | Ja |
> | registries / webhooks / getCallbackConfig | Nee | Nee |
> | registries / webhooks / ping | Nee | Nee |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerServices | Ja | Ja |
> | managedClusters | Ja | Ja |
> | openShiftManagedClusters | Ja | Ja |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Waarschuwingen | Nee | Nee |
> | BillingAccounts | Nee | Nee |
> | Budgetten | Nee | Nee |
> | CloudConnectors | Nee | Nee |
> | Connectors | Ja | Ja |
> | Departments | Nee | Nee |
> | Dimensies | Nee | Nee |
> | EnrollmentAccounts | Nee | Nee |
> | Exports | Nee | Nee |
> | ExternalBillingAccounts | Nee | Nee |
> | ExternalBillingAccounts / Alerts | Nee | Nee |
> | ExternalBillingAccounts / Dimensions | Nee | Nee |
> | ExternalBillingAccounts / Forecast | Nee | Nee |
> | ExternalBillingAccounts / Query | Nee | Nee |
> | ExternalSubscriptions | Nee | Nee |
> | ExternalSubscriptions / Alerts | Nee | Nee |
> | ExternalSubscriptions / Dimensions | Nee | Nee |
> | ExternalSubscriptions / Forecast | Nee | Nee |
> | ExternalSubscriptions / Query | Nee | Nee |
> | Forecast | Nee | Nee |
> | Query | Nee | Nee |
> | register | Nee | Nee |
> | Reportconfigs | Nee | Nee |
> | Rapporten | Nee | Nee |
> | Instellingen | Nee | Nee |
> | showbackRules | Nee | Nee |
> | Weergaven | Nee | Nee |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | requests | Nee | Nee |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | associations | Nee | Nee |
> | resourceProviders | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | Ja | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Ja | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaces | Ja | Nee |
> | workspaces / virtualNetworkPeerings | Nee | Nee |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | catalogs | Ja | Ja |
> | datacatalogs | Ja | Ja |
> | datacatalogs / datasources | Nee | Nee |
> | datacatalogs / datasources / scans | Nee | Nee |
> | datacatalogs / datasources / scans / datasets | Nee | Nee |
> | datacatalogs / datasources / scans / triggers | Nee | Nee |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataFactories | Ja | Nee |
> | dataFactories / diagnosticSettings | Nee | Nee |
> | dataFactories / metricDefinitions | Nee | Nee |
> | dataFactorySchema | Nee | Nee |
> | factories | Ja | Nee |
> | factories / integrationRuntimes | Nee | Nee |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts / dataLakeStoreAccounts | Nee | Nee |
> | accounts / storageAccounts | Nee | Nee |
> | accounts / storageAccounts / containers | Nee | Nee |
> | accounts / transferAnalyticsUnits | Nee | Nee |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts / eventGridFilters | Nee | Nee |
> | accounts / firewallRules | Nee | Nee |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | Nee | Nee |
> | services / projects | Nee | Nee |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts / shares | Nee | Nee |
> | accounts / shares / datasets | Nee | Nee |
> | accounts / shares / invitations | Nee | Nee |
> | accounts / shares / providersharesubscriptions | Nee | Nee |
> | accounts / shares / synchronizationSettings | Nee | Nee |
> | accounts / sharesubscriptions | Nee | Nee |
> | accounts / sharesubscriptions / consumerSourceDataSets | Nee | Nee |
> | accounts / sharesubscriptions / datasetmappings | Nee | Nee |
> | accounts / sharesubscriptions / triggers | Nee | Nee |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | servers | Ja | Ja |
> | servers / advisors | Nee | Nee |
> | servers / privateEndpointConnectionProxies | Nee | Nee |
> | servers / privateEndpointConnections | Nee | Nee |
> | servers / privateLinkResources | Nee | Nee |
> | servers / queryTexts | Nee | Nee |
> | servers / recoverableServers | Nee | Nee |
> | servers / topQueryStatistics | Nee | Nee |
> | servers / virtualNetworkRules | Nee | Nee |
> | servers / waitStatistics | Nee | Nee |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | servers | Ja | Ja |
> | servers / advisors | Nee | Nee |
> | servers / privateEndpointConnectionProxies | Nee | Nee |
> | servers / privateEndpointConnections | Nee | Nee |
> | servers / privateLinkResources | Nee | Nee |
> | servers / queryTexts | Nee | Nee |
> | servers / recoverableServers | Nee | Nee |
> | servers / topQueryStatistics | Nee | Nee |
> | servers / virtualNetworkRules | Nee | Nee |
> | servers / waitStatistics | Nee | Nee |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | serverGroups | Ja | Ja |
> | servers | Ja | Ja |
> | servers / advisors | Nee | Nee |
> | servers / keys | Nee | Nee |
> | servers / privateEndpointConnectionProxies | Nee | Nee |
> | servers / privateEndpointConnections | Nee | Nee |
> | servers / privateLinkResources | Nee | Nee |
> | servers / queryTexts | Nee | Nee |
> | servers / recoverableServers | Nee | Nee |
> | servers / topQueryStatistics | Nee | Nee |
> | servers / virtualNetworkRules | Nee | Nee |
> | servers / waitStatistics | Nee | Nee |
> | serversv2 | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | artifactSources | Ja | Ja |
> | rollouts | Ja | Ja |
> | serviceTopologies | Ja | Ja |
> | serviceTopologies / services | Ja | Ja |
> | serviceTopologies / services / serviceUnits | Ja | Ja |
> | stappen | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Ja | Ja |
> | applicationgroups / applications | Nee | Nee |
> | applicationgroups / desktops | Nee | Nee |
> | applicationgroups / startmenuitems | Nee | Nee |
> | hostpools | Ja | Ja |
> | hostpools / sessionhosts | Nee | Nee |
> | hostpools / sessionhosts / usersessions | Nee | Nee |
> | hostpools / usersessions | Nee | Nee |
> | workspaces | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Ja | Ja |
> | ElasticPools / IotHubTenants | Ja | Ja |
> | IotHubs | Ja | Ja |
> | IotHubs / eventGridFilters | Nee | Nee |
> | ProvisioningServices | Ja | Ja |
> | usages | Nee | Nee |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | pipelines | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | controllers | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labcenters | Ja | Ja |
> | labs | Ja | Ja |
> | labs / environments | Ja | Ja |
> | labs / serviceRunners | Ja | Ja |
> | labs / virtualMachines | Ja | Ja |
> | schedules | Ja | Ja |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Nee | Nee |
> | databaseAccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | Ja | Ja |
> | domains / domainOwnershipIdentifiers | Nee | Nee |
> | generateSsoRequest | Nee | Nee |
> | topLevelDomains | Nee | Nee |
> | validateDomainRegistrationInformation | Nee | Nee |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nee | Nee |
> | lcsprojects / clouddeployments | Nee | Nee |
> | lcsprojects / connectors | Nee | Nee |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | Ja | Ja |
> | domains / topics | Nee | Nee |
> | eventSubscriptions | Nee | Nee |
> | extensionTopics | Nee | Nee |
> | topics | Ja | Ja |
> | topicTypes | Nee | Nee |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | namespaces | Ja | Ja |
> | namespaces / authorizationrules | Nee | Nee |
> | namespaces / disasterrecoveryconfigs | Nee | Nee |
> | namespaces / eventhubs | Nee | Nee |
> | namespaces / eventhubs / authorizationrules | Nee | Nee |
> | namespaces / eventhubs / consumergroups | Nee | Nee |
> | namespaces / networkrulesets | Nee | Nee |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | database | Nee | Nee |
> | providers | Nee | Nee |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | enroll | Nee | Nee |
> | galleryitems | Nee | Nee |
> | generateartifactaccessuri | Nee | Nee |
> | myareas | Nee | Nee |
> | myareas / areas | Nee | Nee |
> | myareas / areas / areas | Nee | Nee |
> | myareas / areas / areas / galleryitems | Nee | Nee |
> | myareas / areas / galleryitems | Nee | Nee |
> | myareas / galleryitems | Nee | Nee |
> | register | Nee | Nee |
> | resources | Nee | Nee |
> | retrieveresourcesbyid | Nee | Nee |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationProfileAssignments | Nee | Nee |
> | guestConfigurationAssignments | Nee | Nee |
> | software | Nee | Nee |
> | softwareUpdateProfile | Nee | Nee |
> | softwareUpdates | Nee | Nee |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Ja | Ja |
> | sapMonitors | Ja | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | clusters / applications | Nee | Nee |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | machines | Ja | Ja |
> | machines / extensions | Ja | Ja |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataManagers | Ja | Ja |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | Ja | Ja |
> | networkScopes | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | Ja | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nee | Nee |
> | diagnosticSettingsCategories | Nee | Nee |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appTemplates | Nee | Nee |
> | IoTApps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Grafiek | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Nee | Nee |
> | hsmPools | Ja | Ja |
> | vaults | Ja | Ja |
> | vaults / accessPolicies | Nee | Nee |
> | vaults / eventGridFilters | Nee | Nee |
> | vaults / secrets | Nee | Nee |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | clusters / attacheddatabaseconfigurations | Nee | Nee |
> | clusters / databases | Nee | Nee |
> | clusters / databases / dataconnections | Nee | Nee |
> | clusters / databases / eventhubconnections | Nee | Nee |
> | clusters / sharedidentities | Nee | Nee |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labaccounts | Ja | Ja |
> | gebruikers | Nee | Nee |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Ja | Ja |
> | integrationAccounts | Ja | Ja |
> | integrationServiceEnvironments | Ja | Ja |
> | integrationServiceEnvironments / managedApis | Ja | Ja |
> | isolatedEnvironments | Ja | Ja |
> | workflows | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Ja | Ja |
> | webServices | Ja | Ja |
> | Werkruimten | Ja | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaces | Ja | Ja |
> | workspaces / computes | Nee | Nee |
> | workspaces / eventGridFilters | Nee | Nee |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Identities | Nee | Nee |
> | userAssignedIdentities | Ja | Ja |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Nee | Nee |
> | registrationAssignments | Nee | Nee |
> | registrationDefinitions | Nee | Nee |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | getEntities | Nee | Nee |
> | managementGroups | Nee | Nee |
> | resources | Nee | Nee |
> | startTenantBackfill | Nee | Nee |
> | tenantBackfillStatus | Nee | Nee |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts / eventGridFilters | Nee | Nee |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | offers | Nee | Nee |
> | offerTypes | Nee | Nee |
> | offerTypes / publishers | Nee | Nee |
> | offerTypes / publishers / offers | Nee | Nee |
> | offerTypes / publishers / offers / plans | Nee | Nee |
> | offerTypes / publishers / offers / plans / agreements | Nee | Nee |
> | offerTypes / publishers / offers / plans / configs | Nee | Nee |
> | offerTypes / publishers / offers / plans / configs / importImage | Nee | Nee |
> | privategalleryitems | Nee | Nee |
> | products | Nee | Nee |
> | publishers | Nee | Nee |
> | publishers / offers | Nee | Nee |
> | publishers / offers / amendments | Nee | Nee |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Ja | Ja |
> | updateCommunicationPreference | Nee | Nee |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | agreements | Nee | Nee |
> | offertypes | Nee | Nee |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mediaservices | Ja | Ja |
> | mediaservices / accountFilters | Nee | Nee |
> | mediaservices / assets | Nee | Nee |
> | mediaservices / assets / assetFilters | Nee | Nee |
> | mediaservices / contentKeyPolicies | Nee | Nee |
> | mediaservices / eventGridFilters | Nee | Nee |
> | mediaservices / liveEventOperations | Nee | Nee |
> | mediaservices / liveEvents | Ja | Ja |
> | mediaservices / liveEvents / liveOutputs | Nee | Nee |
> | mediaservices / liveOutputOperations | Nee | Nee |
> | mediaservices / mediaGraphs | Nee | Nee |
> | mediaservices / streamingEndpointOperations | Nee | Nee |
> | mediaservices / streamingEndpoints | Ja | Ja |
> | mediaservices / streamingLocators | Nee | Nee |
> | mediaservices / streamingPolicies | Nee | Nee |
> | mediaservices / transforms | Nee | Nee |
> | mediaservices / transforms / jobs | Nee | Nee |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appClusters | Ja | Ja |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Ja | Ja |
> | migrateprojects | Ja | Ja |
> | projects | Ja | Ja |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Ja | Ja |
> | objectUnderstandingAccounts | Ja | Ja |
> | remoteRenderingAccounts | Ja | Ja |
> | spatialAnchorsAccounts | Ja | Ja |
> | surfaceReconstructionAccounts | Ja | Ja |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Ja | Nee |
> | netAppAccounts / capacityPools | Ja | Nee |
> | netAppAccounts / capacityPools / volumes | Ja | Nee |
> | netAppAccounts / capacityPools / volumes / mountTargets | Ja | Nee |
> | netAppAccounts / capacityPools / volumes / snapshots | Ja | Nee |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Ja | Ja |
> | applicationGatewayWebApplicationFirewallPolicies | Ja | Ja |
> | applicationSecurityGroups | Ja | Ja |
> | azureFirewallFqdnTags | Nee | Nee |
> | azureFirewalls | Ja | Nee |
> | bastionHosts | Ja | Ja |
> | bgpServiceCommunities | Nee | Nee |
> | connections | Ja | Ja |
> | ddosCustomPolicies | Ja | Ja |
> | ddosProtectionPlans | Ja | Ja |
> | dnsOperationStatuses | Nee | Nee |
> | dnszones | Ja | Ja |
> | dnszones / A | Nee | Nee |
> | dnszones / AAAA | Nee | Nee |
> | dnszones / all | Nee | Nee |
> | dnszones / CAA | Nee | Nee |
> | dnszones / CNAME | Nee | Nee |
> | dnszones / MX | Nee | Nee |
> | dnszones / NS | Nee | Nee |
> | dnszones / PTR | Nee | Nee |
> | dnszones / recordsets | Nee | Nee |
> | dnszones / SOA | Nee | Nee |
> | dnszones / SRV | Nee | Nee |
> | dnszones / TXT | Nee | Nee |
> | expressRouteCircuits | Ja | Ja |
> | expressRouteCrossConnections | Ja | Ja |
> | expressRouteGateways | Ja | Ja |
> | expressRoutePorts | Ja | Ja |
> | expressRouteServiceProviders | Nee | Nee |
> | firewallPolicies | Ja | Ja |
> | frontdoors | Yes, but limited (see [note below](#frontdoor)) | Ja |
> | frontdoorWebApplicationFirewallManagedRuleSets | Yes, but limited (see [note below](#frontdoor)) | Nee |
> | frontdoorWebApplicationFirewallPolicies | Yes, but limited (see [note below](#frontdoor)) | Ja |
> | getDnsResourceReference | Nee | Nee |
> | internalNotify | Nee | Nee |
> | loadBalancers | Ja | Nee |
> | localNetworkGateways | Ja | Ja |
> | natGateways | Ja | Ja |
> | networkIntentPolicies | Ja | Ja |
> | networkInterfaces | Ja | Ja |
> | networkProfiles | Ja | Ja |
> | networkSecurityGroups | Ja | Ja |
> | networkWatchers | Ja | Nee |
> | networkWatchers / connectionMonitors | Ja | Nee |
> | networkWatchers / lenses | Ja | Nee |
> | networkWatchers / pingMeshes | Ja | Nee |
> | p2sVpnGateways | Ja | Ja |
> | privateDnsOperationStatuses | Nee | Nee |
> | privateDnsZones | Ja | Ja |
> | privateDnsZones / A | Nee | Nee |
> | privateDnsZones / AAAA | Nee | Nee |
> | privateDnsZones / all | Nee | Nee |
> | privateDnsZones / CNAME | Nee | Nee |
> | privateDnsZones / MX | Nee | Nee |
> | privateDnsZones / PTR | Nee | Nee |
> | privateDnsZones / SOA | Nee | Nee |
> | privateDnsZones / SRV | Nee | Nee |
> | privateDnsZones / TXT | Nee | Nee |
> | privateDnsZones / virtualNetworkLinks | Ja | Ja |
> | privateEndpoints | Ja | Ja |
> | privateLinkServices | Ja | Ja |
> | publicIPAddresses | Ja | Ja |
> | publicIPPrefixes | Ja | Ja |
> | routeFilters | Ja | Ja |
> | routeTables | Ja | Ja |
> | serviceEndpointPolicies | Ja | Ja |
> | trafficManagerGeographicHierarchies | Nee | Nee |
> | trafficmanagerprofiles | Ja | Ja |
> | trafficmanagerprofiles/heatMaps | Nee | Nee |
> | trafficManagerUserMetricsKeys | Nee | Nee |
> | virtualHubs | Ja | Ja |
> | virtualNetworkGateways | Ja | Ja |
> | virtualNetworks | Ja | Ja |
> | virtualNetworkTaps | Ja | Ja |
> | virtualWans | Ja | Ja |
> | vpnGateways | Ja | Nee |
> | vpnSites | Ja | Ja |
> | webApplicationFirewallPolicies | Ja | Ja |

<a id="frontdoor" />

> [!NOTE]
> For Azure Front Door Service, you can apply tags when creating the resource, but updating or adding tags is not currently supported.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Ja | Nee |
> | namespaces / notificationHubs | Ja | Nee |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Ja | Ja |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Ja | Ja |
> | ImportSites | Ja | Ja |
> | ServerSites | Ja | Ja |
> | VMwareSites | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | apparaten | Nee | Nee |
> | linkTargets | Nee | Nee |
> | storageInsightConfigs | Nee | Nee |
> | workspaces | Ja | Ja |
> | workspaces / dataSources | Nee | Nee |
> | workspaces / linkedServices | Nee | Nee |
> | workspaces / query | Nee | Nee |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managementassociations | Nee | Nee |
> | managementconfigurations | Ja | Ja |
> | oplossingen | Ja | Ja |
> | views | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Nee | Nee |
> | peerAsns | Nee | Nee |
> | peerings | Ja | Ja |
> | peeringServiceProviders | Nee | Nee |
> | peeringServices | Ja | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | policyEvents | Nee | Nee |
> | policyMetadata | Nee | Nee |
> | policyStates | Nee | Nee |
> | policyTrackedResources | Nee | Nee |
> | remediations | Nee | Nee |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | consoles | Nee | Nee |
> | dashboards | Ja | Ja |
> | userSettings | Nee | Nee |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capacities | Ja | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Nee | Nee |
> | vaults | Ja | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Ja | Ja |
> | namespaces / authorizationrules | Nee | Nee |
> | namespaces / hybridconnections | Nee | Nee |
> | namespaces / hybridconnections / authorizationrules | Nee | Nee |
> | namespaces / wcfrelays | Nee | Nee |
> | namespaces / wcfrelays / authorizationrules | Nee | Nee |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Nee | Nee |
> | collections | Ja | Ja |
> | collections / applications | Nee | Nee |
> | collections / securityprincipals | Nee | Nee |
> | templateImages | Nee | Nee |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | query's | Ja | Ja |
> | resourceChangeDetails | Nee | Nee |
> | resourceChanges | Nee | Nee |
> | resources | Nee | Nee |
> | resourcesHistory | Nee | Nee |
> | subscriptionsStatus | Nee | Nee |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Nee | Nee |
> | childAvailabilityStatuses | Nee | Nee |
> | childResources | Nee | Nee |
> | events | Nee | Nee |
> | impactedResources | Nee | Nee |
> | metagegevens | Nee | Nee |
> | notifications | Nee | Nee |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deployments | Ja | Nee |
> | deployments / operations | Nee | Nee |
> | deploymentScripts | Ja | Ja |
> | deploymentScripts / logs | Nee | Nee |
> | links | Nee | Nee |
> | notifyResourceJobs | Nee | Nee |
> | providers | Nee | Nee |
> | resourceGroups | Ja | Nee |
> | resources | Nee | Nee |
> | subscriptions | Nee | Nee |
> | subscriptions / providers | Nee | Nee |
> | subscriptions / resources | Nee | Nee |
> | subscriptions / tagnames | Nee | Nee |
> | subscriptions / tagNames / tagValues | Nee | Nee |
> | tenants | Nee | Nee |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | toepassingen | Ja | Ja |
> | saasresources | Nee | Nee |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobcollections | Ja | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Nee | Nee |
> | searchServices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Nee | Nee |
> | advancedThreatProtectionSettings | Nee | Nee |
> | waarschuwingen | Nee | Nee |
> | allowedConnections | Nee | Nee |
> | applicationWhitelistings | Nee | Nee |
> | assessmentMetadata | Nee | Nee |
> | assessments | Nee | Nee |
> | autoDismissAlertsRules | Nee | Nee |
> | automations | Ja | Ja |
> | AutoProvisioningSettings | Nee | Nee |
> | Compliances | Nee | Nee |
> | dataCollectionAgents | Nee | Nee |
> | deviceSecurityGroups | Nee | Nee |
> | discoveredSecuritySolutions | Nee | Nee |
> | externalSecuritySolutions | Nee | Nee |
> | InformationProtectionPolicies | Nee | Nee |
> | iotSecuritySolutions | Ja | Ja |
> | iotSecuritySolutions / analyticsModels | Nee | Nee |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nee | Nee |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nee | Nee |
> | jitNetworkAccessPolicies | Nee | Nee |
> | networkData | Nee | Nee |
> | policies | Nee | Nee |
> | pricings | Nee | Nee |
> | regulatoryComplianceStandards | Nee | Nee |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nee | Nee |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nee | Nee |
> | securityContacts | Nee | Nee |
> | securitySolutions | Nee | Nee |
> | securitySolutionsReferenceData | Nee | Nee |
> | securityStatuses | Nee | Nee |
> | securityStatusesSummaries | Nee | Nee |
> | serverVulnerabilityAssessments | Nee | Nee |
> | instellingen | Nee | Nee |
> | subAssessments | Nee | Nee |
> | taken | Nee | Nee |
> | topologies | Nee | Nee |
> | workspaceSettings | Nee | Nee |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nee | Nee |
> | diagnosticSettingsCategories | Nee | Nee |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aggregations | Nee | Nee |
> | alertRules | Nee | Nee |
> | alertRuleTemplates | Nee | Nee |
> | bookmarks | Nee | Nee |
> | cases | Nee | Nee |
> | dataConnectors | Nee | Nee |
> | entities | Nee | Nee |
> | entityQueries | Nee | Nee |
> | officeConsents | Nee | Nee |
> | instellingen | Nee | Nee |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Ja | Nee |
> | namespaces / authorizationrules | Nee | Nee |
> | namespaces / disasterrecoveryconfigs | Nee | Nee |
> | namespaces / eventgridfilters | Nee | Nee |
> | namespaces / networkrulesets | Nee | Nee |
> | namespaces / queues | Nee | Nee |
> | namespaces / queues / authorizationrules | Nee | Nee |
> | namespaces / topics | Nee | Nee |
> | namespaces / topics / authorizationrules | Nee | Nee |
> | namespaces / topics / subscriptions | Nee | Nee |
> | namespaces / topics / subscriptions / rules | Nee | Nee |
> | premiumMessagingRegions | Nee | Nee |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | toepassingen | Ja | Ja |
> | clusters | Ja | Ja |
> | clusters / applications | Nee | Nee |
> | containerGroups | Ja | Ja |
> | containerGroupSets | Ja | Ja |
> | edgeclusters | Ja | Ja |
> | edgeclusters / applications | Nee | Nee |
> | networks | Ja | Ja |
> | secretstores | Ja | Ja |
> | secretstores / certificates | Nee | Nee |
> | secretstores / secrets | Nee | Nee |
> | volumes | Ja | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | toepassingen | Ja | Ja |
> | containerGroups | Ja | Ja |
> | gateways | Ja | Ja |
> | networks | Ja | Ja |
> | geheimen | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nee | Nee |
> | providerRegistrations / resourceTypeRegistrations | Nee | Nee |
> | rollouts | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SignalR | Ja | Ja |
> | SignalR / eventGridFilters | Nee | Nee |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Ja | Ja |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Nee | Nee |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Ja | Ja |
> | toepassingen | Ja | Ja |
> | jitRequests | Ja | Ja |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managedInstances | Ja | Ja |
> | managedInstances / databases | Yes (see [note below](#sqlnote)) | Ja |
> | managedInstances / databases / backupShortTermRetentionPolicies | Nee | Nee |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Nee | Nee |
> | managedInstances / databases / vulnerabilityAssessments | Nee | Nee |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Nee | Nee |
> | managedInstances / encryptionProtector | Nee | Nee |
> | managedInstances / keys | Nee | Nee |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nee | Nee |
> | managedInstances / vulnerabilityAssessments | Nee | Nee |
> | servers | Ja | Ja |
> | servers / administrators | Nee | Nee |
> | servers / communicationLinks | Nee | Nee |
> | servers / databases | Yes (see [note below](#sqlnote)) | Ja |
> | servers / encryptionProtector | Nee | Nee |
> | servers / firewallRules | Nee | Nee |
> | servers / keys | Nee | Nee |
> | servers / restorableDroppedDatabases | Nee | Nee |
> | servers / serviceobjectives | Nee | Nee |
> | servers / tdeCertificates | Nee | Nee |
> | virtualClusters | Nee | Nee |

<a id="sqlnote" />

> [!NOTE]
> The Master database doesn't support tags, but other databases, including Azure SQL Data Warehouse databases, support tags. Azure SQL Data Warehouse databases must be in Active (not Paused) state.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Ja | Ja |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nee | Nee |
> | SqlVirtualMachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Ja | Ja |
> | storageAccounts / blobServices | Nee | Nee |
> | storageAccounts / fileServices | Nee | Nee |
> | storageAccounts / queueServices | Nee | Nee |
> | storageAccounts / services | Nee | Nee |
> | storageAccounts / services / metricDefinitions | Nee | Nee |
> | storageAccounts / tableServices | Nee | Nee |
> | usages | Nee | Nee |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | caches | Ja | Ja |
> | caches / storageTargets | Nee | Nee |
> | usageModels | Nee | Nee |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Nee | Nee |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Nee | Nee |
> | storageSyncServices / syncGroups | Nee | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee | Nee |
> | storageSyncServices / workflows | Nee | Nee |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Nee | Nee |
> | storageSyncServices / syncGroups | Nee | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee | Nee |
> | storageSyncServices / workflows | Nee | Nee |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Nee | Nee |
> | storageSyncServices / syncGroups | Nee | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee | Nee |
> | storageSyncServices / workflows | Nee | Nee |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managers | Ja | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Yes (see note below) | Ja |

> [!NOTE]
> You can't add a tag when streamingjobs is running. Stop the resource to add a tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | cancel | Nee | Nee |
> | CreateSubscription | Nee | Nee |
> | enable | Nee | Nee |
> | rename | Nee | Nee |
> | SubscriptionDefinitions | Nee | Nee |
> | SubscriptionOperations | Nee | Nee |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | Ja | Nee |
> | environments / accessPolicies | Nee | Nee |
> | environments / eventsources | Ja | Nee |
> | environments / referenceDataSets | Ja | Nee |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Ja | Ja |
> | dedicatedCloudServices | Ja | Ja |
> | virtualMachines | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nee | Nee |
> | apiManagementAccounts / apiAcls | Nee | Nee |
> | apiManagementAccounts / apis | Nee | Nee |
> | apiManagementAccounts / apis / apiAcls | Nee | Nee |
> | apiManagementAccounts / apis / connectionAcls | Nee | Nee |
> | apiManagementAccounts / apis / connections | Nee | Nee |
> | apiManagementAccounts / apis / connections / connectionAcls | Nee | Nee |
> | apiManagementAccounts / apis / localizedDefinitions | Nee | Nee |
> | apiManagementAccounts / connectionAcls | Nee | Nee |
> | apiManagementAccounts / connections | Nee | Nee |
> | billingMeters | Nee | Nee |
> | certificates | Ja | Ja |
> | connectionGateways | Ja | Ja |
> | connections | Ja | Ja |
> | customApis | Ja | Ja |
> | deletedSites | Nee | Nee |
> | functions | Nee | Nee |
> | hostingEnvironments | Ja | Ja |
> | hostingEnvironments / multiRolePools | Nee | Nee |
> | hostingEnvironments / workerPools | Nee | Nee |
> | publishingUsers | Nee | Nee |
> | recommendations | Nee | Nee |
> | resourceHealthMetadata | Nee | Nee |
> | runtimes | Nee | Nee |
> | serverFarms | Ja | Ja |
> | serverFarms / eventGridFilters | Nee | Nee |
> | sites | Ja | Ja |
> | sites / config  | Nee | Nee |
> | sites / eventGridFilters | Nee | Nee |
> | sites / hostNameBindings | Nee | Nee |
> | sites / networkConfig | Nee | Nee |
> | sites / premieraddons | Ja | Ja |
> | sites / slots | Ja | Ja |
> | sites / slots / eventGridFilters | Nee | Nee |
> | sites / slots / hostNameBindings | Nee | Nee |
> | sites / slots / networkConfig | Nee | Nee |
> | sourceControls | Nee | Nee |
> | validate | Nee | Nee |
> | verifyHostingEnvironmentVnet | Nee | Nee |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nee | Nee |
> | diagnosticSettingsCategories | Nee | Nee |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Ja | Ja |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resourcetype | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | Nee | Nee |
> | componentsSummary | Nee | Nee |
> | monitorInstances | Nee | Nee |
> | monitorInstancesSummary | Nee | Nee |
> | monitors | Nee | Nee |
> | notificationSettings | Nee | Nee |

## <a name="next-steps"></a>Volgende stappen

To learn how to apply tags to resources, see [Use tags to organize your Azure resources](resource-group-using-tags.md).
