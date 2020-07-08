---
title: SAP HANA data bases herstellen op virtuele Azure-machines
description: In dit artikel vindt u informatie over het herstellen van SAP HANA-data bases die worden uitgevoerd op Azure Virtual Machines.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a3db88ca3c995c3c190da051dbf9df6ae5e29530
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851440"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>SAP HANA data bases herstellen op virtuele Azure-machines

In dit artikel wordt beschreven hoe u SAP HANA-data bases kunt herstellen die worden uitgevoerd op een virtuele Azure-machine (VM), waarvan de Azure Backup-service een back-up heeft gemaakt op een Recovery Services kluis. Herstel bewerkingen kunnen worden gebruikt om kopieën te maken van de gegevens voor dev/test-scenario's of om terug te gaan naar een eerdere status.

Zie back-up maken van [SAP Hana data bases op Azure-vm's](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)voor meer informatie over het maken van back-ups van SAP Hana-data bases.

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Herstellen naar een bepaald tijdstip of naar een herstel punt

Azure Backup kunt SAP HANA-data bases die worden uitgevoerd op virtuele Azure-machines als volgt herstellen:

* Herstel naar een specifieke datum of tijd (naar de tweede) met behulp van logboek back-ups. Azure Backup bepaalt automatisch de juiste volledige, differentiële back-ups en de keten van logboek back-ups die nodig zijn om te herstellen op basis van de geselecteerde tijd.

* Herstel naar een specifieke volledige of een differentiële back-up om naar een specifiek herstel punt te herstellen.

## <a name="prerequisites"></a>Vereisten

Let op het volgende voordat u een Data Base herstelt:

* U kunt de data base alleen herstellen naar een SAP HANA-exemplaar dat zich in dezelfde regio bevindt

* Het doel exemplaar moet zijn geregistreerd bij dezelfde kluis als de bron

* Azure Backup kan niet twee verschillende SAP HANA exemplaren op dezelfde VM identificeren. Het terugzetten van gegevens van het ene naar het andere exemplaar op dezelfde VM is niet mogelijk.

* Om ervoor te zorgen dat het doel SAP HANA exemplaar gereed is voor herstel, controleert u de status van de **back-up** :

  1. Open de kluis waarin het doel SAP HANA exemplaar is geregistreerd

  1. Kies **back-up** in het kluis dashboard, onder **aan**de slag

      ![Back-up in het kluis dashboard](media/sap-hana-db-restore/getting-started-backup.png)

  1. Klik in **back-up**, onder **waarvan wilt u een back-up maken?** Kies **SAP Hana in azure VM**

      ![SAP HANA kiezen in een Azure-VM](media/sap-hana-db-restore/sap-hana-backup.png)

  1. Klik onder **Db's detecteren in vm's**op **Details weer geven**

      ![Details weergeven](media/sap-hana-db-restore/view-details.png)

  1. De voor **bereiding van de back-up** van de doel-VM controleren

      ![Beveiligde servers](media/sap-hana-db-restore/protected-servers.png)

* Raadpleeg de SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) voor meer informatie over de typen herstel die SAP Hana ondersteunt.

## <a name="restore-a-database"></a>Een database herstellen

Als u wilt herstellen, hebt u de volgende machtigingen nodig:

* Machtigingen voor **Back-upoperators** in de kluis waar u de herstel bewerking uitvoert.
* **Inzender (write)** toegang tot de bron-VM waarvan een back-up is gemaakt.
* **Inzender (write**) toegang tot de doel-VM:
  * Als u naar dezelfde VM wilt herstellen, is dit de bron-VM.
  * Als u naar een alternatieve locatie wilt herstellen, is dit de nieuwe doel-VM.

1. Open de kluis waarin de SAP HANA data base moet worden hersteld

1. Kies op het kluis dashboard onder **beveiligde items** **Back-upitems**

    ![Back-upitems](media/sap-hana-db-restore/backup-items.png)

1. In **Back-upitems**, onder **type back-upbeheer** selecteert u **SAP Hana in azure VM**

    ![Type back-upbeheer](media/sap-hana-db-restore/backup-management-type.png)

