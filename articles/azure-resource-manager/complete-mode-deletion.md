---
title: Verwijdering van de modus complete Azure Resource Manager per resource type
description: Toont hoe bron typen het verwijderen van de modus volt ooien in Azure Resource Manager sjablonen verwerken.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: tomfitz
ms.openlocfilehash: 268f6b7f3e7462986e6289088a0716794dc832f1
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983971"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Verwijdering van Azure-resources voor implementaties in de volledige modus

In dit artikel wordt beschreven hoe bron typen worden verwijderd wanneer dat niet het geval is in een sjabloon die in de modus voltooid wordt geïmplementeerd.

De resource typen die zijn gemarkeerd met **Ja** , worden verwijderd wanneer het type niet in de sjabloon staat die is geïmplementeerd met de modus volt ooien.

De resource typen die zijn gemarkeerd met **Nee** , worden niet automatisch verwijderd wanneer deze niet in de sjabloon staan; ze worden echter verwijderd als de bovenliggende resource wordt verwijderd. Zie [Azure Resource Manager implementatie modi](deployment-modes.md)voor een volledige beschrijving van het gedrag.

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
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
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
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
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
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | DomainServices | Ja |
> | DomainServices/oucontainer | Nee |
> | DomainServices/ReplicaSets | Ja |

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
> | middelen | Nee |
> | anonymousapiusers | Nee |
> | configuratie | Nee |
> | logs | Nee |
> | rapporten | Nee |
> | servicehealthmetrics | Nee |
> | services | Nee |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | configuraties | Nee |
> | generateRecommendations | Nee |
> | metagegevens | Nee |
> | aanbevelingen | Nee |
> | onderdrukkingen | Nee |

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
> | Feedback | Nee |
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
> | configurationStores/eventGridFilters | Nee |

## <a name="microsoftappplatform"></a>Micro soft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Lente | Ja |

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
> | vergren delingen | Nee |
> | machtigingen | Nee |
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
> | automationAccounts/configurations | Ja |
> | automationAccounts/jobs | Nee |
> | automationAccounts/runbooks | Ja |
> | automationAccounts/softwareUpdateConfigurations | Nee |
> | automationAccounts/webhooks | Nee |

## <a name="microsoftazconfig"></a>Micro soft. Azconfig

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | configurationStores | Ja |
> | configurationStores/eventGridFilters | Nee |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | verschillend | Nee |
> | omgevingen/accounts | Nee |
> | omgevingen/accounts/naam ruimten | Nee |
> | omgevingen/accounts/naam ruimten/configuraties | Nee |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | b2cDirectories | Ja |
> | b2ctenants | Nee |

