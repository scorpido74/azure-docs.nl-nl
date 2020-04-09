---
title: Ondersteuning voor resources taggen
description: Hiermee ziet u welke Azure-brontypen tags ondersteunen. Biedt details voor alle Azure-services.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: b8b1c0b738bb8b94ee53433141f1ae3dbbb3f942
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982324"
---
# <a name="tag-support-for-azure-resources"></a>Tagondersteuning voor Azure-bronnen
In dit artikel wordt beschreven of een resourcetype [tags](tag-resources.md)ondersteunt. De kolom met **de labels Ondersteunt geeft** aan of het resourcetype een eigenschap voor de tag heeft. De kolom met het label **Tag in het kostenrapport** geeft aan of dat resourcetype de tag doorgeeft aan het kostenrapport. U de kosten bekijken op tags in de [kostenanalyse van kostenbeheer](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) en de [Azure-factuur en gegevens over het dagelijks gebruik.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)

Download [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)om dezelfde gegevens te krijgen als een bestand met door komma's gescheiden waarden.

Ga naar de naamruimte van een resourceprovider:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsBeheer](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig (Microsoft.Azconfig)](#microsoftazconfig)
> - [Microsoft.Azure.Genève](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn (Microsoft.Cdn)](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Verbruik](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
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
> - [Microsoft.DataMigratie](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualisatie](#microsoftdesktopvirtualization)
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
> - [Microsoft.Falcon (Microsoft.Falcon)](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics (Microsoft.Genomics)](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra (Microsoft.Hydra)](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.ioTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes (Microsoft.Kubernetes)](#microsoftkubernetes)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migreren](#microsoftmigrate)
> - [Microsoft.MixedReality Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.Notebooks](#microsoftnotebooks)
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
> - [Microsoft.ProjectBabylon (Microsoft.ProjectBabylon)](#microsoftprojectbabylon)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
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
> - [Microsoft.SpoolService](#microsoftspoolservice)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplicatie](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Abonnement](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | DomainServices | Ja | Ja |
> | DomainServices / oucontainer | Nee | Nee |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | ondersteuningProviders | Nee | Nee |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Nee | Nee |
> | voegt services toe | Nee | Nee |
> | Agenten | Nee | Nee |
> | anonieme apigebruikers | Nee | Nee |
> | configuratie | Nee | Nee |
> | logboeken | Nee | Nee |
> | rapporten | Nee | Nee |
> | servicehealthmetrics | Nee | Nee |
> | services | Nee | Nee |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Configuraties | Nee | Nee |
> | aanbevelingen genereren | Nee | Nee |
> | metagegevens | Nee | Nee |
> | aanbevelingen | Nee | Nee |
> | onderdrukkingen | Nee | Nee |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsBeheer

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | actieRegels | Ja | Ja |
> | waarschuwingen | Nee | Nee |
> | waarschuwingenLijst | Nee | Nee |
> | alertsMetaData | Nee | Nee |
> | waarschuwingenSamenvatting | Nee | Nee |
> | waarschuwingenOverzichtslijst | Nee | Nee |
> | smartDetectorAlertRegels | Ja | Ja |
> | smartGroups | Nee | Nee |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Servers | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | rapportFeedback | Nee | Nee |
> | service | Ja | Ja |
> | validateServiceName | Nee | Nee |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Ja |
> | configurationStores / eventGridFilters | Nee | Nee |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Spring | Ja | Ja |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Nee | Nee |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | klassiekebeheerders | Nee | Nee |
> | gegevensAliassen | Nee | Nee |
> | denyOpdrachten | Nee | Nee |
> | elevateAccess | Nee | Nee |
> | findOrphanRoleAssignments | Nee | Nee |
> | Sloten | Nee | Nee |
> | Machtigingen | Nee | Nee |
> | beleidToewijzingen | Nee | Nee |
> | beleidsdefinities | Nee | Nee |
> | policySetDefinities | Nee | Nee |
> | providerOperations | Nee | Nee |
> | rolOpdrachten | Nee | Nee |
> | roleAssignmentsUsageMetrics | Nee | Nee |
> | rolDefinities | Nee | Nee |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | automatiseringAccounts | Ja | Ja |
> | automationAccounts / configuraties | Ja | Ja |
> | automatiseringAccounts / taken | Nee | Nee |
> | automationAccounts / privateEndpointConnectionProxies | Nee | Nee |
> | automationAccounts / privateEndpointConnections | Nee | Nee |
> | automationAccounts / privateLinkResources | Nee | Nee |
> | automationAccounts / runbooks | Ja | Ja |
> | automationAccounts / softwareUpdateConfiguraties | Nee | Nee |
> | automationAccounts / webhooks | Nee | Nee |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig (Microsoft.Azconfig)

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Ja |
> | configurationStores / eventGridFilters | Nee | Nee |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Genève

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Omgevingen | Nee | Nee |
> | omgevingen / accounts | Nee | Nee |
> | omgevingen / accounts / naamruimten | Nee | Nee |
> | omgevingen / accounts / naamruimten / configuraties | Nee | Nee |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | b2c-mappen | Ja | Nee |
> | b2ctenants | Nee | Nee |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Ja | Ja |
> | postgresInstances | Ja | Ja |
> | sqlInstances | Ja | Ja |
> | sqlServerRegistraties | Ja | Ja |
> | sqlServerRegistrations / sqlServers | Nee | Nee |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | cloudManifestBestanden | Nee | Nee |
> | Registraties | Ja | Ja |
> | registraties / klantAbonnementen | Nee | Nee |
> | registraties / producten | Nee | Nee |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Ja | Ja |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | factureringAccounts | Nee | Nee |
> | billingAccounts / overeenkomsten | Nee | Nee |
> | billingAccounts / factureringMachtigingen | Nee | Nee |
> | billingAccounts / factureringSprofielen | Nee | Nee |
> | billingAccounts / factureringProfielen / factureringMachtigingen | Nee | Nee |
> | billingAccounts / billingProfiles / billingRoleAssignments BillingAccounts / billingRoleAssignments BillingAccounts / billingProfiles / billingRoleAssignments BillingAccounts / billingRole | Nee | Nee |
> | billingAccounts / billingProfiles / billingRoleDefinities | Nee | Nee |
> | billingAccounts / factureringProfielen / factureringAbonnementen | Nee | Nee |
> | billingAccounts / billingProfiles / createBillingRoleAssignment BillingAccounts / billingProfiles | Nee | Nee |
> | billingAccounts / billingProfiles / klanten | Nee | Nee |
> | billingAccounts / billingProfiles / instructies | Nee | Nee |
> | billingAccounts / billingProfiles / facturen | Nee | Nee |
> | billingAccounts / billingProfiles / facturen / pricesheet | Nee | Nee |
> | billingAccounts / billingProfiles / facturen / transacties | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections | Nee | Nee |
> | billingAccounts / billingProfiles / billingSections / billingPermissions BillingAccounts / billingProfiles / billingS | Nee | Nee |
> | billingAccounts / billingProfiles / billingSections / billingRoleAssignments Billings | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nee | Nee |
> | billingAccounts / billingProfiles / billingSections / billingSubscriptions | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment BillingAccounts / billingProfiles / invoiceSections | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / producten | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / producten / overdracht | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / producten / updateAutoRenew | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / transacties | Nee | Nee |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Nee | Nee |
> | billingAccounts / BillingProfiles / patchOperations | Nee | Nee |
> | billingAccounts / billingProfiles / paymentMethods | Nee | Nee |
> | billingAccounts / factureringProfielen / beleid | Nee | Nee |
> | billingAccounts / billingProfiles / pricesheet | Nee | Nee |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nee | Nee |
> | billingAccounts / billingProfiles / producten | Nee | Nee |
> | billingAccounts / billingProfiles / transacties | Nee | Nee |
> | billingAccounts / billingRoleAssignments | Nee | Nee |
> | billingAccounts / billingRoleDefinities | Nee | Nee |
> | billingAccounts / factureringAbonnementen | Nee | Nee |
> | billingAccounts / factureringAbonnementen / facturen | Nee | Nee |
> | billingAccounts / facturatieToewijzing maken | Nee | Nee |
> | billingAccounts / invoicesectionoperations maken | Nee | Nee |
> | billingAccounts / klanten | Nee | Nee |
> | billingAccounts / klanten / factureringMachtigingen | Nee | Nee |
> | billingAccounts / klanten / factureringAbonnementen | Nee | Nee |
> | billingAccounts / klanten / initiateTransfer | Nee | Nee |
> | billingAccounts / klanten / beleid | Nee | Nee |
> | billingAccounts / klanten / producten | Nee | Nee |
> | billingAccounts / klanten / transacties | Nee | Nee |
> | billingAccounts / klanten / overschrijvingen | Nee | Nee |
> | billingAccounts / afdelingen | Nee | Nee |
> | billingAccounts / inschrijvingAccounts | Nee | Nee |
> | billingAccounts / facturen | Nee | Nee |
> | billingAccounts / factuurSecties | Nee | Nee |
> | billingAccounts / factuurSecties / billingSubscriptionMoveOperations | Nee | Nee |
> | billingAccounts / factuurSecties / factureringAbonnementen | Nee | Nee |
> | billingAccounts / factuurSecties / factureringAbonnementen / overdracht | Nee | Nee |
> | billingAccounts / factuurSecties / verheffen | Nee | Nee |
> | billingAccounts / factuurSecties / ingewijdeTransfer | Nee | Nee |
> | billingAccounts / invoiceSections / patchOperations | Nee | Nee |
> | billingAccounts / invoiceSections / productMoveOperations | Nee | Nee |
> | billingAccounts / factuurSecties / producten | Nee | Nee |
> | billingAccounts / factuurSecties / producten / overdracht | Nee | Nee |
> | billingAccounts / factuurSecties / producten / updateAutoRenew | Nee | Nee |
> | billingAccounts / factuurSecties / transacties | Nee | Nee |
> | billingAccounts / factuurSecties / overschrijvingen | Nee | Nee |
> | billingAccounts / lineOfCredit | Nee | Nee |
> | billingAccounts / patchOperations | Nee | Nee |
> | billingAccounts / betalingsmethoden | Nee | Nee |
> | billingAccounts / producten | Nee | Nee |
> | billingAccounts / transacties | Nee | Nee |
> | factureringPerioden | Nee | Nee |
> | factureringMachtigingen | Nee | Nee |
> | factureringProperty | Nee | Nee |
> | factureringRoleAssignments | Nee | Nee |
> | factureringRoleDefinities | Nee | Nee |
> | facturatietoewijzing maken | Nee | Nee |
> | Afdelingen | Nee | Nee |
> | inschrijvingAccounts | Nee | Nee |
> | Facturen | Nee | Nee |
> | Transfers | Nee | Nee |
> | transfers / acceptTransfer | Nee | Nee |
> | transfers / weigerenTransfer | Nee | Nee |
> | transfers / operatieStatus | Nee | Nee |
> | transfers / validateTransfer | Nee | Nee |
> | validateAddress validateAddress validateAddress validateAddress | Nee | Nee |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | mapApis mapApis | Ja | Ja |
> | updateCommunicationPreference | Nee | Nee |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | blockchainLeden | Ja | Ja |
> | cordaLeden | Ja | Ja |
> | Watchers | Ja | Ja |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | TokenServices | Ja | Ja |
> | TokenServices / BlockchainNetworks | Nee | Nee |
> | TokenServices / Groepen | Nee | Nee |
> | TokenServices / Groepen / Accounts | Nee | Nee |
> | TokenServices / TokenSjablonen | Nee | Nee |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | blauwdrukToewijzingen | Nee | Nee |
> | blauwdrukOpdrachten / opdrachtBewerkingen | Nee | Nee |
> | blauwdrukOpdrachten / bewerkingen | Nee | Nee |
> | Blauwdrukken | Nee | Nee |
> | blauwdrukken / artefacten | Nee | Nee |
> | blauwdrukken / versies | Nee | Nee |
> | blauwdrukken / versies / artefacten | Nee | Nee |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | botServices | Ja | Ja |
> | botServices / kanalen | Nee | Nee |
> | botServices / verbindingen | Nee | Nee |
> | talen | Nee | Nee |
> | sjablonen | Nee | Nee |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Redis | Ja | Ja |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | toegepastReserveringen | Nee | Nee |
> | autoQuotaverhoging | Nee | Nee |
> | calculateExchange berekenen | Nee | Nee |
> | calculatePrice calculatePrice calculatePrice | Nee | Nee |
> | BerekeningAankoopprijs | Nee | Nee |
> | Catalogi | Nee | Nee |
> | commercieelReserveringsorders | Nee | Nee |
> | Exchange | Nee | Nee |
> | placePurchaseOrder | Nee | Nee |
> | reserveringOrders | Nee | Nee |
> | reserveringOrders / berekenenRestitutie | Nee | Nee |
> | reserveringOrders / samenvoegen | Nee | Nee |
> | reserveringBestellingen / reserveringen | Nee | Nee |
> | reserveringOrders / reserveringen / revisies | Nee | Nee |
> | reserveringOrders / retour | Nee | Nee |
> | reserveringOrders / gesplitst | Nee | Nee |
> | reserveringOrders / swap | Nee | Nee |
> | Reserveringen | Nee | Nee |
> | resourceProviders | Nee | Nee |
> | resources | Nee | Nee |
> | validatieReserveringsorder | Nee | Nee |

## <a name="microsoftcdn"></a>Microsoft.Cdn (Microsoft.Cdn)

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nee | Nee |
> | CdnWebApplicationFirewall-beleid | Ja | Ja |
> | randknooppunten | Nee | Nee |
> | Profielen | Ja | Ja |
> | profielen / eindpunten | Ja | Ja |
> | profielen / eindpunten / aangepaste domeinen | Nee | Nee |
> | profielen / eindpunten / oorsprongsgroepen | Nee | Nee |
> | profielen / eindpunten / oorsprong | Nee | Nee |
> | validateProbe validateProbe validateProbe validateProbe | Nee | Nee |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | certificaatOrders | Ja | Ja |
> | certificateOrders / certificaten | Nee | Nee |
> | validateCertificateRegistrationInformation validateCertificateRegistrationInformation validateCertificate | Nee | Nee |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Mogelijkheden | Nee | Nee |
> | Domeinnamen | Nee | Nee |
> | domainNames / mogelijkheden | Nee | Nee |
> | domainNames / internalLoadBalancers | Nee | Nee |
> | domainNames / serviceCertificaten | Nee | Nee |
> | domeinNamen / slots | Nee | Nee |
> | domeinNamen / slots / rollen | Nee | Nee |
> | domainNames / slots / rollen / metricDefinities | Nee | Nee |
> | domainNames / slots / rollen / metrics | Nee | Nee |
> | moveSubscriptionResources | Nee | Nee |
> | operatingSystemFamilies | Nee | Nee |
> | operatingSystems | Nee | Nee |
> | quotas | Nee | Nee |
> | resourceTypen | Nee | Nee |
> | validateSubscriptionMoveAvailability validateSubscriptionMoveAvailability validateSubscriptionMoveAvailability validateSubscription | Nee | Nee |
> | virtueleMachines | Nee | Nee |
> | virtualMachines / diagnostischeInstellingen | Nee | Nee |
> | virtualMachines / metrischeDefinities | Nee | Nee |
> | virtualMachines / statistieken | Nee | Nee |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | klassiekeInfrastructureResources | Nee | Nee |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Mogelijkheden | Nee | Nee |
> | expressRouteCrossConnections | Nee | Nee |
> | expressRouteCrossConnections / peerings | Nee | Nee |
> | gatewayOndersteundeapparaten | Nee | Nee |
> | networkSecurityGroups | Nee | Nee |
> | quotas | Nee | Nee |
> | gereserveerdIps | Nee | Nee |
> | virtuelenetwerken | Nee | Nee |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Nee | Nee |
> | virtualNetworks / virtualNetworkPeerings | Nee | Nee |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Mogelijkheden | Nee | Nee |
> | Schijven | Nee | Nee |
> | images | Nee | Nee |
> | osAfbeeldingen | Nee | Nee |
> | osPlatformImages | Nee | Nee |
> | publicImages | Nee | Nee |
> | quotas | Nee | Nee |
> | storageAccounts | Nee | Nee |
> | storageAccounts / blobServices | Nee | Nee |
> | storageAccounts / fileServices | Nee | Nee |
> | storageAccounts / metricDefinities | Nee | Nee |
> | storageAccounts / statistieken | Nee | Nee |
> | storageAccounts / queueServices | Nee | Nee |
> | storageAccounts / services | Nee | Nee |
> | storageAccounts / services / diagnosticSettings | Nee | Nee |
> | storageAccounts / services / metricDefinities | Nee | Nee |
> | storageAccounts / services / statistieken | Nee | Nee |
> | storageAccounts / tableServices | Nee | Nee |
> | storageAccounts / vmImages | Nee | Nee |
> | vmAfbeeldingen | Nee | Nee |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | RateCard | Nee | Nee |
> | GebruikAggregaten | Nee | Nee |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | beschikbaarheidSets | Ja | Ja |
> | diskEncryptionSets | Ja | Ja |
> | Schijven | Ja | Ja |
> | Galeries | Ja | Ja |
> | galerijen / toepassingen | Nee | Nee |
> | galerijen / toepassingen / versies | Nee | Nee |
> | galerijen / afbeeldingen | Nee | Nee |
> | galerijen / afbeeldingen / versies | Nee | Nee |
> | hostGroepen | Ja | Ja |
> | hostGroups / hosts | Ja | Ja |
> | images | Ja | Ja |
> | proximityPlacementGroepen | Ja | Ja |
> | restorePointCollections | Ja | Ja |
> | restorePointCollections / restorePoints | Nee | Nee |
> | sharedVMExtensions | Ja | Ja |
> | sharedVMExtensions / versies | Nee | Nee |
> | sharedVMImages | Ja | Ja |
> | sharedVMImages / versies | Nee | Nee |
> | momentopnamen | Ja | Ja |
> | sshPublicKeys sshPublicKeys sshPublicKeys ssh | Ja | Ja |
> | virtueleMachines | Ja | Ja |
> | virtualMachines / extensies | Ja | Ja |
> | virtualMachines / metrischeDefinities | Nee | Nee |
> | virtualMachineScaleSets | Ja | Ja |
> | virtualMachineScaleSets / extensies | Nee | Nee |
> | virtualMachineScaleSets / networkInterfaces | Nee | Nee |
> | virtualMachineScaleSets / publicIPAddresses | Nee | Nee |
> | virtualMachineScaleSets / virtualMachines | Nee | Nee |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Nee | Nee |

## <a name="microsoftconsumption"></a>Microsoft.Verbruik

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Geaggregeerde kosten | Nee | Nee |
> | Tegoeden | Nee | Nee |
> | Budgetten | Nee | Nee |
> | Kosten | Nee | Nee |
> | CostTags | Nee | Nee |
> | Credits | Nee | Nee |
> | events | Nee | Nee |
> | Prognoses | Nee | Nee |
> | Veel | Nee | Nee |
> | Markten | Nee | Nee |
> | Prijzenoverzichten | Nee | Nee |
> | Producten | Nee | Nee |
> | ReserveringDetails | Nee | Nee |
> | Reserveringsaanbevelingen | Nee | Nee |
> | Reserveringssamenvattingen | Nee | Nee |
> | Reserveringtransacties | Nee | Nee |
> | Tags | Nee | Nee |
> | Huurders | Nee | Nee |
> | Voorwaarden | Nee | Nee |
> | GebruikDetails | Nee | Nee |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | containerGroepen | Ja | Ja |
> | serviceVerenigingLinks | Nee | Nee |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Registers | Ja | Ja |
> | registers / agentPools | Ja | Ja |
> | registers / builds | Nee | Nee |
> | registers / builds / annuleren | Nee | Nee |
> | registers / builds / getLogLink | Nee | Nee |
> | registers / buildTaken | Ja | Ja |
> | registers / buildTaken / stappen | Nee | Nee |
> | registers / eventGridFilters | Nee | Nee |
> | registers / genererenReferenties | Nee | Nee |
> | registers / getBuildSourceUploadUrl | Nee | Nee |
> | registers / GetCredentials | Nee | Nee |
> | registers / importImage | Nee | Nee |
> | registers / privateEndpointConnectionProxies | Nee | Nee |
> | registers / privateEndpointConnectionProxies / valideren | Nee | Nee |
> | registers / privateEndpointConnections | Nee | Nee |
> | registers / privateLinkResources | Nee | Nee |
> | registers / wachtrijBouwen | Nee | Nee |
> | registers / regenererenCredential | Nee | Nee |
> | registers / regenererenReferenties | Nee | Nee |
> | registers / replicaties | Ja | Ja |
> | registers / runs | Nee | Nee |
> | registers / runs / annuleren | Nee | Nee |
> | registers / schemaRun | Nee | Nee |
> | registers / scopeMaps | Nee | Nee |
> | registers / taakRuns | Ja | Ja |
> | registers / taken | Ja | Ja |
> | registers / tokens | Nee | Nee |
> | registers / updateBeleid | Nee | Nee |
> | registers / webhooks | Ja | Ja |
> | registers / webhooks / getCallbackConfig | Nee | Nee |
> | registers / webhooks / ping | Nee | Nee |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | containerServices | Ja | Ja |
> | managedClusters | Ja | Ja |
> | openShiftManagedClusters | Ja | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Waarschuwingen | Nee | Nee |
> | FactureringAccounts | Nee | Nee |
> | Budgetten | Nee | Nee |
> | CloudConnectors | Nee | Nee |
> | Connectors | Ja | Ja |
> | Afdelingen | Nee | Nee |
> | Dimensies | Nee | Nee |
> | InschrijvingenAccounts | Nee | Nee |
> | Export | Nee | Nee |
> | ExternFacturerende rekeningen | Nee | Nee |
> | Extern Factureren / Waarschuwingen | Nee | Nee |
> | Extern Factureren / Afmetingen | Nee | Nee |
> | Extern Facturerende rekeningen / Prognose | Nee | Nee |
> | ExternFactureren / Query | Nee | Nee |
> | Extern abonnementen | Nee | Nee |
> | Extern abonnementen / waarschuwingen | Nee | Nee |
> | Extern abonnementen / afmetingen | Nee | Nee |
> | Extern abonnementen / prognose | Nee | Nee |
> | Extern abonnementen / query | Nee | Nee |
> | Prognose | Nee | Nee |
> | Query’s uitvoeren | Nee | Nee |
> | registreren | Nee | Nee |
> | Reportconfigs | Nee | Nee |
> | Rapporten | Nee | Nee |
> | Instellingen | Nee | Nee |
> | showbackRegels | Nee | Nee |
> | Weergaven | Nee | Nee |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Verzoeken | Nee | Nee |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Verenigingen | Nee | Nee |
> | resourceProviders | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Banen | Ja | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeApparaten | Ja | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | werkruimten | Ja | Nee |
> | werkruimten / dbWorkspaces | Nee | Nee |
> | werkruimten / opslagVersleuteling | Nee | Nee |
> | werkruimten / virtualNetworkPeerings | Nee | Nee |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Catalogi | Ja | Ja |
> | gegevenscatalogi | Ja | Ja |
> | datacatalogi / databronnen | Nee | Nee |
> | datacatalogi / datasources / scans | Nee | Nee |
> | datacatalogi / datasources / scans / datasets | Nee | Nee |
> | datacatalogi / datasources / scans / triggers | Nee | Nee |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | dataFabrieken | Ja | Nee |
> | dataFabrieken / diagnostischeInstellingen | Nee | Nee |
> | gegevensFabrieken / metrischeDefinities | Nee | Nee |
> | dataFactorySchema | Nee | Nee |
> | Fabrieken | Ja | Nee |
> | fabrieken / integratieRuntimes | Nee | Nee |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts / dataLakeStoreAccounts | Nee | Nee |
> | accounts / storageAccounts | Nee | Nee |
> | accounts / opslagAccounts / containers | Nee | Nee |
> | accounts / transferAnalyticsUnits | Nee | Nee |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts / eventGridFilters | Nee | Nee |
> | accounts / firewallRegels | Nee | Nee |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigratie

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | services | Nee | Nee |
> | diensten / projecten | Nee | Nee |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | rekeningen / aandelen | Nee | Nee |
> | accounts / aandelen / datasets | Nee | Nee |
> | accounts / aandelen / uitnodigingen | Nee | Nee |
> | accounts / aandelen / providershareabonnementen | Nee | Nee |
> | accounts / shares / synchronisatieInstellingen | Nee | Nee |
> | accounts / aandelenabonnementen | Nee | Nee |
> | accounts / sharesubscriptions / consumerSourceDataSets | Nee | Nee |
> | accounts / sharesubscriptions / datasetmappings | Nee | Nee |
> | accounts / sharesubscriptions / triggers | Nee | Nee |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Servers | Ja | Ja |
> | servers / adviseurs | Nee | Nee |
> | servers / sleutels | Nee | Nee |
> | servers / privateEndpointConnectionProxies | Nee | Nee |
> | servers / privateEndpointConnections | Nee | Nee |
> | servers / privateLinkResources | Nee | Nee |
> | servers / queryTeksten | Nee | Nee |
> | servers / recoverableServers | Nee | Nee |
> | servers / topQueryStatistieken | Nee | Nee |
> | servers / virtualNetworkRules | Nee | Nee |
> | servers / waitStatistics | Nee | Nee |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Servers | Ja | Ja |
> | servers / adviseurs | Nee | Nee |
> | servers / sleutels | Nee | Nee |
> | servers / privateEndpointConnectionProxies | Nee | Nee |
> | servers / privateEndpointConnections | Nee | Nee |
> | servers / privateLinkResources | Nee | Nee |
> | servers / queryTeksten | Nee | Nee |
> | servers / recoverableServers | Nee | Nee |
> | servers / topQueryStatistieken | Nee | Nee |
> | servers / virtualNetworkRules | Nee | Nee |
> | servers / waitStatistics | Nee | Nee |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | serverGroepen | Ja | Ja |
> | Servers | Ja | Ja |
> | servers / adviseurs | Nee | Nee |
> | servers / sleutels | Nee | Nee |
> | servers / privateEndpointConnectionProxies | Nee | Nee |
> | servers / privateEndpointConnections | Nee | Nee |
> | servers / privateLinkResources | Nee | Nee |
> | servers / queryTeksten | Nee | Nee |
> | servers / recoverableServers | Nee | Nee |
> | servers / topQueryStatistieken | Nee | Nee |
> | servers / virtualNetworkRules | Nee | Nee |
> | servers / waitStatistics | Nee | Nee |
> | serversv2 | Ja | Ja |
> | singleServers | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | artefactBronnen | Ja | Ja |
> | implementaties | Ja | Ja |
> | serviceTopologieën | Ja | Ja |
> | serviceTopologieën / diensten | Ja | Ja |
> | serviceTopologieën / diensten / serviceUnits | Ja | Ja |
> | stappen | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualisatie

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | toepassingsgroepen | Ja | Ja |
> | toepassingsgroepen / toepassingen | Nee | Nee |
> | toepassingsgroepen / desktops | Nee | Nee |
> | toepassingsgroepen / startmenu's | Nee | Nee |
> | hostpools | Ja | Ja |
> | hostpools / sessiehosts | Nee | Nee |
> | hostpools / sessionhosts / usersessions | Nee | Nee |
> | hostpools / gebruikerssessies | Nee | Nee |
> | werkruimten | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Elastische pools | Ja | Ja |
> | ElasticPools / IotHubTenants | Ja | Ja |
> | ElasticPools / IotHubTenants / beveiligingsinstellingen | Nee | Nee |
> | IotHubs | Ja | Ja |
> | IotHubs / eventGridFilters | Nee | Nee |
> | IotHubs / beveiligingsinstellingen | Nee | Nee |
> | ProvisioningServices | Ja | Ja |
> | gebruik | Nee | Nee |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Pijpleidingen | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Controllers | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | labcenters | Ja | Ja |
> | Labs | Ja | Ja |
> | labs / omgevingen | Ja | Ja |
> | labs / serviceRunners | Ja | Ja |
> | labs / virtualMachines | Ja | Ja |
> | Planningen | Ja | Ja |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Nee | Nee |
> | databaseAccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Domeinen | Ja | Ja |
> | domeinen / domeinOwnershipIdentifiers | Nee | Nee |
> | genereertSsoRequest | Nee | Nee |
> | topLevelDomains | Nee | Nee |
> | validateDomainRegistrationInformation validateDomainRegistrationInformation validateDomainRegistrationInformation validateDomain | Nee | Nee |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nee | Nee |
> | lcsprojects / clouddeployments | Nee | Nee |
> | lcsprojects / connectoren | Nee | Nee |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Domeinen | Ja | Ja |
> | domeinen / onderwerpen | Nee | Nee |
> | eventAbonnementen | Nee | Nee |
> | extensieOnderwerpen | Nee | Nee |
> | partnerNaamruimten | Ja | Ja |
> | partnerNamespaces / eventKanalen | Nee | Nee |
> | partnerRegistraties | Ja | Ja |
> | partnerOnderwerpen | Ja | Ja |
> | partnerOnderwerpen / eventAbonnementen | Nee | Nee |
> | systemTopics systemTopics systemTopics systemTopics | Ja | Ja |
> | systemTopics / eventAbonnementen | Nee | Nee |
> | Onderwerpen | Ja | Ja |
> | onderwerpTypen | Nee | Nee |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Clusters | Ja | Ja |
> | Naamruimten | Ja | Ja |
> | naamruimten / autorisatieregels | Nee | Nee |
> | naamruimten / disasterrecoveryconfigs | Nee | Nee |
> | naamruimten / eventhubs | Nee | Nee |
> | naamruimten / eventhubs / autorisatieregels | Nee | Nee |
> | naamruimten / eventhubs / consumentengroepen | Nee | Nee |
> | naamruimten / netwerkregelsets | Nee | Nee |

## <a name="microsoftfalcon"></a>Microsoft.Falcon (Microsoft.Falcon)

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Naamruimten | Ja | Ja |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | featureProviders | Nee | Nee |
> | functies | Nee | Nee |
> | providers | Nee | Nee |
> | abonnementFeatureRegistrations | Nee | Nee |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Inschrijven | Nee | Nee |
> | galerijobjecten | Nee | Nee |
> | genererenartefactaccessuri | Nee | Nee |
> | mijngebieden | Nee | Nee |
> | myareas / gebieden | Nee | Nee |
> | myareas / gebieden / gebieden | Nee | Nee |
> | myareas / areas / areas / galleryitems myareas / areas / galleryitems myareas / areas / areas / galleryitems myareas / areas | Nee | Nee |
> | myareas / areas / galleryitems | Nee | Nee |
> | myareas / galerijitems | Nee | Nee |
> | registreren | Nee | Nee |
> | resources | Nee | Nee |
> | resourcesbyid ophalen | Nee | Nee |

## <a name="microsoftgenomics"></a>Microsoft.Genomics (Microsoft.Genomics)

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Ja | Ja |
> | autoManagedVmConfigurationProfiles | Ja | Ja |
> | configuratieProfielToewijzingen | Nee | Nee |
> | gastConfiguratieToewijzingen | Nee | Nee |
> | Software | Nee | Nee |
> | softwareUpdateProfiel | Nee | Nee |
> | softwareUpdates | Nee | Nee |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | hanaInstances hanaInstances | Ja | Ja |
> | sapMonitors | Ja | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | dedicatedHSM's | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Clusters | Ja | Ja |
> | clusters / toepassingen | Nee | Nee |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Machines | Ja | Ja |
> | machines / extensies | Ja | Ja |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | dataManagers | Ja | Ja |

## <a name="microsofthydra"></a>Microsoft.Hydra (Microsoft.Hydra)

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Onderdelen | Ja | Ja |
> | networkScopes | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Banen | Ja | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | diagnostische instellingen | Nee | Nee |
> | diagnostischeInstellingenCategorieën | Nee | Nee |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | appSjablonen | Nee | Nee |
> | IoTApps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.ioTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | verwijderde Vaults | Nee | Nee |
> | hsmPools | Ja | Ja |
> | Kluizen | Ja | Ja |
> | kluizen / toegangsbeleid | Nee | Nee |
> | kluizen / eventGridFilters | Nee | Nee |
> | kluizen / geheimen | Nee | Nee |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes (Microsoft.Kubernetes)

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | connectedClusters | Ja | Ja |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Clusters | Ja | Ja |
> | clusters / gekoppelde databaseconfiguraties | Nee | Nee |
> | clusters / databases | Nee | Nee |
> | clusters / databases / dataverbindingen | Nee | Nee |
> | clusters / databases / eventhubverbindingen | Nee | Nee |
> | clusters / databases / principalassignments | Nee | Nee |
> | clusters / dataverbindingen | Nee | Nee |
> | clusters / principalassignments | Nee | Nee |
> | clusters / gedeelde identiteiten | Nee | Nee |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | labaccounts | Ja | Ja |
> | gebruikers | Nee | Nee |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | hostingOmgevingen | Ja | Ja |
> | integratieAccounts | Ja | Ja |
> | integratieServiceOmgevingen | Ja | Ja |
> | integrationServiceEnvironments / managedApis | Ja | Ja |
> | geïsoleerde omgevingen | Ja | Ja |
> | Werkstromen | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | inzetPlannen | Ja | Ja |
> | Webservices | Ja | Ja |
> | Workspaces | Ja | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | werkruimten | Ja | Ja |
> | werkruimten / berekent | Nee | Nee |
> | werkruimten / eventGridFilters | Nee | Nee |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | applyUpdates toepassen | Nee | Nee |
> | configuratieToewijzingen | Nee | Nee |
> | onderhoudsConfiguraties | Ja | Ja |
> | updates | Nee | Nee |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Identiteiten | Nee | Nee |
> | userAssignedIdentities | Ja | Ja |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | marktplaatsRegistratieDefinities | Nee | Nee |
> | registratieOpdrachten | Nee | Nee |
> | registratieDefinities | Nee | Nee |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | getEntities | Nee | Nee |
> | beheerGroepen | Nee | Nee |
> | beheerGroepen / instellingen | Nee | Nee |
> | resources | Nee | Nee |
> | startTenantBackfill | Nee | Nee |
> | tenantBackfillStatus | Nee | Nee |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |
> | accounts / eventGridFilters | Nee | Nee |
> | accounts / privateAtes | Ja | Ja |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Biedt | Nee | Nee |
> | aanbiedingTypen | Nee | Nee |
> | aanbiedingTypes / uitgevers | Nee | Nee |
> | aanbodTypes / uitgevers / aanbiedingen | Nee | Nee |
> | offerTypes / uitgevers / aanbiedingen / plannen | Nee | Nee |
> | offerTypes / uitgevers / aanbiedingen / plannen / overeenkomsten | Nee | Nee |
> | offerTypes / uitgevers / aanbiedingen / plannen / configs | Nee | Nee |
> | offerTypes / uitgevers / aanbiedingen / plannen / configs / importImage | Nee | Nee |
> | privategalleryitems | Nee | Nee |
> | privateStoreClient | Nee | Nee |
> | privateStores | Nee | Nee |
> | privateStores / aanbiedingen | Nee | Nee |
> | Producten | Nee | Nee |
> | Uitgevers | Nee | Nee |
> | uitgevers / aanbiedingen | Nee | Nee |
> | uitgevers / aanbiedingen / wijzigingen | Nee | Nee |
> | registreren | Nee | Nee |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Ja | Ja |
> | updateCommunicationPreference | Nee | Nee |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Overeenkomsten | Nee | Nee |
> | aanbiedingstypen | Nee | Nee |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | mediaservices | Ja | Ja |
> | mediaservices / accountFilters | Nee | Nee |
> | mediaservices / activa | Nee | Nee |
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
> | mediaservices / streamingBeleid | Nee | Nee |
> | mediaservices / transformaties | Nee | Nee |
> | mediaservices / transformaties / jobs | Nee | Nee |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | appClusters | Ja | Ja |

## <a name="microsoftmigrate"></a>Microsoft.Migreren

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | beoordelingProjecten | Ja | Ja |
> | projecten migreren | Ja | Ja |
> | moveCollecties verplaatsen | Ja | Ja |
> | Projecten | Ja | Ja |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Ja | Ja |
> | objectAccounts begrijpen | Ja | Ja |
> | remoteRenderingAccounts | Ja | Ja |
> | spatialAnchorsAccounts | Ja | Ja |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Ja | Nee |
> | netAppAccounts / accountBack-ups | Nee | Nee |
> | netAppAccounts / capaciteitPools | Ja | Nee |
> | netAppAccounts / capaciteitPools / volumes | Ja | Nee |
> | netAppAccounts / capaciteitPools / volumes / momentopnamen | Nee | Nee |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Ja | Ja |
> | applicationGatewayWebApplicationFirewallBeleid | Ja | Ja |
> | applicationSecurityGroups | Ja | Ja |
> | azureFirewallFqdnTags | Nee | Nee |
> | azureFirewalls | Ja | Nee |
> | bastionHosts | Ja | Ja |
> | bgpServiceCommunities | Nee | Nee |
> | Verbindingen | Ja | Ja |
> | ddosCustomPolicies | Ja | Ja |
> | ddosProtectionPlans ddosProtectionPlans | Ja | Ja |
> | dnsOperationStatussen | Nee | Nee |
> | dnszones | Ja | Ja |
> | dnszones / A | Nee | Nee |
> | dnszones / AAAA | Nee | Nee |
> | dnszones / alle | Nee | Nee |
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
> | FirewallBeleid | Ja | Ja |
> | voordeuren | Ja, maar beperkt (zie [noot hieronder)](#frontdoor) | Ja |
> | frontdoorWebApplicationFirewallManagedRuleSets | Ja, maar beperkt (zie [noot hieronder)](#frontdoor) | Nee |
> | frontdoorWebApplicationFirewallBeleid | Ja, maar beperkt (zie [noot hieronder)](#frontdoor) | Ja |
> | getDnsResourceReference | Nee | Nee |
> | internNotify | Nee | Nee |
> | loadBalancers | Ja | Nee |
> | localNetworkGateways | Ja | Ja |
> | natGateways | Ja | Ja |
> | netwerkIntentPolicies | Ja | Ja |
> | netwerkInterfaces | Ja | Ja |
> | netwerkProfielen | Ja | Ja |
> | networkSecurityGroups | Ja | Ja |
> | networkWatchers | Ja | Nee |
> | networkWatchers / connectionMonitors | Ja | Nee |
> | networkWatchers / flowLogs | Nee | Nee |
> | networkWatchers / lenzen | Ja | Nee |
> | networkWatchers / pingMeshes | Ja | Nee |
> | p2sVpnGateways | Ja | Ja |
> | privateDnsOperationStatussen | Nee | Nee |
> | privateDnsZones | Ja | Ja |
> | privateDnsZones / A | Nee | Nee |
> | privatednszones / AAAA | Nee | Nee |
> | privateDnsZones / alle | Nee | Nee |
> | privateDnsZones / CNAME | Nee | Nee |
> | privateDnsZones / MX | Nee | Nee |
> | privateDnsZones / PTR | Nee | Nee |
> | privateDnsZones / SOA | Nee | Nee |
> | privateDnsZones / SRV | Nee | Nee |
> | privateDnsZones / TXT | Nee | Nee |
> | privateDnsZones / virtualNetworkLinks | Ja | Ja |
> | privéeindpunten | Ja | Ja |
> | privateLinkServices | Ja | Ja |
> | publicIPAddresses | Ja | Ja |
> | publicIPPrefixes | Ja | Ja |
> | routeFilters | Ja | Ja |
> | routeTabellen | Ja | Ja |
> | serviceEndpointBeleid | Ja | Ja |
> | trafficManagerGeografische hiërarchieën | Nee | Nee |
> | trafficmanagerprofielen | Ja | Ja |
> | trafficmanagerprofielen/heatMaps | Nee | Nee |
> | trafficManagerUserMetricsKeys | Nee | Nee |
> | virtualHubs | Ja | Ja |
> | virtualNetworkGateways | Ja | Ja |
> | virtuelenetwerken | Ja | Ja |
> | virtualNetworkTaps | Ja | Ja |
> | virtualWans virtualWans | Ja | Ja |
> | vpnGateways | Ja | Nee |
> | vpnSites | Ja | Ja |
> | webApplicationFirewallBeleid | Ja | Ja |

<a id="frontdoor" />

> [!NOTE]
> Voor Azure Front Door Service u tags toepassen bij het maken van de bron, maar het bijwerken of toevoegen van tags wordt momenteel niet ondersteund.


## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | Nee | Nee |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Naamruimten | Ja | Nee |
> | naamruimten / notificationHubs | Ja | Nee |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | osNaamruimten | Ja | Ja |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Ja | Ja |
> | ImportSites | Ja | Ja |
> | Serversites | Ja | Ja |
> | VMwareSites | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Clusters | Ja | Ja |
> | linkDoelen | Nee | Nee |
> | opslagInsightConfigs | Nee | Nee |
> | werkruimten | Ja | Ja |
> | werkruimten / gegevensExport | Nee | Nee |
> | werkruimten / gegevensBronnen | Nee | Nee |
> | werkruimten / linkedServices | Nee | Nee |
> | werkruimten / linkedStorageAccounts | Nee | Nee |
> | werkruimten / query | Nee | Nee |
> | werkruimten / scopedPrivateLinkProxies | Nee | Nee |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | beheersverenigingen | Nee | Nee |
> | beheerconfiguraties | Ja | Ja |
> | oplossingen | Ja | Ja |
> | Weergaven | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Nee | Nee |
> | peerAsns | Nee | Nee |
> | peerings | Ja | Ja |
> | peeringServiceLanden | Nee | Nee |
> | peeringServiceproviders | Nee | Nee |
> | peeringServices | Ja | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | policyEvents | Nee | Nee |
> | policyMetadata | Nee | Nee |
> | beleidsstaten | Nee | Nee |
> | policyTrackedResources | Nee | Nee |
> | herstelacties | Nee | Nee |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> |  -consoles | Nee | Nee |
> | dashboards | Ja | Ja |
> | userSettings | Nee | Nee |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | werkruimtecollecties | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Capaciteiten | Ja | Ja |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon (Microsoft.ProjectBabylon)

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Ja | Ja |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Workspaces | Ja | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | back-upProtectedItems | Nee | Nee |
> | Kluizen | Ja | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Naamruimten | Ja | Ja |
> | naamruimten / autorisatieregels | Nee | Nee |
> | naamruimten / hybrideverbindingen | Nee | Nee |
> | naamruimten / hybrideverbindingen / autorisatieregels | Nee | Nee |
> | naamruimten / wcfrelays | Nee | Nee |
> | naamruimten / wcfrelays / autorisatieregels | Nee | Nee |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | accounts | Nee | Nee |
> | Collecties | Ja | Ja |
> | collecties / toepassingen | Nee | Nee |
> | collecties / securityprincipals | Nee | Nee |
> | sjabloonafbeeldingen | Nee | Nee |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Query 's | Ja | Ja |
> | resourceChangeDetails | Nee | Nee |
> | resourceWijzigingen | Nee | Nee |
> | resources | Nee | Nee |
> | resourcesGeschiedenis | Nee | Nee |
> | abonnementenStatus | Nee | Nee |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | beschikbaarheidStatussen | Nee | Nee |
> | ChildAvailabilityStatussen | Nee | Nee |
> | childResources | Nee | Nee |
> | opkomende vraagstukken | Nee | Nee |
> | events | Nee | Nee |
> | impactresources | Nee | Nee |
> | metagegevens | Nee | Nee |
> | meldingen | Nee | Nee |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Implementaties | Ja | Nee |
> | implementaties / bewerkingen | Nee | Nee |
> | implementatieScripts | Ja | Ja |
> | implementatieScripts / logboeken | Nee | Nee |
> | Verwijzigingen | Nee | Nee |
> | Melding ResourceJobs | Nee | Nee |
> | providers | Nee | Nee |
> | resourceGroepen | Ja | Nee |
> | Abonnementen | Ja | Nee |
> | Huurders | Nee | Nee |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | toepassingen | Ja | Ja |
> | saasresources | Nee | Nee |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Nee | Nee |
> | searchServices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Nee | Nee |
> | advancedThreatProtectionSettings | Nee | Nee |
> | waarschuwingen | Nee | Nee |
> | toegestane verbindingen | Nee | Nee |
> | toepassingWhitelistings | Nee | Nee |
> | beoordelingMetadata | Nee | Nee |
> | evaluaties | Nee | Nee |
> | regels voor meldingen automatisch afwijzen | Nee | Nee |
> | automatiseringen | Ja | Ja |
> | Instellingen voor automatisch inrichten | Nee | Nee |
> | Nalevingen | Nee | Nee |
> | gegevensCollectionAgents | Nee | Nee |
> | deviceSecurityGroups | Nee | Nee |
> | ontdektSecuritySolutions | Nee | Nee |
> | externalSecuritySolutions | Nee | Nee |
> | Informatiebeschermingsbeleid | Nee | Nee |
> | iotSecuritySolutions | Ja | Ja |
> | iotSecuritySolutions / analyticsModellen | Nee | Nee |
> | iotSecuritySolutions / analyticsModellen / aggregatedAlerts | Nee | Nee |
> | iotSecuritySolutions / analyticsModellen / geaggregeerdaanbevelingen | Nee | Nee |
> | jitNetworkAccessPolicies | Nee | Nee |
> | netwerkGegevens | Nee | Nee |
> | policies | Nee | Nee |
> | prijzen | Nee | Nee |
> | regelgevingComplianceStandards | Nee | Nee |
> | wettelijke ComplianceStandards / regulatoryComplianceControls | Nee | Nee |
> | wettelijke ComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nee | Nee |
> | secureScoreControlDefinities | Nee | Nee |
> | secureScoreControls | Nee | Nee |
> | secureScores | Nee | Nee |
> | secureScores / secureScoreControls | Nee | Nee |
> | securityContacten | Nee | Nee |
> | securitySolutions | Nee | Nee |
> | securitySolutionsReferenceData | Nee | Nee |
> | beveiligingsstatussen | Nee | Nee |
> | securityStatusesSamenvattingen | Nee | Nee |
> | serverVulnerabilityAssessments | Nee | Nee |
> | instellingen | Nee | Nee |
> | subbeoordelingen | Nee | Nee |
> | taken | Nee | Nee |
> | Topologieën | Nee | Nee |
> | instellingen voor werkruimten | Nee | Nee |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | diagnostische instellingen | Nee | Nee |
> | diagnostischeInstellingenCategorieën | Nee | Nee |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | aggregaties | Nee | Nee |
> | alertRegels | Nee | Nee |
> | alertRuleTemplates | Nee | Nee |
> | bladwijzers | Nee | Nee |
> | Gevallen | Nee | Nee |
> | gegevensConnectors | Nee | Nee |
> | dataConnectorsCheckRequirements | Nee | Nee |
> | Entiteiten | Nee | Nee |
> | entiteitQueries | Nee | Nee |
> | Incidenten | Nee | Nee |
> | officeConsents | Nee | Nee |
> | instellingen | Nee | Nee |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Naamruimten | Ja | Nee |
> | naamruimten / autorisatieregels | Nee | Nee |
> | naamruimten / disasterrecoveryconfigs | Nee | Nee |
> | naamruimten / eventgridfilters | Nee | Nee |
> | naamruimten / netwerkregelsets | Nee | Nee |
> | naamruimten / wachtrijen | Nee | Nee |
> | naamruimten / wachtrijen / autorisatieregels | Nee | Nee |
> | naamruimten / onderwerpen | Nee | Nee |
> | naamruimten / onderwerpen / autorisatieregels | Nee | Nee |
> | naamruimten / onderwerpen / abonnementen | Nee | Nee |
> | naamruimten / onderwerpen / abonnementen / regels | Nee | Nee |
> | premiumMessagingRegio's | Nee | Nee |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | toepassingen | Ja | Ja |
> | Clusters | Ja | Ja |
> | clusters / toepassingen | Nee | Nee |
> | containerGroepen | Ja | Ja |
> | containerGroepSets | Ja | Ja |
> | edgeclusters | Ja | Ja |
> | edgeclusters / toepassingen | Nee | Nee |
> | managedclusters | Ja | Ja |
> | managedclusters / nodetypes | Nee | Nee |
> | Netwerken | Ja | Ja |
> | secretstores | Ja | Ja |
> | secretstores / certificaten | Nee | Nee |
> | secretstores / geheimen | Nee | Nee |
> | volumes | Ja | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | toepassingen | Ja | Ja |
> | containerGroepen | Ja | Ja |
> | Gateways | Ja | Ja |
> | Netwerken | Ja | Ja |
> | geheimen | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | providerRegistraties | Nee | Nee |
> | providerRegistraties / resourceTypeRegistrations | Nee | Nee |
> | implementaties | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | SignalR | Ja | Ja |
> | SignalR / eventGridFilters | Nee | Nee |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Ja | Ja |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | hybrideGebruikSvoordelen | Nee | Nee |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | toepassingsDefinities | Ja | Ja |
> | toepassingen | Ja | Ja |
> | jitRequests | Ja | Ja |

## <a name="microsoftspoolservice"></a>Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | geregistreerdeAbonnementen | Nee | Nee |
> | Spoelen | Ja | Ja |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | managedInstances | Ja | Ja |
> | managedInstances / databases | Ja (zie [noot hieronder)](#sqlnote) | Ja |
> | managedInstances / databases / back-upShortTermRetentionPolicies | Nee | Nee |
> | managedInstances / databases / schema's / tabellen / kolommen / sensitivityLabels | Nee | Nee |
> | managedInstances / databases / kwetsbaarheidAssessments | Nee | Nee |
> | managedInstances / databases / kwetsbaarheidAssessments / regels / basislijnen | Nee | Nee |
> | managedInstances / encryptionProtector | Nee | Nee |
> | managedInstances / toetsen | Nee | Nee |
> | managedInstances / restorableDroppedDatabases / back-upShortTermRetentionPolicies | Nee | Nee |
> | managedInstances / kwetsbaarheidAssessments | Nee | Nee |
> | Servers | Ja | Ja |
> | servers / beheerders | Nee | Nee |
> | servers / communicatieLinks | Nee | Nee |
> | servers / databases | Ja (zie [noot hieronder)](#sqlnote) | Ja |
> | servers / encryptieProtector | Nee | Nee |
> | servers / firewallRegels | Nee | Nee |
> | servers / sleutels | Nee | Nee |
> | servers / restorableDroppedDatabases | Nee | Nee |
> | servers / servicedoelstellingen | Nee | Nee |
> | servers / tdecertificaten | Nee | Nee |
> | virtuele clusters | Nee | Nee |

<a id="sqlnote" />

> [!NOTE]
> De Master-database biedt geen ondersteuning voor tags, maar voor andere databases, waaronder Azure SQL Data Warehouse-databases, ondersteuningstags. Azure SQL Data Warehouse-databases moeten de status Actief (niet onderbroken) hebben.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroepen | Ja | Ja |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nee | Nee |
> | SqlVirtualMachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Ja | Ja |
> | storageAccounts / blobServices | Nee | Nee |
> | storageAccounts / fileServices | Nee | Nee |
> | storageAccounts / queueServices | Nee | Nee |
> | storageAccounts / services | Nee | Nee |
> | storageAccounts / services / metricDefinities | Nee | Nee |
> | storageAccounts / tableServices | Nee | Nee |
> | gebruik | Nee | Nee |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Caches | Ja | Ja |
> | caches / storageTargets | Nee | Nee |
> | gebruikModellen | Nee | Nee |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplicatie

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | replicatiegroepen | Nee | Nee |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Nee | Nee |
> | storageSyncServices / syncGroups | Nee | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee | Nee |
> | storageSyncServices / werkstromen | Nee | Nee |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Nee | Nee |
> | storageSyncServices / syncGroups | Nee | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee | Nee |
> | storageSyncServices / werkstromen | Nee | Nee |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Nee | Nee |
> | storageSyncServices / syncGroups | Nee | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee | Nee |
> | storageSyncServices / werkstromen | Nee | Nee |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Managers | Ja | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Ja (zie noot hieronder) | Ja |

> [!NOTE]
> U geen tag toevoegen wanneer streamingjobs wordt uitgevoerd. Stop de bron om een tag toe te voegen.

## <a name="microsoftsubscription"></a>Microsoft.Abonnement

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | annuleren | Nee | Nee |
> | Abonnement maken | Nee | Nee |
> | inschakelen | Nee | Nee |
> | naam wijzigen | Nee | Nee |
> | Abonnementsdefinities | Nee | Nee |
> | Abonnementen | Nee | Nee |
> | Abonnementen | Nee | Nee |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Omgevingen | Ja | Nee |
> | omgevingen / toegangsbeleid | Nee | Nee |
> | omgevingen / eventsources | Ja | Nee |
> | omgevingen / referenceDataSets | Ja | Nee |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Ja | Ja |
> | dedicatedCloudServices | Ja | Ja |
> | virtueleMachines | Ja | Ja |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | apparaten | Ja | Ja |
> | geregistreerdeAbonnementen | Nee | Nee |
> | Leveranciers | Nee | Nee |
> | leveranciers / skus | Nee | Nee |
> | leveranciers / vnfs | Nee | Nee |
> | virtualNetworkFunctionSkus virtualNetworkFunctionSkus | Nee | Nee |
> | vnfs | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nee | Nee |
> | apiManagementAccounts / apiAcls | Nee | Nee |
> | apiManagementAccounts / api's | Nee | Nee |
> | apiManagementAccounts / apis / apiAcls | Nee | Nee |
> | apiManagementAccounts / apis / connectionAcls | Nee | Nee |
> | apiManagementAccounts / api's / verbindingen | Nee | Nee |
> | apiManagementAccounts / api's / verbindingen / connectionAcls | Nee | Nee |
> | apiManagementAccounts / apis / gelokaliseerdeDefinities | Nee | Nee |
> | apiManagementAccounts / connectionAcls | Nee | Nee |
> | apiManagementAccounts / verbindingen | Nee | Nee |
> | billingMeters | Nee | Nee |
> | certificaten | Ja | Ja |
> | verbindingenGateways | Ja | Ja |
> | Verbindingen | Ja | Ja |
> | customApis | Ja | Ja |
> | verwijderde Sites | Nee | Nee |
> | hostingOmgevingen | Ja | Ja |
> | hostingOmgevingen / eventGridFilters | Nee | Nee |
> | hostingOmgevingen / multiRolePools | Nee | Nee |
> | hostingOmgevingen / workerPools | Nee | Nee |
> | kubeOmgevingen | Ja | Ja |
> | publicerenGebruikers | Nee | Nee |
> | aanbevelingen | Nee | Nee |
> | resourceHealthMetadata | Nee | Nee |
> | looptijden | Nee | Nee |
> | serverFarms | Ja | Ja |
> | serverFarms / eventGridFilters | Nee | Nee |
> | sites | Ja | Ja |
> | sites / config  | Nee | Nee |
> | sites / eventGridFilters | Nee | Nee |
> | sites / hostNameBindings | Nee | Nee |
> | sites / netwerkConfig | Nee | Nee |
> | sites / premieraddons | Ja | Ja |
> | sites / slots | Ja | Ja |
> | sites / slots / eventGridFilters | Nee | Nee |
> | sites / slots / hostNameBindings | Nee | Nee |
> | sites / slots / networkConfig | Nee | Nee |
> | bronBesturingselementen | Nee | Nee |
> | staticSites | Ja | Ja |
> | Valideren | Nee | Nee |
> | verifiërenHostingEnvironmentVnet | Nee | Nee |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | diagnostische instellingen | Nee | Nee |
> | diagnostischeInstellingenCategorieën | Nee | Nee |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Ja | Ja |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resourcetype | Ondersteunt tags | Tag in kostenrapport |
> | ------------- | ----------- | ----------- |
> | Onderdelen | Nee | Nee |
> | componentenSamenvatting | Nee | Nee |
> | monitorInstanties | Nee | Nee |
> | monitorInstancesSamenvatting | Nee | Nee |
> | Monitoren | Nee | Nee |
> | meldingInstellingen | Nee | Nee |

## <a name="next-steps"></a>Volgende stappen

Zie Tags gebruiken om uw [Azure-bronnen te ordenen](tag-resources.md)voor meer informatie over het toepassen van tags op resources.
