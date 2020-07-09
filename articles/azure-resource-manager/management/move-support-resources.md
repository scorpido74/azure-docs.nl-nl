---
title: Ondersteuning voor het verplaatsen van bewerkingen per resource type
description: Een lijst met de Azure-resource typen die kunnen worden verplaatst naar een nieuwe resource groep of een nieuw abonnement.
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 785243676040fc86d42e3125684239ebe6da2061
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86134662"
---
# <a name="move-operation-support-for-resources"></a>Ondersteuning voor het verplaatsen van resources

Dit artikel geeft een overzicht van de vraag of een Azure-resource type de verplaatsings bewerking ondersteunt. Het bevat ook informatie over speciale voor waarden die u moet overwegen bij het verplaatsen van een resource.

Ga naar de naam ruimte van een resource provider:
> [!div class="op_single_selector"]
> - [Micro soft. AAD](#microsoftaad)
> - [micro soft. aadiam](#microsoftaadiam)
> - [Micro soft. Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Micro soft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Micro soft. AppConfiguration](#microsoftappconfiguration)
> - [Micro soft. AppPlatform](#microsoftappplatform)
> - [Micro soft. AppService](#microsoftappservice)
> - [Micro soft. Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Micro soft. AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Micro soft. Azureworden](#microsoftazuredata)
> - [Micro soft. AzureStack](#microsoftazurestack)
> - [Micro soft. AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Micro soft. BizTalkServices](#microsoftbiztalkservices)
> - [Micro soft. Block Chain](#microsoftblockchain)
> - [Micro soft. BlockchainTokens](#microsoftblockchaintokens)
> - [Micro soft. blauw druk](#microsoftblueprint)
> - [Micro soft. BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Micro soft. capacity](#microsoftcapacity)
> - [Micro soft. CDN](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Micro soft. ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Micro soft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Micro soft. ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Micro soft. verbruik](#microsoftconsumption)
> - [Micro soft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Micro soft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Micro soft. CostManagement](#microsoftcostmanagement)
> - [Micro soft. CostManagementExports](#microsoftcostmanagementexports)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Micro soft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Micro soft. CustomProviders](#microsoftcustomproviders)
> - [Micro soft. DataBox](#microsoftdatabox)
> - [Micro soft. DataBoxEdge](#microsoftdataboxedge)
> - [Micro soft. Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Micro soft. DataConnect](#microsoftdataconnect)
> - [Micro soft. DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Micro soft. DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Micro soft. DataMigration](#microsoftdatamigration)
> - [Micro soft. DataProtection](#microsoftdataprotection)
> - [Micro soft. DataShare](#microsoftdatashare)
> - [Micro soft. DBforMariaDB](#microsoftdbformariadb)
> - [Micro soft. DBforMySQL](#microsoftdbformysql)
> - [Micro soft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Micro soft. DeploymentManager](#microsoftdeploymentmanager)
> - [Micro soft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Micro soft. DevOps](#microsoftdevops)
> - [Micro soft. DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Micro soft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Micro soft. DomainRegistration](#microsoftdomainregistration)
> - [Micro soft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Micro soft. EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Micro soft. experimenten](#microsoftexperimentation)
> - [Micro soft. Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Micro soft. Genomics](#microsoftgenomics)
> - [Micro soft. GuestConfiguration](#microsoftguestconfiguration)
> - [Micro soft. HanaOnAzure](#microsofthanaonazure)
> - [Micro soft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Micro soft. HealthcareApis](#microsofthealthcareapis)
> - [Micro soft. HybridCompute](#microsofthybridcompute)
> - [Micro soft. HybridData](#microsofthybriddata)
> - [Micro soft. HybridNetwork](#microsofthybridnetwork)
> - [Micro soft. Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [micro soft. Insights](#microsoftinsights)
> - [Micro soft. IoTCentral](#microsoftiotcentral)
> - [Micro soft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Micro soft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Micro soft. LabServices](#microsoftlabservices)
> - [Micro soft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Micro soft. bestand locationservices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Micro soft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Micro soft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Micro soft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Micro soft. onderhoud](#microsoftmaintenance)
> - [Micro soft. ManagedIdentity](#microsoftmanagedidentity)
> - [Micro soft. ManagedNetwork](#microsoftmanagednetwork)
> - [Micro soft. ManagedServices](#microsoftmanagedservices)
> - [Micro soft. Management](#microsoftmanagement)
> - [Micro soft. Maps](#microsoftmaps)
> - [Micro soft. Marketplace](#microsoftmarketplace)
> - [Micro soft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Micro soft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Micro soft. migrate](#microsoftmigrate)
> - [Micro soft. MixedReality](#microsoftmixedreality)
> - [Micro soft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Micro soft. ObjectStore](#microsoftobjectstore)
> - [Micro soft. OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Micro soft. peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Micro soft. Portal](#microsoftportal)
> - [Micro soft. PowerBI](#microsoftpowerbi)
> - [Micro soft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Micro soft. PowerPlatform](#microsoftpowerplatform)
> - [Micro soft. ProjectBabylon](#microsoftprojectbabylon)
> - [Micro soft. ProviderHub](#microsoftproviderhub)
> - [Micro soft. Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Micro soft. RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Micro soft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Micro soft. SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Micro soft. SecurityInsights](#microsoftsecurityinsights)
> - [Micro soft. SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Micro soft. ServiceFabric](#microsoftservicefabric)
> - [Micro soft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Micro soft. Services](#microsoftservices)
> - [Micro soft. SignalRService](#microsoftsignalrservice)
> - [Micro soft. SoftwarePlan](#microsoftsoftwareplan)
> - [Micro soft. Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Micro soft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Micro soft. StorageCache](#microsoftstoragecache)
> - [Micro soft. StorageSync](#microsoftstoragesync)
> - [Micro soft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Micro soft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Micro soft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Micro soft. Subscription](#microsoftsubscription)
> - [micro soft. ondersteuning](#microsoftsupport)
> - [Micro soft. Synapse](#microsoftsynapse)
> - [Micro soft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Micro soft. token](#microsofttoken)
> - [Micro soft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [micro soft. Visual Studio](#microsoftvisualstudio)
> - [Micro soft. VMware](#microsoftvmware)
> - [Micro soft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Micro soft. VnfManager](#microsoftvnfmanager)
> - [Micro soft. VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Micro soft. WindowsESU](#microsoftwindowsesu)
> - [Micro soft. WindowsIoT](#microsoftwindowsiot)
> - [Micro soft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Micro soft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Micro soft. AAD

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | domainservices | Nee | Nee |
> | domainservices / oucontainer | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftaadiam"></a>micro soft. aadiam

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | Nee | Nee |
> | diagnosticsettingscategories | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | privatelinkforazuread | Ja | Ja |
> | tenants | Ja | Ja |

## <a name="microsoftaddons"></a>Micro soft. Addons

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | supportproviders | Nee | Nee |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | Nee | Nee |
> | addsservices | Nee | Nee |
> | middelen | Nee | Nee |
> | anonymousapiusers | Nee | Nee |
> | configuratie | Nee | Nee |
> | logboeken | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | rapporten | Nee | Nee |
> | servicehealthmetrics | Nee | Nee |
> | services | Nee | Nee |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | configuraties | Nee | Nee |
> | generaterecommendations | Nee | Nee |
> | metagegevens | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | aanbevelingen | Nee | Nee |
> | onderdrukkingen | Nee | Nee |

## <a name="microsoftalertsmanagement"></a>Micro soft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | actionrules | Ja | Ja |
> | waarschuwingen | Nee | Nee |
> | alertslist | Nee | Nee |
> | alertsmetadata | Nee | Nee |
> | alertssummary | Nee | Nee |
> | alertssummarylist | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | smartdetectoralertrules | Ja | Ja |
> | smartgroups | Nee | Nee |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | locaties | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/operationstatuses | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | Server | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | Nee | Nee |
> | checknameavailability | Nee | Nee |
> | checkservicenameavailability | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | reportfeedback | Nee | Nee |
> | service | Ja | Ja |
> | validateservicename | Nee | Nee |

> [!IMPORTANT]
> Een API Management-service die is ingesteld op de verbruiks-SKU, kan niet worden verplaatst.

## <a name="microsoftappconfiguration"></a>Micro soft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | configurationstores | Ja | Ja |
> | configurationstores / eventgridfilters | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/operationsstatus | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftappplatform"></a>Micro soft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | locaties | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/operationstatus | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | lente | Ja | Ja |
> | lente/apps | Nee | Nee |
> | lente/apps/implementaties | Nee | Nee |

## <a name="microsoftappservice"></a>Micro soft. AppService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | apiapps | Nee | Nee |
> | appidentities | Nee | Nee |
> | gateways | Nee | Nee |

> [!IMPORTANT]
> Zie [app service richt lijnen voor verplaatsen](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftattestation"></a>Micro soft. Attestation

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Ja | Ja |
> | bewerkingen | Nee | Nee |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checkaccess | Nee | Nee |
> | classicadministrators | Nee | Nee |
> | dataaliases | Nee | Nee |
> | denyassignments | Nee | Nee |
> | elevateaccess | Nee | Nee |
> | findorphanroleassignments | Nee | Nee |
> | vergren delingen | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | operationstatus | Nee | Nee |
> | permissions | Nee | Nee |
> | policyassignments | Nee | Nee |
> | policydefinitions | Nee | Nee |
> | policysetdefinitions | Nee | Nee |
> | privatelinkassociations | Nee | Nee |
> | provideroperations | Nee | Nee |
> | resourcemanagementprivatelinks | Nee | Nee |
> | roleassignments | Nee | Nee |
> | roleassignmentsusagemetrics | Nee | Nee |
> | roledefinitions | Nee | Nee |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Ja | Ja |
> | automationaccounts/configuraties | Ja | Ja |
> | automationaccounts/Jobs | Nee | Nee |
> | automationaccounts / privateendpointconnectionproxies | Nee | Nee |
> | automationaccounts / privateendpointconnections | Nee | Nee |
> | automationaccounts / privatelinkresources | Nee | Nee |
> | automationaccounts/runbooks | Ja | Ja |
> | automationaccounts / softwareupdateconfigurations | Nee | Nee |
> | automationaccounts/webhooks | Nee | Nee |
> | bewerkingen | Nee | Nee |

> [!IMPORTANT]
> Runbooks moeten zich in dezelfde resource groep bevinden als het Automation-account.
>
> Zie [uw Azure Automation-account naar een ander abonnement verplaatsen](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json)voor meer informatie.

## <a name="microsoftavs"></a>Micro soft. AVS

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | locaties | Nee | Nee |
> | locaties/checkquotaavailability | Nee | Nee |
> | locaties/checktrialavailability | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | privateclouds | Ja | Ja |
> | privateclouds/clusters | Nee | Nee |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Ja | Ja |
> | b2ctenants | Nee | Nee |
> | checknameavailability | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftazuredata"></a>Micro soft. Azureworden

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Nee | Nee |
> | hybriddatamanagers | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | postgresinstances | Nee | Nee |
> | sqlinstances | Nee | Nee |
> | sqlmanagedinstances | Nee | Nee |
> | sqlserverinstances | Nee | Nee |
> | sqlserverregistrations | Ja | Ja |
> | sqlserverregistrations / sqlservers | Nee | Nee |

## <a name="microsoftazurestack"></a>Micro soft. AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | registraties | Ja | Ja |
> | registraties/customersubscriptions | Nee | Nee |
> | registraties/producten | Nee | Nee |

## <a name="microsoftazurestackhci"></a>Micro soft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ja | Ja |
> | locaties | Nee | Nee |
> | locaties/accountoperationresults | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | locaties/quota's | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Nee | Nee |
> | billingaccounts/overeenkomsten | Nee | Nee |
> | billingaccounts / billingpermissions | Nee | Nee |
> | billingaccounts / billingprofiles | Nee | Nee |
> | billingaccounts / billingprofiles / availablebalance | Nee | Nee |
> | billingaccounts / billingprofiles / billingpermissions | Nee | Nee |
> | billingaccounts / billingprofiles / billingroleassignments | Nee | Nee |
> | billingaccounts / billingprofiles / billingroledefinitions | Nee | Nee |
> | billingaccounts / billingprofiles / billingsubscriptions | Nee | Nee |
> | billingaccounts / billingprofiles / createbillingroleassignment | Nee | Nee |
> | billingaccounts/billingprofiles/klanten | Nee | Nee |
> | billingaccounts/billingprofiles/instructies | Nee | Nee |
> | billingaccounts/billingprofiles/facturen | Nee | Nee |
> | billingaccounts/billingprofiles/facturen/prijzen overzicht | Nee | Nee |
> | billingaccounts/billingprofiles/facturen/trans acties | Nee | Nee |
> | billingaccounts / billingprofiles / invoicesections | Nee | Nee |
> | billingaccounts / billingprofiles / invoicesections / billingpermissions | Nee | Nee |
> | billingaccounts / billingprofiles / invoicesections / billingroleassignments | Nee | Nee |
> | billingaccounts / billingprofiles / invoicesections / billingroledefinitions | Nee | Nee |
> | billingaccounts / billingprofiles / invoicesections / billingsubscriptions | Nee | Nee |
> | billingaccounts / billingprofiles / invoicesections / createbillingroleassignment | Nee | Nee |
> | billingaccounts / billingprofiles / invoicesections / initiatetransfer | Nee | Nee |
> | billingaccounts/billingprofiles/invoicesections/Products | Nee | Nee |
> | billingaccounts/billingprofiles/invoicesections/Products/overdracht | Nee | Nee |
> | billingaccounts/billingprofiles/invoicesections/Products/updateautorenew | Nee | Nee |
> | billingaccounts/billingprofiles/invoicesections/trans acties | Nee | Nee |
> | billingaccounts/billingprofiles/invoicesections/transfers | Nee | Nee |
> | billingaccounts / billingprofiles / patchoperations | Nee | Nee |
> | billingaccounts / billingprofiles / paymentmethods | Nee | Nee |
> | billingaccounts/billingprofiles/beleid | Nee | Nee |
> | billingaccounts/billingprofiles/prijzen overzicht | Nee | Nee |
> | billingaccounts / billingprofiles / pricesheetdownloadoperations | Nee | Nee |
> | billingaccounts/billingprofiles/producten | Nee | Nee |
> | billingaccounts/billingprofiles/trans acties | Nee | Nee |
> | billingaccounts / billingroleassignments | Nee | Nee |
> | billingaccounts / billingroledefinitions | Nee | Nee |
> | billingaccounts / billingsubscriptions | Nee | Nee |
> | billingaccounts/billingsubscriptions/facturen | Nee | Nee |
> | billingaccounts / createbillingroleassignment | Nee | Nee |
> | billingaccounts / createinvoicesectionoperations | Nee | Nee |
> | billingaccounts/klanten | Nee | Nee |
> | billingaccounts/klanten/billingpermissions | Nee | Nee |
> | billingaccounts/klanten/billingsubscriptions | Nee | Nee |
> | billingaccounts/klanten/initiatetransfer | Nee | Nee |
> | billingaccounts/klanten/beleids regels | Nee | Nee |
> | billingaccounts/klanten/producten | Nee | Nee |
> | billingaccounts/klanten/trans acties | Nee | Nee |
> | billingaccounts/klanten/overdrachten | Nee | Nee |
> | billingaccounts/afdelingen | Nee | Nee |
> | billingaccounts / enrollmentaccounts | Nee | Nee |
> | billingaccounts/facturen | Nee | Nee |
> | billingaccounts / invoicesections | Nee | Nee |
> | billingaccounts / invoicesections / billingsubscriptionmoveoperations | Nee | Nee |
> | billingaccounts / invoicesections / billingsubscriptions | Nee | Nee |
> | billingaccounts/invoicesections/billingsubscriptions/overdracht | Nee | Nee |
> | billingaccounts/invoicesections/verhoogde bevoegdheid | Nee | Nee |
> | billingaccounts / invoicesections / initiatetransfer | Nee | Nee |
> | billingaccounts / invoicesections / patchoperations | Nee | Nee |
> | billingaccounts / invoicesections / productmoveoperations | Nee | Nee |
> | billingaccounts/invoicesections/producten | Nee | Nee |
> | billingaccounts/invoicesections/producten/overdracht | Nee | Nee |
> | billingaccounts/invoicesections/Products/updateautorenew | Nee | Nee |
> | billingaccounts / invoicesections / producttransfersresults | Nee | Nee |
> | billingaccounts/invoicesections/trans acties | Nee | Nee |
> | billingaccounts/invoicesections/overdrachten | Nee | Nee |
> | billingaccounts / lineofcredit | Nee | Nee |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpermission | Nee | Nee |
> | billingaccounts / operationresults | Nee | Nee |
> | billingaccounts / patchoperations | Nee | Nee |
> | billingaccounts / paymentmethods | Nee | Nee |
> | billingaccounts/producten | Nee | Nee |
> | billingaccounts/trans acties | Nee | Nee |
> | billingperiods | Nee | Nee |
> | billingpermissions | Nee | Nee |
> | billingproperty | Nee | Nee |
> | billingroleassignments | Nee | Nee |
> | billingroledefinitions | Nee | Nee |
> | createbillingroleassignment | Nee | Nee |
> | afdeling | Nee | Nee |
> | enrollmentaccounts | Nee | Nee |
> | factureer | Nee | Nee |
> | operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | operationstatus | Nee | Nee |
> | Making | Nee | Nee |
> | overdrachten/accepttransfer | Nee | Nee |
> | overdrachten/declinetransfer | Nee | Nee |
> | overdrachten/operationstatus | Nee | Nee |
> | overdrachten/validatetransfer | Nee | Nee |
> | validateaddress | Nee | Nee |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | Nee | Nee |
> | mapapis | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | updatecommunicationpreference | Nee | Nee |

## <a name="microsoftbiztalkservices"></a>Micro soft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | biztalk | Nee | Nee |

## <a name="microsoftblockchain"></a>Micro soft. Block Chain

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Nee | Nee |
> | cordamembers | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/blockchainmemberoperationresults | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | locaties/listconsortiums | Nee | Nee |
> | locaties/watcheroperationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | Volg | Nee | Nee |

## <a name="microsoftblockchaintokens"></a>Micro soft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | bewerkingen | Nee | Nee |
> | tokenservices | Nee | Nee |

## <a name="microsoftblueprint"></a>Micro soft. blauw druk

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Nee | Nee |
> | blueprintassignments / assignmentoperations | Nee | Nee |
> | blueprintassignments/bewerkingen | Nee | Nee |
> | blauw drukken | Nee | Nee |
> | blauw drukken/artefacten | Nee | Nee |
> | blauw drukken/versies | Nee | Nee |
> | blauw drukken/versies/artefacten | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftbotservice"></a>Micro soft. BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | botservices | Ja | Ja |
> | botservices/kanalen | Nee | Nee |
> | botservices/verbindingen | Nee | Nee |
> | checknameavailability | Nee | Nee |
> | listauthserviceproviders | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/operationsstatus | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | redis | Ja | Ja |
> | redis/eventgridfilters | Nee | Nee |
> | redis/privatelinkresources | Nee | Nee |
> | redisenterprise | Nee | Nee |

> [!IMPORTANT]
> Als de Azure-cache voor redis-exemplaar is geconfigureerd met een virtueel netwerk, kan het exemplaar niet worden verplaatst naar een ander abonnement. Zie [beperkingen](./move-limitations/networking-move-limitations.md)voor het verplaatsen van netwerken.

## <a name="microsoftcapacity"></a>Micro soft. capacity

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | appliedreservations | Nee | Nee |
> | calculateexchange | Nee | Nee |
> | calculateprice | Nee | Nee |
> | calculatepurchaseprice | Nee | Nee |
> | catalogi | Nee | Nee |
> | checkoffers | Nee | Nee |
> | checkpurchasestatus | Nee | Nee |
> | checkscopes | Nee | Nee |
> | commercialreservationorders | Nee | Nee |
> | Exchange | Nee | Nee |
> | listbenefits | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | placepurchaseorder | Nee | Nee |
> | reservationorders | Nee | Nee |
> | reservationorders / availablescopes | Nee | Nee |
> | reservationorders / calculaterefund | Nee | Nee |
> | reservationorders/samen voegen | Nee | Nee |
> | reservationorders/reserve ringen | Nee | Nee |
> | reservationorders/reserve ringen/availablescopes | Nee | Nee |
> | reservationorders/reserve ringen/revisies | Nee | Nee |
> | reservationorders/retour neren | Nee | Nee |
> | reservationorders/splitsen | Nee | Nee |
> | reservationorders/swap | Nee | Nee |
> | ringen | Nee | Nee |
> | resources | Nee | Nee |
> | validatereservationorder | Nee | Nee |

## <a name="microsoftcdn"></a>Micro soft. CDN

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Nee | Nee |
> | cdnwebapplicationfirewallpolicies | Ja | Ja |
> | checknameavailability | Nee | Nee |
> | checkresourceusage | Nee | Nee |
> | edgenodes | Nee | Nee |
> | operationresults | Nee | Nee |
> | operationresults / profileresults | Nee | Nee |
> | operationresults / profileresults / endpointresults | Nee | Nee |
> | operationresults / profileresults / endpointresults / customdomainresults | Nee | Nee |
> | operationresults / profileresults / endpointresults / origingroupresults | Nee | Nee |
> | operationresults / profileresults / endpointresults / originresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | profielen | Ja | Ja |
> | profielen/eind punten | Ja | Ja |
> | profielen/eind punten/customdomains | Nee | Nee |
> | profielen/eind punten/origingroups | Nee | Nee |
> | profielen/eind punten/oorsprong | Nee | Nee |
> | validateprobe | Nee | Nee |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | certificateorders | Ja | Ja |
> | certificateorders/certificaten | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | validatecertificateregistrationinformation | Nee | Nee |

> [!IMPORTANT]
> Zie [app service richt lijnen voor verplaatsen](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftchangeanalysis"></a>Micro soft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | bewerkingen | Nee | Nee |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | mogelijkheden | Nee | Nee |
> | checkdomainnameavailability | Nee | Nee |
> | domein naam | Yes | No |
> | domein naam/mogelijkheden | Nee | Nee |
> | domein naam/internalloadbalancers | Nee | Nee |
> | domein naam/servicecertificates | Nee | Nee |
> | domein naam/sleuven | Nee | Nee |
> | domein naam/sleuven/rollen | Nee | Nee |
> | domein naam/sleuven/rollen/metricdefinitions | Nee | Nee |
> | domein naam/sleuven/rollen/metrieken | Nee | Nee |
> | movesubscriptionresources | Nee | Nee |
> | operatingsystemfamilies | Nee | Nee |
> | operatingsystems | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | operationstatuses | Nee | Nee |
> | quotas | Nee | Nee |
> | resourcetypes | Nee | Nee |
> | validatesubscriptionmoveavailability | Nee | Nee |
> | informatie | Ja | No |
> | informatie/diagnosticsettings | Nee | Nee |
> | informatie/metricdefinitions | Nee | Nee |
> | informatie/meet waarden | Nee | Nee |

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/classic-model-move-limitations.md)voor het verplaatsen van de klassieke implementatie. Klassieke implementatie resources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

## <a name="microsoftclassicinfrastructuremigrate"></a>Micro soft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Nee | Nee |

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/classic-model-move-limitations.md)voor het verplaatsen van de klassieke implementatie. Klassieke implementatie resources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | mogelijkheden | Nee | Nee |
> | expressroutecrossconnections | Nee | Nee |
> | expressroutecrossconnections/peerings | Nee | Nee |
> | gatewaysupporteddevices | Nee | Nee |
> | networksecuritygroups | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | quotas | Nee | Nee |
> | reservedips | Nee | Nee |
> | virtualnetworks | Nee | Nee |
> | virtualnetworks/remotevirtualnetworkpeeringproxies | Nee | Nee |
> | virtualnetworks/virtualnetworkpeerings | Nee | Nee |

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/classic-model-move-limitations.md)voor het verplaatsen van de klassieke implementatie. Klassieke implementatie resources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | mogelijkheden | Nee | Nee |
> | checkstorageaccountavailability | Nee | Nee |
> | cd's | Nee | Nee |
> | images | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | osimages | Nee | Nee |
> | osplatformimages | Nee | Nee |
> | publicimages | Nee | Nee |
> | quotas | Nee | Nee |
> | Storage accounts | Yes | No |
> | Storage accounts/blobservices | Nee | Nee |
> | Storage accounts/fileservices | Nee | Nee |
> | Storage accounts/metricdefinitions | Nee | Nee |
> | Storage accounts/meet waarden | Nee | Nee |
> | Storage accounts/queueservices | Nee | Nee |
> | Storage accounts/Services | Nee | Nee |
> | Storage accounts/Services/diagnosticsettings | Nee | Nee |
> | Storage accounts/Services/metricdefinitions | Nee | Nee |
> | Storage accounts/Services/metrische gegevens | Nee | Nee |
> | Storage accounts/tableservices | Nee | Nee |
> | Storage accounts/vmimages | Nee | Nee |
> | vmimages | Nee | Nee |

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/classic-model-move-limitations.md)voor het verplaatsen van de klassieke implementatie. Klassieke implementatie resources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

## <a name="microsoftclassicsubscription"></a>Micro soft. ClassicSubscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | bewerkingen | Nee | Nee |

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/classic-model-move-limitations.md)voor het verplaatsen van de klassieke implementatie. Klassieke implementatie resources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |
> | checkdomainavailability | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/checkskuavailability | Nee | Nee |
> | locaties/deletevirtualnetworkorsubnets | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | bewerkingen | Nee | Nee |
> | ratecard | Nee | Nee |
> | usageaggregates | Nee | Nee |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Availability sets | Ja | Ja |
> | diskaccesses | Nee | Nee |
> | diskencryptionsets | Nee | Nee |
> | cd's | Ja | Ja |
> | Galerij | Nee | Nee |
> | galerieën/afbeeldingen | Nee | Nee |
> | galerieën/afbeeldingen/versies | Nee | Nee |
> | hostgroups | Nee | Nee |
> | hostgroups/hosts | Nee | Nee |
> | images | Ja | Ja |
> | locaties | Nee | Nee |
> | locaties/artifactpublishers | Nee | Nee |
> | locaties/capsoperations | Nee | Nee |
> | locaties/onkoperen | Nee | Nee |
> | locaties/loganalytics | Nee | Nee |
> | locaties/bewerkingen | Nee | Nee |
> | locaties/uitgevers | Nee | Nee |
> | locaties/opdracht uitvoeren | Nee | Nee |
> | locaties/gebruik | Nee | Nee |
> | locaties/informatie | Nee | Nee |
> | locaties/toegestane VM | Nee | Nee |
> | locaties/vsmoperations | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | proximityplacementgroups | Ja | Ja |
> | restorepointcollections | Nee | Nee |
> | restorepointcollections / restorepoints | Nee | Nee |
> | sharedvmextensions | Nee | Nee |
> | sharedvmimages | Nee | Nee |
> | sharedvmimages/versies | Nee | Nee |
> | momentopnamen | Ja | Ja |
> | sshpublickeys | Nee | Nee |
> | informatie | Ja | Ja |
> | informatie/extensies | Ja | Ja |
> | informatie/metricdefinitions | Nee | Nee |
> | informatie/RunCommand | Nee | Nee |
> | virtualmachinescalesets | Ja | Ja |
> | virtualmachinescalesets/extensies | Nee | Nee |
> | virtualmachinescalesets/networkinterfaces | Nee | Nee |
> | virtualmachinescalesets/publicipaddresses | Nee | Nee |
> | virtualmachinescalesets/informatie | Nee | Nee |
> | virtualmachinescalesets/informatie/networkinterfaces | Nee | Nee |

> [!IMPORTANT]
> Zie [virtual machines richt lijnen voor verplaatsen](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Micro soft. verbruik

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Nee | Nee |
> | saldi | Nee | Nee |
> | budgetten | Nee | Nee |
> | belastingen | Nee | Nee |
> | costtags | Nee | Nee |
> | aanvragen | Nee | Nee |
> | events | Nee | Nee |
> | Forecast | Nee | Nee |
> | extra | Nee | Nee |
> | markt plaatsen | Nee | Nee |
> | operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | operationstatus | Nee | Nee |
> | pricesheets | Nee | Nee |
> | producten | Nee | Nee |
> | reservationdetails | Nee | Nee |
> | reservationrecommendationdetails | Nee | Nee |
> | reservationrecommendations | Nee | Nee |
> | reservationsummaries | Nee | Nee |
> | reservationtransactions | Nee | Nee |
> | tags | Nee | Nee |
> | tenants | Nee | Nee |
> | inzake | Nee | Nee |
> | usagedetails | Nee | Nee |

## <a name="microsoftcontainerinstance"></a>Micro soft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | containergroups | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/cachedimages | Nee | Nee |
> | locaties/mogelijkheden | Nee | Nee |
> | locaties/deletevirtualnetworkorsubnets | Nee | Nee |
> | locaties/bewerkingen | Nee | Nee |
> | locaties/gebruik | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | serviceassociationlinks | Nee | Nee |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/autoriseren | Nee | Nee |
> | locaties/deletevirtualnetworkorsubnets | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/setupauth | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | registers | Ja | Ja |
> | registers/agentpools | Ja | Ja |
> | registers/agentpools/listqueuestatus | Nee | Nee |
> | registers/builds | Nee | Nee |
> | registers/builds/annuleren | Nee | Nee |
> | registers/builds/getloglink | Nee | Nee |
> | registers/buildtasks | Ja | Ja |
> | registers/buildtasks/listsourcerepositoryproperties | Nee | Nee |
> | registers/buildtasks/stappen | Nee | Nee |
> | registers/buildtasks/stappen/listbuildarguments | Nee | Nee |
> | registers/eventgridfilters | Nee | Nee |
> | registers/exportpipelines | Nee | Nee |
> | registers/generatecredentials | Nee | Nee |
> | registers/getbuildsourceuploadurl | Nee | Nee |
> | registers/getcredentials | Nee | Nee |
> | registers/importimage | Nee | Nee |
> | registers/importpipelines | Nee | Nee |
> | registers/listbuildsourceuploadurl | Nee | Nee |
> | registers/listcredentials | Nee | Nee |
> | registers/listpolicies | Nee | Nee |
> | registers/listusages | Nee | Nee |
> | registers/pipelineruns | Nee | Nee |
> | registers/privateendpointconnectionproxies | Nee | Nee |
> | registers/privateendpointconnectionproxies/valideren | Nee | Nee |
> | registers/privateendpointconnections | Nee | Nee |
> | registers/privatelinkresources | Nee | Nee |
> | registers/queuebuild | Nee | Nee |
> | registers/regeneratecredential | Nee | Nee |
> | registers/regeneratecredentials | Nee | Nee |
> | registers/replicaties | Ja | Ja |
> | registers/uitvoeringen | Nee | Nee |
> | registers/uitvoeringen/annuleren | Nee | Nee |
> | registers/uitvoeringen/listlogsasurl | Nee | Nee |
> | registers/schedulerun | Nee | Nee |
> | registers/scopemaps | Nee | Nee |
> | registers/taskruns | Nee | Nee |
> | registers/taskruns/listdetails | Nee | Nee |
> | registers/taken | Ja | Ja |
> | registers/taken/listdetails | Nee | Nee |
> | registers/tokens | Nee | Nee |
> | registers/updatepolicies | Nee | Nee |
> | registers/webhooks | Ja | Ja |
> | registers/webhooks/getcallbackconfig | Nee | Nee |
> | registers/webhooks/listevents | Nee | Nee |
> | registers/webhooks/ping | Nee | Nee |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | containerservices | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/openshiftclusters | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/bewerkingen | Nee | Nee |
> | locaties/Orchestrator | Nee | Nee |
> | managedclusters | Nee | Nee |
> | openshiftmanagedclusters | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | toepassingen | Nee | Nee |

## <a name="microsoftcortanaanalytics"></a>Micro soft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftcostmanagement"></a>Micro soft. CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | waarschuwingen | Nee | Nee |
> | billingaccounts | Nee | Nee |
> | budgetten | Nee | Nee |
> | cloudconnectors | Nee | Nee |
> | connectoren | Ja | Ja |
> | afdeling | Nee | Nee |
> | hoogte | Nee | Nee |
> | enrollmentaccounts | Nee | Nee |
> | dump | Nee | Nee |
> | externalbillingaccounts | Nee | Nee |
> | externalbillingaccounts/waarschuwingen | Nee | Nee |
> | externalbillingaccounts/dimensies | Nee | Nee |
> | externalbillingaccounts/prognose | Nee | Nee |
> | externalbillingaccounts/query | Nee | Nee |
> | externalsubscriptions | Nee | Nee |
> | externalsubscriptions/waarschuwingen | Nee | Nee |
> | externalsubscriptions/dimensies | Nee | Nee |
> | externalsubscriptions/prognose | Nee | Nee |
> | externalsubscriptions/query | Nee | Nee |
> | forecast | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | query | Nee | Nee |
> | registreren | Nee | Nee |
> | reportconfigs | Nee | Nee |
> | rapporten | Nee | Nee |
> | instellingen | Nee | Nee |
> | showbackrules | Nee | Nee |
> | Weergaven | Nee | Nee |

## <a name="microsoftcostmanagementexports"></a>Micro soft. CostManagementExports

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | bewerkingen | Nee | Nee |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hubs | Nee | Nee |

## <a name="microsoftcustomerlockbox"></a>Micro soft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | bewerkingen | Nee | Nee |
> | aanvragen | Nee | Nee |

## <a name="microsoftcustomproviders"></a>Micro soft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | lidkoppelingen | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/operationstatuses | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | resourceproviders | Ja | Ja |

## <a name="microsoftdatabox"></a>Micro soft. DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | functies | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/availableskus | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/regionconfiguration | Nee | Nee |
> | locaties/validateaddress | Nee | Nee |
> | locaties/validateinputs | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftdataboxedge"></a>Micro soft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | availableskus | Nee | Nee |
> | databoxedgedevices | Ja | Ja |
> | databoxedgedevices / checknameavailability | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftdatabricks"></a>Micro soft. Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | locaties | Nee | Nee |
> | locaties/getnetworkpolicies | Nee | Nee |
> | locaties/operationstatuses | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | workspaces | Nee | Nee |
> | werk ruimten/dbworkspaces | Nee | Nee |
> | werk ruimten/virtualnetworkpeerings | Nee | Nee |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | catalogi | Ja | Ja |
> | checknameavailability | Nee | Nee |
> | datacatalogs | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/taken | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftdataconnect"></a>Micro soft. DataConnect

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Nee | Nee |

## <a name="microsoftdataexchange"></a>Micro soft. DataExchange

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Pakketten | Nee | Nee |
> | plant | Nee | Nee |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checkazuredatafactorynameavailability | Nee | Nee |
> | checkdatafactorynameavailability | Nee | Nee |
> | datafactories | Ja | Ja |
> | datafactories / diagnosticsettings | Nee | Nee |
> | datafactories / metricdefinitions | Nee | Nee |
> | datafactoryschema | Nee | Nee |
> | factory's | Ja | Ja |
> | fabrieken/integrationruntimes | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/configurefactoryrepo | Nee | Nee |
> | locaties/getfeaturevalue | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftdatalake"></a>Micro soft. DataLake

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Nee | Nee |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |
> | accounts/datalakestoreaccounts | Nee | Nee |
> | accounts/Storage accounts | Nee | Nee |
> | accounts/Storage accounts/containers | Nee | Nee |
> | accounts/Storage accounts/containers/listsastokens | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/mogelijkheid | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/gebruik | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |
> | accounts/eventgridfilters | Nee | Nee |
> | accounts/firewallrules | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/mogelijkheid | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | locaties/deletevirtualnetworkorsubnets | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/gebruik | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftdatamigration"></a>Micro soft. DataMigration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | locaties | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/operationstatuses | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | services | Nee | Nee |
> | Services/projecten | Nee | Nee |
> | sleuf | Nee | Nee |

## <a name="microsoftdataprotection"></a>Micro soft. DataProtection

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | backupvaults | Nee | Nee |
> | locaties | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftdatashare"></a>Micro soft. DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |
> | accounts/shares | Nee | Nee |
> | accounts/shares/gegevens sets | Nee | Nee |
> | accounts/shares/uitnodigingen | Nee | Nee |
> | accounts/shares/providersharesubscriptions | Nee | Nee |
> | accounts/shares/synchronizationsettings | Nee | Nee |
> | accounts/sharesubscriptions | Nee | Nee |
> | accounts/sharesubscriptions/consumersourcedatasets | Nee | Nee |
> | accounts/sharesubscriptions/datasetmappings | Nee | Nee |
> | accounts/sharesubscriptions/triggers | Nee | Nee |
> | listinvitations | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/consumerinvitations | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/rejectinvitation | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftdbformariadb"></a>Micro soft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/azureasyncoperation | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/performancetiers | Nee | Nee |
> | locaties/privateendpointconnectionazureasyncoperation | Nee | Nee |
> | locaties/privateendpointconnectionoperationresults | Nee | Nee |
> | locaties/privateendpointconnectionproxyazureasyncoperation | Nee | Nee |
> | locaties/privateendpointconnectionproxyoperationresults | Nee | Nee |
> | locaties/recommendedactionsessionsazureasyncoperation | Nee | Nee |
> | locaties/recommendedactionsessionsoperationresults | Nee | Nee |
> | locaties/securityalertpoliciesazureasyncoperation | Nee | Nee |
> | locaties/securityalertpoliciesoperationresults | Nee | Nee |
> | locaties/serverkeyazureasyncoperation | Nee | Nee |
> | locaties/serverkeyoperationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | Server | Ja | Ja |
> | servers/adviseurs | Nee | Nee |
> | servers/privateendpointconnectionproxies | Nee | Nee |
> | servers/privateendpointconnections | Nee | Nee |
> | servers/privatelinkresources | Nee | Nee |
> | servers/querytexts | Nee | Nee |
> | servers/recoverableservers | Nee | Nee |
> | servers/topquerystatistics | Nee | Nee |
> | servers/virtualnetworkrules | Nee | Nee |
> | servers/waitstatistics | Nee | Nee |

## <a name="microsoftdbformysql"></a>Micro soft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/administratorazureasyncoperation | Nee | Nee |
> | locaties/administratoroperationresults | Nee | Nee |
> | locaties/azureasyncoperation | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/performancetiers | Nee | Nee |
> | locaties/privateendpointconnectionazureasyncoperation | Nee | Nee |
> | locaties/privateendpointconnectionoperationresults | Nee | Nee |
> | locaties/privateendpointconnectionproxyazureasyncoperation | Nee | Nee |
> | locaties/privateendpointconnectionproxyoperationresults | Nee | Nee |
> | locaties/recommendedactionsessionsazureasyncoperation | Nee | Nee |
> | locaties/recommendedactionsessionsoperationresults | Nee | Nee |
> | locaties/securityalertpoliciesazureasyncoperation | Nee | Nee |
> | locaties/securityalertpoliciesoperationresults | Nee | Nee |
> | locaties/serverkeyazureasyncoperation | Nee | Nee |
> | locaties/serverkeyoperationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | Server | Ja | Ja |
> | servers/adviseurs | Nee | Nee |
> | servers/sleutels | Nee | Nee |
> | servers/privateendpointconnectionproxies | Nee | Nee |
> | servers/privateendpointconnections | Nee | Nee |
> | servers/privatelinkresources | Nee | Nee |
> | servers/querytexts | Nee | Nee |
> | servers/recoverableservers | Nee | Nee |
> | servers/topquerystatistics | Nee | Nee |
> | servers/virtualnetworkrules | Nee | Nee |
> | servers/waitstatistics | Nee | Nee |

## <a name="microsoftdbforpostgresql"></a>Micro soft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/administratorazureasyncoperation | Nee | Nee |
> | locaties/administratoroperationresults | Nee | Nee |
> | locaties/azureasyncoperation | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/performancetiers | Nee | Nee |
> | locaties/privateendpointconnectionazureasyncoperation | Nee | Nee |
> | locaties/privateendpointconnectionoperationresults | Nee | Nee |
> | locaties/privateendpointconnectionproxyazureasyncoperation | Nee | Nee |
> | locaties/privateendpointconnectionproxyoperationresults | Nee | Nee |
> | locaties/recommendedactionsessionsazureasyncoperation | Nee | Nee |
> | locaties/recommendedactionsessionsoperationresults | Nee | Nee |
> | locaties/securityalertpoliciesazureasyncoperation | Nee | Nee |
> | locaties/securityalertpoliciesoperationresults | Nee | Nee |
> | locaties/serverkeyazureasyncoperation | Nee | Nee |
> | locaties/serverkeyoperationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | servergroups | Nee | Nee |
> | Server | Ja | Ja |
> | servers/adviseurs | Nee | Nee |
> | servers/sleutels | Nee | Nee |
> | servers/privateendpointconnectionproxies | Nee | Nee |
> | servers/privateendpointconnections | Nee | Nee |
> | servers/privatelinkresources | Nee | Nee |
> | servers/querytexts | Nee | Nee |
> | servers/recoverableservers | Nee | Nee |
> | servers/topquerystatistics | Nee | Nee |
> | servers/virtualnetworkrules | Nee | Nee |
> | servers/waitstatistics | Nee | Nee |
> | serversv2 | Ja | Ja |
> | singleservers | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Micro soft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | artifactsources | Ja | Ja |
> | operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | implementaties | Ja | Ja |
> | servicetopologies | Ja | Ja |
> | servicetopologies/Services | Ja | Ja |
> | servicetopologies/Services/serviceunits | Ja | Ja |
> | stappen | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Micro soft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Ja | Ja |
> | applicationgroups/toepassingen | Nee | Nee |
> | applicationgroups/Bureau bladen | Nee | Nee |
> | applicationgroups / startmenuitems | Nee | Nee |
> | hostpools | Ja | Ja |
> | hostpools / sessionhosts | Nee | Nee |
> | hostpools / sessionhosts / usersessions | Nee | Nee |
> | hostpools / usersessions | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | workspaces | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | checkprovisioningservicenameavailability | Nee | Nee |
> | elasticpools | Nee | Nee |
> | elasticpools / iothubtenants | Nee | Nee |
> | iothubs | Ja | Ja |
> | iothubs/eventgridfilters | Nee | Nee |
> | iothubs/securitysettings | Nee | Nee |
> | operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | provisioningservices | Ja | Ja |
> | gebruik | Nee | Nee |

## <a name="microsoftdevops"></a>Micro soft. DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | pijp lijnen | Ja | Ja |

## <a name="microsoftdevspaces"></a>Micro soft. DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | fungeren | Ja | Ja |
> | controllers/listconnectiondetails | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/checkcontainerhostmapping | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | labcenters | Nee | Nee |
> | Labs | Yes | No |
> | Labs/omgevingen | Ja | Ja |
> | Labs-servicerunners | Ja | Ja |
> | Labs-informatie | Yes | No |
> | locaties | Nee | Nee |
> | locaties/bewerkingen | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | schema's | Ja | Ja |

## <a name="microsoftdigitaltwins"></a>Micro soft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Nee | Nee |
> | digitaltwinsinstances / operationresults | Nee | Nee |
> | locaties | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | Nee | Nee |
> | databaseaccounts | Ja | Ja |
> | locaties | Nee | Nee |
> | locaties/deletevirtualnetworkorsubnets | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/operationsstatus | Nee | Nee |
> | operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftdomainregistration"></a>Micro soft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | Nee | Nee |
> | domeinen | Ja | Ja |
> | domeinen/domainownershipidentifiers | Nee | Nee |
> | generatessorequest | Nee | Nee |
> | listdomainrecommendations | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | topleveldomains | Nee | Nee |
> | validatedomainregistrationinformation | Nee | Nee |

## <a name="microsoftenterpriseknowledgegraph"></a>Micro soft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | locaties | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Micro soft. EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | domeinen | Ja | Ja |
> | domeinen/onderwerpen | Nee | Nee |
> | eventsubscriptions | Nee, kan niet onafhankelijk worden verplaatst, maar automatisch verplaatst met een geabonneerde resource. | Nee, kan niet onafhankelijk worden verplaatst, maar automatisch verplaatst met een geabonneerde resource. |
> | extensiontopics | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/eventsubscriptions | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/operationsstatus | Nee | Nee |
> | locaties/topictypes | Nee | Nee |
> | operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | operationsstatus | Nee | Nee |
> | partnernamespaces | Ja | Ja |
> | partnernamespaces/eventchannels | Nee | Nee |
> | partnerregistrations | Nee | Nee |
> | partnertopics | Ja | Ja |
> | partnertopics / eventsubscriptions | Nee | Nee |
> | systemtopics | Ja | Ja |
> | systemtopics / eventsubscriptions | Nee | Nee |
> | onderwerp | Ja | Ja |
> | topictypes | Nee | Nee |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | Nee | Nee |
> | checknameavailability | Nee | Nee |
> | checknamespaceavailability | Nee | Nee |
> | clusters | Ja | Ja |
> | locaties | Nee | Nee |
> | locaties/deletevirtualnetworkorsubnets | Nee | Nee |
> | naam ruimten | Ja | Ja |
> | naam ruimten/authorizationrules | Nee | No |
> | naam ruimten/disasterrecoveryconfigs | Nee | No |
> | naam ruimten/disasterrecoveryconfigs/checknameavailability | Nee | No |
> | naam ruimten/Event hubs | Nee | No |
> | naam ruimten/Event hubs/authorizationrules | Nee | No |
> | naam ruimten/Event hubs/consumergroups | Nee | No |
> | naam ruimten/networkrulesets | Nee | No |
> | bewerkingen | Nee | No |
> | sku | Nee | No |

## <a name="microsoftexperimentation"></a>Micro soft. experimenten

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Nee | No |
> | locaties | Nee | No |
> | locaties/bewerkingen | Nee | No |

## <a name="microsoftfalcon"></a>Micro soft. Falcon

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | naam ruimten | Ja | Ja |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | featureproviders | Nee | No |
> | features | Nee | No |
> | bewerkingen | Nee | No |
> | providers | Nee | No |
> | subscriptionfeatureregistrations | Nee | No |

## <a name="microsoftgenomics"></a>Micro soft. Genomics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | No |

## <a name="microsoftguestconfiguration"></a>Micro soft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | Nee | No |
> | automanagedvmconfigurationprofiles | Nee | No |
> | guestconfigurationassignments | Nee | No |
> | bewerkingen | Nee | No |
> | software | Nee | No |
> | softwareupdateprofile | Nee | No |
> | softwareupdates | Nee | No |

## <a name="microsofthanaonazure"></a>Micro soft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hanainstances | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/bewerkingen | Nee | Nee |
> | locaties/operationsstatus | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | sapmonitors | Ja | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Micro soft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | Nee | Nee |
> | locaties | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Ja | Ja |
> | clusters/toepassingen | Nee | Nee |
> | clusters/operationresults | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/azureasyncoperations | Nee | Nee |
> | locaties/billingspecs | Nee | Nee |
> | locaties/mogelijkheden | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/gebruik | Nee | Nee |
> | locaties/validatecreaterequest | Nee | Nee |
> | bewerkingen | Nee | Nee |

> [!IMPORTANT]
> U kunt HDInsight-clusters verplaatsen naar een nieuw abonnement of een nieuwe resource groep. U kunt echter niet overstappen op de netwerk resources die zijn gekoppeld aan het HDInsight-cluster (zoals het virtuele netwerk, de NIC of de load balancer). Daarnaast kunt u niet verplaatsen naar een nieuwe resource groep een NIC die is gekoppeld aan een virtuele machine voor het cluster.
>
> Wanneer u een HDInsight-cluster naar een nieuw abonnement verplaatst, moet u eerst andere resources (zoals het opslag account) verplaatsen. Verplaats vervolgens het HDInsight-cluster zelf.

## <a name="microsofthealthcareapis"></a>Micro soft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | services | Ja | Ja |
> | Services/privateendpointconnections | Nee | Nee |
> | Services/privatelinkresources | Nee | Nee |

## <a name="microsofthybridcompute"></a>Micro soft. HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | locaties | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/operationstatus | Nee | Nee |
> | apparaten | Ja | Ja |
> | computers/uitbrei dingen | Ja | Ja |
> | bewerkingen | Nee | Nee |

## <a name="microsofthybriddata"></a>Micro soft. HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | datamanagers | Ja | Ja |
> | bewerkingen | Nee | Nee |

## <a name="microsofthybridnetwork"></a>Micro soft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | devices | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/operationstatuses | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | vnfs | Nee | Nee |

## <a name="microsofthydra"></a>Micro soft. Hydra

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | materialen | Nee | Nee |
> | locaties | Nee | Nee |
> | networkscopes | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | functies | Ja | Ja |
> | locaties | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftinsights"></a>micro soft. Insights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | actiongroups | Ja | Ja |
> | activitylogalerts | Nee | Nee |
> | alertrules | Ja | Ja |
> | autoscalesettings | Ja | Ja |
> | basislijn | Nee | Nee |
> | calculatebaseline | Nee | Nee |
> | materialen | Ja | Ja |
> | onderdelen/gebeurtenissen | Nee | Nee |
> | onderdelen/linkedstorageaccounts | Nee | Nee |
> | onderdelen/meta gegevens | Nee | Nee |
> | onderdelen/metrische gegevens | Nee | Nee |
> | onderdelen/pricingplans | Nee | Nee |
> | onderdelen/query | Nee | Nee |
> | datacollectionrules | Nee | Nee |
> | diagnosticsettings | Nee | Nee |
> | diagnosticsettingscategories | Nee | Nee |
> | eventcategories | Nee | Nee |
> | eventtypes | Nee | Nee |
> | extendeddiagnosticsettings | Nee | Nee |
> | guestdiagnosticsettings | Nee | Nee |
> | listmigrationdate | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | logdefinitions | Nee | Nee |
> | logprofiles | Nee | Nee |
> | logboeken | Nee | Nee |
> | metricalerts | Nee | Nee |
> | metricbaselines | Nee | Nee |
> | metricbatch | Nee | Nee |
> | metricdefinitions | Nee | Nee |
> | metricnamespaces | Nee | Nee |
> | metrics | Nee | Nee |
> | migratealertrules | Nee | Nee |
> | migratetonewpricingmodel | Nee | Nee |
> | myworkbooks | Nee | Nee |
> | notificationgroups | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | privatelinkscopeoperationstatuses | Nee | Nee |
> | privatelinkscopes | Nee | Nee |
> | privatelinkscopes / privateendpointconnectionproxies | Nee | Nee |
> | privatelinkscopes / privateendpointconnections | Nee | Nee |
> | privatelinkscopes / scopedresources | Nee | Nee |
> | rollbacktolegacypricingmodel | Nee | Nee |
> | scheduledqueryrules | Ja | Ja |
> | topologie | Nee | Nee |
> | transacties | Nee | Nee |
> | vminsightsonboardingstatuses | Nee | Nee |
> | webtests | Ja | Ja |
> | webtesten/gettestresultfile | Nee | Nee |
> | werkmappen | Ja | Ja |
> | workbooktemplates | Ja | Ja |

> [!IMPORTANT]
> Zorg ervoor dat het verplaatsen naar een nieuw abonnement geen [abonnements quota's](azure-subscription-service-limits.md#azure-monitor-limits)overschrijdt.

## <a name="microsoftiotcentral"></a>Micro soft. IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | apptemplates | Nee | Nee |
> | checknameavailability | Nee | Nee |
> | checksubdomainavailability | Nee | Nee |
> | iotapps | Ja | Ja |
> | bewerkingen | Nee | Nee |

## <a name="microsoftiotspaces"></a>Micro soft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ja | Ja |
> | Graph | Ja | Ja |
> | bewerkingen | Nee | Nee |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | deletedvaults | Nee | Nee |
> | hsmpools | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/deletedvaults | Nee | Nee |
> | locaties/deletevirtualnetworkorsubnets | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | managedhsms | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | kluizen | Ja | Ja |
> | kluizen/accesspolicies | Nee | Nee |
> | kluizen/eventgridfilters | Nee | Nee |
> | kluizen/geheimen | Nee | Nee |

> [!IMPORTANT]
> Sleutel kluizen die worden gebruikt voor schijf versleuteling, kunnen niet worden verplaatst naar een resource groep in hetzelfde abonnement of tussen abonnementen.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Ja | Ja |
> | locaties | Nee | Nee |
> | locaties/operationstatuses | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | registeredsubscriptions | Nee | Nee |

## <a name="microsoftkubernetesconfiguration"></a>Micro soft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | Nee | Nee |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Ja | Ja |
> | clusters/attacheddatabaseconfigurations | Nee | Nee |
> | clusters/data bases | Nee | Nee |
> | clusters/data bases/dataConnections | Nee | Nee |
> | clusters/data bases/eventhubconnections | Nee | Nee |
> | clusters/data bases/principalassignments | Nee | Nee |
> | clusters/principalassignments | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftlabservices"></a>Micro soft. LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | labaccounts | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/bewerkingen | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | gebruikers | Nee | Nee |

## <a name="microsoftlocationbasedservices"></a>Micro soft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftlocationservices"></a>Micro soft. bestand locationservices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Nee | Nee |
> | integrationaccounts | Ja | Ja |
> | integrationserviceenvironments | Yes | No |
> | integrationserviceenvironments/beheerdeapi's | Yes | No |
> | isolatedenvironments | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/werk stromen | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | stroom | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/bewerkingen | Nee | Nee |
> | locaties/operationsstatus | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | webservices | Yes | No |
> | workspaces | Ja | Ja |

## <a name="microsoftmachinelearningcompute"></a>Micro soft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Nee | Nee |

## <a name="microsoftmachinelearningexperimentation"></a>Micro soft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |
> | accounts/werk ruimten | Nee | Nee |
> | accounts/werk ruimten/projecten | Nee | Nee |
> | teamaccounts | Nee | Nee |
> | teamaccounts/werk ruimten | Nee | Nee |
> | teamaccounts/werk ruimten/projecten | Nee | Nee |

## <a name="microsoftmachinelearningmodelmanagement"></a>Micro soft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | locaties | Nee | Nee |
> | locaties/computeoperationsstatus | Nee | Nee |
> | locaties/quota's | Nee | Nee |
> | locaties/updatequotas | Nee | Nee |
> | locaties/gebruik | Nee | Nee |
> | locaties/toegestane VM | Nee | Nee |
> | locaties/workspaceoperationsstatus | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | workspaces | Nee | Nee |
> | werk ruimten/reken bewerkingen | Nee | Nee |
> | werk ruimten/eventgridfilters | Nee | Nee |

## <a name="microsoftmaintenance"></a>Micro soft. onderhoud

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | applyupdates | Nee | Nee |
> | configurationassignments | Nee | Nee |
> | maintenanceconfigurations | Ja | Ja |
> | updates | Nee | Nee |

## <a name="microsoftmanagedidentity"></a>Micro soft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | identiteit | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | userassignedidentities | Nee | Nee |

## <a name="microsoftmanagednetwork"></a>Micro soft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | managednetworks | Nee | Nee |
> | managednetworks / managednetworkgroups | Nee | Nee |
> | managednetworks / managednetworkpeeringpolicies | Nee | Nee |
> | melding | Nee | Nee |

## <a name="microsoftmanagedservices"></a>Micro soft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | operationstatuses | Nee | Nee |
> | registrationassignments | Nee | Nee |
> | registrationdefinitions | Nee | Nee |

## <a name="microsoftmanagement"></a>Micro soft. Management

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | getentities | Nee | Nee |
> | managementgroups | Nee | Nee |
> | managementgroups/instellingen | Nee | Nee |
> | operationresults | Nee | Nee |
> | operationresults / asyncoperation | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | resources | Nee | Nee |
> | starttenantbackfill | Nee | Nee |
> | tenantbackfillstatus | Nee | Nee |

## <a name="microsoftmaps"></a>Micro soft. Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |
> | accounts/eventgridfilters | Nee | Nee |
> | accounts/privateatlases | Ja | Ja |
> | bewerkingen | Nee | Nee |

## <a name="microsoftmarketplace"></a>Micro soft. Marketplace

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | Nee | Nee |
> | over | Nee | Nee |
> | offertypes | Nee | Nee |
> | offertypes/uitgevers | Nee | Nee |
> | offertypes/uitgevers/aanbiedingen | Nee | Nee |
> | offertypes/uitgevers/aanbiedingen/abonnementen | Nee | Nee |
> | offertypes/uitgevers/aanbiedingen/plannen/overeenkomsten | Nee | Nee |
> | offertypes/uitgevers/aanbiedingen/plannen/configuraties | Nee | Nee |
> | offertypes/uitgevers/aanbiedingen/plannen/configuraties/importimage | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | privategalleryitems | Nee | Nee |
> | privatestoreclient | Nee | Nee |
> | privatestores | Nee | Nee |
> | privatestores/aanbiedingen | Nee | Nee |
> | producten | Nee | Nee |
> | uitgevers | Nee | Nee |
> | uitgevers/aanbiedingen | Nee | Nee |
> | uitgevers/aanbiedingen/wijzigingen | Nee | Nee |
> | registreren | Nee | Nee |

## <a name="microsoftmarketplaceapps"></a>Micro soft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nee | Nee |
> | listcommunicationpreference | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | updatecommunicationpreference | Nee | Nee |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | gesloten | Nee | Nee |
> | offertypes | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | Media Services | Ja | Ja |
> | Media Services/accountfilters | Nee | Nee |
> | Media Services/assets | Nee | Nee |
> | Media Services/assets/assetfilters | Nee | Nee |
> | Media Services/contentkeypolicies | Nee | Nee |
> | Media Services/eventgridfilters | Nee | Nee |
> | Media Services/liveeventoperations | Nee | Nee |
> | Media Services/liveevents | Ja | Ja |
> | Media Services/liveevents/liveoutputs | Nee | Nee |
> | Media Services/liveoutputoperations | Nee | Nee |
> | Media Services/streamingendpointoperations | Nee | Nee |
> | Media Services/streamingendpoints | Ja | Ja |
> | Media Services/streaminglocators | Nee | Nee |
> | Media Services/streamingpolicies | Nee | Nee |
> | Media Services/trans formaties | Nee | Nee |
> | Media Services/trans formaties/taken | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftmicroservices4spring"></a>Micro soft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | appclusters | Nee | Nee |

## <a name="microsoftmigrate"></a>Micro soft. migrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/assessmentoptions | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | migrateprojects | Nee | Nee |
> | movecollections | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | projecten | Nee | Nee |

## <a name="microsoftmixedreality"></a>Micro soft. MixedReality

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | objectunderstandingaccounts | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | remoterenderingaccounts | Ja | Ja |
> | spatialanchorsaccounts | Ja | Ja |

## <a name="microsoftnetapp"></a>Micro soft. NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Nee | Nee |
> | netappaccounts / backuppolicies | Nee | Nee |
> | netappaccounts / capacitypools | Nee | Nee |
> | netappaccounts/capacitypools/volumes | Nee | Nee |
> | netappaccounts/capacitypools/volumes/mounttargets | Nee | Nee |
> | netappaccounts/capacitypools/volumes/moment opnamen | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | Nee | Nee |
> | applicationgatewayavailableresponseheaders | Nee | Nee |
> | applicationgatewayavailableservervariables | Nee | Nee |
> | applicationgatewayavailablessloptions | Nee | Nee |
> | applicationgatewayavailablewafrulesets | Nee | Nee |
> | applicationgateways | Nee | Nee |
> | applicationgatewaywebapplicationfirewallpolicies | Nee | Nee |
> | applicationsecuritygroups | Ja | Ja |
> | azurefirewallfqdntags | Nee | Nee |
> | azurefirewalls | Nee | Nee |
> | bastionhosts | Nee | Nee |
> | bgpservicecommunities | Nee | Nee |
> | checkfrontdoornameavailability | Nee | Nee |
> | checktrafficmanagernameavailability | Nee | Nee |
> | inbel | Ja | Ja |
> | ddoscustompolicies | Ja | Ja |
> | ddosprotectionplans | Nee | Nee |
> | dnsoperationresults | Nee | Nee |
> | dnsoperationstatuses | Nee | Nee |
> | dnszones | Ja | Ja |
> | dnszones/a | Nee | Nee |
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
> | dnszones/txt | Nee | Nee |
> | expressroutecircuits | Nee | Nee |
> | expressroutegateways | Nee | Nee |
> | expressrouteserviceproviders | Nee | Nee |
> | firewallpolicies | Ja | Ja |
> | frontdooroperationresults | Nee | Nee |
> | frontdoors | Nee | Nee |
> | frontdoors / frontendendpoints | Nee | Nee |
> | frontdoorwebapplicationfirewallmanagedrulesets | Nee | Nee |
> | frontdoorwebapplicationfirewallpolicies | Nee | Nee |
> | getdnsresourcereference | Nee | Nee |
> | internalnotify | Nee | Nee |
> | ipallocations | Ja | Ja |
> | ipgroups | Ja | Ja |
> | loadbalancers | Ja, basis-SKU<br>Geen standaard-SKU | Ja, basis-SKU<br>Geen standaard-SKU |
> | localnetworkgateways | Ja | Ja |
> | locaties | Nee | Nee |
> | locaties/autoapprovedprivatelinkservices | Nee | Nee |
> | locaties/availabledelegations | Nee | Nee |
> | locaties/availableprivateendpointtypes | Nee | Nee |
> | locaties/availableservicealiases | Nee | Nee |
> | locaties/baremetaltenants | Nee | Nee |
> | locaties/batchnotifyprivateendpointsforresourcemove | Nee | Nee |
> | locaties/batchvalidateprivateendpointsforresourcemove | Nee | Nee |
> | locaties/checkacceleratednetworkingsupport | Nee | Nee |
> | locaties/checkdnsnameavailability | Nee | Nee |
> | locaties/checkprivatelinkservicevisibility | Nee | Nee |
> | locaties/commitinternalazurenetworkmanagerconfiguration | Nee | Nee |
> | locaties/effectiveresourceownership | Nee | Nee |
> | locaties/nfvoperationresults | Nee | Nee |
> | locaties/nfvoperations | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/bewerkingen | Nee | Nee |
> | locaties/servicetags | Nee | Nee |
> | locaties/setresourceownership | Nee | Nee |
> | locaties/supportedvirtualmachinesizes | Nee | Nee |
> | locaties/gebruik | Nee | Nee |
> | locaties/validateresourceownership | Nee | Nee |
> | locaties/virtualnetworkavailableendpointservices | Nee | Nee |
> | natgateways | Ja | Ja |
> | networkexperimentprofiles | Nee | Nee |
> | networkintentpolicies | Ja | Ja |
> | networkinterfaces | Ja | Ja |
> | networkprofiles | Nee | Nee |
> | networksecuritygroups | Ja | Ja |
> | networkwatchers | Yes | No |
> | networkwatchers / connectionmonitors | Yes | No |
> | networkwatchers / flowlogs | Yes | No |
> | networkwatchers / pingmeshes | Yes | No |
> | bewerkingen | Nee | Nee |
> | p2svpngateways | Nee | Nee |
> | privatednsoperationresults | Nee | Nee |
> | privatednsoperationstatuses | Nee | Nee |
> | privatednszones | Ja | Ja |
> | privatednszones/a | Nee | Nee |
> | privatednszones/AAAA | Nee | Nee |
> | privatednszones/alle | Nee | Nee |
> | privatednszones/CNAME | Nee | Nee |
> | privatednszones/MX | Nee | Nee |
> | privatednszones/PTR | Nee | Nee |
> | privatednszones/SOA | Nee | Nee |
> | privatednszones/SRV | Nee | Nee |
> | privatednszones/txt | Nee | Nee |
> | privatednszones / virtualnetworklinks | Ja | Ja |
> | privatednszonesinternal | Nee | Nee |
> | privateendpointredirectmaps | Nee | Nee |
> | privateendpoints | Ja | Ja |
> | privatelinkservices | Nee | Nee |
> | publicipaddresses | Ja, basis-SKU<br>Geen standaard-SKU | Ja, basis-SKU<br>Geen standaard-SKU |
> | publicipprefixes | Ja | Ja |
> | routefilters | Nee | Nee |
> | routetables | Ja | Ja |
> | securitypartnerproviders | Ja | Ja |
> | serviceendpointpolicies | Ja | Ja |
> | trafficmanagergeographichierarchies | Nee | Nee |
> | trafficmanagerprofiles | Ja | Ja |
> | trafficmanagerprofiles/Heatmaps | Nee | Nee |
> | trafficmanagerusermetricskeys | Nee | Nee |
> | virtualhubs | Nee | Nee |
> | virtualnetworkgateways | Ja | Ja |
> | virtualnetworks | Ja | Ja |
> | virtualnetworktaps | Nee | Nee |
> | virtualrouters | Ja | Ja |
> | virtualwans | Nee | Nee |
> | vpngateways (virtueel WAN) | Nee | Nee |
> | vpnserverconfigurations | Nee | Nee |
> | vpnsites (virtueel WAN) | Nee | Nee |

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/networking-move-limitations.md)voor het verplaatsen van netwerken.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | checknamespaceavailability | Nee | Nee |
> | naam ruimten | Ja | Ja |
> | naam ruimten/notification hubs | Ja | Ja |
> | operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftobjectstore"></a>Micro soft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Ja | Ja |

## <a name="microsoftoffazure"></a>Micro soft. OffAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hypervsites | Nee | Nee |
> | importsites | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | serversites | Nee | Nee |
> | vmwaresites | Nee | Nee |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Nee | Nee |
> | deletedworkspaces | Nee | Nee |
> | linktargets | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/operationstatuses | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | storageinsightconfigs | Nee | Nee |
> | workspaces | Ja | Ja |
> | werk ruimten/gegevens bronnen | Nee | Nee |
> | werk ruimten/linkedservices | Nee | Nee |
> | werk ruimten/linkedstorageaccounts | Nee | Nee |
> | werk ruimten/meta gegevens | Nee | Nee |
> | werk ruimten/query | Nee | Nee |
> | werk ruimten/scopedprivatelinkproxies | Nee | Nee |

> [!IMPORTANT]
> Zorg ervoor dat het verplaatsen naar een nieuw abonnement geen [abonnements quota's](azure-subscription-service-limits.md#azure-monitor-limits)overschrijdt.
>
> U kunt geen werk ruimten verplaatsen die een gekoppeld Automation-account hebben. Zorg ervoor dat u alle Automation-accounts ontkoppelt voordat u begint met een Verplaats bewerking.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | managementassociations | Nee | Nee |
> | managementconfigurations | Ja | Ja |
> | bewerkingen | Nee | Nee |
> | oplossingen | Ja | Ja |
> | Weergaven | Ja | Ja |

## <a name="microsoftpeering"></a>Micro soft. peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | Nee | Nee |
> | legacypeerings | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | peerasns | Nee | Nee |
> | peeringlocations | Nee | Nee |
> | Peerings | Nee | Nee |
> | peeringservicecountries | Nee | Nee |
> | peeringservicelocations | Nee | Nee |
> | peeringserviceproviders | Nee | Nee |
> | peeringservices | Nee | Nee |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | policyevents | Nee | Nee |
> | policystates | Nee | Nee |
> | policytrackedresources | Nee | Nee |
> | herstel | Nee | Nee |

## <a name="microsoftportal"></a>Micro soft. Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> |  -consoles | Nee | Nee |
> | dashboards | Ja | Ja |
> | locaties | Nee | Nee |
> | locaties/consoles | Nee | Nee |
> | locaties/usersettings | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | usersettings | Nee | Nee |

## <a name="microsoftpowerbi"></a>Micro soft. PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | locaties | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | workspacecollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Micro soft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | beschikt | Ja | Ja |
> | locaties | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/operationstatuses | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftpowerplatform"></a>Micro soft. PowerPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | bewerkingen | Nee | Nee |

## <a name="microsoftprojectbabylon"></a>Micro soft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |
> | checknameavailability | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftproviderhub"></a>Micro soft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Nee | Nee |
> | providerregistrations | Nee | Nee |
> | providerregistrations / resourcetyperegistrations | Nee | Nee |
> | implementaties | Nee | Nee |

## <a name="microsoftquantum"></a>Micro soft. Quantum

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | locaties | Nee | Nee |
> | locaties/operationstatuses | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | workspaces | Nee | Nee |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/allocatedstamp | Nee | Nee |
> | locaties/Allo Cate Stamp | Nee | Nee |
> | locaties/backupaadproperties | Nee | Nee |
> | locaties/backupcrossregionrestore | Nee | Nee |
> | locaties/backupcrrjob | Nee | Nee |
> | locaties/backupcrrjobs | Nee | Nee |
> | locaties/backupcrroperationresults | Nee | Nee |
> | locaties/backupcrroperationsstatus | Nee | Nee |
> | locaties/backupprevalidateprotection | Nee | Nee |
> | locaties/backupstatus | Nee | Nee |
> | locaties/backupvalidatefeatures | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | replicationeligibilityresults | Nee | Nee |
> | kluizen | Ja | Ja |

> [!IMPORTANT]
> Zie [Recovery Services richt lijnen voor verplaatsen](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Micro soft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | locaties | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/operationsstatus | Nee | Nee |
> | openshiftclusters | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | naam ruimten | Ja | Ja |
> | naam ruimten/authorizationrules | Nee | Nee |
> | naam ruimten/hybridconnections | Nee | Nee |
> | naam ruimten/hybridconnections/authorizationrules | Nee | Nee |
> | naam ruimten/privateendpointconnections | Nee | Nee |
> | naam ruimten/wcfrelays | Nee | Nee |
> | naam ruimten/wcfrelays/authorizationrules | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftresourcegraph"></a>Micro soft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | bewerkingen | Nee | Nee |
> | aanvragen | Ja | Ja |
> | resourcechangedetails | Nee | Nee |
> | resourcechanges | Nee | Nee |
> | resources | Nee | Nee |
> | resourceshistory | Nee | Nee |
> | subscriptionsstatus | Nee | Nee |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Nee | Nee |
> | childavailabilitystatuses | Nee | Nee |
> | childresources | Nee | Nee |
> | emergingissues | Nee | Nee |
> | events | Nee | Nee |
> | metagegevens | Nee | Nee |
> | meldingen | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | calculatetemplatehash | Nee | Nee |
> | checkpolicycompliance | Nee | Nee |
> | checkresourcename | Nee | Nee |
> | implementaties | Nee | Nee |
> | implementaties/bewerkingen | Nee | Nee |
> | deploymentscripts | Nee | Nee |
> | deploymentscripts/logboeken | Nee | Nee |
> | koppelen | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/deploymentscriptoperationresults | Nee | Nee |
> | notifyresourcejobs | Nee | Nee |
> | operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | providers | Nee | Nee |
> | ResourceGroups | Nee | Nee |
> | resources | Nee | Nee |
> | geabonneerd | Nee | Nee |
> | abonnementen/locaties | Nee | Nee |
> | abonnementen/operationresults | Nee | Nee |
> | abonnementen/providers | Nee | Nee |
> | abonnementen/ResourceGroups | Nee | Nee |
> | abonnementen/ResourceGroups/resources | Nee | Nee |
> | abonnementen/resources | Nee | Nee |
> | abonnementen/TagName | Nee | Nee |
> | abonnementen/TagName/tagvalues | Nee | Nee |
> | tags | Nee | Nee |
> | templatespecs | Nee | Nee |
> | templatespecs/versies | Nee | Nee |
> | tenants | Nee | Nee |

## <a name="microsoftsaas"></a>Micro soft. SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | toepassingen | Ja | No |
> | checkmoderneligibility | Nee | Nee |
> | checknameavailability | Nee | Nee |
> | operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | saasresources | Nee | Nee |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | checkservicenameavailability | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | resourcehealthmetadata | Nee | Nee |
> | searchservices | Ja | Ja |

> [!IMPORTANT]
> U kunt niet meerdere Zoek resources in verschillende regio's in één bewerking verplaatsen. Verplaats deze in plaats daarvan in afzonderlijke bewerkingen.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Nee | Nee |
> | advancedthreatprotectionsettings | Nee | Nee |
> | waarschuwingen | Nee | Nee |
> | allowedconnections | Nee | Nee |
> | applicationwhitelistings | Nee | Nee |
> | assessmentmetadata | Nee | Nee |
> | evaluaties | Nee | Nee |
> | autodismissalertsrules | Nee | Nee |
> | automatisering | Ja | Ja |
> | autoprovisioningsettings | Nee | Nee |
> | complianceresults | Nee | Nee |
> | ingebouwde strengste | Nee | Nee |
> | datacollectionagents | Nee | Nee |
> | devicesecuritygroups | Nee | Nee |
> | discoveredsecuritysolutions | Nee | Nee |
> | externalsecuritysolutions | Nee | Nee |
> | informationprotectionpolicies | Nee | Nee |
> | iotsecuritysolutions | Ja | Ja |
> | iotsecuritysolutions / analyticsmodels | Nee | Nee |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Nee | Nee |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Nee | Nee |
> | jitnetworkaccesspolicies | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/waarschuwingen | Nee | Nee |
> | locaties/allowedconnections | Nee | Nee |
> | locaties/applicationwhitelistings | Nee | Nee |
> | locaties/discoveredsecuritysolutions | Nee | Nee |
> | locaties/externalsecuritysolutions | Nee | Nee |
> | locaties/jitnetworkaccesspolicies | Nee | Nee |
> | locaties/securitysolutions | Nee | Nee |
> | locaties/securitysolutionsreferencedata | Nee | Nee |
> | locaties/taken | Nee | Nee |
> | locaties/topologieën | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | policies | Nee | Nee |
> | prijzen | Nee | Nee |
> | regulatorycompliancestandards | Nee | Nee |
> | regulatorycompliancestandards / regulatorycompliancecontrols | Nee | Nee |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Nee | Nee |
> | securitycontacts | Nee | Nee |
> | securitysolutions | Nee | Nee |
> | securitysolutionsreferencedata | Nee | Nee |
> | securitystatuses | Nee | Nee |
> | securitystatusessummaries | Nee | Nee |
> | servervulnerabilityassessments | Nee | Nee |
> | instellingen | Nee | Nee |
> | subevaluaties | Nee | Nee |
> | taken | Nee | Nee |
> | topologieën | Nee | Nee |
> | workspacesettings | Nee | Nee |

## <a name="microsoftsecurityinsights"></a>Micro soft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | aggregaties | Nee | Nee |
> | alertrules | Nee | Nee |
> | alertruletemplates | Nee | Nee |
> | automationrules | Nee | Nee |
> | bladwijzers | Nee | Nee |
> | meldingen | Nee | Nee |
> | dataconnectors | Nee | Nee |
> | dataconnectorscheckrequirements | Nee | Nee |
> | Rijg | Nee | Nee |
> | entityqueries | Nee | Nee |
> | gevallen | Nee | Nee |
> | officeconsents | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | instellingen | Nee | Nee |
> | threatintelligence | Nee | Nee |

## <a name="microsoftserialconsole"></a>Micro soft. SerialConsole

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | consoleservices | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/consoleservices | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | gateways | Nee | Nee |
> | punt | Nee | Nee |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | checknamespaceavailability | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/deletevirtualnetworkorsubnets | Nee | Nee |
> | naam ruimten | Ja | Ja |
> | naam ruimten/authorizationrules | Nee | Nee |
> | naam ruimten/disasterrecoveryconfigs | Nee | Nee |
> | naam ruimten/disasterrecoveryconfigs/checknameavailability | Nee | Nee |
> | naam ruimten/eventgridfilters | Nee | Nee |
> | naam ruimten/networkrulesets | Nee | Nee |
> | naam ruimten/wacht rijen | Nee | Nee |
> | naam ruimten/wacht rijen/authorizationrules | Nee | Nee |
> | naam ruimten/onderwerpen | Nee | Nee |
> | naam ruimten/onderwerpen/authorizationrules | Nee | Nee |
> | naam ruimten/onderwerpen/abonnementen | Nee | Nee |
> | naam ruimten/onderwerpen/abonnementen/regels | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | premiummessagingregions | Nee | Nee |
> | sku | Nee | Nee |

## <a name="microsoftservicefabric"></a>Micro soft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | toepassingen | Nee | Nee |
> | clusters | Ja | Ja |
> | clusters/toepassingen | Nee | Nee |
> | containergroups | Nee | Nee |
> | containergroupsets | Nee | Nee |
> | edgeclusters | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/clusterversions | Nee | Nee |
> | locaties/omgevingen | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/bewerkingen | Nee | Nee |
> | managedclusters | Nee | Nee |
> | netwerken | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | secretstores | Nee | Nee |
> | volumes | Nee | Nee |

## <a name="microsoftservicefabricmesh"></a>Micro soft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | toepassingen | Ja | Ja |
> | containergroups | Nee | Nee |
> | gateways | Ja | Ja |
> | locaties | Nee | Nee |
> | locaties/applicationoperations | Nee | Nee |
> | locaties/gatewayoperations | Nee | Nee |
> | locaties/networkoperations | Nee | Nee |
> | locaties/secretoperations | Nee | Nee |
> | locaties/volumeoperations | Nee | Nee |
> | netwerken | Ja | Ja |
> | bewerkingen | Nee | Nee |
> | geheimen | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservices"></a>Micro soft. Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | implementaties | Nee | Nee |

## <a name="microsoftsignalrservice"></a>Micro soft. SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | locaties | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/operationstatuses | Nee | Nee |
> | locaties/gebruik | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | signalr | Ja | Ja |
> | signaal sterkte/eventgridfilters | Nee | Nee |

## <a name="microsoftsoftwareplan"></a>Micro soft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftsolutions"></a>Micro soft. Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Nee | Nee |
> | toepassingen | Nee | Nee |
> | jitrequests | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/operationstatuses | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | instancepools | Nee | Nee |
> | locaties | Ja | Ja |
> | locaties/administratorazureasyncoperation | Nee | Nee |
> | locaties/administratoroperationresults | Nee | Nee |
> | locaties/auditingsettingsazureasyncoperation | Nee | Nee |
> | locaties/auditingsettingsoperationresults | Nee | Nee |
> | locaties/mogelijkheden | Nee | Nee |
> | locaties/databaseazureasyncoperation | Nee | Nee |
> | locaties/databaseoperationresults | Nee | Nee |
> | locaties/databaserestoreazureasyncoperation | Nee | Nee |
> | locaties/deletevirtualnetworkorsubnets | Nee | Nee |
> | locaties/deletevirtualnetworkorsubnetsazureasyncoperation | Nee | Nee |
> | locaties/deletevirtualnetworkorsubnetsoperationresults | Nee | Nee |
> | locaties/dnsaliasasyncoperation | Nee | Nee |
> | locaties/dnsaliasoperationresults | Nee | Nee |
> | locaties/elasticpoolazureasyncoperation | Nee | Nee |
> | locaties/elasticpooloperationresults | Nee | Nee |
> | locaties/encryptionprotectorazureasyncoperation | Nee | Nee |
> | locaties/encryptionprotectoroperationresults | Nee | Nee |
> | locaties/extendedauditingsettingsazureasyncoperation | Nee | Nee |
> | locaties/extendedauditingsettingsoperationresults | Nee | Nee |
> | locaties/failovergroupazureasyncoperation | Nee | Nee |
> | locaties/failovergroupoperationresults | Nee | Nee |
> | locaties/firewallrulesazureasyncoperation | Nee | Nee |
> | locaties/firewallrulesoperationresults | Nee | Nee |
> | locaties/instancefailovergroupazureasyncoperation | Nee | Nee |
> | locaties/instancefailovergroupoperationresults | Nee | Nee |
> | locaties/instancefailovergroups | Nee | Nee |
> | locaties/instancepoolazureasyncoperation | Nee | Nee |
> | locaties/instancepooloperationresults | Nee | Nee |
> | locaties/jobagentazureasyncoperation | Nee | Nee |
> | locaties/jobagentoperationresults | Nee | Nee |
> | locaties/longtermretentionbackupazureasyncoperation | Nee | Nee |
> | locaties/longtermretentionbackupoperationresults | Nee | Nee |
> | locaties/longtermretentionbackups | Nee | Nee |
> | locaties/longtermretentionmanagedinstancebackupazureasyncoperation | Nee | Nee |
> | locaties/longtermretentionmanagedinstancebackupoperationresults | Nee | Nee |
> | locaties/longtermretentionmanagedinstancebackups | Nee | Nee |
> | locaties/longtermretentionmanagedinstances | Nee | Nee |
> | locaties/longtermretentionpolicyazureasyncoperation | Nee | Nee |
> | locaties/longtermretentionpolicyoperationresults | Nee | Nee |
> | locaties/longtermretentionservers | Nee | Nee |
> | locaties/manageddatabaseazureasyncoperation | Nee | Nee |
> | locaties/manageddatabasecompleterestoreazureasyncoperation | Nee | Nee |
> | locaties/manageddatabasecompleterestoreoperationresults | Nee | Nee |
> | locaties/manageddatabaseoperationresults | Nee | Nee |
> | locaties/manageddatabaserestoreazureasyncoperation | Nee | Nee |
> | locaties/manageddatabaserestoreoperationresults | Nee | Nee |
> | locaties/managedinstanceazureasyncoperation | Nee | Nee |
> | locaties/managedinstanceencryptionprotectorazureasyncoperation | Nee | Nee |
> | locaties/managedinstanceencryptionprotectoroperationresults | Nee | Nee |
> | locaties/managedinstancekeyazureasyncoperation | Nee | Nee |
> | locaties/managedinstancekeyoperationresults | Nee | Nee |
> | locaties/managedinstancelongtermretentionpolicyazureasyncoperation | Nee | Nee |
> | locaties/managedinstancelongtermretentionpolicyoperationresults | Nee | Nee |
> | locaties/managedinstanceoperationresults | Nee | Nee |
> | locaties/managedinstancetdecertazureasyncoperation | Nee | Nee |
> | locaties/managedinstancetdecertoperationresults | Nee | Nee |
> | locaties/managedserversecurityalertpoliciesazureasyncoperation | Nee | Nee |
> | locaties/managedserversecurityalertpoliciesoperationresults | Nee | Nee |
> | locaties/managedshorttermretentionpolicyazureasyncoperation | Nee | Nee |
> | locaties/managedshorttermretentionpolicyoperationresults | Nee | Nee |
> | locaties/notifyazureasyncoperation | Nee | Nee |
> | locaties/privateendpointconnectionazureasyncoperation | Nee | Nee |
> | locaties/privateendpointconnectionoperationresults | Nee | Nee |
> | locaties/privateendpointconnectionproxyazureasyncoperation | Nee | Nee |
> | locaties/privateendpointconnectionproxyoperationresults | Nee | Nee |
> | locaties/securityalertpoliciesazureasyncoperation | Nee | Nee |
> | locaties/securityalertpoliciesoperationresults | Nee | Nee |
> | locaties/serveradministratorazureasyncoperation | Nee | Nee |
> | locaties/serveradministratoroperationresults | Nee | Nee |
> | locaties/serverazureasyncoperation | Nee | Nee |
> | locaties/serverkeyazureasyncoperation | Nee | Nee |
> | locaties/serverkeyoperationresults | Nee | Nee |
> | locaties/serveroperationresults | Nee | Nee |
> | locaties/shorttermretentionpolicyazureasyncoperation | Nee | Nee |
> | locaties/shorttermretentionpolicyoperationresults | Nee | Nee |
> | locaties/syncagentoperationresults | Nee | Nee |
> | locaties/syncdatabaseids | Nee | Nee |
> | locaties/syncgroupoperationresults | Nee | Nee |
> | locaties/syncmemberoperationresults | Nee | Nee |
> | locaties/tdecertazureasyncoperation | Nee | Nee |
> | locaties/tdecertoperationresults | Nee | Nee |
> | locaties/gebruik | Nee | Nee |
> | locaties/virtualclusterazureasyncoperation | Nee | Nee |
> | locaties/virtualclusteroperationresults | Nee | Nee |
> | locaties/virtualnetworkrulesazureasyncoperation | Nee | Nee |
> | locaties/virtualnetworkrulesoperationresults | Nee | Nee |
> | locaties/vulnerabilityassessmentscanazureasyncoperation | Nee | Nee |
> | locaties/vulnerabilityassessmentscanoperationresults | Nee | Nee |
> | managedinstances | Nee | Nee |
> | managedinstances/beheerders | Nee | Nee |
> | managedinstances/data bases | Nee | Nee |
> | managedinstances/data bases/backuplongtermretentionpolicies | Nee | Nee |
> | managedinstances/data bases/vulnerabilityassessments | Nee | Nee |
> | managedinstances / metricdefinitions | Nee | Nee |
> | managedinstances/meet waarden | Nee | Nee |
> | managedinstances / recoverabledatabases | Nee | Nee |
> | managedinstances / tdecertificates | Nee | Nee |
> | managedinstances / vulnerabilityassessments | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | Server | Ja | Ja |
> | servers/administratoroperationresults | Nee | Nee |
> | servers/beheerders | Nee | Nee |
> | servers/adviseurs | Nee | Nee |
> | servers/aggregateddatabasemetrics | Nee | Nee |
> | servers/auditingpolicies | Nee | Nee |
> | servers/auditingsettings | Nee | Nee |
> | servers/automatictuning | Nee | Nee |
> | servers/communicationlinks | Nee | Nee |
> | servers/connectionpolicies | Nee | Nee |
> | servers/data bases | Ja | Ja |
> | servers/data bases/adviseurs | Nee | Nee |
> | servers/data bases/auditingpolicies | Nee | Nee |
> | servers/data bases/auditingsettings | Nee | Nee |
> | servers/data bases/auditrecords | Nee | Nee |
> | servers/data bases/automatictuning | Nee | Nee |
> | servers/data bases/backuplongtermretentionpolicies | Nee | Nee |
> | servers/data bases/backupshorttermretentionpolicies | Nee | Nee |
> | servers/data bases/connectionpolicies | Nee | Nee |
> | servers/data bases/datamaskingpolicies | Nee | Nee |
> | servers/data bases/datamaskingpolicies/rules | Nee | Nee |
> | servers/data bases/uitbrei dingen | Nee | Nee |
> | servers/data bases/geobackuppolicies | Nee | Nee |
> | servers/data bases/metricdefinitions | Nee | Nee |
> | servers/data bases/metrische gegevens | Nee | Nee |
> | servers/data bases/recommendedsensitivitylabels | Nee | Nee |
> | servers/data bases/securityalertpolicies | Nee | Nee |
> | servers/data bases/syncgroups | Nee | Nee |
> | servers/data bases/syncgroups/syncmembers | Nee | Nee |
> | servers/data bases/topqueries | Nee | Nee |
> | servers/data bases/topqueries/QueryText | Nee | Nee |
> | servers/data bases/transparentdataencryption | Nee | Nee |
> | servers/data bases/vulnerabilityassessment | Nee | Nee |
> | servers/data bases/vulnerabilityassessments | Nee | Nee |
> | servers/data bases/vulnerabilityassessmentscans | Nee | Nee |
> | servers/data bases/vulnerabilityassessmentsettings | Nee | Nee |
> | servers/data bases/workloadgroups | Nee | Nee |
> | servers/databasesecuritypolicies | Nee | Nee |
> | servers/disasterrecoveryconfiguration | Nee | Nee |
> | servers/dnsaliases | Nee | Nee |
> | servers/elasticpoolestimates | Nee | Nee |
> | servers/elasticpools | Ja | Ja |
> | servers/elasticpools/Advisor | Nee | Nee |
> | servers/elasticpools/metricdefinitions | Nee | Nee |
> | servers/elasticpools/metrieken | Nee | Nee |
> | servers/encryptionprotector | Nee | Nee |
> | servers/extendedauditingsettings | Nee | Nee |
> | servers/failovergroups | Nee | Nee |
> | servers/importeren | Nee | Nee |
> | servers/importexportoperationresults | Nee | Nee |
> | servers/jobaccounts | Ja | Ja |
> | servers/jobagents | Ja | Ja |
> | servers/jobagents/taken | Nee | Nee |
> | servers/jobagents/Jobs/uitvoeringen | Nee | Nee |
> | servers/jobagents/Jobs/stappen | Nee | Nee |
> | servers/sleutels | Nee | Nee |
> | servers/operationresults | Nee | Nee |
> | servers/recommendedelasticpools | Nee | Nee |
> | servers/recoverabledatabases | Nee | Nee |
> | servers/restorabledroppeddatabases | Nee | Nee |
> | servers/securityalertpolicies | Nee | Nee |
> | servers/serviceobjectives | Nee | Nee |
> | servers/syncagents | Nee | Nee |
> | servers/tdecertificates | Nee | Nee |
> | servers/gebruiks toepassingen | Nee | Nee |
> | servers/virtualnetworkrules | Nee | Nee |
> | servers/vulnerabilityassessments | Nee | Nee |
> | virtualclusters | Ja | Ja |

> [!IMPORTANT]
> Een Data Base en server moeten zich in dezelfde resource groep bestaan. Wanneer u een SQL-Server verplaatst, worden alle bijbehorende data bases ook verplaatst. Dit gedrag is van toepassing op Azure SQL Database en Azure Synapse Analytics-data bases.

## <a name="microsoftsqlvirtualmachine"></a>Micro soft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | locaties | Nee | Nee |
> | locaties/availabilitygrouplisteneroperationresults | Nee | Nee |
> | locaties/operationtypes | Nee | Nee |
> | locaties/sqlvirtualmachinegroupoperationresults | Nee | Nee |
> | locaties/sqlvirtualmachineoperationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | sqlvirtualmachinegroups | Ja | Ja |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | Nee | Nee |
> | sqlvirtualmachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/asyncoperations | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | locaties/deletevirtualnetworkorsubnets | Nee | Nee |
> | locaties/gebruik | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | Storage accounts | Ja | Ja |
> | Storage accounts/blobservices | Nee | Nee |
> | Storage accounts/fileservices | Nee | Nee |
> | Storage accounts/listaccountsas | Nee | Nee |
> | Storage accounts/listservicesas | Nee | Nee |
> | Storage accounts/queueservices | Nee | Nee |
> | Storage accounts/Services | Nee | Nee |
> | Storage accounts/Services/metricdefinitions | Nee | Nee |
> | Storage accounts/tableservices | Nee | Nee |
> | gebruik | Nee | Nee |

## <a name="microsoftstoragecache"></a>Micro soft. StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | caches | Nee | Nee |

## <a name="microsoftstoragesync"></a>Micro soft. StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | locaties | Nee | Nee |
> | locaties/checknameavailability | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/bewerkingen | Nee | Nee |
> | locaties/werk stromen | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | storagesyncservices | Ja | Ja |
> | storagesyncservices/registeredserver | Nee | Nee |
> | storagesyncservices / syncgroups | Nee | Nee |
> | storagesyncservices / syncgroups / cloudendpoints | Nee | Nee |
> | storagesyncservices / syncgroups / serverendpoints | Nee | Nee |
> | storagesyncservices/werk stromen | Nee | Nee |

## <a name="microsoftstoragesyncdev"></a>Micro soft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nee | Nee |

## <a name="microsoftstoragesyncint"></a>Micro soft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nee | Nee |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | leider | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/quota's | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | streamingjobs | Ja | Ja |

> [!IMPORTANT]
> Stream Analytics taken kunnen niet worden verplaatst wanneer de status actief is.

## <a name="microsoftstreamanalyticsexplorer"></a>Micro soft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | verschillend | Nee | Nee |
> | omgevingen/eventsources | Nee | Nee |
> | vaak | Nee | Nee |
> | exemplaren/omgevingen | Nee | Nee |
> | exemplaren/omgevingen/eventsources | Nee | Nee |

## <a name="microsoftsubscription"></a>Micro soft. Subscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | annuleren | Nee | Nee |
> | createsubscription | Nee | Nee |
> | inschakelen | Nee | Nee |
> | operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | naam wijzigen | Nee | Nee |
> | subscriptiondefinitions | Nee | Nee |
> | subscriptionoperations | Nee | Nee |
> | geabonneerd | Nee | Nee |

## <a name="microsoftsupport"></a>micro soft. ondersteuning

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | operationresults | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | operationsstatus | Nee | Nee |
> | services | Nee | Nee |
> | Services/problemclassifications | Nee | Nee |
> | supporttickets | Nee | Nee |

## <a name="microsoftsynapse"></a>Micro soft. Synapse

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | workspaces | Ja | Ja |
> | werk ruimten/bigdatapools | Ja | Ja |
> | werk ruimten/operationresults | Nee | Nee |
> | werk ruimten/operationstatuses | Nee | Nee |
> | werk ruimten/sqlpools | Ja | Ja |

## <a name="microsofttimeseriesinsights"></a>Micro soft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | verschillend | Ja | Ja |
> | omgevingen/accesspolicies | Nee | Nee |
> | omgevingen/eventsources | Ja | Ja |
> | omgevingen/referencedatasets | Ja | Ja |
> | bewerkingen | Nee | Nee |

## <a name="microsofttoken"></a>Micro soft. token

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | opslaat | Ja | Ja |
> | winkels/accesspolicies | Nee | Nee |
> | winkels/Services | Nee | Nee |
> | archieven/Services/tokens | Nee | Nee |

## <a name="microsoftvirtualmachineimages"></a>Micro soft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Nee | Nee |
> | imagetemplates / runoutputs | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/bewerkingen | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftvisualstudio"></a>micro soft. Visual Studio

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | account | Nee | Nee |
> | account/extensie | Nee | Nee |
> | account/project | Nee | Nee |
> | checknameavailability | Nee | Nee |
> | bewerkingen | Nee | Nee |

> [!IMPORTANT]
> Als u het abonnement voor Azure DevOps wilt wijzigen, raadpleegt u [het Azure-abonnement wijzigen dat wordt gebruikt voor facturering](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmware"></a>Micro soft. VMware

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | arczones | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/operationstatuses | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | resourcepools | Nee | Nee |
> | vCenter | Nee | Nee |
> | informatie | Nee | Nee |
> | virtualmachinetemplates | Nee | Nee |
> | virtualnetworks | Nee | Nee |

## <a name="microsoftvmwarecloudsimple"></a>Micro soft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Nee | Nee |
> | dedicatedcloudservices | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/Availabilities | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/privateclouds | Nee | Nee |
> | locaties/privateclouds/resourcepools | Nee | Nee |
> | locaties/privateclouds/virtualmachinetemplates | Nee | Nee |
> | locaties/privateclouds/virtualnetworks | Nee | Nee |
> | locaties/gebruik | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | informatie | Nee | Nee |

## <a name="microsoftvnfmanager"></a>Micro soft. VnfManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | devices | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/operationstatuses | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | vnfs | Nee | Nee |

## <a name="microsoftvsonline"></a>Micro soft. VSOnline

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | plant | Nee | Nee |
> | registeredsubscriptions | Nee | Nee |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | availablestacks | Nee | Nee |
> | billingmeters | Nee | Nee |
> | certificaten | No | Yes |
> | checknameavailability | Nee | Nee |
> | connectiongateways | Ja | Ja |
> | inbel | Ja | Ja |
> | customapis | Ja | Ja |
> | deletedsites | Nee | Nee |
> | deploymentlocations | Nee | Nee |
> | georegio's | Nee | Nee |
> | hostingenvironments | Nee | Nee |
> | hostingenvironments / eventgridfilters | Nee | Nee |
> | hostingenvironments / multirolepools | Nee | Nee |
> | hostingenvironments / workerpools | Nee | Nee |
> | ishostingenvironmentnameavailable | Nee | Nee |
> | ishostnameavailable | Nee | Nee |
> | isusernameavailable | Nee | Nee |
> | kubeenvironments | Ja | Ja |
> | listsitesassignedtohostname | Nee | Nee |
> | locaties | Nee | Nee |
> | locaties/apioperations | Nee | Nee |
> | locaties/connectiongatewayinstallations | Nee | Nee |
> | locaties/deletedsites | Nee | Nee |
> | locaties/deletevirtualnetworkorsubnets | Nee | Nee |
> | locaties/extractapidefinitionfromwsdl | Nee | Nee |
> | locaties/getnetworkpolicies | Nee | Nee |
> | locaties/listwsdlinterfaces | Nee | Nee |
> | locaties/beheerdeapi's | Nee | Nee |
> | locaties/operationresults | Nee | Nee |
> | locaties/bewerkingen | Nee | Nee |
> | locaties/Runtimes | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | publishingusers | Nee | Nee |
> | aanbevelingen | Nee | Nee |
> | resourcehealthmetadata | Nee | Nee |
> | Runtimes | Nee | Nee |
> | server farms | Ja | Ja |
> | server farms/eventgridfilters | Nee | Nee |
> | sites | Ja | Ja |
> | sites/eventgridfilters | Nee | Nee |
> | sites/hostnamebindings | Nee | Nee |
> | sites/networkconfig | Nee | Nee |
> | sites/premieraddons | Ja | Ja |
> | sites/sleuven | Ja | Ja |
> | sites/sleuven/eventgridfilters | Nee | Nee |
> | sites/sleuven/hostnamebindings | Nee | Nee |
> | sites/sleuven/networkconfig | Nee | Nee |
> | sourcecontrols | Nee | Nee |
> | staticsites | Nee | Nee |
> | subelementid | Nee | Nee |
> | verifyhostingenvironmentvnet | Nee | Nee |

> [!IMPORTANT]
> Zie [app service richt lijnen voor verplaatsen](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Micro soft. WindowsESU

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | locaties | Nee | Nee |
> | locaties/operationstatuses | Nee | Nee |
> | multipleactivationkeys | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftwindowsiot"></a>Micro soft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | deviceservices | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="microsoftworkloadbuilder"></a>Micro soft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | locaties | Nee | Nee |
> | locaties/operationstatuses | Nee | Nee |
> | bewerkingen | Nee | Nee |
> | workloads | Nee | Nee |

## <a name="microsoftworkloadmonitor"></a>Micro soft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | materialen | Nee | Nee |
> | componentssummary | Nee | Nee |
> | monitorinstances | Nee | Nee |
> | monitorinstancessummary | Nee | Nee |
> | monitors | Nee | Nee |
> | notificationsettings | Nee | Nee |
> | bewerkingen | Nee | Nee |

## <a name="third-party-services"></a>Services van derden

Services van derden bieden momenteel geen ondersteuning voor de verplaatsings bewerking.

## <a name="next-steps"></a>Volgende stappen
Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](move-resource-group-and-subscription.md)voor opdrachten voor het verplaatsen van resources.

Als u dezelfde gegevens wilt ophalen als een bestand met door komma's gescheiden waarden, downloadt u [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).