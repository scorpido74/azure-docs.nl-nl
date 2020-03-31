---
title: Hoe de versleutelingsstatus voor Linux te verifiëren
description: In dit artikel vindt u instructies over het verifiëren van de versleutelingsstatus vanaf platform- en osniveau.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123427"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Hoe de versleutelingsstatus voor Linux te verifiëren 

**Dit scenario is van toepassing op ADE dual-pass en single-pass extensies.**  
Deze documentscope is het valideren van de versleutelingsstatus van een virtuele machine met behulp van verschillende methoden.

### <a name="environment"></a>Omgeving

- Linux-distributies

### <a name="procedure"></a>Procedure

Een virtuele machine is versleuteld met behulp van dual-pass of single-pass.

De versleutelingsstatus kan tijdens of na de versleuteling worden gevalideerd met behulp van verschillende methoden.

>[!NOTE] 
>We gebruiken variabelen in het hele document en vervangen de waarden dienovereenkomstig.

### <a name="verification"></a>Verificatie

De verificatie kan worden uitgevoerd vanuit de Portal, PowerShell, AZ CLI en, of van de VM OS kant. 

Deze verificatie kan worden gedaan door de schijven die aan een bepaalde VM zijn gekoppeld te controleren. 

Of door de versleutelingsinstellingen op elke afzonderlijke schijf op te vragen of de schijf is aangesloten of losgemaakt.

Hieronder de verschillende validatiemethoden:

## <a name="using-the-portal"></a>De portal gebruiken

Valideer de versleutelingsstatus door de sectie extensies op de Azure-portal te controleren.

In de sectie **Extensies** wordt de ADE-extensie weergegeven. 

Klik erop en neem een kijkje op de **status bericht,** zal het de huidige encryptie status aan te geven:

