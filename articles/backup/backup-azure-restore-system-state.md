---
title: Systeem status herstellen naar een Windows-Server
description: Stapsgewijze uitleg over het herstellen van de systeem status van Windows Server vanuit een back-up in Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 08/18/2017
ms.openlocfilehash: 8e256fbac0651b4c237c540151b3377927989d36
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172814"
---
# <a name="restore-system-state-to-windows-server"></a>Systeem status herstellen naar Windows Server

In dit artikel wordt uitgelegd hoe u back-ups van Windows Server-systeem status kunt herstellen vanuit een Azure Recovery Services kluis. Als u de systeem status wilt herstellen, moet u een systeem status back-up hebben (gemaakt met behulp van de instructies in een back-up van de [systeem status](backup-azure-system-state.md#back-up-windows-server-system-state)en ervoor zorgen dat u de [meest recente versie van de Microsoft Azure Recovery Services-agent (Mars)](https://aka.ms/azurebackup_agent)hebt geïnstalleerd. Het herstellen van de Windows Server-systeem status gegevens van een Azure Recovery Services kluis is een proces dat uit twee stappen bestaat:

1. De systeem status herstellen als bestanden van Azure Backup. Bij het herstellen van de systeem status als bestanden van Azure Backup, kunt u het volgende doen:
   * Herstel de systeem status op dezelfde server als de back-ups zijn gemaakt of
   * Herstel het systeem status bestand naar een andere server.

2. Pas de herstelde systeem status bestanden toe op een Windows-Server.

## <a name="recover-system-state-files-to-the-same-server"></a>Systeem status bestanden herstellen op dezelfde server

In de volgende stappen wordt uitgelegd hoe u de configuratie van uw Windows-Server terugdraait naar een eerdere status. Het kan erg waardevol zijn om uw server configuratie terug te brengen naar een bekende, stabiele status. Met de volgende stappen herstelt u de systeem status van de server van een Recovery Services kluis.

1. Open de **Microsoft Azure Backup**-module. Als u niet weet waar de module is geïnstalleerd, zoekt u op de computer of server naar **Microsoft Azure backup**.

    De bureau blad-app moet worden weer gegeven in de zoek resultaten.

2. Klik op **gegevens herstellen** om de wizard te starten.

    ![Gegevens herstellen](./media/backup-azure-restore-windows-server/recover.png)

3. Selecteer in het deel venster **aan** de slag de optie **deze server (`<server name>`)** en klik op **volgende**om de gegevens op dezelfde server of computer te herstellen.

    ![Selecteer deze optie voor de server om de gegevens op dezelfde computer te herstellen](./media/backup-azure-restore-system-state/samemachine.png)

4. Kies in het deel venster **herstel modus selecteren** de optie **systeem status** en klik vervolgens op **volgende**.

    ![Door bestanden bladeren](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. Selecteer een herstel punt in de agenda in het deel venster **volume en datum selecteren** .

    U kunt herstellen vanaf een herstel punt in de tijd. **Vetgedrukte** datums geven de beschik baarheid van ten minste één herstel punt aan. Wanneer u een datum selecteert en er meerdere herstel punten beschikbaar zijn, kiest u het specifieke herstel punt in het vervolg keuzemenu **tijd** .

    ![Volume en datum](./media/backup-azure-restore-system-state/select-date.png)

6. Wanneer u het herstel punt hebt gekozen dat u wilt herstellen, klikt u op **volgende**.

    Azure Backup koppelt het lokale herstel punt en gebruikt dit als een herstel volume.

7. Geef in het volgende deel venster de bestemming op voor de herstelde systeem status bestanden en klik op **Bladeren** om Windows Verkenner te openen en de gewenste bestanden en mappen te vinden. Met deze optie kunt **u kopieën maken, zodat u met beide versies**kopieën van afzonderlijke bestanden in een bestaand systeem status bestands archief maakt, in plaats van de kopie van het hele archief van de systeem status te maken.

    ![Herstel opties](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Controleer de details van herstel in het **bevestigings** venster en klik op **herstellen**.

   ![Klik op herstellen om de herstel actie te bevestigen](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Kopieer de *WindowsImageBackup* -map in de herstel bestemming naar een niet-kritiek volume van de-server. Normaal gesp roken is het volume van het Windows-besturings systeem het essentiële volume.

10. Nadat het herstel is voltooid, volgt u de stappen in de sectie, de [herstelde systeem status bestanden Toep assen op de Windows-Server](backup-azure-restore-system-state.md)om het systeem status herstel proces te volt ooien.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Systeem status bestanden herstellen naar een alternatieve server

Als uw Windows-Server beschadigd of niet toegankelijk is en u deze wilt herstellen naar een stabiele status door de systeem status van Windows Server te herstellen, kunt u de systeem status van de beschadigde server herstellen vanaf een andere server. Gebruik de volgende stappen om de systeem status te herstellen op een afzonderlijke server.  

De terminologie die in deze stappen wordt gebruikt, omvat:

* *Bron machine* : de oorspronkelijke machine waarvan de back-up is gemaakt en die momenteel niet beschikbaar is.
* *Doel computer* : de computer waarop de gegevens worden hersteld.
* Voor beeld van de *kluis* : de Recovery Services kluis waarmee de *bron computer* en de *doel computer* zijn geregistreerd. <br/>

> [!NOTE]
> Back-ups die zijn gemaakt van de ene machine kunnen niet worden hersteld naar een computer waarop een eerdere versie van het besturings systeem wordt uitgevoerd. Back-ups die zijn gemaakt van een Windows Server 2016-computer, kunnen bijvoorbeeld niet worden hersteld naar Windows Server 2012 R2. De inverse is echter mogelijk. U kunt back-ups van Windows Server 2012 R2 gebruiken om Windows Server 2016 te herstellen.
>

1. Open de module **Microsoft Azure backup** op de *doel computer*.
2. Zorg ervoor dat de *doel computer* en de *bron machine* zijn geregistreerd bij dezelfde Recovery Services kluis.
3. Klik op **gegevens herstellen** om de werk stroom te initiëren.
4. **Een andere server** selecteren

    ![Andere server](./media/backup-azure-restore-system-state/anotherserver.png)

5. Geef het kluis referentie bestand op dat overeenkomt met de voor *beeld-kluis*. Als het kluis referentie bestand ongeldig is (of is verlopen), downloadt u een nieuw kluis referentie bestand van de voor *beeld-kluis* in de Azure Portal. Zodra het kluis referentie bestand is ingevuld, wordt de Recovery Services kluis die aan het kluis referentie bestand is gekoppeld weer gegeven.

6. Selecteer in het deel venster back-upserver selecteren de *bron machine* in de lijst met weer gegeven machines.
7. Kies in het deel venster herstel modus selecteren de optie **systeem status** en klik op **volgende**.

    ![Zoeken](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. Selecteer een herstel punt in de agenda in het deel venster **volume en datum selecteren** . U kunt herstellen vanaf een herstel punt in de tijd. **Vetgedrukte** datums geven de beschik baarheid van ten minste één herstel punt aan. Wanneer u een datum selecteert en er meerdere herstel punten beschikbaar zijn, kiest u het specifieke herstel punt in het vervolg keuzemenu **tijd** .

    ![Items zoeken](./media/backup-azure-restore-system-state/select-date.png)

9. Wanneer u het herstel punt hebt gekozen dat u wilt herstellen, klikt u op **volgende**.

10. Geef in het deel venster **systeem status herstel modus selecteren** het doel op waar u de systeem status bestanden wilt herstellen en klik vervolgens op **volgende**.

    ![Versleuteling](./media/backup-azure-restore-system-state/recover-as-files.png)

    Met deze optie kunt **u kopieën maken, zodat u met beide versies**kopieën van afzonderlijke bestanden in een bestaand systeem status bestands archief maakt, in plaats van de kopie van het hele archief van de systeem status te maken.

11. Controleer de details van herstel in het bevestigings venster en klik op **herstellen**.

    ![Klik op de knop herstellen om het herstel proces te bevestigen](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Kopieer de *WindowsImageBackup* -map naar een niet-kritiek volume van de server (bijvoorbeeld D:\). Normaal gesp roken is het volume van het Windows-besturings systeem het essentiële volume.

13. Om het herstel proces te volt ooien, gebruikt u de volgende sectie om [de herstelde systeem status bestanden toe te passen op een Windows-Server](#apply-restored-system-state-on-a-windows-server).

## <a name="apply-restored-system-state-on-a-windows-server"></a>Herstelde systeem status Toep assen op een Windows-Server

Nadat u de systeem status hebt hersteld als bestanden met behulp van Azure Recovery Services agent, gebruikt u het hulp programma Windows Server Back-up om de herstelde systeem status toe te passen op Windows Server. Het hulp programma Windows Server Back-up is al beschikbaar op de server. In de volgende stappen wordt uitgelegd hoe de herstelde systeem status moet worden toegepast.

1. Gebruik de volgende opdrachten om de server opnieuw op te starten in de *modus Active Directory-Services herstellen*. In een opdracht prompt met verhoogde bevoegdheid:

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

2. Nadat het systeem opnieuw is opgestart, opent u de module Windows Server Back-up. Als u niet weet waar de module is geïnstalleerd, zoekt u op de computer of server naar **Windows Server back-up**.

    De bureau blad-app wordt weer gegeven in de zoek resultaten.

3. Selecteer in de module de optie **lokale back-up**.

    ![Lokale back-up selecteren om van daaruit te herstellen](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. Klik op de lokale back-upconsole in het **deel venster acties**op **herstellen** om de wizard herstellen te openen.

5. Selecteer de optie, **een back-up die is opgeslagen op een andere locatie**en klik op **volgende**.

   ![kiezen om naar een andere server te herstellen](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Wanneer u het locatie type opgeeft, selecteert u **externe gedeelde map** als uw systeem status back-up is hersteld naar een andere server. Als uw systeem status lokaal is hersteld, selecteert u **lokale stations**.

    ![selecteren of u wilt herstellen van de lokale server of een andere](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Voer het pad naar de *WindowsImageBackup* -map in of kies het lokale station dat deze map bevat (bijvoorbeeld D:\WindowsImageBackup), hersteld als onderdeel van het herstel van de systeem status bestanden met behulp van Azure Recovery Services agent en klik op **volgende**.

    ![pad naar het gedeelde bestand](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Selecteer de systeem status versie die u wilt herstellen en klik op **volgende**.

9. Selecteer in het deel venster herstel type selecteren de optie **systeem status** en klik op **volgende**.

10. Voor de locatie van de systeem status herstel selecteert u **oorspronkelijke locatie**en klikt u op **volgende**.

11. Controleer de details van de bevestiging, Controleer de instellingen voor opnieuw opstarten en klik op **herstellen** om de herstelde systeem status bestanden toe te passen.

    ![Start de herstel systeem status bestanden](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Speciale overwegingen voor herstel van de systeem status op Active Directory server

Back-up van de systeem status bevat Active Directory gegevens. Gebruik de volgende stappen om Active Directory-domein-service (AD DS) te herstellen van de huidige status naar een eerdere status.

1. Start de domein controller opnieuw op in de modus Active Directory terugzetten (DSRM).
2. Volg de onderstaande [stappen om](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx) Windows Server back-up-cmdlets te gebruiken om AD DS te herstellen.

## <a name="troubleshoot-failed-system-state-restore"></a>Problemen met het herstellen van de systeem status oplossen

Als het vorige proces van het Toep assen van de systeem status niet is voltooid, gebruikt u de Windows herstel omgeving (Win RE) om uw Windows-Server te herstellen. In de volgende stappen wordt uitgelegd hoe u kunt herstellen met Win RE. Gebruik deze optie alleen als Windows Server niet normaal wordt opgestart na een systeem status herstel. Met het volgende proces worden niet-systeem gegevens gewist, wees voorzichtig.

1. Start uw Windows-Server op in de Windows herstel omgeving (Win RE).

2. Selecteer problemen oplossen in de drie beschik bare opties.

    ![menu openen](./media/backup-azure-restore-system-state/winre-1.png)

3. In het scherm **Geavanceerde opties** selecteert u **opdracht prompt** en geeft u de gebruikers naam en het wacht woord voor de server beheerder op.

   ![menu openen](./media/backup-azure-restore-system-state/winre-2.png)

4. Geef de gebruikers naam en het wacht woord van de server beheerder op.

    ![menu openen](./media/backup-azure-restore-system-state/winre-3.png)

5. Wanneer u de opdracht prompt in de beheerders modus opent, voert u de volgende opdracht uit om de back-upversie van de systeem status te verkrijgen.

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![back-upversies van systeem status ophalen](./media/backup-azure-restore-system-state/winre-4.png)

6. Voer de volgende opdracht uit om alle volumes te verkrijgen die beschikbaar zijn in de back-up.

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![back-upversies van systeem status ophalen](./media/backup-azure-restore-system-state/winre-5.png)

7. Met de volgende opdracht worden alle volumes hersteld die deel uitmaken van de systeem status back-up. Houd er rekening mee dat met deze stap alleen de essentiële volumes die deel uitmaken van de systeem status, worden hersteld. Alle niet-systeem gegevens worden gewist.

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![back-upversies van systeem status ophalen](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>Volgende stappen

* Nu u uw bestanden en mappen hebt hersteld, kunt u [uw back-ups beheren](backup-azure-manage-windows-server.md).
