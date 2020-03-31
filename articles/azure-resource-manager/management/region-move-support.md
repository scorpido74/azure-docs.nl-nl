---
title: Ondersteuning voor het verplaatsen van Azure-resources in verschillende regio's
description: Hiermee worden de Azure-brontypen weergegeven die in Azure-regio's kunnen worden verplaatst
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/20/2020
ms.author: raynew
ms.openlocfilehash: 9bc7dc66ccf3049ac878f7871c816e5ade1afde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760705"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Ondersteuning voor het verplaatsen van Azure-resources in verschillende regio's

In dit artikel wordt bevestigd of een Azure-brontype wordt ondersteund voor het verplaatsen naar een andere Azure-regio. 

Ga naar de naamruimte van een resourceprovider:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsBeheer](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn (Microsoft.Cdn)](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigratie](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics (Microsoft.Genomics)](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.ioTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimenteren](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migreren](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM (Microsoft.SqlVM)](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- | 
> | domeinservices | Nee | 
> | domainservices / replicasets | Nee | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Huurders | Nee |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsBeheer

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | actieregels | Nee | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Servers | Nee |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | service |  Ja | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | configurationstores | Nee | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | apiapps | Nee | 
> | appidentige | Nee | 
> | Gateways | Nee | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | beleidstoewijzingen | Nee |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | automatiseringsrekeningen | Nee | 
> | automatiseringsaccounts / configuraties | Nee | 
> | automationaccounts / runbooks | Nee | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | b2cdirectories | Nee | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | sqlserverregistraties | Nee |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Registraties | Nee | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | batchaccounts | Nee |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Clusters | Nee | 
> | bestandsservers | Nee | 
> | Banen | Nee | 
> | werkruimten | Nee | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | mapapis | Nee | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | biztalk | Nee | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | blockchainleden | Nee |
> | Watchers | Nee | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | blauwdrukopdrachten | Nee | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | botservices | Nee | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Redis | Nee | 


## <a name="microsoftcdn"></a>Microsoft.Cdn (Microsoft.Cdn)

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallbeleid | Nee |
> | Profielen | Nee | 
> | profielen / eindpunten | Nee | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | certificaatorders | Nee | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Domeinnamen | Nee |  
> | virtuele machines | Nee | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | networksecuritygroups | Nee |
> | gereserveerde ips | Nee | 
> | virtuele netwerken | Nee | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | opslagrekeningen | Ja |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | beschikbaarheidssets | Nee | 
> | diskencryptiesets | Nee | 
> | Schijven | Nee | 
> | Galeries | Nee | 
> | galerijen / afbeeldingen | Nee | 
> | galerijen / afbeeldingen / versies | Nee | 
> | hostgroepen | Nee | 
> | hostgroups / hosts | Nee | 
> | images | Nee | 
> | proximityplacementgroepen | Nee | 
> | restorepointcollections | Nee | 
> | sharedvmimages | Nee | 
> | sharedvmimages / versies | Nee | 
> | momentopnamen | Nee | 
> | virtuele machines | Ja | 
> | virtualmachines / extensies | Nee | 
> | virtuelemachinescalesets | Nee | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | containergroepen | Nee | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | containergroepen | Nee | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Registers | Nee |  
> | registers / buildtaken | Nee |  
> | registers / replicaties | Nee | 
> | registers / taken | Nee |  
> | registers / webhooks | Nee | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | containerdiensten | Nee | 
> | managedclusters | Nee | 
> | openshiftmanagedclusters | Nee | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | toepassingen | Nee | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | connectoren | Nee |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | hubs | Nee |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | resourceproviders | Nee | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Banen | Nee | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | databoxedgeapparaten | Nee | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | werkruimten | Nee | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Catalogi | Nee | 
> | gegevenscatalogi | Nee | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | connectionmanagers | Nee | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Pakketten | Nee | 
> | Plannen | Nee | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | datafabrieken | Nee | 
> | Fabrieken | Nee |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | datalakeaccounts | Nee | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigratie

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | services | Nee | 
> | diensten / projecten | Nee | 
> | Slots | Nee | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Servers | Nee |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Servers | Nee |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | servergroepen | Nee | 
> | Servers | Nee |  
> | serversv2 | Nee | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | artefactenbronnen | Nee | 
> | implementaties | Nee |  
> | servicetopologieën | Nee | 
> | servicetopologieën / diensten | Nee |  
> | servicetopologieën / diensten / serviceunits | Nee | 
> | stappen | Nee | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | elastische pools | Nee | 
> | elasticpools / iothubtenants | Nee | 
> | iothubs | Ja | 
> | dienstverlening | Nee | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Controllers | Nee | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | labcenters | Nee | 
> | Labs | Nee | 
> | labs / omgevingen | Nee |  
> | labs / servicerunners | Nee | 
> | labs / virtualmachines | Nee |  
> | Planningen | Nee |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | databaseaccounts | Nee | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Domeinen | Nee | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | services | Nee |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Domeinen | Nee |  
> | Onderwerpen | Nee | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Clusters | Nee |  
> | Naamruimten | Nee | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics (Microsoft.Genomics)

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | hanainstances | Nee | 
> | sapmonitors | Nee |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Clusters | Nee | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | services | Nee |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Machines | Nee | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | datamanagers |  Nee | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Banen |  Nee | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 
> | actiegroepen |  Nee | 
> | activitylogalerts | Nee | 
> | waarschuwingsregels |  Nee | 
> | instellingen voor automatisch schalen |  Nee | 
> | Onderdelen |  Nee |  
> | gastdiagnoseinstellingen | Nee | 
> | metrische waarschuwingen | Nee | 
> | meldingsgroepen | Nee | 
> | kennisgevingsregels | Nee | 
> | geplande queryregels |  Nee | 
> | webtests |  Nee | 
> | werkmappen |  Nee |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | iotapps |  Nee |  

## <a name="microsoftiotspaces"></a>Microsoft.ioTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | beschikbaarheid van checkname |  Nee |  
> | Grafiek |  Nee | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | hsmpools | Nee | 
> | Kluizen |  Nee | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Clusters |  Nee |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | labaccounts | Nee | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | hostingomgevingen | Nee | 
> | integratierekeningen |  Nee |  
> | integratieserviceomgevingen | Nee | 
> | geïsoleerde omgevingen | Nee | 
> | Werkstromen |  Nee |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | toezeggingsplannen |  Nee | 
> | Webservices |  Nee | 
> | werkruimten |  Nee | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | operationalisatieclusters |  Nee | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimenteren

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 
> | accounts / werkruimten | Nee | 
> | accounts / werkplekken / projecten | Nee | 
> | teamaccounts | Nee | 
> | teamaccounts / werkruimten | Nee | 
> | teamaccounts / werkplekken / projecten | Nee | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | hostingaccounts | Nee | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | werkruimten | Nee | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | userassignedidentities | Nee | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | accounts |  Nee |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | classicdevservices | Nee | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | mediaservices |  Nee | 
> | mediaservices / liveevents |  Nee | 
> | mediaservices / streamingendpoints |  Nee | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | appclusters | Nee | 

## <a name="microsoftmigrate"></a>Microsoft.Migreren

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | beoordelingsprojecten | Nee | 
> | projecten migreren | Nee | 
> | Projecten | Nee | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | netappaccounts | Nee | 
> | netappaccounts / capaciteitspools | Nee | 
> | netappaccounts / capaciteitspools / volumes | Nee | 
> | netappaccounts / capaciteitspools / volumes / mounttargets | Nee | 
> | netappaccounts / capaciteitspools / volumes / momentopnamen | Nee | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | applicationgateways | Nee | 
> | applicationgatewaywebapplicationfirewallbeleid | Nee | 
> | applicationsecuritygroups |  Nee |  
> | azurefirewalls |  Nee |  
> | bastionhosts | Nee | 
> | Verbindingen |  Nee | 
> | ddoscustompolicies |  Nee | 
> | ddosprotectionplans | Nee | 
> | dnszones |  Nee | 
> | expressroutecircuits | Nee | 
> | snelwegen | Nee | 
> | expressroutegateways | Nee | 
> | expressrouteports | Nee | 
> | voordeuren | Nee | 
> | frontdoorwebapplicationfirewallbeleid | Nee | 
> | loadbalancers | Ja - Basis SKU<br>Nee - Standaard SKU | Ja - Basis SKU<br> -Ja Standaard SKU |
> | localnetworkgateways |  Nee | 
> | natgateways |  Nee | 
> | netwerkintentpolicies |  Nee | 
> | netwerkinterfaces | Ja | 
> | netwerkprofielen | Nee | 
> | networksecuritygroups | Ja | 
> | netwerkwatchers |  Nee |  
> | networkwatchers / connectionmonitors |  Nee | 
> | networkwatchers / lenzen |  Nee | 
> | networkwatchers / pingmeshes |  Nee | 
> | p2svpngateways | Nee | 
> | privatednszones |  Nee |  
> | privatednszones / virtualnetworklinks |  Nee |  
> | privateendpoints | Nee | 
> | privatelinkdiensten | Nee | 
> | openbare ipadressen | Ja - Basis SKU<br>Nee - Standaard SKU | Ja - Basis SKU<br>Nee - Standaard SKU |
> | publicipprefixes | Nee | 
> | routefilters | Nee | 
> | routetabellen |  Nee | 
> | serviceendpointbeleid |  Nee | 
> | trafficmanagerprofielen |  Nee | 
> | virtuele hubs | Nee | 
> | virtualnetworkgateways |  Nee |  
> | virtuele netwerken |  Nee | 
> | virtuele netwerkkranen | Nee | 
> | virtualwans virtualwans | Nee | 
> | vpngateways (Virtual WAN) | Nee | 
> | vpnsites (Virtual WAN) | Nee | 
> | webapplicationfirewallbeleid |  Nee | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Naamruimten |  Nee | 
> | naamruimten / notificationhubs |  Nee |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | werkruimten |  Nee | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | beheerconfiguraties |  Nee | 
> | Weergaven |  Nee | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | peerings | Nee | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | dashboards | Nee | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | wortelbronnen | Nee | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | werkruimtecollecties |  Nee | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Capaciteiten |  Nee | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Kluizen | Nee. [Kluis uitschakelen en opnieuw maken](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions) voor siteherstel  | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Naamruimten |  Nee | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Query 's |  Nee |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | toepassingen |  Nee | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | flows |  Nee |  
> | jobcollecties |  Nee | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | zoekservices |  Nee | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  Nee | 
> | playbookconfiguraties | Nee | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Gateways | Nee | 
> | Knooppunten | Nee | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Naamruimten |  Nee | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | toepassingen | Nee | 
> | Clusters |  Nee | 
> | clusters / toepassingen | Nee | 
> | containergroepen | Nee | 
> | containergroepsets | Nee | 
> | edgeclusters | Nee | 
> | Netwerken | Nee | 
> | secretstores | Nee | 
> | volumes | Nee | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | toepassingen |  Nee | 
> | containergroepen | Nee | 
> | Gateways |  Nee | 
> | Netwerken |  Nee | 
> | geheimen |  Nee | 
> | volumes |  Nee |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | signaalgever |  Nee |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | toesteldefinities | Nee | 
> | Apparaten | Nee | 
> | toepassingsdefinities | Nee | 
> | toepassingen | Nee | 
> | jitrequests | Nee | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | instantiepools | Nee | 
> | managedinstances | Ja | 
> | managedinstances / databases | Ja | 
> | Servers | Ja | 
> | servers / databases | Ja | 
> | servers / elastische pools | Ja | 
> | virtuele clusters | Ja | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroepen |  Nee |  
> | sqlvirtualmachines |  Nee |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM (Microsoft.SqlVM)

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | dwvm | Nee | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | opslagrekeningen | Ja | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Caches | Nee | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | storagesyncservices |  Nee | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | storagesyncservices | Nee | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | storagesyncservices | Nee | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Managers | Nee | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | streamingjobs |  Nee |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Omgevingen | Nee | 
> | omgevingen / eventsources | Nee | 
> | Exemplaren | Nee | 
> | instanties / omgevingen | Nee | 
> | instanties / omgevingen / eventsources | Nee | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | providerregistraties | Nee | 
> | resources | Nee | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Omgevingen |  Nee | 
> | omgevingen / eventsources |  Nee |  
> | omgevingen / referentiedatasets |  Nee | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | Winkels | Nee | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | afbeeldingssjablonen | Nee | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | account |  Nee | 
> | account / extensie |  Nee | 
> | account / project |  Nee | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Nee | 
> | dedicatedcloudservices | Nee | 
> | virtuele machines | Nee | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | certificaten | Nee | 
> | verbindingsgateways |  Nee |  
> | Verbindingen |  Nee |  
> | customapis |  Nee | 
> | hostingomgevingen | Nee | 
> | serverfarms |  Nee |  
> | sites |  Nee | 
> | sites / premieraddons |  Nee |  
> | sites / slots |  Nee |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | apparaatservices | Nee | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Resourcetype | Regioverplaatsen | 
> | ------------- | ----------- |
> | toepassingsgroepen | Nee | 
> | hostpools | Nee | 
> | werkruimten | Nee | 

## <a name="third-party-services"></a>Diensten van derden

Services van derden ondersteunen momenteel de verplaatsingsbewerking niet.
