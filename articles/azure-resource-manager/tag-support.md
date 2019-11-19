---
title: Label ondersteuning voor bronnen
description: Hier wordt weer gegeven welke Azure-resource typen tags ondersteunen. Geeft Details voor alle Azure-Services.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: aa8736310a5c18a1f94d3920a4f88632212c7af4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173556"
---
# <a name="tag-support-for-azure-resources"></a>Ondersteuning voor labels voor Azure-resources
In dit artikel wordt beschreven of een resource type [labels](resource-group-using-tags.md)ondersteunt. De kolom met de naam **ondersteunt labels** geeft aan of het resource type een eigenschap voor de tag heeft. De kolom label **in het kosten rapport** geeft aan of dit resource type de tag doorgeeft aan het kosten rapport.

Down load [tag-support. CSV](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)om dezelfde gegevens op te halen als een bestand met door komma's gescheiden waarden.

Ga naar de naam ruimte van een resource provider:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Micro soft. Addons](#microsoftaddons)
> - [Micro soft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Micro soft. Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Micro soft. AppPlatform](#microsoftappplatform)
> - [Micro soft. Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Micro soft. Azconfig](#microsoftazconfig)
> - [Micro soft. Azure. Genève](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Micro soft. Azureworden](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Micro soft. billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Micro soft. capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Micro soft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Micro soft. commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Micro soft. verbruik](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Micro soft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Micro soft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Micro soft. DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Micro soft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Micro soft. DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Micro soft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Micro soft. features](#microsoftfeatures)
> - [Micro soft. Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Micro soft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Micro soft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Micro soft. Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Micro soft. intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Micro soft. ManagedServices](#microsoftmanagedservices)
> - [Micro soft. Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Micro soft. Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Micro soft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Micro soft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Micro soft. MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Micro soft. ObjectStore](#microsoftobjectstore)
> - [Micro soft. OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Micro soft. RemoteApp](#microsoftremoteapp)
> - [Micro soft. ResourceGraph](#microsoftresourcegraph)
> - [Micro soft. ResourceHealth](#microsoftresourcehealth)
> - [Micro soft. resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Micro soft. SecurityGraph](#microsoftsecuritygraph)
> - [Micro soft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Micro soft. Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Micro soft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Micro soft. SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Micro soft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Micro soft. Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Micro soft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Micro soft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | DomainServices | Ja | Ja |
> | DomainServices/oucontainer | Nee | Nee |
> | DomainServices/ReplicaSets | Ja | Ja |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | supportProviders | Nee | Nee |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Nee | Nee |
> | addsservices | Nee | Nee |
> | middelen | Nee | Nee |
> | anonymousapiusers | Nee | Nee |
> | configuratie | Nee | Nee |
> | logs | Nee | Nee |
> | rapporten | Nee | Nee |
> | servicehealthmetrics | Nee | Nee |
> | services | Nee | Nee |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | configuraties | Nee | Nee |
> | generateRecommendations | Nee | Nee |
> | metagegevens | Nee | Nee |
> | vereisten | Nee | Nee |
> | onderdrukkingen | Nee | Nee |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
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
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | servers | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Nee | Nee |
> | service | Ja | Ja |
> | validateServiceName | Nee | Nee |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Ja |
> | configurationStores/eventGridFilters | Nee | Nee |

## <a name="microsoftappplatform"></a>Micro soft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Spring | Ja | Ja |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Nee | Nee |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Nee | Nee |
> | dataAliases | Nee | Nee |
> | denyAssignments | Nee | Nee |
> | elevateAccess | Nee | Nee |
> | findOrphanRoleAssignments | Nee | Nee |
> | vergren delingen | Nee | Nee |
> | machtigingen | Nee | Nee |
> | policyAssignments | Nee | Nee |
> | policyDefinitions | Nee | Nee |
> | policySetDefinitions | Nee | Nee |
> | providerOperations | Nee | Nee |
> | roleAssignments | Nee | Nee |
> | roleDefinitions | Nee | Nee |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Ja | Ja |
> | automationAccounts/configurations | Ja | Ja |
> | automationAccounts/jobs | Nee | Nee |
> | automationAccounts/runbooks | Ja | Ja |
> | automationAccounts/softwareUpdateConfigurations | Nee | Nee |
> | automationAccounts/webhooks | Nee | Nee |

## <a name="microsoftazconfig"></a>Micro soft. Azconfig

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Ja |
> | configurationStores/eventGridFilters | Nee | Nee |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | verschillend | Nee | Nee |
> | omgevingen/accounts | Nee | Nee |
> | omgevingen/accounts/naam ruimten | Nee | Nee |
> | omgevingen/accounts/naam ruimten/configuraties | Nee | Nee |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Ja | Nee |
> | b2ctenants | Nee | Nee |

## <a name="microsoftazuredata"></a>Micro soft. Azureworden

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Ja | Ja |
> | postgresInstances | Ja | Ja |
> | sqlBigDataClusters | Ja | Ja |
> | sqlInstances | Ja | Ja |
> | sqlServerRegistrations | Ja | Ja |
> | sqlServerRegistrations/sqlServers | Nee | Nee |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | registraties | Ja | Ja |
> | registrations/customerSubscriptions | Nee | Nee |
> | registraties/producten | Nee | Nee |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Ja | Ja |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Nee | Nee |
> | billingAccounts/overeenkomsten | Nee | Nee |
> | billingAccounts/billingPermissions | Nee | Nee |
> | billingAccounts/billingProfiles | Nee | Nee |
> | billingAccounts/billingProfiles/billingPermissions | Nee | Nee |
> | billingAccounts/billingProfiles/billingRoleAssignments | Nee | Nee |
> | billingAccounts/billingProfiles/billingRoleDefinitions | Nee | Nee |
> | billingAccounts/billingProfiles/billingSubscriptions | Nee | Nee |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | Nee | Nee |
> | billingAccounts/billingProfiles/klanten | Nee | Nee |
> | billingAccounts/billingProfiles/invoices | Nee | Nee |
> | billingAccounts/billingProfiles/invoices/pricesheet | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/Products | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/Products/overdracht | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/trans acties | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/transfers | Nee | Nee |
> | billingAccounts/BillingProfiles/patchOperations | Nee | Nee |
> | billingAccounts/billingProfiles/paymentMethods | Nee | Nee |
> | billingAccounts/billingProfiles/policies | Nee | Nee |
> | billingAccounts/billingProfiles/pricesheet | Nee | Nee |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Nee | Nee |
> | billingAccounts/billingProfiles/products | Nee | Nee |
> | billingAccounts/billingProfiles/transactions | Nee | Nee |
> | billingAccounts/billingRoleAssignments | Nee | Nee |
> | billingAccounts/billingRoleDefinitions | Nee | Nee |
> | billingAccounts/billingSubscriptions | Nee | Nee |
> | billingAccounts/createBillingRoleAssignment | Nee | Nee |
> | billingAccounts/createInvoiceSectionOperations | Nee | Nee |
> | billingAccounts/klanten | Nee | Nee |
> | billingAccounts/klanten/billingPermissions | Nee | Nee |
> | billingAccounts/klanten/billingSubscriptions | Nee | Nee |
> | billingAccounts/klanten/initiateTransfer | Nee | Nee |
> | billingAccounts/klanten/beleids regels | Nee | Nee |
> | billingAccounts/klanten/producten | Nee | Nee |
> | billingAccounts/klanten/trans acties | Nee | Nee |
> | billingAccounts/klanten/overdrachten | Nee | Nee |
> | billingAccounts/departments | Nee | Nee |
> | billingAccounts/enrollmentAccounts | Nee | Nee |
> | billingAccounts/invoices | Nee | Nee |
> | billingAccounts/invoiceSections | Nee | Nee |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | Nee | Nee |
> | billingAccounts/invoiceSections/billingSubscriptions | Nee | Nee |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Nee | Nee |
> | billingAccounts/invoiceSections/verhoogde bevoegdheid | Nee | Nee |
> | billingAccounts/invoiceSections/initiateTransfer | Nee | Nee |
> | billingAccounts/invoiceSections/patchOperations | Nee | Nee |
> | billingAccounts/invoiceSections/productMoveOperations | Nee | Nee |
> | billingAccounts/invoiceSections/products | Nee | Nee |
> | billingAccounts/invoiceSections/producten/overdracht | Nee | Nee |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Nee | Nee |
> | billingAccounts/invoiceSections/trans acties | Nee | Nee |
> | billingAccounts/invoiceSections/transfers | Nee | Nee |
> | billingAccounts/lineOfCredit | Nee | Nee |
> | billingAccounts/patchOperations | Nee | Nee |
> | billingAccounts/paymentMethods | Nee | Nee |
> | billingAccounts/producten | Nee | Nee |
> | billingAccounts/transactions | Nee | Nee |
> | billingPeriods | Nee | Nee |
> | billingPermissions | Nee | Nee |
> | billingProperty | Nee | Nee |
> | billingRoleAssignments | Nee | Nee |
> | billingRoleDefinitions | Nee | Nee |
> | createBillingRoleAssignment | Nee | Nee |
> | afdeling | Nee | Nee |
> | enrollmentAccounts | Nee | Nee |
> | factureer | Nee | Nee |
> | Making | Nee | Nee |
> | transfers/acceptTransfer | Nee | Nee |
> | overdrachten/declineTransfer | Nee | Nee |
> | overdrachten/operationStatus | Nee | Nee |
> | overdrachten/validateTransfer | Nee | Nee |
> | validateAddress | Nee | Nee |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | mapApis | Ja | Ja |
> | updateCommunicationPreference | Nee | Nee |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Ja | Ja |
> | cordaMembers | Ja | Ja |
> | kijkers | Ja | Ja |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Nee | Nee |
> | blueprintAssignments/assignmentOperations | Nee | Nee |
> | blueprintAssignments/operations | Nee | Nee |
> | blauw drukken | Nee | Nee |
> | blauw drukken/artefacten | Nee | Nee |
> | blauw drukken/versies | Nee | Nee |
> | blauw drukken/versies/artefacten | Nee | Nee |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | botServices | Ja | Ja |
> | botServices/kanalen | Nee | Nee |
> | botServices/verbindingen | Nee | Nee |
> | Talen | Nee | Nee |
> | sjablonen | Nee | Nee |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Redis | Ja | Ja |
> | RedisConfigDefinition | Nee | Nee |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Nee | Nee |
> | calculateExchange | Nee | Nee |
> | calculatePrice | Nee | Nee |
> | calculatePurchasePrice | Nee | Nee |
> | catalogi | Nee | Nee |
> | commercialReservationOrders | Nee | Nee |
> | exchange | Nee | Nee |
> | placePurchaseOrder | Nee | Nee |
> | reservationOrders | Nee | Nee |
> | reservationOrders/calculateRefund | Nee | Nee |
> | reservationOrders/samen voegen | Nee | Nee |
> | reservationOrders/reserve ringen | Nee | Nee |
> | reservationOrders/reserve ringen/revisies | Nee | Nee |
> | reservationOrders/retour neren | Nee | Nee |
> | reservationOrders/splitsen | Nee | Nee |
> | reservationOrders/swap | Nee | Nee |
> | ringen | Nee | Nee |
> | resources | Nee | Nee |
> | validateReservationOrder | Nee | Nee |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nee | Nee |
> | CdnWebApplicationFirewallPolicies | Ja | Ja |
> | edgenodes | Nee | Nee |
> | profiles | Ja | Ja |
> | profielen/eind punten | Ja | Ja |
> | profielen/eind punten/customdomains | Nee | Nee |
> | profielen/eind punten/oorsprong | Nee | Nee |
> | validateProbe | Nee | Nee |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Ja | Ja |
> | certificateOrders/certificaten | Nee | Nee |
> | validateCertificateRegistrationInformation | Nee | Nee |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | bieden | Nee | Nee |
> | Domein naam | Nee | Nee |
> | Domein naam/mogelijkheden | Nee | Nee |
> | Domein naam/internalLoadBalancers | Nee | Nee |
> | Domein naam/serviceCertificates | Nee | Nee |
> | Domein naam/sleuven | Nee | Nee |
> | Domein naam/sleuven/rollen | Nee | Nee |
> | Domein naam/sleuven/rollen/metricDefinitions | Nee | Nee |
> | Domein naam/sleuven/rollen/metrieken | Nee | Nee |
> | moveSubscriptionResources | Nee | Nee |
> | operatingSystemFamilies | Nee | Nee |
> | operatingSystems | Nee | Nee |
> | quotas | Nee | Nee |
> | resourceTypes | Nee | Nee |
> | validateSubscriptionMoveAvailability | Nee | Nee |
> | Informatie | Nee | Nee |
> | Informatie/diagnosticSettings | Nee | Nee |
> | Informatie/metricDefinitions | Nee | Nee |
> | Informatie/meet waarden | Nee | Nee |

## <a name="microsoftclassicinfrastructuremigrate"></a>Micro soft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Nee | Nee |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | bieden | Nee | Nee |
> | expressRouteCrossConnections | Nee | Nee |
> | expressRouteCrossConnections/peerings | Nee | Nee |
> | gatewaySupportedDevices | Nee | Nee |
> | networkSecurityGroups | Nee | Nee |
> | quotas | Nee | Nee |
> | reservedIps | Nee | Nee |
> | virtualNetworks | Nee | Nee |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nee | Nee |
> | virtualNetworks/virtualNetworkPeerings | Nee | Nee |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | bieden | Nee | Nee |
> | cd's | Nee | Nee |
> | images | Nee | Nee |
> | osImages | Nee | Nee |
> | osPlatformImages | Nee | Nee |
> | publicImages | Nee | Nee |
> | quotas | Nee | Nee |
> | storageAccounts | Nee | Nee |
> | storageAccounts/blobServices | Nee | Nee |
> | storageAccounts/fileServices | Nee | Nee |
> | Storage accounts/metricDefinitions | Nee | Nee |
> | Storage accounts/meet waarden | Nee | Nee |
> | storageAccounts/queueServices | Nee | Nee |
> | storageAccounts/services | Nee | Nee |
> | storageAccounts/services/diagnosticSettings | Nee | Nee |
> | Storage accounts/Services/metricDefinitions | Nee | Nee |
> | Storage accounts/Services/metrische gegevens | Nee | Nee |
> | storageAccounts/tableServices | Nee | Nee |
> | storageAccounts/vmImages | Nee | Nee |
> | vmImages | Nee | Nee |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | RateCard | Nee | Nee |
> | UsageAggregates | Nee | Nee |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Availability sets | Ja | Ja |
> | diskEncryptionSets | Ja | Ja |
> | cd's | Ja | Ja |
> | Galerij | Ja | Ja |
> | galerieën/toepassingen | Nee | Nee |
> | galerieën/toepassingen/versies | Nee | Nee |
> | galerieën/afbeeldingen | Nee | Nee |
> | galerieën/afbeeldingen/versies | Nee | Nee |
> | hostGroups | Ja | Ja |
> | hostGroups/hosts | Ja | Ja |
> | images | Ja | Ja |
> | proximityPlacementGroups | Ja | Ja |
> | restorePointCollections | Ja | Ja |
> | restorePointCollections/restorePoints | Nee | Nee |
> | sharedVMExtensions | Ja | Ja |
> | sharedVMExtensions/versies | Nee | Nee |
> | sharedVMImages | Ja | Ja |
> | sharedVMImages/versies | Nee | Nee |
> | momentopnamen | Ja | Ja |
> | Informatie | Ja | Ja |
> | Informatie/extensies | Ja | Ja |
> | Informatie/metricDefinitions | Nee | Nee |
> | virtualMachineScaleSets | Ja | Ja |
> | virtualMachineScaleSets/extensions | Nee | Nee |
> | virtualMachineScaleSets/networkInterfaces | Nee | Nee |
> | virtualMachineScaleSets/publicIPAddresses | Nee | Nee |
> | virtualMachineScaleSets/virtualMachines | Nee | Nee |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Nee | Nee |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Nee | Nee |
> | Tegoeden | Nee | Nee |
> | Budgetten | Nee | Nee |
> | Kosten | Nee | Nee |
> | CostTags | Nee | Nee |
> | aanvragen | Nee | Nee |
> | events | Nee | Nee |
> | Prognoses | Nee | Nee |
> | extra | Nee | Nee |
> | Markt plaatsen | Nee | Nee |
> | Pricesheets | Nee | Nee |
> | producten | Nee | Nee |
> | ReservationDetails | Nee | Nee |
> | ReservationRecommendations | Nee | Nee |
> | ReservationSummaries | Nee | Nee |
> | ReservationTransactions | Nee | Nee |
> | Tags | Nee | Nee |
> | Tenants | Nee | Nee |
> | Voorwaarden | Nee | Nee |
> | UsageDetails | Nee | Nee |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | containerGroups | Ja | Ja |
> | serviceAssociationLinks | Nee | Nee |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | registers | Ja | Ja |
> | registers/builds | Nee | Nee |
> | registers/builds/annuleren | Nee | Nee |
> | registries/builds/getLogLink | Nee | Nee |
> | registers/buildTasks | Ja | Ja |
> | registers/buildTasks/stappen | Nee | Nee |
> | registries/eventGridFilters | Nee | Nee |
> | registers/generateCredentials | Nee | Nee |
> | registers/getBuildSourceUploadUrl | Nee | Nee |
> | registers/GetCredentials | Nee | Nee |
> | registries/importImage | Nee | Nee |
> | registries/queueBuild | Nee | Nee |
> | registers/regenerateCredential | Nee | Nee |
> | registers/regenerateCredentials | Nee | Nee |
> | registers/replicaties | Ja | Ja |
> | registers/uitvoeringen | Nee | Nee |
> | registers/uitvoeringen/annuleren | Nee | Nee |
> | registers/scheduleRun | Nee | Nee |
> | registers/scopeMaps | Nee | Nee |
> | registers/taken | Ja | Ja |
> | registers/tokens | Nee | Nee |
> | registries/updatePolicies | Nee | Nee |
> | registers/webhooks | Ja | Ja |
> | registries/webhooks/getCallbackConfig | Nee | Nee |
> | registers/webhooks/ping | Nee | Nee |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | containerServices | Ja | Ja |
> | managedClusters | Ja | Ja |
> | openShiftManagedClusters | Ja | Ja |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Waarschuwingen | Nee | Nee |
> | BillingAccounts | Nee | Nee |
> | Budgetten | Nee | Nee |
> | CloudConnectors | Nee | Nee |
> | Connectors | Ja | Ja |
> | afdeling | Nee | Nee |
> | Dimensies | Nee | Nee |
> | EnrollmentAccounts | Nee | Nee |
> | Exports | Nee | Nee |
> | ExternalBillingAccounts | Nee | Nee |
> | ExternalBillingAccounts/waarschuwingen | Nee | Nee |
> | ExternalBillingAccounts/dimensies | Nee | Nee |
> | ExternalBillingAccounts/prognose | Nee | Nee |
> | ExternalBillingAccounts/query | Nee | Nee |
> | ExternalSubscriptions | Nee | Nee |
> | ExternalSubscriptions/waarschuwingen | Nee | Nee |
> | ExternalSubscriptions/dimensies | Nee | Nee |
> | ExternalSubscriptions/prognose | Nee | Nee |
> | ExternalSubscriptions/query | Nee | Nee |
> | Functies | Nee | Nee |
> | Query's uitvoeren | Nee | Nee |
> | inschrijving | Nee | Nee |
> | Reportconfigs | Nee | Nee |
> | Rapporten | Nee | Nee |
> | Instellingen | Nee | Nee |
> | showbackRules | Nee | Nee |
> | Weergaven | Nee | Nee |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | aanvragen | Nee | Nee |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | lidkoppelingen | Nee | Nee |
> | resourceProviders | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | functies | Ja | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Ja | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | werk ruimten | Ja | Nee |
> | workspaces/virtualNetworkPeerings | Nee | Nee |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | catalogi | Ja | Ja |
> | datacatalogs | Ja | Ja |
> | datacatalogs/gegevens bronnen | Nee | Nee |
> | datacatalogs/gegevens bronnen/scans | Nee | Nee |
> | datacatalogs/gegevens bronnen/scans/gegevens sets | Nee | Nee |
> | datacatalogs/gegevens bronnen/scans/triggers | Nee | Nee |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | dataFactories | Ja | Nee |
> | dataFactories/diagnosticSettings | Nee | Nee |
> | dataFactories/metricDefinitions | Nee | Nee |
> | dataFactorySchema | Nee | Nee |
> | factory's | Ja | Nee |
> | fabrieken/integrationRuntimes | Nee | Nee |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/dataLakeStoreAccounts | Nee | Nee |
> | accounts/storageAccounts | Nee | Nee |
> | accounts/storageAccounts/containers | Nee | Nee |
> | accounts/transferAnalyticsUnits | Nee | Nee |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/eventGridFilters | Nee | Nee |
> | accounts/firewallRules | Nee | Nee |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | services | Nee | Nee |
> | Services/projecten | Nee | Nee |

## <a name="microsoftdatashare"></a>Micro soft. DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/shares | Nee | Nee |
> | accounts/shares/gegevens sets | Nee | Nee |
> | accounts/shares/uitnodigingen | Nee | Nee |
> | accounts/shares/providersharesubscriptions | Nee | Nee |
> | accounts/shares/synchronizationSettings | Nee | Nee |
> | accounts/sharesubscriptions | Nee | Nee |
> | accounts/sharesubscriptions/consumerSourceDataSets | Nee | Nee |
> | accounts/sharesubscriptions/datasetmappings | Nee | Nee |
> | accounts/sharesubscriptions/triggers | Nee | Nee |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | servers | Ja | Ja |
> | servers/adviseurs | Nee | Nee |
> | servers/privateEndpointConnectionProxies | Nee | Nee |
> | servers/privateEndpointConnections | Nee | Nee |
> | servers/privateLinkResources | Nee | Nee |
> | servers/queryTexts | Nee | Nee |
> | servers/recoverableServers | Nee | Nee |
> | servers/topQueryStatistics | Nee | Nee |
> | servers/virtualNetworkRules | Nee | Nee |
> | servers/waitStatistics | Nee | Nee |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | servers | Ja | Ja |
> | servers/adviseurs | Nee | Nee |
> | servers/privateEndpointConnectionProxies | Nee | Nee |
> | servers/privateEndpointConnections | Nee | Nee |
> | servers/privateLinkResources | Nee | Nee |
> | servers/queryTexts | Nee | Nee |
> | servers/recoverableServers | Nee | Nee |
> | servers/topQueryStatistics | Nee | Nee |
> | servers/virtualNetworkRules | Nee | Nee |
> | servers/waitStatistics | Nee | Nee |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | serverGroups | Ja | Ja |
> | servers | Ja | Ja |
> | servers/adviseurs | Nee | Nee |
> | servers/sleutels | Nee | Nee |
> | servers/privateEndpointConnectionProxies | Nee | Nee |
> | servers/privateEndpointConnections | Nee | Nee |
> | servers/privateLinkResources | Nee | Nee |
> | servers/queryTexts | Nee | Nee |
> | servers/recoverableServers | Nee | Nee |
> | servers/topQueryStatistics | Nee | Nee |
> | servers/virtualNetworkRules | Nee | Nee |
> | servers/waitStatistics | Nee | Nee |
> | serversv2 | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | artifactSources | Ja | Ja |
> | implementaties | Ja | Ja |
> | serviceTopologies | Ja | Ja |
> | serviceTopologies/Services | Ja | Ja |
> | serviceTopologies/Services/serviceUnits | Ja | Ja |
> | stappen | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Micro soft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Ja | Ja |
> | applicationgroups/toepassingen | Nee | Nee |
> | applicationgroups/Bureau bladen | Nee | Nee |
> | applicationgroups/startmenuitems | Nee | Nee |
> | hostpools | Ja | Ja |
> | hostpools/sessionhosts | Nee | Nee |
> | hostpools/sessionhosts/usersessions | Nee | Nee |
> | hostpools/usersessions | Nee | Nee |
> | werk ruimten | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Ja | Ja |
> | ElasticPools/IotHubTenants | Ja | Ja |
> | IotHubs | Ja | Ja |
> | IotHubs/eventGridFilters | Nee | Nee |
> | ProvisioningServices | Ja | Ja |
> | gebruik | Nee | Nee |

## <a name="microsoftdevops"></a>Micro soft. DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | pijp lijnen | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | fungeren | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | labcenters | Ja | Ja |
> | Labs | Ja | Ja |
> | Labs/omgevingen | Ja | Ja |
> | Labs-serviceRunners | Ja | Ja |
> | Labs-informatie | Ja | Ja |
> | schema's | Ja | Ja |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Nee | Nee |
> | databaseAccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | domeinen | Ja | Ja |
> | domeinen/domainOwnershipIdentifiers | Nee | Nee |
> | generateSsoRequest | Nee | Nee |
> | topLevelDomains | Nee | Nee |
> | validateDomainRegistrationInformation | Nee | Nee |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nee | Nee |
> | lcsprojects/clouddeployments | Nee | Nee |
> | lcsprojects/connectors | Nee | Nee |

## <a name="microsoftenterpriseknowledgegraph"></a>Micro soft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | domeinen | Ja | Ja |
> | domeinen/onderwerpen | Nee | Nee |
> | eventSubscriptions | Nee | Nee |
> | extensionTopics | Nee | Nee |
> | onderwerp | Ja | Ja |
> | topicTypes | Nee | Nee |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | naam ruimten | Ja | Ja |
> | naam ruimten/authorizationrules | Nee | Nee |
> | naam ruimten/disasterrecoveryconfigs | Nee | Nee |
> | naam ruimten/Event hubs | Nee | Nee |
> | naam ruimten/Event hubs/authorizationrules | Nee | Nee |
> | naam ruimten/Event hubs/consumergroups | Nee | Nee |
> | naam ruimten/networkrulesets | Nee | Nee |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | database | Nee | Nee |
> | Providers | Nee | Nee |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Inge | Nee | Nee |
> | galleryitems | Nee | Nee |
> | generateartifactaccessuri | Nee | Nee |
> | myareas | Nee | Nee |
> | myareas/areas | Nee | Nee |
> | myareas/areas/areas | Nee | Nee |
> | myareas/gebieden/gebieden/galleryitems | Nee | Nee |
> | myareas/areas/galleryitems | Nee | Nee |
> | myareas/galleryitems | Nee | Nee |
> | inschrijving | Nee | Nee |
> | resources | Nee | Nee |
> | retrieveresourcesbyid | Nee | Nee |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |

## <a name="microsoftguestconfiguration"></a>Micro soft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | autoManagedVmConfigurationProfiles | Ja | Ja |
> | configurationProfileAssignments | Nee | Nee |
> | guestConfigurationAssignments | Nee | Nee |
> | software | Nee | Nee |
> | softwareUpdateProfile | Nee | Nee |
> | softwareUpdates | Nee | Nee |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Ja | Ja |
> | sapMonitors | Ja | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | clusters/toepassingen | Nee | Nee |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | apparaten | Ja | Ja |
> | computers/uitbrei dingen | Ja | Ja |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | dataManagers | Ja | Ja |

## <a name="microsofthydra"></a>Micro soft. Hydra

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | materialen | Ja | Ja |
> | networkScopes | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | functies | Ja | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nee | Nee |
> | diagnosticSettingsCategories | Nee | Nee |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | appTemplates | Nee | Nee |
> | IoTApps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Nee | Nee |
> | hsmPools | Ja | Ja |
> | kluizen | Ja | Ja |
> | kluizen/accessPolicies | Nee | Nee |
> | kluizen/eventGridFilters | Nee | Nee |
> | kluizen/geheimen | Nee | Nee |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | clusters/attacheddatabaseconfigurations | Nee | Nee |
> | clusters/data bases | Nee | Nee |
> | clusters/data bases/dataConnections | Nee | Nee |
> | clusters/data bases/eventhubconnections | Nee | Nee |
> | clusters/sharedidentities | Nee | Nee |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | labaccounts | Ja | Ja |
> | gebruikers | Nee | Nee |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Ja | Ja |
> | integrationAccounts | Ja | Ja |
> | integrationServiceEnvironments | Ja | Ja |
> | integrationServiceEnvironments/Beheerdeapi's | Ja | Ja |
> | isolatedEnvironments | Ja | Ja |
> | stroom | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Ja | Ja |
> | webServices | Ja | Ja |
> | Workspaces | Ja | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | werk ruimten | Ja | Ja |
> | workspaces/computes | Nee | Nee |
> | werk ruimten/eventGridFilters | Nee | Nee |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Identiteit | Nee | Nee |
> | userAssignedIdentities | Ja | Ja |

## <a name="microsoftmanagedservices"></a>Micro soft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Nee | Nee |
> | registrationAssignments | Nee | Nee |
> | registrationDefinitions | Nee | Nee |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | getEntities | Nee | Nee |
> | managementGroups | Nee | Nee |
> | resources | Nee | Nee |
> | startTenantBackfill | Nee | Nee |
> | tenantBackfillStatus | Nee | Nee |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/eventGridFilters | Nee | Nee |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | over | Nee | Nee |
> | offerTypes | Nee | Nee |
> | offerTypes/publishers | Nee | Nee |
> | offerTypes/publishers/offers | Nee | Nee |
> | offerTypes/publishers/offers/plans | Nee | Nee |
> | offerTypes/publishers/offers/plans/agreements | Nee | Nee |
> | offerTypes/publishers/offers/plans/configs | Nee | Nee |
> | offerTypes/publishers/offers/plans/configs/importImage | Nee | Nee |
> | privategalleryitems | Nee | Nee |
> | producten | Nee | Nee |
> | uitgevers | Nee | Nee |
> | uitgevers/aanbiedingen | Nee | Nee |
> | uitgevers/aanbiedingen/wijzigingen | Nee | Nee |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Ja | Ja |
> | updateCommunicationPreference | Nee | Nee |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | gesloten | Nee | Nee |
> | offertypes | Nee | Nee |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Media Services | Ja | Ja |
> | mediaservices/accountFilters | Nee | Nee |
> | Media Services/assets | Nee | Nee |
> | mediaservices/assets/assetFilters | Nee | Nee |
> | Media Services/contentKeyPolicies | Nee | Nee |
> | mediaservices/eventGridFilters | Nee | Nee |
> | mediaservices/liveEventOperations | Nee | Nee |
> | Media Services/liveEvents | Ja | Ja |
> | Media Services/liveEvents/liveOutputs | Nee | Nee |
> | mediaservices/liveOutputOperations | Nee | Nee |
> | Media Services/mediaGraphs | Nee | Nee |
> | mediaservices/streamingEndpointOperations | Nee | Nee |
> | mediaservices/streamingEndpoints | Ja | Ja |
> | mediaservices/streamingLocators | Nee | Nee |
> | mediaservices/streamingPolicies | Nee | Nee |
> | Media Services/trans formaties | Nee | Nee |
> | Media Services/trans formaties/taken | Nee | Nee |

## <a name="microsoftmicroservices4spring"></a>Micro soft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | appClusters | Ja | Ja |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Ja | Ja |
> | migrateprojects | Ja | Ja |
> | projecten | Ja | Ja |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Ja | Ja |
> | objectUnderstandingAccounts | Ja | Ja |
> | remoteRenderingAccounts | Ja | Ja |
> | spatialAnchorsAccounts | Ja | Ja |
> | surfaceReconstructionAccounts | Ja | Ja |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Ja | Nee |
> | netAppAccounts/capacityPools | Ja | Nee |
> | netAppAccounts/capacityPools/volumes | Ja | Nee |
> | netAppAccounts/capacityPools/volumes/mountTargets | Ja | Nee |
> | netAppAccounts/capacityPools/volumes/moment opnamen | Ja | Nee |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Ja | Ja |
> | applicationGatewayWebApplicationFirewallPolicies | Ja | Ja |
> | applicationSecurityGroups | Ja | Ja |
> | azureFirewallFqdnTags | Nee | Nee |
> | azureFirewalls | Ja | Nee |
> | bastionHosts | Ja | Ja |
> | bgpServiceCommunities | Nee | Nee |
> | inbel | Ja | Ja |
> | ddosCustomPolicies | Ja | Ja |
> | ddosProtectionPlans | Ja | Ja |
> | dnsOperationStatuses | Nee | Nee |
> | dnszones | Ja | Ja |
> | dnszones/A | Nee | Nee |
> | dnszones/AAAA | Nee | Nee |
> | dnszones/all | Nee | Nee |
> | dnszones/CAA | Nee | Nee |
> | dnszones/CNAME | Nee | Nee |
> | dnszones/MX | Nee | Nee |
> | dnszones/NS | Nee | Nee |
> | dnszones/PTR | Nee | Nee |
> | dnszones/recordsets | Nee | Nee |
> | dnszones/SOA | Nee | Nee |
> | dnszones/SRV | Nee | Nee |
> | dnszones/TXT | Nee | Nee |
> | expressRouteCircuits | Ja | Ja |
> | expressRouteCrossConnections | Ja | Ja |
> | expressRouteGateways | Ja | Ja |
> | expressRoutePorts | Ja | Ja |
> | expressRouteServiceProviders | Nee | Nee |
> | firewallPolicies | Ja | Ja |
> | frontdoors | Ja, maar beperkt (Zie [Opmerking hieronder](#frontdoor)) | Ja |
> | frontdoorWebApplicationFirewallManagedRuleSets | Ja, maar beperkt (Zie [Opmerking hieronder](#frontdoor)) | Nee |
> | frontdoorWebApplicationFirewallPolicies | Ja, maar beperkt (Zie [Opmerking hieronder](#frontdoor)) | Ja |
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
> | networkWatchers/connectionMonitors | Ja | Nee |
> | networkWatchers/lenses | Ja | Nee |
> | networkWatchers/pingMeshes | Ja | Nee |
> | p2sVpnGateways | Ja | Ja |
> | privateDnsOperationStatuses | Nee | Nee |
> | privateDnsZones | Ja | Ja |
> | privateDnsZones/A | Nee | Nee |
> | privateDnsZones/AAAA | Nee | Nee |
> | privateDnsZones/alle | Nee | Nee |
> | privateDnsZones/CNAME | Nee | Nee |
> | privateDnsZones/MX | Nee | Nee |
> | privateDnsZones/PTR | Nee | Nee |
> | privateDnsZones/SOA | Nee | Nee |
> | privateDnsZones/SRV | Nee | Nee |
> | privateDnsZones/TXT | Nee | Nee |
> | privateDnsZones/virtualNetworkLinks | Ja | Ja |
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
> Voor de Azure front-deur service kunt u Tags Toep assen bij het maken van de resource, maar het bijwerken of toevoegen van labels wordt momenteel niet ondersteund.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | naam ruimten | Ja | Nee |
> | namespaces/notificationHubs | Ja | Nee |

## <a name="microsoftobjectstore"></a>Micro soft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Ja | Ja |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Ja | Ja |
> | ImportSites | Ja | Ja |
> | ServerSites | Ja | Ja |
> | VMwareSites | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | apparaten | Nee | Nee |
> | linkTargets | Nee | Nee |
> | storageInsightConfigs | Nee | Nee |
> | werk ruimten | Ja | Ja |
> | werk ruimten/gegevens bronnen | Nee | Nee |
> | workspaces/linkedServices | Nee | Nee |
> | werk ruimten/query | Nee | Nee |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | managementassociations | Nee | Nee |
> | managementconfigurations | Ja | Ja |
> | oplossingen | Ja | Ja |
> | Weergaven | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Nee | Nee |
> | peerAsns | Nee | Nee |
> | Peerings | Ja | Ja |
> | peeringServiceProviders | Nee | Nee |
> | peeringServices | Ja | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | policyEvents | Nee | Nee |
> | policyMetadata | Nee | Nee |
> | policyStates | Nee | Nee |
> | policyTrackedResources | Nee | Nee |
> | herstel | Nee | Nee |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | consoles | Nee | Nee |
> | Dash boards | Ja | Ja |
> | userSettings | Nee | Nee |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | beschikt | Ja | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Nee | Nee |
> | kluizen | Ja | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | naam ruimten | Ja | Ja |
> | naam ruimten/authorizationrules | Nee | Nee |
> | naam ruimten/hybridconnections | Nee | Nee |
> | naam ruimten/hybridconnections/authorizationrules | Nee | Nee |
> | naam ruimten/wcfrelays | Nee | Nee |
> | naam ruimten/wcfrelays/authorizationrules | Nee | Nee |

## <a name="microsoftremoteapp"></a>Micro soft. RemoteApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Nee | Nee |
> | reeksen | Ja | Ja |
> | verzamelingen/toepassingen | Nee | Nee |
> | verzamelingen/securityprincipals | Nee | Nee |
> | templateImages | Nee | Nee |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | query's | Ja | Ja |
> | resourceChangeDetails | Nee | Nee |
> | resourceChanges | Nee | Nee |
> | resources | Nee | Nee |
> | resourcesHistory | Nee | Nee |
> | subscriptionsStatus | Nee | Nee |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Nee | Nee |
> | childAvailabilityStatuses | Nee | Nee |
> | childResources | Nee | Nee |
> | events | Nee | Nee |
> | impactedResources | Nee | Nee |
> | metagegevens | Nee | Nee |
> | meldingen | Nee | Nee |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | implementaties | Ja | Nee |
> | implementaties/bewerkingen | Nee | Nee |
> | deploymentScripts | Ja | Ja |
> | deploymentScripts/logboeken | Nee | Nee |
> | koppelen | Nee | Nee |
> | notifyResourceJobs | Nee | Nee |
> | Providers | Nee | Nee |
> | resourceGroups | Ja | Nee |
> | resources | Nee | Nee |
> | geabonneerd | Nee | Nee |
> | abonnementen/providers | Nee | Nee |
> | abonnementen/resourceGroups | Nee | Nee |
> | abonnementen/ResourceGroups/resources | Nee | Nee |
> | abonnementen/resources | Nee | Nee |
> | abonnementen/TagName | Nee | Nee |
> | subscriptions/tagNames/tagValues | Nee | Nee |
> | Tenants | Nee | Nee |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | toepassingen | Ja | Ja |
> | saasresources | Nee | Nee |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | jobcollections | Ja | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Nee | Nee |
> | searchServices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Nee | Nee |
> | advancedThreatProtectionSettings | Nee | Nee |
> | waarschuwingen | Nee | Nee |
> | allowedConnections | Nee | Nee |
> | applicationWhitelistings | Nee | Nee |
> | assessmentMetadata | Nee | Nee |
> | evaluaties | Nee | Nee |
> | automatisering | Ja | Ja |
> | AutoProvisioningSettings | Nee | Nee |
> | Ingebouwde strengste | Nee | Nee |
> | dataCollectionAgents | Nee | Nee |
> | deviceSecurityGroups | Nee | Nee |
> | discoveredSecuritySolutions | Nee | Nee |
> | externalSecuritySolutions | Nee | Nee |
> | InformationProtectionPolicies | Nee | Nee |
> | iotSecuritySolutions | Ja | Ja |
> | iotSecuritySolutions/analyticsModels | Nee | Nee |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | Nee | Nee |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | Nee | Nee |
> | jitNetworkAccessPolicies | Nee | Nee |
> | networkData | Nee | Nee |
> | playbookConfigurations | Ja | Ja |
> | restrictie | Nee | Nee |
> | prijzen | Nee | Nee |
> | regulatoryComplianceStandards | Nee | Nee |
> | regulatoryComplianceStandards/regulatoryComplianceControls | Nee | Nee |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | Nee | Nee |
> | securityContacts | Nee | Nee |
> | securitySolutions | Nee | Nee |
> | securitySolutionsReferenceData | Nee | Nee |
> | securityStatuses | Nee | Nee |
> | securityStatusesSummaries | Nee | Nee |
> | serverVulnerabilityAssessments | Nee | Nee |
> | instellingen | Nee | Nee |
> | subevaluaties | Nee | Nee |
> | taken | Nee | Nee |
> | topologieën | Nee | Nee |
> | workspaceSettings | Nee | Nee |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nee | Nee |
> | diagnosticSettingsCategories | Nee | Nee |

## <a name="microsoftsecurityinsights"></a>Micro soft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | aggregaties | Nee | Nee |
> | alertRules | Nee | Nee |
> | alertRuleTemplates | Nee | Nee |
> | wijzer | Nee | Nee |
> | meldingen | Nee | Nee |
> | dataConnectors | Nee | Nee |
> | Rijg | Nee | Nee |
> | entityQueries | Nee | Nee |
> | officeConsents | Nee | Nee |
> | instellingen | Nee | Nee |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | naam ruimten | Ja | Nee |
> | naam ruimten/authorizationrules | Nee | Nee |
> | naam ruimten/disasterrecoveryconfigs | Nee | Nee |
> | naam ruimten/eventgridfilters | Nee | Nee |
> | naam ruimten/networkrulesets | Nee | Nee |
> | naam ruimten/wacht rijen | Nee | Nee |
> | naam ruimten/wacht rijen/authorizationrules | Nee | Nee |
> | naam ruimten/onderwerpen | Nee | Nee |
> | naam ruimten/onderwerpen/authorizationrules | Nee | Nee |
> | naam ruimten/onderwerpen/abonnementen | Nee | Nee |
> | naam ruimten/onderwerpen/abonnementen/regels | Nee | Nee |
> | premiumMessagingRegions | Nee | Nee |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | toepassingen | Ja | Ja |
> | clusters | Ja | Ja |
> | clusters/toepassingen | Nee | Nee |
> | containerGroups | Ja | Ja |
> | containerGroupSets | Ja | Ja |
> | edgeclusters | Ja | Ja |
> | edgeclusters/toepassingen | Nee | Nee |
> | netwerken | Ja | Ja |
> | secretstores | Ja | Ja |
> | secretstores/certificaten | Nee | Nee |
> | secretstores/geheimen | Nee | Nee |
> | volumes | Ja | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | toepassingen | Ja | Ja |
> | containerGroups | Ja | Ja |
> | gateways | Ja | Ja |
> | netwerken | Ja | Ja |
> | geheimen | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservices"></a>Micro soft. Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nee | Nee |
> | providerRegistrations/resourceTypeRegistrations | Nee | Nee |
> | implementaties | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | SignalR | Ja | Ja |
> | Signaal sterkte/eventGridFilters | Nee | Nee |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Ja | Ja |

## <a name="microsoftsoftwareplan"></a>Micro soft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Nee | Nee |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Ja | Ja |
> | toepassingen | Ja | Ja |
> | jitRequests | Ja | Ja |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | managedInstances | Ja | Ja |
> | managedInstances/data bases | Ja (Zie [Opmerking hieronder](#sqlnote)) | Ja |
> | managedInstances/databases/backupShortTermRetentionPolicies | Nee | Nee |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Nee | Nee |
> | managedInstances/data bases/vulnerabilityAssessments | Nee | Nee |
> | managedInstances/data bases/vulnerabilityAssessments/Rules/basis lijnen | Nee | Nee |
> | managedInstances/encryptionProtector | Nee | Nee |
> | managedInstances/sleutels | Nee | Nee |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nee | Nee |
> | managedInstances/vulnerabilityAssessments | Nee | Nee |
> | servers | Ja | Ja |
> | servers/beheerders | Nee | Nee |
> | servers/communicationLinks | Nee | Nee |
> | servers/data bases | Ja (Zie [Opmerking hieronder](#sqlnote)) | Ja |
> | servers/encryptionProtector | Nee | Nee |
> | servers/firewallRules | Nee | Nee |
> | servers/sleutels | Nee | Nee |
> | servers/restorableDroppedDatabases | Nee | Nee |
> | servers/serviceobjectives | Nee | Nee |
> | servers/tdeCertificates | Nee | Nee |
> | virtualClusters | Nee | Nee |

<a id="sqlnote" />

> [!NOTE]
> De hoofd database ondersteunt geen tags, maar andere data bases, inclusief Azure SQL Data Warehouse data bases, ondersteunings Tags. Azure SQL Data Warehouse-data bases moeten de status actief (niet onderbroken) hebben.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Ja | Ja |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Nee | Nee |
> | SqlVirtualMachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Ja | Ja |
> | storageAccounts/blobServices | Nee | Nee |
> | storageAccounts/fileServices | Nee | Nee |
> | storageAccounts/queueServices | Nee | Nee |
> | storageAccounts/services | Nee | Nee |
> | Storage accounts/Services/metricDefinitions | Nee | Nee |
> | storageAccounts/tableServices | Nee | Nee |
> | gebruik | Nee | Nee |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | caches | Ja | Ja |
> | caches/storageTargets | Nee | Nee |
> | usageModels | Nee | Nee |

## <a name="microsoftstoragereplication"></a>Micro soft. StorageReplication

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Nee | Nee |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices/registeredServers | Nee | Nee |
> | storageSyncServices/syncGroups | Nee | Nee |
> | storageSyncServices/syncGroups/cloudEndpoints | Nee | Nee |
> | storageSyncServices/syncGroups/serverEndpoints | Nee | Nee |
> | storageSyncServices/werk stromen | Nee | Nee |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices/registeredServers | Nee | Nee |
> | storageSyncServices/syncGroups | Nee | Nee |
> | storageSyncServices/syncGroups/cloudEndpoints | Nee | Nee |
> | storageSyncServices/syncGroups/serverEndpoints | Nee | Nee |
> | storageSyncServices/werk stromen | Nee | Nee |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices/registeredServers | Nee | Nee |
> | storageSyncServices/syncGroups | Nee | Nee |
> | storageSyncServices/syncGroups/cloudEndpoints | Nee | Nee |
> | storageSyncServices/syncGroups/serverEndpoints | Nee | Nee |
> | storageSyncServices/werk stromen | Nee | Nee |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | leider | Ja | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Ja (zie opmerking hieronder) | Ja |

> [!NOTE]
> U kunt geen tag toevoegen wanneer streamingjobs wordt uitgevoerd. Stop de resource om een tag toe te voegen.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Annuleren | Nee | Nee |
> | CreateSubscription | Nee | Nee |
> | kunt | Nee | Nee |
> | domeinnaam | Nee | Nee |
> | SubscriptionDefinitions | Nee | Nee |
> | SubscriptionOperations | Nee | Nee |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | verschillend | Ja | Nee |
> | omgevingen/accessPolicies | Nee | Nee |
> | omgevingen/eventsources | Ja | Nee |
> | omgevingen/referenceDataSets | Ja | Nee |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Ja | Ja |
> | dedicatedCloudServices | Ja | Ja |
> | Informatie | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nee | Nee |
> | apiManagementAccounts/apiAcls | Nee | Nee |
> | apiManagementAccounts/apis | Nee | Nee |
> | apiManagementAccounts/apis/apiAcls | Nee | Nee |
> | apiManagementAccounts/apis/connectionAcls | Nee | Nee |
> | apiManagementAccounts/api's/Connections | Nee | Nee |
> | apiManagementAccounts/apis/connections/connectionAcls | Nee | Nee |
> | apiManagementAccounts/api's/localizedDefinitions | Nee | Nee |
> | apiManagementAccounts/connectionAcls | Nee | Nee |
> | apiManagementAccounts/verbindingen | Nee | Nee |
> | billingMeters | Nee | Nee |
> | bewijzen | Ja | Ja |
> | connectionGateways | Ja | Ja |
> | inbel | Ja | Ja |
> | customApis | Ja | Ja |
> | deletedSites | Nee | Nee |
> | functions | Nee | Nee |
> | hostingEnvironments | Ja | Ja |
> | hostingEnvironments/multiRolePools | Nee | Nee |
> | hostingEnvironments/workerPools | Nee | Nee |
> | publishingUsers | Nee | Nee |
> | vereisten | Nee | Nee |
> | resourceHealthMetadata | Nee | Nee |
> | Runtimes | Nee | Nee |
> | Server farms | Ja | Ja |
> | Server farms/eventGridFilters | Nee | Nee |
> | sites | Ja | Ja |
> | sites/configuratie  | Nee | Nee |
> | sites/eventGridFilters | Nee | Nee |
> | sites/hostNameBindings | Nee | Nee |
> | sites/networkConfig | Nee | Nee |
> | sites/premieraddons | Ja | Ja |
> | sites/sleuven | Ja | Ja |
> | sites/sleuven/eventGridFilters | Nee | Nee |
> | sites/sleuven/hostNameBindings | Nee | Nee |
> | sites/sleuven/networkConfig | Nee | Nee |
> | sourceControls | Nee | Nee |
> | subelementid | Nee | Nee |
> | verifyHostingEnvironmentVnet | Nee | Nee |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nee | Nee |
> | diagnosticSettingsCategories | Nee | Nee |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Ja | Ja |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | materialen | Nee | Nee |
> | componentsSummary | Nee | Nee |
> | monitorInstances | Nee | Nee |
> | monitorInstancesSummary | Nee | Nee |
> | monitors | Nee | Nee |
> | notificationSettings | Nee | Nee |

## <a name="next-steps"></a>Volgende stappen

Zie [Tags gebruiken om uw Azure-resources te organiseren](resource-group-using-tags.md)voor meer informatie over het Toep assen van tags op resources.