![Portal controle nummer 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

In de lijst met extensies ziet u de bijbehorende ADE-extensieversie. Versie 0.x komt overeen met ADE Dual-Pass en versie 1.x komt overeen met ADE Single-pass.

U meer informatie krijgen die op de extensie klikt en vervolgens op *De gedetailleerde status bekijken*.

U ziet een meer gedetailleerde status van het versleutelingsproces in json-indeling:

![Portal controle nummer 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Portal controle nummer 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Een andere manier om de versleutelingsstatus te valideren is door een kijkje te nemen in de sectie **Schijven.**

![Portal controle nummer 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Deze status betekent dat de schijven hebben encryptie-instellingen gestempeld, maar niet dat ze daadwerkelijk werden versleuteld op OS-niveau. Door het ontwerp, de schijven krijgen eerst gestempeld en later versleuteld. Als het versleutelingsproces mislukt, kunnen de schijven uiteindelijk gestempeld, maar niet versleuteld. Om te bevestigen of de schijven echt versleuteld zijn, u de versleuteling van elke schijf op OS-niveau dubbel controleren.

## <a name="using-powershell"></a>PowerShell gebruiken

U de **algemene** versleutelingsstatus van een versleutelde vm valideren met de volgende PowerShell-opdrachten:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![PowerShell 1 controleren](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

U de versleutelingsinstellingen van elke afzonderlijke schijf vastleggen met de volgende PowerShell-opdrachten:

### <a name="single-pass"></a>Single-Pass
Als de versleutelingsinstellingen met één pas op elk van de schijven (OS en Gegevens) worden stempelen, u de instellingen voor de versleuteling van de OS-schijf in één keer vastleggen:

``` powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![Os Single pass 01 verifiëren](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Als de schijf geen coderingsinstellingen heeft gestempeld, is de uitvoer leeg, zoals hieronder wordt weergegeven:

![OS-versleutelingsinstellingen 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Versleutelingsinstellingen voor gegevensschijf(s):

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Gegevens enkele ps 001 verifiëren](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Dual-Pass
In Dual Pass worden de versleutelingsinstellingen gestempeld in het VM-model en niet op elke afzonderlijke schijf.

Als u wilt controleren of de versleutelingsinstellingen in dual-pass zijn gestempeld, u de volgende opdrachten gebruiken:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![Controleer dual pass PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Niet-gekoppelde schijven

Controleer de versleutelingsinstellingen voor schijven die niet aan een virtuele machine zijn gekoppeld.

### <a name="managed-disks"></a>Managed Disks
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="using-az-cli"></a>AZ CLI gebruiken

U de **algemene** versleutelingsstatus van een versleutelde VM valideren met de volgende AZ CLI-opdrachten:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Algemene controle met CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Enkele pas
U de versleutelingsinstellingen van elke afzonderlijke schijf valideren met de volgende AZ CLI-opdrachten:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Instellingen voor gegevensversleuteling](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> In het geval dat de schijf geen coderingsinstellingen heeft gestempeld, wordt deze weergegeven als "Schijf is niet versleuteld"

Gedetailleerde instellingen voor status en versleuteling:

OS-schijf:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![OSSingleCLI (OSSingleCLI)](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Gegevensschijven:

```bash
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Gegevens single CLI ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Dubbele pas

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Algemene dubbele controleren ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) met CLI U ook de versleutelingsinstellingen controleren op het VM-modelopslagprofiel van de OS-schijf:

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Vm-profiel dubbel verifiëren met CLI ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Niet-gekoppelde schijven

Controleer de versleutelingsinstellingen voor schijven die niet aan een virtuele machine zijn gekoppeld.

### <a name="managed-disks"></a>Managed Disks

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Niet-beheerde schijven

Onbeheerde schijven zijn VHD-bestanden die zijn opgeslagen als paginablobs in Azure-opslagaccounts.

Om de details van een specifieke schijf te krijgen, moet u het gewenste overzicht opgeven:

De id van het opslagaccount dat de schijf bevat.
Een verbindingstekenreeks voor dat specifieke opslagaccount.
De naam van de container die de schijf opslaat.
De naam van de schijf.

Met deze opdracht worden alle iD's voor al uw opslagaccounts weergegeven:

```bash
az storage account list --query [].[id] -o tsv
```
De opslagaccount--geïdentificeerde gegevens worden in de volgende vorm vermeld:

/subscriptions/\<subscription id>/resourceGroups/\<resource group name>/providers/Microsoft.Storage/storageAccounts/\<storage account name>

Selecteer de juiste id en sla deze op een variabele op:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
De verbindingstekenreeks.

Met deze opdracht wordt de verbindingstekenreeks voor een bepaald opslagaccount opgezocht en wordt deze op een variabele opgeslagen:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

De containernaam.

In de volgende opdracht worden alle containers onder een opslagaccount weergegeven:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
De container die wordt gebruikt voor schijven wordt normaal gesproken "vhds" genoemd

De containernaam opslaan op een variabele 
```bash
ContainerName="name of the container"
```

De naam van de schijf.

Deze opdracht gebruiken om alle blobs op een bepaalde container weer te geven
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Kies de schijf die u wilt opvragen en sla de naam ervan op een variabele op.
```bash
DiskName="diskname.vhd"
```
De instellingen voor schijfversleuteling opvragen
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Van het OS
Valideren of de gegevensschijfpartities zijn versleuteld (en de os-schijf niet)

Wanneer een partitie/schijf is versleuteld, wordt deze weergegeven als **cryptetype,** wanneer deze niet is versleuteld, wordt deze weergegeven als **onderdeel/schijftype**

``` bash
lsblk
```

![Os Crypt-laag ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

U meer details met behulp van de volgende lsblk variant. 

U ziet een **crypttypelaag** die door de extensie is gemonteerd.

Het volgende voorbeeld toont logische volumes en normale schijven met een "**crypto\_LUKS FSTYPE**".

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Os Crypt laag 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Als extra stap u ook valideren of de gegevensschijf sleutels heeft geladen

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

En welke dm-apparaten worden vermeld als crypte

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Volgende stappen

- [Problemen met Azure Disk Encryption oplossen](disk-encryption-troubleshooting.md)