## <a name="microsoftazuredata"></a>Micro soft. Azureworden

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | hybridDataManagers | Ja |
> | postgresInstances | Ja |
> | sqlBigDataClusters | Ja |
> | sqlInstances | Ja |
> | sqlServerRegistrations | Ja |
> | sqlServerRegistrations/sqlServers | Nee |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | registraties | Ja |
> | registrations/customerSubscriptions | Nee |
> | registraties/producten | Nee |

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
> | billingAccounts/overeenkomsten | Nee |
> | billingAccounts/billingPermissions | Nee |
> | billingAccounts/billingProfiles | Nee |
> | billingAccounts/billingProfiles/billingPermissions | Nee |
> | billingAccounts/billingProfiles/billingRoleAssignments | Nee |
> | billingAccounts/billingProfiles/billingRoleDefinitions | Nee |
> | billingAccounts/billingProfiles/billingSubscriptions | Nee |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | Nee |
> | billingAccounts/billingProfiles/klanten | Nee |
> | billingAccounts/billingProfiles/invoices | Nee |
> | billingAccounts/billingProfiles/invoices/pricesheet | Nee |
> | billingAccounts/billingProfiles/invoiceSections | Nee |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | Nee |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | Nee |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | Nee |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | Nee |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | Nee |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | Nee |
> | billingAccounts/billingProfiles/invoiceSections/Products | Nee |
> | billingAccounts/billingProfiles/invoiceSections/Products/overdracht | Nee |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Nee |
> | billingAccounts/billingProfiles/invoiceSections/trans acties | Nee |
> | billingAccounts/billingProfiles/invoiceSections/transfers | Nee |
> | billingAccounts/BillingProfiles/patchOperations | Nee |
> | billingAccounts/billingProfiles/paymentMethods | Nee |
> | billingAccounts/billingProfiles/policies | Nee |
> | billingAccounts/billingProfiles/pricesheet | Nee |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Nee |
> | billingAccounts/billingProfiles/products | Nee |
> | billingAccounts/billingProfiles/transactions | Nee |
> | billingAccounts/billingRoleAssignments | Nee |
> | billingAccounts/billingRoleDefinitions | Nee |
> | billingAccounts/billingSubscriptions | Nee |
> | billingAccounts/createBillingRoleAssignment | Nee |
> | billingAccounts/createInvoiceSectionOperations | Nee |
> | billingAccounts/klanten | Nee |
> | billingAccounts/klanten/billingSubscriptions | Nee |
> | billingAccounts/klanten/initiateTransfer | Nee |
> | billingAccounts/klanten/producten | Nee |
> | billingAccounts/klanten/trans acties | Nee |
> | billingAccounts/klanten/overdrachten | Nee |
> | billingAccounts/departments | Nee |
> | billingAccounts/enrollmentAccounts | Nee |
> | billingAccounts/invoices | Nee |
> | billingAccounts/invoiceSections | Nee |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | Nee |
> | billingAccounts/invoiceSections/billingSubscriptions | Nee |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Nee |
> | billingAccounts/invoiceSections/verhoogde bevoegdheid | Nee |
> | billingAccounts/invoiceSections/initiateTransfer | Nee |
> | billingAccounts/invoiceSections/patchOperations | Nee |
> | billingAccounts/invoiceSections/productMoveOperations | Nee |
> | billingAccounts/invoiceSections/products | Nee |
> | billingAccounts/invoiceSections/producten/overdracht | Nee |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Nee |
> | billingAccounts/invoiceSections/trans acties | Nee |
> | billingAccounts/invoiceSections/transfers | Nee |
> | billingAccounts/lineOfCredit | Nee |
> | billingAccounts/patchOperations | Nee |
> | billingAccounts/paymentMethods | Nee |
> | billingAccounts/producten | Nee |
> | billingAccounts/transactions | Nee |
> | billingPeriods | Nee |
> | BillingPermissions | Nee |
> | billingProperty | Nee |
> | BillingRoleAssignments | Nee |
> | BillingRoleDefinitions | Nee |
> | CreateBillingRoleAssignment | Nee |
> | afdeling | Nee |
> | enrollmentAccounts | Nee |
> | factureer | Nee |
> | Making | Nee |
> | transfers/acceptTransfer | Nee |
> | overdrachten/declineTransfer | Nee |
> | overdrachten/operationStatus | Nee |
> | overdrachten/validateTransfer | Nee |
> | validateAddress | Nee |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | mapApis | Ja |
> | updateCommunicationPreference | Nee |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | BizTalk | Ja |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | blockchainMembers | Ja |
> | kijkers | Ja |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | blueprintAssignments | Nee |
> | blueprintAssignments/assignmentOperations | Nee |
> | blueprintAssignments/operations | Nee |
> | blauw drukken | Nee |
> | blauw drukken/artefacten | Nee |
> | blauw drukken/versies | Nee |
> | blauw drukken/versies/artefacten | Nee |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | botServices | Ja |
> | botServices/kanalen | Nee |
> | botServices/verbindingen | Nee |
> | Talen | Nee |
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
> | catalogi | Nee |
> | commercialReservationOrders | Nee |
> | exchange | Nee |
> | placePurchaseOrder | Nee |
> | reservationOrders | Nee |
> | reservationOrders/calculateRefund | Nee |
> | reservationOrders/samen voegen | Nee |
> | reservationOrders/reserve ringen | Nee |
> | reservationOrders/reserve ringen/revisies | Nee |
> | reservationOrders/retour neren | Nee |
> | reservationOrders/splitsen | Nee |
> | reservationOrders/swap | Nee |
> | reserveringen | Nee |
> | bronnen | Nee |
> | validateReservationOrder | Nee |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nee |
> | CdnWebApplicationFirewallPolicies | Ja |
> | edgenodes | Nee |
> | profiles | Ja |
> | profielen/eind punten | Ja |
> | profielen/eind punten/customdomains | Nee |
> | profielen/eind punten/oorsprong | Nee |
> | validateProbe | Nee |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | certificateOrders | Ja |
> | certificateOrders/certificaten | Nee |
> | validateCertificateRegistrationInformation | Nee |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Bieden | Nee |
> | Domein naam | Ja |
> | Domein naam/mogelijkheden | Nee |
> | Domein naam/internalLoadBalancers | Nee |
> | Domein naam/serviceCertificates | Nee |
> | Domein naam/sleuven | Nee |
> | Domein naam/sleuven/rollen | Nee |
> | Domein naam/sleuven/rollen/metricDefinitions | Nee |
> | Domein naam/sleuven/rollen/metrieken | Nee |
> | moveSubscriptionResources | Nee |
> | operatingSystemFamilies | Nee |
> | operatingSystems | Nee |
> | quotas | Nee |
> | resourceTypes | Nee |
> | validateSubscriptionMoveAvailability | Nee |
> | Informatie | Ja |
> | Informatie/diagnosticSettings | Nee |
> | Informatie/metricDefinitions | Nee |
> | Informatie/meet waarden | Nee |

