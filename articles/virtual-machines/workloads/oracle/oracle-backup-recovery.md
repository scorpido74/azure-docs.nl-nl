---
title: Back-ups maken en herstellen van een Oracle Database 12c-Data Base op een virtuele Azure Linux-machine | Microsoft Docs
description: Meer informatie over het maken van een back-up en het herstellen van een Oracle Database 12c-data base in uw Azure-omgeving.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rgardler
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogardle
ms.openlocfilehash: 7f6b9ddfd5b25ea8482e0bbf5622fa16bc450662
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525598"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Back-ups maken en herstellen van een Oracle Database 12c-Data Base op een virtuele Azure Linux-machine

U kunt Azure CLI gebruiken om Azure-resources te maken en te beheren via een opdracht prompt of scripts te gebruiken. In dit artikel gebruiken we Azure CLI-scripts om een Oracle Database 12c-data base te implementeren vanuit een galerie met installatie kopieën van Azure Marketplace.

Voordat u begint, moet u ervoor zorgen dat Azure CLI is geïnstalleerd. Raadpleeg de [installatie handleiding voor Azure cli](/cli/azure/install-azure-cli)voor meer informatie.

## <a name="prepare-the-environment"></a>De omgeving voorbereiden

### <a name="step-1-prerequisites"></a>Stap 1: vereisten

*   Als u het back-up-en herstel proces wilt uitvoeren, moet u eerst een virtuele Linux-machine maken met een geïnstalleerd exemplaar van Oracle Database 12c. De Marketplace-installatie kopie die u gebruikt om de virtuele machine te maken heet *Oracle: Oracle-data base-ee: 12.1.0.2: Latest*.

    Zie de [Snelstartgids Oracle Create Data Base](./oracle-database-quick-create.md)(Engelstalig) voor meer informatie over het maken van een Oracle-data base.


### <a name="step-2-connect-to-the-vm"></a>Stap 2: verbinding maken met de virtuele machine

*   Gebruik de volgende opdracht om een SSH-sessie (Secure Shell) met de virtuele machine te maken. Vervang het IP-adres en de hostnaam combi natie met de `publicIpAddress` waarde voor uw VM.

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Stap 3: de data base voorbereiden

1.  Bij deze stap wordt ervan uitgegaan dat u een Oracle-exemplaar (cdb1) hebt dat wordt uitgevoerd op een virtuele machine met de naam *myVM*.

    Voer de root van de *Oracle* -super gebruiker uit en Initialiseer vervolgens de listener:

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

2.  Beschrijving Zorg ervoor dat de data base zich in de archief modus Archive bevindt:

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

3.  Beschrijving Een tabel maken om de door voer te testen:

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

