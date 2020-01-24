---
title: Ondersteuning voor het verplaatsen van bewerkingen per resource type
description: Een lijst met de Azure-resource typen die kunnen worden verplaatst naar een nieuwe resource groep of een nieuw abonnement.
ms.topic: conceptual
ms.date: 01/22/2020
ms.openlocfilehash: 3fe404b8b501056f75ac45ccebc5bdbed1acbe43
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705476"
---
# <a name="move-operation-support-for-resources"></a>Ondersteuning voor het verplaatsen van resources voor bronnen
Dit artikel geeft een overzicht van de vraag of een Azure-resource type de verplaatsings bewerking ondersteunt. Het bevat ook informatie over speciale voor waarden die u moet overwegen bij het verplaatsen van een resource.

Ga naar de naam ruimte van een resource provider:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Micro soft. Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Micro soft. AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Micro soft. Azureworden](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Micro soft. billing](#microsoftbilling)
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
> - [Micro soft. verbruik](#microsoftconsumption)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Micro soft. CustomProviders](#microsoftcustomproviders)
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
> - [Micro soft. DataProtection](#microsoftdataprotection)
> - [Micro soft. DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Micro soft. DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Micro soft. GuestConfiguration](#microsoftguestconfiguration)
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
> - [Micro soft. Kubernetes](#microsoftkubernetes)
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
> - [Micro soft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Micro soft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Micro soft. ObjectStore](#microsoftobjectstore)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Micro soft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Micro soft. ProviderHub](#microsoftproviderhub)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Micro soft. ResourceGraph](#microsoftresourcegraph)
> - [Micro soft. ResourceHealth](#microsoftresourcehealth)
> - [Micro soft. resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Micro soft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Micro soft. Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Micro soft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Micro soft. Subscription](#microsoftsubscription)
> - [micro soft. ondersteuning](#microsoftsupport)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Micro soft. VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Micro soft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | domainservices | Nee | Nee |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | tenants | Nee | Nee |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | configuraties | Nee | Nee |
> | aanbevelingen | Nee | Nee |
> | onderdrukkingen | Nee | Nee |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | actionrules | Ja | Ja |
> | waarschuwingen | Nee | Nee |
> | alertssummary | Nee | Nee |
> | smartdetectoralertrules | Ja | Ja |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | servers | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | service | Ja | Ja |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | configurationstores | Ja | Ja |

## <a name="microsoftappplatform"></a>Micro soft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | lente | Ja | Ja |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | apiapps | Nee | Nee |
> | appidentities | Nee | Nee |
> | gateways | Nee | Nee |

> [!IMPORTANT]
> Zie [app service richt lijnen voor verplaatsen](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checkaccess | Nee | Nee |
> | denyassignments | Nee | Nee |
> | findorphanroleassignments | Nee | Nee |
> | vergren delingen | Nee | Nee |
> | machtigingen | Nee | Nee |
> | policyassignments | Nee | Nee |
> | policydefinitions | Nee | Nee |
> | policysetdefinitions | Nee | Nee |
> | roleassignments | Nee | Nee |
> | roleassignmentsusagemetrics | Nee | Nee |
> | roledefinitions | Nee | Nee |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Ja | Ja |
> | automationaccounts/configuraties | Ja | Ja |
> | automationaccounts/runbooks | Ja | Ja |

> [!IMPORTANT]
> Runbooks moeten zich in dezelfde resource groep bevinden als het Automation-account.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Ja | Ja |

## <a name="microsoftazuredata"></a>Micro soft. Azureworden

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | Nee | Nee |
> | postgresinstances | Nee | Nee |
> | sqlbigdataclusters | Nee | Nee |
> | sqlinstances | Nee | Nee |
> | sqlserverregistrations | Ja | Ja |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | registraties | Ja | Ja |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ja | Ja |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Nee | Nee |
> | fileservers | Nee | Nee |
> | functies | Nee | Nee |
> | werkruimten | Nee | Nee |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | billingperiods | Nee | Nee |
> | billingpermissions | Nee | Nee |
> | billingroleassignments | Nee | Nee |
> | billingroledefinitions | Nee | Nee |
> | createbillingroleassignment | Nee | Nee |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | mapapis | Nee | Nee |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | biztalk | Nee | Nee |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Nee | Nee |
> | kijkers | Nee | Nee |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Nee | Nee |
> | blauw drukken | Nee | Nee |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | botservices | Ja | Ja |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | redis | Ja | Ja |

> [!IMPORTANT]
> Als de Azure-cache voor redis-exemplaar is geconfigureerd met een virtueel netwerk, kan het exemplaar niet worden verplaatst naar een ander abonnement. Zie [beperkingen](./move-limitations/networking-move-limitations.md)voor het verplaatsen van netwerken.

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Ja | Ja |
> | profiles | Ja | Ja |
> | profielen/eind punten | Ja | Ja |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | certificateorders | Ja | Ja |

> [!IMPORTANT]
> Zie [app service richt lijnen voor verplaatsen](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | domein naam | Ja | Nee |
> | informatie | Ja | Nee |

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/classic-model-move-limitations.md)voor het verplaatsen van de klassieke implementatie. Klassieke implementatie resources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Nee | Nee |
> | reservedips | Nee | Nee |
> | virtualnetworks | Nee | Nee |

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/classic-model-move-limitations.md)voor het verplaatsen van de klassieke implementatie. Klassieke implementatie resources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Storage accounts | Ja | Nee |

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/classic-model-move-limitations.md)voor het verplaatsen van de klassieke implementatie. Klassieke implementatie resources kunnen worden verplaatst tussen abonnementen met een bewerking die specifiek is voor dat scenario.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Availability sets | Ja | Ja |
> | diskencryptionsets | Nee | Nee |
> | cd's | Ja | Ja |
> | Galerij | Nee | Nee |
> | galerieën/afbeeldingen | Nee | Nee |
> | galerieën/afbeeldingen/versies | Nee | Nee |
> | hostgroups | Nee | Nee |
> | hostgroups/hosts | Nee | Nee |
> | images | Ja | Ja |
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

## <a name="microsoftconsumption"></a>Microsoft.Consumption

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
> | Markt plaatsen | Nee | Nee |
> | operationresults | Nee | Nee |
> | operationstatus | Nee | Nee |
> | Pricesheets | Nee | Nee |
> | producten | Nee | Nee |
> | reservationdetails | Nee | Nee |
> | reservationrecommendations | Nee | Nee |
> | reservationsummaries | Nee | Nee |
> | reservationtransactions | Nee | Nee |
> | tags | Nee | Nee |
> | tenants | Nee | Nee |
> | inzake | Nee | Nee |
> | usagedetails | Nee | Nee |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | containergroups | Nee | Nee |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | containergroups | Nee | Nee |
> | serviceassociationlinks | Nee | Nee |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | registers | Ja | Ja |
> | registers/buildtasks | Ja | Ja |
> | registers/replicaties | Ja | Ja |
> | registers/taskruns | Ja | Ja |
> | registers/taken | Ja | Ja |
> | registers/webhooks | Ja | Ja |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | containerservices | Nee | Nee |
> | managedclusters | Nee | Nee |
> | openshiftmanagedclusters | Nee | Nee |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | toepassingen | Nee | Nee |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | waarschuwingen | Nee | Nee |
> | budgetten | Nee | Nee |
> | connectoren | Ja | Ja |
> | hoogte | Nee | Nee |
> | dump | Nee | Nee |
> | externalsubscriptions | Nee | Nee |
> | forecast | Nee | Nee |
> | query | Nee | Nee |
> | Reportconfigs | Nee | Nee |
> | rapporten | Nee | Nee |
> | showbackrules | Nee | Nee |
> | Weergaven | Nee | Nee |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hubs | Nee | Nee |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | lidkoppelingen | Nee | Nee |
> | resourceproviders | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | functies | Nee | Nee |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Nee | Nee |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | werkruimten | Nee | Nee |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | catalogi | Ja | Ja |
> | datacatalogs | Nee | Nee |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Nee | Nee |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Pakketten | Nee | Nee |
> | plant | Nee | Nee |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | datafactories | Ja | Ja |
> | factory's | Ja | Ja |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Nee | Nee |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | services | Nee | Nee |
> | Services/projecten | Nee | Nee |
> | sleuf | Nee | Nee |

## <a name="microsoftdataprotection"></a>Micro soft. DataProtection

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | backupvaults | Nee | Nee |

## <a name="microsoftdatashare"></a>Micro soft. DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | servers | Ja | Ja |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | servers | Ja | Ja |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | servergroups | Nee | Nee |
> | servers | Ja | Ja |
> | serversv2 | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | artifactsources | Ja | Ja |
> | implementaties | Ja | Ja |
> | servicetopologies | Ja | Ja |
> | servicetopologies/Services | Ja | Ja |
> | servicetopologies/Services/serviceunits | Ja | Ja |
> | stappen | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | elasticpools | Nee | Nee |
> | elasticpools / iothubtenants | Nee | Nee |
> | iothubs | Ja | Ja |
> | provisioningservices | Ja | Ja |

## <a name="microsoftdevops"></a>Micro soft. DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | pijp lijnen | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | fungeren | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | labcenters | Nee | Nee |
> | Labs | Ja | Nee |
> | Labs/omgevingen | Ja | Ja |
> | Labs-servicerunners | Ja | Ja |
> | Labs-informatie | Ja | Nee |
> | schema's | Ja | Ja |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | domeinen | Ja | Ja |

## <a name="microsoftenterpriseknowledgegraph"></a>Micro soft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | domeinen | Ja | Ja |
> | eventSubscriptions | Nee, kan niet onafhankelijk worden verplaatst, maar automatisch verplaatst met een geabonneerde resource. | Nee, kan niet onafhankelijk worden verplaatst, maar automatisch verplaatst met een geabonneerde resource. |
> | eventsubscriptions | Nee, kan niet onafhankelijk worden verplaatst, maar automatisch verplaatst met een geabonneerde resource. | Nee, kan niet onafhankelijk worden verplaatst, maar automatisch verplaatst met een geabonneerde resource. |
> | extensiontopics | Nee | Nee |
> | -onderwerpen | Ja | Ja |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Ja | Ja |
> | naam ruimten | Ja | Ja |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftguestconfiguration"></a>Micro soft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | guestconfigurationassignments | Nee | Nee |
> | software | Nee | Nee |
> | softwareupdateprofile | Nee | Nee |
> | softwareupdates | Nee | Nee |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hanainstances | Nee | Nee |
> | sapmonitors | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Ja | Ja |

> [!IMPORTANT]
> U kunt de HDInsight-clusters verplaatsen naar een nieuw abonnement of resourcegroep. U kunt echter niet verplaatsen tussen abonnementen de netwerkresources die worden gekoppeld aan het HDInsight-cluster (zoals het virtuele netwerk, een NIC of een load balancer). Bovendien verplaatsen u niet naar een nieuwe resourcegroep een NIC die is gekoppeld aan een virtuele machine voor het cluster.
>
> Bij het verplaatsen van een HDInsight-cluster naar een nieuw abonnement, verplaatst u eerst andere bronnen (zoals de storage-account). Verplaats het HDInsight-cluster op zichzelf.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | services | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | apparaten | Ja | Ja |
> | computers/uitbrei dingen | Nee | Nee |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | datamanagers | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | functies | Ja | Ja |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | actiongroups | Ja | Ja |
> | activitylogalerts | Nee | Nee |
> | alertrules | Ja | Ja |
> | automatedexportsettings | Nee | Nee |
> | autoscalesettings | Ja | Ja |
> | gebonden | Nee | Nee |
> | calculatebaseline | Nee | Nee |
> | materialen | Ja | Ja |
> | diagnosticsettings | Nee | Nee |
> | diagnosticsettingscategories | Nee | Nee |
> | eventtypes | Nee | Nee |
> | extendeddiagnosticsettings | Nee | Nee |
> | logdefinitions | Nee | Nee |
> | logboeken | Nee | Nee |
> | metricalerts | Nee | Nee |
> | metricbaselines | Nee | Nee |
> | metricdefinitions | Nee | Nee |
> | metricnamespaces | Nee | Nee |
> | metrics | Nee | Nee |
> | myworkbooks | Nee | Nee |
> | scheduledqueryrules | Ja | Ja |
> | topologie | Nee | Nee |
> | transacties | Nee | Nee |
> | vminsightsonboardingstatuses | Nee | Nee |
> | webtests | Ja | Ja |
> | werkmappen | Ja | Ja |
> | workbooktemplates | Ja | Ja |

> [!IMPORTANT]
> Zorg ervoor dat het verplaatsen naar een nieuw abonnement geen [abonnements quota's](azure-subscription-service-limits.md#azure-monitor-limits)overschrijdt.

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | iotapps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ja | Ja |
> | graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | kluizen | Ja | Ja |

> [!IMPORTANT]
> Sleutel kluizen die worden gebruikt voor schijf versleuteling, kunnen niet worden verplaatst naar een resource groep in hetzelfde abonnement of tussen abonnementen.

## <a name="microsoftkubernetes"></a>Micro soft. Kubernetes

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Nee | Nee |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Ja | Ja |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | labaccounts | Nee | Nee |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

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
> | integrationserviceenvironments | Ja | Nee |
> | integrationserviceenvironments/beheerdeapi's | Ja | Nee |
> | isolatedenvironments | Nee | Nee |
> | stroom | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Ja | Ja |
> | webservices | Ja | Nee |
> | werkruimten | Ja | Ja |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Nee | Nee |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |
> | accounts/werk ruimten | Nee | Nee |
> | accounts/werk ruimten/projecten | Nee | Nee |
> | teamaccounts | Nee | Nee |
> | teamaccounts/werk ruimten | Nee | Nee |
> | teamaccounts/werk ruimten/projecten | Nee | Nee |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Nee | Nee |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | werkruimten | Nee | Nee |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | identiteit | Nee | Nee |
> | userassignedidentities | Nee | Nee |

## <a name="microsoftmanagedservices"></a>Micro soft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | registrationassignments | Nee | Nee |
> | registrationdefinitions | Nee | Nee |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nee | Nee |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Media Services | Ja | Ja |
> | Media Services/liveevents | Ja | Ja |
> | Media Services/streamingendpoints | Ja | Ja |

## <a name="microsoftmicroservices4spring"></a>Micro soft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | appclusters | Nee | Nee |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Ja | Ja |
> | migrateprojects | Ja | Ja |
> | projecten | Nee | Nee |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Nee | Nee |
> | netappaccounts / backuppolicies | Nee | Nee |
> | netappaccounts / capacitypools | Nee | Nee |
> | netappaccounts/capacitypools/volumes | Nee | Nee |
> | netappaccounts/capacitypools/volumes/mounttargets | Nee | Nee |
> | netappaccounts/capacitypools/volumes/moment opnamen | Nee | Nee |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Nee | Nee |
> | applicationgatewaywebapplicationfirewallpolicies | Nee | Nee |
> | applicationsecuritygroups | Ja | Ja |
> | azurefirewalls | Ja | Ja |
> | bastionhosts | Nee | Nee |
> | inbel | Ja | Ja |
> | ddoscustompolicies | Ja | Ja |
> | ddosprotectionplans | Nee | Nee |
> | dnszones | Ja | Ja |
> | expressroutecircuits | Nee | Nee |
> | expressroutegateways | Nee | Nee |
> | frontdoors | Nee | Nee |
> | frontdoorwebapplicationfirewallpolicies | Nee | Nee |
> | loadbalancers | Ja, basis-SKU<br>Geen standaard-SKU | Ja, basis-SKU<br>Geen standaard-SKU |
> | localnetworkgateways | Ja | Ja |
> | networkexperimentprofiles | Ja | Ja |
> | networkintentpolicies | Ja | Ja |
> | networkinterfaces | Ja | Ja |
> | networkprofiles | Nee | Nee |
> | networksecuritygroups | Ja | Ja |
> | networkwatchers | Ja | Ja |
> | networkwatchers / connectionmonitors | Ja | Ja |
> | networkwatchers / flowlogs | Ja | Ja |
> | networkwatchers/lenzen | Ja | Ja |
> | networkwatchers / pingmeshes | Ja | Ja |
> | p2svpngateways | Nee | Nee |
> | privatednszones | Ja | Ja |
> | privatednszones / virtualnetworklinks | Ja | Ja |
> | privateendpointredirectmaps | Nee | Nee |
> | privateendpoints | Nee | Nee |
> | privatelinkservices | Nee | Nee |
> | publicipaddresses | Ja, basis-SKU<br>Geen standaard-SKU | Ja, basis-SKU<br>Geen standaard-SKU |
> | publicipprefixes | Ja | Ja |
> | routefilters | Nee | Nee |
> | routetables | Ja | Ja |
> | serviceendpointpolicies | Ja | Ja |
> | trafficmanagerprofiles | Ja | Ja |
> | virtualhubs | Nee | Nee |
> | virtualnetworkgateways | Ja | Ja |
> | virtualnetworks | Ja | Ja |
> | virtualnetworktaps | Nee | Nee |
> | virtualrouters | Ja | Ja |
> | virtualwans | Nee | Nee |
> | vpngateways (virtueel WAN) | Nee | Nee |
> | vpnserverconfigurations | Nee | Nee |
> | vpnsites (virtueel WAN) | Nee | Nee |
> | webapplicationfirewallpolicies | Ja | Ja |

> [!IMPORTANT]
> Zie [richt lijnen](./move-limitations/networking-move-limitations.md)voor het verplaatsen van netwerken.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | naam ruimten | Ja | Ja |
> | naam ruimten/notification hubs | Ja | Ja |

## <a name="microsoftobjectstore"></a>Micro soft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | storageinsightconfigs | Nee | Nee |
> | werkruimten | Ja | Ja |

> [!IMPORTANT]
> Zorg ervoor dat het verplaatsen naar een nieuw abonnement geen [abonnements quota's](azure-subscription-service-limits.md#azure-monitor-limits)overschrijdt.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | managementassociations | Nee | Nee |
> | managementconfigurations | Ja | Ja |
> | oplossingen | Ja | Ja |
> | Weergaven | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Peerings | Ja | Ja |
> | peeringservices | Nee | Nee |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | policyevents | Nee | Nee |
> | policystates | Nee | Nee |
> | policytrackedresources | Nee | Nee |
> | herstel | Nee | Nee |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | dashboards | Ja | Ja |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | rootresources | Nee | Nee |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | beschikt | Ja | Ja |

## <a name="microsoftprojectbabylon"></a>Micro soft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftproviderhub"></a>Micro soft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | implementaties | Nee | Nee |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Nee | Nee |
> | replicationeligibilityresults | Nee | Nee |
> | kluizen | Ja | Ja |

> [!IMPORTANT]
> Zie [Recovery Services richt lijnen voor verplaatsen](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | naam ruimten | Ja | Ja |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | query's | Ja | Ja |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Nee | Nee |
> | childavailabilitystatuses | Nee | Nee |
> | childresources | Nee | Nee |
> | events | Nee | Nee |
> | meldingen | Nee | Nee |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | Nee | Nee |
> | koppelen | Nee | Nee |
> | tags | Nee | Nee |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | toepassingen | Ja | Nee |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | jobcollections | Ja | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | searchservices | Ja | Ja |

> [!IMPORTANT]
> U kunt niet meerdere Zoek resources in verschillende regio's in één bewerking verplaatsen. Verplaats deze in plaats daarvan in afzonderlijke bewerkingen.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Nee | Nee |
> | advancedthreatprotectionsettings | Nee | Nee |
> | assessmentmetadata | Nee | Nee |
> | evaluaties | Nee | Nee |
> | automatisering | Ja | Ja |
> | complianceresults | Nee | Nee |
> | Ingebouwde strengste | Nee | Nee |
> | datacollectionagents | Nee | Nee |
> | datacollectionresults | Nee | Nee |
> | devicesecuritygroups | Nee | Nee |
> | informationprotectionpolicies | Nee | Nee |
> | iotsecuritysolutions | Ja | Ja |
> | servervulnerabilityassessments | Nee | Nee |

## <a name="microsoftsecurityinsights"></a>Micro soft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | aggregaties | Nee | Nee |
> | alertrules | Nee | Nee |
> | alertruletemplates | Nee | Nee |
> | bladwijzers | Nee | Nee |
> | meldingen | Nee | Nee |
> | dataconnectors | Nee | Nee |
> | Rijg | Nee | Nee |
> | entityqueries | Nee | Nee |
> | officeconsents | Nee | Nee |
> | instellingen | Nee | Nee |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | gateways | Nee | Nee |
> | knooppunten | Nee | Nee |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | naam ruimten | Ja | Ja |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
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
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | toepassingen | Ja | Ja |
> | gateways | Ja | Ja |
> | netwerken | Ja | Ja |
> | geheimen | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservices"></a>Micro soft. Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | implementaties | Nee | Nee |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | signalr | Ja | Ja |

## <a name="microsoftsoftwareplan"></a>Micro soft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Nee | Nee |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Nee | Nee |
> | toepassingen | Nee | Nee |
> | jitrequests | Nee | Nee |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
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
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Ja | Ja |
> | sqlvirtualmachines | Ja | Ja |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | dwvm | Nee | Nee |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Storage accounts | Ja | Ja |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ja | Ja |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nee | Nee |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nee | Nee |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | leider | Nee | Nee |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Ja | Ja |

> [!IMPORTANT]
> Stream Analytics taken kunnen niet worden verplaatst wanneer de status actief is.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | verschillend | Nee | Nee |
> | omgevingen/eventsources | Nee | Nee |
> | instanties | Nee | Nee |
> | exemplaren/omgevingen | Nee | Nee |
> | exemplaren/omgevingen/eventsources | Nee | Nee |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | createsubscription | Nee | Nee |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | createsupportticket | Nee | Nee |
> | supporttickets | Nee | Nee |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Nee | Nee |
> | resources | Nee | Nee |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | verschillend | Ja | Ja |
> | omgevingen/eventsources | Ja | Ja |
> | omgevingen/referencedatasets | Ja | Ja |

## <a name="microsofttoken"></a>Micro soft. token

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | opslaat | Ja | Ja |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | account | Nee | Nee |
> | account/extensie | Nee | Nee |
> | account/project | Nee | Nee |

> [!IMPORTANT]
> Als u het abonnement voor Azure DevOps wilt wijzigen, raadpleegt u [het Azure-abonnement wijzigen dat wordt gebruikt voor facturering](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Nee | Nee |
> | dedicatedcloudservices | Nee | Nee |
> | informatie | Nee | Nee |

## <a name="microsoftvsonline"></a>Micro soft. VSOnline

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |
> | plant | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | certificaten | Nee | Ja |
> | connectiongateways | Ja | Ja |
> | inbel | Ja | Ja |
> | customapis | Ja | Ja |
> | hostingenvironments | Nee | Nee |
> | server farms | Ja | Ja |
> | sites | Ja | Ja |
> | sites/premieraddons | Ja | Ja |
> | sites/sleuven | Ja | Ja |
> | staticsites | Nee | Nee |

> [!IMPORTANT]
> Zie [app service richt lijnen voor verplaatsen](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | deviceservices | Nee | Nee |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | materialen | Nee | Nee |
> | monitorinstances | Nee | Nee |
> | monitors | Nee | Nee |
> | notificationsettings | Nee | Nee |

## <a name="third-party-services"></a>Services van derden

Services van derden bieden momenteel geen ondersteuning voor de verplaatsings bewerking.

## <a name="next-steps"></a>Volgende stappen
Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](move-resource-group-and-subscription.md)voor opdrachten voor het verplaatsen van resources.

Down load [Move-support-resources. CSV](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)om dezelfde gegevens op te halen als een bestand met door komma's gescheiden waarden.