## <a name="microsoftclassicinfrastructuremigrate"></a>Micro soft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | classicInfrastructureResources | Nee |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Bieden | Nee |
> | expressRouteCrossConnections | Nee |
> | expressRouteCrossConnections/peerings | Nee |
> | gatewaySupportedDevices | Nee |
> | networkSecurityGroups | Ja |
> | quotas | Nee |
> | reservedIps | Ja |
> | virtualNetworks | Ja |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nee |
> | virtualNetworks/virtualNetworkPeerings | Nee |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Bieden | Nee |
> | schijven | Nee |
> | afbeeldingen | Nee |
> | osImages | Nee |
> | osPlatformImages | Nee |
> | publicImages | Nee |
> | quotas | Nee |
> | storageAccounts | Ja |
> | storageAccounts/blobServices | Nee |
> | storageAccounts/fileServices | Nee |
> | Storage accounts/metricDefinitions | Nee |
> | Storage accounts/meet waarden | Nee |
> | storageAccounts/queueServices | Nee |
> | storageAccounts/services | Nee |
> | storageAccounts/services/diagnosticSettings | Nee |
> | Storage accounts/Services/metricDefinitions | Nee |
> | Storage accounts/Services/metrische gegevens | Nee |
> | storageAccounts/tableServices | Nee |
> | storageAccounts/vmImages | Nee |
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
> | Availability sets | Ja |
> | diskEncryptionSets | Ja |
> | schijven | Ja |
> | Galerij | Ja |
> | galerieën/toepassingen | Ja |
> | galerieën/toepassingen/versies | Ja |
> | galerieën/afbeeldingen | Ja |
> | galerieën/afbeeldingen/versies | Ja |
> | hostGroups | Ja |
> | hostGroups/hosts | Ja |
> | afbeeldingen | Ja |
> | proximityPlacementGroups | Ja |
> | restorePointCollections | Ja |
> | restorePointCollections/restorePoints | Nee |
> | sharedVMImages | Ja |
> | sharedVMImages/versies | Ja |
> | momentopnamen | Ja |
> | Informatie | Ja |
> | Informatie/extensies | Ja |
> | Informatie/metricDefinitions | Nee |
> | Informatie/scriptJobs | Nee |
> | Informatie/softwareUpdateDeployments | Nee |
> | virtualMachineScaleSets | Ja |
> | virtualMachineScaleSets/extensions | Nee |
> | virtualMachineScaleSets/networkInterfaces | Nee |
> | virtualMachineScaleSets/publicIPAddresses | Nee |
> | virtualMachineScaleSets/virtualMachines | Nee |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Nee |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | AggregatedCost | Nee |
> | Tegoeden | Nee |
> | Budgetten | Nee |
> | Kosten | Nee |
> | CostTags | Nee |
> | aanvragen | Nee |
> | gebeurtenissen | Nee |
> | Prognoses | Nee |
> | extra | Nee |
> | Markt plaatsen | Nee |
> | Pricesheets | Nee |
> | producten | Nee |
> | ReservationDetails | Nee |
> | ReservationRecommendations | Nee |
> | ReservationSummaries | Nee |
> | ReservationTransactions | Nee |
> | Labels | Nee |
> | tenants | Nee |
> | Gebruiksrechtovereenkomst | Nee |
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
> | registers | Ja |
> | registers/builds | Nee |
> | registers/builds/annuleren | Nee |
> | registries/builds/getLogLink | Nee |
> | registers/buildTasks | Ja |
> | registers/buildTasks/stappen | Nee |
> | registries/eventGridFilters | Nee |
> | registers/getBuildSourceUploadUrl | Nee |
> | registers/GetCredentials | Nee |
> | registries/importImage | Nee |
> | registries/queueBuild | Nee |
> | registers/regenerateCredential | Nee |
> | registers/regenerateCredentials | Nee |
> | registers/replicaties | Ja |
> | registers/uitvoeringen | Nee |
> | registers/uitvoeringen/annuleren | Nee |
> | registers/scheduleRun | Nee |
> | registers/taken | Ja |
> | registries/updatePolicies | Nee |
> | registers/webhooks | Ja |
> | registries/webhooks/getCallbackConfig | Nee |
> | registers/webhooks/ping | Nee |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | containerServices | Ja |
> | managedClusters | Ja |
> | openShiftManagedClusters | Ja |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingen | Ja |
> | updateCommunicationPreference | Nee |

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
> | Afdelingen | Nee |
> | Dimensies | Nee |
> | enrollmentAccounts | Nee |
> | Exports | Nee |
> | ExternalBillingAccounts | Nee |
> | ExternalBillingAccounts/waarschuwingen | Nee |
> | ExternalBillingAccounts/dimensies | Nee |
> | ExternalBillingAccounts/prognose | Nee |
> | ExternalBillingAccounts/query | Nee |
> | ExternalSubscriptions | Nee |
> | ExternalSubscriptions/waarschuwingen | Nee |
> | ExternalSubscriptions/dimensies | Nee |
> | ExternalSubscriptions/prognose | Nee |
> | ExternalSubscriptions/query | Nee |
> | Prognose | Nee |
> | Query’s uitvoeren | Nee |
> | inschrijving | Nee |
> | Reportconfigs | Nee |
> | Rapporten | Nee |
> | Instellingen | Nee |
> | showbackRules | Nee |
> | Weergaven | Nee |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | aanvragen | Nee |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | lidkoppelingen | Nee |
> | resourceProviders | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | taken | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | werkruimten | Ja |
> | workspaces/virtualNetworkPeerings | Nee |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | catalogi | Ja |
> | datacatalogs | Ja |
> | datacatalogs/gegevens bronnen | Nee |
> | datacatalogs/gegevens bronnen/scans | Nee |
> | datacatalogs/gegevens bronnen/scans/gegevens sets | Nee |
> | datacatalogs/gegevens bronnen/scans/triggers | Nee |
> | datacatalogs/scantargets | Nee |
> | datacatalogs/scantargets/gegevens sets | Nee |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dataFactories | Ja |
> | dataFactories/diagnosticSettings | Nee |
> | dataFactories/metricDefinitions | Nee |
> | dataFactorySchema | Nee |
> | factory's | Ja |
> | fabrieken/integrationRuntimes | Nee |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |
> | accounts/dataLakeStoreAccounts | Nee |
> | accounts/storageAccounts | Nee |
> | accounts/storageAccounts/containers | Nee |
> | accounts/transferAnalyticsUnits | Nee |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |
> | accounts/eventGridFilters | Nee |
> | accounts/firewallRules | Nee |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | services | Ja |
> | Services/projecten | Ja |
> | sleuf | Ja |

