---
title: Hoge beschikbaarheid ingesteld met STONITH voor SAP HANA op Azure (Large Instances)| Microsoft Documenten
description: Hoge beschikbaarheid voor SAP HANA op Azure (Large Instances) instellen in SUSE met behulp van de STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4060dbe936af8ff1f9dd8c958f64834cb06525de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77615086"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Hoge beschikbaarheid ingesteld in SUSE met behulp van de STONITH
Dit document bevat de gedetailleerde stapsgewijze instructies voor het instellen van de hoge beschikbaarheid op het SUSE-besturingssysteem met behulp van het STONITH-apparaat.

**Disclaimer:** *Deze handleiding wordt afgeleid door het testen van de setup in de Microsoft HANA Large Instances-omgeving, die met succes werkt. Aangezien het Microsoft Service Management-team voor HANA Large Instances geen ondersteuning biedt voor het besturingssysteem, moet u mogelijk contact opnemen met SUSE voor verdere probleemoplossing of verduidelijking van de besturingssysteemlaag. Het Service Management-team van Microsoft heeft stonith-apparaat ingesteld en ondersteunt en kan worden betrokken bij het oplossen van problemen met STONITH-apparaten.*
## <a name="overview"></a>Overzicht
Om de hoge beschikbaarheid in te stellen met SUSE-clustering, moeten de volgende vereisten voldoen.
### <a name="pre-requisites"></a>Vereisten
- HANA Large Instances zijn ingericht
- Besturingssysteem is geregistreerd
- HANA Large Instances servers zijn verbonden met SMT server om patches/pakketten te krijgen
- Besturingssysteem hebben nieuwste patches geïnstalleerd
- NTP (tijdserver) is ingesteld
- Lees en begrijp de nieuwste versie van SUSE-documentatie over HA-setup

### <a name="setup-details"></a>Details van het instellen
In deze handleiding wordt de volgende instelling gebruikt:
- Besturingssysteem: SLES 12 SP1 voor SAP
- HANA Large Instances: 2xS192 (vier sockets, 2 TB)
- HANA Versie: HANA 2.0 SP1
- Servernamen: sapprdhdb95 (node1) en sapprdhdb96 (node2)
- STONITH-apparaat: iSCSI-apparaat op basis van STONITH
- NTP ingesteld op een van de HANA Large Instance-knooppunten

Wanneer u HANA Large Instances instelt met HSR, u het Microsoft Service Management-team vragen om STONITH in te stellen. Als u al een bestaande klant bent die HANA Large Instances heeft ingericht en stonith-apparaat moet instellen voor uw bestaande blades, moet u de volgende informatie aan het Microsoft Service Management-team verstrekken in het servicerequestformulier (SRF). U het SRF-formulier aanvragen via de Technical Account Manager of uw Microsoft Contact voor HANA Large Instance onboarding. De nieuwe klanten kunnen stonith-apparaat aanvragen op het moment van inlevering. De inputs zijn beschikbaar in het inrichtingsformulier.

- Servernaam en SERVER IP-adres (bijvoorbeeld myhanaserver1, 10.35.0.1)
- Locatie (bijvoorbeeld US East)
- Naam van de klant (bijvoorbeeld Microsoft)
- SID - HANA-systeemidentificatie (bijvoorbeeld H11)

Zodra het STONITH-apparaat is geconfigureerd, biedt het Microsoft Service Management-team u de naam en het IP-adres van het SBD-apparaat van de iSCSI-opslag, waarmee u de STONITH-installatie configureren. 

Om het einde van HA in te stellen met STONITH, moeten de volgende stappen worden gevolgd:

1.  Het SBD-apparaat identificeren
2.  Initialiseren van het SBD-apparaat
3.  Het cluster configureren
4.  Het opzetten van de Softdog Watchdog
5.  Lid worden van het knooppunt naar het cluster
6.  Het cluster valideren
7.  De resources configureren voor het cluster
8.  Het failoverproces testen

## <a name="1---identify-the-sbd-device"></a>1. Het SBD-apparaat identificeren
In dit gedeelte wordt beschreven hoe u het SBD-apparaat voor uw installatie bepalen nadat het Service Management Team van Microsoft de STONITH heeft geconfigureerd. **Deze sectie is alleen van toepassing op de bestaande klant.** Als u een nieuwe klant bent, verstrekt het Service Management Team van Microsoft u wel de naam van het SBD-apparaat en u deze sectie overslaan.

