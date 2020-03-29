---
title: MPIO configureren op StorSimple Linux-host
description: MPIO configureren op StorSimple die is aangesloten op een Linux-host met CentOS 6.6
author: alkohli
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: 5dadd231335e93839e947077168f32dbfe96eb45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278364"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>MPIO configureren op een StorSimple-host met CentOS
In dit artikel worden de stappen uitgelegd die nodig zijn om Multipathing IO (MPIO) te configureren op uw Centos 6.6-hostserver. De hostserver is verbonden met uw Microsoft Azure StorSimple-apparaat voor hoge beschikbaarheid via iSCSI-initiators. Het beschrijft in detail de automatische ontdekking van multipath apparaten en de specifieke setup alleen voor StorSimple volumes.

Deze procedure is van toepassing op alle modellen van StorSimple 8000-serie apparaten.

> [!NOTE]
> Deze procedure kan niet worden gebruikt voor een StorSimple Cloud Appliance. Zie voor meer informatie hoe u hostservers voor uw cloudtoestel configureert.


## <a name="about-multipathing"></a>Over multipathing
Met de multipathing-functie u meerdere I/O-paden configureren tussen een hostserver en een opslagapparaat. Deze I/O-paden zijn fysieke SAN-verbindingen die afzonderlijke kabels, switches, netwerkinterfaces en controllers kunnen bevatten. Multipathing verzamelt de I/O-paden om een nieuw apparaat te configureren dat is gekoppeld aan alle geaggregeerde paden.

Het doel van multipathing is tweeledig:

* **Hoge beschikbaarheid**: het biedt een alternatief pad als een element van het I/O-pad (zoals een kabel, switch, netwerkinterface of controller) uitvalt.
* **Load balancing**: Afhankelijk van de configuratie van uw opslagapparaat kan het de prestaties verbeteren door belastingen op de I/O-paden te detecteren en deze belastingen dynamisch opnieuw in evenwicht te brengen.

### <a name="about-multipathing-components"></a>Over multipathing componenten
Multipathing in Linux bestaat uit kernel componenten en user-space componenten zoals hieronder getabuleerd.

* **Kernel:** De belangrijkste component is de *apparaatmapper* die I/O omleidt en failover voor paden en padgroepen ondersteunt.

* **Gebruikersruimte:** dit zijn *multipath-tools* die multipathed devices beheren door de multipathmodule van het apparaat mapper te instrueren wat te doen. De gereedschappen bestaan uit:
   
   * **Multipath:** geeft meerpathige apparaten weer en configureert deze.
   * **Multipathd**: daemon die multipath uitvoert en de paden bewaakt.
   * **Devmap-naam**: biedt een zinvolle apparaatnaam aan udev voor devmaps.
   * **Kpartx**: brengt lineaire devmaps in kaart met apparaatpartities om multipath-kaarten partitionerbaar te maken.
   * **Multipath.conf**: configuratiebestand voor multipath daemon dat wordt gebruikt om de ingebouwde configuratietabel te overschrijven.

### <a name="about-the-multipathconf-configuration-file"></a>Over het configuratiebestand multipath.conf
Het configuratiebestand `/etc/multipath.conf` maakt veel van de multipathing-functies gebruikersconfigureerbaar. De `multipath` opdracht en `multipathd` de kernel daemon gebruiken informatie in dit bestand. Het bestand wordt alleen geraadpleegd tijdens de configuratie van de multipath-apparaten. Zorg ervoor dat alle wijzigingen worden `multipath` aangebracht voordat u de opdracht uitvoert. Als u het bestand daarna wijzigt, moet u stoppen en opnieuw beginnen met multipathen om de wijzigingen van kracht te laten worden.

De multipath.conf bestaat uit vijf secties:

