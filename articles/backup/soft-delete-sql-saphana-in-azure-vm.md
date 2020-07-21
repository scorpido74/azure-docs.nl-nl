---
title: Voorlopig verwijderen voor SQL Server in azure VM en SAP HANA in werk belastingen voor Azure VM
description: Meer informatie over hoe zacht verwijderen voor SQL Server in azure VM en SAP HANA in azure VM-workloads maakt back-ups veiliger.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c0eaedea2d5428376befaade42f87348cf84e7bc
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538187"
---
# <a name="soft-delete-for-sql-server-in-azure-vm-and-sap-hana-in-azure-vm-workloads"></a>Voorlopig verwijderen voor SQL Server in azure VM en SAP HANA in werk belastingen voor Azure VM

Azure Backup biedt nu zacht verwijderen voor SQL Server in azure VM en SAP HANA in azure VM-workloads. Dit is een aanvulling op het al ondersteunde [scenario voor het voorlopig verwijderen van een virtuele machine van Azure](soft-delete-virtual-machines.md).

[Zacht verwijderen](backup-azure-security-feature-cloud.md) is een beveiligings functie die u helpt bij het beveiligen van back-upgegevens, zelfs na het verwijderen. Met zacht verwijderen, zelfs als een schadelijke actor de back-up van een Data Base verwijdert (of als er per ongeluk back-upgegevens worden verwijderd), worden de back-upgegevens 14 extra dagen bewaard. Hiermee kan het back-upitem worden hersteld zonder gegevens verlies. Bij deze extra Bewaar periode van 14 dagen van de back-upgegevens in de status ' voorlopig verwijderen ' worden er geen kosten in rekening gebracht voor de klant.

>[!NOTE]
>Zodra de preview-versie voor een abonnement is ingeschakeld, is het niet mogelijk om alleen de optie voorlopig verwijderen uit te scha kelen voor SQL Server of SAP HANA Db's terwijl de virtuele machines in dezelfde kluis ingeschakeld blijven. U kunt afzonderlijke kluizen maken voor gedetailleerde controle.

## <a name="steps-to-enroll-in-preview"></a>Stappen voor het inschrijven van de preview-versie

1. Meld u aan bij uw Azure-account.

   ```powershell
   Login-AzureRmAccount
   ```

2. Selecteer het abonnement dat u wilt inschrijven in de preview-versie:

   ```powershell
   Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
   ```

3. Dit abonnement registreren bij het preview-programma:

   ```powershell
   Register-AzureRMProviderFeature -FeatureName WorkloadBackupSoftDelete -ProviderNamespace Microsoft.RecoveryServices
   ```

4. Wacht 30 minuten totdat het abonnement is inge schreven in de preview-versie.

5. Voer de volgende cmdlets uit om de status te controleren:

   ```powershell
   Get-AzureRmProviderFeature -FeatureName WorkloadBackupSoftDelete -ProviderNamespace Microsoft.RecoveryServices
   ```

6. Wanneer het abonnement wordt weer gegeven als geregistreerd, voert u de volgende opdracht uit:

   ```powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

>[!NOTE]
>Telkens wanneer een nieuwe kluis/kluis wordt gemaakt onder het abonnement voor voorlopig verwijderen, moet de volgende opdracht opnieuw worden uitgevoerd om de functie in te scha kelen voor de nieuwe kluizen.<BR>
> `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

## <a name="soft-delete-for-sql-server-in-azure-vm-using-azure-portal"></a>Voorlopig verwijderen voor SQL Server in azure VM met behulp van Azure Portal

>[!NOTE]
>Deze instructies zijn ook van toepassing op SAP HANA in azure VM.

1. Als u de back-upgegevens van een data base in een SQL-Server wilt verwijderen, moet de back-up worden gestopt. Ga in het Azure Portal naar de Recovery Services-kluis, ga naar het back-upitem en kies **back-up stoppen**.

   ![Back-up stoppen](./media/soft-delete-sql-saphana-in-azure-vm/stop-backup.png)

2. In het volgende venster krijgt u de mogelijkheid om de back-upgegevens te verwijderen of te bewaren. Als u **back-upgegevens verwijderen**kiest, wordt de back-up van de data base niet definitief verwijderd. In plaats daarvan worden de back-upgegevens 14 dagen in de modus voorlopig verwijderd bewaard. De verwijdering wordt uitgesteld tot de vijftiende dag met gewone waarschuwings berichten over de eerste, twaalfde en vijftiende dag van de back-upstatus van de Data Base naar de gebruiker.

   ![Back-upgegevens verwijderen](./media/soft-delete-sql-saphana-in-azure-vm/delete-backup-data.png)