4.  De locatie en grootte van het back-upbestand controleren of wijzigen:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```

5. Oracle Recovery Manager (RMAN) gebruiken voor het maken van een back-up van de Data Base:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Stap 4: toepassings consistente back-up voor virtuele Linux-machines

Toepassings consistente back-ups is een nieuwe functie in Azure Backup. U kunt scripts maken en selecteren die moeten worden uitgevoerd vóór en na de moment opname van de virtuele machine (vóór de moment opname en na de moment opname).

1. Down load het JSON-bestand.

    Down load VMSnapshotScriptPluginConfig.jsop van https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig . De bestands inhoud ziet er ongeveer als volgt uit:

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

2. Maak de map/etc/Azure op de VM:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Kopieer het JSON-bestand.

    Kopieer VMSnapshotScriptPluginConfig.jsnaar de map/etc/Azure.

4. Bewerk het JSON-bestand.

    Bewerk de VMSnapshotScriptPluginConfig.jsin het bestand met de `PreScriptLocation` `PostScriptlocation` para meters en. Bijvoorbeeld:

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

5. Maak de script bestanden vóór de moment opname en de post momentopnamen.

    Hier volgt een voor beeld van scripts van vóór moment opnamen en post momentopnamen voor een ' koude back-up ' (een offline back-up, met afsluiten en opnieuw opstarten):

    Voor/etc/Azure/pre_script. v:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    Voor/etc/Azure/post_script. v:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Hier volgt een voor beeld van scripts van vóór moment opnamen en post momentopnamen voor een ' hot backup ' (een online back-up):

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Voor/etc/Azure/post_script. v:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Voor/etc/Azure/pre_script. SQL wijzigt u de inhoud van het bestand volgens uw vereisten:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    Voor/etc/Azure/post_script. SQL wijzigt u de inhoud van het bestand volgens uw vereisten:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Bestands machtigingen wijzigen:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. De scripts testen.

    Als u de scripts eerst wilt testen, meldt u zich aan als root. Controleer vervolgens of er geen fouten zijn:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Zie [toepassings consistente back-ups voor virtuele Linux-machines](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/)voor meer informatie.


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Stap 5: Azure Recovery Services-kluizen gebruiken om een back-up te maken van de VM

1.  Zoek in het Azure Portal naar **Recovery Services-kluizen**.

    ![Pagina Recovery Services kluizen](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Klik op de Blade **Recovery Services kluizen** op **toevoegen**om een nieuwe kluis toe te voegen.

    ![Pagina Recovery Services kluizen toevoegen](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Klik op **myVault**om door te gaan.

    ![Detail pagina Recovery Services kluizen](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Klik op de Blade **myVault** op **back-up**.

    ![Back-uppagina Recovery Services kluizen](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Gebruik op de Blade **back-updoel** de standaard waarden van **Azure** en de **virtuele machine**. Klik op **OK**.

    ![Detail pagina Recovery Services kluizen](./media/oracle-backup-recovery/recovery_service_05.png)

6.  Gebruik **defaultpolicy bij**voor het **back-upbeleid**of selecteer **Nieuw beleid maken**. Klik op **OK**.

    ![Detail pagina Recovery Services kluizen back-upbeleid](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Schakel op de Blade **virtuele machines selecteren** het selectie vakje **myVM1** in en klik vervolgens op **OK**. Klik op de knop **back-up inschakelen** .

    ![Recovery Services kluizen-items naar de detail pagina van de back-up](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Nadat u op **back-up inschakelen**hebt geklikt, wordt het back-upproces pas gestart nadat de geplande tijd is verstreken. Voer de volgende stap uit om een onmiddellijke back-up in te stellen.

8.  Selecteer op de Blade **myVault-back-** **upitems, onder aantal back-** items, het aantal back-upitems.

    ![Detail pagina Recovery Services kluizen myVault](./media/oracle-backup-recovery/recovery_service_08.png)

9.  Klik op de Blade **Back-upitems (virtuele machine van Azure)** aan de rechter kant van de pagina op de knop met het weglatings teken (**...**) en klik vervolgens op **Nu back-up maken**.

    ![De opdracht nu back-ups Recovery Services-kluizen](./media/oracle-backup-recovery/recovery_service_09.png)

10. Klik op de knop **back-up** . Wacht totdat het back-upproces is voltooid. Ga vervolgens naar [stap 6: de database bestanden verwijderen](#step-6-remove-the-database-files).

    Als u de status van de back-uptaak wilt weer geven, klikt u op **taken**.

    ![Taak pagina Recovery Services kluizen](./media/oracle-backup-recovery/recovery_service_10.png)

    De status van de back-uptaak wordt weer gegeven in de volgende afbeelding:

    ![Taak pagina Recovery Services kluizen met status](./media/oracle-backup-recovery/recovery_service_11.png)

11. Voor een toepassings consistente back-up verhelpt u eventuele fouten in het logboek bestand. Het logboek bestand bevindt zich op/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Stap 6: de database bestanden verwijderen 
Verderop in dit artikel leert u hoe u het herstel proces kunt testen. Voordat u het herstel proces kunt testen, moet u de database bestanden verwijderen.

1.  Verwijder de tabel ruimte en de back-upbestanden:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  Beschrijving Het Oracle-exemplaar afsluiten:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>De verwijderde bestanden herstellen vanuit de Recovery Services kluizen
Voer de volgende stappen uit om de verwijderde bestanden te herstellen:

1. Zoek in het Azure Portal naar het item *myVault* Recovery Services kluizen. Selecteer op de Blade **overzicht** onder **Back-upitems**het aantal items.

    ![Recovery Services kluizen myVault back-upitems](./media/oracle-backup-recovery/recovery_service_12.png)

2. Selecteer onder **aantal back-UPitems**het aantal items.

    ![Recovery Services kluizen voor het aantal back-items van Azure virtual machine](./media/oracle-backup-recovery/recovery_service_13.png)

3. Klik op de Blade **myvm1** op **bestands herstel (preview-versie)**.

    ![Scherm afbeelding van de pagina Recovery Services kluizen bestands herstel](./media/oracle-backup-recovery/recovery_service_14.png)

4. Klik in het deel venster **bestands herstel (preview)** op **script downloaden**. Sla het Download bestand (. sh) vervolgens op in een map op de client computer.

    ![Opties voor downloaden script bestand opslaan](./media/oracle-backup-recovery/recovery_service_15.png)

5. Kopieer het. sh-bestand naar de virtuele machine.

    In het volgende voor beeld ziet u hoe u een Secure copy-opdracht (SCP) gebruikt om het bestand naar de virtuele machine te verplaatsen. U kunt de inhoud ook kopiëren naar het klem bord en vervolgens de inhoud plakken in een nieuw bestand dat is ingesteld op de virtuele machine.

    > [!IMPORTANT]
    > Zorg er in het volgende voor beeld voor dat u de waarden voor het IP-adres en de map bijwerkt. De waarden moeten worden toegewezen aan de map waarin het bestand is opgeslagen.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```

