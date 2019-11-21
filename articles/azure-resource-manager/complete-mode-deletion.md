---
title: Modus voor volledige verwijdering
description: Shows how resource types handle complete mode deletion in Azure Resource Manager templates.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 075dd5071d6c59c6cc73fff8d51a495b012665dd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232693"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Deletion of Azure resources for complete mode deployments

This article describes how resource types handle deletion when not in a template that is deployed in complete mode.

The resource types marked with **Yes** are deleted when the type isn't in the template deployed with complete mode.

The resource types marked with **No** aren't automatically deleted when not in the template; however, they're deleted if the parent resource is deleted. For a full description of the behavior, see [Azure Resource Manager deployment modes](deployment-modes.md).

If you deploy to [more than one resource group in a template](resource-manager-cross-resource-group-deployment.md), resources in the resource group specified in the deployment operation are eligible to be deleted. Resources in the secondary resource groups aren't deleted.

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
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | DomainServices | Ja |
> | DomainServices / oucontainer | Nee |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | supportProviders | Nee |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | aadsupportcases | Nee |
> | addsservices | Nee |
> | agents | Nee |
> | anonymousapiusers | Nee |
> | configuratie | Nee |
> | logboeken | Nee |
> | reports | Nee |
> | servicehealthmetrics | Nee |
> | services | Nee |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | configurations | Nee |
> | generateRecommendations | Nee |
> | metagegevens | Nee |
> | recommendations | Nee |
> | suppressions | Nee |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | actionRules | Ja |
> | waarschuwingen | Nee |
> | alertsList | Nee |
> | alertsMetaData | Nee |
> | alertsSummary | Nee |
> | alertsSummaryList | Nee |
> | feedback | Nee |
> | smartDetectorAlertRules | Ja |
> | smartDetectorRuntimeEnvironments | Nee |
> | smartGroups | Nee |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | servers | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | reportFeedback | Nee |
> | service | Ja |
> | validateServiceName | Nee |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | configurationStores | Ja |
> | configurationStores / eventGridFilters | Nee |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Spring | Ja |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | attestationProviders | Nee |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | classicAdministrators | Nee |
> | dataAliases | Nee |
> | denyAssignments | Nee |
> | elevateAccess | Nee |
> | findOrphanRoleAssignments | Nee |
> | locks | Nee |
> | permissions | Nee |
> | policyAssignments | Nee |
> | policyDefinitions | Nee |
> | policySetDefinitions | Nee |
> | providerOperations | Nee |
> | roleAssignments | Nee |
> | roleDefinitions | Nee |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | automationAccounts | Ja |
> | automationAccounts / configurations | Ja |
> | automationAccounts / jobs | Nee |
> | automationAccounts / runbooks | Ja |
> | automationAccounts / softwareUpdateConfigurations | Nee |
> | automationAccounts / webhooks | Nee |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | configurationStores | Ja |
> | configurationStores / eventGridFilters | Nee |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | environments | Nee |
> | environments / accounts | Nee |
> | environments / accounts / namespaces | Nee |
> | environments / accounts / namespaces / configurations | Nee |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | b2cDirectories | Ja |
> | b2ctenants | Nee |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | hybridDataManagers | Ja |
> | postgresInstances | Ja |
> | sqlBigDataClusters | Ja |
> | sqlInstances | Ja |
> | sqlServerRegistrations | Ja |
> | sqlServerRegistrations / sqlServers | Nee |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | registrations | Ja |
> | registrations / customerSubscriptions | Nee |
> | registrations / products | Nee |
> | verificationKeys | Nee |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | batchAccounts | Ja |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | billingAccounts | Nee |
> | billingAccounts / agreements | Nee |
> | billingAccounts / billingPermissions | Nee |
> | billingAccounts / billingProfiles | Nee |
> | billingAccounts / billingProfiles / billingPermissions | Nee |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nee |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nee |
> | billingAccounts / billingProfiles / billingSubscriptions | Nee |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nee |
> | billingAccounts / billingProfiles / customers | Nee |
> | billingAccounts / billingProfiles / invoices | Nee |
> | billingAccounts / billingProfiles / invoices / pricesheet | Nee |
> | billingAccounts / billingProfiles / invoiceSections | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nee |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nee |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nee |
> | billingAccounts / billingProfiles / invoiceSections / products | Nee |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Nee |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Nee |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Nee |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Nee |
> | billingAccounts / BillingProfiles / patchOperations | Nee |
> | billingAccounts / billingProfiles / paymentMethods | Nee |
> | billingAccounts / billingProfiles / policies | Nee |
> | billingAccounts / billingProfiles / pricesheet | Nee |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nee |
> | billingAccounts / billingProfiles / products | Nee |
> | billingAccounts / billingProfiles / transactions | Nee |
> | billingAccounts / billingRoleAssignments | Nee |
> | billingAccounts / billingRoleDefinitions | Nee |
> | billingAccounts / billingSubscriptions | Nee |
> | billingAccounts / billingSubscriptions / invoices | Nee |
> | billingAccounts / createBillingRoleAssignment | Nee |
> | billingAccounts / createInvoiceSectionOperations | Nee |
> | billingAccounts / customers | Nee |
> | billingAccounts / customers / billingPermissions | Nee |
> | billingAccounts / customers / billingSubscriptions | Nee |
> | billingAccounts / customers / initiateTransfer | Nee |
> | billingAccounts / customers / policies | Nee |
> | billingAccounts / customers / products | Nee |
> | billingAccounts / customers / transactions | Nee |
> | billingAccounts / customers / transfers | Nee |
> | billingAccounts / departments | Nee |
> | billingAccounts / enrollmentAccounts | Nee |
> | billingAccounts / invoices | Nee |
> | billingAccounts / invoiceSections | Nee |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nee |
> | billingAccounts / invoiceSections / billingSubscriptions | Nee |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Nee |
> | billingAccounts / invoiceSections / elevate | Nee |
> | billingAccounts / invoiceSections / initiateTransfer | Nee |
> | billingAccounts / invoiceSections / patchOperations | Nee |
> | billingAccounts / invoiceSections / productMoveOperations | Nee |
> | billingAccounts / invoiceSections / products | Nee |
> | billingAccounts / invoiceSections / products / transfer | Nee |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Nee |
> | billingAccounts / invoiceSections / transactions | Nee |
> | billingAccounts / invoiceSections / transfers | Nee |
> | billingAccounts / lineOfCredit | Nee |
> | billingAccounts / patchOperations | Nee |
> | billingAccounts / paymentMethods | Nee |
> | billingAccounts / products | Nee |
> | billingAccounts / transactions | Nee |
> | billingPeriods | Nee |
> | billingPermissions | Nee |
> | billingProperty | Nee |
> | billingRoleAssignments | Nee |
> | billingRoleDefinitions | Nee |
> | createBillingRoleAssignment | Nee |
> | departments | Nee |
> | enrollmentAccounts | Nee |
> | invoices | Nee |
> | transfers | Nee |
> | transfers / acceptTransfer | Nee |
> | transfers / declineTransfer | Nee |
> | transfers / operationStatus | Nee |
> | transfers / validateTransfer | Nee |
> | validateAddress | Nee |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | mapApis | Ja |
> | updateCommunicationPreference | Nee |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | blockchainMembers | Ja |
> | cordaMembers | Ja |
> | kijkers | Ja |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | blueprintAssignments | Nee |
> | blueprintAssignments / assignmentOperations | Nee |
> | blueprintAssignments / operations | Nee |
> | blueprints | Nee |
> | blueprints / artifacts | Nee |
> | blueprints / versions | Nee |
> | blueprints / versions / artifacts | Nee |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | botServices | Ja |
> | botServices / channels | Nee |
> | botServices / connections | Nee |
> | languages | Nee |
> | sjablonen | Nee |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Redis | Ja |
> | RedisConfigDefinition | Nee |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | appliedReservations | Nee |
> | calculateExchange | Nee |
> | calculatePrice | Nee |
> | calculatePurchasePrice | Nee |
> | catalogs | Nee |
> | commercialReservationOrders | Nee |
> | exchange | Nee |
> | placePurchaseOrder | Nee |
> | reservationOrders | Nee |
> | reservationOrders / calculateRefund | Nee |
> | reservationOrders / merge | Nee |
> | reservationOrders / reservations | Nee |
> | reservationOrders / reservations / revisions | Nee |
> | reservationOrders / return | Nee |
> | reservationOrders / split | Nee |
> | reservationOrders / swap | Nee |
> | reservations | Nee |
> | resources | Nee |
> | validateReservationOrder | Nee |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nee |
> | CdnWebApplicationFirewallPolicies | Ja |
> | edgenodes | Nee |
> | profiles | Ja |
> | profiles / endpoints | Ja |
> | profiles / endpoints / customdomains | Nee |
> | profiles / endpoints / origins | Nee |
> | validateProbe | Nee |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | certificateOrders | Ja |
> | certificateOrders / certificates | Nee |
> | validateCertificateRegistrationInformation | Nee |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | capabilities | Nee |
> | domainNames | Ja |
> | domainNames / capabilities | Nee |
> | domainNames / internalLoadBalancers | Nee |
> | domainNames / serviceCertificates | Nee |
> | domainNames / slots | Nee |
> | domainNames / slots / roles | Nee |
> | domainNames / slots / roles / metricDefinitions | Nee |
> | domainNames / slots / roles / metrics | Nee |
> | moveSubscriptionResources | Nee |
> | operatingSystemFamilies | Nee |
> | operatingSystems | Nee |
> | quotas | Nee |
> | resourceTypes | Nee |
> | validateSubscriptionMoveAvailability | Nee |
> | virtualMachines | Ja |
> | virtualMachines / diagnosticSettings | Nee |
> | virtualMachines / metricDefinitions | Nee |
> | virtualMachines / metrics | Nee |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | classicInfrastructureResources | Nee |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | capabilities | Nee |
> | expressRouteCrossConnections | Nee |
> | expressRouteCrossConnections / peerings | Nee |
> | gatewaySupportedDevices | Nee |
> | networkSecurityGroups | Ja |
> | quotas | Nee |
> | reservedIps | Ja |
> | virtualNetworks | Ja |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Nee |
> | virtualNetworks / virtualNetworkPeerings | Nee |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | capabilities | Nee |
> | disks | Nee |
> | images | Nee |
> | osImages | Nee |
> | osPlatformImages | Nee |
> | publicImages | Nee |
> | quotas | Nee |
> | storageAccounts | Ja |
> | storageAccounts / blobServices | Nee |
> | storageAccounts / fileServices | Nee |
> | storageAccounts / metricDefinitions | Nee |
> | storageAccounts / metrics | Nee |
> | storageAccounts / queueServices | Nee |
> | storageAccounts / services | Nee |
> | storageAccounts / services / diagnosticSettings | Nee |
> | storageAccounts / services / metricDefinitions | Nee |
> | storageAccounts / services / metrics | Nee |
> | storageAccounts / tableServices | Nee |
> | storageAccounts / vmImages | Nee |
> | vmImages | Nee |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | RateCard | Nee |
> | UsageAggregates | Nee |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | availabilitySets | Ja |
> | diskEncryptionSets | Ja |
> | disks | Ja |
> | galleries | Ja |
> | galleries / applications | Nee |
> | galleries / applications / versions | Nee |
> | galleries / images | Nee |
> | galleries / images / versions | Nee |
> | hostGroups | Ja |
> | hostGroups / hosts | Ja |
> | images | Ja |
> | proximityPlacementGroups | Ja |
> | restorePointCollections | Ja |
> | restorePointCollections / restorePoints | Nee |
> | sharedVMImages | Ja |
> | sharedVMImages / versions | Nee |
> | momentopnamen | Ja |
> | virtualMachines | Ja |
> | virtualMachines / extensions | Ja |
> | virtualMachines / metricDefinitions | Nee |
> | virtualMachineScaleSets | Ja |
> | virtualMachineScaleSets / extensions | Nee |
> | virtualMachineScaleSets / networkInterfaces | Nee |
> | virtualMachineScaleSets / publicIPAddresses | Nee |
> | virtualMachineScaleSets / virtualMachines | Nee |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Nee |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | AggregatedCost | Nee |
> | Tegoeden | Nee |
> | Budgetten | Nee |
> | Kosten | Nee |
> | CostTags | Nee |
> | credits | Nee |
> | events | Nee |
> | Prognoses | Nee |
> | lots | Nee |
> | Marketplaces | Nee |
> | Pricesheets | Nee |
> | products | Nee |
> | ReservationDetails | Nee |
> | ReservationRecommendations | Nee |
> | ReservationSummaries | Nee |
> | ReservationTransactions | Nee |
> | Tags | Nee |
> | tenants | Nee |
> | Voorwaarden | Nee |
> | UsageDetails | Nee |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | containerGroups | Ja |
> | serviceAssociationLinks | Nee |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | registries | Ja |
> | registries / builds | Nee |
> | registries / builds / cancel | Nee |
> | registries / builds / getLogLink | Nee |
> | registries / buildTasks | Ja |
> | registries / buildTasks / steps | Nee |
> | registries / eventGridFilters | Nee |
> | registries / generateCredentials | Nee |
> | registries / getBuildSourceUploadUrl | Nee |
> | registries / GetCredentials | Nee |
> | registries / importImage | Nee |
> | registries / queueBuild | Nee |
> | registries / regenerateCredential | Nee |
> | registries / regenerateCredentials | Nee |
> | registries / replications | Ja |
> | registries / runs | Nee |
> | registries / runs / cancel | Nee |
> | registries / scheduleRun | Nee |
> | registries / scopeMaps | Nee |
> | registries / taskRuns | Ja |
> | registries / tasks | Ja |
> | registries / tokens | Nee |
> | registries / updatePolicies | Nee |
> | registries / webhooks | Ja |
> | registries / webhooks / getCallbackConfig | Nee |
> | registries / webhooks / ping | Nee |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | containerServices | Ja |
> | managedClusters | Ja |
> | openShiftManagedClusters | Ja |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Waarschuwingen | Nee |
> | BillingAccounts | Nee |
> | Budgetten | Nee |
> | CloudConnectors | Nee |
> | Connectors | Ja |
> | Departments | Nee |
> | Dimensies | Nee |
> | EnrollmentAccounts | Nee |
> | Exports | Nee |
> | ExternalBillingAccounts | Nee |
> | ExternalBillingAccounts / Alerts | Nee |
> | ExternalBillingAccounts / Dimensions | Nee |
> | ExternalBillingAccounts / Forecast | Nee |
> | ExternalBillingAccounts / Query | Nee |
> | ExternalSubscriptions | Nee |
> | ExternalSubscriptions / Alerts | Nee |
> | ExternalSubscriptions / Dimensions | Nee |
> | ExternalSubscriptions / Forecast | Nee |
> | ExternalSubscriptions / Query | Nee |
> | Forecast | Nee |
> | Query | Nee |
> | register | Nee |
> | Reportconfigs | Nee |
> | Rapporten | Nee |
> | Instellingen | Nee |
> | showbackRules | Nee |
> | Weergaven | Nee |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | requests | Nee |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | associations | Nee |
> | resourceProviders | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | jobs | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | workspaces | Ja |
> | workspaces / virtualNetworkPeerings | Nee |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | catalogs | Ja |
> | datacatalogs | Ja |
> | datacatalogs / datasources | Nee |
> | datacatalogs / datasources / scans | Nee |
> | datacatalogs / datasources / scans / datasets | Nee |
> | datacatalogs / datasources / scans / triggers | Nee |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dataFactories | Ja |
> | dataFactories / diagnosticSettings | Nee |
> | dataFactories / metricDefinitions | Nee |
> | dataFactorySchema | Nee |
> | factories | Ja |
> | factories / integrationRuntimes | Nee |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |
> | accounts / dataLakeStoreAccounts | Nee |
> | accounts / storageAccounts | Nee |
> | accounts / storageAccounts / containers | Nee |
> | accounts / transferAnalyticsUnits | Nee |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |
> | accounts / eventGridFilters | Nee |
> | accounts / firewallRules | Nee |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | services | Ja |
> | services / projects | Ja |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |
> | accounts / shares | Nee |
> | accounts / shares / datasets | Nee |
> | accounts / shares / invitations | Nee |
> | accounts / shares / providersharesubscriptions | Nee |
> | accounts / shares / synchronizationSettings | Nee |
> | accounts / sharesubscriptions | Nee |
> | accounts / sharesubscriptions / consumerSourceDataSets | Nee |
> | accounts / sharesubscriptions / datasetmappings | Nee |
> | accounts / sharesubscriptions / triggers | Nee |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | servers | Ja |
> | servers / advisors | Nee |
> | servers / privateEndpointConnectionProxies | Nee |
> | servers / privateEndpointConnections | Nee |
> | servers / privateLinkResources | Nee |
> | servers / queryTexts | Nee |
> | servers / recoverableServers | Nee |
> | servers / topQueryStatistics | Nee |
> | servers / virtualNetworkRules | Nee |
> | servers / waitStatistics | Nee |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | servers | Ja |
> | servers / advisors | Nee |
> | servers / privateEndpointConnectionProxies | Nee |
> | servers / privateEndpointConnections | Nee |
> | servers / privateLinkResources | Nee |
> | servers / queryTexts | Nee |
> | servers / recoverableServers | Nee |
> | servers / topQueryStatistics | Nee |
> | servers / virtualNetworkRules | Nee |
> | servers / waitStatistics | Nee |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | serverGroups | Ja |
> | servers | Ja |
> | servers / advisors | Nee |
> | servers / keys | Nee |
> | servers / privateEndpointConnectionProxies | Nee |
> | servers / privateEndpointConnections | Nee |
> | servers / privateLinkResources | Nee |
> | servers / queryTexts | Nee |
> | servers / recoverableServers | Nee |
> | servers / topQueryStatistics | Nee |
> | servers / virtualNetworkRules | Nee |
> | servers / waitStatistics | Nee |
> | serversv2 | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | artifactSources | Ja |
> | rollouts | Ja |
> | serviceTopologies | Ja |
> | serviceTopologies / services | Ja |
> | serviceTopologies / services / serviceUnits | Ja |
> | stappen | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | applicationgroups | Ja |
> | applicationgroups / applications | Nee |
> | applicationgroups / desktops | Nee |
> | applicationgroups / startmenuitems | Nee |
> | hostpools | Ja |
> | hostpools / sessionhosts | Nee |
> | hostpools / sessionhosts / usersessions | Nee |
> | hostpools / usersessions | Nee |
> | workspaces | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | ElasticPools | Ja |
> | ElasticPools / IotHubTenants | Ja |
> | IotHubs | Ja |
> | IotHubs / eventGridFilters | Nee |
> | ProvisioningServices | Ja |
> | usages | Nee |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | pipelines | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | controllers | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | labcenters | Ja |
> | labs | Ja |
> | labs / environments | Ja |
> | labs / serviceRunners | Ja |
> | labs / virtualMachines | Ja |
> | schedules | Ja |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | databaseAccountNames | Nee |
> | databaseAccounts | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | domains | Ja |
> | domains / domainOwnershipIdentifiers | Nee |
> | generateSsoRequest | Nee |
> | topLevelDomains | Nee |
> | validateDomainRegistrationInformation | Nee |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | lcsprojects | Nee |
> | lcsprojects / clouddeployments | Nee |
> | lcsprojects / connectors | Nee |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | domains | Ja |
> | domains / topics | Nee |
> | eventSubscriptions | Nee |
> | extensionTopics | Nee |
> | topics | Ja |
> | topicTypes | Nee |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | clusters | Ja |
> | namespaces | Ja |
> | namespaces / authorizationrules | Nee |
> | namespaces / disasterrecoveryconfigs | Nee |
> | namespaces / eventhubs | Nee |
> | namespaces / eventhubs / authorizationrules | Nee |
> | namespaces / eventhubs / consumergroups | Nee |
> | namespaces / networkrulesets | Nee |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | database | Nee |
> | providers | Nee |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | enroll | Nee |
> | galleryitems | Nee |
> | generateartifactaccessuri | Nee |
> | myareas | Nee |
> | myareas / areas | Nee |
> | myareas / areas / areas | Nee |
> | myareas / areas / areas / galleryitems | Nee |
> | myareas / areas / galleryitems | Nee |
> | myareas / galleryitems | Nee |
> | register | Nee |
> | resources | Nee |
> | retrieveresourcesbyid | Nee |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | configurationProfileAssignments | Nee |
> | guestConfigurationAssignments | Nee |
> | software | Nee |
> | softwareUpdateProfile | Nee |
> | softwareUpdates | Nee |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | hanaInstances | Ja |
> | sapMonitors | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dedicatedHSMs | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | clusters | Ja |
> | clusters / applications | Nee |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | machines | Ja |
> | machines / extensions | Ja |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dataManagers | Ja |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | components | Ja |
> | networkScopes | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | jobs | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | diagnosticSettings | Nee |
> | diagnosticSettingsCategories | Nee |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | appTemplates | Nee |
> | IoTApps | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Grafiek | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | deletedVaults | Nee |
> | hsmPools | Ja |
> | vaults | Ja |
> | vaults / accessPolicies | Nee |
> | vaults / eventGridFilters | Nee |
> | vaults / secrets | Nee |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | clusters | Ja |
> | clusters / attacheddatabaseconfigurations | Nee |
> | clusters / databases | Nee |
> | clusters / databases / dataconnections | Nee |
> | clusters / databases / eventhubconnections | Nee |
> | clusters / sharedidentities | Nee |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | labaccounts | Ja |
> | gebruikers | Nee |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | hostingEnvironments | Ja |
> | integrationAccounts | Ja |
> | integrationServiceEnvironments | Ja |
> | integrationServiceEnvironments / managedApis | Ja |
> | isolatedEnvironments | Ja |
> | workflows | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | commitmentPlans | Ja |
> | webServices | Ja |
> | Werkruimten | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | workspaces | Ja |
> | workspaces / computes | Nee |
> | workspaces / eventGridFilters | Nee |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Identities | Nee |
> | userAssignedIdentities | Ja |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Nee |
> | registrationAssignments | Nee |
> | registrationDefinitions | Nee |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | getEntities | Nee |
> | managementGroups | Nee |
> | resources | Nee |
> | startTenantBackfill | Nee |
> | tenantBackfillStatus | Nee |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |
> | accounts / eventGridFilters | Nee |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | offers | Nee |
> | offerTypes | Nee |
> | offerTypes / publishers | Nee |
> | offerTypes / publishers / offers | Nee |
> | offerTypes / publishers / offers / plans | Nee |
> | offerTypes / publishers / offers / plans / agreements | Nee |
> | offerTypes / publishers / offers / plans / configs | Nee |
> | offerTypes / publishers / offers / plans / configs / importImage | Nee |
> | privategalleryitems | Nee |
> | products | Nee |
> | publishers | Nee |
> | publishers / offers | Nee |
> | publishers / offers / amendments | Nee |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | classicDevServices | Ja |
> | updateCommunicationPreference | Nee |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | agreements | Nee |
> | offertypes | Nee |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | mediaservices | Ja |
> | mediaservices / accountFilters | Nee |
> | mediaservices / assets | Nee |
> | mediaservices / assets / assetFilters | Nee |
> | mediaservices / contentKeyPolicies | Nee |
> | mediaservices / eventGridFilters | Nee |
> | mediaservices / liveEventOperations | Nee |
> | mediaservices / liveEvents | Ja |
> | mediaservices / liveEvents / liveOutputs | Nee |
> | mediaservices / liveOutputOperations | Nee |
> | mediaservices / mediaGraphs | Nee |
> | mediaservices / streamingEndpointOperations | Nee |
> | mediaservices / streamingEndpoints | Ja |
> | mediaservices / streamingLocators | Nee |
> | mediaservices / streamingPolicies | Nee |
> | mediaservices / transforms | Nee |
> | mediaservices / transforms / jobs | Nee |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | appClusters | Ja |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | assessmentProjects | Ja |
> | migrateprojects | Ja |
> | projects | Ja |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Ja |
> | objectUnderstandingAccounts | Ja |
> | remoteRenderingAccounts | Ja |
> | spatialAnchorsAccounts | Ja |
> | surfaceReconstructionAccounts | Ja |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | netAppAccounts | Ja |
> | netAppAccounts / capacityPools | Ja |
> | netAppAccounts / capacityPools / volumes | Ja |
> | netAppAccounts / capacityPools / volumes / mountTargets | Ja |
> | netAppAccounts / capacityPools / volumes / snapshots | Ja |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | applicationGateways | Ja |
> | applicationGatewayWebApplicationFirewallPolicies | Ja |
> | applicationSecurityGroups | Ja |
> | azureFirewallFqdnTags | Nee |
> | azureFirewalls | Ja |
> | bastionHosts | Ja |
> | bgpServiceCommunities | Nee |
> | connections | Ja |
> | ddosCustomPolicies | Ja |
> | ddosProtectionPlans | Ja |
> | dnsOperationStatuses | Nee |
> | dnszones | Ja |
> | dnszones / A | Nee |
> | dnszones / AAAA | Nee |
> | dnszones / all | Nee |
> | dnszones / CAA | Nee |
> | dnszones / CNAME | Nee |
> | dnszones / MX | Nee |
> | dnszones / NS | Nee |
> | dnszones / PTR | Nee |
> | dnszones / recordsets | Nee |
> | dnszones / SOA | Nee |
> | dnszones / SRV | Nee |
> | dnszones / TXT | Nee |
> | expressRouteCircuits | Ja |
> | expressRouteCrossConnections | Ja |
> | expressRouteGateways | Ja |
> | expressRoutePorts | Ja |
> | expressRouteServiceProviders | Nee |
> | firewallPolicies | Ja |
> | frontdoors | Ja |
> | frontdoorWebApplicationFirewallManagedRuleSets | Nee |
> | frontdoorWebApplicationFirewallPolicies | Ja |
> | getDnsResourceReference | Nee |
> | internalNotify | Nee |
> | loadBalancers | Ja |
> | localNetworkGateways | Ja |
> | natGateways | Ja |
> | networkIntentPolicies | Ja |
> | networkInterfaces | Ja |
> | networkProfiles | Ja |
> | networkSecurityGroups | Ja |
> | networkWatchers | Ja |
> | networkWatchers / connectionMonitors | Ja |
> | networkWatchers / lenses | Ja |
> | networkWatchers / pingMeshes | Ja |
> | p2sVpnGateways | Ja |
> | privateDnsOperationStatuses | Nee |
> | privateDnsZones | Ja |
> | privateDnsZones / A | Nee |
> | privateDnsZones / AAAA | Nee |
> | privateDnsZones / all | Nee |
> | privateDnsZones / CNAME | Nee |
> | privateDnsZones / MX | Nee |
> | privateDnsZones / PTR | Nee |
> | privateDnsZones / SOA | Nee |
> | privateDnsZones / SRV | Nee |
> | privateDnsZones / TXT | Nee |
> | privateDnsZones / virtualNetworkLinks | Ja |
> | privateEndpoints | Ja |
> | privateLinkServices | Ja |
> | publicIPAddresses | Ja |
> | publicIPPrefixes | Ja |
> | routeFilters | Ja |
> | routeTables | Ja |
> | serviceEndpointPolicies | Ja |
> | trafficManagerGeographicHierarchies | Nee |
> | trafficmanagerprofiles | Ja |
> | trafficmanagerprofiles / heatMaps | Nee |
> | trafficManagerUserMetricsKeys | Nee |
> | virtualHubs | Ja |
> | virtualNetworkGateways | Ja |
> | virtualNetworks | Ja |
> | virtualNetworkTaps | Ja |
> | virtualWans | Ja |
> | vpnGateways | Ja |
> | vpnSites | Ja |
> | webApplicationFirewallPolicies | Ja |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | namespaces | Ja |
> | namespaces / notificationHubs | Ja |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | osNamespaces | Ja |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | HyperVSites | Ja |
> | ImportSites | Ja |
> | ServerSites | Ja |
> | VMwareSites | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | clusters | Ja |
> | apparaten | Nee |
> | linkTargets | Nee |
> | storageInsightConfigs | Nee |
> | workspaces | Ja |
> | workspaces / dataSources | Nee |
> | workspaces / linkedServices | Nee |
> | workspaces / query | Nee |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | managementassociations | Nee |
> | managementconfigurations | Ja |
> | oplossingen | Ja |
> | views | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | legacyPeerings | Nee |
> | peerAsns | Nee |
> | peerings | Ja |
> | peeringServiceProviders | Nee |
> | peeringServices | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | policyEvents | Nee |
> | policyMetadata | Nee |
> | policyStates | Nee |
> | policyTrackedResources | Nee |
> | remediations | Nee |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | consoles | Nee |
> | dashboards | Ja |
> | userSettings | Nee |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | workspaceCollections | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | capacities | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | backupProtectedItems | Nee |
> | vaults | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | namespaces | Ja |
> | namespaces / authorizationrules | Nee |
> | namespaces / hybridconnections | Nee |
> | namespaces / hybridconnections / authorizationrules | Nee |
> | namespaces / wcfrelays | Nee |
> | namespaces / wcfrelays / authorizationrules | Nee |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Nee |
> | collections | Ja |
> | collections / applications | Nee |
> | collections / securityprincipals | Nee |
> | templateImages | Nee |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | query's | Ja |
> | resourceChangeDetails | Nee |
> | resourceChanges | Nee |
> | resources | Nee |
> | resourcesHistory | Nee |
> | subscriptionsStatus | Nee |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | availabilityStatuses | Nee |
> | childAvailabilityStatuses | Nee |
> | childResources | Nee |
> | events | Nee |
> | impactedResources | Nee |
> | metagegevens | Nee |
> | notifications | Nee |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | deployments | Nee |
> | deployments / operations | Nee |
> | deploymentScripts | Ja |
> | deploymentScripts / logs | Nee |
> | links | Nee |
> | notifyResourceJobs | Nee |
> | providers | Nee |
> | resourceGroups | Nee |
> | resources | Nee |
> | subscriptions | Nee |
> | subscriptions / providers | Nee |
> | subscriptions / resources | Nee |
> | subscriptions / tagnames | Nee |
> | subscriptions / tagNames / tagValues | Nee |
> | tenants | Nee |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingen | Ja |
> | saasresources | Nee |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | jobcollections | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | resourceHealthMetadata | Nee |
> | searchServices | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Nee |
> | advancedThreatProtectionSettings | Nee |
> | waarschuwingen | Nee |
> | allowedConnections | Nee |
> | applicationWhitelistings | Nee |
> | assessmentMetadata | Nee |
> | assessments | Nee |
> | autoDismissAlertsRules | Nee |
> | automations | Ja |
> | AutoProvisioningSettings | Nee |
> | Compliances | Nee |
> | dataCollectionAgents | Nee |
> | deviceSecurityGroups | Nee |
> | discoveredSecuritySolutions | Nee |
> | externalSecuritySolutions | Nee |
> | InformationProtectionPolicies | Nee |
> | iotSecuritySolutions | Ja |
> | iotSecuritySolutions / analyticsModels | Nee |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nee |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nee |
> | jitNetworkAccessPolicies | Nee |
> | networkData | Nee |
> | policies | Nee |
> | pricings | Nee |
> | regulatoryComplianceStandards | Nee |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nee |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nee |
> | securityContacts | Nee |
> | securitySolutions | Nee |
> | securitySolutionsReferenceData | Nee |
> | securityStatuses | Nee |
> | securityStatusesSummaries | Nee |
> | serverVulnerabilityAssessments | Nee |
> | instellingen | Nee |
> | subAssessments | Nee |
> | taken | Nee |
> | topologies | Nee |
> | workspaceSettings | Nee |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | diagnosticSettings | Nee |
> | diagnosticSettingsCategories | Nee |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | aggregations | Nee |
> | alertRules | Nee |
> | alertRuleTemplates | Nee |
> | bookmarks | Nee |
> | cases | Nee |
> | dataConnectors | Nee |
> | entities | Nee |
> | entityQueries | Nee |
> | officeConsents | Nee |
> | instellingen | Nee |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | namespaces | Ja |
> | namespaces / authorizationrules | Nee |
> | namespaces / disasterrecoveryconfigs | Nee |
> | namespaces / eventgridfilters | Nee |
> | namespaces / networkrulesets | Nee |
> | namespaces / queues | Nee |
> | namespaces / queues / authorizationrules | Nee |
> | namespaces / topics | Nee |
> | namespaces / topics / authorizationrules | Nee |
> | namespaces / topics / subscriptions | Nee |
> | namespaces / topics / subscriptions / rules | Nee |
> | premiumMessagingRegions | Nee |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingen | Ja |
> | clusters | Ja |
> | clusters / applications | Nee |
> | containerGroups | Ja |
> | containerGroupSets | Ja |
> | edgeclusters | Ja |
> | edgeclusters / applications | Nee |
> | networks | Ja |
> | secretstores | Ja |
> | secretstores / certificates | Nee |
> | secretstores / secrets | Nee |
> | volumes | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingen | Ja |
> | containerGroups | Ja |
> | gateways | Ja |
> | networks | Ja |
> | geheimen | Ja |
> | volumes | Ja |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | providerRegistrations | Nee |
> | providerRegistrations / resourceTypeRegistrations | Nee |
> | rollouts | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | SignalR | Ja |
> | SignalR / eventGridFilters | Nee |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | SiteRecoveryVault | Ja |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | hybridUseBenefits | Nee |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | applicationDefinitions | Ja |
> | toepassingen | Ja |
> | jitRequests | Ja |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | managedInstances | Ja |
> | managedInstances / databases | Ja |
> | managedInstances / databases / backupShortTermRetentionPolicies | Nee |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Nee |
> | managedInstances / databases / vulnerabilityAssessments | Nee |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Nee |
> | managedInstances / encryptionProtector | Nee |
> | managedInstances / keys | Nee |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nee |
> | managedInstances / vulnerabilityAssessments | Nee |
> | servers | Ja |
> | servers / administrators | Nee |
> | servers / communicationLinks | Nee |
> | servers / databases | Ja |
> | servers / encryptionProtector | Nee |
> | servers / firewallRules | Nee |
> | servers / keys | Nee |
> | servers / restorableDroppedDatabases | Nee |
> | servers / serviceobjectives | Nee |
> | servers / tdeCertificates | Nee |
> | virtualClusters | Nee |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Ja |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nee |
> | SqlVirtualMachines | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageAccounts | Ja |
> | storageAccounts / blobServices | Nee |
> | storageAccounts / fileServices | Nee |
> | storageAccounts / queueServices | Nee |
> | storageAccounts / services | Nee |
> | storageAccounts / services / metricDefinitions | Nee |
> | storageAccounts / tableServices | Nee |
> | usages | Nee |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | caches | Ja |
> | caches / storageTargets | Nee |
> | usageModels | Nee |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | replicationGroups | Nee |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Nee |
> | storageSyncServices / syncGroups | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee |
> | storageSyncServices / workflows | Nee |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Nee |
> | storageSyncServices / syncGroups | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee |
> | storageSyncServices / workflows | Nee |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Nee |
> | storageSyncServices / syncGroups | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee |
> | storageSyncServices / workflows | Nee |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | managers | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | streamingjobs | Ja |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | cancel | Nee |
> | CreateSubscription | Nee |
> | enable | Nee |
> | rename | Nee |
> | SubscriptionDefinitions | Nee |
> | SubscriptionOperations | Nee |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | environments | Ja |
> | environments / accessPolicies | Nee |
> | environments / eventsources | Ja |
> | environments / referenceDataSets | Ja |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Ja |
> | dedicatedCloudServices | Ja |
> | virtualMachines | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | apiManagementAccounts | Nee |
> | apiManagementAccounts / apiAcls | Nee |
> | apiManagementAccounts / apis | Nee |
> | apiManagementAccounts / apis / apiAcls | Nee |
> | apiManagementAccounts / apis / connectionAcls | Nee |
> | apiManagementAccounts / apis / connections | Nee |
> | apiManagementAccounts / apis / connections / connectionAcls | Nee |
> | apiManagementAccounts / apis / localizedDefinitions | Nee |
> | apiManagementAccounts / connectionAcls | Nee |
> | apiManagementAccounts / connections | Nee |
> | billingMeters | Nee |
> | certificates | Ja |
> | connectionGateways | Ja |
> | connections | Ja |
> | customApis | Ja |
> | deletedSites | Nee |
> | functions | Nee |
> | hostingEnvironments | Ja |
> | hostingEnvironments / multiRolePools | Nee |
> | hostingEnvironments / workerPools | Nee |
> | publishingUsers | Nee |
> | recommendations | Nee |
> | resourceHealthMetadata | Nee |
> | runtimes | Nee |
> | serverFarms | Ja |
> | serverFarms / eventGridFilters | Nee |
> | sites | Ja |
> | sites/config  | Nee |
> | sites / eventGridFilters | Nee |
> | sites / hostNameBindings | Nee |
> | sites / networkConfig | Nee |
> | sites / premieraddons | Ja |
> | sites / slots | Ja |
> | sites / slots / eventGridFilters | Nee |
> | sites / slots / hostNameBindings | Nee |
> | sites / slots / networkConfig | Nee |
> | sourceControls | Nee |
> | validate | Nee |
> | verifyHostingEnvironmentVnet | Nee |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | diagnosticSettings | Nee |
> | diagnosticSettingsCategories | Nee |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | DeviceServices | Ja |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | components | Nee |
> | componentsSummary | Nee |
> | monitorInstances | Nee |
> | monitorInstancesSummary | Nee |
> | monitors | Nee |
> | notificationSettings | Nee |

## <a name="next-steps"></a>Volgende stappen

To get the same data as a file of comma-separated values, download [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
