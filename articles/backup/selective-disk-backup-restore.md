---
title: Back-up en herstel van selectieve schijven voor virtuele Azure-machines
description: In dit artikel vindt u informatie over selectieve back-ups en herstel met behulp van de back-upoplossing van Azure virtual machine.
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: references_regions
ms.openlocfilehash: dd4691f6248099bdc4fa713c84d396adac6011fa
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757435"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>Back-up en herstel van selectieve schijven voor virtuele Azure-machines

Azure Backup biedt ondersteuning voor het maken van een back-up van alle schijven (besturings systeem en gegevens) in een virtuele machine met behulp van de back-upoplossing voor VM'S. Nu kunt u, met behulp van de functies voor het maken en herstellen van selectieve schijven, een back-up maken van een subset van de gegevens schijven in een VM. Dit biedt een efficiënte en rendabele oplossing voor uw back-up-en herstel behoeften. Elk herstel punt bevat alleen de schijven die zijn opgenomen in de back-upbewerking. Hierdoor kunt u tijdens de herstel bewerking een subset van schijven herstellen die zijn hersteld vanaf het opgegeven herstel punt. Dit is van toepassing op beide herstel bewerkingen van moment opnamen en de kluis.

>[!NOTE]
>Het maken van back-ups en herstellen van selectieve schijven voor virtuele Azure-machines is in alle regio's open bare preview.

## <a name="scenarios"></a>Scenario's

Deze oplossing is vooral nuttig in de volgende scenario's:

1. Als u essentiële gegevens hebt waarvan u een back-up wilt maken op slechts één schijf, of een subset van de schijven en geen back-up wilt maken van de rest van de schijven die zijn gekoppeld aan een virtuele machine om de opslag kosten voor back-ups te minimaliseren.
2. Als u andere back-upoplossingen hebt voor een deel van uw VM of gegevens. Als u bijvoorbeeld een back-up maakt van uw data bases of gegevens met een andere back-up van de werk belasting en u Azure VM-niveau back-ups wilt gebruiken voor de rest van de gegevens of schijven, kunt u een efficiënt en robuust systeem bouwen met behulp van de beste mogelijkheden die beschikbaar zijn.

Met Power shell of Azure CLI kunt u selectieve schijf back-ups van de Azure-VM configureren.  Met behulp van een script kunt u gegevens schijven opnemen of uitsluiten met behulp van hun LUN-nummers.  Op dit moment is de mogelijkheid om de back-up van selectieve schijven via de Azure Portal te configureren beperkt tot de optie **alleen back-Upbesturingssysteem schijf** . U kunt dus back-ups van uw Azure-VM configureren met de besturingssysteem schijf en alle gekoppelde gegevens schijven uitsluiten.

>[!NOTE]
> De besturingssysteem schijf is standaard toegevoegd aan de back-up van de virtuele machine en kan niet worden uitgesloten.

## <a name="using-azure-cli"></a>Azure CLI gebruiken

Zorg ervoor dat u AZ CLI versie 2.0.80 of hoger gebruikt. U kunt de CLI-versie ophalen met de volgende opdracht:

```azurecli
az --version
```

Meld u aan bij de abonnement-ID waarin de Recovery Services kluis en de virtuele machine zich bevindt:

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>In elke onderstaande opdracht is alleen de naam van de **ResourceGroup** (niet het object) die overeenkomt met de kluis vereist.

### <a name="configure-backup-with-azure-cli"></a>Back-up configureren met Azure CLI

Tijdens het configureren van de beveiliging moet u de schijf lijst instelling opgeven met een **inclusion**  /  **uitsluitings** parameter opnemen, zodat de LUN-nummers van de schijven in de back-up worden opgenomen of uitgesloten.

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

Als de virtuele machine zich niet in dezelfde resource groep bevindt als de kluis, verwijst **ResourceGroup** naar de resource groep waar de kluis is gemaakt. Geef in plaats van de naam van de virtuele machine de VM-ID op zoals hieronder wordt aangegeven.

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id | tr -d '"') --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>Beveiliging wijzigen voor al back-ups van Vm's met Azure CLI

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>Alleen back-ups van besturingssysteem schijf tijdens configureren van back-up met Azure CLI

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} -- exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>Alleen back-upschijf van het besturings systeem tijdens het wijzigen van de beveiliging met Azure CLI

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>Schijven herstellen met Azure CLI

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-to-staging-storage-account --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>Alleen besturingssysteem schijf herstellen met Azure CLI

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-to-staging-storage-account --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>Beveiligd item ophalen om details van schijf uitsluitingen op te halen met Azure CLI

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

