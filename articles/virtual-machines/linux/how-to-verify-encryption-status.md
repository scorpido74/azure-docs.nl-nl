---
title: De versleutelings status voor Linux controleren
description: In dit artikel vindt u instructies voor het controleren van de versleutelings status van platform-en besturingssysteem niveau.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80123427"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>De versleutelings status voor Linux controleren 

**Dit scenario is van toepassing op een ADE-extensie met dubbele Pass-en single-pass-extensies.**  
Dit document bereik is het valideren van de versleutelings status van een virtuele machine met behulp van verschillende methoden.

### <a name="environment"></a>Omgeving

- Linux-distributies

### <a name="procedure"></a>Procedure

Een virtuele machine is versleuteld met behulp van twee slagen of eenmalige Pass.

De versleutelings status kan worden gevalideerd tijdens of na de versleuteling met behulp van verschillende methoden.

>[!NOTE] 
>We gebruiken variabelen in het hele document. Vervang de waarden dienovereenkomstig.

### <a name="verification"></a>Verificatie

De verificatie kan worden uitgevoerd vanuit de portal, Power shell, AZ CLI en, of vanuit de VM-besturingssysteem kant. 

Deze verificatie kan worden uitgevoerd door de schijven te controleren die aan een bepaalde virtuele machine zijn gekoppeld. 

Of door de versleutelings instellingen op elke afzonderlijke schijf op te vragen, ongeacht of de schijf is gekoppeld of niet is gekoppeld.

Onder de verschillende validatie methoden:

## <a name="using-the-portal"></a>De portal gebruiken

Valideer de versleutelings status door de sectie extensies op het Azure Portal te controleren.

In de sectie **extensies** ziet u de ade-extensie die wordt weer gegeven. 

Klik hierop om het **status bericht**te bekijken en de huidige versleutelings status op te geven:

![Controle nummer 1 van portal](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

In de lijst met extensies ziet u de bijbehorende versie van de ADE-extensie. Versie 0. x komt overeen met ADE Dual-Pass en versie 1. x komt overeen met een eenmalige wachtwoord-ADE.

U kunt meer informatie krijgen door te klikken op de uitbrei ding en vervolgens op *gedetailleerde status weer geven*.

U ziet een gedetailleerdere status van het versleutelings proces in JSON-indeling:

![Controle nummer 2 van portal](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Portal-controle nummer 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Een andere manier om de versleutelings status te valideren is door de sectie **schijven** te bekijken.

![Portal controle nummer 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Deze status betekent dat de schijven versleutelings instellingen hebben stempel, maar niet dat ze daad werkelijk zijn versleuteld op het niveau van het besturings systeem. Op basis van het ontwerp worden de schijven eerst gestempeld en later versleuteld. Als het versleutelings proces mislukt, kunnen de schijven een stempel hebben, maar niet versleuteld. Als u wilt controleren of de schijven echt zijn versleuteld, kunt u de versleuteling van elke schijf controleren op het niveau van het besturings systeem.

## <a name="using-powershell"></a>PowerShell gebruiken

U kunt de **algemene** versleutelings status van een versleutelde virtuele machine valideren met behulp van de volgende Power shell-opdrachten:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![Power shell 1 controleren](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

U kunt de versleutelings instellingen van elke afzonderlijke schijf vastleggen met behulp van de volgende Power shell-opdrachten:

### <a name="single-pass"></a>Eenmalige Pass
Als single pass, de versleutelings instellingen op elk van de schijven (besturings systeem en gegevens) zijn, kunt u de versleutelings instellingen voor het besturings systeem als volgt vastleggen in één keer:

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
![Eenmalige pass van het besturings systeem controleren 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Als de schijf geen versleutelings instellingen heeft, is de uitvoer leeg, zoals hieronder wordt weer gegeven:

![Instellingen voor versleuteling van besturings systeem 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Versleutelings instellingen voor gegevens schijven vastleggen:

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
![Gegevens van één PS 001 controleren](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Dual Pass
In Dual Pass worden de versleutelings instellingen stempel in het VM-model en niet op elke afzonderlijke schijf.

U kunt de volgende opdrachten gebruiken om te controleren of de versleutelings instellingen zijn voorzien van een dubbele keer:

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
![Dubbele Pass Power shell 1 controleren](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Niet-gekoppelde schijven

Controleer de versleutelings instellingen voor schijven die niet zijn gekoppeld aan een virtuele machine.

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
## <a name="using-az-cli"></a>Met AZ CLI

U kunt de **algemene** versleutelings status van een versleutelde virtuele machine valideren met behulp van de volgende AZ cli-opdrachten:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Algemeen controleren met CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Eenmalige Pass
U kunt de versleutelings instellingen van elke afzonderlijke schijf valideren met behulp van de volgende AZ CLI-opdrachten:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Instellingen voor gegevens versleuteling](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Als de schijf geen versleutelings instellingen heeft, wordt deze weer gegeven als ' schijf is niet versleuteld '

Gedetailleerde status-en versleutelings instellingen:

BESTURINGSSYSTEEM schijf:

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

![OSSingleCLI](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Gegevens schijven:

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

![Gegevens-single CLI ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Dubbele Pass

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Controleer algemene dubbele met CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) u kunt ook de versleutelings instellingen controleren op het VM-model opslag profiel van de besturingssysteem schijf:

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

![VM-profiel verifiëren, dubbel met CLI ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Niet-gekoppelde schijven

Controleer de versleutelings instellingen voor schijven die niet zijn gekoppeld aan een virtuele machine.

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

Onbeheerde schijven zijn VHD-bestanden die zijn opgeslagen als pagina-blobs in azure-opslag accounts.

Als u de details van een specifieke schijf wilt weer geven, moet u het volgende opgeven:

De ID van het opslag account dat de schijf bevat.
Een connection string voor dat specifieke opslag account.
De naam van de container waarin de schijf wordt opgeslagen.
De naam van de schijf.

Met deze opdracht worden alle Id's voor al uw opslag accounts weer gegeven:

```bash
az storage account list --query [].[id] -o tsv
```
De Id's van het opslag account worden in de volgende vorm weer gegeven:

/Subscriptions/\<-abonnements-id\<> naam van/resourcegroups/-\<resource groep>/providers/Microsoft.Storage/storageaccounts/-opslag account>

Selecteer de juiste ID en sla deze op in een variabele:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
Het connection string.

Met deze opdracht wordt de connection string voor een bepaald opslag account opgehaald en opgeslagen in een variabele:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

De naam van de container.

Met de volgende opdracht worden alle containers onder een opslag account weer gegeven:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
De container die wordt gebruikt voor schijven heet meestal vhd's

De container naam opslaan op een variabele 
```bash
ContainerName="name of the container"
```

De naam van de schijf.

Gebruik deze opdracht om alle blobs in een bepaalde container weer te geven
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Kies de schijf die u wilt doorzoeken en sla de naam op in een variabele.
```bash
DiskName="diskname.vhd"
```
Query's uitvoeren op de instellingen voor schijf versleuteling
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Van het besturings systeem
Valideren of de partities met de gegevens schijf zijn versleuteld (en de besturingssysteem schijf is niet)

Wanneer een partitie/schijf wordt versleuteld, wordt deze weer gegeven als het **crypt** -type, wanneer deze niet wordt versleuteld als **onderdeel/schijf** type

``` bash
lsblk
```

![Cryptografie-laag van besturings systeem ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

U kunt meer informatie krijgen met behulp van de volgende ' lsblk-variant. 

U ziet een **crypt** -type-laag die wordt gekoppeld door de extensie.

In het volgende voor beeld ziet u logische volumes en normale schijven met een "**\_crypto luks FSTYPE**".

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![OS crypt-laag 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Als extra stap kunt u ook valideren of de gegevens schijf sleutels heeft geladen

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

En welke DM-apparaten worden vermeld als crypt

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Volgende stappen

- [Problemen met Azure Disk Encryption oplossen](disk-encryption-troubleshooting.md)