- **Standaardinstellingen op systeemniveau** *(standaardinstellingen):* u standaardinstellingen op systeemniveau overschrijven.
- **Apparaten op de zwarte lijst** *(zwarte lijst)*: U de lijst opgeven met apparaten die niet door apparaatmapper moeten worden bestuurd.
- **Uitzonderingen op de zwarte lijst** *(blacklist_exceptions)*: U specifieke apparaten identificeren die als multipath-apparaten moeten worden behandeld, zelfs als deze op de zwarte lijst worden vermeld.
- **Specifieke instellingen voor opslagcontroller** *(apparaten):* U configuratie-instellingen opgeven die worden toegepast op apparaten met leveranciers- en productgegevens.
- **Apparaatspecifieke instellingen** *(multipaths):* U deze sectie gebruiken om de configuratie-instellingen voor afzonderlijke LUN's te verfijnen.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Multipathing configureren op StorSimple die is verbonden met Linux-host
Een StorSimple-apparaat dat is aangesloten op een Linux-host kan worden geconfigureerd voor hoge beschikbaarheid en taakverdeling. Als de Linux-host bijvoorbeeld twee interfaces heeft die zijn aangesloten op het SAN en het apparaat twee interfaces heeft die zijn aangesloten op het SAN, zodat deze interfaces zich op hetzelfde subnet bevinden, zijn er 4 paden beschikbaar. Als elke DATA-interface op het apparaat en de hostinterface zich echter op een ander IP-subnet bevinden (en niet routeerbaar), zijn er slechts 2 paden beschikbaar. U multipathing configureren om automatisch alle beschikbare paden te ontdekken, een load-balancing-algoritme voor die paden te kiezen, specifieke configuratie-instellingen toe te passen voor alleen StorSimple-volumes en vervolgens multipathing in- en te verifiëren.

In de volgende procedure wordt beschreven hoe u multipathing configureert wanneer een StorSimple-apparaat met twee netwerkinterfaces is verbonden met een host met twee netwerkinterfaces.

## <a name="prerequisites"></a>Vereisten
In dit gedeelte worden de configuratievereisten voor CentOS-server en uw StorSimple-apparaat beschreven.

### <a name="on-centos-host"></a>Op CentOS-host
1. Zorg ervoor dat uw CentOS-host twee netwerkinterfaces heeft ingeschakeld. Type:
   
    `ifconfig`
   
    In het volgende voorbeeld wordt de`eth0` `eth1`uitvoer weergegeven wanneer er twee netwerkinterfaces (en ) op de host aanwezig zijn.
   
        [root@centosSS ~]# ifconfig
        eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
          inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
         RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)
   
        eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
          inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
          TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)
   
        loLink encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:12 errors:0 dropped:0 overruns:0 frame:0
          TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)
1. Installeer *iSCSI-initiator-utils* op uw CentOS-server. Voer de volgende stappen uit om *iSCSI-initiator-utils te*installeren.
   
   1. Meld je `root` aan bij je CentOS-host.
   1. Installeer de *iSCSI-initiator-utils*. Type:
      
       `yum install iscsi-initiator-utils`
   1. Nadat de *iSCSI-Initiator-utils* is geïnstalleerd, start u de iSCSI-service. Type:
      
       `service iscsid start`
      
       Soms, `iscsid` kan niet daadwerkelijk `--force` beginnen en de optie kan nodig zijn
   1. Gebruik de opdracht om de `chkconfig` service in te schakelen om ervoor te zorgen dat uw iSCSI-initiator tijdens het opstarten is ingeschakeld.
      
       `chkconfig iscsi on`
   1. Voer de opdracht uit om te controleren of deze correct is ingesteld:
      
       `chkconfig --list | grep iscsi`
      
       Hieronder ziet u een voorbeeld van de uitvoer.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       In het bovenstaande voorbeeld u zien dat uw iSCSI-omgeving wordt uitgevoerd op opstarttijd op run-niveaus 2, 3, 4 en 5.
1. Installeer *apparaat-mapper-multipath*. Type:
   
    `yum install device-mapper-multipath`
   
    De installatie start. Type **Y** om door te gaan wanneer om bevestiging wordt gevraagd.

### <a name="on-storsimple-device"></a>Op StorSimple-apparaat
Uw StorSimple-apparaat moet beschikken over:

* Minimaal twee interfaces ingeschakeld voor iSCSI. Voer de volgende stappen uit in de klassieke Azure-portal voor uw StorSimple-apparaat om te controleren of twee interfaces op uw StorSimple-apparaat zijn ingeschakeld:
  
  1. Log in op de klassieke portal voor uw StorSimple-apparaat.
  1. Selecteer uw StorSimple Manager-service, klik op **Apparaten** en kies het specifieke StorSimple-apparaat. Klik **op De** instellingen van de netwerkinterface configureren en verifiëren. Hieronder vindt u een schermafbeelding met twee netwerkinterfaces met iSCSI-interface. Hier data 2 en DATA 3 zijn beide 10 GbE-interfaces ingeschakeld voor iSCSI.
     
      ![MPIO StorsSimple DATA 2 config](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO StorSimple DATA 3 Config](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      Op de pagina **Configureren**
     
     1. Zorg ervoor dat beide netwerkinterfaces iSCSI-enabled zijn. Het veld **met iSCSI-ingeschakeld** moet worden ingesteld op **Ja**.
     1. Zorg ervoor dat de netwerkinterfaces dezelfde snelheid hebben, beide moeten 1 GbE of 10 GbE zijn.
     1. Let op de IPv4-adressen van de iSCSI-interfaces en sla op voor later gebruik op de host.
* De iSCSI-interfaces op uw StorSimple-apparaat moeten bereikbaar zijn vanaf de CentOS-server.
      Om dit te verifiëren, moet u de IP-adressen van uw StorSimple iSCSI-netwerkinterfaces op uw hostserver opgeven. De gebruikte opdrachten en de bijbehorende uitvoer met DATA2 (10.126.162.25) en DATA3 (10.126.162.26) worden hieronder weergegeven:
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>Hardwareconfiguratie
We raden u aan de twee iSCSI-netwerkinterfaces op afzonderlijke paden aan te sluiten voor redundantie. De onderstaande afbeelding toont de aanbevolen hardwareconfiguratie voor hoge beschikbaarheid en load-balancing multipathing voor uw CentOS-server en StorSimple-apparaat.

![MPIO hardware config voor StorSimple naar Linux host](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Zoals in het voorgaande cijfer wordt aangegeven:

* Uw StorSimple-apparaat bevindt zich in een actief-passieve configuratie met twee controllers.
* Er zijn twee SAN-switches aangesloten op de controllers van uw apparaat.
* Twee iSCSI-initiators zijn ingeschakeld op uw StorSimple-apparaat.
* Twee netwerkinterfaces zijn ingeschakeld op uw CentOS-host.

De bovenstaande configuratie levert 4 afzonderlijke paden op tussen uw apparaat en de host als de host- en gegevensinterfaces routeerbaar zijn.

> [!IMPORTANT]
> * Wij raden u aan geen 1 GbE- en 10 GbE-netwerkinterfaces te mixen voor multipathing. Bij het gebruik van twee netwerkinterfaces moeten beide interfaces hetzelfde type zijn.
> * Op uw StorSimple-apparaat, DATA0, DATA1, DATA4 en DATA5 zijn 1 GbE-interfaces, terwijl DATA2 en DATA3 10 GbE-netwerkinterfaces zijn.|
> 
> 

## <a name="configuration-steps"></a>Configuratiestappen
De configuratiestappen voor multipathing omvatten het configureren van de beschikbare paden voor automatische detectie, het opgeven van het te gebruiken algoritme voor het balanceren van de lasten, het inschakelen van multipathing en het uiteindelijk verifiëren van de configuratie. Elk van deze stappen wordt in detail besproken in de volgende secties.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Stap 1: Multipathing configureren voor automatische detectie
De multipath-ondersteunde apparaten kunnen automatisch worden gedetecteerd en geconfigureerd.

1. Bestand initialiseren. `/etc/multipath.conf` Type:
   
     `mpathconf --enable`
   
    De bovenstaande opdracht `sample/etc/multipath.conf` maakt een bestand.
1. Multipath-service starten. Type:
   
    `service multipathd start`
   
    U ziet de volgende uitvoer:
   
    `Starting multipathd daemon:`
1. Automatische detectie van meerdere paden inschakelen. Type:
   
    `mpathconf --find_multipaths y`
   
    Hiermee wordt het gedeelte standaardwaarden van uw `multipath.conf` gedeelte gewijzigd, zoals hieronder wordt weergegeven:
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Stap 2: Multipathing configureren voor StorSimple-volumes
Standaard staan alle apparaten in het bestand multipath.conf en worden ze omzeild. U moet uitzonderingen op de zwarte lijst maken om multipathing mogelijk te maken voor volumes van StorSimple-apparaten.

1. Bewerk `/etc/mulitpath.conf` het bestand. Type:
   
    `vi /etc/multipath.conf`
1. Zoek de sectie blacklist_exceptions in het bestand multipath.conf. Uw StorSimple-apparaat moet in deze sectie worden vermeld als een uitzondering op de zwarte lijst. U de relevante regels in dit bestand ongedaan maken om het te wijzigen zoals hieronder wordt weergegeven (gebruik alleen het specifieke model van het apparaat dat u gebruikt):
   
        blacklist_exceptions {
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8100*"
            }
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8600*"
            }
           }

