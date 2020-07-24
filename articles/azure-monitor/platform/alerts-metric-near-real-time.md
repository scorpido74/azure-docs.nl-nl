---
title: Ondersteunde resources voor metrische waarschuwingen in Azure Monitor
description: Naslag informatie over metrische gegevens en logboeken voor metrische waarschuwingen in Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: bc3b11cd09c1c987a74ad07b12117e62b065a6de
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87045409"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Ondersteunde resources voor metrische waarschuwingen in Azure Monitor

Azure Monitor ondersteunt nu een [nieuw type metrische waarschuwing](../../azure-monitor/platform/alerts-overview.md) met aanzienlijke voor delen ten opzichte van de oudere [klassieke metrische waarschuwingen](../../azure-monitor/platform/alerts-classic.overview.md). Metrische gegevens zijn beschikbaar voor een [grote lijst met Azure-Services](../../azure-monitor/platform/metrics-supported.md). De nieuwere waarschuwingen bieden ondersteuning voor een deel verzameling van de resource typen. Dit artikel bevat een lijst met deze subset.

U kunt ook nieuwere metrische waarschuwingen gebruiken voor populaire logboek gegevens die zijn opgeslagen in een Log Analytics werk ruimte geëxtraheerd als metrische waarden. Bekijk [metrische waarschuwingen voor logboeken](../../azure-monitor/platform/alerts-metric-logs.md)voor meer informatie.

## <a name="portal-powershell-cli-rest-support"></a>Portal, Power shell, CLI, REST-ondersteuning
Op dit moment kunt u alleen nieuwe metrische waarschuwingen maken in de sjablonen Azure Portal, [rest API](/rest/api/monitor/metricalerts/)of [Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md). Ondersteuning voor het configureren van nieuwere waarschuwingen met behulp van Power shell en Azure CLI versie 2,0 en hoger wordt binnenkort beschikbaar.

## <a name="metrics-and-dimensions-supported"></a>Ondersteunde metrische gegevens en dimensies
Nieuwere metrische waarschuwingen ondersteunen waarschuwingen voor metrische gegevens die gebruikmaken van dimensies. U kunt dimensies gebruiken om uw metrische gegevens te filteren op het juiste niveau. Alle ondersteunde metrische gegevens en de toepasselijke dimensies kunnen worden verkend en gevisualiseerd vanaf [Azure monitor-Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

Hier volgt de volledige lijst met metrische gegevens bronnen van Azure monitor die worden ondersteund door de nieuwere waarschuwingen:

|Resourcetype  |Ondersteunde dimensies |Waarschuwingen voor meerdere resources| Beschik bare metrische gegevens|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Yes| Nee | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Micro soft. AppPlatform/lente |Nee| Yes|
|Micro soft. Automation/automationAccounts | Yes| Nee | [Automation-accounts](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Bat-CH/batchAccounts | N.v.t.| No | [Batchaccounts](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|Yes| Nee |[Azure Cache voor Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Micro soft. ClassicStorage/Storage accounts/mmxclassic|Nee|Yes|
|Micro soft. ClassicStorage/Storage accounts/mmxclassic/blobServices|Nee|Yes|
|Micro soft. ClassicStorage/Storage accounts/mmxclassic/fileServices|Nee|Yes|
|Micro soft. ClassicStorage/Storage accounts/mmxclassic/queueServices|Nee|Yes|
|Micro soft. ClassicStorage/Storage accounts/mmxclassic/tableServices|Nee|Yes| |
|Micro soft. CognitiveServices/accounts| N.v.t. | No | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Ja | Ja | [Virtual Machines](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |N.v.t. | Yes |[Schaal sets voor virtuele machines](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Micro soft. ContainerInstance/containerGroups | Yes| Nee | [Containergroepen](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Micro soft. container service/managedClusters | Yes | Nee | [Beheerde clusters](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Micro soft. DataBoxEdge/dataBoxEdgeDevices | Ja | Ja | |
|Micro soft. DataFactory/datafactories| Yes| Nee | [Gegevens fabrieken v1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Micro soft. DataFactory/fabrieken |Yes | Nee |[Gegevens fabrieken v2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Micro soft. DataShare/accounts |Nee| Yes|
|Microsoft.DBforMySQL/servers |N.v.t.| No |[DB voor MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |N.v.t. | No | [DB voor PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Micro soft. devices/IotHubs | N.v.t. | No |[Metrische gegevens van IoT Hub](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Micro soft. devices/provisioningServices| Ja | No |[Metrische gegevens DPS](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Micro soft. EventGrid/domeinen|No|Ja| |
|Micro soft. EventGrid/topics |Ja | No |[Event Grid-onderwerpen](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Micro soft. EventHub/clusters |Ja| No |[Event Hubs clusters](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namespaces |Ja| No |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Micro soft.-sleutel kluis/-kluizen| Nee |Nee |[Kluizen](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Micro soft. Logic/werk stromen |N.v.t. | No |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Micro soft. MachineLearningServices/werk ruimten|Ja| No | [Machine Learning](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Micro soft. NetApp/netAppAccounts/capacityPools |Ja| No | [Azure NetApp-capaciteits Pools](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Micro soft. NetApp/netAppAccounts/capacityPools/volumes |Ja| No | [Azure NetApp-volumes](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Micro soft. Network/applicationGateways|N.v.t.| No |  |
|Micro soft. Network/dnsZones | N.v.t.| No | [DNS-zones](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Micro soft. Network/expressRouteCircuits | N.v.t. | No |[Express Route-circuits](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Micro soft. Network/loadBalancers (alleen voor standaard-Sku's)| Ja| No | [Load balancers](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Micro soft. Network/natGateways|No|Ja|
|Micro soft. Network/privateEndpoints|No|Ja|
|Micro soft. Network/privateLinkServices|No|Ja|
|Micro soft. Network/publicipaddresses |N.v.t. | No |[Openbare IP-adressen](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Micro soft. Network/trafficManagerProfiles | Ja | No | [Traffic Manager-profielen](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Ja | No | [Log Analytics-werkruimten](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Micro soft. relay/naam ruimten | Ja | No | [Relays](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Micro soft. peering/peeringServices|No|Ja|
|Micro soft. PowerBIDedicated/capaciteiten | N.v.t. | No | [Capaciteiten](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Micro soft. Search/searchServices |N.v.t.|No | [Services zoeken](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |Ja| No |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Micro soft. SQL/servers/elasticPools |    No | Ja |
|Microsoft.Sql/servers/databases    | No | Ja |
|Microsoft.Storage/storageAccounts |Ja | Nee | [Opslag accounts](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Micro soft. Storage/Storage accounts/Services | Ja| Nee | [BLOB Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Bestands Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [wachtrij Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) en [tabel Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Micro soft. StreamAnalytics/streamingjobs |N.v.t.| No | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Micro soft. micro soft. VMWareCloudSimple/informatie |Ja|Nee |[Virtuele CloudSimple-machines](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Micro soft. Web/hostingEnvironments/multiRolePools | Ja | Nee | [Groepen met meerdere rollen App Service Environment](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Micro soft. Web/hostingEnvironments/workerPools | Ja | Nee | [Werk groepen App Service Environment](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Micro soft. web/server farms | Ja | Nee | [App Service plannen](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Micro soft. web/sites | Ja | Nee | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) en [functions](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Micro soft. web/sites/sleuven | Ja | Nee | [App Service sleuven](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

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
