---
title: Windows VM-statistieken verzamelen in Azure Monitor met sjabloon
description: Gastgegevens van besturingssysteem naar de Azure Monitor-metrische winkel verzenden met behulp van een resourcemanagersjabloon voor een virtuele Windows-machine
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e747ca89912c36538bfb9d02986629fe57c5adcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657364"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Gastbesturingssysteemstatistieken naar de Azure Monitor-metrische winkel verzenden met behulp van een resourcemanagersjabloon voor een virtuele Windows-machine

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Met de Azure Monitor [Diagnostics-extensie](diagnostics-extension-overview.md)u statistieken en logboeken verzamelen van het gastbesturingssysteem (Guest OS) dat wordt uitgevoerd als onderdeel van een cluster van virtuele machines, cloudservices of Service Fabric. De extensie kan telemetrie naar [veel verschillende locaties verzenden.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

In dit artikel wordt het proces beschreven voor het verzenden van prestatiestatistieken voor gastbesturingssysteem voor een virtuele Windows-machine naar het Azure Monitor-gegevensarchief. Vanaf Diagnostics versie 1.11 u statistieken rechtstreeks schrijven naar de Azure Monitor-statistiekenwinkel, waar standaardplatformstatistieken al zijn verzameld.

Als u ze op deze locatie opslaat, hebt u toegang tot dezelfde acties voor platformstatistieken. Acties omvatten near-real-time waarschuwingen, grafieken, routering en toegang vanuit een REST API en meer. In het verleden is de extensie Diagnostische gegevens geschreven naar Azure Storage, maar niet naar het Azure Monitor-gegevensarchief.

Als u nieuw bent in Resource Manager-sjablonen, leest u meer over [sjabloonimplementaties](../../azure-resource-manager/management/overview.md) en de structuur en syntaxis ervan.

## <a name="prerequisites"></a>Vereisten

- Uw abonnement moet zijn geregistreerd bij [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- U moet [Azure PowerShell](/powershell/azure) of [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) hebben geïnstalleerd.

- Uw VM-bron moet zich in een [regio bevinden die aangepaste statistieken ondersteunt.](metrics-custom-overview.md#supported-regions) 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Azure Monitor instellen als gegevenssink
De Azure Diagnostics-extensie gebruikt een functie genaamd 'gegevenssinks' om statistieken en logboeken naar verschillende locaties te routeren. In de volgende stappen wordt uitgelegd hoe u een Resource Manager-sjabloon en PowerShell gebruikt om een VM te implementeren met behulp van de nieuwe gegevenssink 'Azure Monitor'.

## <a name="author-resource-manager-template"></a>Sjabloon Auteur Resource Manager
In dit voorbeeld u een openbaar beschikbare voorbeeldsjabloon gebruiken. De startsjablonen https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windowszijn op .

- **Azuredeploy.json** is een vooraf geconfigureerde Resource Manager-sjabloon voor de implementatie van een virtuele machine.

- **Azuredeploy.parameters.json** is een parametersbestand dat informatie opslaat, zoals de gebruikersnaam en het wachtwoord dat u voor uw vm wilt instellen. Tijdens de implementatie gebruikt de sjabloon Resourcebeheer de parameters die in dit bestand zijn ingesteld.

Download en sla beide bestanden lokaal op.

### <a name="modify-azuredeployparametersjson"></a>Azuredeploy.parameters.json wijzigen
Het bestand *azuredeploy.parameters.json* openen

1. Voer waarden in voor **adminGebruikersnaam** en **adminPassword** voor de VM. Deze parameters worden gebruikt voor externe toegang tot de VM. Om te voorkomen dat uw VM wordt gekaapt, gebruikt U DE WAARDEN IN DEZE sjabloon NIET. Bots scannen het internet op gebruikersnamen en wachtwoorden in openbare GitHub-repositories. Ze zijn waarschijnlijk het testen van VM's met deze standaardinstellingen.

1. Maak een unieke dnsname voor de VM.

### <a name="modify-azuredeployjson"></a>Azuredeploy.json wijzigen

Het *azuredeploy.json-bestand* openen

Voeg een opslagaccount-id toe aan het **gedeelte variabelen** van de sjabloon na de vermelding voor **storageAccountName.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Voeg deze MSI-extensie (Managed Service Identity) toe aan de sjabloon boven aan de sectie **resources.** De extensie zorgt ervoor dat Azure Monitor de statistieken accepteert die worden uitgestoten.

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

Voeg de **identiteitsconfiguratie** toe aan de VM-bron om ervoor te zorgen dat Azure een systeemidentiteit toewijst aan de MSI-extensie. Deze stap zorgt ervoor dat de VM gaststatistieken over zichzelf kan uitzenden naar Azure Monitor.

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

Voeg de volgende configuratie toe om de Diagnostische extensie op een virtuele Windows-machine in te schakelen. Voor een eenvoudige virtuele machine op basis van Resource Manager kunnen we de extensieconfiguratie toevoegen aan de array resources voor de virtuele machine. De regel "zinkt"&mdash; "AzMonSink" en de bijbehorende "SinksConfig" later in de sectie&mdash;stellen de extensie in staat om statistieken rechtstreeks naar Azure Monitor uit te zenden. Voeg de prestatiemeteritems waar nodig toe of verwijder deze.


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


Beide bestanden opslaan en sluiten.


## <a name="deploy-the-resource-manager-template"></a>De sjabloon Resourcemanager implementeren

> [!NOTE]
> U moet de Azure Diagnostics-extensie versie 1.5 of hoger uitvoeren en de **autoUpgradeMinorVersion:** eigenschap hebben ingesteld op 'true' in uw Resource Manager-sjabloon. Azure laadt vervolgens de juiste extensie wanneer de VM wordt gestart. Als u deze instellingen niet in uw sjabloon hebt, wijzigt u deze en implementeert u de sjabloon opnieuw.


Als u de sjabloon Resource Manager wilt implementeren, maken we gebruik van Azure PowerShell.

1. Start PowerShell.
1. Log in bij `Login-AzAccount`Azure met behulp van .
1. Ontvang uw lijst met `Get-AzSubscription`abonnementen via .
1. Stel het abonnement in dat u gebruikt om de virtuele machine te maken/bijwerken in:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Voer de volgende opdracht uit om een nieuwe resourcegroep voor de VM te maken die wordt geïmplementeerd:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Vergeet niet om [een Azure-regio](metrics-custom-overview.md)te gebruiken die is ingeschakeld voor aangepaste statistieken.

1. Voer de volgende opdrachten uit om de VM te implementeren met de sjabloon Resourcebeheer.
   > [!NOTE]
   > Als u een bestaande vm wilt bijwerken, voegt u *incrementeel -Modus toe* aan het einde van de volgende opdracht.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Nadat uw implementatie is geslaagd, moet de VM zich in de Azure-portal bevinden en metrische gegevens uitzenden naar Azure Monitor.

   > [!NOTE]
   > U fouten tegenkomen rond de geselecteerde vmSkuSize. Als dit gebeurt, gaat u terug naar het bestand azuredeploy.json en werkt u de standaardwaarde van de parameter vmSkuSize bij. In dit geval raden we aan om "Standard_DS1_v2" te proberen.

## <a name="chart-your-metrics"></a>Uw statistieken in kaart brengen

1. Meld u aan bij Azure Portal.

2. Selecteer In het linkermenu de optie **Monitor**.

3. Selecteer op de pagina Monitor de optie **Metrische gegevens**.

   ![Pagina Statistieken](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Wijzig de aggregatieperiode in **Laatste 30 minuten**.

5. Selecteer in de vervolgkeuzelijst resource de VM die u hebt gemaakt. Als u de naam in de sjabloon niet hebt gewijzigd, moet dit *SimpleWinVM2*zijn.

6. Selecteer **azure.vm.windows.guest** in het vervolgkeuzemenu namespaces

7. Selecteer in het vervolgkeuzemenu Metrische gegevens de optie **Geheugenvastgelegde\%bytes in gebruik**.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [aangepaste statistieken](metrics-custom-overview.md).

