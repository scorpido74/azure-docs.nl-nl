---
title: Ondersteunde resources voor metrische waarschuwingen in Azure Monitor
description: Naslag informatie over metrische gegevens en logboeken voor metrische waarschuwingen in Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: e4dfc33756a287b6d2f6e41edded3332ca85241d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363410"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Ondersteunde resources voor metrische waarschuwingen in Azure Monitor

Azure Monitor ondersteunt nu een [nieuw type metrische waarschuwing](../../azure-monitor/platform/alerts-overview.md) met aanzienlijke voor delen ten opzichte van de oudere [klassieke metrische waarschuwingen](../../azure-monitor/platform/alerts-classic.overview.md). Metrische gegevens zijn beschikbaar voor een [grote lijst met Azure-Services](../../azure-monitor/platform/metrics-supported.md). De nieuwere waarschuwingen bieden ondersteuning voor een deel verzameling van de resource typen. Dit artikel bevat een lijst met deze subset.

U kunt ook nieuwere metrische waarschuwingen gebruiken voor populaire logboek gegevens die zijn opgeslagen in een Log Analytics werk ruimte geëxtraheerd als metrische waarden. Bekijk [metrische waarschuwingen voor logboeken](../../azure-monitor/platform/alerts-metric-logs.md)voor meer informatie.

## <a name="portal-powershell-cli-rest-support"></a>Portal, Power shell, CLI, REST-ondersteuning
Op dit moment kunt u alleen nieuwe metrische waarschuwingen maken in de sjablonen Azure Portal, [rest API](https://docs.microsoft.com/rest/api/monitor/metricalerts/)of [Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md). Ondersteuning voor het configureren van nieuwere waarschuwingen met behulp van Power shell en Azure CLI versie 2,0 en hoger wordt binnenkort beschikbaar.

## <a name="metrics-and-dimensions-supported"></a>Ondersteunde metrische gegevens en dimensies
Nieuwere metrische waarschuwingen ondersteunen waarschuwingen voor metrische gegevens die gebruikmaken van dimensies. U kunt dimensies gebruiken om uw metrische gegevens te filteren op het juiste niveau. Alle ondersteunde metrische gegevens en de toepasselijke dimensies kunnen worden verkend en gevisualiseerd vanaf [Azure monitor-Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

Hier volgt de volledige lijst met metrische gegevens bronnen van Azure monitor die worden ondersteund door de nieuwere waarschuwingen:

|Resourcetype  |Ondersteunde dimensies |Waarschuwingen voor meerdere resources| Beschik bare metrische gegevens|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Ja| Nee | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Micro soft. AppPlatform/lente |Nee| Ja|
|Microsoft.Automation/automationAccounts | Ja| Nee | [Automation-accounts](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N.v.t.| Nee | [Batch-accounts](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|Ja| Nee |[Azure Cache voor Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Micro soft. ClassicStorage/Storage accounts/mmxclassic|Nee|Ja|
|Micro soft. ClassicStorage/Storage accounts/mmxclassic/blobServices|Nee|Ja|
|Micro soft. ClassicStorage/Storage accounts/mmxclassic/fileServices|Nee|Ja|
|Micro soft. ClassicStorage/Storage accounts/mmxclassic/queueServices|Nee|Ja|
|Micro soft. ClassicStorage/Storage accounts/mmxclassic/tableServices|Nee|Ja| |
|Microsoft.CognitiveServices/accounts| N.v.t. | Nee | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Ja | Ja | [Virtuele machines](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |N.v.t. | Ja |[Virtuele-machineschaalsets](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Ja| Nee | [Container groepen](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Ja | Nee | [Beheerde clusters](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Ja | Ja | |
|Microsoft.DataFactory/datafactories| Ja| Nee | [Gegevens fabrieken v1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories |Ja | Nee |[Gegevens fabrieken v2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Micro soft. DataShare/accounts |Nee| Ja|
|Microsoft.DBforMySQL/servers |N.v.t.| Nee |[DB voor MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |N.v.t. | Nee | [DB voor PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | N.v.t. | Nee |[IoT Hub metrische gegevens](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| Ja | Nee |[Metrische gegevens DPS](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Micro soft. EventGrid/domeinen|Nee|Ja| |
|Micro soft. EventGrid/topics |Ja | Nee |[Event Grid onderwerpen](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |Ja| Nee |[Event Hubs clusters](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namespaces |Ja| Nee |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Nee |Nee |[Kluizen](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows |N.v.t. | Nee |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces|Ja| Nee | [Machine Learning](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.NetApp/netAppAccounts/capacityPools |Ja| Nee | [Azure NetApp-capaciteits Pools](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Micro soft. NetApp/netAppAccounts/capacityPools/volumes |Ja| Nee | [Azure NetApp-volumes](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways|N.v.t.| Nee |  |
|Microsoft.Network/dnsZones | N.v.t.| Nee | [DNS-zones](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | N.v.t. | Nee |[Express route-circuits](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Micro soft. Network/loadBalancers (alleen voor standaard-Sku's)| Ja| Nee | [Load balancers](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Micro soft. Network/natGateways|Nee|Ja|
|Micro soft. Network/privateEndpoints|Nee|Ja|
|Micro soft. Network/privateLinkServices|Nee|Ja|
|Microsoft.Network/publicipaddresses |N.v.t. | Nee |[Openbare IP-adressen](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Ja | Nee | [Traffic Manager profielen](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Ja | Nee | [Log Analytics-werk ruimten](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Micro soft. relay/naam ruimten | Ja | Nee | [Relays](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Micro soft. peering/peeringServices|Nee|Ja|
|Micro soft. PowerBIDedicated/capaciteiten | N.v.t. | Nee | [Beschikt](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |N.v.t.|Nee | [Services zoeken](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |Ja| Nee |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |   Nee | Ja |
|Microsoft.Sql/servers/databases    | Nee | Ja |
|Microsoft.Storage/storageAccounts |Ja | Nee | [Opslagaccounts](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | Ja| Nee | [BLOB Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Bestands Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [wachtrij Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) en [tabel Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |N.v.t.| Nee | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Micro soft. micro soft. VMWareCloudSimple/informatie |Ja|Nee |[CloudSimple Virtual Machines](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/hostingEnvironments/multiRolePools | Ja | Nee | [Groepen met meerdere rollen App Service Environment](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Ja | Nee | [Werk groepen App Service Environment](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Ja | Nee | [App Service plannen](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Ja | Nee | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) en [functions](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | Ja | Nee | [App Service sleuven](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Payload-schema

> [!NOTE]
> U kunt ook het [schema common alert](https://aka.ms/commonAlertSchemaDocs)gebruiken. Dit biedt het voor deel van het gebruik van een enkele uitbreid bare en Unified payload van waarschuwingen voor alle waarschuwings services in azure monitor voor uw webhook-integraties. [Meer informatie over de algemene schema definities voor waarschuwingen.](https://aka.ms/commonAlertSchemaDefinitions)


De POST-bewerking bevat de volgende JSON-nettolading en het schema voor alle nabije nieuwere metrische waarschuwingen wanneer een passende geconfigureerde [actie groep](../../azure-monitor/platform/action-groups.md) wordt gebruikt:

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de nieuwe [meldings ervaring](../../azure-monitor/platform/alerts-overview.md).
* Meer informatie over [logboek waarschuwingen in azure](../../azure-monitor/platform/alerts-unified-log.md).
* Meer informatie over [waarschuwingen in azure](../../azure-monitor/platform/alerts-overview.md).
