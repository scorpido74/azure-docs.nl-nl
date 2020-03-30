---
title: Prestatiebewaking met Windows Azure Diagnostics
description: Gebruik Windows Azure Diagnostics om prestatiemeteritems te verzamelen voor uw Azure Service Fabric-clusters.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 0819ca02d088aeb9ada5de1269467f70242bbcca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609907"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Prestatiebewaking met de Windows Azure Diagnostics-extensie

Dit document bevat de stappen die nodig zijn om het verzamelen van prestatiemeteritems in te stellen via de WINDOWS Azure Diagnostics (WAD)-extensie voor Windows-clusters. Voor Linux-clusters stelt u de [loganalytics-agent](service-fabric-diagnostics-oms-agent.md) in om prestatiemeteritems voor uw knooppunten te verzamelen. 

 > [!NOTE]
> De WAD-extensie moet worden geïmplementeerd op uw cluster voor deze stappen om voor u te werken. Als deze niet is ingesteld, gaat u naar [Gebeurtenisaggregatie en verzameling met Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Verzamel prestatiebalies via de WadCfg

Als u prestatiemeteritems wilt verzamelen via WAD, moet u de configuratie op de juiste manier wijzigen in de resourcemanagersjabloon van uw cluster. Volg deze stappen om een prestatiemeter toe te voegen die u aan uw sjabloon wilt verzamelen en voer een resourcemanager-bronupgrade uit.

1. Zoek de WAD-configuratie in de `WadCfg`sjabloon van uw cluster - zoek . U voegt prestatiemeteritems toe `DiagnosticMonitorConfiguration`om te verzamelen onder de .

2. Stel uw configuratie in om prestatiemeteritems te `DiagnosticMonitorConfiguration`verzamelen door de volgende sectie toe te voegen aan uw . 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    De `scheduledTransferPeriod` bepaalt hoe vaak de waarden van de tellers die worden verzameld worden overgebracht naar uw Azure-opslagtabel en naar een geconfigureerde gootsteen. 

3. Voeg de prestatiemeteritems toe die `PerformanceCounterConfiguration` u wilt verzamelen aan de prestatiemeter die in de vorige stap is aangegeven. Elke teller die u wilt verzamelen, `counterSpecifier` `sampleRate`wordt `unit` `annotation`gedefinieerd met `sinks`een , , , en alle relevante .

Hier is een voorbeeld van een configuratie met de teller voor de *totale processortijd* (de hoeveelheid tijd die de CPU gebruikte voor verwerkingen) en *Service Fabric Actor Method Invocations per Seconde*, een van de aangepaste prestatiemeteritems van Service Fabric. Raadpleeg [de prestatiemeteritems voor betrouwbare actorprestaties](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) en [betrouwbare serviceprestatiemeteritems](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) voor een volledige lijst met aangepaste perf-tellers van Service Fabric.

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 De sample rate voor de teller kan worden gewijzigd volgens uw behoeften. Het formaat voor `PT<time><unit>`het is, dus als je wilt dat `"sampleRate": "PT15S"`de teller verzameld elke seconde, dan moet je de .

 U ook variabelen in uw ARM-sjabloon gebruiken om een reeks prestatiemeteritems te verzamelen, wat handig kan zijn wanneer u prestatiemeteritems per proces verzamelt. In het onderstaande voorbeeld verzamelen we processortijd en garbage collector-tijd per proces en vervolgens 2 prestatiemeteritems op de knooppunten zelf, allemaal met behulp van variabelen. 

 ```json
"variables": {
  "copy": [
      {
        "name": "processorTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\Process([parameters('monitoredProcesses')[copyIndex('processorTimeCounters')]])\\% Processor Time",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('processorTimeCounters')],' Processor Time')]",
              "locale": "en-us"
            }
          ]
        }
      },
      {
        "name": "gcTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\.NET CLR Memory([parameters('monitoredProcesses')[copyIndex('gcTimeCounters')]])\\% Time in GC",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('gcTimeCounters')],' Time in GC')]",
              "locale": "en-us"
            }
          ]
        }
      }
    ],
    "machineCounters": [
      {
        "counterSpecifier": "\\Memory\\Available Bytes",
        "sampleRate": "PT1M",
        "unit": "KB",
        "sinks": "applicationInsights",
        "annotation": [
          {
            "displayName": "Memory Available Kb",
            "locale": "en-us"
          }
        ]
      },
      {
        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
        "sampleRate": "PT15S",
        "unit": "percent",
        "annotation": [
          {
            "displayName": "Memory usage",
            "locale": "en-us"
          }
        ]
      }
    ]
  }
....
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": "50000",
      "Metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', variables('vmNodeTypeApp2Name'))]"
      },
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": "[concat(variables ('processorTimeCounters'), variables('gcTimeCounters'),  variables('machineCounters'))]"
      },
....
```

1. Zodra u de juiste prestatiemeteritems hebt toegevoegd die moeten worden verzameld, moet u uw clusterbron upgraden, zodat deze wijzigingen worden weergegeven in uw lopende cluster. Sla uw `template.json` gewijzigde en open PowerShell. U uw `New-AzResourceGroupDeployment`cluster upgraden met behulp van. De aanroep vereist de naam van de resourcegroep, het bijgewerkte sjabloonbestand en het parametersbestand en vraagt Resource Manager om de juiste wijzigingen aan te brengen in de bronnen die u hebt bijgewerkt. Zodra u bent aangemeld bij uw account en in het juiste abonnement bent, gebruikt u de volgende opdracht om de upgrade uit te voeren:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Zodra de upgrade klaar is met de uitrol (duurt tussen de 15-45 minuten, afhankelijk van of het de eerste implementatie en de grootte van uw resourcegroep is), moet WAD de prestatiemeteritems verzamelen en naar de tabel met de naam WADPerformanceCountersTabel in het opslagaccount dat aan uw cluster is gekoppeld. Bekijk uw prestatiemeteritems in Application Insights door [de AI-sink toe te voegen aan de sjabloon Resource Manager.](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)

## <a name="next-steps"></a>Volgende stappen
* Verzamel meer prestatiemeteritems voor uw cluster. Zie [Prestatiestatistieken](service-fabric-diagnostics-event-generation-perf.md) voor een lijst met tellers die u moet verzamelen.
* [Gebruik bewaking en diagnose met een Windows VM- en Azure Resource Manager-sjablonen](../virtual-machines/windows/extensions-diagnostics-template.md) om verdere wijzigingen aan te brengen in uw `WadCfg`, inclusief het configureren van extra opslagaccounts om diagnostische gegevens naar te verzenden.
* Ga naar de [WadCfg-bouwer](https://azure.github.io/azure-diagnostics-tools/config-builder/) om een sjabloon helemaal opnieuw te bouwen en ervoor te zorgen dat uw syntaxis correct is. (https://azure.github.io/azure-diagnostics-tools/config-builder/) om een sjabloon helemaal opnieuw te bouwen en ervoor te zorgen dat de syntaxis correct is.