6. Wijzig het bestand, zodat het eigendom is van de hoofdmap.

    In het volgende voor beeld wijzigt u het bestand zodat het eigendom is van de hoofdmap. Wijzig vervolgens de machtigingen.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```

    In het volgende voor beeld ziet u wat u moet zien nadat u het voor gaande script hebt uitgevoerd. Wanneer u wordt gevraagd om door te gaan, voert u **Y**in.

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

7. De toegang tot de gekoppelde volumes wordt bevestigd.

    Als u wilt afsluiten, typt u **q**en zoekt u naar de gekoppelde volumes. Als u een lijst met de toegevoegde volumes wilt maken, voert u bij de opdracht prompt **VG-k**in.

    ![De df-k-opdracht](./media/oracle-backup-recovery/recovery_service_16.png)

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

9. In het volgende script gebruikt u RMAN om de data base te herstellen:

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

    Klik in de Azure Portal op de Blade **bestands herstel (preview-versie)** op **schijven ontkoppelen**.

    ![Schijven ontkoppelen, opdracht](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>De volledige VM herstellen

In plaats van de verwijderde bestanden van de Recovery Services kluizen te herstellen, kunt u de hele virtuele machine herstellen.

### <a name="step-1-delete-myvm"></a>Stap 1: myVM verwijderen

*   Ga in het Azure Portal naar de **myVM1** -kluis en selecteer vervolgens **verwijderen**.

    ![Opdracht kluis verwijderen](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Stap 2: de virtuele machine herstellen

1.  Ga naar **Recovery Services kluizen**en selecteer vervolgens **myVault**.

    ![myVault-vermelding](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Selecteer op de Blade **overzicht** onder **Back-upitems**het aantal items.

    ![myVault back-up maken van items](./media/oracle-backup-recovery/recover_vm_03.png)

3.  Selecteer op de Blade **Back-upitems (Azure virtual machine)** de optie **myvm1**.

    ![Pagina herstel-VM](./media/oracle-backup-recovery/recover_vm_04.png)

4.  Klik op de Blade **myvm1** op de knop met het weglatings teken (**...**) en klik vervolgens op **VM herstellen**.

    ![VM-opdracht herstellen](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Selecteer op de Blade **herstel punt selecteren** het item dat u wilt herstellen en klik vervolgens op **OK**.

    ![Het herstel punt selecteren](./media/oracle-backup-recovery/recover_vm_06.png)

    Als u toepassings consistente back-up hebt ingeschakeld, wordt er een verticale blauwe balk weer gegeven.

6.  Selecteer op de Blade **herstel configuratie** de naam van de virtuele machine, selecteer de resource groep en klik vervolgens op **OK**.

    ![Configuratie waarden herstellen](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Als u de virtuele machine wilt herstellen, klikt u op de knop **herstellen** .

8.  Als u de status van het herstel proces wilt weer geven, klikt u op **taken**en vervolgens op **back-uptaken**.

    ![Opdracht status van back-uptaken](./media/oracle-backup-recovery/recover_vm_08.png)

    In de volgende afbeelding ziet u de status van het herstel proces:

    ![Status van het herstel proces](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Stap 3: het open bare IP-adres instellen
Nadat de virtuele machine is hersteld, stelt u het open bare IP-adres in.

1.  Voer in het zoekvak het **open bare IP-adres**in.

    ![Lijst met open bare IP-adressen](./media/oracle-backup-recovery/create_ip_00.png)

2.  Klik op de Blade **open bare IP-adressen** op **toevoegen**. Selecteer op de Blade **openbaar IP-adres maken** bij **naam**de open bare IP-naam. Voor **Resourcegroep** selecteert u **Bestaande gebruiken**. Klik vervolgens op **maken**.

    ![IP-adres maken](./media/oracle-backup-recovery/create_ip_01.png)

3.  Als u het open bare IP-adres wilt koppelen aan de netwerk interface voor de virtuele machine, zoekt en selecteert u **myVMip**. Klik vervolgens op **koppelen**.

    ![IP-adres koppelen](./media/oracle-backup-recovery/create_ip_02.png)

4.  Selecteer **netwerk interface**bij **bron type**. Selecteer de netwerk interface die wordt gebruikt door het myVM-exemplaar en klik vervolgens op **OK**.

    ![Resource type en NIC-waarden selecteren](./media/oracle-backup-recovery/create_ip_03.png)

5.  Zoek en open het exemplaar van myVM dat is geporteerd vanuit de portal. Het IP-adres dat is gekoppeld aan de virtuele machine wordt weer gegeven op de Blade **overzicht** van myVM.

    ![Waarde van IP-adres](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Stap 4: verbinding maken met de virtuele machine

*   Gebruik het volgende script om verbinding te maken met de virtuele machine:

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Stap 5: testen of de data base toegankelijk is
*   Als u de toegankelijkheid wilt testen, gebruikt u het volgende script:

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Als de opdracht voor het **opstarten** van de Data Base een fout genereert, raadpleegt u [stap 6: rman gebruiken om de data](#step-6-optional-use-rman-to-recover-the-database)Base te herstellen als u de Data Base wilt herstellen.

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Stap 6: (optioneel) gebruik RMAN om de data base te herstellen
*   Gebruik het volgende script om de data base te herstellen:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

De back-up en het herstel van de Oracle Database 12c-Data Base op een Azure Linux VM is nu voltooid.

## <a name="delete-the-vm"></a>De VM verwijderen

Wanneer u de virtuele machine niet meer nodig hebt, kunt u de volgende opdracht gebruiken om de resource groep, de virtuele machine en alle gerelateerde resources te verwijderen:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

[Zelf studie: Maxi maal beschik bare Vm's maken](../../linux/create-cli-complete.md)

[Azure CLI-voor beelden van VM-implementatie verkennen](../../linux/cli-samples.md)
