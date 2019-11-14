---
title: Azure-quickstart - Een back-up van een VM maken met PowerShell
description: In deze Quick Start leert u hoe u een back-up van uw virtuele Azure-machines maakt met behulp van de module Azure PowerShell.
author: dcurwin
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 741c6c1f937988dcce41603417a1bc7dc95091cb
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073982"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Een back-up van een virtuele machine maken in Azure met PowerShell

De [Azure POWERSHELL AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) -module wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdracht regel of in scripts.

[Azure backup](backup-overview.md) maakt back-ups van on-premises machines en apps en Azure-vm's. In dit artikel wordt beschreven hoe u een back-up van een virtuele machine van Azure maakt met de module AZ. U kunt ook een back-up maken van een VM met behulp van de [Azure cli](quick-backup-vm-cli.md)of in de [Azure Portal](quick-backup-vm-portal.md).

Deze quickstart is bedoeld voor een back-up van een bestaande VM in Azure. Als u nog een VM wilt maken, kan dat ook [met Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Voor deze Quick start is de Azure PowerShell AZ module version 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Aanmelden en registreren

1. Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

    ```powershell
    Connect-AzAccount
    ```

2. De eerste keer dat u Azure Backup gebruikt, moet u als volgt de Azure Recovery service-provider registreren in uw abonnement met [REGI ster-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider):

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een [Recovery Services kluis](backup-azure-recovery-services-vault-overview.md) is een logische container waarmee back-upgegevens worden opgeslagen voor beveiligde bronnen, zoals virtuele Azure-machines. Wanneer een back-uptaak wordt uitgevoerd, wordt er een herstel punt in de Recovery Services kluis gemaakt. U kunt vervolgens een van deze herstelpunten gebruiken om gegevens voor dat tijdstip te herstellen.

Bij het maken van de kluis:

- Geef voor de resource groep en-locatie de resource groep en de locatie op van de virtuele machine waarvan u een back-up wilt maken.
- Als u dit [voorbeeld script](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) hebt gebruikt om de virtuele machine te maken, is de resource groep **myResourceGroup**, de virtuele machine is ***myVM**en de resources bevinden zich in de **Europa West** -regio.
- Azure Backup beheert automatisch de opslag voor back-ups van gegevens. De kluis maakt standaard gebruik van [geo-redundante opslag (GRS)](../storage/common/storage-redundancy-grs.md). Geo-redundantie zorgt ervoor dat back-ups van gegevens worden gerepliceerd naar een secundaire Azure-regio en honderden kilo meters van de primaire regio worden verwijderd.

Maak nu een kluis:

1. Gebruik de [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) om de kluis te maken:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Stel de kluis context in met [set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext), als volgt:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Wijzig de opslag redundantie configuratie (LRS/GRS) van de kluis met [set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty), als volgt:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > Opslag redundantie kan alleen worden gewijzigd als er geen back-upitems zijn die zijn beveiligd met de kluis.

## <a name="enable-backup-for-an-azure-vm"></a>Back-up voor een virtuele Azure-machine inschakelen

U kunt back-up voor een virtuele Azure-machine inschakelen en een back-upbeleid opgeven.

- Het beleid definieert wanneer back-ups worden uitgevoerd en hoe lang de herstel punten moeten worden bewaard die door de back-ups worden gemaakt.
- In het standaard beveiligings beleid wordt één keer per dag een back-up uitgevoerd en worden de gemaakte herstel punten gedurende 30 dagen bewaard. U kunt dit standaard beleid gebruiken om uw virtuele machine snel te beveiligen.

Schakel back-up als volgt in:

1. Stel eerst het standaard beleid in met [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Schakel VM-back-up in met [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Geef het beleid, de resource groep en de naam van de virtuele machine op.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>Een back-uptaak starten

Back-ups worden uitgevoerd volgens de planning die is opgegeven in het back-upbeleid. U kunt ook een back-up op aanvraag uitvoeren:

- Met de eerste eerste back-uptaak wordt een volledig herstel punt gemaakt.
- Na de eerste back-up worden met elke back-uptaak incrementele herstel punten gemaakt.
- Incrementele herstelpunten zijn efficiënt qua opslag en tijd aangezien ze alleen wijzigingen bevatten die sinds de laatste back-up zijn doorgevoerd.

Als u een back-up op aanvraag wilt uitvoeren, gebruikt u de [back-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

- U geeft een container op in de kluis die uw back-upgegevens bevat met [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Elke VM voor back-up wordt als een item beschouwd. Als u een back-uptaak wilt starten, haalt u informatie op over de virtuele machine met [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Voer een back-uptaak op aanvraag als volgt uit:

1. Geef de container op, haal de VM-informatie op en voer de back-up uit.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Mogelijk moet u Maxi maal 20 minuten wachten, omdat de eerste back-uptaak een volledig herstel punt maakt. Controleer de taak zoals beschreven in de volgende procedure.

## <a name="monitor-the-backup-job"></a>Uitvoering van back-uptaak volgen

1. Voer [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) uit om de taak status te controleren.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    Uitvoer is vergelijkbaar met het volgende voor beeld, waarin de taak wordt weer gegeven als **InProgress**:

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Wanneer de taak status **voltooid**is, wordt de virtuele machine beveiligd en is er een volledig herstel punt opgeslagen.

## <a name="clean-up-the-deployment"></a>De implementatie opschonen

Als u een back-up van de virtuele machine niet meer nodig hebt, kunt u deze opschonen.

- Als u de virtuele machine opnieuw wilt proberen te herstellen, slaat u de opschoon bewerking over.
- Als u een bestaande virtuele machine hebt gebruikt, kunt u de laatste [Remove-AzResourceGroup-](/powershell/module/az.resources/remove-azresourcegroup) cmdlet overs Laan om de resource groep en de VM op locatie te laten staan.

Schakel de beveiliging uit, verwijder de herstel punten en de kluis. Verwijder vervolgens de resource groep en de bijbehorende VM-resources als volgt:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Recovery Services-kluis gemaakt, de beveiliging op een VM ingeschakeld en het eerste herstelpunt gemaakt.

- [Meer informatie over](tutorial-backup-vm-at-scale.md) het maken van een back-up van virtuele machines in de Azure Portal.
- [Meer informatie over hoe](tutorial-restore-disk.md) u een virtuele machine snel kunt herstellen
