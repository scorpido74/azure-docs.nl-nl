---
title: Een Microsoft Azure Recovery Services kluis verwijderen
description: In dit artikel vindt u informatie over het verwijderen van afhankelijkheden en het verwijderen van een Microsoft Azure Backup Recovery Services (MARS)-kluis.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 23e0d2874229616037c44800639411f66bc4d1b6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173176"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Een Azure Backup Recovery Services kluis verwijderen

In dit artikel wordt beschreven hoe u een micro soft [Azure Backup](backup-overview.md) Recovery Services (Mars)-kluis verwijdert. Het bevat instructies voor het verwijderen van afhankelijkheden en het verwijderen van een kluis.

## <a name="before-you-start"></a>Voordat u begint

U kunt een Recovery Services kluis met afhankelijkheden, zoals beveiligde servers of back-upbeheer servers, niet verwijderen.

- Kluizen die back-upgegevens bevatten, kunnen niet worden verwijderd (zelfs niet als u de beveiliging hebt gestopt, maar de back-upgegevens zijn behouden).

- Als u een kluis verwijdert die afhankelijkheden bevat, wordt het volgende bericht weer gegeven:

  ![Verwijder de kluis fout.](./media/backup-azure-delete-vault/error.png)

- Als u een on-premises beveiligde item verwijdert uit een portal dat afhankelijkheden bevat, wordt er een waarschuwings bericht weer gegeven:

  ![Verwijder de fout met de beveiligde server.](./media/backup-azure-delete-vault/error-message.jpg)

