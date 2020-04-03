---
title: Ondersteunde resources voor metrische waarschuwingen in Azure Monitor
description: Referentie voor ondersteuningsstatistieken en logboeken voor metrische waarschuwingen in Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: c036fa3708d718d6199d27989e60b11015a1227e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585860"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Ondersteunde resources voor metrische waarschuwingen in Azure Monitor

Azure Monitor ondersteunt nu een [nieuw metrische waarschuwingstype](../../azure-monitor/platform/alerts-overview.md) dat aanzienlijke voordelen heeft ten opzichte van de oudere [klassieke metrische waarschuwingen.](../../azure-monitor/platform/alerts-classic.overview.md) Statistieken zijn beschikbaar voor [grote lijst met Azure-services.](../../azure-monitor/platform/metrics-supported.md) De nieuwere waarschuwingen ondersteunen een (groeiende) subset van de resourcetypen. In dit artikel wordt die subset vermeld.

U ook nieuwere metrische waarschuwingen gebruiken voor populaire logboekgegevens die zijn opgeslagen in een werkruimte log Analytics die is geëxtraheerd als statistieken. Bekijk [metrische waarschuwingen voor logboeken voor](../../azure-monitor/platform/alerts-metric-logs.md)meer informatie .

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, CLI, REST-ondersteuning
Momenteel u alleen nieuwere metrische waarschuwingen maken in de Azure-portal, [REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/)of [Resource Manager-sjablonen.](../../azure-monitor/platform/alerts-metric-create-templates.md) Ondersteuning voor het configureren van nieuwere waarschuwingen met PowerShell- en Azure CLI-versies 2.0 en hoger komt binnenkort.

## <a name="metrics-and-dimensions-supported"></a>Ondersteunde statistieken en dimensies
Nieuwere metrische waarschuwingen ondersteunen waarschuwingen voor statistieken die dimensies gebruiken. U dimensies gebruiken om uw statistiek op het juiste niveau te filteren. Alle ondersteunde statistieken en de toepasselijke afmetingen kunnen worden verkend en gevisualiseerd vanuit [Azure Monitor - Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

Hier volgt de volledige lijst met azure-monitormetrische bronnen die worden ondersteund door de nieuwere waarschuwingen:

|Resourcetype  |Ondersteunde afmetingen |Waarschuwingen voor meerdere bronnen| Beschikbare statistieken|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Ja| Nee | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.AppPlatform/Spring |Nee| Ja|
|Microsoft.Automation/automationAccounts | Ja| Nee | [Automation-accounts](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N.v.t.| Nee | [Batchaccounts](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|Ja| Nee |[Azure Cache voor Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic|Nee|Ja|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/blobServices|Nee|Ja|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/fileServices|Nee|Ja|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/queueServices|Nee|Ja|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/tableServices|Nee|Ja| |
|Microsoft.CognitiveServices/accounts| N.v.t. | Nee | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Ja | Ja | [Virtuele machines](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |N.v.t. | Ja |[Virtuele-machineschaalsets](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroepen | Ja| Nee | [Containergroepen](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Ja | Nee | [Beheerde clusters](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Ja | Ja | |
|Microsoft.DataFactory/datafabrieken| Ja| Nee | [Data fabrieken V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/fabrieken |Ja | Nee |[Data fabrieken V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DataShare/accounts |Nee| Ja|
|Microsoft.DBforMySQL/servers |N.v.t.| Nee |[DB voor MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |N.v.t. | Nee | [DB voor PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | N.v.t. | Nee |[Metrische gegevens van IoT Hub](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| Ja | Nee |[DPS-statistieken](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft.EventGrid/domeinen|Nee|Ja| |
|Microsoft.EventGrid/onderwerpen |Ja | Nee |[Onderwerpen voor gebeurtenisrasters](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |Ja| Nee |[Clusterclusters voor gebeurtenishubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/naamruimten |Ja| Nee |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/kluizen| Nee |Nee |[Kluizen](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/werkstromen |N.v.t. | Nee |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/werkruimten|Ja| Nee | [Machine Learning](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.NetApp/netAppAccounts/capacityPools |Ja| Nee | [Azure NetApp-capaciteitsgroepen](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes |Ja| Nee | [Azure NetApp-volumes](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways|N.v.t.| Nee |  |
|Microsoft.Network/dnsZones | N.v.t.| Nee | [DNS-zones](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | N.v.t. | Nee |[Express Route-circuits](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (alleen voor standaard SKU's)| Ja| Nee | [Load Balancers](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways|Nee|Ja|
|Microsoft.Network/privateEindpunten|Nee|Ja|
|Microsoft.Network/privateLinkServices|Nee|Ja|
|Microsoft.Network/publicipaddresses |N.v.t. | Nee |[Openbare IP-adressen](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Ja | Nee | [Traffic Manager-profielen](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/werkruimten| Ja | Nee | [Log Analytics-werkruimten](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/naamruimten | Ja | Nee | [Relays](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft.Peering/peeringServices|Nee|Ja|
|Microsoft.PowerBIDedicated/capaciteiten | N.v.t. | Nee | [Capaciteiten](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |N.v.t.|Nee | [Zoekservices](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/naamruimten |Ja| Nee |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |    Nee | Ja |
|Microsoft.Sql/servers/databases    | Nee | Ja |
|Microsoft.Storage/storageAccounts |Ja | Nee | [Opslagaccounts](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | Ja| Nee | [Blob Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Bestandsservices,](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices) [wachtrijservices](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) en [tabelservices](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |N.v.t.| Nee | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.Microsoft.VMWareCloudSimple/virtualMachines |Ja|Nee |[Virtuele CloudSimple-machines](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/hostingOmgevingen/multiRolePools | Ja | Nee | [MultirolePools voor app-serviceomgeving](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingOmgevingen/workerPools | Ja | Nee | [Groep werknemers in de app-serviceomgeving](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Ja | Nee | [App-serviceplannen](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Ja | Nee | [App-services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) en [-functies](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | Ja | Nee | [App-servicesleuven](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Payload-schema

> [!NOTE]
> U ook het [algemene waarschuwingsschema](https://aka.ms/commonAlertSchemaDocs)gebruiken, dat het voordeel biedt dat u één uitbreidbare en uniforme waarschuwingspayload hebt voor alle waarschuwingsservices in Azure Monitor, voor uw webhook-integraties. [Meer informatie over de algemene definities van waarschuwingsschema's.](https://aka.ms/commonAlertSchemaDefinitions)


De bewerking POST bevat de volgende JSON-payload en -schema voor alle near newer metrische waarschuwingen wanneer een correct geconfigureerde [actiegroep](../../azure-monitor/platform/action-groups.md) wordt gebruikt:

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

* Meer informatie over de nieuwe [alerts-ervaring](../../azure-monitor/platform/alerts-overview.md).
* Meer informatie over [logboekwaarschuwingen in Azure](../../azure-monitor/platform/alerts-unified-log.md).
* Meer informatie over [waarschuwingen in Azure](../../azure-monitor/platform/alerts-overview.md).
