---
title: Back-ups maken van Windows-machines met de MARS-agent
description: Gebruik de Azure Backup micro soft Recovery Services-agent (MARS) om een back-up te maken van Windows-machines.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 990929cc95d1c25117873ca39415d33370456b91
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025534"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Back-up maken van Windows-machines met de Azure Backup MARS-agent

In dit artikel wordt uitgelegd hoe u een back-up maakt van Windows-machines met behulp van de [Azure backup](backup-overview.md) -service en de Microsoft Azure Recovery Services-agent (Mars), ook wel bekend als de Azure backup agent.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Controleer de vereisten en maak een Recovery Services kluis.
> * De MARS-agent downloaden en instellen
> * Maak een back-upbeleid en-schema.
> * Een back-up op aanvraag uitvoeren.

## <a name="about-the-mars-agent"></a>Over de MARS-agent

De MARS-agent wordt door Azure Backup gebruikt voor het maken van back-ups van bestanden, mappen en de systeem status van on-premises machines en Azure-Vm's naar een back-up Recovery Services kluis in Azure. U kunt de agent als volgt uitvoeren:

* Voer de agent rechtstreeks op on-premises Windows-computers uit, zodat ze rechtstreeks een back-up kunnen maken naar een back-upRecovery Services kluis in Azure.
* Voer de agent uit op virtuele Azure-machines waarop Windows wordt uitgevoerd (naast de Azure VM-extensie voor back-ups) om een back-up te maken van specifieke bestanden en mappen op de VM.
* Voer de agent uit op een Microsoft Azure Backup Server (MABS) of een DPM-server (System Center Data Protection-Manager). In dit scenario maken machines en workloads een back-up van MABS/DPM en vervolgens MABS/DPM een back-up naar een kluis in azure met behulp van de MARS-agent.
Waarvan u een back-up kunt maken, is afhankelijk van waar de agent is geïnstalleerd.