Er is een aanvullende **diskExclusionProperties** -para meter toegevoegd aan het beveiligde item, zoals hieronder wordt weer gegeven:

```azurecli
"extendedProperties": {
      "diskExclusionProperties": {
        "diskLunList": [
          0,
          1
        ],
        "isInclusionList": true
      }
```

### <a name="get-backup-job-with-azure-cli"></a>Back-uptaak ophalen met Azure CLI

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

Met deze opdracht worden de details van de schijven waarvan een back-up is gemaakt en uitgesloten schijven, zoals hieronder weer gegeven:

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

### <a name="list-recovery-points-with-azure-cli"></a>Herstel punten weer geven met Azure CLI

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

Dit geeft de informatie over het aantal schijven dat is gekoppeld en waarvan een back-up is gemaakt in de VM.

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>Herstel punt ophalen met Azure CLI

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

Elk herstel punt bevat de gegevens van de opgenomen en uitgesloten schijven:

```azurecli
  "recoveryPointDiskConfiguration": {
      "excludedDiskList": [
        {
          "lun": 2,
          "name": "diskextest_DataDisk_2"
        }
      ],
      "includedDiskList": [
        {
          "lun": -1,
          "name": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b"
        },
        {
          "lun": 0,
          "name": "diskextest_DataDisk_0"
        },
        {
          "lun": 1,
          "name": "diskextest_DataDisk_1"
        }
      ],
      "numberOfDisksAttachedToVm": 4,
      "numberOfDisksIncludedInBackup": 3
```

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>Instellingen voor schijf uitsluiting verwijderen en beveiligd item ophalen met Azure CLI

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

Wanneer u deze opdrachten uitvoert, ziet u `"diskExclusionProperties": null` .

## <a name="using-powershell"></a>PowerShell gebruiken

Zorg ervoor dat u Azure versie 3.7.0 of hoger gebruikt.

### <a name="enable-backup-with-powershell"></a>Back-up inschakelen met Power shell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -DiskListSetting "Include"/"Exclude" -DisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>Alleen back-ups van besturingssysteem schijf tijdens configureren van back-up met Power shell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>Object voor back-upitem ophalen dat moet worden door gegeven in beveiliging wijzigen met Power shell

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $Vault.ID -FriendlyName "V2VM"
```

U moet het hierboven verkregen **$item** -object door geven aan de para meter **– item** in de volgende cmdlets.

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>Beveiliging wijzigen voor al back-ups van Vm's met Power shell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -DiskListSetting "Include"/"Exclude" -DisksList[Strings]   -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>Alleen back-ups van de besturingssysteem schijf tijdens het wijzigen van de beveiliging met Power shell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>Instelling voor schijf uitsluiting opnieuw instellen met Power shell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -DiskListSetting "Reset" -VaultId $targetVault.ID
```

