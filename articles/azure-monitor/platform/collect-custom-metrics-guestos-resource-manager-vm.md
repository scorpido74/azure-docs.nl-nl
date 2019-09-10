---
title: Metrische gegevens van het gast besturingssysteem naar het Azure Monitor metrische archief verzenden met een resource manager-sjabloon voor een virtuele Windows-machine
description: Metrische gegevens van het gast besturingssysteem naar het Azure Monitor metrische archief verzenden met een resource manager-sjabloon voor een virtuele Windows-machine
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: ac8d059c2bcad7aaa005b4afe1fb7814d49f9339
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844959"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Metrische gegevens van het gast besturingssysteem naar het Azure Monitor metrische archief verzenden met een resource manager-sjabloon voor een virtuele Windows-machine

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Met de Azure Monitor Diagnostics- [extensie](diagnostics-extension-overview.md)kunt u metrische gegevens en logboeken verzamelen van het gast besturingssysteem (gast besturingssysteem) dat wordt uitgevoerd als onderdeel van een virtuele machine, Cloud service of service Fabric cluster. De uitbrei ding kan telemetrie verzenden naar een [groot aantal verschillende locaties.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

In dit artikel wordt het proces beschreven voor het verzenden van de prestaties van een virtuele Windows-machine naar het Azure Monitor-gegevens archief. Te beginnen met diagnostische gegevens van versie 1,11, kunt u metrische gegevens rechtstreeks naar de opslag voor metrische gegevens van Azure Monitor schrijven, waar de metrische gegevens van het standaard platform al zijn verzameld.

Door ze op deze locatie op te slaan, hebt u toegang tot dezelfde acties voor platform metrieken. Acties omvatten bijna realtime waarschuwingen, grafieken, route ring en toegang vanaf een REST API en meer. In het verleden schreef de diagnostische uitbrei ding naar Azure Storage, maar niet naar de Azure Monitor gegevens opslag.

Als u niet bekend bent met Resource Manager-sjablonen, kunt u meer te weten komen over [sjabloon implementaties](../../azure-resource-manager/resource-group-overview.md) en de bijbehorende structuur en syntaxis.

## <a name="prerequisites"></a>Vereisten

- Uw abonnement moet zijn geregistreerd bij [micro soft. Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- U moet [Azure PowerShell](/powershell/azure) of [Azure Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview) hebben geïnstalleerd.

- De VM-resource moet zich in een regio bevinden [die aangepaste metrische gegevens ondersteunt](metrics-custom-overview.md#supported-regions). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Azure Monitor instellen als een gegevens Sink
De uitbrei ding Azure Diagnostics gebruikt een functie met de naam ' gegevens-sinks ' voor het routeren van metrieken en logboeken naar verschillende locaties. De volgende stappen laten zien hoe u een resource manager-sjabloon en Power shell gebruikt om een virtuele machine te implementeren met behulp van de nieuwe gegevens Sink ' Azure Monitor '.

## <a name="author-resource-manager-template"></a>Resource Manager-sjabloon maken
Voor dit voor beeld kunt u een openbaar beschik bare voorbeeld sjabloon gebruiken. De start sjablonen bevinden https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows zich op.

- **Azuredeploy. json** is een vooraf geconfigureerde Resource Manager-sjabloon voor de implementatie van een virtuele machine.

- **Azuredeploy. para meters. json** is een bestand met para meters waarin gegevens worden opgeslagen, zoals de gebruikers naam en het wacht woord die u wilt instellen voor uw VM. Tijdens de implementatie gebruikt de Resource Manager-sjabloon de para meters die in dit bestand zijn ingesteld.

Down load en sla beide bestanden lokaal op.

### <a name="modify-azuredeployparametersjson"></a>Azuredeploy. para meters. json wijzigen
Open het bestand *azuredeploy. para meters. json*

1. Voer waarden in voor **adminUsername** en **adminPassword** voor de virtuele machine. Deze para meters worden gebruikt voor externe toegang tot de virtuele machine. Gebruik niet de waarden in deze sjabloon om te voor komen dat uw virtuele machine wordt overgenomen. Bots scannen op internet voor gebruikers namen en wacht woorden in open bare GitHub-opslag plaatsen. Waarschijnlijk worden er Vm's getest met deze standaard waarden.

1. Maak een unieke dnsname voor de virtuele machine.

### <a name="modify-azuredeployjson"></a>Azuredeploy. json wijzigen

Open het bestand *azuredeploy. json*

Voeg een opslag account-ID toe aan de sectie **variabelen** van de sjabloon na de vermelding voor **storageAccountName.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Voeg deze Managed Service Identity (MSI)-extensie toe aan de sjabloon boven aan de sectie **resources** . De uitbrei ding zorgt ervoor dat Azure Monitor de metrische gegevens accepteert die worden verzonden.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'), '/', 'WADExtensionSetup')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    },
```

Voeg de **identiteits** configuratie toe aan de VM-resource om ervoor te zorgen dat Azure een systeem identiteit toewijst aan de MSI-extensie. Met deze stap zorgt u ervoor dat de virtuele machine de metrische gegevens over de gast over zichzelf kan verzenden naar Azure Monitor.

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    // add these 3 lines below
    "identity": {
    "type": "SystemAssigned"
    },
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {
    ...
```

Voeg de volgende configuratie toe om de diagnostische uitbrei ding op een virtuele Windows-machine in te scha kelen. Voor een eenvoudige virtuele machine op basis van Resource Manager kunt u de extensie configuratie toevoegen aan de resource matrix voor de virtuele machine. De regel "sink"&mdash; "AzMonSink" en de bijbehorende "SinksConfig" verderop in de sectie&mdash;zorgen ervoor dat de uitbrei ding de metrische gegevens rechtstreeks naar Azure monitor kan verzenden. U kunt zo nodig prestatie meter items toevoegen of verwijderen.


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]"
    }
}
},
//Start of section to add
"resources": [
{
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'), '/', 'Microsoft.Insights.VMDiagnosticsSettings')]",
            "apiVersion": "2017-12-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.12",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "WadCfg": {
                "DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
        },
                    "Directories": {
                    "scheduledTransferPeriod": "PT1M",
    "IISLogs": {
                        "containerName": "wad-iis-logfiles"
                    },
                    "FailedRequestLogs": {
                        "containerName": "wad-failedrequestlogs"
                    }
                    },
                    "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "sinks": "AzMonSink",
                    "PerformanceCounterConfiguration": [
                        {
                        "counterSpecifier": "\\Memory\\Available Bytes",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\Committed Bytes",
                        "sampleRate": "PT15S"
                        }
                    ]
                    },
                    "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                        {
                        "name": "Application!*"
                        }
                    ]
                    },
                    "Logs": {
                    "scheduledTransferPeriod": "PT1M",
                    "scheduledTransferLogLevelFilter": "Error"
                    }
                },
                "SinksConfig": {
                    "Sink": [
                    {
                        "name" : "AzMonSink",
                        "AzureMonitor" : {}
                    }
                    ]
                }
                },
                "StorageAccount": "[variables('storageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[variables('storageAccountName')]",
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]",
                "storageAccountEndPoint": "https://core.windows.net/"
            }
            }
        }
        ]
