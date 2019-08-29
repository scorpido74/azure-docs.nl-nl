---
title: Hoge Beschik baarheid is ingesteld met STONITH voor SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Hoge Beschik baarheid instellen voor SAP HANA op Azure (grote instanties) in SUSE met behulp van de STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d97130251f7ca56adaf77c5e70d6f08bd5cf514
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101516"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Hoge Beschik baarheid die in SUSE is ingesteld met behulp van de STONITH
Dit document bevat gedetailleerde stapsgewijze instructies voor het instellen van de hoge Beschik baarheid op SUSE-besturings systeem met behulp van het STONITH-apparaat.

**Uitsluiting** *Deze hand leiding wordt afgeleid door de installatie te testen in de micro soft HANA-omgeving met grote instanties, die goed werkt. Als het micro soft service management-team voor HANA grote instanties biedt geen ondersteuning voor het besturings systeem, moet u mogelijk contact opnemen met SUSE voor eventuele verdere problemen oplossen of verduidelijken op de laag van het besturings systeem. Het micro soft service management-team stelt STONITH-apparaat in en biedt volledige ondersteuning en kan worden betrokken bij het oplossen van problemen met STONITH-apparaten.*
## <a name="overview"></a>Overzicht
Voor het instellen van de maximale Beschik baarheid met behulp van SUSE-clustering moeten aan de volgende vereisten worden voldaan.
### <a name="pre-requisites"></a>Vereisten
- HANA grote instanties worden ingericht
- Het besturings systeem is geregistreerd
- HANA-grote instanties servers zijn verbonden met een SMT-server voor het verkrijgen van patches/pakketten
- Er zijn nieuwste patches geïnstalleerd op het besturings systeem
- NTP (tijd server) is ingesteld
- Lees en begrijp de nieuwste versie van de SUSE-documentatie op HA Setup

### <a name="setup-details"></a>Details van de installatie
In deze hand leiding worden de volgende instellingen gebruikt:
- Besturingssysteem: SLES 12 SP1 voor SAP
- HANA grote instanties: 2xS192 (vier sockets, 2 TB)
- HANA-versie: HANA 2.0 SP1
- Server namen: sapprdhdb95 (Knooppunt1) en sapprdhdb96 (Knooppunt2)
- STONITH-apparaat: op iSCSI gebaseerd STONITH-apparaat
- NTP ingesteld op een van de HANA grote instantie knooppunten

Wanneer u HANA grote instanties instelt met HSR, kunt u een micro soft service management-team aanvragen om STONITH in te stellen. Als u al een bestaande klant bent en u een STONITH-apparaat hebt ingesteld voor uw bestaande Blade, moet u de volgende informatie over het micro soft service management-team opgeven in het formulier service aanvraag (SRF). U kunt een SRF-formulier aanvragen via de Technical Account Manager of uw contact persoon van micro soft voor de onboarding van een grote instantie van HANA. De nieuwe klanten kunnen een STONITH-apparaat vragen op het moment van inrichten. De invoer is beschikbaar in het aanvraag formulier voor inrichting.

- Server naam en server-IP-adres (bijvoorbeeld myhanaserver1, 10.35.0.1)
- Locatie (bijvoorbeeld VS Oost)
- Klant naam (bijvoorbeeld micro soft)
- SID-HANA-systeem-id (bijvoorbeeld H11)

Zodra het STONITH-apparaat is geconfigureerd, biedt micro soft service management team u de SBD-apparaatnaam en het IP-adres van de iSCSI-opslag, die u kunt gebruiken voor het configureren van STONITH Setup. 

Als u de end-to-end HA wilt instellen met behulp van STONITH, moet u de volgende stappen volgen:

1.  Het SBD-apparaat identificeren
2.  Het SBD-apparaat initialiseren
3.  Het cluster configureren
4.  De Softdog-watchdog instellen
5.  Het knoop punt toevoegen aan het cluster
6.  Het cluster valideren
7.  De resources configureren voor het cluster
8.  Het failoverproces testen

## <a name="1---identify-the-sbd-device"></a>1.   Het SBD-apparaat identificeren
In deze sectie wordt beschreven hoe u het SBD-apparaat voor uw Setup kunt bepalen nadat het micro soft service management team de STONITH heeft geconfigureerd. **Deze sectie is alleen van toepassing op de bestaande klant**. Als u een nieuwe klant bent, kunt u het micro soft service management-team de naam van een SBD-apparaat geven en deze sectie overs Laan.

1,1 */etc/iscsi/initiatorname.iSCI* wijzigen in 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Micro soft Service Management biedt deze teken reeks. Wijzig het bestand op **beide** knoop punten, maar het knooppunt nummer wijkt af van elk knoop punt.

