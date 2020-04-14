---
title: Oracle ASM instellen op een virtuele Azure Linux-machine | Microsoft Documenten
description: Snel oracle ASM operationeel maken in uw Azure-omgeving.
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
ms.openlocfilehash: 88fd740cad74b12860b6d367c501ba84635f9592
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263162"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Oracle ASM instellen op een virtuele Azure Linux-machine  

Virtuele machines in Azure bieden een volledig geconfigureerde en flexibele computeromgeving. Deze zelfstudie heeft betrekking op de eenvoudige implementatie van virtuele Azure-machines in combinatie met de installatie en configuratie van Oracle Automated Storage Management (ASM).  Procedures voor:

> [!div class="checklist"]
> * Een Oracle Database VM maken en verbinding maken met een Oracle Database VM
> * Oracle Automated Storage Management installeren en configureren
> * Oracle Grid-infrastructuur installeren en configureren
> * Een Oracle ASM-installatie initialiseren
> * Een Oracle DB maken die wordt beheerd door ASM


Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie Azure CLI 2.0.4 of nieuwer uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>De omgeving voorbereiden

### <a name="create-a-resource-group"></a>Een resourcegroep maken

U kunt een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-brongroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In dit voorbeeld wordt een resourcegroep met de naam *myResourceGroup* in de *regio Eastus* genoemd.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Een virtuele machine maken

Als u een virtuele machine wilt maken op basis van de Oracle Database-afbeelding en deze wilt configureren om Oracle ASM te gebruiken, gebruikt u de opdracht [AZ VM Create.](/cli/azure/vm) 

In het volgende voorbeeld wordt een VM met de naam myVM met de Standard_DS2_v2 grootte met vier gekoppelde gegevensschijven van elk 50 GB. Als ze nog niet bestaan in de standaardsleutellocatie, worden er ook SSH-toetsen gemaakt.  Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Nadat u de VM hebt gemaakt, geeft Azure CLI informatie weer die vergelijkbaar is met het volgende voorbeeld. Let op `publicIpAddress`de waarde voor . U gebruikt dit adres om toegang te krijgen tot de VM.

   ```output
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

Als u een SSH-sessie met de VM wilt maken en extra instellingen wilt configureren, gebruikt u de volgende opdracht. Vervang het IP-adres door de `publicIpAddress` waarde voor uw virtuele machine.

```bash
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Oracle ASM installeren

Voer de volgende stappen uit om Oracle ASM te installeren. 

Zie [Oracle ASMLib Downloads voor Oracle Linux 6 voor](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html)meer informatie over het installeren van Oracle ASM.  

1. Je moet inloggen als root om verder te gaan met ASM installatie:

   ```bash
   sudo su -
   ```
   
2. Voer deze aanvullende opdrachten uit om Oracle ASM-componenten te installeren:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Controleer of Oracle ASM is geïnstalleerd:

   ```bash
   rpm -qa |grep oracleasm
   ```

    De uitvoer van deze opdracht moet de volgende onderdelen weergeven:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM vereist specifieke gebruikers en rollen om correct te kunnen functioneren. Met de volgende opdrachten worden de vereiste gebruikersaccounts en -groepen gemaakt: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Controleer of gebruikers en groepen correct zijn gemaakt:

   ```bash
   id grid
   ```

    De uitvoer van deze opdracht moet de volgende gebruikers en groepen weergeven:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Maak een map voor *gebruikersraster* en verander de eigenaar:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Oracle ASM instellen

Voor deze zelfstudie is de standaardgebruiker *raster* en de standaardgroep *asmadmin*. Zorg ervoor dat de *oracle-gebruiker* deel uitmaakt van de asmadmin-groep. Voer de volgende stappen uit om uw Oracle ASM-installatie in te stellen:

1. Het instellen van het oracle ASM-bibliotheekstuurprogramma omvat het definiëren van de standaardgebruiker (raster) en standaardgroep (asmadmin) en het configureren van het station om te starten bij het opstarten (kies y) en om te scannen op schijven op het opstarten (kies y). U moet de aanwijzingen beantwoorden via de volgende opdracht:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   De uitvoer van deze opdracht moet er hetzelfde uitzien als het volgende, stoppen met prompts die moeten worden beantwoord.

    ```output
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. Bekijk de schijfconfiguratie:

   ```bash
   cat /proc/partitions
   ```

   De uitvoer van deze opdracht moet er hetzelfde uitzien als de volgende lijst met beschikbare schijven

   ```output
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. Opformeer schijf */dev/sdc* door de volgende opdracht uit te voeren en de prompts te beantwoorden met:
   - *n* voor nieuwe partitie
   - *p* voor primaire partitie
   - *1* om de eerste partitie te selecteren
   - druk `enter` op de standaard eerste cilinder
   - druk `enter` op de standaard laatste cilinder
   - druk op *w* om de wijzigingen in de partitietabel te schrijven  

   ```bash
   fdisk /dev/sdc
   ```
   
   Met behulp van de bovenstaande `fdisk` antwoorden moet de uitvoer voor de opdracht er als volgt uitzien:

   ```output
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. Herhaal de `fdisk` vorige `/dev/sdd`opdracht `/dev/sde`voor `/dev/sdf`, en .

5. Controleer de schijfconfiguratie:

   ```bash
   cat /proc/partitions
   ```

   De uitvoer van de opdracht moet er als volgt uitzien:

   ```output
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Controleer de oracle ASM-servicestatus en start de Oracle ASM-service:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   De uitvoer van de opdracht moet er als volgt uitzien:

   ```output
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Oracle ASM-schijven maken:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```

   De uitvoer van de opdracht moet er als volgt uitzien:

   ```output
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Lijst Oracle ASM-schijven:

   ```bash
   service oracleasm listdisks
   ```

   De uitvoer van de opdracht moet de volgende Oracle ASM-schijven weergeven:

   ```output
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Wijzig de wachtwoorden voor de gebruikers van root,oracle en grid. **Noteer deze nieuwe wachtwoorden** terwijl u ze later tijdens de installatie gebruikt.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. De maptoestemming wijzigen:

    ```bash
    chmod -R 775 /opt 
    chown grid:oinstall /opt 
    chown oracle:oinstall /dev/sdc1 
    chown oracle:oinstall /dev/sdd1 
    chown oracle:oinstall /dev/sde1 
    chown oracle:oinstall /dev/sdf1 
    chmod 600 /dev/sdc1 
    chmod 600 /dev/sdd1 
    chmod 600 /dev/sde1 
    chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Oracle Grid Infrastructure downloaden en voorbereiden

Voer de volgende stappen uit om de Oracle Grid Infrastructure-software te downloaden en voor te bereiden:

1. Download Oracle Grid Infrastructure van de [oracle ASM-downloadpagina.](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html) 

   Download de twee .zip-bestanden onder de download getiteld **Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) voor Linux x86-64.**

2. Nadat u de .zip-bestanden naar uw clientcomputer hebt gedownload, u Secure Copy Protocol (SCP) gebruiken om de bestanden naar uw vm te kopiëren:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. SSH terug naar uw Oracle VM in Azure om de .zip-bestanden naar de /opt-map te verplaatsen. Wijzig vervolgens de eigenaar van de bestanden:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Rits de bestanden open. (Installeer de Linux unzip tool als het nog niet is geïnstalleerd.)

   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Machtigingen wijzigen:

   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Geconfigureerde swapruimte bijwerken. Oracle Grid-componenten hebben ten minste 6,8 GB wisselruimte nodig om Grid te installeren. De standaardbestandsgrootte voor Oracle Linux-afbeeldingen in Azure is slechts 2048 MB. U moet `ResourceDisk.SwapSizeMB` het `/etc/waagent.conf` bestand vergroten en de WALinuxAgent-service opnieuw starten om de bijgewerkte instellingen van kracht te laten worden. Omdat het een alleen-lezen bestand is, moet u bestandsmachtigingen wijzigen om schrijftoegang in te schakelen.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```

   Zoek `ResourceDisk.SwapSizeMB` naar en wijzig de waarde naar **8192**. U moet indrukken `insert` om de invoegmodus in te voeren, de `esc` waarde van **8192** in te voeren en vervolgens op te drukken om terug te keren naar de opdrachtmodus. Als u de wijzigingen wilt `:wq` schrijven `enter`en het bestand wilt afsluiten, typt en drukt u op .
   
   > [!NOTE]
   > We raden u ten `WALinuxAgent` zeerste aan om swapruimte altijd te configureren, zodat deze altijd is gemaakt op de lokale tijdelijke schijf (tijdelijke schijf) voor de beste prestaties. Zie [Een swapbestand toevoegen in virtuele Linux Azure-machines](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines)voor meer informatie.

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Uw lokale client en vm voorbereiden om x11 uit te voeren
Voor het configureren van Oracle ASM is een grafische interface nodig om de installatie en configuratie te voltooien. We gebruiken het x11 protocol om deze installatie te vergemakkelijken. Als u een clientsysteem (Mac of Linux) gebruikt dat al X11-mogelijkheden heeft ingeschakeld en geconfigureerd - u deze configuratie en installatie exclusief voor Windows-machines overslaan. 

