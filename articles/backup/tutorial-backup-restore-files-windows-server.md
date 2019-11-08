---
title: Bestanden herstellen van Azure naar een Windows-server
description: In deze zelf studie leert u hoe u de MARS-agent (Microsoft Azure Recovery Services agent) kunt gebruiken voor het herstellen van items van Azure naar een Windows-Server.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 02/14/2018
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: a0945c70b271d5213e8eb5b6adbd768ec390f40b
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73746929"
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Bestanden herstellen van Azure naar een Windows-server

Met Azure Backup kunt u afzonderlijke items uit de back-ups van uw Windows-Server herstellen. Het herstellen van individuele bestanden is handig als u bestanden die per ongeluk zijn verwijderd snel wilt herstellen. Deze zelfstudie behandelt het gebruiken van de MARS-agent (Microsoft Azure Recovery Services) om items te herstellen uit back-ups die u eerder in Azure hebt gemaakt. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Herstel van afzonderlijke items initiëren
> * Een herstelpunt selecteren
> * Items herstellen vanaf een herstelpunt

In deze zelfstudie wordt ervan uitgegaan dat u de stappen in [Een back-up maken van Windows Server naar Azure](backup-configure-vault.md) al hebt uitgevoerd, en dat u ten minste één back-up van uw Windows Server-bestanden in Azure hebt.

## <a name="initiate-recovery-of-individual-items"></a>Herstel van afzonderlijke items initiëren

Er wordt een handige gebruikersinterfacewizard met de naam Microsoft Azure Backup geïnstalleerd bij de MARS-agent (Microsoft Azure Recovery Services). De wizard Microsoft Azure Backup werkt samen met de MARS-agent (Microsoft Azure Recovery Services) om back-up gegevens op te halen van in Azure opgeslagen herstelpunten. Gebruik de wizard Microsoft Azure Backup om de bestanden of mappen te identificeren die u wilt herstellen naar Windows Server.

1. Open de **Microsoft Azure Backup**-module. U vindt deze door te zoeken naar **Microsoft Azure Backup** op uw machine.

    ![Back-up in behandeling](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. Klik in de wizard op **Gegevens herstellen** in het **actievenster** van de agentconsole om de wizard **Gegevens herstellen** te starten.

    ![Back-up in behandeling](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. Selecteer op de pagina **Aan de slag** pagina **Deze server (servernaam)** en klik op **Volgende**.

4. Selecteer op de pagina **Selecteer herstelmodus** **Afzonderlijke bestanden en mappen** en klik op **Volgende** om het proces voor het selecteren van een herstelpunt te starten.

5. Selecteer op de pagina **Selecteer volume en datum** het volume dat de bestanden of mappen bevat die u wilt herstellen en klik op **Koppelen**. Selecteer een datum en selecteer in de vervolgkeuzelijst een tijd die overeenkomt met een herstelpunt. **Vet** weergegeven datums geven aan dat er ten minste één herstelpunt beschikbaar is op die dag.

    ![Back-up in behandeling](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)

    Wanneer u op **Koppelen** klikt, maakt Azure Backup het herstelpunt beschikbaar als een schijf. Blader door de schijf en herstel de gewenste bestanden.

## <a name="restore-items-from-a-recovery-point"></a>Items herstellen vanaf een herstelpunt

1. Nadat het herstelvolume is gekoppeld, klikt u op **Bladeren** om Windows Verkenner te openen en de bestanden en mappen te zoeken die u wilt herstellen.

    ![Back-up in behandeling](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    U kunt de bestanden rechtstreeks vanuit het herstelvolume openen en de bestanden controleren.

2. Kopieer in Windows Verkenner de bestanden en/of de mappen die u wilt herstellen en plak ze op elke gewenste locatie op de server.

    ![Back-up in behandeling](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. Wanneer u klaar bent met het herstellen van de bestanden en/of mappen klikt u op de pagina **Bladeren en herstelbestanden** van de wizard **Gegevens herstellen** op **Ontkoppelen**.

    ![Back-up in behandeling](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4. Klik op **Ja** om te bevestigen dat u het volume wilt ontkoppelen.

    Nadat de momentopname ontkoppeld is, wordt er **Taak voltooid** weergegeven in het deelvenster **Taken** in de console van de agent.

## <a name="next-steps"></a>Volgende stappen

Dit was de laatste in de reeks zelfstudies over het maken van back-ups en het herstellen van Windows Server-gegevens naar Azure. Zie voor meer informatie over Azure Backup het PowerShell-voorbeeld voor het maken van back-ups van versleutelde virtuele machines.

> [!div class="nextstepaction"]
> [Back-up maken van een versleutelde VM](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