![initiatorname. png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1,2 */etc/iscsi/iscsid.conf*wijzigen: Stel *node. Session. Timeo. replacement_timeout = 5* en *node. startup = Automatic*in. Wijzig het bestand op **beide** knoop punten.

1,3 de detectie opdracht uit te voeren, worden vier sessies weer gegeven. Voer de app uit op beide knoop punten.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1,4 Voer de opdracht uit om u aan te melden bij het iSCSI-apparaat. er worden vier sessies weer gegeven. Voer de app uit op **beide** knoop punten.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1,5 Voer het script opnieuw scannen uit: *rescan-SCSI-bus.sh*.  Met dit script worden de nieuwe schijven weer gegeven die u voor u hebt gemaakt.  Voer de app uit op beide knoop punten. Als het goed is, ziet u een LUN-nummer dat groter is dan nul (bijvoorbeeld: 1, 2 enz.)

```
rescan-scsi-bus.sh
```
![rescanscsibus. png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1,6 als u de apparaatnaam wilt ophalen, voert u de opdracht *fdisk – l*. Voer de app uit op beide knoop punten. Kies het apparaat met de grootte van **178 MIB**.

```
  fdisk –l
```

![fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   Het SBD-apparaat initialiseren

2,1 het SBD-apparaat op **beide** knoop punten initialiseren

```
sbd -d <SBD Device Name> create
```
![sbdcreate. png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2,2 Controleer wat er is geschreven op het apparaat. Doe dit op **beide** knoop punten

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3.   Het cluster configureren
In deze sectie worden de stappen beschreven voor het instellen van het cluster SUSE HA.
### <a name="31-package-installation"></a>3,1-pakket installatie
3.1.1 Controleer of ha_sles en SAPHanaSR-doc-patronen zijn geïnstalleerd. Als deze niet is geïnstalleerd, installeert u ze. Installeer de app op **beide** knoop punten.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3,2 het cluster instellen
3.2.1 u kunt *HA-cluster-init* opdracht gebruiken of de wizard YaST2 gebruiken om het cluster in te stellen. In dit geval wordt de wizard YaST2 gebruikt. U voert deze stap **alleen uit op het primaire knoop punt**.

Volg YaST2 > hoge Beschik baarheid ![> cluster YaST-Control-Center](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
. png![YaST-Hawk-install. png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Klik op **Annuleren** omdat het halk2-pakket al is geïnstalleerd.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Klik op **door gaan**

Verwachte waarde = aantal geïmplementeerde knoop punten (in dit geval 2 ![) YaST-cluster-Security.](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) PNG Klik op **volgende**
![YaST-cluster-configure-csync2](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) . png Voeg knooppunt namen toe en klik vervolgens op toevoegen voorgestelde bestanden '

Klik op ' csync2 inschakelen ' op '

Klik op ' vooraf gedeelde sleutels genereren ', deze wordt weer gegeven onder pop-up

![YaST-key-file. png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Klik op **OK**

De verificatie wordt uitgevoerd met behulp van de IP-adressen en vooraf gedeelde sleutels in Csync2. Het sleutel bestand wordt gegenereerd met csync2-k/etc/csync2/key_hagroup. Het bestand key_hagroup moet hand matig worden gekopieerd naar alle leden van het cluster nadat het is gemaakt. **Zorg ervoor dat u het bestand kopieert van het knoop punt 1 naar Knooppunt2**.

![YaST-cluster-conntrackd. png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Klik op **volgende**
![YaST-Cluster-service. png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

In de standaard optie is opstarten uitgeschakeld, gewijzigd in aan, zodat pacemaker wordt gestart bij het opstarten. U kunt de keuze maken op basis van uw installatie vereisten.
Klik op **volgende** en de cluster configuratie is voltooid.

## <a name="4---setting-up-the-softdog-watchdog"></a>4.   De Softdog-watchdog instellen
In deze sectie wordt de configuratie van de watchdog (softdog) beschreven.

4,1 Voeg de volgende regel toe aan */etc/init.d/boot.local* op **beide** knoop punten.
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4,2 werk het bestand */etc/sysconfig/SBD* op **beide** knoop punten als volgt bij:
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4,3 laden van de kernel-module op **beide** knoop punten door de volgende opdracht uit te voeren
```
modprobe softdog
```
![modprobe-softdog-Command. png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4,4 Controleer en zorg ervoor dat softdog wordt uitgevoerd, zoals op de volgende knoop punten:
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4,5 start het SBD-apparaat op **beide** knoop punten
```
/usr/share/sbd/sbd.sh start
```
![SBD-sh-start. png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4,6 test de SBD-daemon op **beide** knoop punten. U ziet twee vermeldingen nadat u deze op **beide** knoop punten hebt geconfigureerd
```
sbd -d <SBD Device Name> list
```
![SBD-list. png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,7 een test bericht verzenden naar **een** van uw knoop punten
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![SBD-list. png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,8 op het **tweede** knoop punt (Knooppunt2) kunt u de bericht status controleren
```
sbd  -d <SBD Device Name> list
```
![SBD-List-Message. png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4,9 als u de SBD-configuratie wilt aannemen, werkt u het bestand */etc/sysconfig/SBD* als volgt bij. Het bestand op **beide** knoop punten bijwerken
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4,10 start de pacemaker-service op het **primaire knoop punt** (Knooppunt1)
```
systemctl start pacemaker
```
![start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Als de pacemaker-service *mislukt*, raadpleegt u *scenario 5: Pacemaker-service mislukt*

## <a name="5---joining-the-cluster"></a>5.   Lid worden van het cluster
In deze sectie wordt beschreven hoe u het knoop punt aan het cluster kunt toevoegen.

### <a name="51-add-the-node"></a>5,1 het knoop punt toevoegen
Voer de volgende opdracht uit op **Knooppunt2** om Knooppunt2 aan het cluster toe te voegen.
```
ha-cluster-join
```
Als er een *fout optreedt* tijdens het toevoegen van het cluster *, raadpleegt u scenario 6: Knoop punt 2 kan niet aan het*cluster worden toegevoegd.

## <a name="6---validating-the-cluster"></a>6.   Het cluster valideren

### <a name="61-start-the-cluster-service"></a>6,1 de Cluster service starten
Om het cluster voor de eerste keer op **beide** knoop punten te controleren en te starten.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker. png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6,2 de status controleren
Voer de opdracht *crm_mon* uit om ervoor te zorgen dat **beide** knoop punten online zijn. U kunt het uitvoeren op **een van de knoop punten** van het cluster
```
crm_mon
```
![CRM-Mon. png](media/HowToHLI/HASetupWithStonith/crm-mon.png) u kunt zich ook aanmelden bij Hawk om de cluster status *https://\<node IP >: 7630*te controleren. De standaard gebruiker is hacluster en het wacht woord is Linux. Indien nodig kunt u het wacht woord wijzigen met de opdracht *passwd* .

## <a name="7-configure-cluster-properties-and-resources"></a>7. Cluster eigenschappen en-bronnen configureren 
In deze sectie worden de stappen beschreven voor het configureren van de cluster resources.
In dit voor beeld stelt u de volgende resource in, de rest kan worden geconfigureerd (indien nodig) door te verwijzen naar de gids van SUSE HA. Voer de configuratie alleen in **een van de knoop punten** uit. Doen op het primaire knoop punt.

- Cluster-Boots trap
- STONITH-apparaat
- Het virtuele IP-adres


### <a name="71-cluster-bootstrap-and-more"></a>7,1-cluster Boots trap en meer
Boots trap van cluster toevoegen. Maak het bestand en voeg de tekst als volgt toe:
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
Voeg de configuratie toe aan het cluster.
```
crm configure load update crm-bs.txt
```
![CRM-configure-crmbs. png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7,2 STONITH-apparaat
Resource STONITH toevoegen. Maak het bestand en voeg de tekst als volgt toe.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
Voeg de configuratie toe aan het cluster.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7,3 het virtuele IP-adres
Voeg het virtuele IP-adres van de resource toe. Maak het bestand en voeg de volgende tekst toe.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Voeg de configuratie toe aan het cluster.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7,4 de resources valideren

Wanneer u de opdracht *crm_mon*uitvoert, ziet u de twee resources daar.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

U kunt ook de status van het *IP-\<adres van het https://-knoop punt zien >: 7630/CIB/Live/State*

![hawlk-status-page. png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Het failoverproces testen
Als u het failoverproces wilt testen, stopt u de pacemaker-service op Knooppunt1 en de failover van resources naar Knooppunt2.
```
Service pacemaker stop
```
Stop nu de pacemaker-service op **Knooppunt2** en bronnen waarvoor failover is uitgevoerd naar **Knooppunt1**

**Voor failover**
before-failover.](media/HowToHLI/HASetupWithStonith/Before-failover.png)
PNG**na**failoverAfter-failover.
PNG](media/HowToHLI/HASetupWithStonith/after-failover.png)
CRM-Mon-after-failover. png![![![](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)


## <a name="9-troubleshooting"></a>9. Problemen oplossen
In deze sectie worden de scenario's beschreven die kunnen worden aangetroffen tijdens de installatie. U hoeft deze problemen mogelijk niet op te lossen.

### <a name="scenario-1-cluster-node-not-online"></a>Scenario 1: Het cluster knooppunt is niet online
Als een van de knoop punten niet online in Cluster beheer wordt weer gegeven, kunt u het volgende proberen om deze online te brengen.

De iSCSI-service starten
```
service iscsid start
```

En nu moet u zich kunnen aanmelden bij dat iSCSI-knoop punt
```
iscsiadm -m node -l
```
De verwachte uitvoer ziet er als volgt uit
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Scenario 2: de grafische weer gave wordt niet weer gegeven in YaST2
Het YaST2-grafisch scherm wordt gebruikt voor het instellen van het cluster met hoge Beschik baarheid in dit document. Als YaST2 niet wordt geopend met het grafische venster, zoals wordt weer gegeven en fout bij het genereren van Qt, voert u de volgende stappen uit. Als deze wordt geopend met het grafische venster, kunt u de stappen overs Laan.

**Optreedt**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Verwachte uitvoer**

![YaST-Control-Center. png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Als de YaST2 niet wordt geopend met de grafische weer gave, volgt u de volgende stappen.

Installeer de vereiste pakketten. U moet zijn aangemeld als gebruiker ' root ' en instellen dat SMT is ingesteld om de pakketten te downloaden/installeren.

Als u de pakketten wilt installeren, gebruikt u YaST > Software-> Software Management > afhankelijkheden > optie Aanbevolen pakketten installeren.... In de volgende scherm afbeelding ziet u de verwachte schermen.
>[!NOTE]
>U moet de stappen op beide knoop punten uitvoeren, zodat u de grafische weer gave YaST2 van beide knoop punten kunt openen.

![YaST-sofwaremanagement. png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

Onder afhankelijkheden selecteert u ' Aanbevolen pakketten installeren ![' YaST-dependencies. png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Bekijk de wijzigingen en klik op OK

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Installatie van pakketten ![loopt YaST-Performing-Installation. png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Klik op Next

![YaST-installation-report. png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Klik op volt ooien

U moet ook de libqt4-en libyui-qt-pakketten installeren.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![Zypper-install-ligyui. png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![Zypper-install-ligyui_part2. png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) YaST2 moet de grafische weer gave nu kunnen openen, zoals hier wordt weer gegeven.
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Scenario 3: YaST2 biedt geen hoge Beschik baarheid
Als u de optie hoge Beschik baarheid wilt weer geven in het YaST2-beheer centrum, moet u de extra pakketten installeren.

De volgende patronen selecteren met behulp van YaST2 > Software > software beheer >

- SAP HANA Server-basis
- C/C++ compiler en hulpprogram ma's
- Hoge beschikbaarheid
- SAP-toepassings server basis

In het volgende scherm ziet u de stappen voor het installeren van de patronen.

Software beheer van YaST2 > > gebruiken

![yast2-Control-Center. png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

De patronen selecteren

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Klik op **accepteren**

![YaST-changed-packages. png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Klik op **door gaan**

![yast2-Performing-Installation. png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Klik op **volgende** wanneer de installatie is voltooid

![yast2-installation-report. png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scenario 4: HANA-installatie mislukt met gcc-assembly's fout
De HANA-installatie mislukt met de volgende fout.

![Hana-Installation-Error. png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Om het probleem op te lossen, moet u de volgende bibliotheken (libgcc_sl en libstdc + + 6) installeren.

![Zypper-install-lib. png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scenario 5: Pacemaker-service mislukt

Het volgende probleem is opgetreden tijdens het starten van de pacemaker-service.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Om het probleem op te lossen, verwijdert u de volgende regel uit het bestand */usr/lib/systemd/System/fstrim.timer*

```
Persistent=true
```

![Permanent. png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Scenario 6: Knoop punt 2 kan niet aan het cluster worden toegevoegd

Wanneer u de Knooppunt2 koppelt aan het bestaande cluster met behulp van de opdracht *HA-cluster-join* , is de volgende fout opgetreden.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![HA-cluster-join-error. png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Voer de volgende handelingen uit op de knoop punten om het probleem op te lossen

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-Knooppunt1. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-Knooppunt2. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Na de voor gaande oplossing moet Knooppunt2 worden toegevoegd aan het cluster

![HA-cluster-join-fix. png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Algemene documentatie
Meer informatie over de configuratie van SUSE HA vindt u in de volgende artikelen: 

- [SAP HANA scenario voor geoptimaliseerde SR-prestaties](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Op opslag gebaseerde omheining](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog-pacemaker-cluster gebruiken voor SAP HANA-deel 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog-pacemaker-cluster gebruiken voor SAP HANA-deel 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
