---
title: Ondersteunde resources voor metrische waarschuwingen in Azure Monitor
description: Naslag informatie over metrische gegevens en logboeken voor metrische waarschuwingen in Azure Monitor
author: harelbr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: harelbr
ms.subservice: alerts
ms.openlocfilehash: fe7f7b8fae988a6e3f95b4fbade6296f4e6ac3f6
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162008"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Ondersteunde resources voor metrische waarschuwingen in Azure Monitor

Azure Monitor ondersteunt nu een [nieuw type metrische waarschuwing](../../azure-monitor/platform/alerts-overview.md) met aanzienlijke voor delen ten opzichte van de oudere [klassieke metrische waarschuwingen](../../azure-monitor/platform/alerts-classic.overview.md). Metrische gegevens zijn beschikbaar voor een [grote lijst met Azure-Services](../../azure-monitor/platform/metrics-supported.md). De nieuwere waarschuwingen bieden ondersteuning voor een deel verzameling van de resource typen. Dit artikel bevat een lijst met deze subset.

U kunt ook nieuwere metrische waarschuwingen gebruiken voor populaire logboek gegevens die zijn opgeslagen in een Log Analytics werk ruimte geëxtraheerd als metrische waarden. Bekijk [metrische waarschuwingen voor logboeken](../../azure-monitor/platform/alerts-metric-logs.md)voor meer informatie.

## <a name="portal-powershell-cli-rest-support"></a>Portal, Power shell, CLI, REST-ondersteuning
Op dit moment kunt u alleen nieuwe metrische waarschuwingen maken in de sjablonen Azure Portal, [rest API](https://docs.microsoft.com/rest/api/monitor/metricalerts/)of [Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md). Ondersteuning voor het configureren van nieuwere waarschuwingen met behulp van Power shell en Azure CLI versie 2,0 en hoger wordt binnenkort beschikbaar.

## <a name="metrics-and-dimensions-supported"></a>Ondersteunde metrische gegevens en dimensies
Nieuwere metrische waarschuwingen ondersteunen waarschuwingen voor metrische gegevens die gebruikmaken van dimensies. U kunt dimensies gebruiken om uw metrische gegevens te filteren op het juiste niveau. Alle ondersteunde metrische gegevens en de toepasselijke dimensies kunnen worden verkend en gevisualiseerd vanaf [Azure monitor-Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

Hier volgt de volledige lijst met metrische gegevens bronnen van Azure monitor die worden ondersteund door de nieuwere waarschuwingen:

|Resourcetype  |Ondersteunde dimensies  | Beschik bare metrische gegevens|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Ja        | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Micro soft. Automation/automationAccounts     |     Ja   | [Automation-accounts](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Micro soft. batch/batchAccounts | N/A| [Batch-accounts](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Micro soft. cache/redis     |    Ja     |[Azure Cache voor Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Micro soft. CognitiveServices/accounts     |    N/A     | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    N/A     | [Virtuele machines](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   N/A      |[Virtuele-machineschaalsets](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Micro soft. ContainerInstance/containerGroups | Ja| [Container groepen](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Micro soft. container service/managedClusters | Ja | [Beheerde clusters](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Micro soft. DataFactory/datafactories| Ja| [Gegevens fabrieken v1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Micro soft. DataFactory/fabrieken     |   Ja     |[Gegevens fabrieken v2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Micro soft. DBforMySQL/servers     |   N/A      |[DB voor MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Micro soft. DBforPostgreSQL/servers     |    N/A     | [DB voor PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Micro soft. devices/IotHubs    | N/A     |[IoT Hub metrische gegevens](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Micro soft. devices/provisioningServices    | Ja     |[Metrische gegevens DPS](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Micro soft. EventGrid/topics     |  Ja      |[Event Grid onderwerpen](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Micro soft. EventHub/clusters     |  Ja      |[Event Hubs clusters](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Micro soft. EventHub/naam ruimten     |  Ja      |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Micro soft.-sleutel kluis/-kluizen| Nee | [Kluizen](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Micro soft. Logic/werk stromen     |     N/A    |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Micro soft. MachineLearningServices/werk ruimten     |    Ja     | [Machine Learning](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Micro soft. Network/applicationGateways     |    N/A     | [Toepassingsgateways](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Micro soft. Network/dnsZones | N/A| [DNS-zones](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Micro soft. Network/expressRouteCircuits | N/A |  [Express route-circuits](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Micro soft. Network/loadBalancers (alleen voor standaard-Sku's)| Ja| [Load balancers](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Micro soft. Network/publicipaddresses     |  N/A       |[Openbare IP-adressen](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Micro soft. Network/trafficManagerProfiles | Ja | [Traffic Manager profielen](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Micro soft. OperationalInsights/werk ruimten| Ja | [Log Analytics-werk ruimten](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Micro soft. relay/naam ruimten | Ja | [Relays](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Micro soft. PowerBIDedicated/capaciteiten | N/A | [Beschikt](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Micro soft. Search/searchServices     |   N/A      |[Services zoeken](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Micro soft. ServiceBus/naam ruimten     |  Ja       |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Ja     | [Opslagaccounts](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Micro soft. Storage/Storage accounts/Services     |     Ja    | [BLOB Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Bestands Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [wachtrij Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) en [tabel Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Micro soft. StreamAnalytics/streamingjobs     |  N/A       | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Micro soft. micro soft. VMWareCloudSimple/informatie     |  Ja       | [CloudSimple Virtual Machines](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
| Microsoft.Web/serverfarms | Ja | [App Service plannen](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)  |
| Microsoft.Web/sites | Ja | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) en [functions](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
| Micro soft. web/sites/sleuven | Ja | [App Service sleuven](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|


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
