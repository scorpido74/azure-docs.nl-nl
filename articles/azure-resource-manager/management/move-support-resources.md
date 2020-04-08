---
title: Ondersteuning voor bewerking verplaatsen op resourcetype
description: Hiermee worden de Azure-brontypen weergegeven die kunnen worden verplaatst naar een nieuwe brongroep of -abonnement.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 90fbec4dc076feb1fee8c38cf9757d3c5ddbafaf
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804789"
---
# <a name="move-operation-support-for-resources"></a>Ondersteuning voor het verplaatsen van resources
In dit artikel wordt weergegeven of een Azure-brontype de verplaatsingsbewerking ondersteunt. Het biedt ook informatie over speciale voorwaarden om te overwegen bij het verplaatsen van een resource.

Ga naar de naamruimte van een resourceprovider:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsBeheer](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn (Microsoft.Cdn)](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.Cognition](#microsoftcognition)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Verbruik](#microsoftconsumption)
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
> - [Microsoft.DataProtection](#microsoftdataprotection)
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
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimenteren](#microsoftexperimentation)
> - [Microsoft.Falcon (Microsoft.Falcon)](#microsoftfalcon)
> - [Microsoft.Genomics (Microsoft.Genomics)](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra (Microsoft.Hydra)](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.ioTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes (Microsoft.Kubernetes)](#microsoftkubernetes)
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
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migreren](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon (Microsoft.ProjectBabylon)](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
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
> - [Microsoft.Abonnement](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | domeinservices | Nee | Nee |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Huurders | Nee | Nee |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Configuraties | Nee | Nee |
> | aanbevelingen | Nee | Nee |
> | onderdrukkingen | Nee | Nee |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsBeheer

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | actieregels | Ja | Ja |
> | waarschuwingen | Nee | Nee |
> | overzicht van waarschuwingen | Nee | Nee |
> | smartdetectoralertregels | Ja | Ja |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Servers | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | service | Ja | Ja |

> [!IMPORTANT]
> Een API-beheerservice die is ingesteld op de SKU voor verbruik, kan niet worden verplaatst.

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | configurationstores | Ja | Ja |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Voorjaar | Ja | Ja |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | apiapps | Nee | Nee |
> | appidentige | Nee | Nee |
> | Gateways | Nee | Nee |

> [!IMPORTANT]
> Zie [Richtlijnen voor verplaatsen van app-service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | checkaccess | Nee | Nee |
> | weigertopdrachten | Nee | Nee |
> | vindorphanroleassignments | Nee | Nee |
> | Sloten | Nee | Nee |
> | Machtigingen | Nee | Nee |
> | beleidstoewijzingen | Nee | Nee |
> | beleidsdefinities | Nee | Nee |
> | policysetdefinities | Nee | Nee |
> | rollentoewijzingen | Nee | Nee |
> | roleassignmentsusagemetrics | Nee | Nee |
> | roldefinities | Nee | Nee |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | automatiseringsrekeningen | Ja | Ja |
> | automatiseringsaccounts / configuraties | Ja | Ja |
> | automationaccounts / runbooks | Ja | Ja |

> [!IMPORTANT]
> Runbooks moeten in dezelfde resourcegroep bestaan als het automatiseringsaccount.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Ja | Ja |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | Nee | Nee |
> | postgresinstances | Nee | Nee |
> | sqlinstances | Nee | Nee |
> | sqlserverregistraties | Ja | Ja |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Registraties | Ja | Ja |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ja | Ja |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Clusters | Nee | Nee |
> | bestandsservers | Nee | Nee |
> | Banen | Nee | Nee |
> | werkruimten | Nee | Nee |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | factureringsperioden | Nee | Nee |
> | factureringsmachtigingen | Nee | Nee |
> | facturatietoewijzingen | Nee | Nee |
> | factureringsroldefinities | Nee | Nee |
> | toewijzing van factureringsrollen maken | Nee | Nee |

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
> | blockchainleden | Nee | Nee |
> | cordamembers | Nee | Nee |
> | Watchers | Nee | Nee |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | tokenservices | Nee | Nee |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | blauwdrukopdrachten | Nee | Nee |
> | Blauwdrukken | Nee | Nee |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | botservices | Ja | Ja |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Redis | Ja | Ja |

> [!IMPORTANT]
> Als de instantie Azure Cache voor Redis is geconfigureerd met een virtueel netwerk, kan de instantie niet worden verplaatst naar een ander abonnement. Zie [Beperkingen voor het verplaatsen van netwerken](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn (Microsoft.Cdn)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallbeleid | Ja | Ja |
> | Profielen | Ja | Ja |
> | profielen / eindpunten | Ja | Ja |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | certificaatorders | Ja | Ja |

> [!IMPORTANT]
> Zie [Richtlijnen voor verplaatsen van app-service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Domeinnamen | Ja | Nee |
> | virtuele machines | Ja | Nee |

> [!IMPORTANT]
> Zie [Richtlijnen voor het verplaatsen van klassieke implementatie .](./move-limitations/classic-model-move-limitations.md) Klassieke implementatiebronnen kunnen worden verplaatst over abonnementen met een bewerking die specifiek is voor dat scenario.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Nee | Nee |
> | gereserveerde ips | Nee | Nee |
> | virtuele netwerken | Nee | Nee |

> [!IMPORTANT]
> Zie [Richtlijnen voor het verplaatsen van klassieke implementatie .](./move-limitations/classic-model-move-limitations.md) Klassieke implementatiebronnen kunnen worden verplaatst over abonnementen met een bewerking die specifiek is voor dat scenario.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | opslagrekeningen | Ja | Nee |

> [!IMPORTANT]
> Zie [Richtlijnen voor het verplaatsen van klassieke implementatie .](./move-limitations/classic-model-move-limitations.md) Klassieke implementatiebronnen kunnen worden verplaatst over abonnementen met een bewerking die specifiek is voor dat scenario.

## <a name="microsoftcognition"></a>Microsoft.Cognition

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | syntheticsaccounts | Nee | Nee |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | beschikbaarheidssets | Ja | Ja |
> | diskencryptiesets | Nee | Nee |
> | Schijven | Ja | Ja |
> | Galeries | Nee | Nee |
> | galerijen / afbeeldingen | Nee | Nee |
> | galerijen / afbeeldingen / versies | Nee | Nee |
> | hostgroepen | Nee | Nee |
> | hostgroups / hosts | Nee | Nee |
> | images | Ja | Ja |
> | proximityplacementgroepen | Ja | Ja |
> | restorepointcollections | Nee | Nee |
> | sharedvm-extensies | Nee | Nee |
> | sharedvmimages | Nee | Nee |
> | sharedvmimages / versies | Nee | Nee |
> | momentopnamen | Ja | Ja |
> | sshpublickeys sshpublickeys sshpublickeys ssh | Nee | Nee |
> | virtuele machines | Ja | Ja |
> | virtualmachines / extensies | Ja | Ja |
> | virtuelemachinescalesets | Ja | Ja |

> [!IMPORTANT]
> Zie [Virtuele machines verplaatsen begeleiding](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft.Verbruik

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | geaggregeerde kosten | Nee | Nee |
> | Saldi | Nee | Nee |
> | Begrotingen | Nee | Nee |
> | Kosten | Nee | Nee |
> | costtags | Nee | Nee |
> | Credits | Nee | Nee |
> | events | Nee | Nee |
> | Prognoses | Nee | Nee |
> | Veel | Nee | Nee |
> | Markten | Nee | Nee |
> | operationeel resultaat | Nee | Nee |
> | werkingsstatus | Nee | Nee |
> | prijslijsten | Nee | Nee |
> | Producten | Nee | Nee |
> | reserveringsgegevens | Nee | Nee |
> | reserveringsaanbevelingen | Nee | Nee |
> | reserveringssamenvattingen | Nee | Nee |
> | reserveringstransacties | Nee | Nee |
> | tags | Nee | Nee |
> | Huurders | Nee | Nee |
> | Voorwaarden | Nee | Nee |
> | gebruiksdetails | Nee | Nee |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | containergroepen | Nee | Nee |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | containergroepen | Nee | Nee |
> | serviceassociationlinks | Nee | Nee |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Registers | Ja | Ja |
> | registers / agentpools | Nee | Nee |
> | registers / buildtaken | Ja | Ja |
> | registers / replicaties | Ja | Ja |
> | registers / taakruns | Ja | Ja |
> | registers / taken | Ja | Ja |
> | registers / webhooks | Ja | Ja |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | containerdiensten | Nee | Nee |
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
> | Begrotingen | Nee | Nee |
> | connectoren | Ja | Ja |
> | Dimensies | Nee | Nee |
> | Export | Nee | Nee |
> | extern abonnementen | Nee | Nee |
> | forecast | Nee | Nee |
> | query | Nee | Nee |
> | reportconfigs | Nee | Nee |
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
> | Verenigingen | Nee | Nee |
> | resourceproviders | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Banen | Nee | Nee |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | databoxedgeapparaten | Nee | Nee |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | werkruimten | Nee | Nee |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Catalogi | Ja | Ja |
> | gegevenscatalogi | Nee | Nee |

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
> | Plannen | Nee | Nee |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | datafabrieken | Ja | Ja |
> | Fabrieken | Ja | Ja |

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

## <a name="microsoftdatamigration"></a>Microsoft.DataMigratie

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | services | Nee | Nee |
> | diensten / projecten | Nee | Nee |
> | Slots | Nee | Nee |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | backupvaults | Nee | Nee |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Servers | Ja | Ja |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Servers | Ja | Ja |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | servergroepen | Nee | Nee |
> | Servers | Ja | Ja |
> | serversv2 | Ja | Ja |
> | singleservers | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | artefactenbronnen | Ja | Ja |
> | implementaties | Ja | Ja |
> | servicetopologieën | Ja | Ja |
> | servicetopologieën / diensten | Ja | Ja |
> | servicetopologieën / diensten / serviceunits | Ja | Ja |
> | stappen | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualisatie

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | toepassingsgroepen | Nee | Nee |
> | hostpools | Nee | Nee |
> | werkruimten | Nee | Nee |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | elastische pools | Nee | Nee |
> | elasticpools / iothubtenants | Nee | Nee |
> | iothubs | Ja | Ja |
> | dienstverlening | Ja | Ja |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Pijpleidingen | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Controllers | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | labcenters | Nee | Nee |
> | Labs | Ja | Nee |
> | labs / omgevingen | Ja | Ja |
> | labs / servicerunners | Ja | Ja |
> | labs / virtualmachines | Ja | Nee |
> | Planningen | Ja | Ja |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

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
> | Domeinen | Ja | Ja |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Domeinen | Ja | Ja |
> | eventAbonnementen | Nee - kan niet onafhankelijk worden verplaatst, maar automatisch verplaatst met geabonneerde resource. | Nee - kan niet onafhankelijk worden verplaatst, maar automatisch verplaatst met geabonneerde resource. |
> | eventabonnementen | Nee - kan niet onafhankelijk worden verplaatst, maar automatisch verplaatst met geabonneerde resource. | Nee - kan niet onafhankelijk worden verplaatst, maar automatisch verplaatst met geabonneerde resource. |
> | extensieonderwerpen | Nee | Nee |
> | partnernaamruimten | Ja | Ja |
> | partnerregistraties | Nee | Nee |
> | partneronderwerpen | Ja | Ja |
> | systeemonderwerpen | Ja | Ja |
> | Onderwerpen | Ja | Ja |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Clusters | Ja | Ja |
> | Naamruimten | Ja | Ja |

## <a name="microsoftexperimentation"></a>Microsoft.Experimenteren

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | experimentwerkruimten | Nee | Nee |

## <a name="microsoftfalcon"></a>Microsoft.Falcon (Microsoft.Falcon)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Naamruimten | Ja | Ja |

## <a name="microsoftgenomics"></a>Microsoft.Genomics (Microsoft.Genomics)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | automatisch beheerde accounts | Nee | Nee |
> | automatisch beheerde vmconfiguratieprofielen | Nee | Nee |
> | gastconfiguratiestoewijzingen | Nee | Nee |
> | Software | Nee | Nee |
> | softwareupdateprofiel | Nee | Nee |
> | software-updates | Nee | Nee |

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
> | Clusters | Ja | Ja |

> [!IMPORTANT]
> U HDInsight-clusters verplaatsen naar een nieuw abonnement of resourcegroep. U echter niet over abonnementen heen de netwerkbronnen verplaatsen die zijn gekoppeld aan het HDInsight-cluster (zoals het virtuele netwerk, NIC of de load balancer). Bovendien u niet naar een nieuwe resourcegroep gaan, een NIC die is gekoppeld aan een virtuele machine voor het cluster.
>
> Wanneer u een HDInsight-cluster naar een nieuw abonnement verplaatst, verplaatst u eerst andere bronnen (zoals het opslagaccount). Verplaats vervolgens het HDInsight-cluster vanzelf.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | services | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Machines | Ja | Ja |
> | machines / extensies | Nee | Nee |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | datamanagers | Ja | Ja |

## <a name="microsofthydra"></a>Microsoft.Hydra (Microsoft.Hydra)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Onderdelen | Nee | Nee |
> | netwerkscopen | Nee | Nee |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Banen | Ja | Ja |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | actiegroepen | Ja | Ja |
> | activitylogalerts | Nee | Nee |
> | waarschuwingsregels | Ja | Ja |
> | instellingen voor automatisch schalen | Ja | Ja |
> | Basislijn | Nee | Nee |
> | calculatebaseline calculatebaseline | Nee | Nee |
> | Onderdelen | Ja | Ja |
> | regels voor gegevensverzameling | Nee | Nee |
> | diagnostische instellingen | Nee | Nee |
> | diagnostische instellingencategorieën | Nee | Nee |
> | eventtypes | Nee | Nee |
> | extendeddiagnosticsettings extendeddiagnosticsettings extendeddiagnosticsettings extended | Nee | Nee |
> | gastdiagnoseinstellingen | Nee | Nee |
> | logdefinities | Nee | Nee |
> | logboeken | Nee | Nee |
> | metrische waarschuwingen | Nee | Nee |
> | metrische basislijnen | Nee | Nee |
> | metrische definities | Nee | Nee |
> | metrische naamruimten | Nee | Nee |
> | metrics | Nee | Nee |
> | myworkbooks | Nee | Nee |
> | meldingsgroepen | Nee | Nee |
> | privatelinkscopes | Ja | Ja |
> | geplande queryregels | Ja | Ja |
> | topologie | Nee | Nee |
> | transacties | Nee | Nee |
> | vminsightsonboardingstatussen | Nee | Nee |
> | webtests | Ja | Ja |
> | werkmappen | Ja | Ja |
> | werkmapsjablonen | Ja | Ja |

> [!IMPORTANT]
> Zorg ervoor dat de overstap naar een nieuw abonnement de [abonnementsquota](azure-subscription-service-limits.md#azure-monitor-limits)niet overschrijdt.

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | iotapps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.ioTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | beschikbaarheid van checkname | Ja | Ja |
> | Grafiek | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hsmpools | Nee | Nee |
> | Kluizen | Ja | Ja |

> [!IMPORTANT]
> Key Vaults die worden gebruikt voor schijfversleuteling, kunnen niet worden verplaatst naar een resourcegroep in hetzelfde abonnement of tussen abonnementen.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes (Microsoft.Kubernetes)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Nee | Nee |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Clusters | Ja | Ja |

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
> | hostingomgevingen | Nee | Nee |
> | integratierekeningen | Ja | Ja |
> | integratieserviceomgevingen | Ja | Nee |
> | integrationserviceomgevingen / managedapis | Ja | Nee |
> | geïsoleerde omgevingen | Nee | Nee |
> | Werkstromen | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | toezeggingsplannen | Nee | Nee |
> | Webservices | Ja | Nee |
> | werkruimten | Ja | Ja |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | operationalisatieclusters | Nee | Nee |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimenteren

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |
> | accounts / werkruimten | Nee | Nee |
> | accounts / werkplekken / projecten | Nee | Nee |
> | teamaccounts | Nee | Nee |
> | teamaccounts / werkruimten | Nee | Nee |
> | teamaccounts / werkplekken / projecten | Nee | Nee |

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
> | werkruimten / berekent | Nee | Nee |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | updates toepassen | Nee | Nee |
> | configuratiestoewijzingen | Nee | Nee |
> | onderhoudsconfiguraties | Ja | Ja |
> | updates | Nee | Nee |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Identiteiten | Nee | Nee |
> | userassignedidentities | Nee | Nee |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | managednetworks | Nee | Nee |
> | managednetworks / managednetworkgroups | Nee | Nee |
> | managednetworks / managednetworkpeering policies managednetworks | Nee | Nee |
> | melding | Nee | Nee |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | registratieopdrachten | Nee | Nee |
> | registratiedefinities | Nee | Nee |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |
> | accounts / privateatlassen | Ja | Ja |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nee | Nee |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | mediaservices | Ja | Ja |
> | mediaservices / liveevents | Ja | Ja |
> | mediaservices / streamingendpoints | Ja | Ja |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | appclusters | Nee | Nee |

## <a name="microsoftmigrate"></a>Microsoft.Migreren

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | beoordelingsprojecten | Ja | Ja |
> | projecten migreren | Ja | Ja |
> | verhuiscollecties | Nee | Nee |
> | Projecten | Nee | Nee |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Nee | Nee |
> | netappaccounts / back-upbeleid | Nee | Nee |
> | netappaccounts / capaciteitspools | Nee | Nee |
> | netappaccounts / capaciteitspools / volumes | Nee | Nee |
> | netappaccounts / capaciteitspools / volumes / mounttargets | Nee | Nee |
> | netappaccounts / capaciteitspools / volumes / momentopnamen | Nee | Nee |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Nee | Nee |
> | applicationgatewaywebapplicationfirewallbeleid | Nee | Nee |
> | applicationsecuritygroups | Ja | Ja |
> | azurefirewalls | Ja | Ja |
> | bastionhosts | Nee | Nee |
> | Verbindingen | Ja | Ja |
> | ddoscustompolicies | Ja | Ja |
> | ddosprotectionplans | Nee | Nee |
> | dnszones | Ja | Ja |
> | expressroutecircuits | Nee | Nee |
> | expressroutegateways | Nee | Nee |
> | firewallbeleid | Ja | Ja |
> | voordeuren | Nee | Nee |
> | frontdoorwebapplicationfirewallbeleid | Nee | Nee |
> | ipgroepen | Ja | Ja |
> | loadbalancers | Ja - Basis SKU<br>Nee - Standaard SKU | Ja - Basis SKU<br>Nee - Standaard SKU |
> | localnetworkgateways | Ja | Ja |
> | natgateways | Ja | Ja |
> | netwerkexperimentprofielen | Ja | Ja |
> | netwerkintentpolicies | Ja | Ja |
> | netwerkinterfaces | Ja | Ja |
> | netwerkprofielen | Nee | Nee |
> | networksecuritygroups | Ja | Ja |
> | netwerkwatchers | Ja | Nee |
> | networkwatchers / connectionmonitors | Ja | Nee |
> | networkwatchers / flowlogs | Ja | Nee |
> | networkwatchers / pingmeshes | Ja | Nee |
> | p2svpngateways | Nee | Nee |
> | privatednszones | Ja | Ja |
> | privatednszones / virtualnetworklinks | Ja | Ja |
> | privateendpointredirectmaps | Nee | Nee |
> | privateendpoints | Ja | Ja |
> | privatelinkdiensten | Nee | Nee |
> | openbare ipadressen | Ja - Basis SKU<br>Nee - Standaard SKU | Ja - Basis SKU<br>Nee - Standaard SKU |
> | publicipprefixes | Ja | Ja |
> | routefilters | Nee | Nee |
> | routetabellen | Ja | Ja |
> | serviceendpointbeleid | Ja | Ja |
> | trafficmanagerprofielen | Ja | Ja |
> | virtuele hubs | Nee | Nee |
> | virtualnetworkgateways | Ja | Ja |
> | virtuele netwerken | Ja | Ja |
> | virtuele netwerkkranen | Nee | Nee |
> | virtualrouters | Ja | Ja |
> | virtualwans virtualwans | Nee | Nee |
> | vpngateways (Virtual WAN) | Nee | Nee |
> | vpnserverconfiguraties | Nee | Nee |
> | vpnsites (Virtual WAN) | Nee | Nee |
> | webapplicationfirewallbeleid | Ja | Ja |

> [!IMPORTANT]
> Zie [Richtlijnen voor netwerkverplaatsingen](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Naamruimten | Ja | Ja |
> | naamruimten / notificationhubs | Ja | Ja |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | geschreven ruimten voor auto's | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Clusters | Nee | Nee |
> | storageinsightconfigs | Nee | Nee |
> | werkruimten | Ja | Ja |

> [!IMPORTANT]
> Zorg ervoor dat de overstap naar een nieuw abonnement de [abonnementsquota](azure-subscription-service-limits.md#azure-monitor-limits)niet overschrijdt.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | beheersverenigingen | Nee | Nee |
> | beheerconfiguraties | Ja | Ja |
> | oplossingen | Ja | Ja |
> | Weergaven | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | peerings | Ja | Ja |
> | peeringservices | Nee | Nee |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | beleidsgebeurtenissen | Nee | Nee |
> | beleidsstaten | Nee | Nee |
> | policytrackedresources | Nee | Nee |
> | herstelacties | Nee | Nee |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | dashboards | Ja | Ja |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | wortelbronnen | Nee | Nee |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | werkruimtecollecties | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Capaciteiten | Ja | Ja |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon (Microsoft.ProjectBabylon)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Nee | Nee |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | implementaties | Nee | Nee |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | werkruimten | Nee | Nee |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Nee | Nee |
> | resultaten voor replicatiegeschiktheid | Nee | Nee |
> | Kluizen | Ja | Ja |

> [!IMPORTANT]
> Zie [Richtlijnen voor het verplaatsen van herstelservices](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | Nee | Nee |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Naamruimten | Ja | Ja |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Query 's | Ja | Ja |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | beschikbaarheidsstatussen | Nee | Nee |
> | beschikbaarheidvan kinderen | Nee | Nee |
> | kinderbronnen | Nee | Nee |
> | events | Nee | Nee |
> | meldingen | Nee | Nee |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | implementatiescripts | Nee | Nee |
> | Verwijzigingen | Nee | Nee |
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
> | zoekservices | Ja | Ja |

> [!IMPORTANT]
> U verschillende zoekbronnen in verschillende regio's niet in één bewerking verplaatsen. Verplaats ze in plaats daarvan in afzonderlijke bewerkingen.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Nee | Nee |
> | advancedthreatprotectionsettings | Nee | Nee |
> | beoordeling metadata | Nee | Nee |
> | evaluaties | Nee | Nee |
> | automatiseringen | Ja | Ja |
> | nalevingsresultaten | Nee | Nee |
> | nalevingen | Nee | Nee |
> | gegevensverzamelingsmiddelen | Nee | Nee |
> | apparaatbeveiligingsgroepen | Nee | Nee |
> | informatiebeschermingsbeleid | Nee | Nee |
> | iotsecuritysolutions | Ja | Ja |
> | beoordeling van serverkwetsbaarheid | Nee | Nee |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | aggregaties | Nee | Nee |
> | waarschuwingsregels | Nee | Nee |
> | waarschuwingsregelsjablonen | Nee | Nee |
> | bladwijzers | Nee | Nee |
> | Gevallen | Nee | Nee |
> | gegevensconnectoren | Nee | Nee |
> | dataconnectorscheckvereisten | Nee | Nee |
> | Entiteiten | Nee | Nee |
> | entiteiten | Nee | Nee |
> | Incidenten | Nee | Nee |
> | officeconsents | Nee | Nee |
> | instellingen | Nee | Nee |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Gateways | Nee | Nee |
> | Knooppunten | Nee | Nee |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Naamruimten | Ja | Ja |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | toepassingen | Nee | Nee |
> | Clusters | Ja | Ja |
> | clusters / toepassingen | Nee | Nee |
> | containergroepen | Nee | Nee |
> | containergroepsets | Nee | Nee |
> | edgeclusters | Nee | Nee |
> | managedclusters | Nee | Nee |
> | Netwerken | Nee | Nee |
> | secretstores | Nee | Nee |
> | volumes | Nee | Nee |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | toepassingen | Ja | Ja |
> | containergroepen | Nee | Nee |
> | Gateways | Ja | Ja |
> | Netwerken | Ja | Ja |
> | geheimen | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | implementaties | Nee | Nee |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | signaalgever | Ja | Ja |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | hybridegebruik | Nee | Nee |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | toepassingsdefinities | Nee | Nee |
> | toepassingen | Nee | Nee |
> | jitrequests | Nee | Nee |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | instantiepools | Nee | Nee |
> | managedinstances | Nee | Nee |
> | managedinstances / databases | Nee | Nee |
> | Servers | Ja | Ja |
> | servers / databases | Ja | Ja |
> | servers / elastische pools | Ja | Ja |
> | servers / jobaccounts | Ja | Ja |
> | servers / jobagents | Ja | Ja |
> | virtuele clusters | Ja | Ja |

> [!IMPORTANT]
> Een database en server moeten zich in dezelfde brongroep bevinden. Wanneer u een SQL-server verplaatst, worden ook alle databases verplaatst. Dit gedrag is van toepassing op Azure SQL Database en Azure SQL Data Warehouse-databases.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroepen | Ja | Ja |
> | sqlvirtualmachines | Ja | Ja |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM (Microsoft.SqlVM)

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | dwvm | Nee | Nee |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | opslagrekeningen | Ja | Ja |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Caches | Nee | Nee |

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
> | Managers | Nee | Nee |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Ja | Ja |

> [!IMPORTANT]
> Stream Analytics-taken kunnen niet worden verplaatst wanneer ze in de bedrijfsstatus zijn.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Omgevingen | Nee | Nee |
> | omgevingen / eventsources | Nee | Nee |
> | Exemplaren | Nee | Nee |
> | instanties / omgevingen | Nee | Nee |
> | instanties / omgevingen / eventsources | Nee | Nee |

## <a name="microsoftsubscription"></a>Microsoft.Abonnement

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | makenabonnement | Nee | Nee |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | supporttickets | Nee | Nee |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | werkruimten | Nee | Nee |
> | werkruimten / bigdatapools | Nee | Nee |
> | werkruimten / sqlpools | Nee | Nee |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | providerregistraties | Nee | Nee |
> | resources | Nee | Nee |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Omgevingen | Ja | Ja |
> | omgevingen / eventsources | Ja | Ja |
> | omgevingen / referentiedatasets | Ja | Ja |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Winkels | Ja | Ja |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | afbeeldingssjablonen | Nee | Nee |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | account | Nee | Nee |
> | account / extensie | Ja | Ja |
> | account / project | Ja | Ja |

> [!IMPORTANT]
> Zie [Het Azure-abonnement dat wordt gebruikt voor facturering wijzigen](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)als u het abonnement voor Azure DevOps wilt wijzigen.

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Nee | Nee |
> | dedicatedcloudservices | Nee | Nee |
> | virtuele machines | Nee | Nee |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | apparaten | Nee | Nee |
> | vnfs | Nee | Nee |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Ja | Ja |
> | Plannen | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | certificaten | Nee | Ja |
> | verbindingsgateways | Ja | Ja |
> | Verbindingen | Ja | Ja |
> | customapis | Ja | Ja |
> | hostingomgevingen | Nee | Nee |
> | kubeomgevingen | Ja | Ja |
> | serverfarms | Ja | Ja |
> | sites | Ja | Ja |
> | sites / premieraddons | Ja | Ja |
> | sites / slots | Ja | Ja |
> | statische sites | Nee | Nee |

> [!IMPORTANT]
> Zie [Richtlijnen voor verplaatsen van app-service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | meerdere activeringssleutels | Nee | Nee |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | apparaatservices | Nee | Nee |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resourcetype | Resourcegroep | Abonnement |
> | ------------- | ----------- | ---------- |
> | Onderdelen | Nee | Nee |
> | monitorinstanties | Nee | Nee |
> | Monitoren | Nee | Nee |
> | meldingsinstellingen | Nee | Nee |

## <a name="third-party-services"></a>Diensten van derden

Services van derden ondersteunen momenteel de verplaatsingsbewerking niet.

## <a name="next-steps"></a>Volgende stappen
Zie Resources verplaatsen naar [een nieuwe resourcegroep of -abonnement](move-resource-group-and-subscription.md)voor opdrachten om resources te verplaatsen.

Download [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)om dezelfde gegevens te krijgen als een bestand met door komma's gescheiden waarden.