1.1 Wijzigen */etc/iscsi/initiatorname.isci* aan 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Microsoft service management biedt deze string. Wijzig het bestand op **beide** knooppunten, maar het knooppuntnummer is verschillend op elk knooppunt.

![initiatorname.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 Wijzigen */etc/iscsi/iscsid.conf*: Set *node.session.timeo.replacement_timeout=5* and *node.startup = automatic*. Wijzig het bestand op **beide** knooppunten.

1.3 Voer de detectieopdracht uit, het toont vier sessies. Voer het uit op beide knooppunten.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 Voer de opdracht uit om in te loggen op het iSCSI-apparaat, het toont vier sessies. Voer het uit op **beide** knooppunten.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 Voer het rescanscript uit: *rescan-scsi-bus.sh*.  Dit script toont u de nieuwe schijven die voor u zijn gemaakt.  Voer het uit op beide knooppunten. U ziet een LUN-getal dat groter is dan nul (bijvoorbeeld: 1, 2 enz.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 Om de naam van het apparaat uit te voeren, wordt de *opdrachtfdisk –l*. Voer het uit op beide knooppunten. Kies het apparaat met de grootte van **178 MiB.**

```
  fdisk –l
```

![fdisk-l.png fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. Initialiseren van het SBD-apparaat

2.1 Initialiseren van het SBD-apparaat op **beide** knooppunten

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Controleer wat er aan het apparaat is geschreven. Doe het op **beide** knooppunten

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. Het cluster configureren
In deze sectie worden de stappen beschreven voor het instellen van het SUSE HA-cluster.
### <a name="31-package-installation"></a>3.1 Pakketinstallatie
3.1.1 Controleer of ha_sles en SAPHanaSR-doc patronen zijn geïnstalleerd. Als het niet is geïnstalleerd, installeer ze dan. Installeer het op **beide** knooppunten.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 Het cluster instellen
3.2.1 U de opdracht *ha-cluster-init* gebruiken of de wizard yast2 gebruiken om het cluster in te stellen. In dit geval wordt de wizard yast2 gebruikt. U voert deze stap alleen uit **op het primaire knooppunt**.

Volg yast2> High ![Availability > Cluster yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Klik **op annuleren** omdat het halk2-pakket al is geïnstalleerd.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Klik **op Doorgaan**

Verwachte waarde=Aantal geïmplementeerde knooppunten (in ![](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) dit geval 2) yast-Cluster-Security.png Klik op **Volgende**
![yast-cluster-configure-csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) Voeg knooppuntnamen toe en klik op 'Voorgestelde bestanden toevoegen'

Klik op 'Csync2 aan zetten'

Klik op 'Pre-Shared-Keys genereren', deze wordt hieronder weergegeven.

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Klik **op OK**

De verificatie wordt uitgevoerd met behulp van de IP-adressen en vooraf gedeelde sleutels in Csync2. Het sleutelbestand wordt gegenereerd met csync2 -k /etc/csync2/key_hagroup. Het bestand key_hagroup moet handmatig worden gekopieerd naar alle leden van het cluster nadat het is gemaakt. **Zorg ervoor dat u het bestand van knooppunt 1 naar knooppunt 2 kopieert.**

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Klik **op Volgende**
![yast-cluster-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

In de standaardoptie was Opstarten uitgeschakeld en wijzigt u deze in 'aan' zodat de pacemaker wordt gestart bij het opstarten. U de keuze maken op basis van uw installatievereisten.
Klik **op Volgende** en de clusterconfiguratie is voltooid.

## <a name="4---setting-up-the-softdog-watchdog"></a>4. Het opzetten van de Softdog Watchdog
Deze sectie beschrijft de configuratie van de waakhond (softdog).

4.1 Voeg de volgende regel toe aan */etc/init.d/boot.local* op **beide** knooppunten.
```
modprobe softdog
```
![modprobe-softdog.png modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 Update het bestand */etc/sysconfig/sbd* op **beide** knooppunten als volgt:
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 De kernelmodule op **beide** knooppunten laden door de volgende opdracht uit te voeren
```
modprobe softdog
```
![modprobe-softdog-command.png modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 Controleer en zorg ervoor dat softdog wordt uitgevoerd als volgt op **beide** knooppunten:
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 Start het SBD-apparaat op **beide** knooppunten
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 Test de SBD daemon op **beide** knooppunten. U ziet twee vermeldingen nadat u deze hebt geconfigureerd op **beide** knooppunten
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 Een testbericht verzenden naar **een** van uw knooppunten
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 Op het **tweede** knooppunt (knooppunt2) u de berichtstatus controleren
```
sbd  -d <SBD Device Name> list
```
![sbd-list-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 Om de sbd config aan te nemen, werkt u het bestand */etc/sysconfig/sbd* als volgt bij. Het bestand op **beide** knooppunten bijwerken
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 Start de pacemakerservice op het **primaire knooppunt** (knooppunt1)
```
systemctl start pacemaker
```
![start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Als de pacemakerservice *mislukt,* raadpleegt u *Scenario 5: Pacemakerservice mislukt*

## <a name="5---joining-the-cluster"></a>5. Lid worden van het cluster
In deze sectie wordt beschreven hoe u het knooppunt aan het cluster deelnemen.

### <a name="51-add-the-node"></a>5.1 Voeg het knooppunt toe
Voer de volgende opdracht uit op **knooppunt2** om node2 bij het cluster te laten aansluiten.
```
ha-cluster-join
```
Als u een *fout* ontvangt tijdens het samenvoegen van het cluster, raadpleegt u *Scenario 6: Knooppunt 2 kan niet deelnemen aan het cluster*.

## <a name="6---validating-the-cluster"></a>6. Validering van het cluster

### <a name="61-start-the-cluster-service"></a>6.1 De clusterservice starten
Om het cluster voor het eerst op **beide** knooppunten te controleren en optioneel te starten.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 De status controleren
Voer de opdracht *crm_mon* uitvoeren om ervoor te zorgen dat **beide** knooppunten online zijn. U het uitvoeren op **een van de knooppunten** van het cluster
```
crm_mon
```
![crm-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) U ook inloggen op hawk om de clusterstatus *https://\<knooppunt IP>:7630*te controleren. De standaard gebruiker is hacluster en het wachtwoord is linux. Indien nodig u het wachtwoord wijzigen met de *opdracht Passwd.*

## <a name="7-configure-cluster-properties-and-resources"></a>7. Clustereigenschappen en -bronnen configureren 
In deze sectie worden de stappen beschreven om de clusterbronnen te configureren.
In dit voorbeeld u de volgende resource instellen, de rest kan worden geconfigureerd (indien nodig) door te verwijzen naar de SUSE HA-handleiding. Voer de config alleen uit in **een van de knooppunten.** Doe op het primaire knooppunt.

- Clusterbootsval
- STONITH-apparaat
- Het virtuele IP-adres


### <a name="71-cluster-bootstrap-and-more"></a>7.1 Cluster bootstrap en meer
Clusterbootsval toevoegen. Maak het bestand en voeg de tekst als volgt toe:
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
![crm-configure-crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 STONITH-apparaat
Voeg resource STONITH toe. Maak het bestand en voeg de tekst als volgt toe.
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

### <a name="73-the-virtual-ip-address"></a>7.3 Het virtuele IP-adres
Virtuele IP-bron toevoegen. Maak het bestand en voeg de tekst hieronder toe.
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

### <a name="74-validate-the-resources"></a>7.4 De middelen valideren

Wanneer u opdracht *crm_mon*uitvoert, u de twee bronnen daar zien.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Ook u de status zien op *https://\<node IP-adres>:7630/cib/live/state*

![hawlk-status-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Het failoverproces testen
Als u het failoverproces wilt testen, stopt u de pacemakerservice op knooppunt1 en mislukken de resources naar knooppunt2.
```
Service pacemaker stop
```
Nu, stop de pacemaker service op **node2** en resources niet over naar **knooppunt1**

**Vóór failover**  
![Before-failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**Na failover**  
![after-failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)  
![crm-mon-after-failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9. Probleemoplossing
In deze sectie worden de weinige foutscenario's beschreven die tijdens de installatie kunnen worden aangetroffen. U mag niet noodzakelijkerwijs geconfronteerd met deze problemen.

### <a name="scenario-1-cluster-node-not-online"></a>Scenario 1: Clusterknooppunt niet online
Als een van de knooppunten niet online wordt weergegeven in clusterbeheer, u proberen deze online te brengen.

De iSCSI-service starten
```
service iscsid start
```

En nu moet je in staat zijn om in te loggen op dat iSCSI-knooppunt
```
iscsiadm -m node -l
```
De verwachte uitvoer ziet eruit als volgt
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
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Scenario 2: yast2 toont geen grafische weergave
Het grafische scherm yast2 wordt gebruikt om het cluster Hoge beschikbaarheid in dit document in te stellen. Als yast2 niet wordt geopend met het grafische venster zoals weergegeven en Qt-fout gooit, voert u de stappen als volgt uit. Als het wordt geopend met het grafische venster, u de stappen overslaan.

**Fout**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Verwachte uitvoer**

![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Als de yast2 niet wordt geopend met de grafische weergave, volgt u de volgende stappen.

Installeer de vereiste pakketten. U moet ingelogd zijn als "root" van de gebruiker en smt hebben ingesteld om de pakketten te downloaden/installeren.

Gebruik yast>Software>Software Management>Afhankelijkheden> optie "Aanbevolen pakketten installeren...". De volgende screenshot illustreert de verwachte schermen.
>[!NOTE]
>U moet de stappen op beide knooppunten uitvoeren, zodat u toegang hebt tot de yast2 grafische weergave van beide knooppunten.

![yast-sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

Selecteer onder Afhankelijkheden de optie !['Aanbevolen pakketten installeren' yast-dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Bekijk de wijzigingen en druk op OK

![Yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Pakketinstallatie verloopt ![yast-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Klik op Next

![yast-installation-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Klik op Voltooien

U moet ook de libqt4 en libyui-qt pakketten installeren.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 moet in staat zijn om de grafische weergave nu te openen, zoals hier getoond.
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Scenario 3: yast2 biedt geen optie hoge beschikbaarheid
Als u de optie Hoge beschikbaarheid zichtbaar wilt maken in het yast2-controlecentrum, moet u de extra pakketten installeren.

Yast2>Software>Software management gebruiken>Selecteer de volgende patronen

- SAP HANA-serverbasis
- C/C++ compiler en hulpprogramma's
- Hoge beschikbaarheid
- Basis van SAP-toepassingsserver

In het volgende scherm ziet u de stappen om de patronen te installeren.

Yast2 > Software > Software Management gebruiken

![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Selecteer de patronen

![yast-patroon1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Klik **op Accepteren**

![yast-changed-packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Klik **op Doorgaan**

![yast2-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Klik **op Volgende** wanneer de installatie is voltooid

![yast2-installatie-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scenario 4: HANA-installatie mislukt met gcc-assemblagefout
De HANA-installatie mislukt met de volgende fout.

![Hana-installatie-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Om het probleem op te lossen, moet u bibliotheken (libgcc_sl en libstdc++6) als volgt installeren.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scenario 5: Pacemakerservice mislukt

Het volgende probleem deed zich voor tijdens de start van de pacemakerservice.

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

Om dit te verhelpen, verwijdert u de volgende regel uit het bestand */usr/lib/systemd/systemd/system/fstrim.timer*

```
Persistent=true
```

![Persistent.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Scenario 6: Knooppunt 2 kan niet deelnemen aan het cluster

Bij het samenvoegen van het knooppunt2 met het bestaande cluster met de opdracht *ha-cluster-join* is de volgende fout opgetreden.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-join-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Voer het volgende uit op beide knooppunten om het volgende op te lossen

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-node1. Png](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-node2. Png](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Na de vorige fix wordt node2 toegevoegd aan het cluster

![ha-cluster-join-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Algemene documentatie
Meer informatie over de installatie van SUSE HA vindt u in de volgende artikelen: 

- [SAP HANA SR Performance Optimized Scenario](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Op opslag gebaseerd hekwerk](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog - Pacemaker cluster gebruiken voor SAP HANA- Deel 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog - Pacemaker cluster gebruiken voor SAP HANA- Deel 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
