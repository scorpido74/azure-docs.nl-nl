---
title: Een hoofddoelserver voor Linux VM-failback installeren met Azure Site Recovery
description: Meer informatie over het instellen van een Linux-hoofddoelserver voor failback naar een on-premises site tijdens het herstel van VMware-VM's naar Azure met Azure Site Recovery.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: mayg
ms.openlocfilehash: 5b4d625d28584bb601905e9439c112c845219e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954380"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Een Linux-hoofddoelserver voor failback installeren
Nadat u uw virtuele machines niet meer hebt uitgevoerd in Azure, u de virtuele machines weer naar de on-premises site terugsturen. Als u niet terug wilt werken, moet u de virtuele machine opnieuw beveiligen van Azure naar de on-premises site. Voor dit proces hebt u een on-premises hoofddoelserver nodig om het verkeer te ontvangen. 

Als uw beveiligde virtuele machine een virtuele Windows-machine is, hebt u een Windows-hoofddoel nodig. Voor een Linux virtuele machine heb je een Linux master target nodig. Lees de volgende stappen om te leren hoe u een Linux-mastertarget maken en installeren.

> [!IMPORTANT]
> Vanaf de release van de 9.10.0 master target server kan de nieuwste master target server alleen worden geïnstalleerd op een Ubuntu 16.04 server. Nieuwe installaties zijn niet toegestaan op CentOS6.6-servers. U uw oude hoofddoelservers echter blijven upgraden met behulp van de 9.10.0-versie.
> Hoofddoelserver op LVM wordt niet ondersteund.

## <a name="overview"></a>Overzicht
Dit artikel bevat instructies voor het installeren van een Linux master target.

