---
title: Oracle Golden Gate implementeren op een Azure Linux VM | Microsoft Documenten
description: Zorg dat een Oracle Golden Gate snel in gebruik wordt gemaakt in uw Azure-omgeving.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: mimckitt
ms.openlocfilehash: d7a52f09463d03fc6ecf0b57583d9eb7a5e65d45
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263145"
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Oracle Golden Gate implementeren op een Azure Linux VM 

De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze handleiding wordt uitgelegd hoe u de Azure CLI gebruiken om een Oracle 12c-database te implementeren vanuit de Azure Marketplace-galerieafbeelding. 

In dit document ziet u stap voor stap hoe u Oracle Golden Gate maakt, installeert en configureert op een Azure VM. In deze zelfstudie worden twee virtuele machines ingesteld in een beschikbaarheidsset in één regio. Dezelfde zelfstudie kan worden gebruikt om OracleGolden Gate voor VM's in verschillende beschikbaarheidszones in één Azure-regio of voor VM's in twee verschillende regio's in te stellen.

Voordat u begint, moet u controleren of de Azure-CLI is geïnstalleerd. Zie voor meer informatie de [Installatiehandleiding van de Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>De omgeving voorbereiden

Als u de Oracle Golden Gate-installatie wilt uitvoeren, moet u twee Azure VM's maken op dezelfde beschikbaarheidsset. De Marketplace-afbeelding die u gebruikt om de VM's te maken, is **Oracle:Oracle-Database-Ee:12.1.0.2:latest**.

Je moet ook vertrouwd zijn met Unix editor vi en hebben een basiskennis van x11 (X Windows).

Het volgende is een samenvatting van de omgevingsconfiguratie:
> 
> |  | **Primaire site** | **Site repliceren** |
> | --- | --- | --- |
> | **Oracle release** |Oracle 12c Release 2 – (12.1.0.2) |Oracle 12c Release 2 – (12.1.0.2)|
> | **Machinenaam** |myVM1 |myVM2 |
> | **Besturingssysteem** |Oracle Linux 6.x |Oracle Linux 6.x |
> | **Oracle SID** |CDB1 |CDB1 |
> | **Replicatieschema** |TEST|TEST |
> | **Golden Gate eigenaar/repliceren** |C##GGADMIN |REPUSER |
> | **Golden Gate proces** |EXTORA |REPORA (REPORA)|


### <a name="sign-in-to-azure"></a>Aanmelden bij Azure 

Meld u aan bij uw Azure-abonnement met de [inlogopdracht az.](/cli/azure/reference-index) Volg vervolgens de aanwijzingen op het scherm.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-brongroep is een logische container waarin Azure-resources worden geïmplementeerd en waaruit ze kunnen worden beheerd. 

In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt op de locatie `westus`.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken

De volgende stap is optioneel, maar aanbevolen. Zie [Handleiding voor Azure-beschikbaarheidssets voor](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)meer informatie.

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een VM met de opdracht [az vm create](/cli/azure/vm). 

In het volgende voorbeeld `myVM1` worden `myVM2`twee VM's met de naam en . SSH-sleutels maken als deze nog niet bestaan op een standaardsleutellocatie. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.

#### <a name="create-myvm1-primary"></a>MyVM1 (primair) maken:

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Nadat de VM is gemaakt, worden in de Azure CLI informatie weergegeven die vergelijkbaar is met het volgende voorbeeld. (Neem nota `publicIpAddress`van de . Dit adres wordt gebruikt om toegang te krijgen tot de VM.)

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

#### <a name="create-myvm2-replicate"></a>MyVM2 maken (repliceren):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Let op `publicIpAddress` de en eer nadat het is gemaakt.

### <a name="open-the-tcp-port-for-connectivity"></a>De TCP-poort openen voor connectiviteit

De volgende stap is het configureren van externe eindpunten, waarmee u op afstand toegang hebt tot de Oracle-database. Voer de volgende opdrachten uit om de externe eindpunten te configureren.

#### <a name="open-the-port-for-myvm1"></a>Open de poort voor myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

De resultaten moeten lijken op het volgende antwoord:

```output
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

#### <a name="open-the-port-for-myvm2"></a>Open de poort voor myVM2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Verbinding maken met de virtuele machine

Gebruik de volgende opdracht om een SSH-sessie te starten voor de virtuele machine. Vervang het IP-adres door het `publicIpAddress` van de virtuele machine.

```bash
ssh <publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>De database maken op myVM1 (primair)

De Oracle-software is al geïnstalleerd op de Marketplace-afbeelding, dus de volgende stap is het installeren van de database. 

Voer de software uit als de 'oracle' superuser:

```bash
sudo su - oracle
```

Maak de database:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```

Uitgangen moeten er hetzelfde uitzien als het volgende antwoord:

```output
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

Stel de ORACLE_SID- en ORACLE_HOME variabelen in.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Optioneel u ORACLE_HOME en ORACLE_SID toevoegen aan het .bashrc-bestand, zodat deze instellingen worden opgeslagen voor toekomstige aanmeldingen:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Oracle-listener starten

```bash
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>De database op myVM2 maken (repliceren)

```bash
sudo su - oracle
```

Maak de database:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```

Stel de ORACLE_SID- en ORACLE_HOME variabelen in.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Optioneel u ORACLE_HOME en ORACLE_SID toevoegen aan het .bashrc-bestand, zodat deze instellingen worden opgeslagen voor toekomstige aanmeldingen.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Oracle-listener starten

```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Gouden poort configureren 
Als u Golden Gate wilt configureren, neemt u de stappen in deze sectie.

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Archieflogboekmodus inschakelen op myVM1 (primair)

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
```
Schakel forcelogging in en zorg ervoor dat er ten minste één logboekbestand aanwezig is.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Golden Gate-software downloaden
Voer de volgende stappen uit om de Oracle Golden Gate-software te downloaden en voor te bereiden:

1. Download het **fbo_ggs_Linux_x64_shiphome.zip-bestand** van de [downloadpagina van Oracle Golden Gate.](https://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html) Onder de downloadtitel **Oracle GoldenGate 12.x.x.x voor Oracle Linux x86-64**moet er een set .zip-bestanden worden gedownload.

2. Nadat u de .zip-bestanden naar uw clientcomputer hebt gedownload, gebruikt u Secure Copy Protocol (SCP) om de bestanden naar uw vm te kopiëren:

   ```bash
   $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
   ```

3. Verplaats de .zip-bestanden naar de **map /opt.** Wijzig vervolgens de eigenaar van de bestanden als volgt:

   ```bash
   $ sudo su -
   # mv <folder>/*.zip /opt
   ```

4. Rits de bestanden uit (installeer het Linux-uitritshulpprogramma als deze nog niet is geïnstalleerd):

   ```bash
   # yum install unzip
   # cd /opt
   # unzip fbo_ggs_Linux_x64_shiphome.zip
   ```

5. Machtigingen wijzigen:

   ```bash
   # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
   ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>De client en de VM voorbereiden om x11 uit te voeren (alleen voor Windows-clients)
Dit is een optionele stap. U deze stap overslaan als u een Linux-client gebruikt of al een x11-installatie hebt.

1. Download PuTTY en Xming op uw Windows-computer:

   * [PuTTY downloaden](https://www.putty.org/)
   * [Kerst downloaden](https://xming.en.softonic.com/)

2. Nadat u PuTTY hebt geïnstalleerd, voert u in de map PuTTY (bijvoorbeeld C:\Program Files\PuTTY) puttygen.exe (PuTTY Key Generator) uit.

3. In PuTTY Key Generator:

   - Als u een sleutel wilt genereren, selecteert u de knop **Genereren.**
   - Kopieer de inhoud van de toets **(Ctrl+C).**
   - Selecteer de knop **Privésleutel opslaan.**
   - Negeer de waarschuwing die wordt weergegeven en selecteer **OK**.

   ![Schermafbeelding van de putty-toetsgeneratorpagina](./media/oracle-golden-gate/puttykeygen.png)

4. Voer de volgende opdrachten uit in uw vm:

   ```bash
   # sudo su - oracle
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. Een bestand maken met de naam **authorized_keys**. Plak de inhoud van de sleutel in dit bestand en sla het bestand op.

   > [!NOTE]
   > De sleutel moet `ssh-rsa`de tekenreeks bevatten. Ook moet de inhoud van de sleutel een enkele regel tekst zijn.
   >  

6. Start PuTTY. Selecteer **Verbinding** > **SSH** > **Auth**in het deelvenster **Categorie** . Blader in het **vak Privésleutel voor verificatie** naar de sleutel die u eerder hebt gegenereerd.

   ![Schermafbeelding van de pagina Privésleutel instellen](./media/oracle-golden-gate/setprivatekey.png)

7. Selecteer **Verbinding** > **SSH** > **X11**in het deelvenster **Categorie** . Schakel vervolgens het **vak X11 doorschakelen inschakelen** in.

   ![Schermafbeelding van de pagina X11 inschakelen](./media/oracle-golden-gate/enablex11.png)

8. Ga **in** het deelvenster Categorie naar **Sessie**. Voer de hostgegevens in en selecteer **Openen**.

   ![Schermafbeelding van de sessiepagina](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Golden Gate-software installeren

Voer de volgende stappen uit om Oracle Golden Gate te installeren:

1. Meld je aan als orakel. (U moet zich kunnen aanmelden zonder dat u om een wachtwoord wordt gevraagd.) Zorg ervoor dat xming wordt uitgevoerd voordat u met de installatie begint.

   ```bash
   $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
   $ ./runInstaller
   ```

2. Selecteer 'Oracle GoldenGate for Oracle Database 12c'. Selecteer **vervolgens Volgende** om door te gaan.

   ![Schermafbeelding van de pagina Installatie selecteren van het installatieprogramma](./media/oracle-golden-gate/golden_gate_install_01.png)

3. Wijzig de softwarelocatie. Selecteer vervolgens het vak **Startbeheer** en voer de databaselocatie in. Selecteer **Volgende** om door te gaan.

   ![Schermafbeelding van de pagina Installatie selecteren](./media/oracle-golden-gate/golden_gate_install_02.png)

4. Wijzig de voorraadmap en selecteer **Volgende** om door te gaan.

   ![Schermafbeelding van de pagina Installatie selecteren](./media/oracle-golden-gate/golden_gate_install_03.png)

5. **Selecteer** Installeren om door te gaan in het scherm **Overzicht.**

   ![Schermafbeelding van de pagina Installatie selecteren van het installatieprogramma](./media/oracle-golden-gate/golden_gate_install_04.png)

6. U wordt mogelijk gevraagd om een script uit te voeren als 'root'. Als dat het zo is, opent u een afzonderlijke sessie, ssh naar de VM, sudo om te rooten en voert u het script uit. Selecteer **OK** doorgaan.

   ![Schermafbeelding van de pagina Installatie selecteren](./media/oracle-golden-gate/golden_gate_install_05.png)

7. Wanneer de installatie is voltooid, selecteert u **Sluiten** om het proces te voltooien.

   ![Schermafbeelding van de pagina Installatie selecteren](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>Service instellen op myVM1 (primair)

1. Het bestand tnsnames.ora maken of bijwerken:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Maak de eigenaar en gebruikersaccounts van de Golden Gate.

   > [!NOTE]
   > Het eigenaaraccount moet c##-voorvoegsel hebben.
   >

    ```bash
    $ sqlplus / as sysdba
    SQL> CREATE USER C##GGADMIN identified by ggadmin;
    SQL> EXEC dbms_goldengate_auth.grant_admin_privilege('C##GGADMIN',container=>'ALL');
    SQL> GRANT DBA to C##GGADMIN container=all;
    SQL> connect C##GGADMIN/ggadmin
    SQL> ALTER SESSION SET CONTAINER=PDB1;
    SQL> EXIT;
    ```

3. Maak het Golden Gate-testgebruikersaccount:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> @demo_ora_insert
   SQL> EXIT;
   ```

4. Configureer het parameterbestand extraheren.

   Start de Golden gate command-line interface (ggsci):

   ```bash
   $ sudo su - oracle
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> DBLOGIN USERID test@pdb1, PASSWORD test
   Successfully logged into database  pdb1
   GGSCI>  ADD SCHEMATRANDATA pdb1.test
   2017-05-23 15:44:25  INFO    OGG-01788  SCHEMATRANDATA has been added on schema test.
   2017-05-23 15:44:25  INFO    OGG-01976  SCHEMATRANDATA for scheduling columns has been added on schema test.

   GGSCI> EDIT PARAMS EXTORA
   ```

5. Voeg het volgende toe aan het parameterbestand EXTRACT (met vi-opdrachten). Druk op Esc-toets: ':wq!' om bestand op te slaan. 

   ```bash
   EXTRACT EXTORA
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTRAIL ./dirdat/rt  
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT 
   LOGALLSUPCOLS
   UPDATERECORDFORMAT COMPACT
   TABLE pdb1.test.TCUSTMER;
   TABLE pdb1.test.TCUSTORD;
   ```

6. Register extract-geïntegreerd extract:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci

   GGSCI> dblogin userid C##GGADMIN, password ggadmin
   Successfully logged into database CDB$ROOT.

   GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

   2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

   GGSCI> exit
   ```

7. Stel controlepunten voor fragmenten in en start realtime extract:

   ```bash
   $ ./ggsci
   GGSCI>  ADD EXTRACT EXTORA, INTEGRATED TRANLOG, BEGIN NOW
   EXTRACT (Integrated) added.

   GGSCI>  ADD RMTTRAIL ./dirdat/rt, EXTRACT EXTORA, MEGABYTES 10
   RMTTRAIL added.

   GGSCI>  START EXTRACT EXTORA

   Sending START request to MANAGER ...
   EXTRACT EXTORA starting

   GGSCI > info all

   Program     Status      Group       Lag at Chkpt  Time Since Chkpt

   MANAGER     RUNNING
   EXTRACT     RUNNING     EXTORA      00:00:11      00:00:04
   ```

   In deze stap vindt u de startende SCN, die later zal worden gebruikt, in een andere sectie:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> SELECT current_scn from v$database;
   CURRENT_SCN
   -----------
      1857887
   SQL> EXIT;
   ```

   ```bash
   $ ./ggsci
   GGSCI> EDIT PARAMS INITEXT
   ```

   ```bash
   EXTRACT INITEXT
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTASK REPLICAT, GROUP INITREP
   TABLE pdb1.test.*, SQLPREDICATE 'AS OF SCN 1857887'; 
   ```

   ```bash
   GGSCI> ADD EXTRACT INITEXT, SOURCEISTABLE
   ```

### <a name="set-up-service-on-myvm2-replicate"></a>Service instellen op myVM2 (repliceren)


1. Het bestand tnsnames.ora maken of bijwerken:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Een replicerende account maken:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> create user repuser identified by rep_pass container=current;
   SQL> grant dba to repuser;
   SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
   SQL> connect repuser/rep_pass@pdb1 
   SQL> EXIT;
   ```

3. Maak een Golden Gate-testgebruikersaccount aan:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> EXIT;
   ```

4. REPLICAT-parameterbestand om wijzigingen te repliceren: 

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> EDIT PARAMS REPORA  
   ```

   Inhoud van REPORA parameterbestand:

   ```bash
   REPLICAT REPORA
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/repora.dsc, PURGE, MEGABYTES 100
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT
   DBOPTIONS INTEGRATEDPARAMS(parallelism 6)
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;
   ```

5. Stel een replicerende controlepunt in:

   ```bash
   GGSCI> ADD REPLICAT REPORA, INTEGRATED, EXTTRAIL ./dirdat/rt
   GGSCI> EDIT PARAMS INITREP

   ```

   ```bash
   REPLICAT INITREP
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/tcustmer.dsc, APPEND
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;   
   ```

   ```bash
   GGSCI> ADD REPLICAT INITREP, SPECIALRUN
   ```

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>De replicatie instellen (myVM1 en myVM2)

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1. De replicatie instellen op myVM2 (repliceren)

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```

Werk het bestand met het volgende bij:

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```

Start vervolgens de Manager-service opnieuw:

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2. De replicatie instellen op myVM1 (primair)

Start de eerste belasting en controleer op fouten:

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```

#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3. De replicatie instellen op myVM2 (repliceren)

Wijzig het SCN-nummer met het nummer dat u eerder hebt verkregen:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```

De replicatie is begonnen en u deze testen door nieuwe records in te voegen in testtabellen.


### <a name="view-job-status-and-troubleshooting"></a>Jobstatus en probleemoplossing weergeven

#### <a name="view-reports"></a>Rapporten weergeven
Voer de volgende opdrachten uit om rapporten over myVM1 weer te geven:

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
Voer de volgende opdrachten uit om rapporten over myVM2 weer te geven:

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>Status en geschiedenis weergeven
Voer de volgende opdrachten uit om status en geschiedenis op myVM1 weer te geven:

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

Voer de volgende opdrachten uit om status en geschiedenis op myVM2 weer te geven:

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
Dit voltooit de installatie en configuratie van Golden Gate op Oracle linux.


## <a name="delete-the-virtual-machine"></a>Verwijder de virtuele machine

Wanneer deze niet meer nodig is, kan de volgende opdracht worden gebruikt om de resourcegroep, vm en alle gerelateerde resources te verwijderen.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

[Zelfstudie voor het maken van virtuele machines met een hoge beschikbaarheid](../../linux/create-cli-complete.md)

[CLI-voorbeelden voor VM-implementatie verkennen](../../linux/cli-samples.md)