> [!NOTE]
> De primaire methode voor het maken van back-ups van virtuele Azure-machines is door gebruik te maken van een Azure Backup-extensie op de VM. Hiermee maakt u een back-up van de volledige VM. Mogelijk wilt u de MARS-agent naast de extensie installeren en gebruiken als u een back-up wilt maken van specifieke bestanden en mappen op de VM. [Meer informatie](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Stappen voor back-upproces](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Voordat u begint

* [Meer informatie](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) Azure Backup maakt een back-up van Windows-computers met de MARS-agent.
* [Meer informatie over](backup-architecture.md#architecture-back-up-to-dpmmabs) de back-uparchitectuur voor het uitvoeren van de Mars-agent op een secundaire MABS of DPM-server.
* [Bekijk](backup-support-matrix-mars-agent.md) wat wordt ondersteund en waarvan een back-up kan worden gemaakt met de Mars-agent.
* Controleer de Internet toegang op de computers waarvan u een back-up wilt maken.
* Als u een back-up wilt maken van een server of client naar Azure, hebt u een Azure-account nodig. Als u er nog geen hebt, kunt u in slechts een paar minuten een [gratis account](https://azure.microsoft.com/free/) maken.

### <a name="verify-internet-access"></a>Internet toegang controleren

Als uw computer beperkte internet toegang heeft, moet u ervoor zorgen dat de firewall-instellingen op de computer of de proxy deze Url's en dit IP-adres toestaan:

#### <a name="urls"></a>Adres

* www\.msftncsi.com
* *.Microsoft.com
* *.WindowsAzure.com
* *.microsoftonline.com
* *.windows.net

#### <a name="ip-addresses"></a>IP-adressen

* 20.190.128.0/18
* 40.126.0.0/18

De toegang tot alle hierboven vermelde Url's en IP-adressen maakt gebruik van het HTTPS-protocol op poort 443.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een Recovery Services kluis slaat alle back-ups en herstel punten op die u in de loop van de tijd maakt en bevat het back-upbeleid dat wordt toegepast op back-upcomputers. Maak als volgt een kluis:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met uw Azure-abonnement.

2. Zoek en selecteer **Recovery Services kluizen**.

    ![Een Recovery Services-kluis maken, stap 1](./media/backup-configure-vault/open-recovery-services-vaults.png)

3. Klik in het menu **Recovery Services kluizen** op **+ toevoegen**.

    ![Een Recovery Services-kluis maken, stap 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. Voer bij **Naam** een beschrijvende naam in om de kluis aan te duiden.

   * De naam moet uniek zijn voor het Azure-abonnement.
   * Dit kan twee tot 50 tekens bevatten.
   * De naam moet beginnen met een letter en mag alleen letters, cijfers en afbreek streepjes bevatten.

5. Selecteer het Azure-abonnement, de resource groep en de geografische regio waarin de kluis moet worden gemaakt. Back-upgegevens worden verzonden naar de kluis. Klik vervolgens op **Maken**.

    ![Een Recovery Services-kluis maken, stap 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   * Het kan even duren voordat de kluis is gemaakt.
   * Bewaak de status meldingen in de rechter bovenhoek van de portal. Als u de kluis na enkele minuten niet ziet, klikt u op **vernieuwen**.

     ![Op de knop Vernieuwen klikken](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>Opslag redundantie instellen

Azure Backup beheert automatisch de opslag voor de kluis. U moet opgeven hoe die opslag wordt gerepliceerd.

1. Klik op de blade **Recovery Services-kluizen** op de nieuwe kluis. Klik onder de sectie **instellingen** op **Eigenschappen**.
2. In **Eigenschappen**, onder **back-upconfiguratie**, klikt u op **bijwerken**.

3. Selecteer het type opslag replicatie en klik op **Opslaan**.

      ![De opslagconfiguratie voor nieuwe kluis instellen](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

* Als u Azure als een primair eind punt voor back-upopslag gebruikt, wordt u aangeraden de standaard **geo-redundante** instelling te gebruiken.
* Als Azure niet uw primaire eindpunt is voor back-upopslag, kiest u **Lokaal redundant**, zodat u de kosten voor Azure-opslag verlaagt.
* Meer informatie over [geo](../storage/common/storage-redundancy-grs.md) en [lokale](../storage/common/storage-redundancy-lrs.md) redundantie.

## <a name="download-the-mars-agent"></a>De MARS-agent downloaden

Down load de MARS-agent voor installatie op computers waarvan u een back-up wilt maken.

* Als u de agent al op alle computers hebt geïnstalleerd, moet u ervoor zorgen dat u de meest recente versie gebruikt.
* De nieuwste versie is beschikbaar in de portal of met behulp van een [directe down load](https://aka.ms/azurebackup_agent)

1. Klik in de kluis, onder **aan**de slag, op **back-up**.

    ![Open de blade back-updoelstelling](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. In **waar wordt uw workload uitgevoerd?** Selecteer **on-premises**. U moet deze optie selecteren, zelfs als u de MARS-agent wilt installeren op een virtuele Azure-machine.
3. Selecteer in **waarvan wilt u een back-up maken?** de optie **bestanden en mappen** en/of **systeem status**. Er zijn een aantal andere opties beschikbaar, maar deze worden alleen ondersteund als u een secundaire back-upserver uitvoert. Klik op **infra structuur voorbereiden**.

      ![Bestanden en mappen configureren](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. Down load de MARS-agent onder **Recovery Services-agent installeren**op de **infra structuur voorbereiden**.

    ![infrastructuur voorbereiden](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. Klik in het downloadpop-upvenster op **Opslaan**. Standaard wordt het bestand **MARSagentinstaller.exe** opgeslagen in de map Downloads.

6. Controleer nu **of u de nieuwste Recovery Services-agent al downloadt of gebruikt**en down load de kluis referenties.

    ![kluisreferenties downloaden](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. Klik op **Opslaan**. Het bestand wordt gedownload naar de downloadmap. Het kluis referentie bestand kan niet worden geopend.

## <a name="install-and-register-the-agent"></a>De agent installeren en registreren

1. Voer het bestand **MARSagentinstaller. exe** uit op de computers waarvan u een back-up wilt maken.
2. Geef in de wizard installatie van de MARS-agent > **installatie-instellingen**op waar u de agent wilt installeren en een locatie die u voor de cache wilt gebruiken. Klik op **Volgende**.
   * Azure Backup gebruikt de cache om moment opnamen van gegevens op te slaan voordat ze naar Azure worden verzonden.
   * De cache locatie moet vrije ruimte hebben die gelijk is aan ten minste 5% van de grootte van de gegevens waarvan u een back-up maakt.

    ![Installatie-instellingen voor de MARS-wizard](./media/backup-configure-vault/mars1.png)

3. Geef in **proxy configuratie**op hoe de agent die wordt uitgevoerd op de Windows-computer verbinding maakt met internet. Klik op **Volgende**.

   * Als u een aangepaste proxy gebruikt, geeft u de proxy-instellingen en referenties op, indien nodig.
   * Houd er rekening mee dat de agent toegang moet hebben tot [deze url's](#verify-internet-access).

     ![MARS-wizard Internet toegang](./media/backup-configure-vault/mars2.png)

4. Controleer de controle op vereisten bij de **installatie** en klik op **installeren**.
5. Nadat de agent is geïnstalleerd, klikt **u op door gaan naar registratie**.
6. Klik in de **wizard Server registreren** > **kluis identificatie**, blader en selecteer het referentie bestand dat u hebt gedownload. Klik op **Volgende**.

    ![Registratie-kluis referenties](./media/backup-configure-vault/register1.png)

7. Geef bij **versleutelings instelling**een wachtwoordzin op die wordt gebruikt voor het versleutelen en ontsleutelen van back-ups voor de machine.

    * De wachtwoordzin voor de versleuteling op een veilige locatie opslaan.
    * Als u de wachtwoordzin kwijtraakt of vergeet, kan micro soft u niet helpen bij het herstellen van de back-upgegevens. Sla het bestand daarom op een veilige locatie op. U hebt deze nodig om een back-up te herstellen.

8. Klik op **Voltooien**. De agent wordt nu geïnstalleerd en uw computer wordt geregistreerd bij de kluis. U kunt nu uw back-up configureren en plannen.

## <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Het back-upbeleid geeft aan wanneer moment opnamen van de gegevens moeten worden gemaakt voor het maken van herstel punten en hoe lang de herstel punten moeten worden bewaard.

* U configureert een back-upbeleid met behulp van de MARS-agent.
* Met Azure Backup wordt niet automatisch zomer tijd in rekening gebracht. Dit kan een verschil veroorzaken tussen de werkelijke tijd en de geplande back-uptijd.

Maak als volgt een beleid:
1. Nadat u de MARS-agent hebt gedownload en geregistreerd, start u de agent console. U vindt deze door te zoeken naar **Microsoft Azure Backup** op uw machine.  
2. Klik in **acties**op **back-up plannen**.

    ![Een back-up van de Windows Server plannen](./media/backup-configure-vault/schedule-first-backup.png)
3. Klik op **volgende**in de wizard Back-up plannen > **aan**de slag te gaan.
4. In **items selecteren waarvan u een back-up wilt maken**, klikt u op **items toevoegen**.

    ![Items selecteren waarvan u een back-up wilt maken](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. Selecteer bij **items selecteren**de waarde waarvan u een back-up wilt maken en klik op **OK**.

    ![Geselecteerde items waarvan een back-up moet worden gemaakt](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. Klik op de pagina **items selecteren waarvan u een back-up wilt maken** op **volgende**.
7. Geef op de pagina **back-upschema opgeven** op wanneer u dagelijks of wekelijks back-ups wilt maken. Klik op **Volgende**.

    - Wanneer er een back-up wordt gemaakt, wordt er een herstel punt aangemaakt.
    - Het aantal herstel punten dat in uw omgeving is gemaakt, is afhankelijk van het back-upschema.


8. U kunt dagelijks back-ups plannen, Maxi maal drie keer per dag. De scherm opname bevat bijvoorbeeld twee dagelijkse back-ups, een om middernacht en één om 6:00 uur.

    ![Dagelijks schema](./media/backup-configure-vault/day-schedule.png)


9. U kunt ook wekelijkse back-ups uitvoeren. De scherm afbeelding toont bijvoorbeeld de back-ups die zijn gemaakt op elke tweede zondag & woensdag om 9:30 uur en 1:00 uur.

    ![Wekelijks schema](./media/backup-configure-vault/week-schedule.png)


10. Geef op de pagina **retentie beleid selecteren** op hoe u historische kopieën van uw gegevens opslaat. Klik op **Volgende**.

    - De Bewaar instellingen geven op welke herstel punten moeten worden opgeslagen en hoelang ze moeten worden opgeslagen.
    - Wanneer u bijvoorbeeld een dagelijkse Bewaar instelling instelt, geeft u aan dat op het tijdstip dat is opgegeven voor de dagelijkse retentie, het laatste herstel punt wordt bewaard gedurende het opgegeven aantal dagen. U kunt ook een maandelijks Bewaar beleid opgeven om aan te geven dat het herstel punt dat op de 30e van elke maand wordt gemaakt, 12 maanden moet worden bewaard.
    - Dagelijks en wekelijks Bewaar periode van het herstel punt vallen doorgaans samen met het back-upschema. Als de back-up wordt geactiveerd volgens schema, wordt het herstel punt dat is gemaakt door de back-up opgeslagen voor de duur die is aangegeven in het dagelijkse of wekelijkse Bewaar beleid.
    - Als voor beeld in de volgende scherm afbeelding:

        -   Dagelijkse back-ups op middernacht en 6:00 uur worden gedurende zeven dagen bewaard.
        -   Back-ups die zijn gemaakt op zaterdag om middernacht en 6:00 uur, worden vier weken bewaard.
        -   Back-ups die zijn gemaakt op zaterdag in de afgelopen week van de maand om middernacht en 6:00 uur, worden twaalf maanden bewaard.
        -   Back-ups die zijn gemaakt op een zaterdag in de afgelopen week van maart worden tien jaar bewaard.

        ![Voor beeld van Bewaar periode](./media/backup-configure-vault/retention-example.png)


11. In **eerste back-uptype kiezen** beslist u of u de eerste back-up via het netwerk wilt maken of offline back-ups wilt gebruiken (zie dit [artikel](offline-backup-azure-data-box.md)voor meer informatie over offline back-up). Als u de eerste back-up via het netwerk wilt maken, selecteert u **automatisch via het netwerk** en klikt u op **volgende**.

    ![eerste back-uptype](./media/backup-azure-manage-mars/choose-initial-backup-type.png)


12. Lees de informatie in **bevestiging**en klik vervolgens op **volt ooien**.

    ![Back-uptype bevestigen](./media/backup-azure-manage-mars/confirm-backup-type.png)


13. Nadat u de wizard voor het maken van een back-upschema hebt doorlopen, klikt u op **Sluiten**.

    ![Het wijzigen van het back-upproces bevestigen](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

U moet een beleid maken op elke computer waarop de agent is geïnstalleerd.

### <a name="perform-the-initial-backup-offline"></a>De eerste back-up offline uitvoeren

U kunt automatisch een initiële back-up uitvoeren via het netwerk of offline. Offline seeding voor een eerste back-up is handig als u grote hoeveel heden gegevens hebt waardoor veel netwerk bandbreedte moet worden overgedragen. U voert een offline-overdracht als volgt uit:

1. U schrijft de back-upgegevens naar een faserings locatie.
2. U kunt het hulp programma AzureOfflineBackupDiskPrep gebruiken om de gegevens van de faserings locatie naar een of meer SATA-schijven te kopiëren.
3. Het hulp programma maakt een Azure import-taak. Meer [informatie](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) over Azure importeren en exporteren.
4. U verzendt de SATA-schijven naar een Azure-Data Center.
5. In het Data Center worden de schijf gegevens gekopieerd naar een Azure-opslag account.
6. Azure Backup kopieert de gegevens van het opslag account naar de kluis en incrementele back-ups worden gepland.

[Meer informatie](offline-backup-azure-data-box.md) over offline seeding.

### <a name="enable-network-throttling"></a>Netwerk beperking inschakelen

U kunt bepalen hoe netwerk bandbreedte wordt gebruikt door de MARS-agent door netwerk beperking in te scha kelen. Beperking is handig als u een back-up wilt maken van gegevens tijdens werk uren, maar u wilt bepalen hoeveel band breedte wordt gebruikt voor back-up-en herstel activiteiten.

* Azure Backup netwerk beperking maakt gebruik van [Quality of service (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) op het lokale besturings systeem.
* Netwerk beperking voor back-up is beschikbaar op Windows Server 2012 en op Windows 8 en hoger. Besturings systemen moeten de nieuwste service packs uitvoeren.

Schakel als volgt netwerk beperking in:

1. Klik in de MARS-agent op **Eigenschappen wijzigen**.
2. Schakel op het tabblad **beperken** het selectie vakje **bandbreedte beperking van Internet bandbreedte voor back-upbewerkingen inschakelen**in.

    ![Netwerk beperking](./media/backup-configure-vault/throttling-dialog.png)
3. Geef de toegestane band breedte op tijdens het werk en buiten kantoor uren. Bandbreedte waarden beginnen bij 512 kbps en gaan tot 1.023 MBps. Klik vervolgens op **OK**.

## <a name="run-an-on-demand-backup"></a>Een back-up op aanvraag uitvoeren

1. Klik in de MARS-agent op **Nu back-up maken**.

    ![Nu back-up maken van Windows Server](./media/backup-configure-vault/backup-now.png)

2. Als de MARS agent-versie 2.0.9169.0 of hoger is, kan een aangepaste Bewaar periode worden ingesteld. Kies in de sectie **back-uptaak behouden** een datum in de weer gegeven agenda:

   ![Back-upagenda behouden](./media/backup-configure-vault/mars-ondemand.png)

3. Controleer de instellingen in **bevestiging**en klik op **back-up**.
4. Klik op **Sluiten** om de wizard te sluiten. Als u dit doet voordat de back-up is voltooid, blijft de wizard op de achtergrond actief.
5. Nadat de eerste back-up is voltooid, wordt de status **Taak voltooid** weergegeven in de back-upconsole.

## <a name="on-demand-backup-policy-retention-behavior"></a>Bewaar gedrag op aanvraag voor back-upbeleid

>[!NOTE]
>Alleen van toepassing op de MARS-agent versies ouder dan 2.0.9169.0
>

* Raadpleeg voor meer informatie stap 8 van [een back-upbeleid maken](backup-configure-vault.md#create-a-backup-policy)

| Optie voor back-upschema | Hoe lang worden de gegevens waarvan een back-up is gemaakt, bewaard?
| -- | --
| Elke: * dag een back-up plannen | **Standaard retentie**: gelijk aan de Bewaar periode in dagen voor dagelijkse back-ups <br/><br/> **Uitzonde ring**: als er een dagelijkse geplande back-up is ingesteld voor lange termijn retentie (weken, maanden of jaren), wordt een back-up op aanvraag geactiveerd nadat de geplande back-up is mislukt voor lange termijn retentie. Anders wordt de volgende geplande back-up overwogen voor lange termijn retentie.<br/><br/> **Voor beeld**: als u de geplande back-up op donderdag 8:00 am uitschakelt en dezelfde back-up moest worden overwogen voor wekelijks/maandelijks/jaarlijks bewaren, wordt de eerste back-up op aanvraag geactiveerd vóór de volgende geplande back-up (bijvoorbeeld) vrijdag, 8:00 uur wordt automatisch gelabeld voor een wekelijkse/maandelijkse/jaarlijkse Bewaar periode voor de donderdag 8:00 uur.
| Een back-up plannen elke: * wekelijks | **Standaard retentie**: 1 dag <br/> Back-ups op aanvraag die zijn gemaakt voor een gegevens bron met wekelijks back-upbeleid, worden de volgende dag verwijderd, zelfs als ze de meest recente back-ups voor de gegevens bron zijn. <br/><br/> **Uitzonde ring**: als er een wekelijkse geplande back-up is ingesteld voor lange termijn retentie (weken, maanden, jaren), wordt een back-up op aanvraag geactiveerd nadat de geplande back-up is mislukt voor lange termijn retentie. Anders wordt de volgende geplande back-up overwogen voor lange termijn retentie. <br/><br/> **Voor beeld**: als u de geplande back-up op donderdag 8:00 am uitschakelt en dezelfde back-up moest worden overwogen voor maandelijks/jaarlijks retentie, wordt de eerste back-up op aanvraag geactiveerd vóór de volgende geplande back-up (bijvoorbeeld) donderdag, 8:00 uur wordt automatisch gelabeld als van toepassing op de donderdag 8:00 uur back-up

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het herstellen van bestanden](backup-azure-restore-windows-server.md).