Plaats opmerkingen of vragen aan het einde van dit artikel of op het [Azure Recovery Services Forum.](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="prerequisites"></a>Vereisten

* Als u de host wilt kiezen waarop het hoofddoel moet worden geïmplementeerd, bepaalt u of de failback naar een bestaande on-premises virtuele machine of naar een nieuwe virtuele machine gaat. 
    * Voor een bestaande virtuele machine moet de host van het hoofddoel toegang hebben tot de gegevensopslag van de virtuele machine.
    * Als de on-premises virtuele machine niet bestaat (in het geval van alternatieve locatieherstel), wordt de failback virtuele machine gemaakt op dezelfde host als het hoofddoel. U elke ESXi-host kiezen om het hoofddoel te installeren.
* Het hoofddoel moet zich bevinden op een netwerk dat kan communiceren met de processerver en de configuratieserver.
* De versie van het hoofddoel moet gelijk zijn aan of eerder dan de versies van de processerver en de configuratieserver. Als de versie van de configuratieserver bijvoorbeeld 9.4 is, kan de versie van het hoofddoel 9.4 of 9.3 zijn, maar niet 9,5.
* Het hoofddoel kan alleen een Virtuele VMware-machine zijn en geen fysieke server.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Richtlijnen voor het aanpassen van waarden voor het maken van hoofddoelserver

Maak het hoofddoel volgens de volgende richtlijnen voor maatgrootte:
- **RAM**: 6 GB of meer
- **OS-schijfgrootte:** 100 GB of meer (om besturingssysteem te installeren)
- **Extra schijfgrootte voor bewaarstation:** 1 TB
- **CPU-cores**: 4 cores of meer

De volgende Ubuntu kernels worden ondersteund.


|Kernel-serie  |Ondersteuning tot  |
|---------|---------|
|4.4      |4.4.0-81-generiek         |
|4.8      |4.8.0-56-generiek         |
|4.10     |4.10.0-24-generiek        |


## <a name="deploy-the-master-target-server"></a>De hoofddoelserver implementeren

### <a name="install-ubuntu-16042-minimal"></a>Installeer Ubuntu 16.04.2 Minimaal

Neem de volgende stappen om het Ubuntu 16.04.2 64-bits besturingssysteem te installeren.

1.   Ga naar de [downloadlink,](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso)kies de dichtstbijzijnde spiegel en download een Ubuntu 16.04.2 minimale 64-bits ISO.
Bewaar een Ubuntu 16.04.2 minimale 64-bits ISO in het dvd-station en start het systeem.

1.  Selecteer **Engels** als uw voorkeurstaal en selecteer **Enter**.
    
    ![Een taal selecteren](./media/vmware-azure-install-linux-master-target/image1.png)
1. Selecteer **Ubuntu-server installeren**en selecteer **Enter**.

    ![Ubuntu-server installeren selecteren](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Selecteer **Engels** als uw voorkeurstaal en selecteer **Enter**.

    ![Selecteer Engels als uw voorkeurstaal](./media/vmware-azure-install-linux-master-target/image3.png)

1. Selecteer de juiste optie in de lijst **Tijdzone-opties** en selecteer **Enter**.

    ![De juiste tijdzone selecteren](./media/vmware-azure-install-linux-master-target/image4.png)

1. Selecteer **Nee** (de standaardoptie) en selecteer **Enter**.

     ![Het toetsenbord configureren](./media/vmware-azure-install-linux-master-target/image5.png)
1. Selecteer **Engels (VS)** als land/regio van herkomst voor het toetsenbord en selecteer **Enter**.

1. Selecteer **Engels (VS)** als de toetsenbordindeling en selecteer **Enter**.

1. Voer de hostnaam voor uw server in het vak **Hostname** in en selecteer **Doorgaan**.

1. Als u een gebruikersaccount wilt maken, voert u de gebruikersnaam in en selecteert u **Doorgaan**.

      ![Een gebruikersaccount maken](./media/vmware-azure-install-linux-master-target/image9.png)

1. Voer het wachtwoord voor het nieuwe gebruikersaccount in en selecteer **Doorgaan**.

1.  Bevestig het wachtwoord voor de nieuwe gebruiker en selecteer **Doorgaan**.

    ![De wachtwoorden bevestigen](./media/vmware-azure-install-linux-master-target/image11.png)

1.  Selecteer in de volgende selectie voor het versleutelen van uw startmap **Nee** (de standaardoptie) en selecteer **Enter**.

1. Als de weergegeven tijdzone juist is, selecteert u **Ja** (de standaardoptie) en selecteert u **Enter**. Als u uw tijdzone opnieuw wilt configureren, selecteert u **Nee**.

1. Selecteer in de opties voor de partitiemethode De optie **Geleid - gebruik de hele schijf**en selecteer **Enter**.

     ![De optie scheidingsmethode selecteren](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Selecteer de juiste schijf in de **opties Schijf selecteren tot partitie** en selecteer **Enter**.

    ![De schijf selecteren](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Selecteer **Ja** om de wijzigingen op schijf te schrijven en selecteer **Enter**.

    ![De standaardoptie selecteren](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  Selecteer in de selectie van de proxy configureren de standaardoptie, selecteer **Doorgaan**en selecteer **Enter**.
     
     ![Selecteer hoe u upgrades beheert](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Selecteer **geen automatische updates** optie in de selectie voor het beheren van upgrades op uw systeem en selecteer **Enter**.

     ![Selecteer hoe u upgrades beheert](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Omdat de Azure Site Recovery master target server een zeer specifieke versie van de Ubuntu vereist, moet u ervoor zorgen dat de kernel upgrades zijn uitgeschakeld voor de virtuele machine. Als ze zijn ingeschakeld, zorgen eventuele regelmatige upgrades ervoor dat de hoofddoelserver defect raakt. Zorg ervoor dat u de optie **Geen automatische updates** selecteert.

1.  Selecteer standaardopties. Als u OpenSSH voor SSH connect wilt openen, selecteert u de **openssh-serveroptie** en selecteert u **Doorgaan**.

    ![Software selecteren](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. Selecteer **Ja**en selecteer **Enter**in de selectie voor het installeren van de GRUB-opstartlader .
     
    ![GRUB boot installer](./media/vmware-azure-install-linux-master-target/image20.png)


1. Selecteer het juiste apparaat voor de installatie van de opstartlader (bij voorkeur **/dev/sda)** en selecteer **Enter**.
     
    ![Geschikt apparaat selecteren](./media/vmware-azure-install-linux-master-target/image21.png)

1. Selecteer **Doorgaan**en selecteer **Enter** om de installatie te voltooien.

    ![De installatie voltooien](./media/vmware-azure-install-linux-master-target/image22.png)

1. Nadat de installatie is voltooid, meldt u zich aan bij de VM met de nieuwe gebruikersreferenties. (Raadpleeg **stap 10** voor meer informatie.)

1. Gebruik de stappen die in de volgende schermafbeelding worden beschreven om het root-gebruikerswachtwoord in te stellen. Meld u dan aan als ROOT-gebruiker.

    ![Het root-gebruikerswachtwoord instellen](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>De machine configureren als hoofddoelserver

Om de ID voor elke SCSI-harde schijf in een Virtuele Linux-machine, de schijf, te **krijgen. De parameter EnableUUID = TRUE** moet worden ingeschakeld. Ga als volgt te werk om deze parameter in te schakelen:

1. Sluit je virtuele machine af.

2. Klik met de rechtermuisknop op het item voor de virtuele machine in het linkerdeelvenster en selecteer **Instellingen bewerken**.

3. Selecteer het tabblad **Opties.**

4. Selecteer **in** > het linkerdeelvenster Geavanceerd**algemeen**en selecteer vervolgens de knop **Configuratieparameters** rechtsonder in het scherm.

    ![Parameter Configuratie openen](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    De optie **Configuratieparameters** is niet beschikbaar wanneer de machine actief is. Als u dit tabblad actief wilt maken, schakelt u de virtuele machine uit.

5. Kijk of een rij met **schijf. EnableUUID** bestaat al.

   - Als de waarde bestaat en is ingesteld op **Onwaar,** wijzigt u de waarde in **True**. (De waarden zijn niet hoofdlettergevoelig.)

   - Als de waarde bestaat en is ingesteld op **True,** selecteert u **Annuleren**.

   - Als de waarde niet bestaat, selecteert u **Rij toevoegen**.

   - Voeg in de naamkolom **schijf toe. Schakel UUID in**en stel de waarde in op **TRUE**.

     ![Controleren of schijf. EnableUUID bestaat al](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Kernel-upgrades uitschakelen

Azure Site Recovery master target server vereist een specifieke versie van de Ubuntu, ervoor te zorgen dat de kernel upgrades zijn uitgeschakeld voor de virtuele machine. Als kernelupgrades zijn ingeschakeld, kan dit ertoe leiden dat de hoofddoelserver defect raakt.

#### <a name="download-and-install-additional-packages"></a>Aanvullende pakketten downloaden en installeren

> [!NOTE]
> Zorg ervoor dat u een internetverbinding hebt om aanvullende pakketten te downloaden en te installeren. Als u geen internetverbinding hebt, moet u deze Deb-pakketten handmatig vinden en installeren.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Het installatieprogramma instellen

Als uw hoofddoel een internetverbinding heeft, u de volgende stappen gebruiken om het installatieprogramma te downloaden. Anders u het installatieprogramma van de processerver kopiëren en vervolgens installeren.

#### <a name="download-the-master-target-installation-packages"></a>Download de master target installatiepakketten

[Download de nieuwste Linux master target installatie bits.](https://aka.ms/latestlinuxmobsvc)

Als u het wilt downloaden met Linux, typt u het:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Zorg ervoor dat u het installatieprogramma in uw thuismap downloadt en uitritst. Als u uitpakt naar **/usr/Local,** dan mislukt de installatie.


#### <a name="access-the-installer-from-the-process-server"></a>Toegang tot het installatieprogramma vanaf de processerver

1. Ga op de processerver naar **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Kopieer het vereiste installatiebestand van de processerver en sla het op als **nieuwstelinuxmobsvc.tar.gz** in uw thuismap.


### <a name="apply-custom-configuration-changes"></a>Aangepaste configuratiewijzigingen toepassen

Als u aangepaste configuratiewijzigingen wilt toepassen, gebruikt u de volgende stappen als ROOT-gebruiker:

1. Voer de volgende opdracht uit om de binaire niet-tarte uit te voeren.

    `tar -xvf latestlinuxmobsvc.tar.gz`

    ![Schermafbeelding van de opdracht die moet worden uitgevoerd](./media/vmware-azure-install-linux-master-target/image16.png)

2. Voer de volgende opdracht uit om toestemming te geven.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Voer de volgende opdracht uit om het script uit te voeren.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Voer het script slechts één keer uit op de server. Sluit vervolgens de server af. Start de server opnieuw nadat u een schijf hebt toegevoegd, zoals beschreven in de volgende sectie.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Een bewaarschijf toevoegen aan de virtuele machine van Linux master target

Gebruik de volgende stappen om een bewaarschijf te maken:

1. Bevestig een nieuwe 1-TB schijf aan de Linux master target virtuele machine, en start de machine.

2. Gebruik de opdracht **multipath -ll** om de multipath-id van de bewaarschijf te leren: **multipath -ll**

    ![Multipath-id](./media/vmware-azure-install-linux-master-target/image27.png)

3. Maak het station op en maak vervolgens een bestandssysteem op het nieuwe station: **mkfs.ext4 /dev/mapper/\<Retention disk's multipath id>**.
    
    ![Bestandssysteem](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Nadat u het bestandssysteem hebt gemaakt, monteert u de bewaarschijf.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Maak de **fstab-vermelding** om het bewaarstation te monteren elke keer dat het systeem wordt gestart.
    
    `vi /etc/fstab`
    
    Selecteer **Invoegen** om te beginnen met het bewerken van het bestand. Maak een nieuwe regel en voeg de volgende tekst in. Bewerk de schijfmultipath-id op basis van de gemarkeerde multipath-id van de vorige opdracht.

    **/dev/mapper/\<Retentieschijven multipath id> /mnt/retention ext4 rw 0 0**

    Selecteer **Esc**en typ **:wq** (schrijven en afsluiten) om het editorvenster te sluiten.

### <a name="install-the-master-target"></a>Het hoofddoel installeren

> [!IMPORTANT]
> De versie van de hoofddoelserver moet gelijk zijn aan of eerder dan de versies van de processerver en de configuratieserver. Als niet aan deze voorwaarde is voldaan, wordt opnieuw beschermd, maar mislukt replicatie.


> [!NOTE]
> Controleer voordat u de hoofddoelserver installeert of het **bestand /etc/hosts** op de virtuele machine vermeldingen bevat die de lokale hostnaam toewijzen aan de IP-adressen die zijn gekoppeld aan alle netwerkadapters.

1. Kopieer de wachtwoordzin van **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** op de configuratieserver. Sla het vervolgens op als **wachtwoordzin.txt** in dezelfde lokale map door de volgende opdracht uit te voeren:

    `echo <passphrase> >passphrase.txt`

    Voorbeeld: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Noteer het IP-adres van de configuratieserver. Voer de volgende opdracht uit om de hoofddoelserver te installeren en de server te registreren bij de configuratieserver.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Voorbeeld: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Wacht tot het script is afgelopen. Als het hoofddoel zich registreert, wordt het hoofddoel weergegeven op de pagina **Site Herstelinfrastructuur** van de portal.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Het hoofddoel installeren met behulp van interactieve installatie

1. Voer de volgende opdracht uit om het hoofddoel te installeren. Kies voor de agentrol **het hoofddoel**.

    ```
    ./install
    ```

2. Kies de standaardlocatie voor installatie en selecteer **Enter** om door te gaan.

    ![Een standaardlocatie kiezen voor de installatie van hoofddoel](./media/vmware-azure-install-linux-master-target/image17.png)

Nadat de installatie is voltooid, registreert u de configuratieserver met behulp van de opdrachtregel.

1. Let op het IP-adres van de configuratieserver. Je hebt het nodig in de volgende stap.

2. Voer de volgende opdracht uit om de hoofddoelserver te installeren en de server te registreren bij de configuratieserver.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Voorbeeld: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Wacht tot het script is afgelopen. Als het hoofddoel is geregistreerd, wordt het hoofddoel weergegeven op de pagina **Site Herstelinfrastructuur** van de portal.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>VMware-hulpprogramma's / open-vm-tools installeren op de hoofddoelserver

U moet VMware-tools of open-vm-tools op het hoofddoel installeren, zodat het de gegevensopslag kan ontdekken. Als de hulpprogramma's niet zijn geïnstalleerd, wordt het scherm opnieuw beveiligen niet weergegeven in de gegevensarchieven. Na de installatie van de VMware-tools moet u opnieuw worden opgestart.

### <a name="upgrade-the-master-target-server"></a>De hoofddoelserver bijwerken

Voer het installatieprogramma uit. Het detecteert automatisch dat de agent is geïnstalleerd op het hoofddoel. Als u wilt upgraden, selecteert u **Y**.  Nadat de installatie is voltooid, controleert u de versie van het hoofddoel dat is geïnstalleerd met behulp van de volgende opdracht:

`cat /usr/local/.vx_version`


U ziet dat het veld **Versie** het versienummer van het hoofddoel geeft.

## <a name="common-issues"></a>Algemene problemen

* Zorg ervoor dat u Storage vMotion niet inschakelt op beheeronderdelen zoals een hoofddoel. Als het hoofddoel wordt verplaatst na een succesvolle herbescherming, kunnen de virtuele machineschijven (VMDKs) niet worden losgekoppeld. In dit geval mislukt failback.

* Het hoofddoel mag geen momentopnamen op de virtuele machine hebben. Als er momentopnamen zijn, mislukt failback.

* Vanwege een aantal aangepaste NIC-configuraties is de netwerkinterface uitgeschakeld tijdens het opstarten en kan de hoofddoelagent niet initialiseren. Zorg ervoor dat de volgende eigenschappen correct zijn ingesteld. Controleer deze eigenschappen in de /etc/sysconfig/network-scripts/ifcfg-eth*van het Ethernet-kaartbestand.
    * BOOTPROTO=DHCP
    * ONBOOT=Ja


## <a name="next-steps"></a>Volgende stappen
Nadat de installatie en registratie van het hoofddoel is voltooid, u het hoofddoel zien verschijnen op de **hoofddoelsectie** in **Site Recovery Infrastructure**, onder het overzicht van de configuratieserver.

U nu doorgaan met [herbescherming,](vmware-azure-reprotect.md)gevolgd door failback.