## <a name="microsoftdatashare"></a>Micro soft. DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |
> | accounts/shares | Nee |
> | accounts/shares/gegevens sets | Nee |
> | accounts/shares/uitnodigingen | Nee |
> | accounts/shares/providersharesubscriptions | Nee |
> | accounts/shares/synchronizationSettings | Nee |
> | accounts/sharesubscriptions | Nee |
> | accounts/sharesubscriptions/consumerSourceDataSets | Nee |
> | accounts/sharesubscriptions/datasetmappings | Nee |
> | accounts/sharesubscriptions/triggers | Nee |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | servers | Ja |
> | servers/adviseurs | Nee |
> | servers/queryTexts | Nee |
> | servers/recoverableServers | Nee |
> | servers/topQueryStatistics | Nee |
> | servers/virtualNetworkRules | Nee |
> | servers/waitStatistics | Nee |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | servers | Ja |
> | servers/adviseurs | Nee |
> | servers/queryTexts | Nee |
> | servers/recoverableServers | Nee |
> | servers/topQueryStatistics | Nee |
> | servers/virtualNetworkRules | Nee |
> | servers/waitStatistics | Nee |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | serverGroups | Ja |
> | servers | Ja |
> | servers/adviseurs | Nee |
> | servers/queryTexts | Nee |
> | servers/recoverableServers | Nee |
> | servers/topQueryStatistics | Nee |
> | servers/virtualNetworkRules | Nee |
> | servers/waitStatistics | Nee |
> | serversv2 | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | artifactSources | Ja |
> | implementaties | Ja |
> | serviceTopologies | Ja |
> | serviceTopologies/Services | Ja |
> | serviceTopologies/Services/serviceUnits | Ja |
> | stappen | Ja |

## <a name="microsoftdesktopvirtualization"></a>Micro soft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | applicationgroups | Ja |
> | applicationgroups/toepassingen | Nee |
> | applicationgroups/startmenuitems | Nee |
> | hostpools | Ja |
> | hostpools/sessionhosts | Nee |
> | hostpools/sessionhosts/usersessions | Nee |
> | hostpools/usersessions | Nee |
> | werkruimten | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | elasticPools | Ja |
> | ElasticPools/IotHubTenants | Ja |
> | IotHubs | Ja |
> | IotHubs/eventGridFilters | Nee |
> | ProvisioningServices | Ja |
> | gebruik | Nee |

