---
title: 'Zelfstudie: Meerdere Azure VM-back-ups met PowerShell'
description: In deze zelfstudie wordt beschreven dat u een back-up maakt van meerdere Azure VM's naar een vault van Recovery Services met Azure PowerShell.
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc
ms.openlocfilehash: 154238eae78ce44b9fc91058e58d9a11e254c0f9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74171787"
---
# <a name="back-up-azure-vms-with-powershell"></a>Back-ups maken van Azure-VM's met behulp van PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In deze zelfstudie wordt beschreven hoe u een azure [backup](backup-overview.md) recovery services-kluis implementeert om een back-up te maken van meerdere Azure VM's met PowerShell.  

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Maak een vault van Recovery Services en stel de kluiscontext in.
> * Een back-upbeleid definiëren
> * Het back-upbeleid toepassen voor de beveiliging van meerdere virtuele machines
> * Activeer een on-demand back-uptaak voor de beveiligde virtuele machines Voordat u een back-up maken (of beschermen) van een virtuele machine, moet u de [vereisten](backup-azure-arm-vms-prepare.md) voltooien om uw omgeving voor te bereiden op het beschermen van uw VM's.

> [!IMPORTANT]
> In deze zelfstudie wordt ervan uitgegaan dat u al een resourcegroep en een virtuele machine van Azure hebt gemaakt.

## <a name="sign-in-and-register"></a>Inloggen en registreren

1. Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

    ```powershell
    Connect-AzAccount
    ```

2. De eerste keer dat u Azure Backup gebruikt, moet u de Azure Recovery Service-provider registreren in uw abonnement bij [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Als je je al hebt geregistreerd, sla je deze stap over.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een [vault van Recovery Services](backup-azure-recovery-services-vault-overview.md) is een logische container die back-upgegevens opslaat voor beveiligde bronnen, zoals Azure VM's. Wanneer een back-uptaak wordt uitgevoerd, wordt een herstelpunt in de kluis van Herstelservices gemaakt. U kunt vervolgens een van deze herstelpunten gebruiken om gegevens voor dat tijdstip te herstellen.

* In deze zelfstudie maakt u de kluis in dezelfde resourcegroep en -locatie als de vm waar u een back-up van wilt maken.
* Azure Backup verwerkt automatisch opslag voor back-upgegevens. Standaard gebruikt de kluis [Geo-Redundant Storage (GRS).](../storage/common/storage-redundancy-grs.md) Georedundantie zorgt ervoor dat back-upgegevens worden gerepliceerd naar een secundair Azure-gebied, honderden kilometers verwijderd van de primaire regio.

Maak de kluis als volgt:

1. Gebruik de [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)om de kluis te maken. Geef de naam en locatie van de resourcegroep op van de vm waarvan u een back-up wilt maken.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. Voor veel Azure Backup-cmdlets is het object Recovery Services-kluis als invoer vereist. Daarom is het handiger het object Backup Recovery Services-kluis in een variabele op te slaan.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. Stel de kluiscontext in met [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   * De context van de kluis is het type gegevens dat in de kluis wordt beveiligd.
   * Zodra de context is ingesteld, is deze van toepassing op alle volgende cmdlets

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Back-ups maken van Azure-VM's

Back-ups worden uitgevoerd volgens de planning die is opgegeven in het back-upbeleid. Als u een Recovery Services-kluis maakt, gaat deze gepaard met standaardbeleid voor beveiliging en retentie.

* Het standaardbeveiligingsbeleid activeert één keer per dag op een bepaald tijdstip een back-uptaak.
* Volgens het standaardbewaarbeleid wordt het dagelijkse herstelpunt gedurende dertig dagen bewaard.

Als we de Azure VM in deze zelfstudie wilt inschakelen en een back-up maken, gaan we als volgt te werk:

1. Geef een container op in de kluis met uw back-upgegevens met [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Elke VM voor back-up is een item. Als u een back-uptaak wilt starten, krijgt u informatie over de VM met [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Voer een on-demand back-up uit met[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).
    * De eerste back-uptaak zorgt voor een volledig herstelpunt.
    * Na de eerste back-up maakt elke back-uptaak incrementele herstelpunten.
    * Incrementele herstelpunten zijn efficiënt qua opslag en tijd aangezien ze alleen wijzigingen bevatten die sinds de laatste back-up zijn doorgevoerd.

Schakel de back-up als volgt in en voer deze uit:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Problemen oplossen

Als u problemen ondervindt terwijl u een back-up maakt van uw virtuele machine, controleert u dit [artikel over het oplossen van problemen.](backup-azure-vms-troubleshoot.md)

### <a name="deleting-a-recovery-services-vault"></a>Een Recovery Services-kluis verwijderen

Als u een kluis moet verwijderen, verwijdert u eerst herstelpunten in de kluis en vervolgens de kluis als volgt uit:

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Volgende stappen

* [Bekijk](backup-azure-vms-automation.md) een meer gedetailleerde walkthrough van het maken van back-ups en het herstellen van Azure VM's met PowerShell.
* [Azure VM's beheren en bewaken](backup-azure-manage-vms.md)
* [Azure-VM's herstellen](backup-azure-arm-restore-vms.md)
