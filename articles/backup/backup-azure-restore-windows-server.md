---
title: Gegevens in Azure herstellen naar een Windows-server
description: In dit artikel leest u hoe u gegevens die in Azure zijn opgeslagen, herstellen naar een Windows-server of Windows-computer met de Mars-agent (Microsoft Azure Recovery Services).
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: 25ca8eecaeb615f071340188a23fae7978ddb75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409810"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Bestanden naar Windows herstellen met het implementatiemodel azure resource manager

In dit artikel wordt uitgelegd hoe u gegevens uit een back-upkluis herstellen. Als u gegevens wilt herstellen, gebruikt u de wizard Gegevens herstellen in de Mars-agent (Microsoft Azure Recovery Services). U kunt:

* Gegevens herstellen naar dezelfde machine waaruit de back-ups zijn genomen.
* Gegevens herstellen naar een alternatieve machine

Gebruik de functie Direct herstellen om een schrijfbare herstelpuntmomentopname te monteren als herstelvolume. U vervolgens het herstelvolume verkennen en bestanden kopiëren naar een lokale computer, waardoor bestanden selectief worden hersteld.

> [!NOTE]
> De [Azure Backup-update van januari 2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) is vereist als u Instant Restore wilt gebruiken om gegevens te herstellen. Ook moeten de back-upgegevens worden beschermd in kluizen in landinstellingen die in het ondersteuningsartikel worden vermeld. Raadpleeg de [Azure Backup-update van januari 2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) voor de nieuwste lijst met landlocaties die Instant Restore ondersteunen.
>

Gebruik Instant Restore with Recovery Services vaults in de Azure-portal. Als u gegevens hebt opgeslagen in back-upkluizen, zijn ze geconverteerd naar vaults van Recovery Services. Als u Instant Restore wilt gebruiken, downloadt u de MARS-update en volgt u de procedures waarin Instant Restore wordt vermeld.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Instant Restore gebruiken om gegevens naar dezelfde machine te herstellen

Als u per ongeluk een bestand hebt verwijderd en het wilt herstellen naar dezelfde machine (waaruit de back-up wordt genomen), helpen de volgende stappen u de gegevens te herstellen.

1. Open de **Microsoft Azure Backup**-module. Als u niet weet waar de module is geïnstalleerd, zoekt u op de computer of server naar **Microsoft Azure Backup.**

    De bureaublad-app moet worden weergegeven in de zoekresultaten.

2. Selecteer **Gegevens herstellen** om de wizard te starten.

    ![Schermafbeelding van Azure Backup, met gegevens herstellen gemarkeerd](./media/backup-azure-restore-windows-server/recover.png)