## <a name="microsoftdevops"></a>Micro soft. DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | pijplijnen | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Fungeren | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | labcenters | Ja |
> | Labs | Ja |
> | Labs/omgevingen | Ja |
> | Labs-serviceRunners | Ja |
> | Labs-informatie | Ja |
> | schema's | Ja |

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
> | domeinen | Ja |
> | domeinen/domainOwnershipIdentifiers | Nee |
> | generateSsoRequest | Nee |
> | topLevelDomains | Nee |
> | validateDomainRegistrationInformation | Nee |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | lcsprojects | Nee |
> | lcsprojects/clouddeployments | Nee |
> | lcsprojects/connectors | Nee |

## <a name="microsoftenterpriseknowledgegraph"></a>Micro soft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | domeinen | Ja |
> | domeinen/onderwerpen | Nee |
> | eventSubscriptions | Nee |
> | extensionTopics | Nee |
> | onderwerp | Ja |
> | topicTypes | Nee |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | clusters | Ja |
> | naamruimten | Ja |
> | naam ruimten/authorizationrules | Nee |
> | naam ruimten/disasterrecoveryconfigs | Nee |
> | naam ruimten/Event hubs | Nee |
> | naam ruimten/Event hubs/authorizationrules | Nee |
> | naam ruimten/Event hubs/consumergroups | Nee |
> | naam ruimten/networkrulesets | Nee |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | database | Nee |
> | Providers | Nee |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Inge | Nee |
> | galleryitems | Nee |
> | generateartifactaccessuri | Nee |
> | myareas | Nee |
> | myareas/areas | Nee |
> | myareas/areas/areas | Nee |
> | myareas/gebieden/gebieden/galleryitems | Nee |
> | myareas/areas/galleryitems | Nee |
> | myareas/galleryitems | Nee |
> | inschrijving | Nee |
> | bronnen | Nee |
> | retrieveresourcesbyid | Nee |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |

## <a name="microsoftguestconfiguration"></a>Micro soft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | autoManagedVmConfigurationProfiles | Ja |
> | guestConfigurationAssignments | Nee |
> | Software | Nee |
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
> | clusters/toepassingen | Nee |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | computers | Ja |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dataManagers | Ja |

## <a name="microsofthydra"></a>Micro soft. Hydra

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Materialen | Ja |
> | networkScopes | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | taken | Ja |

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
> | Graph | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | deletedVaults | Nee |
> | hsmPools | Ja |
> | kluizen | Ja |
> | kluizen/accessPolicies | Nee |
> | kluizen/eventGridFilters | Nee |
> | kluizen/geheimen | Nee |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | clusters | Ja |
> | clusters/attacheddatabaseconfigurations | Nee |
> | clusters/data bases | Nee |
> | clusters/data bases/dataConnections | Nee |
> | clusters/data bases/eventhubconnections | Nee |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | labaccounts | Ja |
> | Gebruikers | Nee |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | hostingEnvironments | Ja |
> | integrationAccounts | Ja |
> | integrationServiceEnvironments | Ja |
> | integrationServiceEnvironments/Beheerdeapi's | Ja |
> | isolatedEnvironments | Ja |
> | stroom | Ja |

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
> | werkruimten | Ja |
> | workspaces/computes | Nee |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Identiteiten | Nee |
> | userAssignedIdentities | Ja |

## <a name="microsoftmanagedservices"></a>Micro soft. ManagedServices

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
> | bronnen | Nee |
> | startTenantBackfill | Nee |
> | tenantBackfillStatus | Nee |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |
> | accounts/eventGridFilters | Nee |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | over | Nee |
> | offerTypes | Nee |
> | offerTypes/publishers | Nee |
> | offerTypes/publishers/offers | Nee |
> | offerTypes/publishers/offers/plans | Nee |
> | offerTypes/publishers/offers/plans/agreements | Nee |
> | offerTypes/publishers/offers/plans/configs | Nee |
> | offerTypes/publishers/offers/plans/configs/importImage | Nee |
> | privategalleryitems | Nee |
> | producten | Nee |
> | uitgevers | Nee |
> | uitgevers/aanbiedingen | Nee |
> | uitgevers/aanbiedingen/wijzigingen | Nee |

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
> | gesloten | Nee |
> | offertypes | Nee |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Media Services | Ja |
> | mediaservices/accountFilters | Nee |
> | Media Services/assets | Nee |
> | mediaservices/assets/assetFilters | Nee |
> | Media Services/contentKeyPolicies | Nee |
> | mediaservices/eventGridFilters | Nee |
> | mediaservices/liveEventOperations | Nee |
> | Media Services/liveEvents | Ja |
> | Media Services/liveEvents/liveOutputs | Nee |
> | mediaservices/liveOutputOperations | Nee |
> | mediaservices/streamingEndpointOperations | Nee |
> | mediaservices/streamingEndpoints | Ja |
> | mediaservices/streamingLocators | Nee |
> | mediaservices/streamingPolicies | Nee |
> | Media Services/trans formaties | Nee |
> | Media Services/trans formaties/taken | Nee |