1. Selecteer de data base die u wilt herstellen

    ![Te herstellen data base](media/sap-hana-db-restore/database-to-restore.png)

1. Bekijk het database menu. Het bevat informatie over back-up van de data base, waaronder:

    * De oudste en meest recente herstel punten

    * De back-upstatus van het logboek voor de afgelopen 24 en 72 uur voor de data base

    ![Menu Data Base](media/sap-hana-db-restore/database-menu.png)

1. **Data Base herstellen** selecteren

1. Onder **configuratie herstellen**, geeft u op waar (of hoe) u gegevens wilt herstellen:

    * **Alternatieve locatie**: herstel de Data Base op een andere locatie en behoud de oorspronkelijke bron database.

    * **Db overschrijven**: herstel de gegevens naar hetzelfde SAP Hana exemplaar als de oorspronkelijke bron. Met deze optie wordt de oorspronkelijke data base overschreven.

      ![Configuratie herstellen](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Herstellen naar alternatieve locatie

1. Selecteer in het menu **herstellen configuratie** , onder **waar u wilt herstellen**, **alternatieve locatie**.

    ![Herstellen naar alternatieve locatie](media/sap-hana-db-restore/restore-alternate-location.png)

1. Selecteer de SAP HANA hostnaam en de naam van het exemplaar waarnaar u de Data Base wilt herstellen.
1. Controleer of het doel SAP HANA exemplaar gereed is voor herstel door de **back-up gereedheid** te garanderen. Raadpleeg de [sectie vereisten](#prerequisites) voor meer informatie.
1. Geef in het vak **Herstelde databasenaam** de naam van de doeldatabase op.

    > [!NOTE]
    > Individuele database-container (dit SDC) moet deze [controles](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)volgen.

1. Indien van toepassing, selecteert u **overschrijven als de data base met dezelfde naam al bestaat in het geselecteerde Hana-exemplaar**.
1. Selecteer **OK**.

    ![Configuratie herstellen-laatste scherm](media/sap-hana-db-restore/restore-configuration-last.png)

1. Selecteer in **herstel punt selecteren** **Logboeken (tijdstip)** om [naar een bepaald punt in de tijd te herstellen](#restore-to-a-specific-point-in-time). Of selecteer **volledige & differentieel** om [terug te zetten naar een specifiek herstel punt](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Herstellen en overschrijven

1. Selecteer in het menu **configuratie herstellen** onder **herstel van herstellen**de optie **Data Base overschrijven**  >  **OK**.

    ![DB overschrijven](media/sap-hana-db-restore/overwrite-db.png)

1. Selecteer in **herstel punt selecteren** **Logboeken (tijdstip)** om [naar een bepaald punt in de tijd te herstellen](#restore-to-a-specific-point-in-time). Of selecteer **volledige & differentieel** om [terug te zetten naar een specifiek herstel punt](#restore-to-a-specific-recovery-point).

### <a name="restore-as-files"></a>Herstellen als bestanden

Als u de back-upgegevens wilt herstellen als bestanden in plaats van een Data Base, kiest u **herstellen als bestanden**. Zodra de bestanden zijn gedumpt naar een opgegeven pad, kunt u deze bestanden naar elke SAP HANA machine nemen waar u ze wilt herstellen als een Data Base. Omdat u deze bestanden naar een wille keurige machine kunt verplaatsen, kunt u de gegevens nu in abonnementen en regio's herstellen.

1. Selecteer in het menu **configuratie herstellen** onder **waar en hoe u kunt herstellen**de optie **herstellen als bestanden**.
1. Selecteer de **host** /Hana-server naam waarnaar u de back-upbestanden wilt herstellen.
1. Voer in het doelpad **op de server**het mappad in op de server die u hebt geselecteerd in stap 2. Dit is de locatie waar de service alle benodigde back-upbestanden zal dumpen.

    De bestanden die worden gedumpt zijn:

    * Back-upbestanden van data base
    * Catalogus bestanden
    * JSON-meta gegevens bestanden (voor elk back-upbestand dat betrokken is)

    Normaal gesp roken is een pad naar de netwerk share of het pad van een gekoppelde Azure-bestands share indien opgegeven als doelpad, waardoor de toegang tot deze bestanden wordt vereenvoudigd door andere computers in hetzelfde netwerk of met dezelfde Azure-bestands share die eraan is gekoppeld.

    >[!NOTE]
    >Als u de back-upbestanden van de Data Base wilt herstellen op een Azure-bestands share die is gekoppeld aan de geregistreerde doel-VM, moet u ervoor zorgen dat het hoofd account lees-en schrijf machtigingen heeft voor de Azure-bestands share.

    ![Doelpad kiezen](media/sap-hana-db-restore/restore-as-files.png)

1. Selecteer het **herstel punt** dat overeenkomt met de back-upbestanden en-mappen zullen worden hersteld.

    ![Herstel punt selecteren](media/sap-hana-db-restore/select-restore-point.png)

1. Alle back-upbestanden die zijn gekoppeld aan het geselecteerde herstel punt, worden in het doelpad gedumpt.
1. Op basis van het type herstel punt dat is gekozen (**tijds duur** of **volledig & differentieel**), ziet u een of meer mappen die zijn gemaakt in het doelpad. Een van de mappen `Data_<date and time of restore>` met de naam bevat de volledige en differentiële back-ups en de andere map `Log` met de naam bevat de logboek back-ups.
1. Verplaats deze teruggezette bestanden naar de SAP HANA-server waar u ze wilt herstellen als een Data Base.
1. Volg dan deze stappen:
    1. Stel machtigingen in voor de map of map waarin de back-upbestanden worden opgeslagen met behulp van de volgende opdracht:

        ```bash
        chown -R <SID>adm:sapsys <directory>
        ```

    1. De volgende set opdrachten uitvoeren als`<SID>adm`

        ```bash
        su - <sid>adm
        ```

    1. Genereer het catalogus bestand voor herstel. Pak de **BackupId** uit vanuit het JSON-meta gegevensbestand voor de volledige back-up, die later in de herstel bewerking wordt gebruikt. Zorg ervoor dat de volledige en logboek back-ups zich in verschillende mappen bevinden en verwijder de catalogus bestanden en JSON-bestanden met meta gegevens in deze mappen.

        ```bash
        hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
        ```

        In de bovenstaande opdracht:

        * `<DataFileDir>`-de map met de volledige back-ups
        * `<LogFilesDir>`-de map die de back-ups van het logboek bevat
        * `<PathToPlaceCatalogFile>`-de map waarin het gegenereerde catalogus bestand moet worden geplaatst

    1. Herstel met behulp van het zojuist gegenereerde catalogus bestand via HANA Studio of voer de HDBSQL-herstel query uit met deze zojuist gegenereerde catalogus. HDBSQL-query's worden hieronder weer gegeven:

    * Herstellen naar een bepaald tijdstip:

        Als u een nieuwe herstelde data base maakt, voert u de opdracht HDBSQL uit om een nieuwe Data Base te maken `<DatabaseName>` en stopt u de Data Base voor herstel. Als u echter alleen een bestaande data base herstelt, voert u de HDBSQL-opdracht uit om de data base te stoppen.

        Voer vervolgens de volgende opdracht uit om de data base te herstellen:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>`-Naam van de nieuwe data base of bestaande data base die u wilt herstellen
        * `<Timestamp>`-Exacte tijds tempel van het herstel punt in de tijd
        * `<DatabaseName@HostName>`-De naam van de data base waarvan de back-up wordt gebruikt voor herstel en de naam van de **host** /SAP Hana server waarop deze data base zich bevindt. `USING SOURCE <DatabaseName@HostName>`Met de optie geeft u op dat de gegevens back-up (gebruikt voor herstel) van een Data Base met een andere sid of naam dan de doel SAP Hana machine is. Dit hoeft dus niet te worden opgegeven voor herstel bewerkingen die zijn uitgevoerd op dezelfde HANA-server vanaf waar de back-up wordt gemaakt.
        * `<PathToGeneratedCatalogInStep3>`-Pad naar het catalogus bestand dat in **stap C** is gegenereerd
        * `<DataFileDir>`-de map met de volledige back-ups
        * `<LogFilesDir>`-de map die de back-ups van het logboek bevat
        * `<BackupIdFromJsonFile>`-de **BackupId** geëxtraheerd in **stap C**

    * Herstellen naar een bepaalde volledige of differentiële back-up:

        Als u een nieuwe herstelde data base maakt, voert u de opdracht HDBSQL uit om een nieuwe Data Base te maken `<DatabaseName>` en stopt u de Data Base voor herstel. Als u echter alleen een bestaande data base herstelt, voert u de HDBSQL-opdracht uit om de data base te stoppen:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>`-de naam van de nieuwe data base of bestaande data base die u wilt herstellen
        * `<Timestamp>`-de exacte tijds tempel van het herstel punt in de tijd
        * `<DatabaseName@HostName>`-de naam van de data base waarvan de back-up wordt gebruikt voor herstel en de naam van de **host** /SAP Hana server waarop deze data base zich bevindt. `USING SOURCE <DatabaseName@HostName>`Met de optie geeft u op dat de gegevens back-up (gebruikt voor herstel) van een Data Base met een andere sid of naam dan de doel SAP Hana machine is. Dit hoeft dus niet te worden opgegeven voor herstel bewerkingen die zijn uitgevoerd op dezelfde HANA-server vanaf waar de back-up wordt gemaakt.
        * `<PathToGeneratedCatalogInStep3>`-het pad naar het catalogus bestand dat in **stap C** is gegenereerd
        * `<DataFileDir>`-de map met de volledige back-ups
        * `<LogFilesDir>`-de map die de back-ups van het logboek bevat
        * `<BackupIdFromJsonFile>`-de **BackupId** geëxtraheerd in **stap C**

### <a name="restore-to-a-specific-point-in-time"></a>Herstellen naar een bepaald punt in de tijd

Ga als volgt te werk als u **Logboeken** hebt geselecteerd (tijdstip) als herstel type:

1. Selecteer een herstel punt in de logboek grafiek en selecteer **OK** om het herstel punt te kiezen.

    ![Herstelpunt](media/sap-hana-db-restore/restore-point.png)

1. Selecteer **Herstellen** in het menu **Herstellen** om de hersteltaak te starten.

    ![Selecteer herstellen](media/sap-hana-db-restore/restore-restore.png)

1. Volg de voortgang van de herstel bewerking in het gebied **meldingen** of volg deze door **taken herstellen** te selecteren in het menu Data Base.

    ![De herstel bewerking is geactiveerd](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Herstellen naar een specifiek herstel punt

Ga als volgt te werk als u **volledige & differentieel** hebt geselecteerd als het herstel type:

1. Selecteer een herstel punt in de lijst en selecteer **OK** om het herstel punt te kiezen.

    ![Specifiek herstel punt herstellen](media/sap-hana-db-restore/specific-recovery-point.png)

1. Selecteer **Herstellen** in het menu **Herstellen** om de hersteltaak te starten.

    ![Herstel taak starten](media/sap-hana-db-restore/restore-specific.png)

1. Volg de voortgang van de herstel bewerking in het gebied **meldingen** of volg deze door **taken herstellen** te selecteren in het menu Data Base.

    ![Voortgang van herstellen](media/sap-hana-db-restore/restore-progress.png)

    > [!NOTE]
    > In meerdere database container (MDC) herstel bewerkingen nadat de systeem database is hersteld naar een doel exemplaar, moet het script vooraf worden uitgevoerd. Alleen dan zullen de volgende Tenant-DB-herstel bewerkingen slagen. Raadpleeg voor meer informatie over het [oplossen van problemen – MDC herstellen](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het](sap-hana-db-manage.md) beheren van SAP Hana databases waarvan een back-up is gemaakt met Azure backup
