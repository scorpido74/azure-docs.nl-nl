---
title: Systeemstatus herstellen naar een Windows-server
description: Stap voor stap uitleg voor het herstellen van windows serversysteemstatus vanuit een back-up in Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 08/18/2017
ms.openlocfilehash: 6d46a091a4e620e26d05735f12a201009663e65d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602462"
---
# <a name="restore-system-state-to-windows-server"></a>Systeemstatus herstellen naar Windows Server

In dit artikel wordt uitgelegd hoe u back-ups van windows serversysteemstatus herstellen uit een kluis van Azure Recovery Services. Als u de systeemstatus wilt herstellen, moet u een systeemstatusback-up hebben (gemaakt met de instructies in [back-upsysteemstatus](backup-azure-system-state.md#back-up-windows-server-system-state)en ervoor zorgen dat u de [nieuwste versie van de MARS-agent (Microsoft Azure Recovery Services)](https://aka.ms/azurebackup_agent)hebt geïnstalleerd. Het herstellen van windows serversysteemstatusgegevens uit een azure recovery services-kluis is een proces in twee stappen:

1. Systeemstatus herstellen als bestanden van Azure Backup. Wanneer u systeemstatus als bestanden herstelt vanuit Azure Backup, u het als:
   * Systeemstatus herstellen naar dezelfde server waar de back-ups zijn gemaakt, of
   * Systeemstatusbestand herstellen naar een alternatieve server.

2. Pas de herstelde systeemstatusbestanden toe op een Windows Server.

## <a name="recover-system-state-files-to-the-same-server"></a>Systeemstatusbestanden herstellen op dezelfde server

In de volgende stappen wordt uitgelegd hoe u uw Windows Server-configuratie terugdraait naar een eerdere status. Het terugdraaien van uw serverconfiguratie naar een bekende, stabiele toestand, kan zeer waardevol zijn. De volgende stappen herstellen de systeemstatus van de server uit een kluis van Recovery Services.

1. Open de **Microsoft Azure Backup**-module. Als u niet weet waar de module is geïnstalleerd, zoekt u op de computer of server naar **Microsoft Azure Backup.**

    De bureaublad-app moet worden weergegeven in de zoekresultaten.

2. Klik **op Gegevens herstellen om** de wizard te starten.

    ![Gegevens herstellen](./media/backup-azure-restore-windows-server/recover.png)

3. Selecteer **Deze server`<server name>`( )** en klik op **Volgende**om **de** gegevens naar dezelfde server of computer te herstellen.

    ![Kies deze serveroptie om de gegevens naar dezelfde machine te herstellen](./media/backup-azure-restore-system-state/samemachine.png)

4. Kies **systeemstatus** in het deelvenster **Herstelmodus selecteren** en klik op **Volgende**.

    ![Bladeren door bestanden](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. Selecteer in de agenda in het deelvenster **Volume en Datum selecteren** een herstelpunt.

    U herstellen vanaf elk herstelpunt in de tijd. Datums **in vet** geven de beschikbaarheid van ten minste één herstelpunt aan. Als u een datum hebt geselecteerd en meerdere herstelpunten beschikbaar zijn, kiest u het specifieke herstelpunt in het vervolgkeuzemenu **Tijd.**

    ![Volume en datum](./media/backup-azure-restore-system-state/select-date.png)

6. Zodra u het herstelpunt hebt gekozen om te herstellen, klikt u op **Volgende**.

    Azure Backup monteert het lokale herstelpunt en gebruikt het als herstelvolume.

7. Geef in het volgende deelvenster de bestemming op voor de herstelde systeemstatusbestanden en klik op **Bladeren** om Windows Verkenner te openen en de gewenste bestanden en mappen te zoeken. De **optie, Kopieën maken zodat u beide versies hebt,** maakt kopieën van afzonderlijke bestanden in een bestaand systeemarchief in plaats van de kopie van het volledige systeemarchief te maken.

    ![Herstelopties](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Controleer de details van het herstel in het **bevestigingsvenster** en klik op **Herstellen**.

   ![klik op Herstellen om de herstelactie te herkennen](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Kopieer de *map WindowsImageBackup* in de herstelbestemming naar een niet-kritiek volume van de server. Meestal is het Windows-besturingssysteemvolume het kritieke volume.

10. Zodra het herstel is voltooid, volgt u de stappen in de sectie [Herstelde systeemstatusbestanden toepassen op de Windows Server](backup-azure-restore-system-state.md)om het herstelproces van de systeemstatus te voltooien.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Systeemstatusbestanden herstellen naar een alternatieve server

Als uw Windows Server beschadigd of ontoegankelijk is en u deze in een stabiele status wilt herstellen door de status van het Windows Server-systeem te herstellen, u de systeemstatus van de beschadigde server van een andere server herstellen. Gebruik de volgende stappen om de systeemstatus op een afzonderlijke server te herstellen.  

De terminologie die in deze stappen wordt gebruikt, omvat:

* *Bronmachine* – De originele machine waaruit de back-up is genomen en die momenteel niet beschikbaar is.
* *Doelmachine* – De machine waarop de gegevens worden hersteld.
* *Voorbeeldkluis* – De kluis Van Recovery Services waarop de *bronmachine* en *de doelmachine* zijn geregistreerd. <br/>

> [!NOTE]
> Back-ups die uit één machine zijn genomen, kunnen niet worden hersteld naar een machine met een eerdere versie van het besturingssysteem. Back-ups die zijn gemaakt van een Windows Server 2016-machine kunnen bijvoorbeeld niet worden hersteld naar Windows Server 2012 R2. Echter, de inverse is mogelijk. U back-ups van Windows Server 2012 R2 gebruiken om Windows Server 2016 te herstellen.
>

1. Open de module **Microsoft Azure Backup** op de *doelmachine*.
2. Zorg ervoor dat de *doelmachine* en de *bronmachine* zijn geregistreerd bij dezelfde kluis van Recovery Services.
3. Klik **op Gegevens herstellen** om de werkstroom te starten.
4. Een **andere server selecteren**

    ![Een andere server](./media/backup-azure-restore-system-state/anotherserver.png)

5. Geef het kluisreferentiebestand op dat overeenkomt met de *voorbeeldkluis.* Als het kluisreferentiebestand ongeldig is (of is verlopen), downloadt u een nieuw kluisgegevensbestand uit de *kluis voorbeeld* in de Azure-portal. Zodra het kluisreferentiebestand is verstrekt, wordt de kluis Van Herstelservices die is gekoppeld aan het kluisreferentiebestand weergegeven.

6. Selecteer in het deelvenster Back-upserver selecteren de *bronmachine* in de lijst met weergegeven machines.
7. Kies **systeemstatus** in het deelvenster Herstelmodus selecteren en klik op **Volgende**.

    ![Search](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. Selecteer in de agenda in het deelvenster **Volume en datum selecteren** een herstelpunt. U herstellen vanaf elk herstelpunt in de tijd. Datums **in vet** geven de beschikbaarheid van ten minste één herstelpunt aan. Als u een datum hebt geselecteerd en meerdere herstelpunten beschikbaar zijn, kiest u het specifieke herstelpunt in het vervolgkeuzemenu **Tijd.**

    ![Zoeken naar objecten](./media/backup-azure-restore-system-state/select-date.png)

9. Zodra u het herstelpunt hebt gekozen om te herstellen, klikt u op **Volgende**.

10. Geef in het deelvenster **Systeemstatusherstelmodus** selecteren de bestemming op waar u systeemstatusbestanden wilt herstellen en klik op **Volgende**.

    ![Versleuteling](./media/backup-azure-restore-system-state/recover-as-files.png)

    De **optie, Kopieën maken zodat u beide versies hebt,** maakt kopieën van afzonderlijke bestanden in een bestaand systeemarchief in plaats van de kopie van het volledige systeemarchief te maken.

11. Controleer de details van het herstel in het bevestigingsvenster en klik op **Herstellen**.

    ![klik op de knop Herstellen om het herstelproces te bevestigen](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Kopieer de *map WindowsImageBackup* naar een niet-kritiek volume\)van de server (bijvoorbeeld D: . Meestal is het Windows-besturingssysteem volume is het kritieke volume.

13. Als u het herstelproces wilt voltooien, gebruikt u de volgende sectie om [de herstelde systeemstatusbestanden toe](#apply-restored-system-state-on-a-windows-server)te passen op een Windows Server.

## <a name="apply-restored-system-state-on-a-windows-server"></a>Een herstelde systeemstatus toepassen op een Windows-server

Zodra u de systeemstatus hebt hersteld als bestanden met Azure Recovery Services Agent, gebruikt u het hulpprogramma voor Windows Server Backup om de herstelde systeemstatus toe te passen op Windows Server. Het hulpprogramma voor Windows Server Backup is al beschikbaar op de server. In de volgende stappen wordt uitgelegd hoe u de herstelde systeemstatus toepassen.

1. Gebruik de volgende opdrachten om uw server opnieuw op te starten in *de reparatiemodus van Directory Services*. In een opdrachtprompt met verhoogde bevoegdheid:

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

2. Open de module Windows Server Backup na het opnieuw opstarten. Als u niet weet waar de module is geïnstalleerd, zoekt u op de computer of server naar **Windows Server Backup.**

    De bureaublad-app wordt weergegeven in de zoekresultaten.

3. Selecteer **lokale back-up**in de module .

    ![Selecteer Lokale back-up om vanaf daar te herstellen](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. Klik op de console Lokale back-up in het **deelvenster Acties**op **Herstellen** om de wizard Herstel te openen.

5. Selecteer de **optie, Een back-up die op een andere locatie is opgeslagen**en klik op **Volgende**.

   ![kiezen om te herstellen naar een andere server](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Wanneer u het locatietype opgeeft, selecteert u **Gedeelde map op afstand** als de back-up van uw systeemstatus is hersteld naar een andere server. Als uw systeemstatus lokaal is hersteld, selecteert u **Lokale stations**.

    ![selecteren of u wilt herstellen van een lokale server of een andere server](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Voer het pad naar de *map WindowsImageBackup in* of kies het lokale station met deze map (bijvoorbeeld D:\WindowsImageBackup), hersteld als onderdeel van het herstel van systeemstatusbestanden met Azure Recovery Services Agent en klik op **Volgende**.

    ![pad naar het gedeelde bestand](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Selecteer de systeemstatusversie die u wilt herstellen en klik op **Volgende**.

9. Selecteer **systeemstatus** in het deelvenster Hersteltype selecteren en klik op **Volgende**.

10. Selecteer **Oorspronkelijke locatie**en klik op **Volgende**voor de locatie van het herstel van de systeemstatus .

11. Controleer de bevestigingsgegevens, controleer de herstelinstellingen en klik op **Herstellen** om de herstelde systeemstatusbestanden toe te passen.

    ![de bestanden van systeemstatus herstellen](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Speciale overwegingen voor systeemstatusherstel op Active Directory-server

Back-up van de systeemstatus bevat Active Directory-gegevens. Gebruik de volgende stappen om Active Directory Domain Service (AD DS) te herstellen van de huidige status naar een eerdere status.

1. Start de domeincontroller opnieuw in de Herstelmodus van Directory Services (DSRM).
2. Volg de stappen [hier](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-nonauthoritative-restore) om de cmdlets van Windows Server Backup te gebruiken om AD DS te herstellen.

## <a name="troubleshoot-failed-system-state-restore"></a>Mislukt herstel van systeemstatus oplossen

Als het vorige proces van het toepassen van systeemstatus niet is voltooid, gebruikt u de Windows-herstelomgeving (Win RE) om uw Windows Server te herstellen. In de volgende stappen wordt uitgelegd hoe u herstellen met Win RE. Gebruik deze optie alleen als Windows Server niet normaal wordt opgestart nadat een systeemstatus is hersteld. Het volgende proces wist niet-systeemgegevens, wees voorzichtig.

1. Start uw Windows Server op in de Windows-herstelomgeving (Win RE).

2. Selecteer Problemen oplossen in de drie beschikbare opties.

    ![openingsmenu](./media/backup-azure-restore-system-state/winre-1.png)

3. Selecteer **opdrachtprompt** in het scherm **Geavanceerde opties** en geef de gebruikersnaam en het wachtwoord van de serverbeheerder op.

   ![openingsmenu](./media/backup-azure-restore-system-state/winre-2.png)

4. Geef de gebruikersnaam en het wachtwoord van de serverbeheerder op.

    ![openingsmenu](./media/backup-azure-restore-system-state/winre-3.png)

5. Wanneer u de opdrachtprompt opent in de beheerdersmodus, voert u de volgende opdracht uit om de back-upversies van de systeemstatus te krijgen.

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![Systeemstatusback-upversies krijgen](./media/backup-azure-restore-system-state/winre-4.png)

6. Voer de volgende opdracht uit om alle beschikbare volumes in de back-up te krijgen.

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Systeemstatusback-upversies krijgen](./media/backup-azure-restore-system-state/winre-5.png)

7. Met de volgende opdracht worden alle volumes hersteld die deel uitmaken van de back-up van de systeemstatus. Houd er rekening mee dat deze stap alleen de kritieke volumes herstelt die deel uitmaken van de systeemstatus. Alle niet-systeemgegevens worden gewist.

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![Systeemstatusback-upversies krijgen](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>Volgende stappen

* Nu u uw bestanden en mappen hebt hersteld, u [uw back-ups beheren.](backup-azure-manage-windows-server.md)
