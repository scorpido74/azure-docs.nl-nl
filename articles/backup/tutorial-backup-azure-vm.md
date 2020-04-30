---
title: 'Zelf studie: meerdere Azure VM-back-ups met Power shell'
description: Deze zelf studie bevat informatie over het maken van back-ups van meerdere virtuele Azure-machines naar een Recovery Services kluis met behulp van Azure PowerShell
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc
ms.openlocfilehash: 154238eae78ce44b9fc91058e58d9a11e254c0f9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74171787"
---
# <a name="back-up-azure-vms-with-powershell"></a>Back-ups maken van Azure-VM's met behulp van PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In deze zelf studie wordt beschreven hoe u een [Azure Backup](backup-overview.md) Recovery Services kluis implementeert om een back-up te maken van meerdere virtuele Azure-machines met Power shell.  

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Maak een Recovery Services kluis en stel de kluis context in.
> * Een back-upbeleid definiëren
> * Het back-upbeleid toepassen voor de beveiliging van meerdere virtuele machines
> * Een back-uptaak op aanvraag activeren voor de beveiligde virtuele machines voordat u een back-up van een virtuele machine kunt maken (of beveiligen), moet u de [vereisten](backup-azure-arm-vms-prepare.md) volt ooien om uw omgeving voor te bereiden voor het beveiligen van uw vm's.

> [!IMPORTANT]
> In deze zelfstudie wordt ervan uitgegaan dat u al een resourcegroep en een virtuele machine van Azure hebt gemaakt.

## <a name="sign-in-and-register"></a>Aanmelden en registreren

1. Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

    ```powershell
    Connect-AzAccount
    ```

2. De eerste keer dat u Azure Backup gebruikt, moet u de Azure Recovery service-provider registreren in uw abonnement met [REGI ster-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Als u zich al hebt geregistreerd, slaat u deze stap over.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een [Recovery Services kluis](backup-azure-recovery-services-vault-overview.md) is een logische container waarmee back-upgegevens worden opgeslagen voor beveiligde bronnen, zoals virtuele Azure-machines. Wanneer een back-uptaak wordt uitgevoerd, wordt er een herstel punt in de Recovery Services kluis gemaakt. U kunt vervolgens een van deze herstelpunten gebruiken om gegevens voor dat tijdstip te herstellen.

* In deze zelf studie maakt u de kluis in dezelfde resource groep en locatie als de virtuele machine waarvan u een back-up wilt maken.
* Azure Backup beheert automatisch de opslag voor back-ups van gegevens. De kluis maakt standaard gebruik van [geo-redundante opslag (GRS)](../storage/common/storage-redundancy-grs.md). Geo-redundantie zorgt ervoor dat back-ups van gegevens worden gerepliceerd naar een secundaire Azure-regio en honderden kilo meters van de primaire regio worden verwijderd.

Maak de kluis als volgt:

1. Gebruik de [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)om de kluis te maken. Geef de naam van de resource groep en de locatie op van de virtuele machine waarvan u een back-up wilt maken.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. Voor veel Azure Backup-cmdlets is het object Recovery Services-kluis als invoer vereist. Daarom is het handiger het object Backup Recovery Services-kluis in een variabele op te slaan.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. Stel de kluis context in met [set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   * De context van de kluis is het type gegevens dat in de kluis wordt beveiligd.
   * Zodra de context is ingesteld, is deze van toepassing op alle volgende cmdlets

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Back-ups maken van Azure-VM's

Back-ups worden uitgevoerd volgens de planning die is opgegeven in het back-upbeleid. Als u een Recovery Services-kluis maakt, gaat deze gepaard met standaardbeleid voor beveiliging en retentie.

* Het standaard beveiligings beleid activeert een back-uptaak één keer per dag op een opgegeven tijdstip.
* Volgens het standaardbewaarbeleid wordt het dagelijkse herstelpunt gedurende dertig dagen bewaard.

Ga als volgt te werk om een back-up van de virtuele Azure-machine in deze zelf studie in te scha kelen:

1. Geef een container in de kluis op die uw back-upgegevens bevat met [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Elke VM voor back-up is een item. Als u een back-uptaak wilt starten, haalt u informatie op over de virtuele machine met [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Voer een back-up op aanvraag uit met[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).
    * Met de eerste eerste back-uptaak wordt een volledig herstel punt gemaakt.
    * Na de eerste back-up worden met elke back-uptaak incrementele herstel punten gemaakt.
    * Incrementele herstelpunten zijn efficiënt qua opslag en tijd aangezien ze alleen wijzigingen bevatten die sinds de laatste back-up zijn doorgevoerd.

Schakel de back-up als volgt in en uit:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Problemen oplossen

Als u problemen ondervindt tijdens het maken van een back-up van de virtuele machine, raadpleegt u dit [artikel over probleem oplossing](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Een Recovery Services-kluis verwijderen

Als u een kluis moet verwijderen, verwijdert u eerst herstel punten in de kluis en maakt u de registratie van de kluis als volgt ongedaan:

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Volgende stappen

* [Bekijk](backup-azure-vms-automation.md) een meer gedetailleerd overzicht van het maken van back-ups en het herstellen van virtuele Azure-machines met Power shell.
* [Virtuele Azure-machines beheren en bewaken](backup-azure-manage-vms.md)
* [Azure-VM's herstellen](backup-azure-arm-restore-vms.md)
