---
title: Een back-up maken van een Azure VM vanuit de VM-instellingen
description: In dit artikel vindt u een back-up van een afzonderlijke Azure VM of meerdere Azure VM's met de Azure Backup-service.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 72d6e5657add3e815bb0d77fadbdbc716712bee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705442"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Een back-up maken van een Azure VM vanuit de VM-instellingen

In dit artikel wordt uitgelegd hoe u een back-up maakt van Azure VM's met de [Azure Backup-service.](backup-overview.md) U een back-up maken van Azure VM's met een paar methoden:

- Eén Azure VM: in de instructies in dit artikel wordt beschreven hoe u rechtstreeks vanuit de VM-instellingen een back-up van een Azure-vm maken.
- Meerdere Azure VM's: u een vault voor Herstelservices instellen en een back-up configureren voor meerdere Azure VM's. Volg de instructies in [dit artikel](backup-azure-arm-vms-prepare.md) voor dit scenario.

## <a name="before-you-start"></a>Voordat u begint

1. [Meer informatie over](backup-architecture.md#how-does-azure-backup-work) hoe back-up werkt en [controleer de](backup-support-matrix.md#azure-vm-backup-support) ondersteuningsvereisten.
2. [Hier krijg je een overzicht](backup-azure-vms-introduction.md) van Azure VM-back-ups.

### <a name="azure-vm-agent-installation"></a>Azure VM-agentinstallatie

Azure Backup installeert een extensie op de VM-agent die op de machine wordt uitgevoerd om een back-up van Azure VM's te maken. Als uw VM is gemaakt op basis van een Azure-marktafbeelding, wordt de agent uitgevoerd. In sommige gevallen bijvoorbeeld als u een aangepaste vm maakt of als u een machine migreert van on-premises. mogelijk moet u de agent handmatig installeren.

- Als u de VM-agent handmatig moet installeren, volgt u de instructies voor [Windows-](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) of Linux-VM's. [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)
- Nadat de agent is geïnstalleerd, installeert Azure Backup de back-upextensie voor de agent wanneer u een back-up inschakelt. Het werkt en patches de extensie zonder tussenkomst van de gebruiker.

## <a name="back-up-from-azure-vm-settings"></a>Een back-up maken vanuit Azure VM-instellingen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Klik op **Alle services** en typ **virtuele machines**in het filter en klik op Virtuele **machines**.
3. Selecteer in de lijst met VM's de vm waarvan u een back-up wilt maken.
4. Klik in het menu VM op **Back-up**.
5. Ga als volgt te werk in de **kluis Recovery Services:**
   - Als u al een kluis hebt, klikt u op **Bestaand selecteren**en selecteert u een kluis.
   - Als u geen kluis hebt, klikt u op **Nieuw maken**. Geef een naam op voor de kluis. Het is gemaakt in dezelfde regio en resourcegroep als de VM. U deze instellingen niet wijzigen wanneer u een back-up rechtstreeks vanuit de VM-instellingen inschakelt.

   ![Wizard Back-up inschakelen](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. Ga als volgt te **werk in Het back-upbeleid kiezen:**

   - Laat het standaardbeleid achter. Hiermee wordt één keer per dag een back-up gemaakt van de VM op het opgegeven tijdstip en worden back-ups gedurende 30 dagen in de kluis bewaard.
   - Selecteer een bestaand back-upbeleid als u er een hebt.
   - Maak een nieuw beleid en definieer de beleidsinstellingen.  

   ![Back-upbeleid selecteren](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Klik **op Back-up inschakelen**. Hiermee wordt het back-upbeleid gekoppeld aan de VM.

    ![Knop Backup inschakelen](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. U de voortgang van de configuratie in de portalmeldingen bijhouden.
9. Nadat de taak is voltooid, klikt u in het menu VM op **Back-up**. De pagina toont de back-upstatus voor de VM, informatie over herstelpunten, taken die worden uitgevoerd en uitgegeven waarschuwingen.

   ![Back-upstatus](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Nadat back-up is ingemaakt, wordt een eerste back-up uitgevoerd. U de eerste back-up onmiddellijk starten of wachten tot deze volgens het back-upschema wordt gestart.
    - Totdat de eerste back-up is voltooid, wordt de **status Laatste back-up** weergegeven als **Waarschuwing (Initiële back-up in behandeling).**
    - Als u wilt zien wanneer de volgende geplande back-up wordt uitgevoerd, klikt u op de naam van het back-upbeleid.

## <a name="run-a-backup-immediately"></a>Direct een back-up uitvoeren

1. Als u een back-up onmiddellijk wilt uitvoeren, klikt u in het menu VM nu op > **Back-up maken.** **Backup**

    ![Back-up uitvoeren](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. Gebruik **in Nu back-up** het agendabesturingselement om te selecteren tot het herstelpunt > en **OK**blijft.

    ![Dag van het bewaren van back-ups](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Portalmeldingen laten u weten dat de back-uptaak is geactiveerd. Als u de voortgang van de back-up wilt controleren, klikt u op **Alle taken weergeven**.

## <a name="back-up-from-the-recovery-services-vault"></a>Back-up smaken uit de kluis van Recovery Services

Volg de instructies in dit artikel om back-ups voor Azure VM's in te schakelen door een Azure Backup Recovery Services-kluis in te stellen en back-up sanueren in de kluis.

>[!NOTE]
> **Azure Backup ondersteunt nu selectieve schijfback-up en herstel met behulp van de Azure Virtual Machine-back-upoplossing.**
>
>Tegenwoordig ondersteunt Azure Backup een back-up van alle schijven (besturingssysteem en gegevens) in een VM samen met behulp van de back-upoplossing voor virtuele machines. Met de functionaliteit van de uitgesloten schijf krijgt u een optie om een of enkele back-ups te maken van de vele gegevensschijven in een vm. Dit biedt een efficiënte en kosteneffectieve oplossing voor uw back-up- en herstelbehoeften. Elk herstelpunt bevat gegevens van de schijven die zijn opgenomen in de back-upbewerking, waarmee u verder een subset van schijven laten herstellen vanaf het gegeven herstelpunt tijdens de herstelbewerking. Dit geldt voor het herstellen van zowel de momentopname als de kluis.
>
>**Als u zich wilt aanmelden voor de preview, schrijft u ons opAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Volgende stappen

- Als u problemen ondervindt met een van de procedures in dit artikel, raadpleegt u de [handleiding voor het oplossen van problemen.](backup-azure-vms-troubleshoot.md)
- [Meer informatie over](backup-azure-manage-vms.md) het beheren van uw back-ups.
