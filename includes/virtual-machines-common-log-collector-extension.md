---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09c4420647043fccc408631fec75854667923721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74085234"
---
Voor het diagnosticeren van problemen met een Microsoft Azure-cloudservice moeten de logboekbestanden van de service op virtuele machines worden verzameld wanneer de problemen zich voordoen. U de AzureLogCollector-extensie on-demand gebruiken om eenmalige verzameling logboeken uit een of meer Cloud Service VM's uit te voeren (van beide webrollen en werknemersrollen) en de verzamelde bestanden over te zetten naar een Azure-opslagaccount - dit alles zonder zich op afstand aan te melden bij een van de VM's.

> [!NOTE]
> Beschrijvingen voor de meeste geregistreerde informatie zijn te vinden ophttps://blogs.msdn.microsoft.com/kwill/2013/08/09/windows-azure-paas-compute-diagnostics-data/
> 
> 

Er zijn twee manieren van verzamelen afhankelijk van de soorten bestanden die moeten worden verzameld.

* **Alleen Azure Guest Agent Logs (GA)**. Deze verzamelingsmodus bevat alle logboeken met betrekking tot Azure-gastagents en andere Azure-componenten.
* **Alle logboeken (vol)**. Deze verzamelingsmodus verzamelt alle bestanden in de GA-modus plus:
  
  * logboeken voor systeem- en toepassingsgebeurtenissen
  * HTTP-foutlogboeken
  * IIS-logboeken
  * Installatielogboeken
  * andere systeemlogboeken

In beide verzamelingsmodi kunnen aanvullende mappen voor het verzamelen van gegevens worden opgegeven met behulp van een verzameling van de volgende structuur:

* **Naam**: De naam van de verzameling, gebruikt als de naam van de submap in het zip-bestand met de verzamelde bestanden.
* **Locatie:** Het pad naar de map op de virtuele machine waar bestanden worden verzameld bevinden zich.
* **SearchPattern**: Het patroon van de namen van bestanden die moeten worden verzameld. Standaard is\*" "
* **Recursief:** als de te verzamelen bestanden zich recursief onder de opgegeven locatie bevinden.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](./updated-for-az.md)]

* Heb een opslagaccount voor extensie om gegenereerde zip-bestanden op te slaan.
* Azure PowerShell. Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps)] voor installatie-instructies.

