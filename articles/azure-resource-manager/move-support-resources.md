---
title: Ondersteuning voor het verplaatsen van bewerkingen door Azure-resource type
description: Een lijst met de Azure-resource typen die kunnen worden verplaatst naar een nieuwe resource groep of een nieuw abonnement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: tomfitz
ms.openlocfilehash: 3100ce897a09c2747c2b339662c699d197ba73d7
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194845"
---
# <a name="move-operation-support-for-resources"></a>Ondersteuning voor het verplaatsen van resources voor bronnen
Dit artikel geeft een overzicht van de vraag of een Azure-resource type de verplaatsings bewerking ondersteunt. Het bevat ook informatie over speciale voor waarden die u moet overwegen bij het verplaatsen van een resource.

Ga naar de naam ruimte van een resource provider:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Micro soft. Azureworden](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Backup](#microsoftbackup)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
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
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [microsoft.dns](#microsoftdns)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Micro soft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Micro soft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
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
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | Nee | Nee |
> | domainservices / replicasets | Nee | Nee |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tenants | Nee | Nee |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | Ja | Ja |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | service | Ja | Ja |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationstores | Ja | Ja |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | Nee | Nee |
> | appidentities | Nee | Nee |
> | gateways | Nee | Nee |

> [!IMPORTANT]
> Zie [app service richt lijnen voor verplaatsen](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | policyassignments | Nee | Nee |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Ja | Ja |
> | automationaccounts/configuraties | Ja | Ja |
> | automationaccounts/runbooks | Ja | Ja |

> [!IMPORTANT]
> Runbooks moeten zich in dezelfde resource groep bevinden als het Automation-account.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Ja | Ja |

## <a name="microsoftazuredata"></a>Micro soft. Azureworden

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlserverregistrations | Nee | Nee |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registraties | Ja | Ja |

## <a name="microsoftbackup"></a>Microsoft.Backup

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupvault | Nee | Nee |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ja | Ja |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Nee | Nee |
> | fileservers | Nee | Nee |
> | taken | Nee | Nee |
> | werkruimten | Nee | Nee |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | Nee | Nee |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | Ja | Ja |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Ja | Ja |
> | kijkers | Nee | Nee |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Nee | Nee |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | Ja | Ja |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | Ja | Ja |

> [!IMPORTANT]
> Als de Azure-cache voor redis-exemplaar is geconfigureerd met een virtueel netwerk, kan het exemplaar niet worden verplaatst naar een ander abonnement. Zie [beperkingen](./move-limitations/networking-move-limitations.md)voor het verplaatsen van netwerken.

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Nee | Nee |
> | profiles | Ja | Ja |
> | profielen/eind punten | Ja | Ja |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | Ja | Ja |

> [!IMPORTANT]
> Zie [app service richt lijnen voor verplaatsen](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domein naam | Ja | Nee |
> | informatie | Ja | Nee |

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/classic-model-move-limitations.md)voor het verplaatsen van de klassieke implementatie. Klassieke implementatie resources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Nee | Nee |
> | reservedips | Nee | Nee |
> | virtualnetworks | Nee | Nee |

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/classic-model-move-limitations.md)voor het verplaatsen van de klassieke implementatie. Klassieke implementatie resources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Storage accounts | Ja | Nee |

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/classic-model-move-limitations.md)voor het verplaatsen van de klassieke implementatie. Klassieke implementatie resources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Availability sets | Ja | Ja |
> | diskencryptionsets | Nee | Nee |
> | schijven | Ja | Ja |
> | Galerij | Nee | Nee |
> | galerieën/afbeeldingen | Nee | Nee |
> | galerieën/afbeeldingen/versies | Nee | Nee |
> | hostgroups | Nee | Nee |
> | hostgroups/hosts | Nee | Nee |
> | afbeeldingen | Ja | Ja |
> | proximityplacementgroups | Nee | Nee |
> | restorepointcollections | Nee | Nee |
> | sharedvmimages | Nee | Nee |
> | sharedvmimages/versies | Nee | Nee |
> | momentopnamen | Ja | Ja |
> | informatie | Ja | Ja |
> | informatie/extensies | Ja | Ja |
> | virtualmachinescalesets | Ja | Ja |

> [!IMPORTANT]
> Zie [virtual machines richt lijnen voor verplaatsen](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Nee | Nee |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Nee | Nee |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registers | Ja | Ja |
> | registers/buildtasks | Ja | Ja |
> | registers/replicaties | Ja | Ja |
> | registers/taken | Ja | Ja |
> | registers/webhooks | Ja | Ja |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | Nee | Nee |
> | managedclusters | Nee | Nee |
> | openshiftmanagedclusters | Nee | Nee |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | toepassingen | Ja | Ja |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectoren | Ja | Ja |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | bedrijfs | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | taken | Nee | Nee |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Nee | Nee |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | werkruimten | Nee | Nee |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogi | Ja | Ja |
> | datacatalogs | Nee | Nee |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Nee | Nee |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pakketten | Nee | Nee |
> | plant | Nee | Nee |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | Ja | Ja |
> | factory's | Ja | Ja |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Nee | Nee |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Nee | Nee |
> | Services/projecten | Nee | Nee |
> | sleuf | Nee | Nee |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Ja | Ja |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Ja | Ja |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | Nee | Nee |
> | servers | Ja | Ja |
> | serversv2 | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | Ja | Ja |
> | implementaties | Ja | Ja |
> | servicetopologies | Ja | Ja |
> | servicetopologies/Services | Ja | Ja |
> | servicetopologies/Services/serviceunits | Ja | Ja |
> | stappen | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | Nee | Nee |
> | elasticpools / iothubtenants | Nee | Nee |
> | iothubs | Ja | Ja |
> | provisioningservices | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fungeren | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | Nee | Nee |
> | Labs | Ja | Nee |
> | Labs/omgevingen | Ja | Ja |
> | Labs-servicerunners | Ja | Ja |
> | Labs-informatie | Ja | Nee |
> | schema's | Ja | Ja |

## <a name="microsoftdns"></a>microsoft.dns

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dnszones | Nee | Nee |
> | dnszones/a | Nee | Nee |
> | dnszones/AAAA | Nee | Nee |
> | dnszones/CNAME | Nee | Nee |
> | dnszones/MX | Nee | Nee |
> | dnszones/PTR | Nee | Nee |
> | dnszones/SRV | Nee | Nee |
> | dnszones/txt | Nee | Nee |
> | trafficmanagerprofiles | Nee | Nee |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domeinen | Ja | Ja |

## <a name="microsoftenterpriseknowledgegraph"></a>Micro soft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domeinen | Ja | Ja |
> | onderwerp | Ja | Ja |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Ja | Ja |
> | naamruimten | Ja | Ja |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | Nee | Nee |
> | sapmonitors | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Ja | Ja |

> [!IMPORTANT]
> U kunt de HDInsight-clusters verplaatsen naar een nieuw abonnement of resourcegroep. U kunt echter niet verplaatsen tussen abonnementen de netwerkresources die worden gekoppeld aan het HDInsight-cluster (zoals het virtuele netwerk, een NIC of een load balancer). Bovendien verplaatsen u niet naar een nieuwe resourcegroep een NIC die is gekoppeld aan een virtuele machine voor het cluster.
>
> Bij het verplaatsen van een HDInsight-cluster naar een nieuw abonnement, verplaatst u eerst andere bronnen (zoals de storage-account). Verplaats het HDInsight-cluster op zichzelf.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | computers | Nee | Nee |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | taken | Ja | Ja |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |
> | actiongroups | Ja | Ja |
> | activitylogalerts | Nee | Nee |
> | alertrules | Ja | Ja |
> | autoscalesettings | Ja | Ja |
> | materialen | Ja | Ja |
> | guestdiagnosticsettings | Nee | Nee |
> | metricalerts | Nee | Nee |
> | notificationgroups | Nee | Nee |
> | notificationrules | Nee | Nee |
> | scheduledqueryrules | Ja | Ja |
> | webtests | Ja | Ja |
> | werkmappen | Ja | Ja |

> [!IMPORTANT]
> Zorg ervoor dat het verplaatsen naar een nieuw abonnement geen [abonnements quota's](../azure-subscription-service-limits.md#azure-monitor-limits)overschrijdt.

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ja | Ja |
> | graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hsmpools | Nee | Nee |
> | kluizen | Ja | Ja |

> [!IMPORTANT]
> Sleutel kluizen die worden gebruikt voor schijf versleuteling, kunnen niet worden verplaatst naar een resource groep in hetzelfde abonnement of tussen abonnementen.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Ja | Ja |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | Nee | Nee |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Nee | Nee |
> | integrationaccounts | Ja | Ja |
> | integrationserviceenvironments | Nee | Nee |
> | isolatedenvironments | Nee | Nee |
> | stroom | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Ja | Ja |
> | webservices | Ja | Nee |
> | werkruimten | Ja | Ja |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Ja | Ja |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |
> | accounts/werk ruimten | Nee | Nee |
> | accounts/werk ruimten/projecten | Nee | Nee |
> | teamaccounts | Nee | Nee |
> | teamaccounts/werk ruimten | Nee | Nee |
> | teamaccounts/werk ruimten/projecten | Nee | Nee |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Nee | Nee |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | werkruimten | Nee | Nee |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | userassignedidentities | Nee | Nee |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nee | Nee |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Media Services | Ja | Ja |
> | Media Services/liveevents | Ja | Ja |
> | Media Services/streamingendpoints | Ja | Ja |

## <a name="microsoftmicroservices4spring"></a>Micro soft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | Nee | Nee |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Nee | Nee |
> | migrateprojects | Nee | Nee |
> | projecten | Nee | Nee |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Nee | Nee |
> | netappaccounts / capacitypools | Nee | Nee |
> | netappaccounts/capacitypools/volumes | Nee | Nee |
> | netappaccounts/capacitypools/volumes/mounttargets | Nee | Nee |
> | netappaccounts/capacitypools/volumes/moment opnamen | Nee | Nee |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Nee | Nee |
> | applicationgatewaywebapplicationfirewallpolicies | Nee | Nee |
> | applicationsecuritygroups | Ja | Ja |
> | azurefirewalls | Ja | Ja |
> | bastionhosts | Nee | Nee |
> | verbindingen | Ja | Ja |
> | ddoscustompolicies | Ja | Ja |
> | ddosprotectionplans | Nee | Nee |
> | dnszones | Ja | Ja |
> | expressroutecircuits | Nee | Nee |
> | expressroutecrossconnections | Nee | Nee |
> | expressroutegateways | Nee | Nee |
> | expressrouteports | Nee | Nee |
> | ingangen | Nee | Nee |
> | frontdoorwebapplicationfirewallpolicies | Nee | Nee |
> | loadbalancers | Ja, basis-SKU<br>Geen standaard-SKU | Ja, basis-SKU<br>Geen standaard-SKU |
> | localnetworkgateways | Ja | Ja |
> | natgateways | Ja | Ja |
> | networkintentpolicies | Ja | Ja |
> | networkinterfaces | Ja | Ja |
> | networkprofiles | Nee | Nee |
> | networksecuritygroups | Ja | Ja |
> | networkwatchers | Ja | Ja |
> | networkwatchers / connectionmonitors | Ja | Ja |
> | networkwatchers/lenzen | Ja | Ja |
> | networkwatchers / pingmeshes | Ja | Ja |
> | p2svpngateways | Nee | Nee |
> | privatednszones | Ja | Ja |
> | privatednszones / virtualnetworklinks | Ja | Ja |
> | privateendpoints | Nee | Nee |
> | privatelinkservices | Nee | Nee |
> | publicipaddresses | Ja, basis-SKU<br>Geen standaard-SKU | Ja, basis-SKU<br>Geen standaard-SKU |
> | publicipprefixes | Ja | Ja |
> | routefilters | Nee | Nee |
> | routetables | Ja | Ja |
> | securegateways | Ja | Ja |
> | serviceendpointpolicies | Ja | Ja |
> | trafficmanagerprofiles | Ja | Ja |
> | virtualhubs | Nee | Nee |
> | virtualnetworkgateways | Ja | Ja |
> | virtualnetworks | Ja | Ja |
> | virtualnetworktaps | Nee | Nee |
> | virtualwans | Nee | Nee |
> | vpngateways (virtueel WAN) | Nee | Nee |
> | vpnsites (virtueel WAN) | Nee | Nee |
> | webapplicationfirewallpolicies | Ja | Ja |

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/networking-move-limitations.md)voor het verplaatsen van netwerken.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | naamruimten | Ja | Ja |
> | naam ruimten/notification hubs | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | werkruimten | Ja | Ja |

> [!IMPORTANT]
> Zorg ervoor dat het verplaatsen naar een nieuw abonnement geen [abonnements quota's](../azure-subscription-service-limits.md#azure-monitor-limits)overschrijdt.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | Ja | Ja |
> | oplossingen | Ja | Ja |
> | weergaven | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | peerings | Nee | Nee |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Dash boards | Ja | Ja |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | Nee | Nee |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | beschikt | Ja | Ja |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | kluizen | Ja | Ja |

> [!IMPORTANT]
> Zie [Recovery Services richt lijnen voor verplaatsen](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | naamruimten | Ja | Ja |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | query's | Ja | Ja |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | toepassingen | Ja | Nee |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | stromen | Ja | Ja |
> | jobcollections | Ja | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | Ja | Ja |

> [!IMPORTANT]
> U kunt niet meerdere Zoek resources in verschillende regio's in één bewerking verplaatsen. Verplaats deze in plaats daarvan in afzonderlijke bewerkingen.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotsecuritysolutions | Ja | Ja |
> | playbookconfigurations | Nee | Nee |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | Nee | Nee |
> | knooppunten | Nee | Nee |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | naamruimten | Ja | Ja |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | toepassingen | Nee | Nee |
> | clusters | Ja | Ja |
> | clusters/toepassingen | Nee | Nee |
> | containergroups | Nee | Nee |
> | containergroupsets | Nee | Nee |
> | edgeclusters | Nee | Nee |
> | netwerken | Nee | Nee |
> | secretstores | Nee | Nee |
> | volumes | Nee | Nee |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | toepassingen | Ja | Ja |
> | containergroups | Nee | Nee |
> | gateways | Ja | Ja |
> | netwerken | Ja | Ja |
> | geheimen | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | Ja | Ja |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | siterecoveryvault | Nee | Nee |

> [!IMPORTANT]
> Zie [Recovery Services richt lijnen voor verplaatsen](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appliancedefinitions | Nee | Nee |
> | uitrusting | Nee | Nee |
> | applicationdefinitions | Nee | Nee |
> | toepassingen | Nee | Nee |
> | jitrequests | Nee | Nee |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | instancepools | Nee | Nee |
> | managedinstances | Nee | Nee |
> | managedinstances/data bases | Nee | Nee |
> | servers | Ja | Ja |
> | servers/data bases | Ja | Ja |
> | servers/elasticpools | Ja | Ja |
> | virtualclusters | Ja | Ja |

> [!IMPORTANT]
> Een Data Base en server moeten zich in dezelfde resource groep bestaan. Wanneer u een SQL-server hebt verplaatst, worden ook alle bijbehorende databases verplaatst. Dit gedrag is van toepassing op Azure SQL Database en Azure SQL Data Warehouse-databases.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Ja | Ja |
> | sqlvirtualmachines | Ja | Ja |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | Nee | Nee |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Storage accounts | Ja | Ja |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | caches | Nee | Nee |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ja | Ja |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nee | Nee |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nee | Nee |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | leider | Nee | Nee |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Ja | Ja |

> [!IMPORTANT]
> Stream Analytics taken kunnen niet worden verplaatst wanneer de status actief is.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | verschillend | Nee | Nee |
> | omgevingen/eventsources | Nee | Nee |
> | instanties | Nee | Nee |
> | exemplaren/omgevingen | Nee | Nee |
> | exemplaren/omgevingen/eventsources | Nee | Nee |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Nee | Nee |
> | bronnen | Nee | Nee |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | verschillend | Ja | Ja |
> | omgevingen/eventsources | Ja | Ja |
> | omgevingen/referencedatasets | Ja | Ja |

## <a name="microsofttoken"></a>Micro soft. token

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | opslaat | Nee | Nee |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Nee | Nee |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | Ja | Ja |
> | account/extensie | Ja | Ja |
> | account/project | Ja | Ja |

> [!IMPORTANT]
> Als u het abonnement voor Azure DevOps wilt wijzigen, raadpleegt u [het Azure-abonnement wijzigen dat wordt gebruikt voor facturering](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Ja | Ja |
> | dedicatedcloudservices | Ja | Ja |
> | informatie | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificaten | Nee | Ja |
> | connectiongateways | Ja | Ja |
> | verbindingen | Ja | Ja |
> | customapis | Ja | Ja |
> | hostingenvironments | Nee | Nee |
> | server farms | Ja | Ja |
> | sites | Ja | Ja |
> | sites/premieraddons | Ja | Ja |
> | sites/sleuven | Ja | Ja |

> [!IMPORTANT]
> Zie [app service richt lijnen voor verplaatsen](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | Nee | Nee |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Resourcetype | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Nee | Nee |
> | hostpools | Nee | Nee |
> | werkruimten | Nee | Nee |

## <a name="third-party-services"></a>Services van derden

Services van derden bieden momenteel geen ondersteuning voor de verplaatsings bewerking.

## <a name="next-steps"></a>Volgende stappen
Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](resource-group-move-resources.md)voor opdrachten voor het verplaatsen van resources.

Down load [Move-support-resources. CSV](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)om dezelfde gegevens op te halen als een bestand met door komma's gescheiden waarden.
