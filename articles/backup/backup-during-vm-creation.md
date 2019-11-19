---
title: Back-up inschakelen wanneer u een Azure-VM maakt
description: Hierin wordt beschreven hoe u back-ups inschakelt wanneer u een Azure VM maakt met Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: f34c5dd8cfdc94775b9bd9a896b4cfbe4154ecf8
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172361"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Back-up inschakelen wanneer u een Azure-VM maakt

Gebruik de Azure Backup-service om een back-up te maken van Azure virtual machines (Vm's). Er wordt een back-up gemaakt van Vm's op basis van een schema dat is opgegeven in een back-upbeleid en herstel punten van back-ups. Herstel punten worden opgeslagen in Recovery Services kluizen.

In dit artikel vindt u informatie over het inschakelen van back-ups bij het maken van een virtuele machine (VM) in de Azure Portal.  

## <a name="before-you-start"></a>Voordat u begint

- [Controleer](backup-support-matrix-iaas.md#supported-backup-actions) welke besturings systemen worden ondersteund als u back-ups inschakelt bij het maken van een virtuele machine.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u nog niet bent aangemeld bij uw account, meldt u zich aan bij de [Azure Portal](https://portal.azure.com).

## <a name="create-a-vm-with-backup-configured"></a>Een virtuele machine maken waarvoor een back-up is geconfigureerd

1. Klik in Azure Portal op **een resource maken**.

2. Klik in de Azure Marketplace op **Compute**en selecteer vervolgens een VM-installatie kopie.

3. Stel de virtuele machine in volgens de instructies voor [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) of [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) .

4. Klik op het tabblad **beheer** in **back-up inschakelen**op **aan**.
5. Azure Backup back-ups naar een Recovery Services kluis. Klik op **nieuwe maken** als u geen bestaande kluis hebt.
6. Accepteer de naam van de voorgestelde kluis of geef uw eigen op.
7. Geef op of maak een resource groep waarin de kluis zich bevindt. De kluis van de resource groep kan afwijken van de resource groep van de virtuele machine.

    ![Back-up voor een virtuele machine inschakelen](./media/backup-during-vm-creation/enable-backup.png)

8. Accepteer het standaard back-upbeleid of wijzig de instellingen.
    - In een back-upbeleid wordt aangegeven hoe vaak back-upmomentopnamen van de virtuele machine moeten worden gemaakt en hoe lang deze back-ups moeten worden bewaard.
    - Het standaard beleid maakt eenmaal per dag een back-up van de VM.
    - U kunt uw eigen back-upbeleid voor een Azure-VM aanpassen om dagelijks of wekelijks back-ups te maken.
    - Meer [informatie](backup-azure-vms-introduction.md#backup-and-restore-considerations) over back-upaandachtspunten voor Azure-vm's.
    - Meer [informatie](backup-instant-restore-capability.md) over de functionaliteit voor direct terugzetten.

      ![Standaard back-upbeleid](./media/backup-during-vm-creation/daily-policy.png)

> [!NOTE]
> Azure Backup service maakt een afzonderlijke resource groep (met uitzonde ring van de resource groep van de VM) voor het opslaan van de moment opname, met de naamgevings indeling **AzureBackupRG_geography_number** (voor beeld: AzureBackupRG_northeurope_1). De gegevens in deze resource groep worden bewaard gedurende de duur in dagen zoals opgegeven in de sectie *onmiddellijke herstel momentopname bewaren* van het back-upbeleid van Azure virtual machine.  Het Toep assen van een vergren deling op deze resource groep kan leiden tot back-upfouten. <br> Deze resource groep moet ook worden uitgesloten van de beperkingen van namen/Tags als een beperkings beleid het maken van resource punt verzamelingen in dat geval voor back-upfouten blokkeert.

## <a name="start-a-backup-after-creating-the-vm"></a>Een back-up starten na het maken van de VM

De back-up van de virtuele machine wordt uitgevoerd in overeenstemming met uw back-upbeleid. We raden u echter aan een eerste back-up uit te voeren.

Nadat de VM is gemaakt, gaat u als volgt te werk:

1. Klik in de VM-eigenschappen op **back-up**. Voor de VM-status is de eerste back-up in behandeling totdat de eerste back-up wordt uitgevoerd
2. Klik op **Nu back** -up maken om een back-up op aanvraag uit te voeren.

    ![Een back-up op aanvraag uitvoeren](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Een resource manager-sjabloon gebruiken om een beveiligde virtuele machine te implementeren

In de vorige stappen wordt uitgelegd hoe u de Azure Portal kunt gebruiken om een virtuele machine te maken en te beveiligen in een Recovery Services kluis. Als u een of meer Vm's snel wilt implementeren en wilt beveiligen in een Recovery Services kluis, raadpleegt u de sjabloon [een Windows VM implementeren en back-up inschakelen](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="next-steps"></a>Volgende stappen

Nu u uw VM hebt beveiligd, leert u hoe u deze kunt beheren en herstellen.

- [Vm's beheren en controleren](backup-azure-manage-vms.md)
- [VM herstellen](backup-azure-arm-restore-vms.md)

Als u problemen ondervindt, [raadpleegt](backup-azure-vms-troubleshoot.md) u de hand leiding voor het oplossen van problemen.
