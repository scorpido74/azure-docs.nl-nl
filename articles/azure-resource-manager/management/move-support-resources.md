---
title: Ondersteuning voor het verplaatsen van bewerkingen per resource type
description: Een lijst met de Azure-resource typen die kunnen worden verplaatst naar een nieuwe resource groep of een nieuw abonnement.
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 8ab194ad240e4f3e0994314ef9ade3bc7159cf81
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273928"
---
# <a name="move-operation-support-for-resources"></a>Ondersteuning voor het verplaatsen van resources voor bronnen
Dit artikel geeft een overzicht van de vraag of een Azure-resource type de verplaatsings bewerking ondersteunt. Het bevat ook informatie over speciale voor waarden die u moet overwegen bij het verplaatsen van een resource.

Ga naar de naam ruimte van een resource provider:
> [!div class="op_single_selector"]
> - [Micro soft. AAD](#microsoftaad)
> - [micro soft. aadiam](#microsoftaadiam)
> - [Micro soft. Advisor](#microsoftadvisor)
> - [Micro soft. AlertsManagement](#microsoftalertsmanagement)
> - [Micro soft. AnalysisServices](#microsoftanalysisservices)
> - [Micro soft. ApiManagement](#microsoftapimanagement)
> - [Micro soft. AppConfiguration](#microsoftappconfiguration)
> - [Micro soft. AppPlatform](#microsoftappplatform)
> - [Micro soft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Micro soft. Automation](#microsoftautomation)
> - [Micro soft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Micro soft. Azureworden](#microsoftazuredata)
> - [Micro soft. AzureStack](#microsoftazurestack)
> - [Micro soft. batch](#microsoftbatch)
> - [Micro soft. BatchAI](#microsoftbatchai)
> - [Micro soft. billing](#microsoftbilling)
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
> - [Micro soft. verbruik](#microsoftconsumption)
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
> - [Micro soft. DataProtection](#microsoftdataprotection)
> - [Micro soft. DataShare](#microsoftdatashare)
> - [Micro soft. DBforMariaDB](#microsoftdbformariadb)
> - [Micro soft. DBforMySQL](#microsoftdbformysql)
> - [Micro soft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Micro soft. DeploymentManager](#microsoftdeploymentmanager)
> - [Micro soft.-apparaten](#microsoftdevices)
> - [Micro soft. DevOps](#microsoftdevops)
> - [Micro soft. DevSpaces](#microsoftdevspaces)
> - [Micro soft. DevTestLab](#microsoftdevtestlab)
> - [Micro soft. DigitalTwins](#microsoftdigitaltwins)
> - [Micro soft. DocumentDB](#microsoftdocumentdb)
> - [Micro soft. DomainRegistration](#microsoftdomainregistration)
> - [Micro soft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Micro soft. EventGrid](#microsofteventgrid)
> - [Micro soft. EventHub](#microsofteventhub)
> - [Micro soft. Genomics](#microsoftgenomics)
> - [Micro soft. GuestConfiguration](#microsoftguestconfiguration)
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
> - [Micro soft. Kubernetes](#microsoftkubernetes)
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
> - [Micro soft. ManagedServices](#microsoftmanagedservices)
> - [Micro soft. Maps](#microsoftmaps)
> - [Micro soft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Micro soft. Media](#microsoftmedia)
> - [Micro soft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Micro soft. migrate](#microsoftmigrate)
> - [Micro soft. NetApp](#microsoftnetapp)
> - [Micro soft. Network](#microsoftnetwork)
> - [Micro soft. notification hubs](#microsoftnotificationhubs)
> - [Micro soft. ObjectStore](#microsoftobjectstore)
> - [Micro soft. OperationalInsights](#microsoftoperationalinsights)
> - [Micro soft. OperationsManagement](#microsoftoperationsmanagement)
> - [Micro soft. peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Micro soft. Portal](#microsoftportal)
> - [Micro soft. PortalSdk](#microsoftportalsdk)
> - [Micro soft. PowerBI](#microsoftpowerbi)
> - [Micro soft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Micro soft. ProjectBabylon](#microsoftprojectbabylon)
> - [Micro soft. ProjectOxford](#microsoftprojectoxford)
> - [Micro soft. ProviderHub](#microsoftproviderhub)
> - [Micro soft. Recovery Services](#microsoftrecoveryservices)
> - [Micro soft. relay](#microsoftrelay)
> - [Micro soft. ResourceGraph](#microsoftresourcegraph)
> - [Micro soft. ResourceHealth](#microsoftresourcehealth)
> - [Micro soft. resources](#microsoftresources)
> - [Micro soft. SaaS](#microsoftsaas)
> - [Micro soft. Search](#microsoftsearch)
> - [Micro soft. Security](#microsoftsecurity)
> - [Micro soft. SecurityInsights](#microsoftsecurityinsights)
> - [Micro soft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Micro soft. ServiceFabric](#microsoftservicefabric)
> - [Micro soft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Micro soft. Services](#microsoftservices)
> - [Micro soft. SignalRService](#microsoftsignalrservice)
> - [Micro soft. SoftwarePlan](#microsoftsoftwareplan)
> - [Micro soft. Solutions](#microsoftsolutions)
> - [Micro soft. SQL](#microsoftsql)
> - [Micro soft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Micro soft. SqlVM](#microsoftsqlvm)
> - [Micro soft. Storage](#microsoftstorage)
> - [Micro soft. StorageSync](#microsoftstoragesync)
> - [Micro soft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Micro soft. StorageSyncInt](#microsoftstoragesyncint)
> - [Micro soft. StorSimple](#microsoftstorsimple)
> - [Micro soft. StreamAnalytics](#microsoftstreamanalytics)
> - [Micro soft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Micro soft. Subscription](#microsoftsubscription)
> - [micro soft. ondersteuning](#microsoftsupport)
> - [Micro soft. TerraformOSS](#microsoftterraformoss)
> - [Micro soft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Micro soft. token](#microsofttoken)
> - [micro soft. Visual Studio](#microsoftvisualstudio)
> - [Micro soft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Micro soft. VSOnline](#microsoftvsonline)
> - [Micro soft. Web](#microsoftweb)
> - [Micro soft. WindowsIoT](#microsoftwindowsiot)
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
> | vereisten | Nee | Nee |
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
> | Server | Ja | Ja |

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
> | werk ruimten | Nee | Nee |

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
> | Volg | Nee | Nee |

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
> | Dump | Nee | Nee |
> | externalsubscriptions | Nee | Nee |
> | Functies | Nee | Nee |
> | query | Nee | Nee |
> | Reportconfigs | Nee | Nee |
> | rapporten | Nee | Nee |
> | showbackrules | Nee | Nee |
> | Weergaven | Nee | Nee |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | bedrijfs | Nee | Nee |

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
> | werk ruimten | Nee | Nee |

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
> | Server | Ja | Ja |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Server | Ja | Ja |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | servergroups | Nee | Nee |
> | Server | Ja | Ja |
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

## <a name="microsoftdigitaltwins"></a>Micro soft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Nee | Nee |

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
> | onderwerp | Ja | Ja |

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
> | privatelinkscopes | Ja | Ja |
> | scheduledqueryrules | Ja | Ja |
> | topologie | Nee | Nee |
> | transacties | Nee | Nee |
> | vminsightsonboardingstatuses | Nee | Nee |
> | webtests | Ja | Ja |
> | bladen | Ja | Ja |
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
> | werk ruimten | Ja | Ja |

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
> | werk ruimten | Nee | Nee |
> | werk ruimten/reken bewerkingen | Nee | Nee |

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
> | firewallpolicies | Ja | Ja |
> | frontdoors | Nee | Nee |
> | frontdoorwebapplicationfirewallpolicies | Nee | Nee |
> | ipgroups | Ja | Ja |
> | loadbalancers | Ja, basis-SKU<br>Geen standaard-SKU | Ja, basis-SKU<br>Geen standaard-SKU |
> | localnetworkgateways | Ja | Ja |
> | networkexperimentprofiles | Ja | Ja |
> | networkintentpolicies | Ja | Ja |
> | networkinterfaces | Ja | Ja |
> | networkprofiles | Nee | Nee |
> | networksecuritygroups | Ja | Ja |
> | networkwatchers | Ja | Nee |
> | networkwatchers / connectionmonitors | Ja | Nee |
> | networkwatchers / flowlogs | Ja | Nee |
> | networkwatchers/lenzen | Ja | Nee |
> | networkwatchers / pingmeshes | Ja | Nee |
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
> | werk ruimten | Ja | Ja |

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
> | Dash boards | Ja | Ja |

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
> | Wijzer | Nee | Nee |
> | meldingen | Nee | Nee |
> | dataconnectors | Nee | Nee |
> | dataconnectorscheckrequirements | Nee | Nee |
> | Rijg | Nee | Nee |
> | entityqueries | Nee | Nee |
> | gevallen | Nee | Nee |
> | officeconsents | Nee | Nee |
> | instellingen | Nee | Nee |

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
> | managedclusters | Nee | Nee |
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
> | Server | Ja | Ja |
> | servers/data bases | Ja | Ja |
> | servers/elasticpools | Ja | Ja |
> | servers/jobaccounts | Ja | Ja |
> | servers/jobagents | Ja | Ja |
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
> | vaak | Nee | Nee |
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
> | account/extensie | Ja | Ja |
> | account/project | Ja | Ja |

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
> | bewijzen | Nee | Ja |
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