## <a name="microsoftmicroservices4spring"></a>Micro soft. Microservices4Spring

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
> | projecten | Ja |

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
> | netAppAccounts/capacityPools | Ja |
> | netAppAccounts/capacityPools/volumes | Ja |
> | netAppAccounts/capacityPools/volumes/mountTargets | Ja |
> | netAppAccounts/capacityPools/volumes/moment opnamen | Ja |

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
> | verbindingen | Ja |
> | ddosCustomPolicies | Ja |
> | ddosProtectionPlans | Ja |
> | dnsOperationStatuses | Nee |
> | dnszones | Ja |
> | dnszones/A | Nee |
> | dnszones/AAAA | Nee |
> | dnszones/all | Nee |
> | dnszones/CAA | Nee |
> | dnszones/CNAME | Nee |
> | dnszones/MX | Nee |
> | dnszones/NS | Nee |
> | dnszones/PTR | Nee |
> | dnszones/recordsets | Nee |
> | dnszones/SOA | Nee |
> | dnszones/SRV | Nee |
> | dnszones/TXT | Nee |
> | expressRouteCircuits | Ja |
> | expressRouteCrossConnections | Ja |
> | expressRouteGateways | Ja |
> | expressRoutePorts | Ja |
> | expressRouteServiceProviders | Nee |
> | firewallPolicies | Ja |
> | ingangen | Ja |
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
> | networkWatchers/connectionMonitors | Ja |
> | networkWatchers/lenses | Ja |
> | networkWatchers/pingMeshes | Ja |
> | p2sVpnGateways | Ja |
> | privateDnsOperationStatuses | Nee |
> | privateDnsZones | Ja |
> | privateDnsZones/A | Nee |
> | privateDnsZones/AAAA | Nee |
> | privateDnsZones/alle | Nee |
> | privateDnsZones/CNAME | Nee |
> | privateDnsZones/MX | Nee |
> | privateDnsZones/PTR | Nee |
> | privateDnsZones/SOA | Nee |
> | privateDnsZones/SRV | Nee |
> | privateDnsZones/TXT | Nee |
> | privateDnsZones/virtualNetworkLinks | Ja |
> | privateEndpoints | Ja |
> | privateLinkServices | Ja |
> | publicIPAddresses | Ja |
> | publicIPPrefixes | Ja |
> | routeFilters | Ja |
> | routeTables | Ja |
> | secureGateways | Ja |
> | serviceEndpointPolicies | Ja |
> | trafficManagerGeographicHierarchies | Nee |
> | trafficmanagerprofiles | Ja |
> | trafficmanagerprofiles/heatMaps | Nee |
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
> | naamruimten | Ja |
> | namespaces/notificationHubs | Ja |

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
> | werkruimten | Ja |
> | werk ruimten/gegevens bronnen | Nee |
> | workspaces/linkedServices | Nee |
> | werk ruimten/query | Nee |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | managementassociations | Nee |
> | managementconfigurations | Ja |
> | oplossingen | Ja |
> | weergaven | Ja |

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
> | policyStates | Nee |
> | policyTrackedResources | Nee |
> | herstel | Nee |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | consoles | Nee |
> | Dash boards | Ja |
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
> | beschikt | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | backupProtectedItems | Nee |
> | kluizen | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | naamruimten | Ja |
> | naam ruimten/authorizationrules | Nee |
> | naam ruimten/hybridconnections | Nee |
> | naam ruimten/hybridconnections/authorizationrules | Nee |
> | naam ruimten/wcfrelays | Nee |
> | naam ruimten/wcfrelays/authorizationrules | Nee |

