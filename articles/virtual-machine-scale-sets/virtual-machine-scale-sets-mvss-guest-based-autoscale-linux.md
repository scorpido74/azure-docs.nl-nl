---
title: Azure-autoscale gebruiken met gaststatistieken in een sjabloon voor de Linux-schaalset
description: Meer informatie over automatisch schalen met gaststatistieken in een sjabloon voor virtuele machineschaal van Linux
author: mayanknayar
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 88f839b281e4d345b012a7e6acff3770dc536045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271955"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Automatisch schalen met gaststatistieken in een sjabloon voor een Linux-schaalset

Er zijn twee brede typen statistieken in Azure die worden verzameld uit VM's en schaalsets: Hostmetrics en Guest metrics. Op een hoog niveau, als u standaard CPU-, schijf- en netwerkstatistieken wilt gebruiken, passen hoststatistieken goed. Als u echter een grotere selectie van statistieken nodig hebt, moet er naar gaststatistieken worden gekeken.

Hoststatistieken vereisen geen extra installatie omdat ze worden verzameld door de host-VM, terwijl u voor gaststatistieken de [Windows Azure Diagnostics-extensie](../virtual-machines/windows/extensions-diagnostics-template.md) of de [Linux Azure Diagnostics-extensie](../virtual-machines/linux/diagnostic-extension.md) in de gast-VM moet installeren. Een veelvoorkomende reden om gaststatistieken te gebruiken in plaats van hoststatistieken is dat gaststatistieken een grotere selectie statistieken bieden dan hoststatistieken. Een voorbeeld hiervan is geheugenverbruiksstatistieken, die alleen beschikbaar zijn via gaststatistieken. De ondersteunde hoststatistieken worden [hier](../azure-monitor/platform/metrics-supported.md)weergegeven en veelgebruikte gaststatistieken worden [hier](../azure-monitor/platform/autoscale-common-metrics.md)weergegeven. In dit artikel ziet u hoe u de [sjabloon voor de basisbare schaalset](virtual-machine-scale-sets-mvss-start.md) wijzigen om automatische schaalregels te gebruiken op basis van gaststatistieken voor Linux-schaalsets.

## <a name="change-the-template-definition"></a>De sjabloondefinitie wijzigen

In een [vorig artikel](virtual-machine-scale-sets-mvss-start.md) hadden we een basissjabloon voor schaalsets gemaakt. We gebruiken die eerdere sjabloon nu en wijzigen deze om een sjabloon te maken die een Linux-schaalset implementeert met gastmetrische automatische schaal.

Voeg eerst parameters `storageAccountName` `storageAccountSasToken`toe voor en . De diagnoseagent slaat metrische gegevens op in een [tabel](../cosmos-db/table-storage-how-to-use-dotnet.md) in dit opslagaccount. Vanaf de Linux Diagnostics Agent versie 3.0 wordt het gebruik van een opslagtoegangssleutel niet meer ondersteund. Gebruik in plaats daarvan een [SAS-token](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Wijzig vervolgens de `extensionProfile` schaalset om de diagnostische extensie op te nemen. Geef in deze configuratie de resource-id op van de schaalset die is ingesteld om statistieken te verzamelen, evenals het opslagaccount en het SAS-token dat moet worden gebruikt om de statistieken op te slaan. Geef op hoe vaak de statistieken worden samengevoegd (in dit geval, elke minuut) en welke statistieken moeten worden bijgehouden (in dit geval procenten gebruikt geheugen). Zie [deze documentatie](../virtual-machines/linux/diagnostic-extension.md)voor meer gedetailleerde informatie over deze configuratie en andere statistieken dan het percentage gebruikte geheugen.

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Voeg ten `autoscaleSettings` slotte een resource toe om automatisch schalen te configureren op basis van deze statistieken. Deze resource `dependsOn` heeft een clausule die verwijst naar de schaaldie is ingesteld om ervoor te zorgen dat de schaalset bestaat voordat u probeert deze automatisch te schalen. Als u een andere statistiek kiest om automatisch `counterSpecifier` op te schalen, `metricName` gebruikt u de configuratie van de diagnostische extensie als de configuratie in de automatische schaal. Zie de aanbevolen procedures voor [automatische schaal](../azure-monitor/platform/autoscale-best-practices.md) en de [naslagdocumentatie voor Azure Monitor REST API](/rest/api/monitor/autoscalesettings)voor meer informatie over automatische configuratie.

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
