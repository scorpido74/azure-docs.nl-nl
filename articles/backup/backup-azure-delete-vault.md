---
title: Een kluis van Microsoft Azure Recovery Services verwijderen
description: Lees in dit artikel hoe u afhankelijkheden verwijdert en vervolgens een kluis van Azure Backup Recovery Services verwijdert.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 69fed6c53914ed7aa16b04b5311ec69966734f25
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025132"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Een kluis azure Backup Recovery Services verwijderen

In dit artikel wordt beschreven hoe u een kluis [azure Backup](backup-overview.md) Recovery Services verwijdert. Het bevat instructies voor het verwijderen van afhankelijkheden en het verwijderen van een kluis.

## <a name="before-you-start"></a>Voordat u begint

U een kluis van Recovery Services met afhankelijkheden, zoals beveiligde servers of back-upbeheerservers, niet verwijderen.

- Kluizen die back-upgegevens bevatten, kunnen niet worden verwijderd (zelfs als u de beveiliging hebt gestopt, maar de back-upgegevens hebt bewaard).

- Als u een kluis verwijdert die afhankelijkheden bevat, wordt het volgende bericht weergegeven:

  ![Verwijder de kluisfout.](./media/backup-azure-delete-vault/error.png)

- Als u een on-premises beveiligd item verwijdert uit een portal die afhankelijkheden bevat, verschijnt er een waarschuwingsbericht:

  ![Verwijder de fout van de beveiligde server.](./media/backup-azure-delete-vault/error-message.jpg)

- Als back-upitems zich onder het waarschuwingsbericht in zachte verwijderde status bevinden en moet u wachten tot ze permanent worden verwijderd. Zie voor meer informatie dit [artikel.](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud)

   ![Verwijder de kluisfout.](./media/backup-azure-delete-vault/error-message-soft-delete.png)

