---
title: Azure PowerShell gebruiken om diagnoses op een Windows-vm in te schakelen
services: virtual-machines-windows
documentationcenter: ''
description: Meer informatie over het gebruik van PowerShell om Azure Diagnostics in te schakelen in een virtuele machine met Windows
author: mimckitt
manager: gwallace
editor: ''
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2015
ms.author: mimckitt
ms.openlocfilehash: 16e1dba8c430a5c1e1d1d69910b8ed2c8d0b8138
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262839"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>PowerShell gebruiken om Azure Diagnostics in te schakelen in een virtuele machine met Windows

Azure Diagnostics is de mogelijkheid binnen Azure waarmee diagnostische gegevens voor een geïmplementeerde toepassing kunnen worden opgehaald. U de diagnostische extensie gebruiken om diagnostische gegevens te verzamelen, zoals toepassingslogboeken of prestatiemeteritems van een virtuele Azure-machine (VM) waarop Windows wordt uitgevoerd. 

 

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>De diagnostische extensie inschakelen als u het implementatiemodel Resource Manager gebruikt
U de diagnostische extensie inschakelen terwijl u een Windows VM maakt via het Azure Resource Manager-implementatiemodel door de extensieconfiguratie toe te voegen aan de sjabloon ResourceBeheer. Zie [Een virtuele Windows-machine maken met bewaking en diagnose met de sjabloon Azure Resource Manager](diagnostics-template.md).

Als u de diagnostische extensie wilt inschakelen op een bestaande VM die is gemaakt via het implementatiemodel van Resource Manager, u de cmdlet [Set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiagnosticsextension) PowerShell gebruiken zoals hieronder weergegeven.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* is het pad naar het bestand dat de diagnostische configuratie in XML bevat, zoals beschreven in het onderstaande [voorbeeld.](#sample-diagnostics-configuration)  

Als in het configuratiebestand een **StorageAccount-element** met een naam van een opslagaccount wordt opgegeven, wordt in het script *Set-AzVMDiagnosticsExtension* automatisch de diagnostische extensie ingesteld om diagnostische gegevens naar dat opslagaccount te verzenden. Om dit te laten werken, moet het opslagaccount in hetzelfde abonnement zijn als de VM.

Als er geen **StorageAccount** is opgegeven in de diagnoseconfiguratie, moet u de parameter *StorageAccountName* doorgeven aan de cmdlet. Als de parameter *StorageAccountName* is opgegeven, gebruikt de cmdlet altijd het opslagaccount dat is opgegeven in de parameter en niet het account dat is opgegeven in het configuratiebestand voor diagnose.

Als het diagnostischopslagaccount zich in een ander abonnement bevindt dan de VM, moet u de parameters *StorageAccountName* en *StorageAccountKey* expliciet doorgeven aan de cmdlet. De parameter *StorageAccountKey* is niet nodig wanneer het diagnostischopslagaccount zich in hetzelfde abonnement bevindt, omdat de cmdlet automatisch de sleutelwaarde kan opvragen en instellen wanneer de diagnostische extensie wordt ingeschakeld. Als het opslagaccount voor diagnostische gegevens echter een ander abonnement heeft, kan de cmdlet de sleutel mogelijk niet automatisch krijgen en moet u de sleutel expliciet opgeven via de parameter *StorageAccountKey.*  

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Zodra de diagnostische extensie is ingeschakeld op een VM, u de huidige instellingen krijgen met behulp van de [cmdlet Get-AzVmDiagnosticsExtension.](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiagnosticsextension)

    Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

De cmdlet retourneert *PublicSettings*, die de diagnoseconfiguratie bevat. Er zijn twee soorten configuratie ondersteund, WadCfg en xmlCfg. WadCfg is JSON-configuratie en xmlCfg is XML-configuratie in een Base64-gecodeerde indeling. Als u de XML wilt lezen, moet u deze decoderen.

    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

De cmdlet [Remove-AzVmDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdiagnosticsextension) kan worden gebruikt om de diagnostische extensie van de VM te verwijderen.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>De diagnostische extensie inschakelen als u het klassieke implementatiemodel gebruikt

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

U de cmdlet [Set-AzureVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) gebruiken om een diagnostische extensie in te schakelen op een VM die u maakt via het klassieke implementatiemodel. In het volgende voorbeeld ziet u hoe u een nieuwe virtuele machine maakt via het klassieke implementatiemodel waarbij de diagnostische extensie is ingeschakeld.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM

Als u de diagnostische extensie wilt inschakelen op een bestaande VM die is gemaakt via het klassieke implementatiemodel, gebruikt u eerst de [cmdlet Get-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azurevm) om de VM-configuratie te krijgen. Werk vervolgens de VM-configuratie bij om de diagnostische extensie op te nemen met de cmdlet [Set-AzureVMDiagnosticsExtension.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) Tot slot u de bijgewerkte configuratie toepassen op de VM met Behulp van [Update-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/update-azurevm).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension  -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Voorbeeldconfiguratie voor diagnostische gegevens
De volgende XML kan worden gebruikt voor de openbare configuratie van de diagnostiek met de bovenstaande scripts. Met deze voorbeeldconfiguratie worden verschillende prestatiemeteritems overgebracht naar het opslagaccount voor diagnostische gegevens, samen met fouten uit de toepassing, beveiliging en systeemkanalen in de Windows-gebeurtenislogboeken en eventuele fouten uit de logboeken van de diagnostische infrastructuur.

De configuratie moet worden bijgewerkt om het volgende op te nemen:

* Het *kenmerk resourceID* van het element **Metrische gegevens** moet worden bijgewerkt met de resource-id voor de virtuele machine.
  
  * De resource-id kan worden opgebouwd met behulp van het volgende patroon: "/abonnementen/{*abonnements-ID voor het abonnement met de VM*}/resourceGroepen/{ De naam van de*resourcegroep voor de VM*}/providers/Microsoft.Compute/virtualMachines/{ The VM*Name*}".
  * Als de abonnements-id voor het abonnement waarop de VM wordt uitgevoerd bijvoorbeeld **11111111-1111-1111-1111-111111111111 11111111**is, is de naam van de resourcegroep voor de resourcegroep **MyResourceGroup**en de VM-naam **MyWindowsVM,** dan is de waarde voor *resource-ID:*
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Zie [Azure Diagnostics metrics table in storage](diagnostics-template.md#wadmetrics-tables-in-storage)voor meer informatie over hoe statistieken worden gegenereerd op basis van de prestatiemeteritems en metrische gegevensconfiguratie.
* Het element **StorageAccount** moet worden bijgewerkt met de naam van het opslagaccount voor diagnostische gegevens.
  
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Volgende stappen
* Zie [Diagnostische gegevens inschakelen in Azure Cloud Services en Virtuele machines](../../cloud-services/cloud-services-dotnet-diagnostics.md)voor aanvullende richtlijnen voor het gebruik van de Azure Diagnostics-mogelijkheid en andere technieken om problemen op te lossen.
* [Het configuratieschema voor diagnostische](https://msdn.microsoft.com/library/azure/mt634524.aspx) gegevens wordt uitgelegd in de verschillende XML-configuratiesopties voor de diagnostische extensie.

