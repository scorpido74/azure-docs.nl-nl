---
title: Een back-up van een Azure VM maken op basis van de VM-instellingen
description: In dit artikel vindt u informatie over het maken van een back-up van een enkelvoudige Azure-VM of meerdere virtuele Azure-machines met de Azure Backup-service.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: bd75bdfb3fe9939c576fe289f6be38ebe875e020
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294062"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Een back-up van een Azure VM maken op basis van de VM-instellingen

In dit artikel wordt uitgelegd hoe u een back-up van virtuele Azure-machines maakt met de [Azure backup](backup-overview.md) -service. U kunt een back-up van virtuele Azure-machines maken met behulp van een aantal methoden:

- Eén Azure VM: in dit artikel wordt beschreven hoe u rechtstreeks vanuit de VM-instellingen een back-up maakt van een Azure-VM.
- Meerdere virtuele machines van Azure: u kunt een Recovery Services kluis instellen en een back-up configureren voor meerdere virtuele Azure-machines. Volg de instructies in [dit artikel](backup-azure-arm-vms-prepare.md) voor dit scenario.

## <a name="before-you-start"></a>Voordat u begint

1. [Meer informatie](backup-architecture.md#how-does-azure-backup-work) over de werking van back-ups en het [controleren](backup-support-matrix.md#azure-vm-backup-support) van de ondersteunings vereisten.
2. [Bekijk een overzicht](backup-azure-vms-introduction.md) van back-ups van Azure-vm's.

### <a name="azure-vm-agent-installation"></a>Installatie van de Azure VM-agent

Azure Backup installeert een uitbrei ding op de VM-agent die op de computer wordt uitgevoerd om een back-up te maken van virtuele Azure-machines. Als uw virtuele machine is gemaakt op basis van een installatie kopie van Azure Marketplace, wordt de agent uitgevoerd. In sommige gevallen, bijvoorbeeld als u een aangepaste VM maakt of als u een machine van on-premises migreert. mogelijk moet u de agent hand matig installeren.

- Als u de VM-agent hand matig moet installeren, volgt u de instructies voor [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) -of [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) -vm's.
- Wanneer de agent is geïnstalleerd en u back-up inschakelt, wordt de back-upextensie door Azure Backup op de agent geïnstalleerd. Hiermee wordt de extensie zonder tussen komst van de gebruiker bijgewerkt en opgelost.

## <a name="back-up-from-azure-vm-settings"></a>Back-ups maken van Azure VM-instellingen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
2. Klik op **alle services** en typ **virtuele machines**in het filter en klik vervolgens op **virtuele machines**.
3. Selecteer in de lijst met Vm's de virtuele machine waarvan u een back-up wilt maken.
4. Klik in het menu VM op **back-up**.
5. Ga in **Recovery Services kluis**als volgt te werk:
   - Als u al een kluis hebt, klikt u op **bestaande selecteren**en selecteert u een kluis.
   - Als u geen kluis hebt, klikt u op **nieuwe maken**. Geef een naam op voor de kluis. Deze wordt gemaakt in dezelfde regio en resource groep als de VM. U kunt deze instellingen niet wijzigen wanneer u back-up rechtstreeks vanuit de VM-instellingen inschakelt.

   ![Wizard Back-up inschakelen](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. Ga als volgt te werk in **back-upbeleid kiezen**:

   - Het standaard beleid blijven. Hiermee wordt een back-up gemaakt van de VM eenmaal per dag op de opgegeven tijd en worden de back-ups gedurende 30 dagen in de kluis bewaard.
   - Selecteer een bestaand back-upbeleid als u er een hebt.
   - Maak een nieuw beleid en definieer de beleids instellingen.  

   ![Back-upbeleid selecteren](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Klik op **back-up inschakelen**. Hiermee wordt het back-upbeleid gekoppeld aan de virtuele machine.

    ![Knop Backup inschakelen](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. U kunt de voortgang van de configuratie in de portal meldingen volgen.
9. Nadat de taak is voltooid, klikt u in het menu VM op **back-up**. De pagina toont de back-upstatus voor de virtuele machine, informatie over herstel punten, actieve taken en waarschuwingen die worden gegeven.

   ![Back-upstatus](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Na het inschakelen van back-up wordt een eerste back-up uitgevoerd. U kunt de eerste back-up onmiddellijk starten of wachten totdat deze begint volgens het back-upschema.
    - Totdat de eerste back-up is voltooid, wordt de **laatste back-upstatus** weer gegeven als **waarschuwing (eerste back-up in behandeling)** .
    - Klik op de naam van het back-upbeleid om te zien wanneer de volgende geplande back-up wordt uitgevoerd.

## <a name="run-a-backup-immediately"></a>Direct een back-up uitvoeren

1. Als u direct een back-up wilt uitvoeren, klikt u in het menu VM op **back** -up > **back-up nu maken**.

    ![Back-up uitvoeren](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. Gebruik in **back-up nu** het besturings element kalender om te selecteren tot wanneer het herstel punt wordt bewaard > en **OK**.

    ![Dag voor back-up bewaren](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Met portal meldingen kunt u weten dat de back-uptaak is geactiveerd. Klik op **alle taken weer geven om de**voortgang van de back-up te bewaken.

## <a name="back-up-from-the-recovery-services-vault"></a>Back-ups maken van Recovery Services kluis

Volg de instructies in dit artikel om back-ups van virtuele Azure-machines in te scha kelen door een Azure Backup Recovery Services kluis in te stellen en back-ups in de kluis in te scha kelen.

>[!NOTE]
> Azure Backup ondersteunt nu selectieve back-up en herstel met behulp van de back-upoplossing van Azure virtual machine.
>
>Momenteel biedt Azure Backup ondersteuning voor het maken van back-ups van alle schijven (besturings systeem en gegevens) in een virtuele machine met behulp van de back-upoplossing van de VM. Met de functionaliteit voor uitsluiten van schijven krijgt u een optie om een back-up te maken van een of enkele van de vele gegevens schijven in een VM. Dit biedt een efficiënte en rendabele oplossing voor uw back-up-en herstel behoeften. Elk herstel punt bevat gegevens van de schijven die zijn opgenomen in de back-upbewerking, waarmee u een subset van schijven die zijn hersteld vanaf het opgegeven herstel punt tijdens de herstel bewerking kunt laten herstellen. Dit is van toepassing om beide te herstellen vanuit de moment opname en de kluis.
>
> Deze oplossing is met name handig in de volgende scenario's:
>  
>1. U hebt essentiële gegevens waarvan u een back-up wilt maken op slechts één schijf en u wilt geen back-up maken van de rest van de schijven die zijn gekoppeld aan een virtuele machine. Hierdoor worden de kosten voor back-upopslag geminimaliseerd.  
>2. U hebt andere back-upoplossingen voor een deel van uw VM-gegevens. U maakt bijvoorbeeld een back-up van uw data bases of gegevens met een andere back-up van de werk belasting en u wilt back-ups op Azure-VM-niveau gebruiken voor de rest van uw schijven en gegevens om een efficiënt en robuust systeem te bouwen met de beste mogelijkheden die beschikbaar zijn.
>
>Als u zich wilt aanmelden voor de preview, schrijft u voor AskAzureBackupTeam@microsoft.com

## <a name="next-steps"></a>Volgende stappen

- Als u problemen ondervindt met een van de procedures in dit artikel, raadpleegt u de [hand leiding](backup-azure-vms-troubleshoot.md)voor het oplossen van problemen.
- [Meer informatie over](backup-azure-manage-vms.md) het beheren van uw back-ups.
