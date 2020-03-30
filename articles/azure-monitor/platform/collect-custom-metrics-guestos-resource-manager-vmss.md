---
title: Windows-schaalsetstatistieken verzamelen in Azure Monitor met sjabloon
description: Gastgegevens van besturingssysteem naar de Azure Monitor-metrische opslag verzenden met behulp van een resourcebeheersjabloon voor een windows-schaalset voor virtuele machines
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 24f83e4f6285d045e67bdaef431ebcff2345ef84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663889"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Gastgegevens van besturingssysteem naar de Azure Monitor-metrische opslag verzenden met behulp van een Azure Resource Manager-sjabloon voor een windows-schaalset voor virtuele machines

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Met de [WAD-extensie (Azure](diagnostics-extension-overview.md)Monitor Windows Diagnostics) u statistieken en logboeken verzamelen van het gastbesturingssysteem (gastbesturingssysteem) dat wordt uitgevoerd als onderdeel van een virtuele machine, cloudservice of Azure Service Fabric-cluster. De extensie kan telemetrie verzenden naar veel verschillende locaties die in het eerder gekoppelde artikel worden vermeld.  

In dit artikel wordt het proces beschreven om prestatiestatistieken voor gastbesturingssysteemen te verzenden voor een Windows-schaalschaal voor virtuele machines die is ingesteld naar het Azure Monitor-gegevensarchief. Vanaf Windows Azure Diagnostics versie 1.11 u statistieken rechtstreeks schrijven naar de Azure Monitor metrics store, waar standaardplatformstatistieken al zijn verzameld. Door ze op deze locatie op te slaan, hebt u toegang tot dezelfde acties die beschikbaar zijn voor platformstatistieken. Acties omvatten near real-time waarschuwingen, grafieken, routering, toegang vanuit de REST API en meer. In het verleden is de Windows Azure Diagnostics-extensie geschreven naar Azure Storage, maar niet naar het Azure Monitor-gegevensarchief.  

Als u nieuw bent in Resource Manager-sjablonen, leest u meer over [sjabloonimplementaties](../../azure-resource-manager/management/overview.md) en de structuur en syntaxis ervan.  

## <a name="prerequisites"></a>Vereisten