## <a name="microsoftremoteapp"></a>Micro soft. RemoteApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Nee |
> | Reeksen | Ja |
> | verzamelingen/toepassingen | Nee |
> | verzamelingen/securityprincipals | Nee |
> | templateImages | Nee |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | query's | Ja |
> | resourceChangeDetails | Nee |
> | resourceChanges | Nee |
> | bronnen | Nee |
> | resourcesHistory | Nee |
> | subscriptionsStatus | Nee |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | availabilityStatuses | Nee |
> | childAvailabilityStatuses | Nee |
> | childResources | Nee |
> | gebeurtenissen | Nee |
> | impactedResources | Nee |
> | metagegevens | Nee |
> | meldingen | Nee |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | implementaties | Nee |
> | implementaties/bewerkingen | Nee |
> | koppelen | Nee |
> | notifyResourceJobs | Nee |
> | Providers | Nee |
> | resourceGroups | Nee |
> | bronnen | Nee |
> | abonnementen | Nee |
> | abonnementen/providers | Nee |
> | abonnementen/resourceGroups | Nee |
> | abonnementen/ResourceGroups/resources | Nee |
> | abonnementen/resources | Nee |
> | abonnementen/TagName | Nee |
> | subscriptions/tagNames/tagValues | Nee |
> | codes | Nee |
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
> | evaluaties | Nee |
> | AutoProvisioningSettings | Nee |
> | Ingebouwde strengste | Nee |
> | dataCollectionAgents | Nee |
> | deviceSecurityGroups | Nee |
> | discoveredSecuritySolutions | Nee |
> | externalSecuritySolutions | Nee |
> | InformationProtectionPolicies | Nee |
> | iotSecuritySolutions | Ja |
> | iotSecuritySolutions/analyticsModels | Nee |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | Nee |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | Nee |
> | jitNetworkAccessPolicies | Nee |
> | networkData | Nee |
> | playbookConfigurations | Ja |
> | beleidsregels | Nee |
> | prijzen | Nee |
> | regulatoryComplianceStandards | Nee |
> | regulatoryComplianceStandards/regulatoryComplianceControls | Nee |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | Nee |
> | securityContacts | Nee |
> | securitySolutions | Nee |
> | securitySolutionsReferenceData | Nee |
> | securityStatuses | Nee |
> | securityStatusesSummaries | Nee |
> | serverVulnerabilityAssessments | Nee |
> | instellingen | Nee |
> | taken | Nee |
> | topologieën | Nee |
> | workspaceSettings | Nee |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | diagnosticSettings | Nee |
> | diagnosticSettingsCategories | Nee |

## <a name="microsoftsecurityinsights"></a>Micro soft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | aggregaties | Nee |
> | alertRules | Nee |
> | alertRuleTemplates | Nee |
> | Wijzer | Nee |
> | meldingen | Nee |
> | dataConnectors | Nee |
> | entiteiten | Nee |
> | entityQueries | Nee |
> | officeConsents | Nee |
> | instellingen | Nee |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | naamruimten | Ja |
> | naam ruimten/authorizationrules | Nee |
> | naam ruimten/disasterrecoveryconfigs | Nee |
> | naam ruimten/eventgridfilters | Nee |
> | naam ruimten/networkrulesets | Nee |
> | naam ruimten/wacht rijen | Nee |
> | naam ruimten/wacht rijen/authorizationrules | Nee |
> | naam ruimten/onderwerpen | Nee |
> | naam ruimten/onderwerpen/authorizationrules | Nee |
> | naam ruimten/onderwerpen/abonnementen | Nee |
> | naam ruimten/onderwerpen/abonnementen/regels | Nee |
> | premiumMessagingRegions | Nee |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingen | Ja |
> | clusters | Ja |
> | clusters/toepassingen | Nee |
> | containerGroups | Ja |
> | containerGroupSets | Ja |
> | edgeclusters | Ja |
> | edgeclusters/toepassingen | Nee |
> | netwerken | Ja |
> | secretstores | Ja |
> | secretstores/certificaten | Nee |
> | secretstores/geheimen | Nee |
> | volumes | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingen | Ja |
> | containerGroups | Ja |
> | gateways | Ja |
> | netwerken | Ja |
> | geheimen | Ja |
> | volumes | Ja |

## <a name="microsoftservices"></a>Micro soft. Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | providerRegistrations | Nee |
> | providerRegistrations/resourceTypeRegistrations | Nee |
> | implementaties | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | SignalR | Ja |
> | Signaal sterkte/eventGridFilters | Nee |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | SiteRecoveryVault | Ja |