### <a name="restore-selective-disks-with-powershell"></a>Selectieve schijven herstellen met Power shell

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [Strings]
```

### <a name="restore-only-os-disk-with-powershell"></a>Alleen besturingssysteem schijf herstellen met Power shell

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Met behulp van de Azure Portal kunt u de opgenomen en uitgesloten schijven bekijken in het deel venster Details van de VM-back-up en het detail venster met de back-uptaak.  Wanneer u tijdens het herstellen het herstel punt selecteert waaruit u wilt herstellen, kunt u de back-upschijven in dat herstel punt bekijken.

Hier kunt u de opgenomen en uitgesloten schijven voor een virtuele machine in de portal bekijken in het deel venster Details van VM-back-up:

![Opgenomen en uitgesloten schijven weer geven uit het deel venster met back-updetails](./media/selective-disk-backup-restore/backup-details.png)

Hier kunt u de opgenomen en uitgesloten schijven weer geven in een back-up vanuit het deel venster met taak Details:

![Opgenomen en uitgesloten schijven in het deel venster taak details weer geven](./media/selective-disk-backup-restore/job-details.png)

Hier kunt u de back-ups van schijven weer geven tijdens het herstellen, wanneer u het herstel punt selecteert waaruit u wilt herstellen:

![Back-ups van schijven weer geven tijdens het herstellen](./media/selective-disk-backup-restore/during-restore.png)

Het configureren van de back-upervaring voor selectieve schijven voor een virtuele machine via de Azure Portal is beperkt tot de optie **alleen back-Upbesturingssysteem schijf** . Gebruik Power shell of Azure CLI om de back-up van selectieve schijven te gebruiken op al een back-up van de virtuele machine of voor een geavanceerde opname of uitsluiting van specifieke gegevens schijven van een virtuele machine.

>[!NOTE]
>Als gegevens van elkaar zijn verdeeld over schijven, zorgt u ervoor dat alle afhankelijke schijven zijn opgenomen in de back-up. Als u geen back-up maakt van alle afhankelijke schijven in een volume, wordt tijdens het herstellen het volume dat bestaat uit een aantal schijven met niet-back-ups, niet gemaakt.

### <a name="backup-os-disk-only-in-the-azure-portal"></a>Alleen back-upbesturingssysteem schijf in de Azure Portal

Wanneer u back-up met Azure Portal inschakelt, kunt u de optie **alleen back-Upbesturingssysteem schijf** kiezen. U kunt dus back-ups van uw Azure-VM configureren met de besturingssysteem schijf en alle gekoppelde gegevens schijven uitsluiten.

![Back-up alleen configureren voor de besturingssysteem schijf](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="selective-disk-restore"></a>Selectief schijf herstel

Selectief schijf herstel is een extra functionaliteit die u krijgt wanneer u de functie Selectieve schijven back-up inschakelt. Met deze functionaliteit kunt u selectieve schijven herstellen vanaf alle schijven waarvan een back-up is gemaakt in een herstel punt. Het is efficiënter en helpt tijd te besparen in scenario's waarin u weet welke van de schijven moeten worden hersteld.

- De besturingssysteem schijf is standaard opgenomen in de back-up en herstel bewerking van de virtuele machine en kan niet worden uitgesloten.
- Het terugzetten van selectieve schijven wordt alleen ondersteund voor herstel punten die zijn gemaakt nadat de functie schijf uitsluiting is ingeschakeld.
- Back-ups met de instelling voor het uitsluiten van schijven bieden alleen ondersteuning **voor** de optie **schijf herstellen** . **VM herstellen** of **vervangen bestaande** herstel opties worden in dit geval niet ondersteund.

![De optie voor het herstellen van de VM en het vervangen ervan zijn niet beschikbaar tijdens de herstel bewerking](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>Beperkingen

De back-upfunctie voor selectieve schijven wordt niet ondersteund voor klassieke virtuele machines en versleutelde virtuele machines. Azure-Vm's die zijn versleuteld met Azure Disk Encryption (ADE) met behulp van BitLocker voor versleuteling van Windows VM en de DM-cryptografie functie voor Linux-Vm's worden niet ondersteund.

De herstel opties voor het maken van een **nieuwe virtuele machine** en **vervangen** , worden niet ondersteund voor de virtuele machine waarvoor selectieve schijven back-upfunctie is ingeschakeld.

## <a name="billing"></a>Billing

Back-ups van virtuele Azure-machines volgen het bestaande prijs model, [in detail beschreven](https://azure.microsoft.com/pricing/details/backup/).

De kosten voor het **beveiligde exemplaar (PI)** worden alleen berekend voor de besturingssysteem schijf als u een back-up wilt maken met behulp van de optie **alleen de besturingssysteem schijf** .  Als u back-up configureert en ten minste één gegevens schijf selecteert, worden de PI-kosten berekend voor alle schijven die aan de virtuele machine zijn gekoppeld. De **kosten voor back-upopslag** worden berekend op basis van alleen de schijven die zijn opgenomen, en u kunt besparen op de opslag kosten. De **kosten voor moment opnamen** worden altijd berekend voor alle schijven in de virtuele machine (zowel de opgenomen als de uitgesloten schijven).  

## <a name="next-steps"></a>Volgende stappen

- [Ondersteuningsmatrix voor back-up van Azure-VM](backup-support-matrix-iaas.md)
- [Veelgestelde vragen: back-ups maken van virtuele Azure-machines](backup-azure-vm-backup-faq.md)
