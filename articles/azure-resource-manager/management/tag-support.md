---
title: Label ondersteuning voor bronnen
description: Hier wordt weer gegeven welke Azure-resource typen tags ondersteunen. Geeft Details voor alle Azure-Services.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: e17e6a996f54d6211f87ec20765d3fde782b7d6d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715600"
---
# <a name="tag-support-for-azure-resources"></a>Ondersteuning voor labels voor Azure-resources
In dit artikel wordt beschreven of een resource type [labels](tag-resources.md)ondersteunt. De kolom met de naam **ondersteunt labels** geeft aan of het resource type een eigenschap voor de tag heeft. De kolom label **in het kosten rapport** geeft aan of dit resource type de tag doorgeeft aan het kosten rapport. U kunt kosten op labels weer geven in de [Cost Management kosten analyse](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) en de gegevens voor de [factuur en dagelijks gebruik van Azure](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

Down load [tag-support. CSV](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)om dezelfde gegevens op te halen als een bestand met door komma's gescheiden waarden.

Ga naar de naam ruimte van een resource provider:
> [!div class="op_single_selector"]
> - [Micro soft. AAD](#microsoftaad)
> - [Micro soft. Addons](#microsoftaddons)
> - [Micro soft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Micro soft. Advisor](#microsoftadvisor)
> - [Micro soft. AlertsManagement](#microsoftalertsmanagement)
> - [Micro soft. AnalysisServices](#microsoftanalysisservices)
> - [Micro soft. ApiManagement](#microsoftapimanagement)
> - [Micro soft. AppConfiguration](#microsoftappconfiguration)
> - [Micro soft. AppPlatform](#microsoftappplatform)
> - [Micro soft. Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Micro soft. Automation](#microsoftautomation)
> - [Micro soft. Azconfig](#microsoftazconfig)
> - [Micro soft. Azure. Genève](#microsoftazuregeneva)
> - [Micro soft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Micro soft. Azureworden](#microsoftazuredata)
> - [Micro soft. AzureStack](#microsoftazurestack)
> - [Micro soft. batch](#microsoftbatch)
> - [Micro soft. billing](#microsoftbilling)
> - [Micro soft. BingMaps](#microsoftbingmaps)
> - [Micro soft. Block Chain](#microsoftblockchain)
> - [Micro soft. blauw druk](#microsoftblueprint)
> - [Micro soft. BotService](#microsoftbotservice)
> - [Micro soft. cache](#microsoftcache)
> - [Micro soft. capacity](#microsoftcapacity)
> - [Micro soft. CDN](#microsoftcdn)
> - [Micro soft. CertificateRegistration](#microsoftcertificateregistration)
> - [Micro soft. ClassicCompute](#microsoftclassiccompute)
> - [Micro soft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Micro soft. ClassicNetwork](#microsoftclassicnetwork)
> - [Micro soft. ClassicStorage](#microsoftclassicstorage)
> - [Micro soft. CognitiveServices](#microsoftcognitiveservices)
> - [Micro soft. commerce](#microsoftcommerce)
> - [Micro soft. compute](#microsoftcompute)
> - [Micro soft. verbruik](#microsoftconsumption)
> - [Micro soft. ContainerInstance](#microsoftcontainerinstance)
> - [Micro soft. ContainerRegistry](#microsoftcontainerregistry)
> - [Micro soft. container service](#microsoftcontainerservice)
> - [Micro soft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Micro soft. CostManagement](#microsoftcostmanagement)
> - [Micro soft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Micro soft. CustomProviders](#microsoftcustomproviders)
> - [Micro soft. DataBox](#microsoftdatabox)
> - [Micro soft. DataBoxEdge](#microsoftdataboxedge)
> - [Micro soft. Databricks](#microsoftdatabricks)
> - [Micro soft. DataCatalog](#microsoftdatacatalog)
> - [Micro soft. DataFactory](#microsoftdatafactory)
> - [Micro soft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Micro soft. data Lake Store](#microsoftdatalakestore)
> - [Micro soft. DataMigration](#microsoftdatamigration)
> - [Micro soft. DataShare](#microsoftdatashare)
> - [Micro soft. DBforMariaDB](#microsoftdbformariadb)
> - [Micro soft. DBforMySQL](#microsoftdbformysql)
> - [Micro soft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Micro soft. DeploymentManager](#microsoftdeploymentmanager)
> - [Micro soft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Micro soft.-apparaten](#microsoftdevices)
> - [Micro soft. DevOps](#microsoftdevops)
> - [Micro soft. DevSpaces](#microsoftdevspaces)
> - [Micro soft. DevTestLab](#microsoftdevtestlab)
> - [Micro soft. DocumentDB](#microsoftdocumentdb)
> - [Micro soft. DomainRegistration](#microsoftdomainregistration)
> - [Micro soft. DynamicsLcs](#microsoftdynamicslcs)
> - [Micro soft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Micro soft. EventGrid](#microsofteventgrid)
> - [Micro soft. EventHub](#microsofteventhub)
> - [Micro soft. features](#microsoftfeatures)
> - [Micro soft. Gallery](#microsoftgallery)
> - [Micro soft. Genomics](#microsoftgenomics)
> - [Micro soft. GuestConfiguration](#microsoftguestconfiguration)
> - [Micro soft. HanaOnAzure](#microsofthanaonazure)
> - [Micro soft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Micro soft. HDInsight](#microsofthdinsight)
> - [Micro soft. HealthcareApis](#microsofthealthcareapis)
> - [Micro soft. HybridCompute](#microsofthybridcompute)
> - [Micro soft. HybridData](#microsofthybriddata)
> - [Micro soft. Hydra](#microsofthydra)
> - [Micro soft. ImportExport](#microsoftimportexport)
> - [Micro soft. intune](#microsoftintune)
> - [Micro soft. IoTCentral](#microsoftiotcentral)
> - [Micro soft. IoTSpaces](#microsoftiotspaces)
> - [Micro soft.-sleutel kluis](#microsoftkeyvault)
> - [Micro soft. Kusto](#microsoftkusto)
> - [Micro soft. LabServices](#microsoftlabservices)
> - [Micro soft. Logic](#microsoftlogic)
> - [Micro soft. MachineLearning](#microsoftmachinelearning)
> - [Micro soft. MachineLearningServices](#microsoftmachinelearningservices)
> - [Micro soft. ManagedIdentity](#microsoftmanagedidentity)
> - [Micro soft. ManagedServices](#microsoftmanagedservices)
> - [Micro soft. Management](#microsoftmanagement)
> - [Micro soft. Maps](#microsoftmaps)
> - [Micro soft. Marketplace](#microsoftmarketplace)
> - [Micro soft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Micro soft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Micro soft. Media](#microsoftmedia)
> - [Micro soft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Micro soft. migrate](#microsoftmigrate)
> - [Micro soft. MixedReality](#microsoftmixedreality)
> - [Micro soft. NetApp](#microsoftnetapp)
> - [Micro soft. Network](#microsoftnetwork)
> - [Micro soft. notification hubs](#microsoftnotificationhubs)
> - [Micro soft. ObjectStore](#microsoftobjectstore)
> - [Micro soft. OffAzure](#microsoftoffazure)
> - [Micro soft. OperationalInsights](#microsoftoperationalinsights)
> - [Micro soft. OperationsManagement](#microsoftoperationsmanagement)
> - [Micro soft. peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Micro soft. Portal](#microsoftportal)
> - [Micro soft. PowerBI](#microsoftpowerbi)
> - [Micro soft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Micro soft. ProjectBabylon](#microsoftprojectbabylon)
> - [Micro soft. Recovery Services](#microsoftrecoveryservices)
> - [Micro soft. relay](#microsoftrelay)
> - [Micro soft. RemoteApp](#microsoftremoteapp)
> - [Micro soft. ResourceGraph](#microsoftresourcegraph)
> - [Micro soft. ResourceHealth](#microsoftresourcehealth)
> - [Micro soft. resources](#microsoftresources)
> - [Micro soft. SaaS](#microsoftsaas)
> - [Micro soft. scheduler](#microsoftscheduler)
> - [Micro soft. Search](#microsoftsearch)
> - [Micro soft. Security](#microsoftsecurity)
> - [Micro soft. SecurityGraph](#microsoftsecuritygraph)
> - [Micro soft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Micro soft. ServiceFabric](#microsoftservicefabric)
> - [Micro soft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Micro soft. Services](#microsoftservices)
> - [Micro soft. SignalRService](#microsoftsignalrservice)
> - [Micro soft. SiteRecovery](#microsoftsiterecovery)
> - [Micro soft. SoftwarePlan](#microsoftsoftwareplan)
> - [Micro soft. Solutions](#microsoftsolutions)
> - [Micro soft. SQL](#microsoftsql)
> - [Micro soft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Micro soft. Storage](#microsoftstorage)
> - [Micro soft. StorageCache](#microsoftstoragecache)
> - [Micro soft. StorageReplication](#microsoftstoragereplication)
> - [Micro soft. StorageSync](#microsoftstoragesync)
> - [Micro soft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Micro soft. StorageSyncInt](#microsoftstoragesyncint)
> - [Micro soft. StorSimple](#microsoftstorsimple)
> - [Micro soft. StreamAnalytics](#microsoftstreamanalytics)
> - [Micro soft. Subscription](#microsoftsubscription)
> - [Micro soft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Micro soft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Micro soft. VnfManager](#microsoftvnfmanager)
> - [Micro soft. Web](#microsoftweb)
> - [Micro soft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Micro soft. WindowsIoT](#microsoftwindowsiot)
> - [Micro soft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | DomainServices | Ja | Ja |
> | DomainServices / oucontainer | Nee | Nee |

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
> | logboeken | Nee | Nee |
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
> | Server | Ja | Ja |

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
> | configurationStores / eventGridFilters | Nee | Nee |

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
> | roleAssignmentsUsageMetrics | Nee | Nee |
> | roleDefinitions | Nee | Nee |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Ja | Ja |
> | automationAccounts/configuraties | Ja | Ja |
> | automationAccounts/Jobs | Nee | Nee |
> | automationAccounts/runbooks | Ja | Ja |
> | automationAccounts / softwareUpdateConfigurations | Nee | Nee |
> | automationAccounts/webhooks | Nee | Nee |

## <a name="microsoftazconfig"></a>Micro soft. Azconfig

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Ja |
> | configurationStores / eventGridFilters | Nee | Nee |

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
> | sqlServerRegistrations / sqlServers | Nee | Nee |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | registraties | Ja | Ja |
> | registraties/customerSubscriptions | Nee | Nee |
> | registraties/producten | Nee | Nee |
> | verificationKeys | Nee | Nee |

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
> | billingAccounts / billingPermissions | Nee | Nee |
> | billingAccounts / billingProfiles | Nee | Nee |
> | billingAccounts / billingProfiles / billingPermissions | Nee | Nee |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nee | Nee |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nee | Nee |
> | billingAccounts / billingProfiles / billingSubscriptions | Nee | Nee |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nee | Nee |
> | billingAccounts/billingProfiles/klanten | Nee | Nee |
> | billingAccounts/billingProfiles/instructies | Nee | Nee |
> | billingAccounts/billingProfiles/facturen | Nee | Nee |
> | billingAccounts/billingProfiles/facturen/prijzen overzicht | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/Products | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/Products/overdracht | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/trans acties | Nee | Nee |
> | billingAccounts/billingProfiles/invoiceSections/transfers | Nee | Nee |
> | billingAccounts / BillingProfiles / patchOperations | Nee | Nee |
> | billingAccounts / billingProfiles / paymentMethods | Nee | Nee |
> | billingAccounts/billingProfiles/beleid | Nee | Nee |
> | billingAccounts/billingProfiles/prijzen overzicht | Nee | Nee |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nee | Nee |
> | billingAccounts/billingProfiles/producten | Nee | Nee |
> | billingAccounts/billingProfiles/trans acties | Nee | Nee |
> | billingAccounts / billingRoleAssignments | Nee | Nee |
> | billingAccounts / billingRoleDefinitions | Nee | Nee |
> | billingAccounts / billingSubscriptions | Nee | Nee |
> | billingAccounts/billingSubscriptions/facturen | Nee | Nee |
> | billingAccounts / createBillingRoleAssignment | Nee | Nee |
> | billingAccounts / createInvoiceSectionOperations | Nee | Nee |
> | billingAccounts/klanten | Nee | Nee |
> | billingAccounts/klanten/billingPermissions | Nee | Nee |
> | billingAccounts/klanten/billingSubscriptions | Nee | Nee |
> | billingAccounts/klanten/initiateTransfer | Nee | Nee |
> | billingAccounts/klanten/beleids regels | Nee | Nee |
> | billingAccounts/klanten/producten | Nee | Nee |
> | billingAccounts/klanten/trans acties | Nee | Nee |
> | billingAccounts/klanten/overdrachten | Nee | Nee |
> | billingAccounts/afdelingen | Nee | Nee |
> | billingAccounts / enrollmentAccounts | Nee | Nee |
> | billingAccounts/facturen | Nee | Nee |
> | billingAccounts / invoiceSections | Nee | Nee |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nee | Nee |
> | billingAccounts / invoiceSections / billingSubscriptions | Nee | Nee |
> | billingAccounts/invoiceSections/billingSubscriptions/overdracht | Nee | Nee |
> | billingAccounts/invoiceSections/verhoogde bevoegdheid | Nee | Nee |
> | billingAccounts / invoiceSections / initiateTransfer | Nee | Nee |
> | billingAccounts / invoiceSections / patchOperations | Nee | Nee |
> | billingAccounts / invoiceSections / productMoveOperations | Nee | Nee |
> | billingAccounts/invoiceSections/producten | Nee | Nee |
> | billingAccounts/invoiceSections/producten/overdracht | Nee | Nee |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Nee | Nee |
> | billingAccounts/invoiceSections/trans acties | Nee | Nee |
> | billingAccounts/invoiceSections/overdrachten | Nee | Nee |
> | billingAccounts / lineOfCredit | Nee | Nee |
> | billingAccounts / patchOperations | Nee | Nee |
> | billingAccounts / paymentMethods | Nee | Nee |
> | billingAccounts/producten | Nee | Nee |
> | billingAccounts/trans acties | Nee | Nee |
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
> | overdrachten/acceptTransfer | Nee | Nee |
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
> | Volg | Ja | Ja |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Nee | Nee |
> | blueprintAssignments / assignmentOperations | Nee | Nee |
> | blueprintAssignments/bewerkingen | Nee | Nee |
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
> | autoQuotaIncrease | Nee | Nee |
> | calculateExchange | Nee | Nee |
> | calculatePrice | Nee | Nee |
> | calculatePurchasePrice | Nee | Nee |
> | catalogi | Nee | Nee |
> | commercialReservationOrders | Nee | Nee |
> | Exchange | Nee | Nee |
> | placePurchaseOrder | Nee | Nee |
> | reservationOrders | Nee | Nee |
> | reservationOrders / calculateRefund | Nee | Nee |
> | reservationOrders/samen voegen | Nee | Nee |
> | reservationOrders/reserve ringen | Nee | Nee |
> | reservationOrders/reserve ringen/revisies | Nee | Nee |
> | reservationOrders/retour neren | Nee | Nee |
> | reservationOrders/splitsen | Nee | Nee |
> | reservationOrders/swap | Nee | Nee |
> | ringen | Nee | Nee |
> | resourceProviders | Nee | Nee |
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
> | Storage accounts/blobServices | Nee | Nee |
> | Storage accounts/fileServices | Nee | Nee |
> | Storage accounts/metricDefinitions | Nee | Nee |
> | Storage accounts/meet waarden | Nee | Nee |
> | Storage accounts/queueServices | Nee | Nee |
> | Storage accounts/Services | Nee | Nee |
> | Storage accounts/Services/diagnosticSettings | Nee | Nee |
> | Storage accounts/Services/metricDefinitions | Nee | Nee |
> | Storage accounts/Services/metrische gegevens | Nee | Nee |
> | Storage accounts/tableServices | Nee | Nee |
> | Storage accounts/vmImages | Nee | Nee |
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
> | restorePointCollections / restorePoints | Nee | Nee |
> | sharedVMImages | Ja | Ja |
> | sharedVMImages/versies | Nee | Nee |
> | momentopnamen | Ja | Ja |
> | Informatie | Ja | Ja |
> | Informatie/extensies | Ja | Ja |
> | Informatie/metricDefinitions | Nee | Nee |
> | virtualMachineScaleSets | Ja | Ja |
> | virtualMachineScaleSets/extensies | Nee | Nee |
> | virtualMachineScaleSets/networkInterfaces | Nee | Nee |
> | virtualMachineScaleSets/publicIPAddresses | Nee | Nee |
> | virtualMachineScaleSets/informatie | Nee | Nee |
> | virtualMachineScaleSets/informatie/networkInterfaces | Nee | Nee |

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
> | tenants | Nee | Nee |
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
> | registers/builds/getLogLink | Nee | Nee |
> | registers/buildTasks | Ja | Ja |
> | registers/buildTasks/stappen | Nee | Nee |
> | registers/eventGridFilters | Nee | Nee |
> | registers/generateCredentials | Nee | Nee |
> | registers/getBuildSourceUploadUrl | Nee | Nee |
> | registers/GetCredentials | Nee | Nee |
> | registers/importImage | Nee | Nee |
> | registers/queueBuild | Nee | Nee |
> | registers/regenerateCredential | Nee | Nee |
> | registers/regenerateCredentials | Nee | Nee |
> | registers/replicaties | Ja | Ja |
> | registers/uitvoeringen | Nee | Nee |
> | registers/uitvoeringen/annuleren | Nee | Nee |
> | registers/scheduleRun | Nee | Nee |
> | registers/scopeMaps | Nee | Nee |
> | registers/taskRuns | Ja | Ja |
> | registers/taken | Ja | Ja |
> | registers/tokens | Nee | Nee |
> | registers/updatePolicies | Nee | Nee |
> | registers/webhooks | Ja | Ja |
> | registers/webhooks/getCallbackConfig | Nee | Nee |
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
> | dump | Nee | Nee |
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
> | werk ruimten/dbWorkspaces | Nee | Nee |
> | werk ruimten/storageEncryption | Nee | Nee |
> | werk ruimten/virtualNetworkPeerings | Nee | Nee |

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
> | dataFactories / diagnosticSettings | Nee | Nee |
> | dataFactories / metricDefinitions | Nee | Nee |
> | dataFactorySchema | Nee | Nee |
> | factory's | Ja | Nee |
> | fabrieken/integrationRuntimes | Nee | Nee |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts/dataLakeStoreAccounts | Nee | Nee |
> | accounts/Storage accounts | Nee | Nee |
> | accounts/Storage accounts/containers | Nee | Nee |
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
> | Server | Ja | Ja |
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

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | Server | Ja | Ja |
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

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | serverGroups | Ja | Ja |
> | Server | Ja | Ja |
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
> | applicationgroups / startmenuitems | Nee | Nee |
> | hostpools | Ja | Ja |
> | hostpools / sessionhosts | Nee | Nee |
> | hostpools / sessionhosts / usersessions | Nee | Nee |
> | hostpools / usersessions | Nee | Nee |
> | werk ruimten | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Ja | Ja |
> | ElasticPools / IotHubTenants | Ja | Ja |
> | ElasticPools/IotHubTenants/securitySettings | Nee | Nee |
> | IotHubs | Ja | Ja |
> | IotHubs/eventGridFilters | Nee | Nee |
> | IotHubs/securitySettings | Nee | Nee |
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
> | lcsprojects / clouddeployments | Nee | Nee |
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
> | partnerNamespaces | Ja | Ja |
> | partnerNamespaces/eventChannels | Nee | Nee |
> | partnerRegistrations | Ja | Ja |
> | partnerTopics | Ja | Ja |
> | systemTopics | Ja | Ja |
> | systemTopics / eventSubscriptions | Nee | Nee |
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
> | functies | Nee | Nee |
> | Providers | Nee | Nee |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | inschrijven | Nee | Nee |
> | galleryitems | Nee | Nee |
> | generateartifactaccessuri | Nee | Nee |
> | myareas | Nee | Nee |
> | myareas/gebieden | Nee | Nee |
> | myareas/gebieden/gebieden | Nee | Nee |
> | myareas/gebieden/gebieden/galleryitems | Nee | Nee |
> | myareas/areas/galleryitems | Nee | Nee |
> | myareas / galleryitems | Nee | Nee |
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
> | autoManagedAccounts | Ja | Ja |
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
> | clusters/data bases/principalassignments | Nee | Nee |
> | clusters/principalassignments | Nee | Nee |
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
> | werk ruimten/reken bewerkingen | Nee | Nee |
> | werk ruimten/eventGridFilters | Nee | Nee |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | identiteit | Nee | Nee |
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
> | offerTypes/uitgevers | Nee | Nee |
> | offerTypes/uitgevers/aanbiedingen | Nee | Nee |
> | offerTypes/uitgevers/aanbiedingen/abonnementen | Nee | Nee |
> | offerTypes/uitgevers/aanbiedingen/plannen/overeenkomsten | Nee | Nee |
> | offerTypes/uitgevers/aanbiedingen/plannen/configuraties | Nee | Nee |
> | offerTypes/uitgevers/aanbiedingen/plannen/configuraties/importImage | Nee | Nee |
> | privategalleryitems | Nee | Nee |
> | privateStoreClient | Nee | Nee |
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
> | Media Services/accountFilters | Nee | Nee |
> | Media Services/assets | Nee | Nee |
> | Media Services/assets/assetFilters | Nee | Nee |
> | Media Services/contentKeyPolicies | Nee | Nee |
> | Media Services/eventGridFilters | Nee | Nee |
> | Media Services/liveEventOperations | Nee | Nee |
> | Media Services/liveEvents | Ja | Ja |
> | Media Services/liveEvents/liveOutputs | Nee | Nee |
> | Media Services/liveOutputOperations | Nee | Nee |
> | Media Services/mediaGraphs | Nee | Nee |
> | Media Services/streamingEndpointOperations | Nee | Nee |
> | Media Services/streamingEndpoints | Ja | Ja |
> | Media Services/streamingLocators | Nee | Nee |
> | Media Services/streamingPolicies | Nee | Nee |
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
> | moveCollections | Ja | Ja |
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
> | netAppAccounts / capacityPools | Ja | Nee |
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
> | dnszones/alle | Nee | Nee |
> | dnszones/CAA | Nee | Nee |
> | dnszones/CNAME | Nee | Nee |
> | dnszones/MX | Nee | Nee |
> | dnszones/NS | Nee | Nee |
> | dnszones/PTR | Nee | Nee |
> | dnszones/record sets | Nee | Nee |
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
> | networkWatchers / connectionMonitors | Ja | Nee |
> | networkWatchers/lenzen | Ja | Nee |
> | networkWatchers / pingMeshes | Ja | Nee |
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
> Voor de Azure front-deur service kunt u Tags Toep assen bij het maken van de resource, maar het bijwerken of toevoegen van labels wordt momenteel niet ondersteund.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | naam ruimten | Ja | Nee |
> | naam ruimten/notification hubs | Ja | Nee |

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
> | werk ruimten/dataExports | Nee | Nee |
> | werk ruimten/gegevens bronnen | Nee | Nee |
> | werk ruimten/linkedServices | Nee | Nee |
> | werk ruimten/privateEndpointConnectionProxies | Nee | Nee |
> | werk ruimten/privateEndpointConnections | Nee | Nee |
> | werk ruimten/privateLinkResources | Nee | Nee |
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

## <a name="microsoftprojectbabylon"></a>Micro soft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |

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
> | emergingissues | Nee | Nee |
> | events | Nee | Nee |
> | impactedResources | Nee | Nee |
> | metagegevens | Nee | Nee |
> | Meldingen | Nee | Nee |

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
> | geabonneerd | Nee | Nee |
> | tenants | Nee | Nee |

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
> | autoDismissAlertsRules | Nee | Nee |
> | automatisering | Ja | Ja |
> | AutoProvisioningSettings | Nee | Nee |
> | Ingebouwde strengste | Nee | Nee |
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
> | restrictie | Nee | Nee |
> | prijzen | Nee | Nee |
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
> | providerRegistrations / resourceTypeRegistrations | Nee | Nee |
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
> | managedInstances/data bases/backupShortTermRetentionPolicies | Nee | Nee |
> | managedInstances/data bases/schema's/tabellen/kolommen/sensitivityLabels | Nee | Nee |
> | managedInstances/data bases/vulnerabilityAssessments | Nee | Nee |
> | managedInstances/data bases/vulnerabilityAssessments/Rules/basis lijnen | Nee | Nee |
> | managedInstances / encryptionProtector | Nee | Nee |
> | managedInstances/sleutels | Nee | Nee |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nee | Nee |
> | managedInstances / vulnerabilityAssessments | Nee | Nee |
> | Server | Ja | Ja |
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
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nee | Nee |
> | SqlVirtualMachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Ja | Ja |
> | Storage accounts/blobServices | Nee | Nee |
> | Storage accounts/fileServices | Nee | Nee |
> | Storage accounts/queueServices | Nee | Nee |
> | Storage accounts/Services | Nee | Nee |
> | Storage accounts/Services/metricDefinitions | Nee | Nee |
> | Storage accounts/tableServices | Nee | Nee |
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
> | storageSyncServices/registeredServer | Nee | Nee |
> | storageSyncServices / syncGroups | Nee | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee | Nee |
> | storageSyncServices/werk stromen | Nee | Nee |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices/registeredServer | Nee | Nee |
> | storageSyncServices / syncGroups | Nee | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee | Nee |
> | storageSyncServices/werk stromen | Nee | Nee |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices/registeredServer | Nee | Nee |
> | storageSyncServices / syncGroups | Nee | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee | Nee |
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

## <a name="microsoftvnfmanager"></a>Micro soft. VnfManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | apparaten | Ja | Ja |
> | crediteur | Nee | Nee |
> | leveranciers/sku's | Nee | Nee |
> | vnfs | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt labels | Label in kosten rapport |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nee | Nee |
> | apiManagementAccounts / apiAcls | Nee | Nee |
> | apiManagementAccounts/api's | Nee | Nee |
> | apiManagementAccounts/api's/apiAcls | Nee | Nee |
> | apiManagementAccounts/api's/connectionAcls | Nee | Nee |
> | apiManagementAccounts/api's/Connections | Nee | Nee |
> | apiManagementAccounts/api's/Connections/connectionAcls | Nee | Nee |
> | apiManagementAccounts/api's/localizedDefinitions | Nee | Nee |
> | apiManagementAccounts / connectionAcls | Nee | Nee |
> | apiManagementAccounts/verbindingen | Nee | Nee |
> | billingMeters | Nee | Nee |
> | Bewijzen | Ja | Ja |
> | connectionGateways | Ja | Ja |
> | inbel | Ja | Ja |
> | customApis | Ja | Ja |
> | deletedSites | Nee | Nee |
> | hostingEnvironments | Ja | Ja |
> | hostingEnvironments / eventGridFilters | Nee | Nee |
> | hostingEnvironments / multiRolePools | Nee | Nee |
> | hostingEnvironments / workerPools | Nee | Nee |
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
> | staticSites | Ja | Ja |
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

Zie [Tags gebruiken om uw Azure-resources te organiseren](tag-resources.md)voor meer informatie over het Toep assen van tags op resources.