- Als back-upitems de status zacht verwijderd krijgen onder een waarschuwing wordt weer gegeven, moet u wachten totdat ze permanent worden verwijderd. Zie [dit artikel](https://aka.ms/SoftDeleteCloudWorkloads) voor meer informatie.

   ![Verwijder de kluis fout.](./media/backup-azure-delete-vault/error-message-soft-delete.png)
  
Als u de kluis wilt verwijderen, kiest u het scenario dat overeenkomt met uw Setup en voert u de aanbevolen stappen uit:

Scenario | Stappen voor het verwijderen van afhankelijkheden om de kluis te verwijderen |
-- | --
Ik heb on-premises bestanden en mappen die zijn beveiligd met behulp van de Azure Backup-Agent, back-ups maken naar Azure | Voer de stappen in [Back-upitems verwijderen uit in de Mars-beheer console](#delete-backup-items-from-the-mars-management-console)
Ik heb on-premises machines die zijn beveiligd met MABS (Microsoft Azure Backup Server) of DPM (System Center Data Protection Manager) naar Azure | Voer de stappen in [Back-upitems verwijderen uit vanuit de MABS-beheer console](#delete-backup-items-from-the-mabs-management-console)
Ik heb beveiligde items in de Cloud (bijvoorbeeld een virtuele machine van laaS of een Azure Files-share)  | Voer de stappen in [beveiligde items verwijderen in de Cloud](#delete-protected-items-in-the-cloud) uit
Ik heb beveiligde items in zowel on-premises als in de Cloud | Voer de stappen in de volgende volg orde uit in de volgende secties: <br> 1. [beveiligde items in de Cloud verwijderen](#delete-protected-items-in-the-cloud)<br> 2. [Back-upitems verwijderen uit de Mars-beheer console](#delete-backup-items-from-the-mars-management-console) <br> 3. [Back-upitems verwijderen uit de MABS-beheer console](#delete-backup-items-from-the-mabs-management-console)
Ik heb geen beveiligde items on-premises of in de Cloud. Ik krijg echter nog steeds de fout melding voor het verwijderen van de kluis | Voer de stappen in [de Recovery Services kluis verwijderen uit met behulp van Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager)

## <a name="delete-protected-items-in-the-cloud"></a>Beveiligde items in de Cloud verwijderen

Lees eerst de sectie **[voordat u begint](#before-you-start)** om inzicht te krijgen in de afhankelijkheden en het verwijderings proces van de kluis.

Voer de volgende stappen uit om de beveiliging te stoppen en de back-upgegevens te verwijderen:

1. Ga in de portal naar **Recovery Services kluis**en ga vervolgens naar **Back-upitems**. Kies vervolgens de beveiligde items in de Cloud (bijvoorbeeld Azure Virtual Machines, Azure Storage [de Azure Files Service] of SQL Server in azure Virtual Machines).

    ![Selecteer het type back-up.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Klik met de rechter muisknop om het back-upitem te selecteren. Afhankelijk van of het back-upitem is beveiligd of niet, wordt in het menu het deel venster **back-up stoppen** of het deel venster **back-upgegevens verwijderen** weer gegeven.

    - Als het deel venster **back-up stoppen** wordt weer gegeven, selecteert u **back-upgegevens verwijderen** in de vervolg keuzelijst. Voer de naam van het back-upitem in (dit veld is hoofdletter gevoelig) en selecteer vervolgens een reden in de vervolg keuzelijst. Voer uw opmerkingen in, als u die hebt. Selecteer vervolgens **back-up stoppen**.

        ![Het deel venster back-up stoppen.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Als het deel venster **back-upgegevens verwijderen** wordt weer gegeven, voert u de naam van het back-upitem in (dit veld is hoofdletter gevoelig) en selecteert u vervolgens een reden in de vervolg keuzelijst. Voer uw opmerkingen in, als u die hebt. Selecteer vervolgens **verwijderen**.

         ![Het deel venster back-upgegevens verwijderen.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. Controleer het **meldings** pictogram: ![het pictogram melding.](./media/backup-azure-delete-vault/messages.png) Nadat het proces is voltooid, wordt het volgende bericht weer gegeven: *back-up stoppen en back-upgegevens verwijderen voor*back-upitem *.* *De bewerking is voltooid*.
4. Selecteer **vernieuwen** in het menu **back-** upitems om er zeker van te zijn dat de back-up is verwijderd.

      ![De pagina Back-upitems verwijderen.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Beveiligde items on-premises verwijderen

Lees eerst de sectie **[voordat u begint](#before-you-start)** om inzicht te krijgen in de afhankelijkheden en het verwijderings proces van de kluis.

1. Selecteer in het menu van het kluis dashboard **back-upinfrastructuur**.
2. Afhankelijk van uw on-premises scenario kiest u een van de volgende opties:

      - Voor MARS selecteert u **beveiligde servers** en vervolgens **Azure backup agent**. Selecteer vervolgens de server die u wilt verwijderen.

        ![Selecteer uw kluis voor MARS om het dash board ervan te openen.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Voor MABS of DPM selecteert u **Backup-beheerser vers**. Selecteer vervolgens de server die u wilt verwijderen.

          ![Selecteer voor MABS de kluis om het dash board ervan te openen.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Het deel venster **verwijderen** wordt weer gegeven met een waarschuwings bericht.

     ![Het deel venster verwijderen.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Bekijk het waarschuwings bericht en de instructies in het dialoog venster toestemming.
    > [!NOTE]
    >- Als de beveiligde server is gesynchroniseerd met Azure-Services en er back-upitems bestaan, wordt in het selectie vakje toestemming het aantal afhankelijke back-upitems en de koppeling voor het weer geven van de back-upitems weer gegeven.
    >- Als de beveiligde server niet is gesynchroniseerd met Azure-Services en er back-upitems bestaan, wordt in het selectie vakje toestemming alleen het aantal back-upitems weer gegeven.
    >- Als er geen back-upitems zijn, wordt in het selectie vakje toestemming gevraagd om te worden verwijderd.

4. Schakel het selectie vakje toestemming in en selecteer vervolgens **verwijderen**.

5. Controleer het **meldings** pictogram ![back-upgegevens verwijderen](./media/backup-azure-delete-vault/messages.png). Nadat de bewerking is voltooid, wordt het volgende bericht weer gegeven: *back-up stoppen en back-upgegevens verwijderen voor ' back-upitem '.* *De bewerking is voltooid*.
6. Selecteer **vernieuwen** in het menu **back-** upitems om er zeker van te zijn dat de back-up wordt verwijderd.

Nadat dit proces is voltooid, kunt u de back-upitems uit de beheer console verwijderen:

- [Back-upitems verwijderen uit de MARS-beheer console](#delete-backup-items-from-the-mars-management-console)
- [Back-upitems verwijderen uit de MABS-beheer console](#delete-backup-items-from-the-mabs-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Back-upitems verwijderen uit de MARS-beheer console

1. Open de MARS-beheer console, ga naar het deel venster **acties** en selecteer **back-up plannen**.
2. Op de pagina **een geplande back-up wijzigen of stoppen** selecteert **u stoppen met het gebruik van dit back-upschema en alle opgeslagen back-ups verwijderen**. Selecteer vervolgens **Volgende**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Selecteer **volt ooien**op de pagina **een geplande back-up stoppen** .

    ![Een geplande back-up stoppen.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. U wordt gevraagd een beveiligings pincode (persoonlijk identificatie nummer) in te voeren, die u hand matig moet genereren. Als u dit wilt doen, meldt u zich eerst aan bij de Azure Portal.
5. Ga naar **Recovery Services kluis** > **instellingen** > **Eigenschappen**.
6. Onder **BEVEILIGINGS pincode**selecteert u **genereren**. Deze pincode kopiëren. De pincode is slechts vijf minuten geldig.
7. Plak de pincode in de beheer console en selecteer **OK**.

    ![Een beveiligings pincode genereren.](./media/backup-azure-delete-vault/security-pin.png)

8. Op de pagina **voortgang van back-up wijzigen** wordt het volgende bericht weer gegeven: *verwijderde back-upgegevens worden 14 dagen bewaard. Na die tijd worden de back-upgegevens permanent verwijderd.*  

    ![Verwijder de back-upinfrastructuur.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Nadat u de on-premises back-upitems hebt verwijderd, voert u de volgende stappen uit in de portal.

### <a name="delete-backup-items-from-the-mabs-management-console"></a>Back-upitems verwijderen uit de MABS-beheer console

Er zijn twee methoden die u kunt gebruiken om back-upitems te verwijderen uit de MABS-beheer console.

#### <a name="method-1"></a>Methode 1

Voer de volgende stappen uit om de beveiliging te stoppen en de back-upgegevens te verwijderen:

1. Open de DPM Administrator-console en selecteer vervolgens **beveiliging** op de navigatie balk.
2. Selecteer in het weergave paneel het lid van de beveiligings groep dat u wilt verwijderen. Klik met de rechter muisknop om de optie **beveiliging van groeps leden stoppen** te selecteren.
3. Selecteer in het dialoog venster **beveiliging stoppen** de optie **beveiligde gegevens verwijderen**en schakel vervolgens het selectie vakje **opslag online verwijderen** in. Klik vervolgens op **beveiliging stoppen**.

    ![Selecteer beveiligde gegevens verwijderen in het deel venster beveiliging stoppen.](./media/backup-azure-delete-vault/delete-storage-online.png)

    De status van de beveiligde leden wordt gewijzigd in *niet-actieve replica beschikbaar*.

4. Klik met de rechter muisknop op inactieve beveiligings groep en selecteer **inactieve beveiliging verwijderen**.

    ![Inactieve beveiliging verwijderen.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Selecteer in het venster **inactieve beveiliging verwijderen** het selectie vakje **online opslag verwijderen** en selecteer vervolgens **OK**.

    ![Verwijder de online opslag.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Methode 2

Open de **MABS-beheer** console. Schakel onder **methode voor gegevens beveiliging selecteren**het selectie vakje **Ik wil online beveiliging** uit.

  ![Selecteer de methode voor gegevens beveiliging.](./media/backup-azure-delete-vault/data-protection-method.png)

Nadat u de on-premises back-upitems hebt verwijderd, voert u de volgende stappen uit in de portal.

## <a name="delete-the-recovery-services-vault"></a>Recovery Services-kluis verwijderen

1. Wanneer alle afhankelijkheden zijn verwijderd, bladert u naar het deel venster **essentiële** elementen in het menu kluis.
2. Controleer of er geen back-upitems, back-upbeheer servers of gerepliceerde items worden weer gegeven. Als items nog steeds in de kluis worden weer gegeven, raadpleegt u de sectie [voordat u begint](#before-you-start) .

3. Als er zich geen items meer in de kluis bevinden, selecteert u **verwijderen** op het kluis dashboard.

    ![Selecteer verwijderen op het kluis dashboard.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Selecteer **Ja** om te controleren of u de kluis wilt verwijderen. De kluis wordt verwijderd. De portal keert terug naar het menu **nieuwe** service.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>De Recovery Services kluis verwijderen met behulp van Power shell

Lees eerst de sectie **[voordat u begint](#before-you-start)** om inzicht te krijgen in de afhankelijkheden en het verwijderings proces van de kluis.

De beveiliging stoppen en de back-upgegevens verwijderen:

- Als u SQL gebruikt in azure Vm's back-up en automatische beveiliging voor SQL-instanties, moet u eerst de automatische beveiliging uitschakelen.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  Meer [informatie](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) over het uitschakelen van de beveiliging voor een met Azure backup beveiligd item.

- Stop de beveiliging en verwijder gegevens voor alle met back-ups beveiligde items in de Cloud (ex. laaS VM, Azure-bestands share enz.):

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    Meer [informatie](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) over het uitschakelen van de beveiliging van een item dat is beveiligd met een back-up.

- Gebruik de volgende Power shell-opdracht voor het verwijderen van de back-upgegevens van elke MARS Power shell-module voor on-premises bestanden en mappen die zijn beveiligd met een Azure Backup-Agent (MARS) die wordt gebruikt voor het maken van een back-up naar Azure:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Post waar de volgende prompt wordt weer gegeven:

    *Microsoft Azure Backup weet u zeker dat u dit back-upbeleid wilt verwijderen? Verwijderde back-upgegevens worden 14 dagen bewaard. Na die tijd worden de back-upgegevens permanent verwijderd. <br/> [Y] Ja [A] Ja op alle [N] Nee [L] geen naar alle [S] Suspend [?] Help (standaard is ' Y '):*

- Voor on-premises machines die zijn beveiligd met MABS (Microsoft Azure Backup Server) of DPM naar Azure (System Center Data Protection Manager), gebruikt u de volgende opdracht om de back-upgegevens in azure te verwijderen.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Post waar de volgende prompt wordt weer gegeven:

   *Microsoft Azure backup* Weet u zeker dat u dit back-upbeleid wilt verwijderen? Verwijderde back-upgegevens worden 14 dagen bewaard. Na die tijd worden de back-upgegevens permanent verwijderd. <br/>
   [Y] Ja [A] Ja op alle [N] Nee [L] geen naar alle [S] Suspend [?] Help (standaard is ' Y '):*

Nadat u de back-upgegevens hebt verwijderd, moet u de registratie van alle on-premises containers en beheerser vers ongedaan maken.

- Voor on-premises bestanden en mappen die zijn beveiligd met Azure Backup-Agent (MARS) back-up naar Azure:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    Meer [informatie](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) over het ongedaan maken van de registratie van een Windows-Server of andere container uit de kluis.

- Voor on-premises machines die zijn beveiligd met MABS (Microsoft Azure Backup Server) of DPM naar Azure (System Center Data Protection beheren:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    Meer [informatie](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) over het ongedaan maken van de registratie van een container voor back-upbeheer vanuit de kluis.

Nadat u de back-ups definitief hebt verwijderd en alle containers hebt geregistreerd, gaat u verder met het verwijderen van de kluis.

Een Recovery Services kluis verwijderen:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

Meer [informatie](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) over het verwijderen van een Recovery Services-kluis.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>De Recovery Services kluis verwijderen met behulp van CLI

Lees eerst de sectie **[voordat u begint](#before-you-start)** om inzicht te krijgen in de afhankelijkheden en het verwijderings proces van de kluis.

> [!NOTE]
> Azure Backup CLI ondersteunt momenteel alleen het beheer van back-ups van virtuele Azure-machines, dus de volgende opdracht voor het verwijderen van de kluis werkt alleen als de kluis back-ups van virtuele Azure-machines bevat. U kunt een kluis niet verwijderen met Azure Backup CLI als de kluis een back-upitem bevat van het type andere dan Azure-Vm's.

Als u de bestaande Recovery Services-kluis wilt verwijderen, voert u de onderstaande stappen uit:

- De beveiliging stoppen en de back-upgegevens verwijderen

    ```CLI
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    Zie dit [artikel](/cli/azure/backup/protection#az-backup-protection-disable)voor meer informatie.

- Een bestaande Recovery Services-kluis verwijderen:

    ```CLI
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Raadpleeg dit [artikel](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest) voor meer informatie

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>De Recovery Services kluis verwijderen door gebruik te maken van Azure Resource Manager

Deze optie voor het verwijderen van de Recovery Services kluis wordt alleen aanbevolen als alle afhankelijkheden zijn verwijderd en u nog steeds de *kluis verwijdert*. Probeer een of meer van de volgende tips:

- Controleer in het deel venster **Essentials** in het menu kluis of er geen back-upitems, back-upbeheer servers of gerepliceerde items worden weer gegeven. Als er back-upitems zijn, raadpleegt u de sectie [voordat u begint](#before-you-start) .
- Probeer [de kluis opnieuw te verwijderen uit de portal](#delete-the-recovery-services-vault) .
- Als alle afhankelijkheden worden verwijderd en u nog steeds de *kluis verwijdert*, gebruikt u het ARMClient-hulp programma om de volgende stappen uit te voeren (na de opmerking).

1. Ga naar [chocolatey.org](https://chocolatey.org/) om de chocolade te downloaden en te installeren. Installeer vervolgens ARMClient door de volgende opdracht uit te voeren:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Meld u aan bij uw Azure-account en voer de volgende opdracht uit:

    `ARMClient.exe login [environment name]`

3. Verzamel in het Azure Portal de abonnements-ID en de naam van de resource groep voor de kluis die u wilt verwijderen.

Zie [ARMCLIENT README](https://github.com/projectkudu/ARMClient/blob/master/README.md)(Engelstalig) voor meer informatie over de ARMClient-opdracht.

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>De Azure Resource Manager-client gebruiken om een Recovery Services kluis te verwijderen

1. Voer de volgende opdracht uit met de abonnements-ID, de naam van de resource groep en de naam van de kluis. Als u geen afhankelijkheden hebt, wordt de kluis verwijderd wanneer u de volgende opdracht uitvoert:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. Als de kluis niet leeg is, wordt het volgende fout bericht weer gegeven: de *kluis kan niet worden verwijderd omdat er bestaande resources zijn in deze kluis.* Als u een beveiligd item of een container binnen een kluis wilt verwijderen, voert u de volgende opdracht uit:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Zorg ervoor dat de kluis is verwijderd in de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Recovery Services kluizen](backup-azure-recovery-services-vault-overview.md)<br/>
[Meer informatie over het controleren en beheren van Recovery Services kluizen](backup-azure-manage-windows-server.md)