3. Gedurende deze 14 dagen, in de Recovery Services kluis, wordt het voorlopig verwijderde item weer gegeven met een rood ' zacht-delete-pictogram ernaast.

   ![Voorlopig verwijderde items](./media/soft-delete-sql-saphana-in-azure-vm/soft-deleted-items.png)

4. Als u de voorlopig verwijderde data base wilt herstellen, moet deze eerst worden verwijderd. Als u de verwijdering ongedaan wilt maken, kiest u de voorlopig verwijderde data base en selecteert u vervolgens de optie **verwijderen ongedaan**maken.

   ![Data base verwijderen](./media/soft-delete-sql-saphana-in-azure-vm/undelete-database.png)

   Er wordt een venster weer gegeven waarin wordt vermeld dat als ongedaan maken is gekozen, alle herstel punten voor de Data Base worden verwijderd en beschikbaar zijn voor het uitvoeren van een herstel bewerking. Het back-upitem wordt opgeslagen in de status ' beveiliging met behoud van gegevens stoppen ' met back-ups onderbroken en back-upgegevens blijven behouden zonder het back-upbeleid effectief te maken.

   ![Waarschuwing voor verwijderen ongedaan maken](./media/soft-delete-sql-saphana-in-azure-vm/undelete-warning.png)

5. Op dit moment kunt u de gegevens ook herstellen door **herstellen** te selecteren voor het gekozen voorlopig verwijderde back-upitem.

   ![VM herstellen](./media/soft-delete-sql-saphana-in-azure-vm/restore-vm.png)

6. Nadat het verwijderen is voltooid, wordt de status weer gegeven als back-up stoppen met gegevens behouden en vervolgens kunt u **back-up hervatten**kiezen. Met de **back-** upbewerking hervatten wordt het back-upitem teruggezet in de actieve status, gekoppeld aan een back-upbeleid dat is geselecteerd door de gebruiker die de back-up-en bewaar schema's definieert.

   ![Back-up hervatten](./media/soft-delete-sql-saphana-in-azure-vm/resume-backup.png)

## <a name="soft-delete-for-sql-server-in-vm-using-azure-powershell"></a>Voorlopig verwijderen voor SQL Server in een virtuele machine met behulp van Azure PowerShell

>[!NOTE]
>De AZ. Recovery Services-versie die is vereist voor het gebruik van voorlopig verwijderen met Azure PowerShell is mini maal 2.2.0. Gebruik `Install-Module -Name Az.RecoveryServices -Force` om de nieuwste versie op te halen.

De volg orde van de stappen voor het gebruik van Azure PowerShell is hetzelfde als in de Azure Portal, hierboven beschreven.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Het back-upitem verwijderen met Azure PowerShell

Verwijder het back-upitem met de cmdlet [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PS.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force
```

De **DeleteState** van het back-upitem wordt gewijzigd van **NotDeleted** naar **ToBeDeleted**. De back-upgegevens worden 14 dagen bewaard. Als u de Verwijder bewerking wilt terugdraaien, moet u het ongedaan maken verwijderen.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>De verwijderings bewerking ongedaan maken met Azure PowerShell

U moet eerst het relevante back-upitem ophalen dat zich in de status voorlopig verwijderen bevindt (dat wil zeggen, dat wil zeggen dat het wordt verwijderd).

```powershell
Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID -Name AppVM1
```

Vervolgens voert u de bewerking voor ongedaan maken uit met de PS [-cmdlet Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) .

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force
```

De **DeleteState** van het back-upitem wordt teruggezet naar **NotDeleted**. De beveiliging is echter nog steeds gestopt. Hervat de back-up om de beveiliging opnieuw in te scha kelen.

## <a name="how-to-disable-soft-delete"></a>Voorlopig verwijderen uitschakelen

Het uitschakelen van deze functie wordt niet aanbevolen. De enige omstandigheid waarbij het uitschakelen van de functie voor het verwijderen van uw beveiligde items naar een nieuwe kluis moet worden aangeraden, is dat u de 14 dagen die vereist zijn voor het verwijderen en opnieuw beveiligen van een test omgeving, niet kunt wachten. Zie voor instructies voor het uitschakelen van de functie voor voorlopig verwijderen, [voorlopig verwijderen inschakelen en uitschakelen](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="next-steps"></a>Volgende stappen

- Lees de [Veelgestelde vragen](backup-azure-security-feature-cloud.md#frequently-asked-questions) over zacht verwijderen
- Meer informatie over alle [beveiligings functies in azure backup](security-overview.md)
