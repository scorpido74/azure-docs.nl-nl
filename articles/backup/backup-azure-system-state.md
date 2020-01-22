---
title: Back-up van Windows-systeem status maken in azure
description: Meer informatie over het maken van een back-up van de systeem status van Windows Server-en/of Windows-computers naar Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: 847ed8fc5a6c102284a03fa593587792767d7913
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "76294011"
---
# <a name="back-up-windows-system-state-in-resource-manager-deployment"></a>Back-up van Windows-systeem status maken in Resource Manager-implementatie

In dit artikel wordt uitgelegd hoe u een back-up maakt van de systeem status van Windows Server naar Azure. Het is bedoeld om u te helpen door de basis beginselen.

Als u meer wilt weten over Azure Backup, lees dan dit [overzicht](backup-overview.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan waarmee u toegang hebt tot alle services van Azure.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Als u een back-up wilt maken van de systeem status van Windows Server, moet u een Recovery Services kluis in de regio waar u de gegevens wilt opslaan. U moet ook bepalen op welke manier u uw opslag wilt repliceren.

### <a name="to-create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

1. Meld u met uw Azure-abonnement aan bij [Azure Portal](https://portal.azure.com/) als u dit nog niet hebt gedaan.
2. Klik in het menu Hub op **Alle services**, typ in de lijst met resources **Recovery Services** en klik vervolgens op **Recovery Services-kluizen**.

    ![Een Recovery Services-kluis maken, stap 1](./media/backup-azure-system-state/open-rs-vault-list.png)

    Als er Recovery Services-kluizen in het abonnement aanwezig zijn, worden deze weergegeven.
3. Klik in het menu **Recovery Services-kluizen** op **Toevoegen**.

    ![Een Recovery Services-kluis maken, stap 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    De blade Recovery Services-kluis wordt geopend en u wordt gevraagd een **naam**, **abonnement**, **resourcegroep** en **locatie** in te voeren.

    ![Een Recovery Services-kluis maken, stap 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. Voer bij **Naam** een beschrijvende naam in om de kluis aan te duiden. De naam moet uniek zijn voor het Azure-abonnement. Typ een naam die tussen 2 en 50 tekens bevat. De naam moet beginnen met een letter en mag alleen letters, cijfers en afbreekstreepjes bevatten.

5. Kies het Azure-abonnement in de vervolgkeuzelijst in het gedeelte **Abonnement**. Als u slechts één abonnement hebt, wordt dit weergegeven en kunt u doorgaan met de volgende stap. Als u niet zeker weet welk abonnement u moet gebruiken, gebruikt u het standaard- (of voorgestelde) abonnement. Er zijn alleen meerdere mogelijkheden als uw organisatieaccount is gekoppeld aan meerdere Azure-abonnementen.

6. In het gedeelte **Resourcegroep**:

    * selecteert u **Nieuw** als u een resourcegroep wilt maken,
    Of
    * selecteert u **Bestaande gebruiken** en klikt u op de vervolgkeuzelijst om de lijst met beschikbare resourcegroepen te zien.

   Zie [Overzicht van Azure Resource Manager](../azure-resource-manager/management/overview.md) voor meer informatie over resourcegroepen.

7. Klik op **Locatie** om de geografische regio voor de kluis te selecteren. Deze keuze bepaalt de geografische regio waar uw back-upgegevens naartoe worden verzonden.

8. Klik onder aan de blade Recovery Services-kluis op **Maken**.

    Het kan enkele minuten duren voordat de Recovery Services-kluis is gemaakt. Controleer de statusmeldingen rechtsboven in de portal. Zodra de kluis is gemaakt, wordt deze weergegeven in de lijst met Recovery Services-kluizen. Als u uw kluis na enkele minuten niet ziet, klik dan op **Vernieuwen**.

    ![Op de knop Vernieuwen klikken](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Wanneer u uw kluis in de lijst met Recovery Services-kluizen ziet, kunt u de opslagredundantie instellen.

### <a name="set-storage-redundancy-for-the-vault"></a>Opslagredundantie instellen voor de kluis

Wanneer u een Recovery Services-kluis maakt, zorg er dan voor dat de opslagredundantie op de gewenste manier is geconfigureerd.

1. Klik op de blade **Recovery Services-kluizen** op de nieuwe kluis.

    ![De nieuwe kluis in de lijst met Recovery Services-kluizen selecteren](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Wanneer u de kluis selecteert, wordt de blade **Recovery Services-kluis** smaller en worden de blade Instellingen (*met bovenaan de kluisnaam*) en de blade met kluisdetails geopend.

    ![De opslagconfiguratie voor nieuwe kluis bekijken](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. Schuif op de blade Instellingen van de nieuwe kluis omlaag naar het gedeelte Beheren en klik op **Back-upinfrastructuur**.
    De blade Back-up maken van infrastructuur wordt geopend.
3. Klik op de blade Back-up maken van infrastructuur op **Back-up maken van configuratie** om de blade **Back-up maken van configuratie** te openen.

    ![De opslagconfiguratie voor nieuwe kluis instellen](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Kies het type opslagreplicatie dat geschikt is voor uw kluis.

    ![keuzes bij opslagconfiguratie](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Uw kluis heeft standaard geografisch redundante opslag. Als Azure uw primaire eindpunt is voor back-upopslag, blijf dan **Geografisch redundant** gebruiken. Als Azure niet uw primaire eindpunt is voor back-upopslag, kiest u **Lokaal redundant**, zodat u de kosten voor Azure-opslag verlaagt. U vindt meer informatie over de opties voor [geografisch redundante](../storage/common/storage-redundancy-grs.md) en [lokaal redundante ](../storage/common/storage-redundancy-lrs.md) opslag in dit [overzicht van opslagredundantie](../storage/common/storage-redundancy.md).

Nu u een kluis hebt gemaakt, configureert u deze voor het maken van een back-up van de Windows-systeem status.

## <a name="configure-the-vault"></a>De kluis configureren

1. Klik in het gedeelte Aan de slag van de blade van de Recovery Services-kluis (voor de kluis die u zojuist hebt gemaakt) op **Back-up** en dan op de blade **Aan de slag met black-ups**. Selecteer vervolgens **Doel van de back-up**.

    ![Open de blade back-updoelstelling](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    De blade **Back-updoelstelling** wordt geopend.

    ![Open de blade back-updoelstelling](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. Selecteer **On-premises** in het menu **Waar wordt uw workload uitgevoerd?** .

    U kiest **On-premises** omdat uw Windows Server of Windows-computer een fysieke machine is die zich niet in Azure bevindt.

3. Selecteer in het menu **waarvan wilt u een back-up maken?** de optie **systeem status**en klik op **OK**.

    ![Bestanden en mappen configureren](./media/backup-azure-system-state/backup-goal-system-state.png)

    Nadat u op OK hebt geklikt, wordt er een vinkje weergegeven naast **Back-updoelstelling** en wordt de blade **Infrastructuur voorbereiden** geopend.

    ![Nu de back-updoelstelling is geconfigureerd, gaat u de infrastructuur voorbereiden](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. Klik op de blade **Infrastructuur voorbereiden** op **Agent voor Windows Server of Windows Client downloaden**.

    ![infrastructuur voorbereiden](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Als u Windows Server Essential gebruikt, kiest u voor het downloaden van de agent voor Windows Server Essential. In een pop-upmenu wordt gevraagd of u MARSAgentInstaller.exe wilt uitvoeren of opslaan.

    ![Dialoogvenster MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. Klik in het downloadpop-upvenster op **Opslaan**.

    Standaard wordt het bestand **MARSagentinstaller.exe** opgeslagen in de map Downloads. Wanneer het installatieprogramma is voltooid, ziet u een pop-upvenster waarin wordt gevraagd of u het installatieprogramma wilt uitvoeren of de map wilt openen.

    ![infrastructuur voorbereiden](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    U hoeft de agent nog niet te installeren. U kunt de agent installeren nadat u de kluisreferenties hebt gedownload.

6. Klik op de blade **Infrastructuur voorbereiden** op **Downloaden**.

    ![kluisreferenties downloaden](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    De kluisreferenties worden naar de map Downloads gedownload. Nadat de kluisreferenties zijn gedownload, ziet u een pop-upvenster waarin u wordt gevraagd of u de referenties wilt openen of opslaan. Klik op **Opslaan**. Als u per ongeluk klikt op **Openen**, kunt u het dialoogvenster waarmee wordt geprobeerd de kluisreferenties te openen, laten mislukken. U kunt de kluisreferenties niet openen. Ga door naar de volgende stap. De kluisreferenties bevinden zich in de map Downloads.

    ![kluisreferenties downloaden is voltooid](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > De kluis referenties moeten alleen worden opgeslagen op een locatie die lokaal is voor de Windows-Server waarop u de agent wilt gebruiken.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>De agent installeren en registreren

> [!NOTE]
> Het maken van back-ups via Azure Portal is nog niet beschikbaar. Gebruik de Microsoft Azure Recovery Services-agent om een back-up te maken van de systeem status van Windows Server.
>

1. Zoek en dubbelklik op **MARSagentinstaller.exe** in de map Downloads (of een andere locatie waar het bestand is opgeslagen).

    Het installatieprogramma geeft een reeks berichten tijdens het uitpakken, installeren en registreren van de Recovery Services-agent.

    ![uitvoeren van installatiereferenties van de Recovery Services-agent](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Doorloop de installatiewizard van de Microsoft Azure Recovery Services Agent. Om de wizard volledig door te kunnen lopen, moet u:

   * Een locatie voor de installatie en de cachemap kiezen.
   * De informatie van uw proxyserver invoeren als u een proxyserver gebruikt om verbinding te maken met internet.
   * Uw gebruikersnaam en wachtwoord invoeren als u gebruikmaakt van een geverifieerde proxyserver.
   * De gedownloade kluisreferenties invoeren
   * De wachtwoordzin voor de versleuteling op een veilige locatie opslaan.

     > [!NOTE]
     > Als u de wachtwoordzin kwijtraakt of vergeet, kan Microsoft u niet helpen om de back-upgegevens te herstellen. Sla het bestand daarom op een veilige locatie op. Het is verplicht om een back-up te herstellen.
     >
     >

De agent wordt nu geïnstalleerd en uw computer wordt geregistreerd bij de kluis. U kunt nu uw back-up configureren en plannen.

## <a name="back-up-windows-server-system-state"></a>Back-ups maken van Windows Server-systeemstatus

De eerste back-up bevat twee taken:

* De back-up plannen
* De eerste keer een back-up maken van de systeem status

Gebruik de Microsoft Azure Recovery Services-agent om de eerste back-up uit te voeren.

> [!NOTE]
> U kunt back-ups maken van de systeem status op Windows Server 2008 R2 via Windows Server 2016. Het maken van een back-up van de systeem status wordt niet ondersteund op client-Sku's. De systeem status wordt niet weer gegeven als een optie voor Windows-clients of Windows Server 2008 SP2-machines.
>
>

### <a name="to-schedule-the-backup-job"></a>De back-up plannen

1. Open de Microsoft Azure Recovery Services-agent. U vindt deze door te zoeken naar **Microsoft Azure Backup** op uw machine.

    ![De Azure Recovery Services-agent starten](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. Klik in de Recovery Services-agent op **Back-up plannen**.

    ![Een back-up van de Windows Server plannen](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Op de pagina Aan de slag van de wizard Back-up plannen klikt u op **Volgende**.

4. Op de pagina Items selecteren voor back-up klikt u op **Items toevoegen**.

5. Selecteer **systeem status** en klik vervolgens op **OK**.

6. Klik op **Volgende**.

7. Selecteer de vereiste back-upfrequentie en het Bewaar beleid voor uw systeem status back-ups op de volgende pagina's.

8. Lees de informatie op de pagina Bevestiging en klik vervolgens op **Voltooien**.

9. Nadat u de wizard voor het maken van een back-upschema hebt doorlopen, klikt u op **Sluiten**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Voor de eerste keer een back-up maken van de Windows Server-systeem status

1. Zorg ervoor dat er geen updates in behandeling zijn voor Windows Server waarvoor opnieuw opstarten is vereist.

2. Klik in de Recovery Services-agent op **Nu een back-up maken** om de eerste seeding via het netwerk te voltooien.

    ![Windows Server maakt nu een back-up](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Selecteer **systeem status** in het scherm **back-upitem selecteren** dat wordt weer gegeven en klik op **volgende**.

4. Controleer op de pagina Bevestiging de instellingen die de wizard Nu back-up maken gebruikt om een back-up te maken van de machine. Klik vervolgens op **Back-up maken**.

5. Klik op **Sluiten** om de wizard te sluiten. Als u de wizard sluit voordat het back-upproces is voltooid, blijft de wizard op de achtergrond aanwezig.
    > [!NOTE]
    > De MARS-agent activeert SFC/verifyonly als onderdeel van de voor controle voor elke systeem status back-up. Dit is om ervoor te zorgen dat bestanden waarvan een back-up is gemaakt als onderdeel van de systeem status, de juiste versies hebben die overeenkomen met de Windows-versie. Meer informatie over System File Checker (SFC) vindt u in [dit artikel](https://docs.microsoft.com/windows-server/administration/windows-commands/sfc).
    >

Nadat de eerste back-up is voltooid, wordt de status **Taak voltooid** weergegeven in de back-upconsole.

  ![IR voltooid](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Vragen?

Als u vragen hebt of als er een functie is die u graag opgenomen ziet worden, [stuur ons dan uw feedback](https://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [back-ups maken van Windows-machines](backup-configure-vault.md).
* Nu u een back-up hebt gemaakt van uw Windows Server-systeem status, kunt u [uw kluizen en servers beheren](backup-azure-manage-windows-server.md).
* Als u een back-up moet herstellen, [zet de bestanden dan terug naar een Windows-machine](backup-azure-restore-windows-server.md) aan de hand van dit artikel.