## <a name="microsoftsoftwareplan"></a>Micro soft. SoftwarePlan

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
> | managedInstances/data bases | Ja |
> | managedInstances/databases/backupShortTermRetentionPolicies | Nee |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Nee |
> | managedInstances/data bases/vulnerabilityAssessments | Nee |
> | managedInstances/data bases/vulnerabilityAssessments/Rules/basis lijnen | Nee |
> | managedInstances/encryptionProtector | Nee |
> | managedInstances/sleutels | Nee |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nee |
> | managedInstances/vulnerabilityAssessments | Nee |
> | servers | Ja |
> | servers/beheerders | Nee |
> | servers/communicationLinks | Nee |
> | servers/data bases | Ja |
> | servers/encryptionProtector | Nee |
> | servers/firewallRules | Nee |
> | servers/sleutels | Nee |
> | servers/restorableDroppedDatabases | Nee |
> | servers/serviceobjectives | Nee |
> | servers/tdeCertificates | Nee |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Ja |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Nee |
> | SqlVirtualMachines | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageAccounts | Ja |
> | storageAccounts/blobServices | Nee |
> | storageAccounts/fileServices | Nee |
> | storageAccounts/queueServices | Nee |
> | storageAccounts/services | Nee |
> | Storage accounts/Services/metricDefinitions | Nee |
> | storageAccounts/tableServices | Nee |
> | gebruik | Nee |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | caches | Ja |
> | caches/storageTargets | Nee |
> | usageModels | Nee |

## <a name="microsoftstoragereplication"></a>Micro soft. StorageReplication

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | replicationGroups | Nee |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices/registeredServers | Nee |
> | storageSyncServices/syncGroups | Nee |
> | storageSyncServices/syncGroups/cloudEndpoints | Nee |
> | storageSyncServices/syncGroups/serverEndpoints | Nee |
> | storageSyncServices/werk stromen | Nee |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices/registeredServers | Nee |
> | storageSyncServices/syncGroups | Nee |
> | storageSyncServices/syncGroups/cloudEndpoints | Nee |
> | storageSyncServices/syncGroups/serverEndpoints | Nee |
> | storageSyncServices/werk stromen | Nee |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices/registeredServers | Nee |
> | storageSyncServices/syncGroups | Nee |
> | storageSyncServices/syncGroups/cloudEndpoints | Nee |
> | storageSyncServices/syncGroups/serverEndpoints | Nee |
> | storageSyncServices/werk stromen | Nee |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | leider | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | streamingjobs | Ja |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | annuleren | Nee |
> | CreateSubscription | Nee |
> | inschakelen | Nee |
> | naam wijzigen | Nee |
> | SubscriptionDefinitions | Nee |
> | SubscriptionOperations | Nee |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | verschillend | Ja |
> | omgevingen/accessPolicies | Nee |
> | omgevingen/eventsources | Ja |
> | omgevingen/referenceDataSets | Ja |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Ja |
> | dedicatedCloudServices | Ja |
> | Informatie | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | apiManagementAccounts | Nee |
> | apiManagementAccounts/apiAcls | Nee |
> | apiManagementAccounts/apis | Nee |
> | apiManagementAccounts/apis/apiAcls | Nee |
> | apiManagementAccounts/apis/connectionAcls | Nee |
> | apiManagementAccounts/api's/Connections | Nee |
> | apiManagementAccounts/apis/connections/connectionAcls | Nee |
> | apiManagementAccounts/api's/localizedDefinitions | Nee |
> | apiManagementAccounts/connectionAcls | Nee |
> | apiManagementAccounts/verbindingen | Nee |
> | billingMeters | Nee |
> | certificaten | Ja |
> | connectionGateways | Ja |
> | verbindingen | Ja |
> | customApis | Ja |
> | deletedSites | Nee |
> | functies | Nee |
> | hostingEnvironments | Ja |
> | hostingEnvironments/multiRolePools | Nee |
> | hostingEnvironments/workerPools | Nee |
> | publishingUsers | Nee |
> | aanbevelingen | Nee |
> | resourceHealthMetadata | Nee |
> | Runtimes | Nee |
> | Server farms | Ja |
> | Server farms/eventGridFilters | Nee |
> | sites | Ja |
> | sites/configuratie  | Nee |
> | sites/eventGridFilters | Nee |
> | sites/hostNameBindings | Nee |
> | sites/networkConfig | Nee |
> | sites/premieraddons | Ja |
> | sites/sleuven | Ja |
> | sites/sleuven/eventGridFilters | Nee |
> | sites/sleuven/hostNameBindings | Nee |
> | sites/sleuven/networkConfig | Nee |
> | sourceControls | Nee |
> | subelementid | Nee |
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
> | Materialen | Nee |
> | componentsSummary | Nee |
> | monitorInstances | Nee |
> | monitorInstancesSummary | Nee |
> | monitors | Nee |
> | notificationSettings | Nee |

## <a name="next-steps"></a>Volgende stappen

Down load [complete-mode-deletion. CSV](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)om dezelfde gegevens op te halen als een bestand met door komma's gescheiden waarden.