- Uw abonnement moet zijn geregistreerd bij [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- U moet [Azure PowerShell](/powershell/azure) hebben geïnstalleerd of u [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)gebruiken. 

- Uw VM-bron moet zich in een [regio bevinden die aangepaste statistieken ondersteunt.](metrics-custom-overview.md#supported-regions)

## <a name="set-up-azure-monitor-as-a-data-sink"></a>Azure Monitor instellen als gegevenssink 
De Azure Diagnostics-extensie maakt gebruik van een functie genaamd **gegevenssinks** om metrische gegevens en logboeken naar verschillende locaties te routeren. In de volgende stappen wordt uitgelegd hoe u een Resource Manager-sjabloon en PowerShell gebruikt om een VM te implementeren met behulp van de nieuwe Azure Monitor-gegevenssink. 

## <a name="author-a-resource-manager-template"></a>Een sjabloon Resourcemanager maken 
In dit voorbeeld u een openbaar beschikbare [voorbeeldsjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale)gebruiken:  

- **Azuredeploy.json** is een vooraf geconfigureerde Resource Manager-sjabloon voor de implementatie van een virtuele machineschaalset.

- **Azuredeploy.parameters.json** is een parametersbestand dat informatie opslaat, zoals welke gebruikersnaam en wachtwoord u wilt instellen voor uw vm. Tijdens de implementatie gebruikt de sjabloon Resourcebeheer de parameters die in dit bestand zijn ingesteld. 

Download en sla beide bestanden lokaal op. 

###  <a name="modify-azuredeployparametersjson"></a>Azuredeploy.parameters.json wijzigen
Open het bestand **azuredeploy.parameters.json:**  
 
- Geef een **vmSKU** die u wilt implementeren. Wij raden Standard_D2_v3 aan. 
- Geef een **gewenste windowsOSVersie** op voor uw virtuele machineschaalset. Wij raden 2016-Datacenter aan. 
- Geef een naam aan de bron van de virtuele machineschaalset die moet worden geïmplementeerd met de eigenschap **vmssName.** Een voorbeeld is **VMSS-WAD-TEST**.    
- Geef het aantal VM's op dat u wilt uitvoeren op de virtuele machineschaal die is ingesteld met de eigenschap **instanceCount.**
- Voer waarden in voor **adminGebruikersnaam** en **adminPassword** voor de virtuele machineschaalset. Deze parameters worden gebruikt voor externe toegang tot de VM's in de schaalset. Om te voorkomen dat uw VM gekaapt, **niet** gebruik maken van degenen in deze sjabloon. Bots scannen het internet op gebruikersnamen en wachtwoorden in openbare GitHub-repositories. Ze zijn waarschijnlijk het testen van VM's met deze standaardinstellingen. 


###  <a name="modify-azuredeployjson"></a>Azuredeploy.json wijzigen
Open het **azuredeploy.json-bestand.** 

Voeg een variabele toe om de opslagaccountgegevens in de sjabloon Resourcebeheer te bewaren. Logboeken of prestatiemeteritems die zijn opgegeven in het diagnostisch config-bestand, worden naar zowel het Azure Monitor-metric store als het opslagaccount geschreven dat u hier opgeeft: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Zoek de definitie van de virtuele machineschaalset in de sectie resources en voeg de **identiteitssectie** toe aan de configuratie. Deze toevoeging zorgt ervoor dat Azure het een systeemidentiteit toewijst. Deze stap zorgt er ook voor dat de VM's in de schaalset gaststatistieken over zichzelf kunnen uitzenden naar Azure Monitor:  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

Zoek in de bron voor virtuele machineschaalset de sectie **virtualMachineProfile.** Voeg een nieuw profiel toe met de naam **extensiesProfiel** om extensies te beheren.  


Voeg in de **extensieProfiel**een nieuwe extensie toe aan de sjabloon zoals weergegeven in de sectie **VMSS-WAD-extensie.**  Deze sectie is de beheerde identiteiten voor Azure-resources-extensie die ervoor zorgt dat de gegevens die worden uitgezonden, worden geaccepteerd door Azure Monitor. Het **naamveld** kan elke naam bevatten. 

De volgende code van de MSI-extensie voegt ook de diagnostische extensie en configuratie toe als een extensiebron aan de bron voor de virtuele machineschaalset. Voeg de prestatiemeteritems indien nodig toe of verwijder deze: 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
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
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Voeg een **dependsOn** toe voor het opslagaccount om er zeker van te zijn dat het in de juiste volgorde is gemaakt: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Maak een opslagaccount als er nog geen is gemaakt in de sjabloon: 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Beide bestanden opslaan en sluiten. 

## <a name="deploy-the-resource-manager-template"></a>De sjabloon Resourcemanager implementeren 

> [!NOTE]  
> U moet de Azure Diagnostics-extensie versie 1.5 of hoger uitvoeren **en** de **eigenschap autoUpgradeMinorVersion:** eigenschap zijn ingesteld op **true** in uw Resource Manager-sjabloon. Azure laadt vervolgens de juiste extensie wanneer de VM wordt gestart. Als u deze instellingen niet in uw sjabloon hebt, wijzigt u deze en implementeert u de sjabloon opnieuw. 


Als u de sjabloon Resourcebeheer wilt implementeren, gebruikt u Azure PowerShell:  

1. Start PowerShell. 
1. Meld u aan `Login-AzAccount`bij Azure met behulp van .
1. Ontvang uw lijst met `Get-AzSubscription`abonnementen via .
1. Stel het abonnement in dat u maakt of werk de virtuele machine bij: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Maak een nieuwe resourcegroep voor de VM die wordt geïmplementeerd. Voer de volgende opdracht uit: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Vergeet niet om een Azure-regio te gebruiken die is ingeschakeld voor aangepaste statistieken. Vergeet niet om een [Azure-regio](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions)te gebruiken die is ingeschakeld voor aangepaste statistieken.
 
1. Voer de volgende opdrachten uit om de VM te implementeren:  

   > [!NOTE]  
   > Als u een bestaande schaalset wilt bijwerken, voegt u **incrementeel -Modus toe** aan het einde van de opdracht. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Nadat uw implementatie is geslaagd, moet u de virtuele machineschaal vinden die is ingesteld in de Azure-portal. Het moet metrische gegevens uitzenden naar Azure Monitor. 

   > [!NOTE]  
   > U fouten tegenkomen rond de geselecteerde **vmSkuSize**. Ga in dat geval terug naar het **azuredeploy.json-bestand** en werk de standaardwaarde van de parameter **vmSkuSize** bij. We raden je aan **Standard_DS1_v2**te proberen. 


## <a name="chart-your-metrics"></a>Uw statistieken in kaart brengen 

1. Meld u aan bij Azure Portal. 

1. Selecteer **Monitor**in het linkermenu . 

1. Selecteer op de pagina **Monitor** de optie **Metrische gegevens**. 

   ![Monitor - Pagina Statistieken](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Wijzig de aggregatieperiode in **Laatste 30 minuten**.  

1. Selecteer in de vervolgkeuzelijst resource de virtuele machineschaalset die u hebt gemaakt.  

1. Selecteer **azure.vm.windows.guest**in het vervolgkeuzemenu namespaces . 

1. Selecteer in de vervolgkeuzelijst Geheugen **\%vastgelegde bytes in gebruik in**het vervolgkeuzelijst voor statistieken .  

U er vervolgens ook voor kiezen om de afmetingen op deze statistiek te gebruiken om deze voor een bepaalde vm in kaart te brengen of om elke VM in de schaalset te plotten. 



## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [aangepaste statistieken](metrics-custom-overview.md).