//End of section to add
```


Sla beide bestanden op en sluit deze.


## <a name="deploy-the-resource-manager-template"></a>De Resource Manager-sjabloon implementeren

> [!NOTE]
> U moet de Azure Diagnostics extensie versie 1,5 of hoger uitvoeren en de eigenschap **autoUpgradeMinorVersion**: ingesteld op True in uw Resource Manager-sjabloon. Azure laadt vervolgens de juiste extensie wanneer de virtuele machine wordt gestart. Als u deze instellingen niet in uw sjabloon hebt, wijzigt u deze en implementeert u de sjabloon opnieuw.


We maken gebruik van Azure PowerShell om de Resource Manager-sjabloon te implementeren.

1. Start Power shell.
1. Meld u aan bij Azure `Login-AzAccount`met.
1. Haal uw lijst met abonnementen op met `Get-AzSubscription`behulp van.
1. Stel het abonnement in dat u gebruikt om de virtuele machine te maken of bij te werken in:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Als u een nieuwe resource groep wilt maken voor de virtuele machine die wordt geïmplementeerd, voert u de volgende opdracht uit:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Vergeet niet [een Azure-regio te gebruiken die is ingeschakeld voor aangepaste metrische gegevens](metrics-custom-overview.md).

1. Voer de volgende opdrachten uit om de virtuele machine te implementeren met behulp van de Resource Manager-sjabloon.
   > [!NOTE]
   > Als u een bestaande virtuele machine wilt bijwerken, kunt u een incrementele toevoeg *modus* toevoegen aan het einde van de volgende opdracht.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Nadat de implementatie is voltooid, moet de virtuele machine zich in de Azure Portal bekomen, waarbij de metrische gegevens naar Azure Monitor worden verzonden.

   > [!NOTE]
   > U kunt fouten rond de geselecteerde vmSkuSize uitvoeren. Als dit het geval is, gaat u terug naar het bestand azuredeploy. json en werkt u de standaard waarde van de vmSkuSize-para meter bij. In dit geval kunt u het beste ' Standard_DS1_v2 ' proberen.

## <a name="chart-your-metrics"></a>Grafieken van uw metrische gegevens

1. Meld u aan bij Azure Portal.

2. Selecteer in het menu links de optie **monitor**.

3. Selecteer **metrische gegevens**op de pagina monitor.

   ![Pagina metrische gegevens](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Wijzig de aggregatie periode in de **laatste 30 minuten**.

5. Selecteer in de vervolg keuzelijst resource de virtuele machine die u hebt gemaakt. Als u de naam in de sjabloon niet hebt gewijzigd, moet deze *SimpleWinVM2*zijn.

6. Selecteer in de vervolg keuzelijst naam ruimten de optie **Azure. VM. Windows. gast**

7. Selecteer in de vervolg keuzelijst metrische gegevens **geheugen\%toegewezen bytes in gebruik**.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [aangepaste metrische gegevens](metrics-custom-overview.md).