## <a name="add-the-extension"></a>De extensie toevoegen
U [Microsoft Azure PowerShell-cmdlets](https://msdn.microsoft.com/library/dn495240.aspx) of SERVICE Management REST [API's](https://msdn.microsoft.com/library/ee460799.aspx) gebruiken om de AzureLogCollector-extensie toe te voegen.

Voor Cloud Services kan de bestaande Azure Powershell-cmdlet, **Set-AzureServiceExtension,** worden gebruikt om de extensie in te schakelen voor functie-exemplaren van Cloud Service. Elke keer dat deze extensie via deze cmdlet is ingeschakeld, wordt logboekverzameling geactiveerd op de geselecteerde rolinstanties van geselecteerde rollen.

Voor virtuele machines kan de bestaande Azure Powershell-cmdlet, **Set-AzureVMExtension,** worden gebruikt om de extensie op virtuele machines in te schakelen. Elke keer dat deze extensie via de cmdlets wordt ingeschakeld, wordt logboekverzameling geactiveerd op elke instantie.

Intern maakt deze extensie gebruik van de op JSON gebaseerde PublicConfiguration en PrivateConfiguration. Het volgende is de lay-out van een voorbeeld JSON voor openbare en private configuratie.

### <a name="publicconfiguration"></a>Openbare configuratie

```json
{
    "Instances":  "*",
    "Mode":  "Full",
    "SasUri":  "SasUri to your storage account with sp=wl",
    "AdditionalData":
    [
      {
              "Name":  "StorageData",
              "Location":  "%roleroot%storage",
              "SearchPattern":  "*.*",
              "Recursive":  "true"
      },
      {
            "Name":  "CustomDataFolder2",
            "Location":  "c:\customFolder",
            "SearchPattern":  "*.log",
            "Recursive":  "false"
      },
    ]
}
```

### <a name="privateconfiguration"></a>Privéconfiguratie

```json
{

}
```

> [!NOTE]
> Deze extensie heeft geen **privateConfiguration**nodig. U gewoon een lege structuur voor de **-PrivateConfiguration** argument.
> 
> 

U een van de twee volgende stappen volgen om de AzureLogCollector toe te voegen aan een of meer exemplaren van een Cloud Service of Virtual Machine van geselecteerde rollen, waarmee de verzamelingen op elke vm worden uitgevoerd en de verzamelde bestanden naar Azure-account worden verzonden.

## <a name="adding-as-a-service-extension"></a>Toevoegen als service-extensie
1. Volg de instructies om Azure PowerShell met uw abonnement te verbinden.
2. Geef de servicenaam, sleuf, rollen en rolinstanties op waaraan u de AzureLogCollector-extensie wilt toevoegen en schakel de AzureLogCollector-extensie in.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'extensiontest2'

   #Specify the slot. 'Production' or 'Staging'
   $slot = 'Production'

   #Specified the roles on which the extension will be installed and enabled
   $roles = @("WorkerRole1","WebRole1")

   #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
   $instances = @("*")

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"
   ```

3. Geef de extra gegevensmap op voor welke bestanden worden verzameld (deze stap is optioneel).

   ```powershell
   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
   #more locations can be added....
   ```

   > [!NOTE]
   > U `%roleroot%` token gebruiken om de hoofdstation voor rollen op te geven, omdat deze geen vast station gebruikt.
   > 
   > 
4. Geef de naam van het Azure-opslagaccount en de sleutel op waarop verzamelde bestanden worden geüpload.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

5. Bel de SetAzureServiceLogCollector.ps1 (inbegrepen aan het einde van het artikel) als volgt om de AzureLogCollector-extensie voor een Cloud Service in te schakelen. Zodra de uitvoering is voltooid, u het geüploade bestand vinden onder`https://YourStorageAccountName.blob.core.windows.net/vmlogs`

   ```powershell
   .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
   ```

Het volgende is de definitie van de parameters die aan het script worden doorgegeven. (Dit is hieronder ook gekopieerd.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances,

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **ServiceName:** Uw naam van uw cloudservice.
* **Rollen:** een lijst met rollen, zoals 'WebRole1' of 'WorkerRole1'.
* **Instanties**: Een lijst met de namen van rolinstanties die zijn gescheiden door komma-- gebruik de tekenreeks joker ("*") voor alle rolinstanties.
* **Slot**: Naam van de sleuf. "Productie" of "Staging".
* **Modus**: Verzamelingsmodus. "Vol" of "GA".
* **StorageAccountName:** Naam van Azure-opslagaccount voor het opslaan van verzamelde gegevens.
* **StorageAccountKey:** naam van azure-opslagaccountsleutel.
* **AdditionalDataLocationList**: Een lijst van de volgende structuur:

  ```powershell
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="adding-as-a-vm-extension"></a>Toevoegen als VM-extensie
Volg de instructies om Azure PowerShell met uw abonnement te verbinden.

1. Geef de servicenaam, VM en de verzamelingsmodus op.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'YourCloudServiceName'

   #Specify the VM name
   $VMName = "'YourVMName'"

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"

   Specify the additional data folder for which files will be collected (this step is optional).

   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
        #more locations can be added....
   ```
  
2. Geef de naam van het Azure-opslagaccount en de sleutel op waarop verzamelde bestanden worden geüpload.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

3. Bel de SetAzureVMLogCollector.ps1 (inbegrepen aan het einde van het artikel) als volgt om de AzureLogCollector-extensie voor een Cloud Service in te schakelen. Zodra de uitvoering is voltooid, u het geüploade bestand vinden onder`https://YourStorageAccountName.blob.core.windows.net/vmlogs`

Het volgende is de definitie van de parameters die aan het script worden doorgegeven. (Dit is hieronder ook gekopieerd.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string] $VMName ,

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **ServiceName:** Uw naam van uw cloudservice.
* **VMName:** de naam van de VM.
* **Modus**: Verzamelingsmodus. "Vol" of "GA".
* **StorageAccountName:** Naam van Azure-opslagaccount voor het opslaan van verzamelde gegevens.
* **StorageAccountKey:** naam van azure-opslagaccountsleutel.
* **AdditionalDataLocationList**: Een lijst van de volgende structuur:

  ```
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="extention-powershell-script-files"></a>PowerShell Script-bestanden van Extention
### <a name="setazureservicelogcollectorps1"></a>SetAzureServiceLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances = '*',

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)

$publicConfig = New-Object PSObject

if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be separated by ,
{
  $instanceText = $Instances[0]
  for ($i = 1;$i -lt $Instances.Count;$i++)
  {
    $instanceText = $instanceText+ "," + $Instances[$i]
  }
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
}
else  #For all instances if not specified.  The value should be a space or *
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
}

if ($Mode -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
  "Container ($ContainerName) doesn't exist. Creating it now.."
  New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json
"publicConfig is:  $publicConfigJSON"

#we just provide an empty privateConfig object
$privateconfig = "{
}"

if ($Roles -ne $null)
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}
else
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}

#
#This is an optional step: generate a sasUri to the container so it can be shared with other people if needed.
#
$SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
$SasUri = $SasUri + "&restype=container&comp=list"
Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"
```

### <a name="setazurevmlogcollectorps1"></a>SetAzureVMLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
              [Parameter(Mandatory=$true)]
              [string]   $ServiceName,

              [Parameter(Mandatory=$false)]
              [string] $VMName ,

              [Parameter(Mandatory=$false)]
              [string]   $Mode = 'Full',

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountName,

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountKey,

              [Parameter(Mandatory=$false)]
              [PSObject[]] $AdditionDataLocationList = $null
        )

$publicConfig = New-Object PSObject
$publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"

if ($Mode -ne $null )
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
    "Container ($ContainerName) doesn't exist. Creating it now.."
    New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json

Write-Output "PublicConfiguration is: \r\n$publicConfigJSON"

#
#we just provide an empty privateConfig object
#
$privateconfig = "{
}"

if ($VMName -ne $null )
{
      $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
      $VM.VM.OSVirtualHardDisk.OS

      if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
      {
            Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose

            #
            #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
            #the presence of SubstatusList field.
            #
            $Completed = $false
            while ($Completed -ne $true)
            {
                    $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                    $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}

                    if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                    {
                        Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                          $Completed = $true
                    }
                    elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                    {
                          $Completed = $false
                          Write-Output "Waiting for operation to complete..."
                    }
                    else
                    {
                          $Completed = $true
                          Write-Output "Operation completed."

                    $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                          $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)

                  #
                        # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                          #
                          $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                          $ReadSasUri = New-AzStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context

                        Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"

                          #
                          #This is an optional step:  Remove the extension after we are done
                          #
                          Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose

                    }
                    Start-Sleep -s 5
            }
      }
      else
      {
          Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
      }

}
else
{
  Write-Output "VM name is not specified, the extension cannot be enabled"
}
```

## <a name="next-steps"></a>Volgende stappen
Nu u uw logboeken vanaf één eenvoudige locatie onderzoeken of kopiëren.

