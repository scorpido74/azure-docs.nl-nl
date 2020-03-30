---
title: TmaxSoft OpenFrame installeren op virtuele Azure-machines
description: Host uw IBM z/OS mainframeworkloads opnieuw met de TmaxSoft OpenFrame-omgeving op Azure Virtual Machines (VM's).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 1ad6e52c421d9cfec4640d3a330b5507d6ed3e9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72436047"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame installeren op Azure

Meer informatie over het instellen van een OpenFrame-omgeving op Azure die geschikt is voor productie-, demo's, tests of productieworkloads. Deze tutorial leidt je door elke stap.

OpenFrame bevat meerdere componenten die de mainframe-emulatieomgeving op Azure maken. OpenFrame onlineservices vervangen bijvoorbeeld de mainframe middleware zoals IBM Customer Information Control System (CICS) en OpenFrame Batch vervangt het IBM mainframe's Job Entry Subsystem (JES).

OpenFrame werkt met elke relationele database, waaronder Oracle Database, Microsoft SQL Server, IBM Db2 en MySQL. Deze installatie van OpenFrame maakt gebruik van de TmaxSoft Tibero relationele database. Zowel OpenFrame als Tibero draaien op een Linux-besturingssysteem. Deze zelfstudie installeert CentOS 7.3, hoewel u andere ondersteunde Linux-distributies gebruiken. De OpenFrame-toepassingsserver en de Tibero-database zijn geïnstalleerd op één virtuele machine (VM).

De zelfstudie doorloopt u bij de installatie van de onderdelen van de OpenFrame-suite. Sommige moeten afzonderlijk worden geïnstalleerd.

Belangrijkste OpenFrame-componenten:

- Vereiste installatiepakketten.
- Tibero database.
- Odbc (Open Database Connectivity) wordt gebruikt door toepassingen in OpenFrame om te communiceren met de Tibero-database.
- OpenFrame Base, de middleware die het hele systeem beheert.
- OpenFrame Batch, de oplossing die de batchsystemen van het mainframe vervangt.
- TACF, een servicemodule die de toegang van gebruikers tot systemen en bronnen regelt.
- ProSort, een sorteertool voor batchtransacties.
- OFCOBOL, een compiler die de COBOL-programma's van het mainframe interpreteert.
- OFASM, een compiler die de assemblerprogramma's van het mainframe interpreteert.
- OpenFrame Server Type C (OSC), de oplossing die de middleware en IBM CICS van het mainframe vervangt.
- Java Enterprise User Solution (JEUS), een webserver die is gecertificeerd voor Java Enterprise Edition 6.
- OFGW, de OpenFrame-gatewaycomponent die een 3270-listener biedt.
- OFManager, een oplossing die de werkings- en beheerfuncties van OpenFrame biedt in de webomgeving.

Andere vereiste OpenFrame-componenten:

- OSI, de oplossing die de mainframe middleware en IMS DC vervangt.
- TJES, de oplossing die de JES-omgeving van het mainframe biedt.
- OFTSAM, de oplossing waarmee (V)SAM-bestanden in het open systeem kunnen worden gebruikt.
- OFHiDB, de oplossing die de IMS DB van het mainframe vervangt.
- OFPLI, een compiler die de PL/I-programma's van het mainframe interpreteert.
- PROTRIEVE, een oplossing die de mainframetaal CA-Easytrieve uitvoert.
- OFMiner, een oplossing die de mainframes-assets analyseert en vervolgens migreert naar Azure.

## <a name="architecture"></a>Architectuur

De volgende afbeelding geeft een overzicht van de architectonische componenten van OpenFrame 7.0 die in deze zelfstudie zijn geïnstalleerd:

![OpenFrame-componenten](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Azure-systeemvereisten

In de volgende tabel worden de vereisten voor de installatie op Azure weergegeven.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Vereiste</th><th>Beschrijving</th></tr>
</thead>
<tbody>
<tr><td>Ondersteunde Linux-distributies op Azure
</td>
<td>
Linux x86 2.6 (32-bits, 64-bits)<br/>
Rode hoed 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Hardware
</td>
<td>Kernen: 2 (minimaal)<br/>
Geheugen: 4 GB (minimaal)<br/>
Wisselruimte: 1 GB (minimaal)<br/>
Harde schijf: 100 GB (minimaal)<br/>
</td>
</tr>
<tr><td>Optionele software voor Windows-gebruikers
</td>
<td>PuTTY: gebruikt in deze handleiding om VM-functies te configureren<br/>
WinSCP: een populaire SFTP-client en FTP-client die u gebruiken<br/>
Eclipse voor Windows: een ontwikkelplatform ondersteund door TmaxSoft<br/>
(Microsoft Visual Studio wordt momenteel niet ondersteund)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Vereisten

Plan op de uitgaven een paar dagen om alle benodigde software te monteren en alle handmatige processen te voltooien.

Ga voordat u aan de slag gaat als volgt:

- Haal de OpenFrame-installatiemedia van TmaxSoft. Als u een bestaande TmaxSoft-klant bent, neemt u contact op met uw TmaxSoft-vertegenwoordiger voor een gelicentieerd exemplaar. Anders, vraag een proefversie van [TmaxSoft](https://www.tmaxsoft.com/contact/).

- Vraag de OpenFrame-documentatie <support@tmaxsoft.com>aan door e-mail te sturen naar .

- Ontvang een Azure-abonnement als u er nog geen hebt. Je ook een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aanmaken voordat je begint.

- Optioneel. Stel een site-to-site VPN-tunnel of een jumpbox in die de toegang tot de Azure VM beperkt tot de toegestane gebruikers in uw organisatie. Deze stap is niet vereist, maar het is een beste praktijk.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Een VM instellen op Azure voor OpenFrame en Tibero

U de OpenFrame-omgeving instellen met behulp van verschillende implementatiepatronen, maar in de volgende procedure wordt uitgelegd hoe u de OpenFrame-toepassingsserver en de Tibero-database op één VM implementeert. In grotere omgevingen en voor aanzienlijke workloads is het een goede gewoonte om de database afzonderlijk op zijn eigen VM te implementeren voor betere prestaties.

**Een VM maken**

1. Ga naar de <https://portal.azure.com> Azure-portal bij en meld u aan bij uw account.

2. Klik op **Virtuele machines**.

    ![Resourcelijst in Azure-portal](media/vm-01.png)

3. Klik op**toevoegen**.

    ![Optie toevoegen in Azure-portal](media/vm-02.png)

4. Klik rechts van **besturingssystemen**op **Meer**.

     ![Meer optie in Azure-portal](media/vm-03.png)

5. Klik op **CentOS-gebaseerde 7.3** om deze doorloop precies te volgen, of u een andere ondersteunde Linux-distributie kiezen.

     ![Opties voor het besturingssysteem in Azure-portal](media/vm-04.png)

6. Voer in de **basisinstellingen** **Naam**, **Gebruikersnaam**, **Verificatietype**, **Abonnement** (Pay-As-You-Go is de AWS-betalingsstijl) en de **groep Resource** in (gebruik een bestaande groep of maak een TmaxSoft-groep).

7. Als u klaar bent (inclusief het sleutelpaar voor **verificatietype**( klik op **Verzenden**.

> [!NOTE]
> Als u een SSH-openbare sleutel gebruikt voor **verificatietype,** raadpleegt u de stappen in de volgende sectie om het openbare/privésleutelpaar te genereren en hervat u de stappen hier.

### <a name="generate-a-publicprivate-key-pair"></a>Een publiek/privé sleutelpaar genereren

Als u een Windows-besturingssysteem gebruikt, hebt u PuTTYgen nodig om een openbaar/privé sleutelpaar te genereren.

De openbare sleutel kan vrij worden gedeeld, maar de private sleutel moet volledig geheim worden gehouden en mag nooit worden gedeeld met een andere partij. Nadat u de sleutels hebt gemaakt, moet u de **openbare SSH-sleutel** in de configuratie plakken en deze in feite uploaden naar de Linux-vm. Het wordt opgeslagen\_in geautoriseerde \~sleutels in de /.ssh-map van de thuismap van het gebruikersaccount. De Linux VM is dan in staat om de verbinding te herkennen en te valideren zodra u de bijbehorende **SSH private key** in de SSH client (in ons geval, PuTTY) hebt verstrekt.

Wanneer u nieuwe personen toegang geeft tot de VM: 

- Elk nieuw individu genereert zijn eigen publieks-private sleutels met PuTTYgen.
- Particulieren slaan hun eigen privésleutels apart op en sturen de openbare sleutelinformatie naar de beheerder van de VM.
- De beheerder plakt de inhoud van \~de openbare sleutel\_op het bestand /.ssh/authorized keys.
- Het nieuwe individu verbindt via PuTTY.

**Een publiek/privaat sleutelpaar genereren**

1.  Download PuTTYgen <https://www.putty.org/> van en installeer het met behulp van alle standaardinstellingen.

2.  Als u PuTTYgen wilt openen, zoekt u\\de\\PuTTY-installatiemap in C: Program Files PuTTY.

    ![PuTTY-interface](media/puttygen-01.png)

3.  Klik op **Genereren**.

    ![Dialoogvenster PuTTY-sleutelgenerator](media/puttygen-02.png)

4.  Na generatie, sla zowel de openbare sleutel en private key. Plak de inhoud van de openbare sleutel in het gedeelte **Openbare sleutel van SSH** van het deelvenster **Basisbeginselen voor virtuele \> machines maken** (weergegeven in stap 6 en 7 in de vorige sectie).

    ![Dialoogvenster PuTTY-sleutelgenerator](media/puttygen-03.png)

### <a name="configure-vm-features"></a>VM-functies configureren

1. Kies in de Azure-portal in het **blad Een grootte kiezen** de gewenste hardware-instellingen voor de Linux-machine. De *minimumvereisten* voor het installeren van zowel Tibero als OpenFrame zijn 2 CPU's en 4 GB RAM, zoals in deze voorbeeldinstallatie wordt weergegeven:

    ![Virtuele machine maken - Basisbeginselen](media/create-vm-01.png)

2. Klik op **3 Instellingen** en gebruik de standaardinstellingen om optionele functies te configureren.
3. Bekijk uw betalingsgegevens.

    ![Virtuele machine maken - Aankoop](media/create-vm-02.png)

4. Dien uw selecties in. Azure begint de VM te implementeren. Dit proces duurt meestal een paar minuten.

5. Wanneer de VM wordt geïmplementeerd, wordt het dashboard weergegeven met alle instellingen die tijdens de configuratie zijn geselecteerd. Noteer het **ip-adres van het publiek**.

    ![tmax op Azure-dashboard](media/create-vm-03.png)

6. Open PuTTY.

7. Typ **voor Host Name**uw gebruikersnaam en het openbare IP-adres dat u hebt gekopieerd. Bijvoorbeeld, **gebruikersnaam\@publicip**.

    ![Dialoogvenster PuTTY-configuratie](media/putty-01.png)

8. Klik **in** het vak Categorie op **Verbinding \> SSH \> Auth**. Geef het pad naar uw **privésleutelbestand** op.

    ![Dialoogvenster PuTTY-configuratie](media/putty-02.png)

9. Klik **op Openen** om het PuTTY-venster te starten. Als dit is gelukt, bent u verbonden met uw nieuwe CentOS VM die op Azure wordt uitgevoerd.

10. Als u zich wilt aanmelden als rootgebruiker, typt u **sudo bash**.

    ![Aanmelding van rootgebruiker in opdrachtvenster](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>De omgeving en pakketten instellen

Nu de VM is gemaakt en u bent aangemeld, moet u een paar installatiestappen uitvoeren en de vereiste voorinstallatiepakketten installeren.

1. Breng de naam **van demo** naar het lokale IP-adres`vi /etc/hosts`met behulp van vi om het hosts-bestand te bewerken ( ). Ervan uitgaande dat onze IP is 192.168.96.148 ofdemo, dit is vóór de verandering:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Dit is na de wijziging:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Groepen en gebruikers maken:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Het wachtwoord voor gebruikersoframe7 wijzigen:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Werk de kernelparameters bij in /etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Vernieuw de kernelparameters dynamisch zonder opnieuw op te starten:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Ontvang de vereiste pakketten: controleer of de server is verbonden met internet, download de volgende pakketten en installeer ze vervolgens:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - Libaio
     - Ncurses

          > [!NOTE]
          > Maak na het installeren van het ncurses-pakket de volgende symbolische koppelingen:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - Gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - Strace
     - Ltrace
     - Gdb

7. Ga als volgt te werk in het geval van javarpm-installatie:

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>De Tibero-database installeren

Tibero biedt de verschillende belangrijke functies in de OpenFrame-omgeving op Azure:

- Tibero wordt gebruikt als het openframe interne datastore voor verschillende systeemfuncties.
- VSAM-bestanden, waaronder KSDS, RRDS en ESDS, gebruiken de Tibero-database intern voor gegevensopslag.
- De TACF data repository wordt opgeslagen in Tibero.
- De openframecatalogusgegevens worden opgeslagen in Tibero.
- De Tibero-database kan worden gebruikt als vervanging voor IBM Db2 om toepassingsgegevens op te slaan.

**Tibero installeren**

1. Controleer of het tibero-installatiebestand aanwezig is en controleer het versienummer.
2. Kopieer de Tibero-software naar het Tibero-gebruikersaccount (oframe). Bijvoorbeeld:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Open .bash-profiel\_`vi .bash_profile`in vi ( ) en plak er het volgende in:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Als u het bashprofiel wilt uitvoeren, moet u bij het opdrachtprompttype het gewenste overzicht geven:

    ```
    source .bash_profile
    ```

5. Genereer het tipbestand (een configuratiebestand voor Tibero) en open het vervolgens in vi. Bijvoorbeeld:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Wijzig \$\_TB HOME/client/config/tbdsn.tbr en plaats 127.0.0.1 in plaats van localhost zoals afgebeeld:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Maak de database. De volgende uitvoer wordt weergegeven:

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Als u Tibero wilt recyclen, schakelt u deze eerst uit met de `tbdown` opdracht. Bijvoorbeeld:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Nu opstarten Tibero met behulp van `tbboot`. Bijvoorbeeld:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Als u een tabelruimte wilt maken, opent u de database met sys-gebruiker (sys/tmax) en maakt u vervolgens de benodigde tabelruimte voor het standaardvolume en TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Typ nu de volgende SQL-opdrachten:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Start Tibero op en controleer of de Tibero-processen worden uitgevoerd:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Uitvoer:

![Tibero-uitgang](media/tibero-01.png)

## <a name="install-odbc"></a>ODBC installeren

Toepassingen in OpenFrame communiceren met de Tibero-database met behulp van de ODBC API van het open-source unixODBC-project.

ODBC installeren:

1. Controleer of het installatiebestand unixODBC-2.3.4.tar.gz aanwezig `wget unixODBC-2.3.4.tar.gz` is of gebruik de opdracht. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Rits de binaire uit. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Navigeer naar de map unixODBC-2.3.4 en genereer de Makefile met behulp van de informatie van de controlemachine. Bijvoorbeeld:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     UnixODBC is standaard geïnstalleerd in /usr `--prefix` /local, dus geeft een waarde door om de locatie te wijzigen. Op dezelfde manier worden configuratiebestanden standaard geïnstalleerd `--sysconfdir` in /etc, dus geeft de waarde van de gewenste locatie door.

4. Makefile uitvoeren:`[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Kopieer het uitvoerbare bestand in de programmamap na het compileren. Bijvoorbeeld:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Gebruik vi om het bashprofiel te bewerken ( )`vi ~/.bash_profile`en voeg het volgende toe:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Breng de ODBC toe. Bewerk de volgende bestanden dienovereenkomstig. Bijvoorbeeld:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Maak een symbolische koppeling en valideer de Tibero-databaseverbinding:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

De volgende uitvoer wordt weergegeven:

![ODBC-uitvoer met verbinding met SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>OpenFrame Base installeren

De basistoepassingsserver wordt geïnstalleerd vóór de afzonderlijke services die OpenFrame gebruikt om het systeem op Azure te beheren, inclusief de transactieverwerkingsserverprocessen.

**OpenFrame Base installeren**

1. Controleer of de Tibero-installatie is geslaagd en\_controleer\_\_of\_de\_volgende OpenFrame Base7 0 Linux x86 64.bin-installatiebestand en base.properties-configuratiebestand aanwezig zijn.

2. Werk het bashprofiel bij met de volgende Tibero-specifieke informatie:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Voer het bashprofiel uit:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Zorg ervoor dat de Tibero-processen worden uitgevoerd. Bijvoorbeeld:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Base](media/base-01.png)

     > [!IMPORTANT]
     > Zorg ervoor dat u Tibero start voor de installatie.

5. Licentie genereren op [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) en plaats de OpenFrame Base-, Batch-, TACF-, OSC-licenties in de juiste map:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Download de binaire en base.properties-bestanden van OpenFrame Base:

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Voer het installatieprogramma uit met het bestand base.properties. Bijvoorbeeld:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Als het bericht Installatie voltooid is voltooid, wordt deze gedesspeelt.

8. Controleer de mapstructuur Van `ls -ltr` OpenFrame Base met de opdracht. Bijvoorbeeld:

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. Start OpenFrame Base:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![tmboot, opdrachtuitvoer](media/base-02.png)

10. Controleer of de processtatus gereed is met de opdracht tmadmin in si. RDY wordt weergegeven in de **statuskolom** voor elk van de processen:

     ![tmadmin opdracht uitvoer](media/base-03.png)

11. OpenFrame-basis afsluiten:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>OpenFrame Batch installeren

OpenFrame Batch bestaat uit verschillende componenten die mainframebatchomgevingen simuleren en wordt gebruikt om batchtaken uit te voeren op Azure.

**Batch installeren**

1. Controleer of de basisinstallatie is geslaagd en\_controleer\_\_of\_het\_configuratiebestand OpenFrame Batch7 0 Fix2 MVS Linux\_x86\_64.bin installerbestand en batch.properties aanwezig zijn:

2. Typ bij de `vi batch.properties` opdrachtprompt het bestand batch.properties bewerken met vi.

3. Wijzig de parameters als volgt:

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. Als u het batch-installatieprogramma wilt uitvoeren, gaat u op het type opdrachtprompt:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Wanneer de installatie is voltooid, start u `tmboot` de geïnstalleerde OpenFrame-suites door te typen op de opdrachtprompt.

    ![tmboot-uitvoer](media/tmboot-01.png)

6. Typ `tmadmin` bij de opdrachtprompt om het OpenFrame-proces te controleren.

    ![Scherm Tmax-beheerder](media/tmadmin-01.png)

7. Voer de volgende opdrachten uit:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Gebruik `tmdown` de opdracht Om Batch op te starten en af te sluiten:

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>TACF installeren

TACF Manager is een OpenFrame-servicemodule die de toegang van gebruikers tot systemen en bronnen regelt via RACF-beveiliging.

**TACF installeren**

1. Controleer of het\_installatiebestand\_\_OpenFrame\_\_Tacf7\_0 Fix2 Linux x86 64.bin en het configuratiebestand tacf.properties aanwezig zijn.
2. Zorg ervoor dat de batch-installatie is geslaagd en gebruik`vi tacf.properties`vi om het tacf.properties-bestand te openen ( ).
3. Wijzig de TACF-parameters:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. Pas na het voltooien van TACF-installateur de TACF-omgevingsvariabelen toe. Typ het volgende na de opdrachtprompt:

     ```
     source \~/.bash\_profile
     ```

5. Voer de TACF installer uit. Typ het volgende na de opdrachtprompt:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     De output ziet er ongeveer als volgt uit:

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. Typ `tmboot` openframe bij de opdrachtprompt. De output ziet er ongeveer als volgt uit:

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. Controleer of de processtatus `tmadmin` klaar `si` is met de opdracht. Bijvoorbeeld:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     In de **statuskolom** wordt RDY weergegeven:

    ![RDY in de statuskolom](media/tmboot-02.png)

8. Voer de volgende opdrachten uit:

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. Sluit de server `tmdown` af met de opdracht. De output ziet er ongeveer als volgt uit:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>ProSort installeren

ProSort is een hulpprogramma dat wordt gebruikt bij batchtransacties voor het sorteren van gegevens.

**ProSort installeren**

1. Zorg ervoor dat de Batch installatie succesvol is, en controleer dan of de **\_prosort-bin-prosort 2sp3-linux64-2123-opt.tar.gz** installer file aanwezig is.

2. Voer het installatieprogramma uit met behulp van het eigenschappenbestand. Typ het volgende na de opdrachtprompt:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Verplaats de prosort directory naar de thuislocatie. Typ het volgende na de opdrachtprompt:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Maak een licentiesubdirectory en kopieer daar het licentiebestand. Bijvoorbeeld:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Open bash.profile in`vi .bash_profile`vi ( ) en werk het als volgt bij:

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. Als u het bashprofiel wilt uitvoeren, typt u bij de opdrachtprompt:`. .bash_profile`

7. Maak het configuratiebestand. Bijvoorbeeld:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Maak de symbolische link. Bijvoorbeeld:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Controleer de ProSort-installatie `prosort -h` door de opdracht uit te voeren. Bijvoorbeeld:

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>Ofcobol installeren

OFCOBOL is de OpenFrame compiler die de COBOL-programma's van het mainframe interpreteert. 

**Ofcobol installeren**

1. Controleer of de batch/online-installatie is geslaagd en\_controleer\_of\_het\_\_installatiebestand\_OpenFrame COBOL3 0 40 Linux x86 64.bin aanwezig is.

2. Typ de opdracht:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Lees de licentieovereenkomst en druk op Enter om door te gaan.

4. Accepteer de licentieovereenkomst. Wanneer de installatie is voltooid, wordt het volgende weergegeven:

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Open het bashprofiel`vi .bash_profile`in vi ( ) en controleer of dit is bijgewerkt met OFCOBOL-variabelen.
6. Voer het bashprofiel uit. Typ het volgende na de opdrachtprompt:

     ```
      source ~/.bash_profile
     ```

7. Kopieer de OFCOBOL-licentie naar de geïnstalleerde map. Bijvoorbeeld:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Ga naar het configuratiebestand OpenFrame tjclrun.conf en open het in vi. Bijvoorbeeld:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Hier is de SYSLIB sectie voor de verandering:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Hier is de SYSLIB sectie na de verandering:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Controleer het\_Bestand\_OpenFrame COBOL InstallLog.log in vi en controleer of er geen fouten zijn. Bijvoorbeeld:
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. Gebruik `ofcob --version` de opdracht en controleer het versienummer om de installatie te verifiëren. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Start OpenFrame `tmdown/tmboot` opnieuw op met de opdracht.

## <a name="install-ofasm"></a>Ofasm installeren

OFASM is de OpenFrame compiler die de assemblerprogramma's van het mainframe interpreteert.

**Ofasm installeren**

1. Controleer of de batch/online-installatie is geslaagd en\_controleer\_of\_\_het installatiebestand OpenFrame ASM3 0 Linux x86\_64.bin aanwezig is.

2. Voer de installateur uit. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Lees de licentieovereenkomst en druk op Enter om door te gaan.
4. Accepteer de licentieovereenkomst.
5. Controleer of het bashprofiel is bijgewerkt met OFASM-variabelen. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Open het configuratiebestand OpenFrame tjclrun.conf in vi en bewerk het als volgt:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Hier is de [SYSLIB] sectie *voor* de wijziging:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Hier is de [SYSLIB] sectie *na* de wijziging:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Open het\_bestand\_OpenFrame ASM InstallLog.log in vi en controleer of er geen fouten zijn. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Start OpenFrame opnieuw op door een van de volgende opdrachten uit te geven:

     ```
     tmdown / tmboot
     ```

     —of —

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>OSC installeren

OSC is de OpenFrame-omgeving die vergelijkbaar is met IBM CICS die snelle OLTP-transacties en andere beheerfuncties ondersteunt.

**OSC installeren**

1. Controleer of de basisinstallatie is geslaagd en\_controleer\_\_of\_het\_installatiebestand\_OpenFrame OSC7 0 Fix2 Linux x86 64.bin en het configuratiebestand van OSC.Properties aanwezig zijn.
2. Bewerk de volgende parameters in het osc.properties-bestand:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Voer het installatieprogramma uit met het eigenschappenbestand zoals weergegeven:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Als het bericht 'Installatie voltooid' is voltooid, wordt het weergegeven.

4. Controleer of het bashprofiel wordt bijgewerkt met OSC-variabelen.
5. Bekijk het\_bestand\_OpenFrame OSC7 0\_Fix2\_InstallLog.log. Het ziet er ongeveer als volgt uit:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Gebruik vi om het configuratiebestand ofsys.seq te openen. Bijvoorbeeld:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. Bewerk \#in \#de secties BASE en BATCH de parameters zoals weergegeven.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Kopieer het licentiebestand. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Als u OSC wilt starten en afsluiten, initialiseert u `osctdlinit OSCOIVP1` het gedeelde geheugen van de CICS-regio door te typen op de opdrachtprompt.

10. Ren `oscboot` naar het opstarten van OSC. De output ziet er ongeveer als volgt uit:

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. Als u wilt controleren of de `tmadmin` processtatus gereed is, gebruikt u de opdracht in si. In alle processen moeten RDY in de **statuskolom** worden weergegeven.

    ![Processen met RDY](media/tmadmin-02.png)

12. Sluit OSC af `oscdown` met behulp van het commando.

## <a name="install-jeus"></a>JEUS installeren

JEUS (Java Enterprise User Solution) biedt de presentatielaag van de OpenFrame-webtoepassingsserver.

Installeer voor de installatie van JEUS het Apache Ant-pakket, dat de bibliotheken en opdrachtregelgereedschappen biedt die nodig zijn om JEUS te installeren.

**Apache Ant installeren**

1. Download Ant binary `wget` met behulp van de opdracht. Bijvoorbeeld:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Gebruik `tar` het hulpprogramma om het binaire bestand te extraheren en naar een geschikte locatie te verplaatsen. Bijvoorbeeld:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Maak voor efficiëntie een symbolische koppeling:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Open het bashprofiel`vi .bash_profile`in vi ( )en werk het bij met de volgende variabelen:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Pas de gewijzigde omgevingsvariabele toe. Bijvoorbeeld:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**JEUS installeren**

1. Vouw de installateur `tar` uit met behulp van het hulpprogramma. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Maak een **jeusmap** ( )`mkdir jeus7`en rits de binaire uit.
3. Wijzig in de **installatiemap** (of gebruik de PARAMETER JEUS voor uw eigen omgeving). Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Uitvoeren `ant clean-all` voordat u de build uitvoert. De output ziet er ongeveer als volgt uit:

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Maak een back-up van het bestand domain-config-template.properties. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Open het bestand domain-config-template.properties in vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Wijzigen `jeus.password=jeusadmin nodename=Tmaxsoft` in`jeus.password=tmax1234 nodename=ofdemo`

8. Voer `ant install` de opdracht uit om JEUS te bouwen.
9.  Werk het\_.bash-profielbestand bij met de JEUS-variabelen zoals weergegeven:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Voer het bashprofiel uit. Bijvoorbeeld:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Optioneel*. Maak een alias voor het eenvoudig afsluiten en opstarten van JEUS-componenten:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Als u de installatie wilt verifiëren, start u de domeinbeheerserver zoals weergegeven:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Controleer op webaanmelding met behulp van de syntaxis:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     <http://192.168.92.133:9736/webadmin/login.> Het aanmeldingsscherm wordt bijvoorbeeld weergegeven:
    
     ![Jeus WebAdmin-aanmeldingsscherm](media/jeus-01.png)

     > [!NOTE]
     > Als u problemen ondervindt met de beveiliging van de poort, opent u poort 9736 of schakelt u de firewall uit (`systemctl stop firewall`).

14. Als u de hostnaam voor server1 wilt wijzigen, klikt u op **Vergrendelen & Bewerken**en klikt u vervolgens op **server1**. Wijzig in het venster Server de hostnaam als volgt:

    1.  **Wijzig knooppuntnaam** in **ofdemo**.
    2.  Klik op **OK** aan de rechterkant van het venster.
    3.  Klik **op Wijzigingen toepassen** linksonder in het venster en voer voor beschrijving *hostnamewijziging in*.

    ![JEUS WebAdmin scherm](media/jeus-02.png)

15. Controleer of de configuratie is geslaagd in het bevestigingsscherm.

    ![jeus_domain-serverscherm](media/jeus-03.png)

16. Start het beheerde serverproces "server1" met de volgende opdracht:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Installeren OFGW

OFGW Is de OpenFrame gateway die de communicatie tussen de 3270 terminal emulator en de OSI basis ondersteunt en beheert de sessies tussen de terminal emulator en OSI.

**OfGW installeren**

1. Zorg ervoor dat JEUS is geïnstalleerd met succes,\_\_controleer vervolgens of het OFGW7\_0 1 Generic.bin installer bestand aanwezig is.
2. Voer de installateur uit. Bijvoorbeeld:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Gebruik de volgende locaties voor de bijbehorende prompts:
     -   JEUS-startpagina
     -   JEUS-domeinnaam
     -   Naam JEUS-server
     -   Tibero-chauffeur
     -   Tmax Node ID ofdemo

4. Accepteer de rest van de standaardinstellingen en druk op Enter om het installatieprogramma te verlaten.

5. Controleer of de URL voor OFGW werkt zoals verwacht:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Het volgende scherm wordt weergegeven:

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>OFManager installeren

OFManager biedt operationele en beheerfuncties voor OpenFrame in de webomgeving.

**OFManager installeren**

1. Controleer of het\_installatiebestand OFManager7 Generic.bin aanwezig is.
2. Voer de installateur uit. Bijvoorbeeld:

     ```
     OFManager7_Generic.bin
     ```

3.  Druk op Enter om door te gaan en accepteer vervolgens de licentieovereenkomst.
4.  Kies de installatiemap.
5.  Accepteer de standaardwaarden.
6.  Kies Tibero als database.
7.  Druk op Enter om de installatieprogramma af te sluiten.
8.  Controleer of de URL voor OFManager werkt zoals verwacht:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Het startscherm wordt weergegeven:

![Aanmeldingsscherm van Tmax OpenFrame Manager](media/ofmanager-01.png)

Daarmee wordt de installatie van de OpenFrame componenten voltooid.

## <a name="next-steps"></a>Volgende stappen

Als u een mainframemigratie overweegt, is ons groeiende partnerecosysteem beschikbaar om u te helpen. Voor gedetailleerde richtlijnen over het kiezen van een partneroplossing, verwijzen wij u naar de [Platform Modernization Alliance.](https://datamigration.microsoft.com/)

-   [Aan de slag met Azure](https://docs.microsoft.com/azure/)
-   [Documentatie van Host Integration Server (HIS)](https://docs.microsoft.com/host-integration-server/)
-   [Lift-and-Shift-handleiding voor Azure Virtual Data Center](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