- Kluizen met geregistreerde opslagaccounts kunnen niet worden verwijderd. Zie [Een opslagaccount uitschrijven](manage-afs-backup.md#unregister-a-storage-account)voor meer informatie over het uitschrijven van het account.
  
Als u de kluis wilt verwijderen, kiest u het scenario dat overeenkomt met uw installatie en volgt u de aanbevolen stappen:

Scenario | Stappen om afhankelijkheden te verwijderen om de kluis te verwijderen |
-- | --
Ik heb on-premises bestanden en mappen beveiligd met de Azure Backup-agent, een back-up maken van Azure | De stappen uitvoeren in [Back-upitems verwijderen van de MARS-beheerconsole](#delete-backup-items-from-the-mars-management-console)
Ik heb on-premises machines die zijn beveiligd met MABS (Microsoft Azure Backup Server) of DPM (System Center Data Protection Manager) in Azure | De stappen uitvoeren in [Back-upitems verwijderen van de MABS-beheerconsole](#delete-backup-items-from-the-mabs-management-console)
Ik heb beveiligde items in de cloud (bijvoorbeeld een laaS-virtuele machine of een Azure-bestandenaandeel)  | De stappen uitvoeren in [Beveiligde items in de cloud verwijderen](#delete-protected-items-in-the-cloud)
Ik heb beveiligde items zowel op locatie als in de cloud | Voer de stappen in alle volgende secties uit in de volgende volgorde: <br> 1. [Beveiligde items in de cloud verwijderen](#delete-protected-items-in-the-cloud)<br> 2. [Back-upitems verwijderen uit de MARS-beheerconsole](#delete-backup-items-from-the-mars-management-console) <br> 3. [Back-upitems verwijderen uit de MABS Management-console](#delete-backup-items-from-the-mabs-management-console)
Ik heb geen beveiligde items on-premises of cloud; Echter, ik ben nog steeds het krijgen van de Vault verwijdering fout | De stappen uitvoeren in [De kluis Herstelservices verwijderen met Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager) <br><br> Zorg ervoor dat er geen opslagaccounts zijn geregistreerd bij de kluis. Zie [Een opslagaccount uitschrijven](manage-afs-backup.md#unregister-a-storage-account)voor meer informatie over het uitschrijven van het account.

## <a name="delete-protected-items-in-the-cloud"></a>Beveiligde items in de cloud verwijderen

Lees eerst de **[sectie Voordat u begint met](#before-you-start)** het verwijderen van de afhankelijkheden en het verwijderingsproces van de kluis.

Voer de volgende stappen uit om de beveiliging te stoppen en de back-upgegevens te verwijderen:

1. Ga vanuit de portal naar **de kluis Van Recovery Services**en ga vervolgens naar **Back-upitems**. Kies vervolgens de beveiligde items in de cloud (bijvoorbeeld Azure Virtual Machines, Azure Storage [de Azure Files-service) of SQL Server op Azure Virtual Machines).

    ![Selecteer het back-uptype.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Klik met de rechtermuisknop om het back-upitem te selecteren. Afhankelijk van of het back-upitem is beveiligd of niet, wordt in het menu het deelvenster **Back-up stoppen** of het deelvenster **Back-upgegevens verwijderen** weergegeven.

    - Als het deelvenster **Back-up stoppen** wordt weergegeven, selecteert u **Back-upgegevens verwijderen** in het vervolgkeuzemenu. Voer de naam in van het back-upitem (dit veld is hoofdlettergevoelig) en selecteer vervolgens een reden in het vervolgkeuzemenu. Voer uw opmerkingen, als je die hebt. Selecteer vervolgens **Back-up stoppen**.

        ![Het deelvenster Back-up stoppen.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Als het deelvenster **Back-upgegevens verwijderen** wordt weergegeven, voert u de naam van het back-upitem in (dit veld is hoofdlettergevoelig) en selecteert u vervolgens een reden in het vervolgkeuzemenu. Voer uw opmerkingen, als je die hebt. Selecteer vervolgens **Verwijderen**.

         ![Het deelvenster Back-upgegevens verwijderen.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. Controleer het pictogram ![ **Melding:** het pictogram Melding.](./media/backup-azure-delete-vault/messages.png) Nadat het proces is voltooid, geeft de service het volgende bericht *weer: Back-up stoppen en back-upgegevens verwijderen voor "* Back-upitem *".* *Heeft de bewerking met succes voltooid.*
4. Selecteer **Vernieuwen** in het menu **Back-upitems** om ervoor te zorgen dat het back-upitem is verwijderd.

      ![De pagina Back-upitems verwijderen.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Beveiligde objecten op locatie verwijderen

Lees eerst de **[sectie Voordat u begint met](#before-you-start)** het verwijderen van de afhankelijkheden en het verwijderingsproces van de kluis.

1. Selecteer **Back-upinfrastructuur**in het menu van het kluisdashboard .
2. Kies een van de volgende opties, afhankelijk van uw on-premises scenario:

      - Selecteer voor MARS **Beveiligde servers** en vervolgens Azure **Backup Agent**. Selecteer vervolgens de server die u wilt verwijderen.

        ![Selecteer voor MARS uw kluis om het dashboard te openen.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Selecteer **Back-upbeheerservers**voor MABS of DPM . Selecteer vervolgens de server die u wilt verwijderen.

          ![Selecteer voor MABS uw kluis om het dashboard te openen.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Het deelvenster **Verwijderen** wordt weergegeven met een waarschuwingsbericht.

     ![Het verwijdervenster.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Controleer het waarschuwingsbericht en de instructies in het selectievakje Toestemming.
    > [!NOTE]
    >- Als de beveiligde server is gesynchroniseerd met Azure-services en er back-upitems bestaan, wordt in het selectievakje Toestemming het aantal afhankelijke back-upitems en de koppeling weergegeven om de back-upitems weer te geven.
    >- Als de beveiligde server niet wordt gesynchroniseerd met Azure-services en er back-upitems bestaan, wordt in het selectievakje Toestemming alleen het aantal back-upitems weergegeven.
    >- Als er geen back-upitems zijn, wordt in het selectievakje Toestemming om verwijdering gevraagd.

4. Schakel het selectievakje Toestemming in en selecteer **Verwijderen**.

5. Controleer **Notification** het ![pictogram Melding](./media/backup-azure-delete-vault/messages.png)verwijderen van back-upgegevens . Nadat de bewerking is voltooid, geeft de service het bericht weer: *Back-up stoppen en back-upgegevens verwijderen voor 'Back-upitem'.* *Heeft de bewerking met succes voltooid.*
6. Selecteer **Vernieuwen** in het menu **Back-upitems** om ervoor te zorgen dat het back-upitem wordt verwijderd.

Nadat dit proces is voltooid, u de back-upitems verwijderen uit de beheerconsole:

- [Back-upitems verwijderen uit de MARS-beheerconsole](#delete-backup-items-from-the-mars-management-console)
- [Back-upitems verwijderen uit de MABS-beheerconsole](#delete-backup-items-from-the-mabs-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Back-upitems verwijderen uit de MARS-beheerconsole

1. Open de MARS-beheerconsole, ga naar het deelvenster **Acties** en selecteer **Back-up plannen**.
2. Selecteer op de pagina **Een geplande back-up wijzigen of stoppen** de optie Stoppen met het gebruik van dit **back-upschema en verwijder alle opgeslagen back-ups.** Selecteer vervolgens **Volgende**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Selecteer Op de pagina **Een geplande back-up stoppen** de optie **Voltooien**.

    ![Stop een geplande back-up.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. U wordt gevraagd een beveiligingspincode (persoonlijk identificatienummer) in te voeren, die u handmatig moet genereren. Meld u hiervoor eerst aan bij de Azure-portal.
5. Ga naar**Eigenschappen**van de > **kluisinstellingen** >  **van Herstelservices**.
6. Selecteer **Onder Beveiligingspincode**de optie **Genereren**. Kopieer deze pincode. De pincode is slechts vijf minuten geldig.
7. Plak de pincode in de beheerconsole en selecteer **OK**.

    ![Een beveiligingspincode genereren.](./media/backup-azure-delete-vault/security-pin.png)

8. Op de pagina **Back-upvoortgang wijzigen** wordt het volgende bericht weergegeven: *verwijderde back-upgegevens worden gedurende 14 dagen bewaard. Na die tijd worden back-upgegevens permanent verwijderd.*  

    ![Verwijder de back-upinfrastructuur.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Nadat u de on-premises back-upitems hebt verwijderd, voert u de volgende stappen van de portal uit.

### <a name="delete-backup-items-from-the-mabs-management-console"></a>Back-upitems verwijderen uit de MABS-beheerconsole

Er zijn twee methoden die u gebruiken om back-upitems uit de MABS-beheerconsole te verwijderen.

#### <a name="method-1"></a>Methode 1

Ga als volgt te werk om de beveiliging te stoppen en back-upgegevens te verwijderen:

1. Open de DPM Administrator Console en selecteer **Vervolgens Beveiliging** op de navigatiebalk.
2. Selecteer in het weergavedeelvenster het lid van de beveiligingsgroep dat u wilt verwijderen. Klik met de rechtermuisknop om de optie **Beveiliging van groepsleden stoppen** te selecteren.
3. Schakel in het dialoogvenster **Beveiliging stoppen** de optie Beveiligde **gegevens verwijderen**in en schakel het selectievakje Opslag **online verwijderen** in. Selecteer vervolgens **Stopbeveiliging**.

    ![Selecteer Beveiligde gegevens verwijderen in het deelvenster Beveiliging stoppen.](./media/backup-azure-delete-vault/delete-storage-online.png)

    De status van beveiligde lid verandert *in inactieve replica die beschikbaar is.*

4. Klik met de rechtermuisknop op de inactieve beveiligingsgroep en selecteer **Inactieve beveiliging verwijderen**.

    ![Inactieve bescherming verwijderen.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Schakel in het venster **Inactieve beveiliging verwijderen** het selectievakje **Online opslag verwijderen** in en schakel **OK**in.

    ![Online opslag verwijderen.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Methode 2

Open de **MABS-beheerconsole.** Schakel **onder Gegevensbeschermingsmethode**selecteren het selectievakje **Ik wil onlinebeveiliging** uit.

  ![Selecteer de methode voor gegevensbescherming.](./media/backup-azure-delete-vault/data-protection-method.png)

Nadat u de on-premises back-upitems hebt verwijderd, voert u de volgende stappen van de portal uit.

## <a name="delete-the-recovery-services-vault"></a>Recovery Services-kluis verwijderen

1. Wanneer alle afhankelijkheden zijn verwijderd, bladert u naar het deelvenster **Essentiële zaken** in het kluismenu.
2. Controleer of er geen back-upitems, back-upbeheerservers of gerepliceerde items worden vermeld. Als items nog steeds in de kluis worden weergegeven, raadpleegt u de sectie [Voordat u begint.](#before-you-start)

3. Als er geen items meer in de kluis zijn, selecteert u **Verwijderen** op het vault-dashboard.

    ![Selecteer Verwijderen op het dashboard van de kluis.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Selecteer **Ja** om te controleren of u de kluis wilt verwijderen. De kluis is verwijderd. De portal keert terug naar het menu **Nieuwe** service.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>De kluis Recovery Services verwijderen met PowerShell

Lees eerst de **[sectie Voordat u begint met](#before-you-start)** het verwijderen van de afhankelijkheden en het verwijderingsproces van de kluis.

Ga als u de beveiliging stop en de back-upgegevens verwijdert:

- Als u SQL gebruikt in Azure VM's back-up en automatische beveiliging voor SQL-exemplaren hebt ingeschakeld, schakelt u eerst de automatische beveiliging uit.

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

  [Meer informatie](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) over het uitschakelen van beveiliging voor een door Azure Backup beveiligd item.

- Stop de beveiliging en verwijder gegevens voor alle back-upbeveiligde items in de cloud (bijvoorbeeld. laaS VM, Azure File Share etc.):

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

    [Meer informatie](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) over het uitschakelen van beveiliging voor een met back-up beveiligd item.

- Gebruik de volgende PowerShell-opdracht om de back-upgegevens van elke MARS PowerShell-module te verwijderen voor on-premises bestanden en mappen die zijn beveiligd met een back-up van Azure Backup Agent (MARS):

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Post waar de volgende prompt zou verschijnen:

    *Microsoft Azure Backup Weet u zeker dat u dit back-upbeleid wilt verwijderen? Verwijderde back-upgegevens worden 14 dagen bewaard. Na die tijd worden back-upgegevens permanent verwijderd. <br/> [Y] Ja [A] Ja tegen Allen [N] Nee [L] Nee tegen Allen [S] Suspend [?] Help (standaard is 'Y'):*

- Gebruik de volgende opdracht om de back-upgegevens in Azure te verwijderen voor on-premises machines die zijn beveiligd met MABS (Microsoft Azure Backup Server) of DPM naar Azure (System Center Data Protection Manager).

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Post waar de volgende prompt zou verschijnen:

   *Microsoft Azure Backup* Weet u zeker dat u dit back-upbeleid wilt verwijderen? Verwijderde back-upgegevens worden 14 dagen bewaard. Na die tijd worden back-upgegevens permanent verwijderd. <br/>
   [Y] Ja [A] Ja tegen Allen [N] Nee [L] Nee tegen Allen [S] Suspend [?] Help (standaard is 'Y'):*

Nadat u de back-upgegevens hebt verwijderd, registreert u alle on-premises containers en beheerservers.

- Voor on-premises bestanden en mappen die zijn beveiligd met een back-up van Azure Backup Agent (MARS):

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

    [Meer informatie](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) over het niet registreren van een Windows Server of andere container uit de kluis.

- Voor on-premises machines die zijn beveiligd met MABS (Microsoft Azure Backup Server) of DPM naar Azure (System Center Data Protection Manage:

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

    [Meer informatie](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) over het niet registreren van een back-upbeheercontainer uit de kluis.

Nadat u definitief back-upgegevens hebt verwijderd en alle containers hebt verwijderd, gaat u verder met het verwijderen van de kluis.

Ga als lid van het openen van een kluis van Recovery Services:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[Meer informatie](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) over het verwijderen van een kluis voor herstelservices.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>De kluis Recovery Services verwijderen met CLI

Lees eerst de **[sectie Voordat u begint met](#before-you-start)** het verwijderen van de afhankelijkheden en het verwijderingsproces van de kluis.

> [!NOTE]
> Op dit moment ondersteunt Azure Backup CLI alleen Azure VM-back-ups, dus de volgende opdracht om de kluis te verwijderen werkt alleen als de kluis Azure VM-back-ups bevat. U een kluis niet verwijderen met Azure Backup CLI als de kluis een ander back-upitem bevat dan Azure VM's.

Voer het onderstaand uit om de kluis van bestaande herstelservices te verwijderen:

- De beveiliging stoppen en de back-upgegevens verwijderen

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    Zie voor meer informatie dit [artikel.](/cli/azure/backup/protection#az-backup-protection-disable)

- Een bestaande kluis van herstelservices verwijderen:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Zie dit [artikel](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest) voor meer informatie

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>De kluis Recovery Services verwijderen met Azure Resource Manager

Deze optie om de kluis Recovery Services te verwijderen wordt alleen aanbevolen als alle afhankelijkheden worden verwijderd en u nog steeds de *fout voor het verwijderen*van de Kluis krijgt. Probeer een of alle van de volgende tips:

- Controleer in het deelvenster **Essentials** in het kluismenu of er geen back-upitems, back-upbeheerservers of gerepliceerde items worden vermeld. Als er back-upitems zijn, raadpleegt u de [sectie Voordat u begint.](#before-you-start)
- Probeer de kluis weer [uit het portaal te](#delete-the-recovery-services-vault) verwijderen.
- Als alle afhankelijkheden worden verwijderd en u nog steeds de fout met het verwijderen van *de Kluis*krijgt, gebruikt u het gereedschap ARMClient om de volgende stappen uit te voeren (na de notitie).

1. Ga naar [chocolatey.org](https://chocolatey.org/) om Chocolatey te downloaden en te installeren. Installeer vervolgens ARMClient door de volgende opdracht uit te voeren:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Meld u aan bij uw Azure-account en voer de volgende opdracht uit:

    `ARMClient.exe login [environment name]`

3. Verzamel in de Azure-portal de naam van de abonnements-id en de brongroep voor de kluis die u wilt verwijderen.

Zie [ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md)voor meer informatie over de opdracht ARMClient.

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>De Azure Resource Manager-client gebruiken om een vault voor Herstelservices te verwijderen

1. Voer de volgende opdracht uit met uw abonnements-ID, de naam van de brongroep en de naam van de kluis. Als u geen afhankelijkheden hebt, wordt de kluis verwijderd wanneer u de volgende opdracht uitvoert:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. Als de kluis niet leeg is, ontvangt u het volgende foutbericht: *Vault kan niet worden verwijderd omdat er bestaande bronnen in deze kluis zijn.* Voer de volgende opdracht uit om een beveiligd item of container in een kluis te verwijderen:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Controleer in de Azure-portal of de kluis is verwijderd.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over kluizen van Recovery Services](backup-azure-recovery-services-vault-overview.md)<br/>
[Meer informatie over het bewaken en beheren van vaults van Recovery Services](backup-azure-manage-windows-server.md)