3. Selecteer **Deze server (`<server name>`)** > **Volgende**op de pagina Aan **de slag** om de gegevens naar dezelfde server of computer te herstellen.

    ![Schermafbeelding van de pagina Gegevenswizard Herstellen Aan de slag](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Kies op de pagina **Herstelmodus selecteren** de optie **Afzonderlijke bestanden en mappen** > **Volgende**.

    ![Schermafbeelding van de pagina Gegevens wizard Gegevens selecteren Selecteren Herstelmodus](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > Voor de optie om afzonderlijke bestanden en mappen te herstellen, is .NET Framework 4.5.2 of hoger vereist. Als u de optie **Afzonderlijke bestanden en mappen** niet ziet, moet u .NET Framework upgraden naar versie 4.5.2 of hoger en het opnieuw proberen.
 
   > [!TIP]
   > Met de optie **Afzonderlijke bestanden en mappen** u snel toegang krijgen tot de gegevens van het herstelpunt. Het is geschikt voor het herstellen van individuele bestanden, met formaten van niet meer dan 80 GB, en biedt overdrachts- of kopieersnelheden tot 6 MBps tijdens het herstel. Met de optie **Volume** worden alle back-upgegevens in een bepaald volume hersteld. Deze optie biedt hogere overdrachtssnelheden (tot 60 MBps), wat ideaal is voor het herstellen van grote gegevens of volledige volumes.

5. Selecteer **op** de pagina Volume en Datum selecteren het volume dat de bestanden en mappen bevat die u wilt herstellen.

    Selecteer in de agenda een herstelpunt. Datums **in vet** geven de beschikbaarheid van ten minste één herstelpunt aan. Als er binnen één datum meerdere herstelpunten beschikbaar zijn, kiest u het specifieke herstelpunt in het vervolgkeuzemenu **Tijd.**

    ![Schermafbeelding van pagina Gegevens selecteren Selecteren Volume en Datum](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Nadat u het herstelpunt hebt gekozen om te herstellen, selecteert u **Mount**.

    Azure Backup monteert het lokale herstelpunt en gebruikt het als herstelvolume.

7. Selecteer **op** de pagina Bestanden bladeren en bestanden herstellen de optie **Bladeren** om Windows Verkenner te openen en zoek de gewenste bestanden en mappen.

    ![Schermafbeelding van de pagina Bladeren van de wizard Gegevens herstellen en bestanden herstellen](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. Kopieer in Windows Verkenner de bestanden en mappen die u wilt herstellen en plak ze op elke locatie die lokaal op de server of computer staat. U de bestanden rechtstreeks vanaf het herstelvolume openen of streamen en controleren of u de juiste versies herstelt.

    ![Schermafbeelding van Windows Verkenner, met Kopiëren gemarkeerd](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Wanneer u klaar bent, selecteert u op de pagina **Bestanden bladeren en herstellen** de optie **Uitdemonteren**. Selecteer vervolgens **Ja** om te bevestigen dat u het volume wilt opheffen.

    ![Schermafbeelding van de pagina Bladeren van de wizard Gegevens herstellen en bestanden herstellen](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Als u **Unmount**niet selecteert, blijft het herstelvolume 6 uur lang gemonteerd vanaf het moment dat het werd gemonteerd. De montagetijd wordt echter verlengd tot maximaal 24 uur in het geval van een doorlopende bestandskopie. Er worden geen back-upbewerkingen uitgevoerd terwijl het volume is gemonteerd. Elke back-upbewerking die gepland is voor gebruik tijdens de tijd dat het volume is gemonteerd, wordt uitgevoerd nadat het herstelvolume is ontkoppeld.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Direct herstellen gebruiken om gegevens naar een alternatieve machine te herstellen

Als uw hele server verloren gaat, u nog steeds gegevens van Azure Backup herstellen naar een andere machine. De volgende stappen illustreren de werkstroom.

Deze stappen omvatten de volgende terminologie:

* *Bronmachine* – De originele machine waaruit de back-up is genomen en die momenteel niet beschikbaar is.
* *Doelmachine* – De machine waarop de gegevens worden hersteld.
* *Voorbeeldkluis* – De kluis Van Recovery Services waarop de bronmachine en de doelmachine zijn geregistreerd.

> [!NOTE]
> Back-ups kunnen niet worden hersteld naar een doelmachine waarop een eerdere versie van het besturingssysteem wordt uitgevoerd. Een back-up van een Windows 7-computer kan bijvoorbeeld worden hersteld op een Windows 7 -computer (of hoger). Een back-up die is gemaakt van een Windows 8-computer kan niet worden hersteld naar een Windows 7-computer.
>
>

1. Open de module **Microsoft Azure Backup** op de doelmachine.

2. Zorg ervoor dat de doelmachine en de bronmachine zijn geregistreerd bij dezelfde kluis van Recovery Services.

3. Selecteer **Gegevens herstellen** om de wizard Gegevens herstellen te **openen**.

    ![Schermafbeelding van Azure Backup, met gegevens herstellen gemarkeerd](./media/backup-azure-restore-windows-server/recover.png)

4. Selecteer **op de** pagina Aan de slag **een andere server**.

    ![Schermafbeelding van de pagina Gegevenswizard Herstellen Aan de slag](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Geef het kluisreferentiebestand op dat overeenkomt met de voorbeeldkluis en selecteer **Volgende**.

    Als het kluisreferentiebestand ongeldig is (of is verlopen), downloadt u een nieuw kluisgegevensbestand uit de voorbeeldkluis in de Azure-portal. Nadat u een geldige kluisreferentie hebt opgemaakt, wordt de naam van de bijbehorende back-upkluis weergegeven.

6. Selecteer op de pagina **Back-upserver selecteren** de bronmachine in de lijst met weergegeven machines en geef de wachtwoordzin op. Selecteer **vervolgens Volgende**.

    ![Schermafbeelding van de pagina Gegevens wijzigen Selecteer Back-upserver](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Selecteer op de pagina **Herstelmodus selecteren** de optie **Afzonderlijke bestanden en mappen** > **Volgende**.

    ![Schermafbeelding van de pagina Gegevens wizard Gegevens selecteren Selecteren Herstelmodus](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Selecteer **op** de pagina Volume en Datum selecteren het volume dat de bestanden en mappen bevat die u wilt herstellen.

    Selecteer in de agenda een herstelpunt. Datums **in vet** geven de beschikbaarheid van ten minste één herstelpunt aan. Als er binnen één datum meerdere herstelpunten beschikbaar zijn, kiest u het specifieke herstelpunt in het vervolgkeuzemenu **Tijd.**

    ![Schermafbeelding van pagina Gegevens selecteren Selecteren Volume en Datum](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Selecteer **Mount** om het herstelpunt lokaal te monteren als herstelvolume op uw doelmachine.

10. Selecteer **op de** pagina Bestanden bladeren en bestanden herstellen de optie **Bladeren** om Windows Verkenner te openen en zoek de gewenste bestanden en mappen.

    ![Schermafbeelding van de pagina Bladeren door de wizard Gegevens herstellen en bestanden herstellen](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. Kopieer in Windows Verkenner de bestanden en mappen vanaf het herstelvolume en plak ze op de locatie van uw doelmachine. U de bestanden rechtstreeks vanaf het herstelvolume openen of streamen en controleren of de juiste versies zijn hersteld.

    ![Schermafbeelding van Windows Verkenner, met Kopiëren gemarkeerd](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Wanneer u klaar bent, selecteert u op de pagina **Bestanden bladeren en herstellen** de optie **Uitdemonteren**. Selecteer vervolgens **Ja** om te bevestigen dat u het volume wilt opheffen.

    ![Schermafbeelding van de pagina Bladeren door de wizard Gegevens herstellen en bestanden herstellen](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Als u **Unmount**niet selecteert, blijft het herstelvolume 6 uur lang gemonteerd vanaf het moment dat het werd gemonteerd. De montagetijd wordt echter verlengd tot maximaal 24 uur in het geval van een doorlopende bestandskopie. Er worden geen back-upbewerkingen uitgevoerd terwijl het volume is gemonteerd. Elke back-upbewerking die gepland is voor gebruik tijdens de tijd dat het volume is gemonteerd, wordt uitgevoerd nadat het herstelvolume is ontkoppeld.
    >

## <a name="next-steps"></a>Volgende stappen

* Nu u uw bestanden en mappen hebt hersteld, u [uw back-ups beheren.](backup-azure-manage-windows-server.md)

* Zoek [veelgestelde vragen over het maken van back-ups van bestanden en mappen.](backup-azure-file-folder-backup-faq.md)
