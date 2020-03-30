---
title: Snelstart - Een back-up maken van een vm met PowerShell
description: In deze Quickstart leert u hoe u een back-up maakt van uw Azure virtuele machines met de Azure PowerShell-module.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.custom: mvc
ms.openlocfilehash: 8021ca553a1434c891bee911e85d351c61938594
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74171944"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Een back-up van een virtuele machine maken in Azure met PowerShell

De [Azure PowerShell AZ-module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts.

[Azure Backup](backup-overview.md) maakt een back-up van on-premises machines en apps en Azure VM's. In dit artikel ziet u hoe u een back-up maakt van een Azure VM met de AZ-module. U ook een back-up maken van een vm met de Azure CLI of in de [Azure-portal.](quick-backup-vm-portal.md) [Azure CLI](quick-backup-vm-cli.md)

Deze quickstart is bedoeld voor een back-up van een bestaande VM in Azure. Als u nog een VM wilt maken, kan dat ook [met Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Voor deze quickstart is de Azure PowerShell AZ-module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Inloggen en registreren

1. Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

    ```powershell
    Connect-AzAccount
    ```

2. De eerste keer dat u Azure Backup gebruikt, moet u de Azure Recovery Service-provider als volgt registreren in uw abonnement bij [Register-AzResourceProvider:](/powershell/module/az.Resources/Register-azResourceProvider)

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een [vault van Recovery Services](backup-azure-recovery-services-vault-overview.md) is een logische container die back-upgegevens opslaat voor beveiligde bronnen, zoals Azure VM's. Wanneer een back-uptaak wordt uitgevoerd, wordt een herstelpunt in de kluis van Herstelservices gemaakt. U kunt vervolgens een van deze herstelpunten gebruiken om gegevens voor dat tijdstip te herstellen.

Wanneer u de kluis maakt:

- Geef voor de resourcegroep en -locatie de resourcegroep en locatie op van de vm waarvan u een back-up wilt maken.
- Als u dit [voorbeeldscript](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) hebt gebruikt om de VM te maken, is de resourcegroep **myResourceGroup,** de VM is ***myVM**en bevinden de resources zich in de regio **West-Europa.**
- Azure Backup verwerkt automatisch opslag voor back-upgegevens. Standaard gebruikt de kluis [Geo-Redundant Storage (GRS).](../storage/common/storage-redundancy-grs.md) Georedundantie zorgt ervoor dat back-upgegevens worden gerepliceerd naar een secundair Azure-gebied, honderden kilometers verwijderd van de primaire regio.

Maak nu een kluis:

1. Gebruik de [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) om de kluis te maken:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Stel de kluiscontext in met [Set-AzRecoveryServicesVaultContext,](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext)als volgt:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Wijzig de opslagredundantieconfiguratie (LRS/GRS) van de kluis met [Set-AzRecoveryServicesBackupProperty,](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty)als volgt:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > Opslagredundantie kan alleen worden gewijzigd als er geen back-upitems zijn die zijn beveiligd met de kluis.

## <a name="enable-backup-for-an-azure-vm"></a>Back-up voor een virtuele Azure-machine inschakelen

U schakelt back-up in voor een Azure VM en geeft een back-upbeleid op.

- Het beleid bepaalt wanneer back-ups worden uitgevoerd en hoe lang herstelpunten die door de back-ups zijn gemaakt, moeten worden behouden.
- Met het standaardbeveiligingsbeleid wordt één keer per dag een back-up voor de VM uitgevoerd en worden de gemaakte herstelpunten gedurende 30 dagen bewaard. U dit standaardbeleid gebruiken om uw vm snel te beveiligen.

Back-up als volgt inschakelen:

1. Stel eerst het standaardbeleid in met [Get-AzRecoveryServicesBackupProtectionPolicy:](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. VM-back-up inschakelen met [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Geef het beleid, de resourcegroep en de VM-naam op.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>Een back-uptaak starten

Back-ups worden uitgevoerd volgens het schema dat is opgegeven in het back-upbeleid. U ook een on-demand back-up uitvoeren:

- De eerste back-uptaak zorgt voor een volledig herstelpunt.
- Na de eerste back-up maakt elke back-uptaak incrementele herstelpunten.
- Incrementele herstelpunten zijn efficiënt qua opslag en tijd aangezien ze alleen wijzigingen bevatten die sinds de laatste back-up zijn doorgevoerd.

Als u een back-up op aanvraag wilt uitvoeren, gebruikt u het [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

- U geeft een container op in de kluis met uw back-upgegevens met [Get-AzRecoveryServicesBackupContainer.](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer)
- Elke VM voor back-up wordt als een item beschouwd. Als u een back-uptaak wilt starten, krijgt u informatie over de VM met [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Voer een on-demand back-uptaak als volgt uit:

1. Geef de container op, verkrijg VM-informatie en voer de back-up uit.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Mogelijk moet u tot 20 minuten wachten, omdat de eerste back-uptaak een volledig herstelpunt creëert. Controleer de taak zoals beschreven in de volgende procedure.

## <a name="monitor-the-backup-job"></a>Uitvoering van back-uptaak volgen

1. Voer [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) uit om de status van de taak te controleren.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    Uitvoer is vergelijkbaar met het volgende voorbeeld, waarin de taak wordt weergegeven als **InProgress:**

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Wanneer de taakstatus is **voltooid,** wordt de VM beveiligd en is een volledig herstelpunt opgeslagen.

## <a name="clean-up-the-deployment"></a>De implementatie opschonen

Als u geen back-up meer van de VM hoeft te maken, u deze opschonen.

- Als u het herstellen van de VM wilt uitproberen, slaat u de opschonen over.
- Als u een bestaande vm hebt gebruikt, u de laatste cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) overslaan om de resourcegroep en VM op hun plaats te laten.

Schakel de beveiliging uit, verwijder de herstelpunten en de kluis. Verwijder vervolgens de brongroep en de bijbehorende VM-resources als volgt:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Recovery Services-kluis gemaakt, de beveiliging op een VM ingeschakeld en het eerste herstelpunt gemaakt.

- [Meer informatie over het](tutorial-backup-vm-at-scale.md) maken van back-ups van VM's in de Azure-portal.
- [Meer informatie over het](tutorial-restore-disk.md) snel herstellen van een virtuele machine
