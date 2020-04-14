---
title: Een back-up maken en een Oracle Database 12c-database herstellen op een virtuele Azure Linux-machine | Microsoft Documenten
description: Meer informatie over het maken van een back-up en het herstellen van een Oracle Database 12c-database in uw Azure-omgeving.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: mimckitt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: mimckitt
ms.openlocfilehash: 9e2fcc9533c183a356b19c369777e1319ca7f9ae
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263196"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Een back-up maken en een Oracle Database 12c-database herstellen op een virtuele Azure Linux-machine

U Azure CLI gebruiken om Azure-resources te maken en te beheren met een opdrachtprompt of scripts te gebruiken. In dit artikel gebruiken we Azure CLI-scripts om een Oracle Database 12c-database te implementeren vanuit een Azure Marketplace-galerieafbeelding.

Controleer voordat u begint of Azure CLI is geïnstalleerd. Zie de azure [CLI-installatiehandleiding](https://docs.microsoft.com/cli/azure/install-azure-cli)voor meer informatie.

## <a name="prepare-the-environment"></a>De omgeving voorbereiden

### <a name="step-1-prerequisites"></a>Stap 1: Voorwaarden

*   Als u het back-up- en herstelproces wilt uitvoeren, moet u eerst een Linux-vm maken met een geïnstalleerd exemplaar van Oracle Database 12c. De Marketplace-afbeelding die u gebruikt om de VM te maken, heet *Oracle:Oracle-Database-Ee:12.1.0.2:latest*.

    Zie de [oracle-database snelaan de slag voor](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create)het maken van een Oracle-database voor meer informatie over het maken van een Oracle-database.


### <a name="step-2-connect-to-the-vm"></a>Stap 2: Verbinding maken met de VM

*   Als u een SSH-sessie (Secure Shell) wilt maken met de VM, gebruikt u de volgende opdracht. Vervang het IP-adres en de `publicIpAddress` combinatie van hostnaam door de waarde voor uw vm.

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Stap 3: De database voorbereiden

1.  Deze stap gaat ervan uit dat u een Oracle-instantie (cdb1) hebt die wordt uitgevoerd op een VM met de naam *myVM*.

    Voer de *hoofdwortel* van oracle-superuser uit en initialiseer vervolgens de listener:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  (Optioneel) Controleer of de database zich in de archieflogboekmodus bevindt:

    ```bash
    $ sqlplus / as sysdba
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG

    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

3.  (Optioneel) Maak een tabel om de commit te testen:

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```

4.  Controleer of wijzig de locatie en grootte van het back-upbestand:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```

5. Gebruik Oracle Recovery Manager (RMAN) om een back-up van de database te maken:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Stap 4: Applicatieconsistente back-up voor Linux VM's

Toepassingsconsistente back-ups is een nieuwe functie in Azure Backup. U scripts maken en selecteren die voor en na de VM-momentopname moeten worden uitgevoerd (pre-snapshot en post-snapshot).

1. Download het JSON-bestand.

    Download VMSnapshotScriptPluginConfig.json https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfigvan . De inhoud van het bestand lijkt op het volgende:

    ```output
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "",
        "postScriptLocation" : "",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

2. Maak de map /etc/azure op de VM:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Kopieer het JSON-bestand.

    Kopieer VMSnapshotScriptPluginConfig.json naar de map /etc/azure.

4. Bewerk het JSON-bestand.

    Bewerk het VMSnapshotScriptPluginConfig.json-bestand `PreScriptLocation` `PostScriptlocation` om de parameters en parameters op te nemen. Bijvoorbeeld:

    ```output
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "/etc/azure/pre_script.sh",
        "postScriptLocation" : "/etc/azure/post_script.sh",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

5. Maak de pre-snapshot- en post-snapshot scriptbestanden.

    Hier volgt een voorbeeld van pre-snapshot en post-snapshot scripts voor een "koude back-up" (een offline back-up, met afsluiten en opnieuw opstarten):

    Voor /etc/azure/pre_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    Voor /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Hier is een voorbeeld van pre-snapshot en post-snapshot scripts voor een "hot backup" (een online back-up):

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Voor /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Wijzig voor /etc/azure/pre_script.sql de inhoud van het bestand volgens uw vereisten:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    Wijzig voor /etc/azure/post_script.sql de inhoud van het bestand volgens uw vereisten:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Bestandsmachtigingen wijzigen:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Test de scripts.

    Als u de scripts wilt testen, moet u zich eerst aanmelden als root. Zorg er vervolgens voor dat er geen fouten zijn:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Zie [Toepassingsconsistente back-up voor Linux VM's voor](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/)meer informatie.


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Stap 5: Vaults van Azure Recovery Services gebruiken om een back-up van de VM te maken

1.  Zoek in de Azure-portal naar **Vaults van Recovery Services.**

    ![Pagina Kluizen van Recovery Services](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Klik op het **kluizenblad van De Herstelservices** om een nieuwe kluis toe te voegen op **Toevoegen**.

    ![Pagina kluizen voor Herstelservices toevoegen](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Als u wilt doorgaan, klikt u op **myVault**.

    ![Detailpagina Vaults vaults van Recovery Services](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Klik op het **myVault-blad** op **Back-up**.

    ![Back-uppagina van Recovery Services-kluizen](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Gebruik op het blade **Back-updoel** de standaardwaarden van **Azure** en **Virtual-machine**. Klik op **OK**.

    ![Detailpagina Vaults vaults van Recovery Services](./media/oracle-backup-recovery/recovery_service_05.png)

6.  Gebruik **Standaardbeleid**voor **back-upbeleid**of selecteer **Nieuw beleid maken**. Klik op **OK**.

    ![Detailpagina back-upbeleid van Recovery Services](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Schakel in het blad **Virtuele machines selecteren** het selectievakje **myVM1** in en klik op **OK**. Klik op de knop **Back-up inschakelen.**

    ![Vaults items van Recovery Services naar de pagina back-updetails](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Nadat u op **Back-up inschakelen**hebt geklikt, wordt het back-upproces pas gestart nadat de geplande tijd is verstreken. Als u een onmiddellijke back-up wilt instellen, voert u de volgende stap uit.

8.  Selecteer op het blade **myVault - Backup items** onder **AANTAL BACK-upitems**het aantal back-upitems.

    ![De pagina MyVault-gegevens van Recovery Services wordt gevaultd](./media/oracle-backup-recovery/recovery_service_08.png)

9.  Klik op het hoofd **van back-upitems (Azure Virtual Machine)** aan de rechterkant van de pagina op de knop alipsis (**... )** en klik vervolgens nu op **Back-up**.

    ![Vaults Backup nu vaults Backup now, opdracht](./media/oracle-backup-recovery/recovery_service_09.png)

10. Klik op de knop **Back-up.** Wacht tot het back-upproces is voltooid. Ga vervolgens naar [Stap 6: Verwijder de databasebestanden.](#step-6-remove-the-database-files)

    Als u de status van de back-uptaak wilt weergeven, klikt u op **Taken**.

    ![Vacaturepagina Vaults van Recovery Services](./media/oracle-backup-recovery/recovery_service_10.png)

    De status van de back-uptaak wordt weergegeven in de volgende afbeelding:

    ![De taakpagina van Recovery Services vaults met status](./media/oracle-backup-recovery/recovery_service_11.png)

11. Voor een toepassingsconsistente back-up u eventuele fouten in het logboekbestand oplossen. Het logboekbestand bevindt zich op /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Stap 6: De databasebestanden verwijderen 
Later in dit artikel leert u hoe u het herstelproces testen. Voordat u het herstelproces testen, moet u de databasebestanden verwijderen.

1.  Verwijder de tabelruimte en back-upbestanden:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (Optioneel) Sluit de Oracle-instantie af:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>De verwijderde bestanden herstellen uit de vaults van Recovery Services
Voer de volgende stappen uit om de verwijderde bestanden te herstellen:

1. Zoek in de Azure-portal naar het object *myVault* Recovery Services vaults. Selecteer op het **blad Overzicht** onder **Back-upitems**het aantal items.

    ![Recovery Services kluizen myVault back-up items](./media/oracle-backup-recovery/recovery_service_12.png)

2. Selecteer **onder AANTAL BACK-upobjecten**het aantal items.

    ![Vaults Azure Virtual Machine-back-upitem aantal Vaults Azure Virtual Machine](./media/oracle-backup-recovery/recovery_service_13.png)

3. Klik op het **myvm1-blad** op **Bestandsherstel (voorbeeld)**.

    ![Schermafbeelding van de pagina Herstelservices van Herstelservices](./media/oracle-backup-recovery/recovery_service_14.png)

4. Klik in het deelvenster **Bestandsherstel (voorbeeld)** op **Script downloaden**. Sla vervolgens het downloadbestand (.sh) op in een map op de clientcomputer.

    ![Opties voor het opslaan van scriptbestanden downloaden](./media/oracle-backup-recovery/recovery_service_15.png)

5. Kopieer het .sh-bestand naar de VM.

    In het volgende voorbeeld ziet u hoe u een opdracht voor beveiligde kopieën (scp) gebruikt om het bestand naar de vm te verplaatsen. U de inhoud ook naar het klembord kopiëren en vervolgens de inhoud plakken in een nieuw bestand dat is ingesteld op de vm.

    > [!IMPORTANT]
    > Zorg er in het volgende voorbeeld voor dat u de IP-adres- en mapwaarden bijwerkt. De waarden moeten worden toegewezen aan de map waar het bestand is opgeslagen.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```

6. Wijzig het bestand, zodat het eigendom is van de root.

    Wijzig in het volgende voorbeeld het bestand zodat het eigendom is van de root. Wijzig vervolgens machtigingen.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```

    In het volgende voorbeeld ziet u wat u moet zien nadat u het vorige script hebt uitgevoerd. Wanneer u wordt gevraagd door te gaan, voert u **Y**in.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. De toegang tot de gemonteerde volumes wordt bevestigd.

    Als u wilt afsluiten, voert u **q**in en zoekt u vervolgens naar de gemonteerde volumes. Als u een lijst met de toegevoegde volumes wilt maken, voert u bij een opdrachtprompt **df -k in.**

    ![De opdracht df -k](./media/oracle-backup-recovery/recovery_service_16.png)

8. Gebruik het volgende script om de ontbrekende bestanden terug te kopiëren naar de mappen:

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```

9. Gebruik RMAN in het volgende script om de database te herstellen:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

10. Ontkoppel de schijf.

    Klik in de Azure-portal in het blade **Bestandsherstel (Preview)** op **Schijven ontmonteren**.

    ![Schijven ongedaan maken, opdracht](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>De hele VM herstellen

In plaats van de verwijderde bestanden uit de vaults van Recovery Services te herstellen, u de hele vm herstellen.

### <a name="step-1-delete-myvm"></a>Stap 1: MyVM verwijderen

*   Ga in de Azure-portal naar de **myVM1-kluis** en selecteer **Verwijderen**.

    ![Vault delete, opdracht](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Stap 2: De VM herstellen

1.  Ga naar **Vaults van Recovery Services**en selecteer **myVault**.

    ![myVault-vermelding](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Selecteer op het **blad Overzicht** onder **Back-upitems**het aantal items.

    ![myVault back-upitems](./media/oracle-backup-recovery/recover_vm_03.png)

3.  Selecteer **myvm1**in het blade **Back-upitems (Azure Virtual Machine).**

    ![Pagina HerstelVM](./media/oracle-backup-recovery/recover_vm_04.png)

4.  Klik op het **myvm1-blad** op de knop ellips (**...**) en klik vervolgens op **VM herstellen**.

    ![VM herstellen, opdracht](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Selecteer in het **puntblad Herstellen** herstellen het item dat u wilt herstellen en klik op **OK**.

    ![Het herstelpunt selecteren](./media/oracle-backup-recovery/recover_vm_06.png)

    Als u een back-up voor toepassingconsistent hebt ingeschakeld, wordt een verticale blauwe balk weergegeven.

6.  Selecteer in het **configuratieblad Het herstellen** de naam van de virtuele machine, selecteer de brongroep en klik op **OK**.

    ![Configuratiewaarden herstellen](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Als u de vm wilt herstellen, klikt u op de knop **Herstellen.**

8.  Als u de status van het herstelproces wilt weergeven, klikt u op **Taken**en klikt u vervolgens op **Back-uptaken**.

    ![Status van back-ups maken, opdracht](./media/oracle-backup-recovery/recover_vm_08.png)

    De volgende afbeelding toont de status van het herstelproces:

    ![Status van het herstelproces](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Stap 3: Het openbare IP-adres instellen
Nadat de vm is hersteld, stelt u het openbare IP-adres in.

1.  Voer in het zoekvak **openbaar IP-adres in**.

    ![Lijst met openbare IP-adressen](./media/oracle-backup-recovery/create_ip_00.png)

2.  Klik op het blad **Openbare IP-adressen** op **Toevoegen**. Selecteer op **het ip-adresblad Openbaar IP-adres** voor **Naam**de openbare IP-naam. Voor **Resourcegroep** selecteert u **Bestaande gebruiken**. Klik vervolgens op **Maken**.

    ![IP-adres maken](./media/oracle-backup-recovery/create_ip_01.png)

3.  Als u het openbare IP-adres wilt koppelen aan de netwerkinterface voor de VM, zoekt en selecteert u **myVMip**. Klik vervolgens op **Koppelen**.

    ![IP-adres koppelen](./media/oracle-backup-recovery/create_ip_02.png)

4.  Selecteer **Netwerkinterface**voor **resourcetype**. Selecteer de netwerkinterface die wordt gebruikt door de instantie myVM en klik op **OK**.

    ![Resourcetype en NIC-waarden selecteren](./media/oracle-backup-recovery/create_ip_03.png)

5.  Zoek naar en open het exemplaar van myVM dat is geport vanaf de portal. Het IP-adres dat aan de VM **Overview** is gekoppeld, wordt weergegeven op het myVM-overzichtsblad.

    ![WAARDE IP-adres](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Stap 4: Verbinding maken met de VM

*   Als u verbinding wilt maken met de virtuele machine, gebruikt u het volgende script:

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Stap 5: Testen of de database toegankelijk is
*   Als u de toegankelijkheid wilt testen, gebruikt u het volgende script:

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Als de **opstartopdracht** van de database een fout genereert, raadpleegt u [Stap 6: RMAN gebruiken om de database te herstellen.](#step-6-optional-use-rman-to-recover-the-database)

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Stap 6: (Optioneel) RMAN gebruiken om de database te herstellen
*   Als u de database wilt herstellen, gebruikt u het volgende script:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

De back-up en het herstel van de Oracle Database 12c-database op een Azure Linux VM is nu voltooid.

## <a name="delete-the-vm"></a>De VM verwijderen

Wanneer u de vm niet meer nodig hebt, u de volgende opdracht gebruiken om de resourcegroep, de vm en alle gerelateerde resources te verwijderen:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

[Zelfstudie: zeer beschikbare VM's maken](../../linux/create-cli-complete.md)

[Azure CLI-voorbeelden voor VM-implementatie verkennen](../../linux/cli-samples.md)



