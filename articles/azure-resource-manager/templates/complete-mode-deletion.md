---
title: Modus voor volledige verwijdering
description: Toont hoe resourcetypen de verwijdering van de volledige modus verwerken in Azure Resource Manager-sjablonen.
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 5f797974212636460306c6a17869d6b8380545ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664403"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Verwijderen van Azure-resources voor implementaties in de volledige modus

In dit artikel wordt beschreven hoe resourcetypen verwijdering verwerken wanneer deze niet in een sjabloon staan die in de volledige modus wordt geïmplementeerd.

De resourcetypen die met **Ja** zijn gemarkeerd, worden verwijderd wanneer het type zich niet in de sjabloon bevindt die met de volledige modus is geïmplementeerd.

De resourcetypen die met **Geen** zijn gemarkeerd, worden niet automatisch verwijderd wanneer deze niet in de sjabloon staan. Ze worden echter verwijderd als de bovenliggende bron wordt verwijderd. Zie [Azure Resource Manager-implementatiemodi](deployment-modes.md)voor een volledige beschrijving van het gedrag.

Als u implementeert in [meer dan één resourcegroep in een sjabloon,](cross-resource-group-deployment.md)komen resources in de resourcegroep die in de implementatiebewerking is opgegeven, in aanmerking om te worden verwijderd. Bronnen in de secundaire resourcegroepen worden niet verwijderd.

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
> - [Microsoft.Migreren](#microsoftmigrate)
> - [Microsoft.MixedReality Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Notebooks](#microsoftnotebooks)
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
> - [Microsoft.ProjectBabylon (Microsoft.ProjectBabylon)](#microsoftprojectbabylon)
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
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | DomainServices | Ja |
> | DomainServices / oucontainer | Nee |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | ondersteuningProviders | Nee |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | aadsupportcases | Nee |
> | voegt services toe | Nee |
> | Agenten | Nee |
> | anonieme apigebruikers | Nee |
> | configuratie | Nee |
> | logboeken | Nee |
> | rapporten | Nee |
> | servicehealthmetrics | Nee |
> | services | Nee |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Configuraties | Nee |
> | aanbevelingen genereren | Nee |
> | metagegevens | Nee |
> | aanbevelingen | Nee |
> | onderdrukkingen | Nee |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsBeheer

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | actieRegels | Ja |
> | waarschuwingen | Nee |
> | waarschuwingenLijst | Nee |
> | alertsMetaData | Nee |
> | waarschuwingenSamenvatting | Nee |
> | waarschuwingenOverzichtslijst | Nee |
> | Feedback | Nee |
> | smartDetectorAlertRegels | Ja |
> | smartDetectorRuntimeEnvironments | Nee |
> | smartGroups | Nee |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Servers | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | rapportFeedback | Nee |
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
> | klassiekebeheerders | Nee |
> | gegevensAliassen | Nee |
> | denyOpdrachten | Nee |
> | elevateAccess | Nee |
> | findOrphanRoleAssignments | Nee |
> | Sloten | Nee |
> | Machtigingen | Nee |
> | beleidToewijzingen | Nee |
> | beleidsdefinities | Nee |
> | policySetDefinities | Nee |
> | providerOperations | Nee |
> | rolOpdrachten | Nee |
> | roleAssignmentsUsageMetrics | Nee |
> | rolDefinities | Nee |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | automatiseringAccounts | Ja |
> | automationAccounts / configuraties | Ja |
> | automatiseringAccounts / taken | Nee |
> | automationAccounts / privateEndpointConnectionProxies | Nee |
> | automationAccounts / privateEndpointConnections | Nee |
> | automationAccounts / privateLinkResources | Nee |
> | automationAccounts / runbooks | Ja |
> | automationAccounts / softwareUpdateConfiguraties | Nee |
> | automationAccounts / webhooks | Nee |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig (Microsoft.Azconfig)

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | configurationStores | Ja |
> | configurationStores / eventGridFilters | Nee |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Genève

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Omgevingen | Nee |
> | omgevingen / accounts | Nee |
> | omgevingen / accounts / naamruimten | Nee |
> | omgevingen / accounts / naamruimten / configuraties | Nee |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | b2c-mappen | Ja |
> | b2ctenants | Nee |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | hybridDataManagers | Ja |
> | postgresInstances | Ja |
> | sqlBigDataClusters | Ja |
> | sqlInstances | Ja |
> | sqlServerRegistraties | Ja |
> | sqlServerRegistrations / sqlServers | Nee |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Registraties | Ja |
> | registraties / klantAbonnementen | Nee |
> | registraties / producten | Nee |
> | verificatieToetsen | Nee |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | batchAccounts | Ja |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | factureringAccounts | Nee |
> | billingAccounts / overeenkomsten | Nee |
> | billingAccounts / factureringMachtigingen | Nee |
> | billingAccounts / factureringSprofielen | Nee |
> | billingAccounts / factureringProfielen / factureringMachtigingen | Nee |
> | billingAccounts / billingProfiles / billingRoleAssignments BillingAccounts / billingRoleAssignments BillingAccounts / billingProfiles / billingRoleAssignments BillingAccounts / billingRole | Nee |
> | billingAccounts / billingProfiles / billingRoleDefinities | Nee |
> | billingAccounts / factureringProfielen / factureringAbonnementen | Nee |
> | billingAccounts / billingProfiles / createBillingRoleAssignment BillingAccounts / billingProfiles | Nee |
> | billingAccounts / billingProfiles / klanten | Nee |
> | billingAccounts / billingProfiles / instructies | Nee |
> | billingAccounts / billingProfiles / facturen | Nee |
> | billingAccounts / billingProfiles / facturen / pricesheet | Nee |
> | billingAccounts / billingProfiles / facturen / transacties | Nee |
> | billingAccounts / billingProfiles / invoiceSections | Nee |
> | billingAccounts / billingProfiles / billingSections / billingPermissions BillingAccounts / billingProfiles / billingS | Nee |
> | billingAccounts / billingProfiles / billingSections / billingRoleAssignments Billings | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nee |
> | billingAccounts / billingProfiles / billingSections / billingSubscriptions | Nee |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment BillingAccounts / billingProfiles / invoiceSections | Nee |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nee |
> | billingAccounts / billingProfiles / invoiceSections / producten | Nee |
> | billingAccounts / billingProfiles / invoiceSections / producten / overdracht | Nee |
> | billingAccounts / billingProfiles / invoiceSections / producten / updateAutoRenew | Nee |
> | billingAccounts / billingProfiles / invoiceSections / transacties | Nee |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Nee |
> | billingAccounts / BillingProfiles / patchOperations | Nee |
> | billingAccounts / billingProfiles / paymentMethods | Nee |
> | billingAccounts / factureringProfielen / beleid | Nee |
> | billingAccounts / billingProfiles / pricesheet | Nee |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nee |
> | billingAccounts / billingProfiles / producten | Nee |
> | billingAccounts / billingProfiles / transacties | Nee |
> | billingAccounts / billingRoleAssignments | Nee |
> | billingAccounts / billingRoleDefinities | Nee |
> | billingAccounts / factureringAbonnementen | Nee |
> | billingAccounts / factureringAbonnementen / facturen | Nee |
> | billingAccounts / facturatieToewijzing maken | Nee |
> | billingAccounts / invoicesectionoperations maken | Nee |
> | billingAccounts / klanten | Nee |
> | billingAccounts / klanten / factureringMachtigingen | Nee |
> | billingAccounts / klanten / factureringAbonnementen | Nee |
> | billingAccounts / klanten / initiateTransfer | Nee |
> | billingAccounts / klanten / beleid | Nee |
> | billingAccounts / klanten / producten | Nee |
> | billingAccounts / klanten / transacties | Nee |
> | billingAccounts / klanten / overschrijvingen | Nee |
> | billingAccounts / afdelingen | Nee |
> | billingAccounts / inschrijvingAccounts | Nee |
> | billingAccounts / facturen | Nee |
> | billingAccounts / factuurSecties | Nee |
> | billingAccounts / factuurSecties / billingSubscriptionMoveOperations | Nee |
> | billingAccounts / factuurSecties / factureringAbonnementen | Nee |
> | billingAccounts / factuurSecties / factureringAbonnementen / overdracht | Nee |
> | billingAccounts / factuurSecties / verheffen | Nee |
> | billingAccounts / factuurSecties / ingewijdeTransfer | Nee |
> | billingAccounts / invoiceSections / patchOperations | Nee |
> | billingAccounts / invoiceSections / productMoveOperations | Nee |
> | billingAccounts / factuurSecties / producten | Nee |
> | billingAccounts / factuurSecties / producten / overdracht | Nee |
> | billingAccounts / factuurSecties / producten / updateAutoRenew | Nee |
> | billingAccounts / factuurSecties / transacties | Nee |
> | billingAccounts / factuurSecties / overschrijvingen | Nee |
> | billingAccounts / lineOfCredit | Nee |
> | billingAccounts / patchOperations | Nee |
> | billingAccounts / betalingsmethoden | Nee |
> | billingAccounts / producten | Nee |
> | billingAccounts / transacties | Nee |
> | factureringPerioden | Nee |
> | factureringMachtigingen | Nee |
> | factureringProperty | Nee |
> | factureringRoleAssignments | Nee |
> | factureringRoleDefinities | Nee |
> | facturatietoewijzing maken | Nee |
> | Afdelingen | Nee |
> | inschrijvingAccounts | Nee |
> | Facturen | Nee |
> | Transfers | Nee |
> | transfers / acceptTransfer | Nee |
> | transfers / weigerenTransfer | Nee |
> | transfers / operatieStatus | Nee |
> | transfers / validateTransfer | Nee |
> | validateAddress validateAddress validateAddress validateAddress | Nee |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | mapApis mapApis | Ja |
> | updateCommunicationPreference | Nee |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | blockchainLeden | Ja |
> | cordaLeden | Ja |
> | Watchers | Ja |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | blauwdrukToewijzingen | Nee |
> | blauwdrukOpdrachten / opdrachtBewerkingen | Nee |
> | blauwdrukOpdrachten / bewerkingen | Nee |
> | Blauwdrukken | Nee |
> | blauwdrukken / artefacten | Nee |
> | blauwdrukken / versies | Nee |
> | blauwdrukken / versies / artefacten | Nee |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | botServices | Ja |
> | botServices / kanalen | Nee |
> | botServices / verbindingen | Nee |
> | talen | Nee |
> | sjablonen | Nee |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Redis | Ja |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toegepastReserveringen | Nee |
> | autoQuotaverhoging | Nee |
> | calculateExchange berekenen | Nee |
> | calculatePrice calculatePrice calculatePrice | Nee |
> | BerekeningAankoopprijs | Nee |
> | Catalogi | Nee |
> | commercieelReserveringsorders | Nee |
> | Exchange | Nee |
> | placePurchaseOrder | Nee |
> | reserveringOrders | Nee |
> | reserveringOrders / berekenenRestitutie | Nee |
> | reserveringOrders / samenvoegen | Nee |
> | reserveringBestellingen / reserveringen | Nee |
> | reserveringOrders / reserveringen / revisies | Nee |
> | reserveringOrders / retour | Nee |
> | reserveringOrders / gesplitst | Nee |
> | reserveringOrders / swap | Nee |
> | Reserveringen | Nee |
> | resourceProviders | Nee |
> | resources | Nee |
> | validatieReserveringsorder | Nee |

## <a name="microsoftcdn"></a>Microsoft.Cdn (Microsoft.Cdn)

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nee |
> | CdnWebApplicationFirewall-beleid | Ja |
> | randknooppunten | Nee |
> | Profielen | Ja |
> | profielen / eindpunten | Ja |
> | profielen / eindpunten / aangepaste domeinen | Nee |
> | profielen / eindpunten / oorsprong | Nee |
> | validateProbe validateProbe validateProbe validateProbe | Nee |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | certificaatOrders | Ja |
> | certificateOrders / certificaten | Nee |
> | validateCertificateRegistrationInformation validateCertificateRegistrationInformation validateCertificate | Nee |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Mogelijkheden | Nee |
> | Domeinnamen | Ja |
> | domainNames / mogelijkheden | Nee |
> | domainNames / internalLoadBalancers | Nee |
> | domainNames / serviceCertificaten | Nee |
> | domeinNamen / slots | Nee |
> | domeinNamen / slots / rollen | Nee |
> | domainNames / slots / rollen / metricDefinities | Nee |
> | domainNames / slots / rollen / metrics | Nee |
> | moveSubscriptionResources | Nee |
> | operatingSystemFamilies | Nee |
> | operatingSystems | Nee |
> | quotas | Nee |
> | resourceTypen | Nee |
> | validateSubscriptionMoveAvailability validateSubscriptionMoveAvailability validateSubscriptionMoveAvailability validateSubscription | Nee |
> | virtueleMachines | Ja |
> | virtualMachines / diagnostischeInstellingen | Nee |
> | virtualMachines / metrischeDefinities | Nee |
> | virtualMachines / statistieken | Nee |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | klassiekeInfrastructureResources | Nee |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Mogelijkheden | Nee |
> | expressRouteCrossConnections | Nee |
> | expressRouteCrossConnections / peerings | Nee |
> | gatewayOndersteundeapparaten | Nee |
> | networkSecurityGroups | Ja |
> | quotas | Nee |
> | gereserveerdIps | Ja |
> | virtuelenetwerken | Ja |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Nee |
> | virtualNetworks / virtualNetworkPeerings | Nee |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Mogelijkheden | Nee |
> | Schijven | Nee |
> | images | Nee |
> | osAfbeeldingen | Nee |
> | osPlatformImages | Nee |
> | publicImages | Nee |
> | quotas | Nee |
> | storageAccounts | Ja |
> | storageAccounts / blobServices | Nee |
> | storageAccounts / fileServices | Nee |
> | storageAccounts / metricDefinities | Nee |
> | storageAccounts / statistieken | Nee |
> | storageAccounts / queueServices | Nee |
> | storageAccounts / services | Nee |
> | storageAccounts / services / diagnosticSettings | Nee |
> | storageAccounts / services / metricDefinities | Nee |
> | storageAccounts / services / statistieken | Nee |
> | storageAccounts / tableServices | Nee |
> | storageAccounts / vmImages | Nee |
> | vmAfbeeldingen | Nee |

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
> | GebruikAggregaten | Nee |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | beschikbaarheidSets | Ja |
> | diskEncryptionSets | Ja |
> | Schijven | Ja |
> | Galeries | Ja |
> | galerijen / toepassingen | Nee |
> | galerijen / toepassingen / versies | Nee |
> | galerijen / afbeeldingen | Nee |
> | galerijen / afbeeldingen / versies | Nee |
> | hostGroepen | Ja |
> | hostGroups / hosts | Ja |
> | images | Ja |
> | proximityPlacementGroepen | Ja |
> | restorePointCollections | Ja |
> | restorePointCollections / restorePoints | Nee |
> | sharedVMImages | Ja |
> | sharedVMImages / versies | Nee |
> | momentopnamen | Ja |
> | virtueleMachines | Ja |
> | virtualMachines / extensies | Ja |
> | virtualMachines / metrischeDefinities | Nee |
> | virtualMachineScaleSets | Ja |
> | virtualMachineScaleSets / extensies | Nee |
> | virtualMachineScaleSets / networkInterfaces | Nee |
> | virtualMachineScaleSets / publicIPAddresses | Nee |
> | virtualMachineScaleSets / virtualMachines | Nee |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Nee |

## <a name="microsoftconsumption"></a>Microsoft.Verbruik

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Geaggregeerde kosten | Nee |
> | Tegoeden | Nee |
> | Budgetten | Nee |
> | Kosten | Nee |
> | CostTags | Nee |
> | Credits | Nee |
> | events | Nee |
> | Prognoses | Nee |
> | Veel | Nee |
> | Markten | Nee |
> | Prijzenoverzichten | Nee |
> | Producten | Nee |
> | ReserveringDetails | Nee |
> | Reserveringsaanbevelingen | Nee |
> | Reserveringssamenvattingen | Nee |
> | Reserveringtransacties | Nee |
> | Tags | Nee |
> | Huurders | Nee |
> | Voorwaarden | Nee |
> | GebruikDetails | Nee |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | containerGroepen | Ja |
> | serviceVerenigingLinks | Nee |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Registers | Ja |
> | registers / builds | Nee |
> | registers / builds / annuleren | Nee |
> | registers / builds / getLogLink | Nee |
> | registers / buildTaken | Ja |
> | registers / buildTaken / stappen | Nee |
> | registers / eventGridFilters | Nee |
> | registers / genererenReferenties | Nee |
> | registers / getBuildSourceUploadUrl | Nee |
> | registers / GetCredentials | Nee |
> | registers / importImage | Nee |
> | registers / privateEndpointConnectionProxies | Nee |
> | registers / privateEndpointConnectionProxies / valideren | Nee |
> | registers / privateEndpointConnections | Nee |
> | registers / privateLinkResources | Nee |
> | registers / wachtrijBouwen | Nee |
> | registers / regenererenCredential | Nee |
> | registers / regenererenReferenties | Nee |
> | registers / replicaties | Ja |
> | registers / runs | Nee |
> | registers / runs / annuleren | Nee |
> | registers / schemaRun | Nee |
> | registers / scopeMaps | Nee |
> | registers / taakRuns | Ja |
> | registers / taken | Ja |
> | registers / tokens | Nee |
> | registers / updateBeleid | Nee |
> | registers / webhooks | Ja |
> | registers / webhooks / getCallbackConfig | Nee |
> | registers / webhooks / ping | Nee |

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
> | FactureringAccounts | Nee |
> | Budgetten | Nee |
> | CloudConnectors | Nee |
> | Connectors | Ja |
> | Afdelingen | Nee |
> | Dimensies | Nee |
> | InschrijvingenAccounts | Nee |
> | Export | Nee |
> | ExternFacturerende rekeningen | Nee |
> | Extern Factureren / Waarschuwingen | Nee |
> | Extern Factureren / Afmetingen | Nee |
> | Extern Facturerende rekeningen / Prognose | Nee |
> | ExternFactureren / Query | Nee |
> | Extern abonnementen | Nee |
> | Extern abonnementen / waarschuwingen | Nee |
> | Extern abonnementen / afmetingen | Nee |
> | Extern abonnementen / prognose | Nee |
> | Extern abonnementen / query | Nee |
> | Prognose | Nee |
> | Query’s uitvoeren | Nee |
> | registreren | Nee |
> | Reportconfigs | Nee |
> | Rapporten | Nee |
> | Instellingen | Nee |
> | showbackRegels | Nee |
> | Weergaven | Nee |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Verzoeken | Nee |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Verenigingen | Nee |
> | resourceProviders | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Banen | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | DataBoxEdgeApparaten | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | werkruimten | Ja |
> | werkruimten / dbWorkspaces | Nee |
> | werkruimten / opslagVersleuteling | Nee |
> | werkruimten / virtualNetworkPeerings | Nee |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Catalogi | Ja |
> | gegevenscatalogi | Ja |
> | datacatalogi / databronnen | Nee |
> | datacatalogi / datasources / scans | Nee |
> | datacatalogi / datasources / scans / datasets | Nee |
> | datacatalogi / datasources / scans / triggers | Nee |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dataFabrieken | Ja |
> | dataFabrieken / diagnostischeInstellingen | Nee |
> | gegevensFabrieken / metrischeDefinities | Nee |
> | dataFactorySchema | Nee |
> | Fabrieken | Ja |
> | fabrieken / integratieRuntimes | Nee |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |
> | accounts / dataLakeStoreAccounts | Nee |
> | accounts / storageAccounts | Nee |
> | accounts / opslagAccounts / containers | Nee |
> | accounts / transferAnalyticsUnits | Nee |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |
> | accounts / eventGridFilters | Nee |
> | accounts / firewallRegels | Nee |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigratie

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | services | Ja |
> | diensten / projecten | Ja |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |
> | rekeningen / aandelen | Nee |
> | accounts / aandelen / datasets | Nee |
> | accounts / aandelen / uitnodigingen | Nee |
> | accounts / aandelen / providershareabonnementen | Nee |
> | accounts / shares / synchronisatieInstellingen | Nee |
> | accounts / aandelenabonnementen | Nee |
> | accounts / sharesubscriptions / consumerSourceDataSets | Nee |
> | accounts / sharesubscriptions / datasetmappings | Nee |
> | accounts / sharesubscriptions / triggers | Nee |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Servers | Ja |
> | servers / adviseurs | Nee |
> | servers / sleutels | Nee |
> | servers / privateEndpointConnectionProxies | Nee |
> | servers / privateEndpointConnections | Nee |
> | servers / privateLinkResources | Nee |
> | servers / queryTeksten | Nee |
> | servers / recoverableServers | Nee |
> | servers / topQueryStatistieken | Nee |
> | servers / virtualNetworkRules | Nee |
> | servers / waitStatistics | Nee |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Servers | Ja |
> | servers / adviseurs | Nee |
> | servers / sleutels | Nee |
> | servers / privateEndpointConnectionProxies | Nee |
> | servers / privateEndpointConnections | Nee |
> | servers / privateLinkResources | Nee |
> | servers / queryTeksten | Nee |
> | servers / recoverableServers | Nee |
> | servers / topQueryStatistieken | Nee |
> | servers / virtualNetworkRules | Nee |
> | servers / waitStatistics | Nee |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | serverGroepen | Ja |
> | Servers | Ja |
> | servers / adviseurs | Nee |
> | servers / sleutels | Nee |
> | servers / privateEndpointConnectionProxies | Nee |
> | servers / privateEndpointConnections | Nee |
> | servers / privateLinkResources | Nee |
> | servers / queryTeksten | Nee |
> | servers / recoverableServers | Nee |
> | servers / topQueryStatistieken | Nee |
> | servers / virtualNetworkRules | Nee |
> | servers / waitStatistics | Nee |
> | serversv2 | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | artefactBronnen | Ja |
> | implementaties | Ja |
> | serviceTopologieën | Ja |
> | serviceTopologieën / diensten | Ja |
> | serviceTopologieën / diensten / serviceUnits | Ja |
> | stappen | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualisatie

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingsgroepen | Ja |
> | toepassingsgroepen / toepassingen | Nee |
> | toepassingsgroepen / desktops | Nee |
> | toepassingsgroepen / startmenu's | Nee |
> | hostpools | Ja |
> | hostpools / sessiehosts | Nee |
> | hostpools / sessionhosts / usersessions | Nee |
> | hostpools / gebruikerssessies | Nee |
> | werkruimten | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Elastische pools | Ja |
> | ElasticPools / IotHubTenants | Ja |
> | ElasticPools / IotHubTenants / beveiligingsinstellingen | Nee |
> | IotHubs | Ja |
> | IotHubs / eventGridFilters | Nee |
> | IotHubs / beveiligingsinstellingen | Nee |
> | ProvisioningServices | Ja |
> | gebruik | Nee |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Pijpleidingen | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Controllers | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | labcenters | Ja |
> | Labs | Ja |
> | labs / omgevingen | Ja |
> | labs / serviceRunners | Ja |
> | labs / virtualMachines | Ja |
> | Planningen | Ja |

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
> | Domeinen | Ja |
> | domeinen / domeinOwnershipIdentifiers | Nee |
> | genereertSsoRequest | Nee |
> | topLevelDomains | Nee |
> | validateDomainRegistrationInformation validateDomainRegistrationInformation validateDomainRegistrationInformation validateDomain | Nee |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | lcsprojects | Nee |
> | lcsprojects / clouddeployments | Nee |
> | lcsprojects / connectoren | Nee |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Domeinen | Ja |
> | domeinen / onderwerpen | Nee |
> | eventAbonnementen | Nee |
> | extensieOnderwerpen | Nee |
> | partnerNaamruimten | Ja |
> | partnerNamespaces / eventKanalen | Nee |
> | partnerRegistraties | Ja |
> | partnerOnderwerpen | Ja |
> | partnerOnderwerpen / eventAbonnementen | Nee |
> | systemTopics systemTopics systemTopics systemTopics | Ja |
> | systemTopics / eventAbonnementen | Nee |
> | Onderwerpen | Ja |
> | onderwerpTypen | Nee |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Clusters | Ja |
> | Naamruimten | Ja |
> | naamruimten / autorisatieregels | Nee |
> | naamruimten / disasterrecoveryconfigs | Nee |
> | naamruimten / eventhubs | Nee |
> | naamruimten / eventhubs / autorisatieregels | Nee |
> | naamruimten / eventhubs / consumentengroepen | Nee |
> | naamruimten / netwerkregelsets | Nee |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | functies | Nee |
> | providers | Nee |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Inschrijven | Nee |
> | galerijobjecten | Nee |
> | genererenartefactaccessuri | Nee |
> | mijngebieden | Nee |
> | myareas / gebieden | Nee |
> | myareas / gebieden / gebieden | Nee |
> | myareas / areas / areas / galleryitems myareas / areas / galleryitems myareas / areas / areas / galleryitems myareas / areas | Nee |
> | myareas / areas / galleryitems | Nee |
> | myareas / galerijitems | Nee |
> | registreren | Nee |
> | resources | Nee |
> | resourcesbyid ophalen | Nee |

## <a name="microsoftgenomics"></a>Microsoft.Genomics (Microsoft.Genomics)

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | autoManagedAccounts | Ja |
> | autoManagedVmConfigurationProfiles | Ja |
> | configuratieProfielToewijzingen | Nee |
> | gastConfiguratieToewijzingen | Nee |
> | Software | Nee |
> | softwareUpdateProfiel | Nee |
> | softwareUpdates | Nee |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | hanaInstances hanaInstances | Ja |
> | sapMonitors | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dedicatedHSM's | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Clusters | Ja |
> | clusters / toepassingen | Nee |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Machines | Ja |
> | machines / extensies | Ja |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dataManagers | Ja |

## <a name="microsofthydra"></a>Microsoft.Hydra (Microsoft.Hydra)

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Onderdelen | Ja |
> | networkScopes | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Banen | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | diagnostische instellingen | Nee |
> | diagnostischeInstellingenCategorieën | Nee |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | appSjablonen | Nee |
> | IoTApps | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.ioTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Graph | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | verwijderde Vaults | Nee |
> | hsmPools | Ja |
> | Kluizen | Ja |
> | kluizen / toegangsbeleid | Nee |
> | kluizen / eventGridFilters | Nee |
> | kluizen / geheimen | Nee |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Clusters | Ja |
> | clusters / gekoppelde databaseconfiguraties | Nee |
> | clusters / databases | Nee |
> | clusters / databases / dataverbindingen | Nee |
> | clusters / databases / eventhubverbindingen | Nee |
> | clusters / databases / principalassignments | Nee |
> | clusters / principalassignments | Nee |
> | clusters / gedeelde identiteiten | Nee |

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
> | hostingOmgevingen | Ja |
> | integratieAccounts | Ja |
> | integratieServiceOmgevingen | Ja |
> | integrationServiceEnvironments / managedApis | Ja |
> | geïsoleerde omgevingen | Ja |
> | Werkstromen | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | inzetPlannen | Ja |
> | Webservices | Ja |
> | Workspaces | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | werkruimten | Ja |
> | werkruimten / berekent | Nee |
> | werkruimten / eventGridFilters | Nee |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Identiteiten | Nee |
> | userAssignedIdentities | Ja |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | marktplaatsRegistratieDefinities | Nee |
> | registratieOpdrachten | Nee |
> | registratieDefinities | Nee |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | getEntities | Nee |
> | beheerGroepen | Nee |
> | beheerGroepen / instellingen | Nee |
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
> | Biedt | Nee |
> | aanbiedingTypen | Nee |
> | aanbiedingTypes / uitgevers | Nee |
> | aanbodTypes / uitgevers / aanbiedingen | Nee |
> | offerTypes / uitgevers / aanbiedingen / plannen | Nee |
> | offerTypes / uitgevers / aanbiedingen / plannen / overeenkomsten | Nee |
> | offerTypes / uitgevers / aanbiedingen / plannen / configs | Nee |
> | offerTypes / uitgevers / aanbiedingen / plannen / configs / importImage | Nee |
> | privategalleryitems | Nee |
> | privateStoreClient | Nee |
> | Producten | Nee |
> | Uitgevers | Nee |
> | uitgevers / aanbiedingen | Nee |
> | uitgevers / aanbiedingen / wijzigingen | Nee |

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
> | Overeenkomsten | Nee |
> | aanbiedingstypen | Nee |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | mediaservices | Ja |
> | mediaservices / accountFilters | Nee |
> | mediaservices / activa | Nee |
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
> | mediaservices / streamingBeleid | Nee |
> | mediaservices / transformaties | Nee |
> | mediaservices / transformaties / jobs | Nee |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | appClusters | Ja |

## <a name="microsoftmigrate"></a>Microsoft.Migreren

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | beoordelingProjecten | Ja |
> | projecten migreren | Ja |
> | moveCollecties verplaatsen | Ja |
> | Projecten | Ja |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Ja |
> | objectAccounts begrijpen | Ja |
> | remoteRenderingAccounts | Ja |
> | spatialAnchorsAccounts | Ja |
> | oppervlakteReconstructieAccounts | Ja |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | netAppAccounts | Ja |
> | netAppAccounts / capaciteitPools | Ja |
> | netAppAccounts / capaciteitPools / volumes | Ja |
> | netAppAccounts / capacityPools / volumes / mountTargets | Ja |
> | netAppAccounts / capaciteitPools / volumes / momentopnamen | Ja |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | NotebookProxies | Nee |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | applicationGateways | Ja |
> | applicationGatewayWebApplicationFirewallBeleid | Ja |
> | applicationSecurityGroups | Ja |
> | azureFirewallFqdnTags | Nee |
> | azureFirewalls | Ja |
> | bastionHosts | Ja |
> | bgpServiceCommunities | Nee |
> | Verbindingen | Ja |
> | ddosCustomPolicies | Ja |
> | ddosProtectionPlans ddosProtectionPlans | Ja |
> | dnsOperationStatussen | Nee |
> | dnszones | Ja |
> | dnszones / A | Nee |
> | dnszones / AAAA | Nee |
> | dnszones / alle | Nee |
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
> | FirewallBeleid | Ja |
> | voordeuren | Ja |
> | frontdoorWebApplicationFirewallManagedRuleSets | Nee |
> | frontdoorWebApplicationFirewallBeleid | Ja |
> | getDnsResourceReference | Nee |
> | internNotify | Nee |
> | loadBalancers | Ja |
> | localNetworkGateways | Ja |
> | natGateways | Ja |
> | netwerkIntentPolicies | Ja |
> | netwerkInterfaces | Ja |
> | netwerkProfielen | Ja |
> | networkSecurityGroups | Ja |
> | networkWatchers | Ja |
> | networkWatchers / connectionMonitors | Ja |
> | networkWatchers / lenzen | Ja |
> | networkWatchers / pingMeshes | Ja |
> | p2sVpnGateways | Ja |
> | privateDnsOperationStatussen | Nee |
> | privateDnsZones | Ja |
> | privateDnsZones / A | Nee |
> | privatednszones / AAAA | Nee |
> | privateDnsZones / alle | Nee |
> | privateDnsZones / CNAME | Nee |
> | privateDnsZones / MX | Nee |
> | privateDnsZones / PTR | Nee |
> | privateDnsZones / SOA | Nee |
> | privateDnsZones / SRV | Nee |
> | privateDnsZones / TXT | Nee |
> | privateDnsZones / virtualNetworkLinks | Ja |
> | privéeindpunten | Ja |
> | privateLinkServices | Ja |
> | publicIPAddresses | Ja |
> | publicIPPrefixes | Ja |
> | routeFilters | Ja |
> | routeTabellen | Ja |
> | serviceEndpointBeleid | Ja |
> | trafficManagerGeografische hiërarchieën | Nee |
> | trafficmanagerprofielen | Ja |
> | trafficmanagerprofielen / heatMaps | Nee |
> | trafficManagerUserMetricsKeys | Nee |
> | virtualHubs | Ja |
> | virtualNetworkGateways | Ja |
> | virtuelenetwerken | Ja |
> | virtualNetworkTaps | Ja |
> | virtualWans virtualWans | Ja |
> | vpnGateways | Ja |
> | vpnSites | Ja |
> | webApplicationFirewallBeleid | Ja |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Naamruimten | Ja |
> | naamruimten / notificationHubs | Ja |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | osNaamruimten | Ja |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | HyperVSites | Ja |
> | ImportSites | Ja |
> | Serversites | Ja |
> | VMwareSites | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Clusters | Ja |
> | linkDoelen | Nee |
> | opslagInsightConfigs | Nee |
> | werkruimten | Ja |
> | werkruimten / gegevensExport | Nee |
> | werkruimten / gegevensBronnen | Nee |
> | werkruimten / linkedServices | Nee |
> | werkruimten / privateEndpointConnectionProxies | Nee |
> | werkruimten / privateEndpointVerbindingen | Nee |
> | werkruimten / privateLinkResources | Nee |
> | werkruimten / query | Nee |
> | werkruimten / scopedPrivateLinkProxies | Nee |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | beheersverenigingen | Nee |
> | beheerconfiguraties | Ja |
> | oplossingen | Ja |
> | Weergaven | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | legacyPeerings | Nee |
> | peerAsns | Nee |
> | peerings | Ja |
> | peeringServiceLanden | Nee |
> | peeringServiceproviders | Nee |
> | peeringServices | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | policyEvents | Nee |
> | policyMetadata | Nee |
> | beleidsstaten | Nee |
> | policyTrackedResources | Nee |
> | herstelacties | Nee |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> |  -consoles | Nee |
> | dashboards | Ja |
> | userSettings | Nee |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | werkruimtecollecties | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Capaciteiten | Ja |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon (Microsoft.ProjectBabylon)

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | back-upProtectedItems | Nee |
> | Kluizen | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Naamruimten | Ja |
> | naamruimten / autorisatieregels | Nee |
> | naamruimten / hybrideverbindingen | Nee |
> | naamruimten / hybrideverbindingen / autorisatieregels | Nee |
> | naamruimten / wcfrelays | Nee |
> | naamruimten / wcfrelays / autorisatieregels | Nee |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Nee |
> | Collecties | Ja |
> | collecties / toepassingen | Nee |
> | collecties / securityprincipals | Nee |
> | sjabloonafbeeldingen | Nee |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Query 's | Ja |
> | resourceChangeDetails | Nee |
> | resourceWijzigingen | Nee |
> | resources | Nee |
> | resourcesGeschiedenis | Nee |
> | abonnementenStatus | Nee |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | beschikbaarheidStatussen | Nee |
> | ChildAvailabilityStatussen | Nee |
> | childResources | Nee |
> | opkomende vraagstukken | Nee |
> | events | Nee |
> | impactresources | Nee |
> | metagegevens | Nee |
> | meldingen | Nee |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Implementaties | Nee |
> | implementaties / bewerkingen | Nee |
> | implementatieScripts | Ja |
> | implementatieScripts / logboeken | Nee |
> | Verwijzigingen | Nee |
> | Melding ResourceJobs | Nee |
> | providers | Nee |
> | resourceGroepen | Nee |
> | Abonnementen | Nee |
> | Huurders | Nee |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingen | Ja |
> | saasresources | Nee |

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
> | toegestane verbindingen | Nee |
> | toepassingWhitelistings | Nee |
> | beoordelingMetadata | Nee |
> | evaluaties | Nee |
> | regels voor meldingen automatisch afwijzen | Nee |
> | automatiseringen | Ja |
> | Instellingen voor automatisch inrichten | Nee |
> | Nalevingen | Nee |
> | gegevensCollectionAgents | Nee |
> | deviceSecurityGroups | Nee |
> | ontdektSecuritySolutions | Nee |
> | externalSecuritySolutions | Nee |
> | Informatiebeschermingsbeleid | Nee |
> | iotSecuritySolutions | Ja |
> | iotSecuritySolutions / analyticsModellen | Nee |
> | iotSecuritySolutions / analyticsModellen / aggregatedAlerts | Nee |
> | iotSecuritySolutions / analyticsModellen / geaggregeerdaanbevelingen | Nee |
> | jitNetworkAccessPolicies | Nee |
> | netwerkGegevens | Nee |
> | policies | Nee |
> | prijzen | Nee |
> | regelgevingComplianceStandards | Nee |
> | wettelijke ComplianceStandards / regulatoryComplianceControls | Nee |
> | wettelijke ComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nee |
> | securityContacten | Nee |
> | securitySolutions | Nee |
> | securitySolutionsReferenceData | Nee |
> | beveiligingsstatussen | Nee |
> | securityStatusesSamenvattingen | Nee |
> | serverVulnerabilityAssessments | Nee |
> | instellingen | Nee |
> | subbeoordelingen | Nee |
> | taken | Nee |
> | Topologieën | Nee |
> | instellingen voor werkruimten | Nee |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | diagnostische instellingen | Nee |
> | diagnostischeInstellingenCategorieën | Nee |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | aggregaties | Nee |
> | alertRegels | Nee |
> | alertRuleTemplates | Nee |
> | bladwijzers | Nee |
> | Gevallen | Nee |
> | gegevensConnectors | Nee |
> | dataConnectorsCheckRequirements | Nee |
> | Entiteiten | Nee |
> | entiteitQueries | Nee |
> | Incidenten | Nee |
> | officeConsents | Nee |
> | instellingen | Nee |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Naamruimten | Ja |
> | naamruimten / autorisatieregels | Nee |
> | naamruimten / disasterrecoveryconfigs | Nee |
> | naamruimten / eventgridfilters | Nee |
> | naamruimten / netwerkregelsets | Nee |
> | naamruimten / wachtrijen | Nee |
> | naamruimten / wachtrijen / autorisatieregels | Nee |
> | naamruimten / onderwerpen | Nee |
> | naamruimten / onderwerpen / autorisatieregels | Nee |
> | naamruimten / onderwerpen / abonnementen | Nee |
> | naamruimten / onderwerpen / abonnementen / regels | Nee |
> | premiumMessagingRegio's | Nee |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingen | Ja |
> | Clusters | Ja |
> | clusters / toepassingen | Nee |
> | containerGroepen | Ja |
> | containerGroepSets | Ja |
> | edgeclusters | Ja |
> | edgeclusters / toepassingen | Nee |
> | managedclusters | Ja |
> | managedclusters / nodetypes | Nee |
> | Netwerken | Ja |
> | secretstores | Ja |
> | secretstores / certificaten | Nee |
> | secretstores / geheimen | Nee |
> | volumes | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingen | Ja |
> | containerGroepen | Ja |
> | Gateways | Ja |
> | Netwerken | Ja |
> | geheimen | Ja |
> | volumes | Ja |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | providerRegistraties | Nee |
> | providerRegistraties / resourceTypeRegistrations | Nee |
> | implementaties | Ja |

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
> | hybrideGebruikSvoordelen | Nee |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingsDefinities | Ja |
> | toepassingen | Ja |
> | jitRequests | Ja |

## <a name="microsoftspoolservice"></a>Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | geregistreerdeAbonnementen | Nee |
> | Spoelen | Ja |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | managedInstances | Ja |
> | managedInstances / databases | Ja |
> | managedInstances / databases / back-upShortTermRetentionPolicies | Nee |
> | managedInstances / databases / schema's / tabellen / kolommen / sensitivityLabels | Nee |
> | managedInstances / databases / kwetsbaarheidAssessments | Nee |
> | managedInstances / databases / kwetsbaarheidAssessments / regels / basislijnen | Nee |
> | managedInstances / encryptionProtector | Nee |
> | managedInstances / toetsen | Nee |
> | managedInstances / restorableDroppedDatabases / back-upShortTermRetentionPolicies | Nee |
> | managedInstances / kwetsbaarheidAssessments | Nee |
> | Servers | Ja |
> | servers / beheerders | Nee |
> | servers / communicatieLinks | Nee |
> | servers / databases | Ja |
> | servers / encryptieProtector | Nee |
> | servers / firewallRegels | Nee |
> | servers / sleutels | Nee |
> | servers / restorableDroppedDatabases | Nee |
> | servers / servicedoelstellingen | Nee |
> | servers / tdecertificaten | Nee |
> | virtuele clusters | Nee |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | SqlVirtualMachineGroepen | Ja |
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
> | storageAccounts / services / metricDefinities | Nee |
> | storageAccounts / tableServices | Nee |
> | gebruik | Nee |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Caches | Ja |
> | caches / storageTargets | Nee |
> | gebruikModellen | Nee |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplicatie

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | replicatiegroepen | Nee |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Nee |
> | storageSyncServices / syncGroups | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee |
> | storageSyncServices / werkstromen | Nee |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Nee |
> | storageSyncServices / syncGroups | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee |
> | storageSyncServices / werkstromen | Nee |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Nee |
> | storageSyncServices / syncGroups | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee |
> | storageSyncServices / werkstromen | Nee |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Managers | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | streamingjobs | Ja |

## <a name="microsoftsubscription"></a>Microsoft.Abonnement

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | annuleren | Nee |
> | Abonnement maken | Nee |
> | inschakelen | Nee |
> | naam wijzigen | Nee |
> | Abonnementsdefinities | Nee |
> | Abonnementen | Nee |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Omgevingen | Ja |
> | omgevingen / toegangsbeleid | Nee |
> | omgevingen / eventsources | Ja |
> | omgevingen / referenceDataSets | Ja |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Ja |
> | dedicatedCloudServices | Ja |
> | virtueleMachines | Ja |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | apparaten | Ja |
> | geregistreerdeAbonnementen | Nee |
> | Leveranciers | Nee |
> | leveranciers / skus | Nee |
> | leveranciers / vnfs | Nee |
> | vnfs | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | apiManagementAccounts | Nee |
> | apiManagementAccounts / apiAcls | Nee |
> | apiManagementAccounts / api's | Nee |
> | apiManagementAccounts / apis / apiAcls | Nee |
> | apiManagementAccounts / apis / connectionAcls | Nee |
> | apiManagementAccounts / api's / verbindingen | Nee |
> | apiManagementAccounts / api's / verbindingen / connectionAcls | Nee |
> | apiManagementAccounts / apis / gelokaliseerdeDefinities | Nee |
> | apiManagementAccounts / connectionAcls | Nee |
> | apiManagementAccounts / verbindingen | Nee |
> | billingMeters | Nee |
> | certificaten | Ja |
> | verbindingenGateways | Ja |
> | Verbindingen | Ja |
> | customApis | Ja |
> | verwijderde Sites | Nee |
> | hostingOmgevingen | Ja |
> | hostingOmgevingen / eventGridFilters | Nee |
> | hostingOmgevingen / multiRolePools | Nee |
> | hostingOmgevingen / workerPools | Nee |
> | publicerenGebruikers | Nee |
> | aanbevelingen | Nee |
> | resourceHealthMetadata | Nee |
> | looptijden | Nee |
> | serverFarms | Ja |
> | serverFarms / eventGridFilters | Nee |
> | sites | Ja |
> | sites/config  | Nee |
> | sites / eventGridFilters | Nee |
> | sites / hostNameBindings | Nee |
> | sites / netwerkConfig | Nee |
> | sites / premieraddons | Ja |
> | sites / slots | Ja |
> | sites / slots / eventGridFilters | Nee |
> | sites / slots / hostNameBindings | Nee |
> | sites / slots / networkConfig | Nee |
> | bronBesturingselementen | Nee |
> | staticSites | Ja |
> | Valideren | Nee |
> | verifiërenHostingEnvironmentVnet | Nee |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | diagnostische instellingen | Nee |
> | diagnostischeInstellingenCategorieën | Nee |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | DeviceServices | Ja |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Onderdelen | Nee |
> | componentenSamenvatting | Nee |
> | monitorInstanties | Nee |
> | monitorInstancesSamenvatting | Nee |
> | Monitoren | Nee |
> | meldingInstellingen | Nee |

## <a name="next-steps"></a>Volgende stappen

Download [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)om dezelfde gegevens te krijgen als een bestand met door komma's gescheiden waarden.
