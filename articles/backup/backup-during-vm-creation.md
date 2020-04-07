---
title: Back-up inschakelen wanneer u een Azure-VM maakt
description: Beschrijft hoe u back-up inschakelen wanneer u een Azure VM maakt met Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 7739109eb8bad88c9b723e67e13adc78c127499a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672809"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Back-up inschakelen wanneer u een Azure-VM maakt

Gebruik de Azure Backup-service om een back-up te maken van virtuele Azure-machines (VM's). Vm's worden geback-upt volgens een schema dat is opgegeven in een back-upbeleid en herstelpunten worden gemaakt op basis van back-ups. Herstelpunten worden opgeslagen in vaults van Recovery Services.

In dit artikel wordt beschreven hoe u back-up inschakelen wanneer u een virtuele machine (VM) maakt in de Azure-portal.  

## <a name="before-you-start"></a>Voordat u begint

- [Controleer](backup-support-matrix-iaas.md#supported-backup-actions) welke besturingssystemen worden ondersteund als u back-ups inschakelt wanneer u een vm maakt.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u nog niet bent aangemeld bij uw account, meldt u zich aan bij de [Azure-portal.](https://portal.azure.com)

## <a name="create-a-vm-with-backup-configured"></a>Een vm maken met back-up geconfigureerd

1. Klik in Azure-portal op **Een resource maken**.

2. Klik in de Azure Marketplace op **Compute**en selecteer vervolgens een VM-afbeelding.

3. Stel de VM in in overeenstemming met de [Windows-](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) of [Linux-instructies.](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)

4. Klik op het tabblad **Beheer** in **Back-up inschakelen**op **Op**.
5. Azure Backup back-ups naar een Vault Recovery Services. Klik **op Nieuw maken** als u geen bestaande kluis hebt.
6. Accepteer de voorgestelde kluisnaam of geef die van uw eigen kluis op.
7. Geef of maak een resourcegroep waarin de kluis zich bevindt. De brongroepkluis kan afwijken van de VM-brongroep.

    ![Back-up voor een virtuele machine inschakelen](./media/backup-during-vm-creation/enable-backup.png)

8. Accepteer het standaardback-upbeleid of wijzig de instellingen.
    - Een back-upbeleid geeft aan hoe vaak back-upmomentopnamen van de vm moeten worden gemaakt en hoe lang deze back-upkopieën moeten worden behouden.
    - Met het standaardbeleid wordt één keer per dag een back-up van de vm gemaakt.
    - U uw eigen back-upbeleid voor een Azure VM aanpassen om dagelijks of wekelijks back-ups te maken.
    - [Meer informatie](backup-azure-vms-introduction.md#backup-and-restore-considerations) over back-upoverwegingen voor Azure VM's.
    - [Meer informatie](backup-instant-restore-capability.md) over de functionaliteit voor direct herstellen.

      ![Standaardback-upbeleid](./media/backup-during-vm-creation/daily-policy.png)

## <a name="azure-backup-resource-group-for-virtual-machines"></a>Azure Backup-brongroep voor virtuele machines

Met de back-upservice wordt een afzonderlijke brongroep (RG) gemaakt, anders dan de resourcegroep van de VM om de RPC (Restore Point Collection) op te slaan. De RPC herbergt de directe herstelpunten van beheerde VM's. De standaardnaamgevingsindeling van de resourcegroep die `AzureBackupRG_<Geo>_<number>`is gemaakt door de back-upservice is: . Bijvoorbeeld: *AzureBackupRG_northeurope_1*. U nu de naam van de resourcegroep aanpassen die is gemaakt door Azure Backup.

Aandachtspunten:

1. U de standaardnaam van de RG gebruiken of bewerken volgens uw bedrijfsvereisten.
2. U geeft het RG-naampatroon op als invoer tijdens het maken van vm-back-upbeleid. De RG-naam moet van `<alpha-numeric string>* n <alpha-numeric string>`het volgende formaat zijn: . 'n' wordt vervangen door een geheel getal (vanaf 1) en wordt gebruikt voor het uitschalen als de eerste RG vol is. Een RG kan vandaag de dag een maximum van 600 RDC's hebben.
              ![Naam kiezen bij het maken van beleid](./media/backup-during-vm-creation/create-policy.png)
3. Het patroon moet de onderstaande RG-naamgevingsregels volgen en de totale lengte mag niet hoger zijn dan de maximaal toegestane RG-naamlengte.
    1. Namen van resourcegroepen staan alleen alfanumerieke tekens, perioden, underscores, koppeltekens en haakjes toe. Ze kunnen niet eindigen in een periode.
    2. Namen van resourcegroepen kunnen maximaal 74 tekens bevatten, waaronder de naam van de RG en het achtervoegsel.
4. De `<alpha-numeric-string>` eerste is verplicht, terwijl de tweede na 'n' optioneel is. Dit geldt alleen als u een aangepaste naam geeft. Als u niets in voert in een van de tekstvakken, wordt de standaardnaam gebruikt.
5. U de naam van de RG bewerken door het beleid te wijzigen indien en wanneer dat nodig is. Als het naampatroon wordt gewijzigd, worden er nieuwe R's gemaakt in de nieuwe RG. De oude R's blijven echter in de oude RG wonen en worden niet verplaatst, omdat RP Collection geen ondersteuning biedt voor resourcemove. Uiteindelijk krijgen de RPs afval verzameld als de punten verlopen.
![Naam wijzigen bij wijzigen van beleid](./media/backup-during-vm-creation/modify-policy.png)
6. Het wordt aangeraden de resourcegroep die is gemaakt voor gebruik door de back-upservice, niet te vergrendelen.

Als u de azure backup-brongroep voor virtuele machines wilt configureren met PowerShell, raadpleegt u [Azure Backup-brongroep maken tijdens het bewaren van momentopnamen](backup-azure-vms-automation.md#creating-azure-backup-resource-group-during-snapshot-retention).

## <a name="start-a-backup-after-creating-the-vm"></a>Een back-up starten na het maken van de VM

Uw VM-back-up wordt uitgevoerd in overeenstemming met uw back-upbeleid. We raden u echter aan een eerste back-up uit te voeren.

Ga als volgt te werk nadat de vm is gemaakt:

1. Klik in de VM-eigenschappen op **Back-up**. De VM-status is Initial Backup Pending totdat de eerste back-up is uitgevoerd
2. Klik **nu op Een back-up** maken om een back-up op aanvraag uit te voeren.

    ![Een on-demand back-up uitvoeren](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Een resourcebeheersjabloon gebruiken om een beveiligde virtuele machine te implementeren

In de vorige stappen wordt uitgelegd hoe u de Azure-portal gebruiken om een virtuele machine te maken en te beveiligen in een kluis van Recovery Services. Als u snel een of meer VM's wilt implementeren en deze wilt beveiligen in een kluis van Recovery Services, raadpleegt u de sjabloon [Een Windows-vm implementeren en back-up inschakelen.](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)

## <a name="next-steps"></a>Volgende stappen

Nu u uw vm hebt beschermd, leert u hoe u deze beheren en herstellen.

- [Virtuele machines beheren en controleren](backup-azure-manage-vms.md)
- [VM herstellen](backup-azure-arm-restore-vms.md)

Als u problemen ondervindt, [bekijkt u](backup-azure-vms-troubleshoot.md) de handleiding voor probleemoplossing.