1. [Download PuTTY](https://www.putty.org/) en [download Xming](https://xming.en.softonic.com/) op uw Windows-computer. U moet de installatie van beide toepassingen voltooien met de standaardwaarden voordat u verdergaat.

2. Nadat u PuTTY hebt geïnstalleerd, opent u een opdrachtprompt, wijzigt u de map PuTTY `puttygen.exe` (bijvoorbeeld C:\Program Files\PuTTY) en voert u deze uit om een sleutel te genereren.

3. In PuTTY Key Generator:
   
   1. Genereer een `Generate` sleutel door de knop te selecteren.
   2. Kopieer de inhoud van de toets (Ctrl+C).
   3. Selecteer de knop `Save private key`.
   4. Negeer de waarschuwing over het beveiligen van de `OK`sleutel met een wachtwoordzin en selecteer .

   ![Schermafbeelding van PuTTY-sleutelgenerator](./media/oracle-asm/puttykeygen.png)

4. Voer de volgende opdrachten uit in uw vm:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Maak een bestand met de naam `authorized_keys`. Plak de inhoud van de sleutel in dit bestand en sla het bestand op.

   > [!NOTE]
   > De sleutel moet `ssh-rsa`de tekenreeks bevatten. Ook moet de inhoud van de sleutel een enkele regel tekst zijn.
   >  

6. Start PuTTY op uw klantensysteem. Ga **in** het deelvenster Categorie naar **Verbinding** > **SSH** > **Auth**. Blader in het **vak Privésleutel voor verificatie** naar de sleutel die u eerder hebt gegenereerd.

   ![Schermafbeelding van de SSH-verificatieopties](./media/oracle-asm/setprivatekey.png)

7. Ga **in** het deelvenster Categorie naar **Verbinding** > **SSH** > **X11**. Schakel het selectievakje **X11 doorschakelen inschakelen** in.

   ![Schermafbeelding van de ssh X11-doorstuuropties](./media/oracle-asm/enablex11.png)

8. Ga **in** het deelvenster Categorie naar **Sessie**. Voer uw Oracle `<publicIPaddress>` ASM VM in het dialoogvenster `Saved Session` hostnaam in, `Save`vul een nieuwe naam in en klik op .  Eenmaal opgeslagen, `open` klikt u op om verbinding te maken met uw Oracle ASM virtuele machine.  De eerste keer dat u verbinding maakt, wordt u gewaarschuwd dat het externe systeem niet in de cache van uw register is opgeslagen. Klik `yes` hierop om het toe te voegen en verder te gaan.

   ![Schermafbeelding van de putty-sessieopties](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Oracle Grid Infrastructure installeren

Voer de volgende stappen uit om Oracle Grid Infrastructure te installeren:

1. Log in als **raster**. (U moet zich kunnen aanmelden zonder dat u om een wachtwoord wordt gevraagd.) 

   > [!NOTE]
   > Als u Windows uitvoert, moet u ervoor zorgen dat u bent begonnen met kerstmis voordat u met de installatie begint.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Oracle Grid Infrastructure 12c Release 1 Installer wordt geopend. (Het kan een paar minuten duren voordat de installateur is gestart.)

2. Selecteer op de pagina **Installatieoptie selecteren** de optie **Oracle Grid Infrastructure voor een zelfstandige server installeren en configureren.**

   ![Schermafbeelding van de pagina Installatieoptie selecteren van het installatieprogramma van het installatieprogramma](./media/oracle-asm/install01.png)

3. Zorg er op de pagina **Producttalen** selecteren voor dat **Engels** of de gewenste taal is geselecteerd.  Klik op `next`.

4. Ga als een op de pagina **ASM-schijfgroep maken:**
   - Voer een naam in voor de schijfgroep.
   - **Selecteer**Extern onder **Redundantie**.
   - Selecteer **Allocation Unit Size** **4**.
   - Selecteer **onder Schijven toevoegen**de optie **ORCLASMSP**.
   - Klik op `next`.

5. Selecteer op de pagina **ASM-wachtwoord opgeven** de optie **Dezelfde wachtwoorden gebruiken voor deze accounts** en voer een wachtwoord in.

   ![Schermafbeelding van de pagina ASM-wachtwoord opgeven van het installatieprogramma](./media/oracle-asm/install04.png)

6. Op de pagina **Beheeropties opgeven** hebt u de optie om EM Cloud Control te configureren. We slaan deze optie `next` over - klik om door te gaan. 

7. Gebruik op de pagina **Groepen van geprivilegieerde besturingssysteemgroepen** de standaardinstellingen. Klik `next` om door te gaan.

8. Gebruik op de pagina **Installatielocatie opgeven** de standaardinstellingen. Klik `next` om door te gaan.

9. Wijzig op de pagina **Voorraad** maken `/u01/app/grid/oraInventory`de voorraadmap in . Klik `next` om door te gaan.

   ![Schermafbeelding van de pagina Voorraad maken van het installatieprogramma](./media/oracle-asm/install08.png)

10. Schakel op de **configuratiepagina Root script uitvoering** het selectievakje **Configuratiescripts automatisch uitvoeren** in. Selecteer vervolgens de optie **'Root'** gebruiken en voer het hoofdgebruikerswachtwoord in.

    ![Schermafbeelding van de configuratiepagina van het rootscript van het installatieprogramma](./media/oracle-asm/install09.png)

11. Op de pagina **Vereiste controle uitvoeren** mislukt de huidige instelling met fouten. Dit is een verwacht gedrag. Selecteer `Fix & Check Again`.

12. Klik in het dialoogvenster Script `OK`op te **stellen** op .

13. Controleer **op** de pagina Overzicht de geselecteerde `Install`instellingen en klik vervolgens op .

    ![Schermafbeelding van de overzichtspagina van het installatieprogramma](./media/oracle-asm/install12.png)

14. Er verschijnt een waarschuwingsdialoogvenster waarin wordt weergegeven dat u configuratiescripts moet uitvoeren als een bevoorrechte gebruiker. Klik `Yes` om door te gaan.

15. Klik **op** `Close` de pagina Voltooien om de installatie af te ronden.

## <a name="set-up-your-oracle-asm-installation"></a>Uw Oracle ASM-installatie instellen

Voer de volgende stappen uit om uw Oracle ASM-installatie in te stellen:

1. Zorg ervoor dat u nog steeds bent aangemeld als **raster,** van uw X11-sessie. Je moet misschien `enter` toeslaan om de terminal nieuw leven in te blazen. Start vervolgens de Oracle Automated Storage Management Configuration Assistant:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Oracle ASM-configuratieassistent wordt geopend.

2. Klik in het dialoogvenster **ASM configureren: schijfgroepen** op de `Create` knop en klik vervolgens op `Show Advanced Options`.

3. Ga als een van de twee in het dialoogvenster **Schijfgroep maken:**

   - Voer de naam GEGEVENS van de schijfgroep **in**.
   - Selecteer onder **Lidschijven selecteren** **ORCL_DATA** en **ORCL_DATA1**.
   - Selecteer **Allocation Unit Size** **4**.
   - Klik `ok` hierom de schijfgroep te maken.
   - Klik `ok` hier om het bevestigingsvenster te sluiten.

   ![Schermafbeelding van het dialoogvenster Schijfgroep maken](./media/oracle-asm/asm02.png)

4. Klik in het dialoogvenster **ASM configureren: schijfgroepen** op de `Create` knop en klik vervolgens op `Show Advanced Options`.

5. Ga als een van de twee in het dialoogvenster **Schijfgroep maken:**

   - Voer de naam FRA van de schijfgroep **in**.
   - Selecteer Extern **(geen)** onder **Redundantie**.
   - Selecteer onder **Lidschijven selecteren** **ORCL_FRA**.
   - Selecteer **Allocation Unit Size** **4**.
   - Klik `ok` hierom de schijfgroep te maken.
   - Klik `ok` hier om het bevestigingsvenster te sluiten.

   ![Schermafbeelding van het dialoogvenster Schijfgroep maken](./media/oracle-asm/asm04.png)

6. Selecteer **Afsluiten** om ASM-configuratieassistent te sluiten.

   ![Schermafbeelding van het dialoogvenster ASM: schijfgroepen configureren met knop Afsluiten](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>De database maken

De Oracle-databasesoftware is al geïnstalleerd op de Azure Marketplace-afbeelding. Voer de volgende stappen uit om een database te maken:

1. Schakel over naar de Oracle-superuser en initialiseer de listener voor logboekregistratie:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```

   Databaseconfiguratieassistent wordt geopend.

2. Klik **op** de pagina `Create Database`Databasebewerking op .

3. Ga als een op de pagina **Aanmaakmodus:**

   - Voer een naam in voor de database.
   - Voor **opslagtype**moet u ervoor zorgen dat **Automatisch opslagbeheer (ASM)** is geselecteerd.
   - Voor **locatie databasebestanden**gebruikt u de standaard asm-voorgestelde locatie.
   - Voor **Snel herstelgebied**gebruikt u de standaard asm-voorgestelde locatie.
   - typ een **administratief wachtwoord** in en bevestig het **wachtwoord**.
   - ervoor `create as container database` zorgen dat is geselecteerd.
   - typ een `pluggable database name` waarde in.

4. Controleer **op** de pagina Overzicht de geselecteerde `Finish` instellingen en klik vervolgens om de database te maken.

   ![Schermopname van de pagina Samenvatting](./media/oracle-asm/createdb03.png)

5. De database is gemaakt. Op de pagina **Voltooien** hebt u de mogelijkheid om extra accounts te ontgrendelen om deze database te gebruiken en de wachtwoorden te wijzigen. Als u dit wilt doen, selecteert u `close` **Wachtwoordbeheer** - klik anders op .

## <a name="delete-the-vm"></a>De VM verwijderen

U hebt Oracle Automated Storage Management geconfigureerd op de Oracle DB-afbeelding van de Azure Marketplace.  Wanneer u deze vm niet meer nodig hebt, u de volgende opdracht gebruiken om de resourcegroep, VM en alle gerelateerde bronnen te verwijderen:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

[Zelfstudie: Oracle DataGuard configureren](configure-oracle-dataguard.md)

[Zelfstudie: Oracle GoldenGate configureren](Configure-oracle-golden-gate.md)

Review [Architect an Oracle DB](oracle-design.md)
