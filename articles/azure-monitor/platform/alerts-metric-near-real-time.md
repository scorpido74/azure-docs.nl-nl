---
title: Ondersteunde resources voor metrische waarschuwingen in Azure Monitor
description: Naslag informatie over metrische gegevens en logboeken voor metrische waarschuwingen in Azure Monitor
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 37ef1cda37e1799a4dc488947e6c9ed4c9ad4055
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636175"
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
|Microsoft.Automation/automationAccounts     |     Ja   | [Automation-accounts](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/A| [Batch-accounts](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    N/A     |[Azure Cache voor Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    N/A     | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    N/A     | [Virtuele machines](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   N/A      |[Schaal sets voor virtuele machines](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Ja| [Container groepen](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Ja | [Beheerde clusters](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| Ja| [Gegevens fabrieken v1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Ja     |[Gegevens fabrieken v2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   N/A      |[DB voor MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    N/A     | [DB voor PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs    | N/A     |[IoT Hub metrische gegevens](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)
|Microsoft.Devices/provisioningServices    | Ja     |[Metrische gegevens DPS](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)
|Microsoft.EventHub/namespaces     |  Ja      |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Nee | [Kluizen](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     N/A    |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    N/A     | [Toepassingsgateways](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones | N/A| [DNS-zones](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | N/A |  [Express route-circuits](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Micro soft. Network/loadBalancers (alleen voor standaard-Sku's)| Ja| [Load balancers](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  N/A       |[Openbare IP-adressen](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Ja | [Traffic Manager profielen](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Ja|[Log Analytics-werk ruimten](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Micro soft. PowerBIDedicated/capaciteiten | N/A | [Beschikt](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   N/A      |[Services zoeken](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Ja       |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Ja     | [Opslagaccounts](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Ja    | [BLOB Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Bestands Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [wachtrij Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) en [tabel Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  N/A       | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | Ja | [App Service plannen](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)  |
| Microsoft.Web/sites | Ja | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) en [functions](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
| Microsoft.Web/sites/slots | Ja | [App Service sleuven](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|


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
