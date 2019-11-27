---
title: Ondersteuning voor het verplaatsen van Azure-resources in verschillende regio's
description: Een lijst met de Azure-resource typen die kunnen worden verplaatst over Azure-regio's
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 60b8708458e081d66514e092edc9ef9af7c33494
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308100"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Ondersteuning voor het verplaatsen van Azure-resources in verschillende regio's

In dit artikel wordt bevestigd of een Azure-resource type wordt ondersteund voor het verplaatsen naar een andere Azure-regio. 

Ga naar de naam ruimte van een resource provider:
> [!div class="op_single_selector"]
> - [Micro soft. AAD](#microsoftaad)
> - [micro soft. aadiam](#microsoftaadiam)
> - [Micro soft. AlertsManagement](#microsoftalertsmanagement)
> - [Micro soft. AnalysisServices](#microsoftanalysisservices)
> - [Micro soft. ApiManagement](#microsoftapimanagement)
> - [Micro soft. AppConfiguration](#microsoftappconfiguration)
> - [Micro soft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Micro soft. Automation](#microsoftautomation)
> - [Micro soft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Micro soft. Azureworden](#microsoftazuredata)
> - [Micro soft. AzureStack](#microsoftazurestack)
> - [Micro soft. batch](#microsoftbatch)
> - [Micro soft. BatchAI](#microsoftbatchai)
> - [Micro soft. BingMaps](#microsoftbingmaps)
> - [Micro soft. BizTalkServices](#microsoftbiztalkservices)
> - [Micro soft. Block Chain](#microsoftblockchain)
> - [Micro soft. blauw druk](#microsoftblueprint)
> - [Micro soft. BotService](#microsoftbotservice)
> - [Micro soft. cache](#microsoftcache)
> - [Micro soft. CDN](#microsoftcdn)
> - [Micro soft. CertificateRegistration](#microsoftcertificateregistration)
> - [Micro soft. ClassicCompute](#microsoftclassiccompute)
> - [Micro soft. ClassicNetwork](#microsoftclassicnetwork)
> - [Micro soft. ClassicStorage](#microsoftclassicstorage)
> - [Micro soft. CognitiveServices](#microsoftcognitiveservices)
> - [Micro soft. compute](#microsoftcompute)
> - [Micro soft. container](#microsoftcontainer)
> - [Micro soft. ContainerInstance](#microsoftcontainerinstance)
> - [Micro soft. ContainerRegistry](#microsoftcontainerregistry)
> - [Micro soft. container service](#microsoftcontainerservice)
> - [Micro soft. ContentModerator](#microsoftcontentmoderator)
> - [Micro soft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Micro soft. CostManagement](#microsoftcostmanagement)
> - [Micro soft. CustomerInsights](#microsoftcustomerinsights)
> - [Micro soft. CustomProviders](#microsoftcustomproviders)
> - [Micro soft. DataBox](#microsoftdatabox)
> - [Micro soft. DataBoxEdge](#microsoftdataboxedge)
> - [Micro soft. Databricks](#microsoftdatabricks)
> - [Micro soft. DataCatalog](#microsoftdatacatalog)
> - [Micro soft. DataConnect](#microsoftdataconnect)
> - [Micro soft. DataExchange](#microsoftdataexchange)
> - [Micro soft. DataFactory](#microsoftdatafactory)
> - [Micro soft. DataLake](#microsoftdatalake)
> - [Micro soft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Micro soft. data Lake Store](#microsoftdatalakestore)
> - [Micro soft. DataMigration](#microsoftdatamigration)
> - [Micro soft. DataShare](#microsoftdatashare)
> - [Micro soft. DBforMariaDB](#microsoftdbformariadb)
> - [Micro soft. DBforMySQL](#microsoftdbformysql)
> - [Micro soft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Micro soft. DeploymentManager](#microsoftdeploymentmanager)
> - [Micro soft.-apparaten](#microsoftdevices)
> - [Micro soft. DevSpaces](#microsoftdevspaces)
> - [Micro soft. DevTestLab](#microsoftdevtestlab)
> - [Micro soft. DocumentDB](#microsoftdocumentdb)
> - [Micro soft. DomainRegistration](#microsoftdomainregistration)
> - [Micro soft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Micro soft. EventGrid](#microsofteventgrid)
> - [Micro soft. EventHub](#microsofteventhub)
> - [Micro soft. Genomics](#microsoftgenomics)
> - [Micro soft. HanaOnAzure](#microsofthanaonazure)
> - [Micro soft. HDInsight](#microsofthdinsight)
> - [Micro soft. HealthcareApis](#microsofthealthcareapis)
> - [Micro soft. HybridCompute](#microsofthybridcompute)
> - [Micro soft. HybridData](#microsofthybriddata)
> - [Micro soft. ImportExport](#microsoftimportexport)
> - [micro soft. Insights](#microsoftinsights)
> - [Micro soft. IoTCentral](#microsoftiotcentral)
> - [Micro soft. IoTSpaces](#microsoftiotspaces)
> - [Micro soft.-sleutel kluis](#microsoftkeyvault)
> - [Micro soft. Kusto](#microsoftkusto)
> - [Micro soft. LabServices](#microsoftlabservices)
> - [Micro soft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Micro soft. bestand locationservices](#microsoftlocationservices)
> - [Micro soft. Logic](#microsoftlogic)
> - [Micro soft. MachineLearning](#microsoftmachinelearning)
> - [Micro soft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Micro soft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Micro soft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Micro soft. MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Micro soft. MachineLearningServices](#microsoftmachinelearningservices)
> - [Micro soft. ManagedIdentity](#microsoftmanagedidentity)
> - [Micro soft. Maps](#microsoftmaps)
> - [Micro soft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Micro soft. Media](#microsoftmedia)
> - [Micro soft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Micro soft. migrate](#microsoftmigrate)
> - [Micro soft. NetApp](#microsoftnetapp)
> - [Micro soft. Network](#microsoftnetwork)
> - [Micro soft. notification hubs](#microsoftnotificationhubs)
> - [Micro soft. OperationalInsights](#microsoftoperationalinsights)
> - [Micro soft. OperationsManagement](#microsoftoperationsmanagement)
> - [Micro soft. peering](#microsoftpeering)
> - [Micro soft. Portal](#microsoftportal)
> - [Micro soft. PortalSdk](#microsoftportalsdk)
> - [Micro soft. PowerBI](#microsoftpowerbi)
> - [Micro soft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Micro soft. ProjectOxford](#microsoftprojectoxford)
> - [Micro soft. Recovery Services](#microsoftrecoveryservices)
> - [Micro soft. relay](#microsoftrelay)
> - [Micro soft. ResourceGraph](#microsoftresourcegraph)
> - [Micro soft. SaaS](#microsoftsaas)
> - [Micro soft. scheduler](#microsoftscheduler)
> - [Micro soft. Search](#microsoftsearch)
> - [Micro soft. Security](#microsoftsecurity)
> - [Micro soft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Micro soft. ServiceFabric](#microsoftservicefabric)
> - [Micro soft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Micro soft. SignalRService](#microsoftsignalrservice)
> - [Micro soft. Solutions](#microsoftsolutions)
> - [Micro soft. SQL](#microsoftsql)
> - [Micro soft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Micro soft. SqlVM](#microsoftsqlvm)
> - [Micro soft. Storage](#microsoftstorage)
> - [Micro soft. StorageCache](#microsoftstoragecache)
> - [Micro soft. StorageSync](#microsoftstoragesync)
> - [Micro soft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Micro soft. StorageSyncInt](#microsoftstoragesyncint)
> - [Micro soft. StorSimple](#microsoftstorsimple)
> - [Micro soft. StreamAnalytics](#microsoftstreamanalytics)
> - [Micro soft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Micro soft. TerraformOSS](#microsoftterraformoss)
> - [Micro soft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Micro soft. token](#microsofttoken)
> - [Micro soft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [micro soft. Visual Studio](#microsoftvisualstudio)
> - [Micro soft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Micro soft. Web](#microsoftweb)
> - [Micro soft. WindowsIoT](#microsoftwindowsiot)
> - [Micro soft. WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- | 
> | domainservices | Nee | 
> | domainservices / replicasets | Nee | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Tenants | Nee |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | actionrules | Nee | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | servers | Nee |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | service | Nee |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | configurationstores | Nee | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | apiapps | Nee | 
> | appidentities | Nee | 
> | gateways | Nee | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | policyassignments | Nee |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | automationaccounts | Nee | 
> | automationaccounts/configuraties | Nee | 
> | automationaccounts/runbooks | Nee | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | b2cdirectories | Nee | 

## <a name="microsoftazuredata"></a>Micro soft. Azureworden

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | sqlserverregistrations | Nee |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | registraties | Nee | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | batchaccounts | Nee |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | clusters | Nee | 
> | fileservers | Nee | 
> | functies | Nee | 
> | werk ruimten | Nee | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | mapapis | Nee | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | biztalk | Nee | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | blockchainmembers | Nee |
> | kijkers | Nee | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | blueprintassignments | Nee | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | botservices | Nee | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | redis | Nee | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Nee |
> | profiles | Nee | 
> | profielen/eind punten | Nee | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | certificateorders | Nee | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | domein naam | Nee |  
> | informatie | Nee | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | networksecuritygroups | Nee |
> | reservedips | Nee | 
> | virtualnetworks | Nee | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Storage accounts | Ja |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Availability sets | Nee | 
> | diskencryptionsets | Nee | 
> | cd's | Nee | 
> | Galerij | Nee | 
> | galerieën/afbeeldingen | Nee | 
> | galerieën/afbeeldingen/versies | Nee | 
> | hostgroups | Nee | 
> | hostgroups/hosts | Nee | 
> | images | Nee | 
> | proximityplacementgroups | Nee | 
> | restorepointcollections | Nee | 
> | sharedvmimages | Nee | 
> | sharedvmimages/versies | Nee | 
> | momentopnamen | Nee | 
> | informatie | Ja | 
> | informatie/extensies | Nee | 
> | virtualmachinescalesets | Nee | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | containergroups | Nee | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | containergroups | Nee | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | registers | Nee |  
> | registers/buildtasks | Nee |  
> | registers/replicaties | Nee | 
> | registers/taken | Nee |  
> | registers/webhooks | Nee | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | containerservices | Nee | 
> | managedclusters | Nee | 
> | openshiftmanagedclusters | Nee | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | toepassingen | Nee | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | connectoren | Nee |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | bedrijfs | Nee |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | resourceproviders | Nee | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | functies | Nee | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | databoxedgedevices | Nee | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | werk ruimten | Nee | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | catalogi | Nee | 
> | datacatalogs | Nee | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | connectionmanagers | Nee | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Pakketten | Nee | 
> | plant | Nee | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | datafactories | Nee | 
> | factory's | Nee |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | datalakeaccounts | Nee | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | services | Nee | 
> | Services/projecten | Nee | 
> | sleuf | Nee | 

## <a name="microsoftdatashare"></a>Micro soft. DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | servers | Nee |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | servers | Nee |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | servergroups | Nee | 
> | servers | Nee |  
> | serversv2 | Nee | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | artifactsources | Nee | 
> | implementaties | Nee |  
> | servicetopologies | Nee | 
> | servicetopologies/Services | Nee |  
> | servicetopologies/Services/serviceunits | Nee | 
> | stappen | Nee | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | elasticpools | Nee | 
> | elasticpools / iothubtenants | Nee | 
> | iothubs | Ja | 
> | provisioningservices | Nee | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | fungeren | Nee | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | labcenters | Nee | 
> | Labs | Nee | 
> | Labs/omgevingen | Nee |  
> | Labs-servicerunners | Nee | 
> | Labs-informatie | Nee |  
> | schema's | Nee |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | databaseaccounts | Nee | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | domeinen | Nee | 

## <a name="microsoftenterpriseknowledgegraph"></a>Micro soft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | services | Nee |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | domeinen | Nee |  
> | onderwerp | Nee | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | clusters | Nee |  
> | naam ruimten | Nee | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | hanainstances | Nee | 
> | sapmonitors | Nee |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | clusters | Nee | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | services | Nee |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | apparaten | Nee | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | datamanagers |  Nee | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | functies |  Nee | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 
> | actiongroups |  Nee | 
> | activitylogalerts | Nee | 
> | alertrules |  Nee | 
> | autoscalesettings |  Nee | 
> | materialen |  Nee |  
> | guestdiagnosticsettings | Nee | 
> | metricalerts | Nee | 
> | notificationgroups | Nee | 
> | notificationrules | Nee | 
> | scheduledqueryrules |  Nee | 
> | webtests |  Nee | 
> | bladen |  Nee |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | iotapps |  Nee |  

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | checknameavailability |  Nee |  
> | graph |  Nee | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | hsmpools | Nee | 
> | kluizen |  Nee | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | clusters |  Nee |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | labaccounts | Nee | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | hostingenvironments | Nee | 
> | integrationaccounts |  Nee |  
> | integrationserviceenvironments | Nee | 
> | isolatedenvironments | Nee | 
> | stroom |  Nee |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | commitmentplans |  Nee | 
> | webservices |  Nee | 
> | werk ruimten |  Nee | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | operationalizationclusters |  Nee | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 
> | accounts/werk ruimten | Nee | 
> | accounts/werk ruimten/projecten | Nee | 
> | teamaccounts | Nee | 
> | teamaccounts/werk ruimten | Nee | 
> | teamaccounts/werk ruimten/projecten | Nee | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | hostingaccounts | Nee | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | werk ruimten | Nee | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | userassignedidentities | Nee | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts |  Nee |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | classicdevservices | Nee | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Media Services |  Nee | 
> | Media Services/liveevents |  Nee | 
> | Media Services/streamingendpoints |  Nee | 

## <a name="microsoftmicroservices4spring"></a>Micro soft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | appclusters | Nee | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | assessmentprojects | Nee | 
> | migrateprojects | Nee | 
> | projecten | Nee | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | netappaccounts | Nee | 
> | netappaccounts / capacitypools | Nee | 
> | netappaccounts/capacitypools/volumes | Nee | 
> | netappaccounts/capacitypools/volumes/mounttargets | Nee | 
> | netappaccounts/capacitypools/volumes/moment opnamen | Nee | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | applicationgateways | Nee | 
> | applicationgatewaywebapplicationfirewallpolicies | Nee | 
> | applicationsecuritygroups |  Nee |  
> | azurefirewalls |  Nee |  
> | bastionhosts | Nee | 
> | inbel |  Nee | 
> | ddoscustompolicies |  Nee | 
> | ddosprotectionplans | Nee | 
> | dnszones |  Nee | 
> | expressroutecircuits | Nee | 
> | expressroutecrossconnections | Nee | 
> | expressroutegateways | Nee | 
> | expressrouteports | Nee | 
> | frontdoors | Nee | 
> | frontdoorwebapplicationfirewallpolicies | Nee | 
> | loadbalancers | Ja, basis-SKU<br>Geen standaard-SKU | Ja, basis-SKU<br> -Ja standaard-SKU |
> | localnetworkgateways |  Nee | 
> | natgateways |  Nee | 
> | networkintentpolicies |  Nee | 
> | networkinterfaces | Ja | 
> | networkprofiles | Nee | 
> | networksecuritygroups | Ja | 
> | networkwatchers |  Nee |  
> | networkwatchers / connectionmonitors |  Nee | 
> | networkwatchers/lenzen |  Nee | 
> | networkwatchers / pingmeshes |  Nee | 
> | p2svpngateways | Nee | 
> | privatednszones |  Nee |  
> | privatednszones / virtualnetworklinks |  Nee |  
> | privateendpoints | Nee | 
> | privatelinkservices | Nee | 
> | publicipaddresses | Ja, basis-SKU<br>Geen standaard-SKU | Ja, basis-SKU<br>Geen standaard-SKU |
> | publicipprefixes | Nee | 
> | routefilters | Nee | 
> | routetables |  Nee | 
> | serviceendpointpolicies |  Nee | 
> | trafficmanagerprofiles |  Nee | 
> | virtualhubs | Nee | 
> | virtualnetworkgateways |  Nee |  
> | virtualnetworks |  Nee | 
> | virtualnetworktaps | Nee | 
> | virtualwans | Nee | 
> | vpngateways (virtueel WAN) | Nee | 
> | vpnsites (virtueel WAN) | Nee | 
> | webapplicationfirewallpolicies |  Nee | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | naam ruimten |  Nee | 
> | naam ruimten/notification hubs |  Nee |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | werk ruimten |  Nee | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | managementconfigurations |  Nee | 
> | Weergaven |  Nee | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Peerings | Nee | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Dash boards | Nee | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | rootresources | Nee | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | workspacecollections |  Nee | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | beschikt |  Nee | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | accounts | Nee | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | kluizen | Ja (voor een back-upkluis die ik denk? | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | naam ruimten |  Nee | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | query's |  Nee |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | toepassingen |  Nee | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | terugloop |  Nee |  
> | jobcollections |  Nee | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | searchservices |  Nee | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  Nee | 
> | playbookconfigurations | Nee | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | gateways | Nee | 
> | knooppunten | Nee | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | naam ruimten |  Nee | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | toepassingen | Nee | 
> | clusters |  Nee | 
> | clusters/toepassingen | Nee | 
> | containergroups | Nee | 
> | containergroupsets | Nee | 
> | edgeclusters | Nee | 
> | netwerken | Nee | 
> | secretstores | Nee | 
> | volumes | Nee | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | toepassingen |  Nee | 
> | containergroups | Nee | 
> | gateways |  Nee | 
> | netwerken |  Nee | 
> | geheimen |  Nee | 
> | volumes |  Nee |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | signalr |  Nee |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | appliancedefinitions | Nee | 
> | uitrusting | Nee | 
> | applicationdefinitions | Nee | 
> | toepassingen | Nee | 
> | jitrequests | Nee | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | instancepools | Nee | 
> | managedinstances | Ja | 
> | managedinstances/data bases | Ja | 
> | servers | Ja | 
> | servers/data bases | Ja | 
> | servers/elasticpools | Ja | 
> | virtualclusters | Ja | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Nee |  
> | sqlvirtualmachines |  Nee |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | dwvm | Nee | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | Storage accounts | Ja? | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | caches | Nee | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | storagesyncservices |  Nee | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | storagesyncservices | Nee | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | storagesyncservices | Nee | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | leider | Nee | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | streamingjobs |  Nee |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | verschillend | Nee | 
> | omgevingen/eventsources | Nee | 
> | instanties | Nee | 
> | exemplaren/omgevingen | Nee | 
> | exemplaren/omgevingen/eventsources | Nee | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | providerregistrations | Nee | 
> | resources | Nee | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | verschillend |  Nee | 
> | omgevingen/eventsources |  Nee |  
> | omgevingen/referencedatasets |  Nee | 

## <a name="microsofttoken"></a>Micro soft. token

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | opslaat | Nee | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | imagetemplates | Nee | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | account |  Nee | 
> | account/extensie |  Nee | 
> | account/project |  Nee | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Nee | 
> | dedicatedcloudservices | Nee | 
> | informatie | Nee | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | bewijzen | Nee | 
> | connectiongateways |  Nee |  
> | inbel |  Nee |  
> | customapis |  Nee | 
> | hostingenvironments | Nee | 
> | server farms |  Nee |  
> | sites |  Nee | 
> | sites/premieraddons |  Nee |  
> | sites/sleuven |  Nee |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | deviceservices | Nee | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Resourcetype | Regio verplaatsen | 
> | ------------- | ----------- |
> | applicationgroups | Nee | 
> | hostpools | Nee | 
> | werk ruimten | Nee | 

## <a name="third-party-services"></a>Services van derden

Services van derden bieden momenteel geen ondersteuning voor de verplaatsings bewerking.