### <a name="step-3-configure-round-robin-multipathing"></a>Stap 3: Round-robin multipathing configureren
Dit load-balancing algoritme maakt gebruik van alle beschikbare multipaths naar de actieve controller in een evenwichtige, round-robin mode.

1. Bewerk `/etc/multipath.conf` het bestand. Type:
   
    `vi /etc/multipath.conf`
1. Stel `defaults` onder de `path_grouping_policy` sectie `multibus`de . Hiermee `path_grouping_policy` geeft u het standaardpadgroeperingsbeleid op dat moet worden toegepast op niet-gespecificeerde multipaden. De sectie standaardwaarden ziet er zoals hieronder wordt weergegeven.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> De meest voorkomende `path_grouping_policy` waarden van zijn:
> 
> * failover = 1 pad per prioriteitsgroep
> * multibus = alle geldige paden in 1 prioriteitsgroep
> 
> 

### <a name="step-4-enable-multipathing"></a>Stap 4: Multipathing inschakelen
1. Start `multipathd` de daemon opnieuw op. Type:
   
    `service multipathd restart`
1. De output zal worden weergegeven hieronder:
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>Stap 5: Multipathing verifiëren
1. Zorg er eerst voor dat de iSCSI-verbinding met het StorSimple-apparaat als volgt tot stand wordt gebracht:
   
   a. Ontdek uw StorSimple-apparaat. Type:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    De uitvoer wanneer ip-adres voor DATA0 10.126.162.25 is en poort 3260 wordt geopend op het StorSimple-apparaat voor uitgaand iSCSI-verkeer is zoals hieronder weergegeven:
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Kopieer de IQN van uw `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`StorSimple-apparaat, van de vorige uitvoer.

   b. Maak verbinding met het apparaat met behulp van doel IQN. Het StorSimple-apparaat is het iSCSI-doelwit hier. Type:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    In het volgende voorbeeld ziet u `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`de uitvoer met een doel-IQN van . De uitvoer geeft aan dat u met succes verbinding hebt gemaakt met de twee iSCSI-netwerkinterfaces op uw apparaat.

    ```
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    Als u hier slechts één hostinterface en twee paden ziet, moet u beide interfaces op host voor iSCSI inschakelen. U de [gedetailleerde instructies in Linux documentatie](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html)volgen.

1. Een volume wordt blootgesteld aan de CentOS-server vanaf het StorSimple-apparaat. Zie [Stap 6: Een volume maken](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) via de Azure-portal op uw StorSimple-apparaat voor meer informatie.

1. Controleer de beschikbare paden. Type:

      ```
      multipath -l
      ```

      In het volgende voorbeeld wordt de uitvoer weergegeven voor twee netwerkinterfaces op een StorSimple-apparaat dat is aangesloten op één hostnetwerkinterface met twee beschikbare paden.

        ```
        mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 7:0:0:1 sdc 8:32 active undef running
        `- 6:0:0:1 sdd 8:48 active undef running
        ```

        The following example shows the output for two network interfaces on a StorSimple device connected to two host network interfaces with four available paths.

        ```
        mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 17:0:0:0 sdb 8:16 active undef running
        |- 15:0:0:0 sdd 8:48 active undef running
        |- 14:0:0:0 sdc 8:32 active undef running
        `- 16:0:0:0 sde 8:64 active undef running
        ```

        After the paths are configured, refer to the specific instructions on your host operating system (Centos 6.6) to mount and format this volume.

## <a name="troubleshoot-multipathing"></a>Problemen met multipathing oplossen
In dit gedeelte vindt u enkele handige tips als u problemen ondervindt tijdens de multipathing-configuratie.

V. Ik zie niet de `multipath.conf` wijzigingen in het bestand van kracht.

A. Als u wijzigingen in `multipath.conf` het bestand hebt aangebracht, moet u de multipathingservice opnieuw starten. Typ de volgende opdracht:

    service multipathd restart

V. Ik heb twee netwerkinterfaces ingeschakeld op het StorSimple-apparaat en twee netwerkinterfaces op de host. Als ik de beschikbare paden opmaak, zie ik slechts twee paden. Ik verwachtte vier beschikbare paden te zien.

A. Zorg ervoor dat de twee paden op hetzelfde subnet en routebaar zijn. Als de netwerkinterfaces zich op verschillende vLAN's bevinden en niet routeerbaar zijn, ziet u slechts twee paden. Een manier om dit te verifiëren is om ervoor te zorgen dat u beide host-interfaces bereiken vanuit een netwerkinterface op het StorSimple-apparaat. U moet contact opnemen [met Microsoft Support,](storsimple-8000-contact-microsoft-support.md) omdat deze verificatie alleen kan worden uitgevoerd via een ondersteuningssessie.

V. Wanneer ik beschikbare paden vermeld, zie ik geen uitvoer.

A. Meestal, niet zien van een multipathed paden suggereert een probleem met de multipathing daemon, en het is zeer waarschijnlijk dat een probleem hier ligt in het `multipath.conf` bestand.

Het zou ook de moeite waard zijn om te controleren of je daadwerkelijk zien sommige schijven na het aansluiten op het doel, als geen reactie van de multipath aanbiedingen kan ook betekenen dat je geen schijven.

* Gebruik de volgende opdracht om de SCSI-bus opnieuw te scannen:
  
    `$ rescan-scsi-bus.sh`(onderdeel van sg3_utils pakket)
* Typ de volgende opdrachten:
  
    `$ dmesg | grep sd*`
     
     of
  
    `$ fdisk -l`
  
    Deze zullen details van onlangs toegevoegde schijven retourneren.
* Gebruik de volgende opdrachten om te bepalen of het een StorSimple-schijf is:
  
    `cat /sys/block/<DISK>/device/model`
  
    Dit zal een string retourneren, die zal bepalen of het een StorSimple schijf.

Een minder waarschijnlijke, maar mogelijke oorzaak kan ook muf iscsid pid. Gebruik de volgende opdracht om u af te melden voor de iSCSI-sessies:

    iscsiadm -m node --logout -p <Target_IP>

Herhaal deze opdracht voor alle verbonden netwerkinterfaces op het iSCSI-doel, dat is uw StorSimple-apparaat. Zodra u zich hebt afgemeld voor alle iSCSI-sessies, gebruikt u het iSCSI-doel IQN om de iSCSI-sessie opnieuw in te stellen. Typ de volgende opdracht:

    iscsiadm -m node --login -T <TARGET_IQN>


V. Ik weet niet zeker of mijn apparaat op de witte lijst staat.

A. Als u wilt controleren of uw apparaat op de witte lijst staat, gebruikt u de volgende interactieve opdracht voor het oplossen van problemen:

    multipathd -k
    multipathd> show devices
    available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored


Ga voor meer informatie naar [probleemoplossing voor multipathing.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/mpio_admin-troubleshoot)

## <a name="list-of-useful-commands"></a>Lijst met nuttige opdrachten
| Type | Opdracht | Beschrijving |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |ISCSI-service starten |
| &nbsp; |`service iscsid stop` |ISCSI-service stoppen |
| &nbsp; |`service iscsid restart` |ISCSI-service opnieuw starten |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Beschikbare doelen ontdekken op het opgegeven adres |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Inloggen bij het iSCSI-doel |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Afmelden bij het iSCSI-doel |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Naam van iSCSI-initiator afdrukken |
| &nbsp; |`iscsiadm -m session -s <sessionid> -P 3` |Controleer de status van de iSCSI-sessie en het volume dat op de host is ontdekt |
| &nbsp; |`iscsi -m session` |Toont alle iSCSI-sessies die zijn vastgesteld tussen de host en het StorSimple-apparaat |
|  | | |
| **Gebruik van meerdere paden** |`service multipathd start` |Start multipath daemon |
| &nbsp; |`service multipathd stop` |Stop multipath daemon |
| &nbsp; |`service multipathd restart` |Opnieuw multipath daemon |
| &nbsp; |`chkconfig multipathd on` </br> OF </br> `mpathconf -with_chkconfig y` |Multipath daemon inschakelen om te starten bij het opstarten |
| &nbsp; |`multipathd -k` |De interactieve console starten voor het oplossen van problemen |
| &nbsp; |`multipath -l` |Multipath-verbindingen en -apparaten weergeven |
| &nbsp; |`mpathconf --enable` |Een voorbeeld van mulitpath.conf-bestand maken in`/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Volgende stappen
Aangezien u MPIO op Linux-host configureert, moet u mogelijk ook verwijzen naar de volgende CentoS 6.6-documenten:

* [MPIO instellen op CentOS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/index)
* [Linux-trainingsgids](http://linux-training.be/linuxsys.pdf)

