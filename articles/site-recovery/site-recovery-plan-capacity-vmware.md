---
title: Capaciteit plannen voor VMware-noodherstel met Azure Site Recovery
description: In dit artikel u capaciteit en schaalvergroting plannen wanneer u het herstel van VMware-VM's voor noodgevallen inStelt op Azure met behulp van Azure Site Recovery.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 467c70a722b8a243be6ac2826188a4ba3459aa06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257613"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Capaciteit en schaling voor VMware-noodherstel plannen naar Azure

Gebruik dit artikel om capaciteit en schaling te plannen wanneer u on-premises Vm's en fysieke servers naar Azure repliceert met [Azure Site Recovery.](site-recovery-overview.md)

## <a name="how-do-i-start-capacity-planning"></a>Hoe start ik met capaciteitsplanning?

Voor meer informatie over azure site recovery-infrastructuurvereisten verzamelt u informatie over uw replicatieomgeving door [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc) voor VMware-replicatie uit te voeren. Zie [Over Site Recovery Deployment Planner voor VMware naar Azure voor](site-recovery-deployment-planner.md)meer informatie. 

Site Recovery Deployment Planner biedt een rapport met volledige informatie over compatibele en oncompatibele VM's, schijven per VM en gegevensverloop per schijf. De tool geeft ook een overzicht van de vereisten voor netwerkbandbreedte om te voldoen aan doel-RPO en de Azure-infrastructuur die nodig is voor succesvolle replicatie en testfailover.

## <a name="capacity-considerations"></a>Overwegingen bij capaciteitsbepaling

Onderdeel | Details
--- | ---
**Replicatie** | **Maximale dagelijkse wijzigingssnelheid**: Een beveiligde machine kan slechts één processerver gebruiken. Eén processerver kan een hoeveelheid dagelijkse wijzigingen afhandelen tot maximaal 2 TB. Dus, 2 TB is de maximale dagelijkse gegevenswijzigingssnelheid die wordt ondersteund voor een beveiligde machine.<br /><br /> **Maximale doorvoer:** een gerepliceerde machine kan deel uitmaken van één opslagaccount in Azure. Een standaard Azure Storage-account kan maximaal 20.000 aanvragen per seconde verwerken. We raden u aan het aantal invoer-/uitvoerbewerkingen per seconde (IOPS) voor een bronmachine te beperken tot 20.000. Als u bijvoorbeeld een bronmachine hebt met vijf schijven en elke schijf 120 IOPS (8 K groot) genereert op de bronmachine, bevindt de bronmachine zich binnen de Azure per-disk IOPS-limiet van 500. (Het aantal vereiste opslagaccounts is gelijk aan de totale bronmachine IOPS gedeeld door 20.000.)
**Configuratieserver** | De configuratieserver moet de dagelijkse wijzigingssnelheid kunnen verwerken voor alle workloads die op beveiligde machines worden uitgevoerd. De configuratiemachine moet voldoende bandbreedte hebben om gegevens continu te repliceren naar Azure Storage.<br /><br /> Een aanbevolen toepassing is om de configuratieserver op hetzelfde netwerk- en LAN-segment te plaatsen als de machines die u wilt beveiligen. U de configuratieserver op een ander netwerk plaatsen, maar machines die u wilt beveiligen, moeten de zichtbaarheid van het layer 3-netwerk hebben.<br /><br /> Aanbevelingen voor de grootte voor de configuratieserver worden samengevat in de tabel in de volgende sectie.
**Processerver** | De eerste processerver is standaard geïnstalleerd op de configuratieserver. U extra processervers implementeren om uw omgeving te schalen. <br /><br /> De processerver ontvangt replicatiegegevens van beveiligde machines. De processerver optimaliseert gegevens met caching, compressie en versleuteling. Vervolgens stuurt de processerver de gegevens naar Azure. De processervermachine moet over voldoende resources beschikken om deze taken uit te voeren.<br /><br /> De processerver maakt gebruik van een cache op schijfbasis. Gebruik een afzonderlijke cacheschijf van 600 GB of meer om gegevenswijzigingen te verwerken die zijn opgeslagen als er een netwerkknelpunt of -storing optreedt.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Aanbevelingen voor grootte voor de configuratieserver en ingebouwde processerver

Een configuratieserver die een ingebouwde processerver gebruikt om de werkbelasting te beschermen, kan tot 200 virtuele machines verwerken op basis van de volgende configuraties:

CPU | Geheugen | Schijfgrootte cache | Gegevenswijzigingssnelheid | Beveiligde machines
--- | --- | --- | --- | ---
8 vCPU's (2 sockets * 4 cores \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB of minder | Gebruiken om minder dan 100 machines te repliceren.
12 vCPU's (2 sockets * 6 cores \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB tot 1 TB | Gebruik om 100 tot 150 machines te repliceren.
16 vCPU's (2 sockets * 8 cores \@ 2,5 GHz) | 32 GB | 1 TB | >1 TB tot 2 TB | Gebruik om 151 tot 200 machines te repliceren.
Implementeer een andere configuratieserver met behulp van een [OVF-sjabloon](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). | | | | Implementeer een nieuwe configuratieserver als u meer dan 200 machines repliceert.
Implementeer een andere [processerver.](vmware-azure-set-up-process-server-scale.md#download-installation-file) | | | >2 TB| Implementeer een nieuwe scale-out processerver als de totale dagelijkse gegevenswijzigingssnelheid groter is dan 2 TB.

In deze configuraties:

* Elke bronmachine heeft drie schijven van elk 100 GB.
* We gebruikten benchmarking opslag van acht gedeelde toegang handtekening schijven van 10 K RPM met RAID 10 voor cache schijf metingen.

## <a name="size-recommendations-for-the-process-server"></a>Aanbevelingen voor grootte voor de processerver

De processerver is de component die gegevensreplicatie verwerkt in Azure Site Recovery. Als de dagelijkse wijzigingssnelheid groter is dan 2 TB, moet u schaal-outprocesservers toevoegen om de replicatiebelasting te verwerken. Als u wilt uitschalen, u het ware opschalen:

* Verhoog het aantal configuratieservers door te implementeren met behulp van een [OVF-sjabloon.](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) U bijvoorbeeld tot 400 machines beveiligen met behulp van twee configuratieservers.
* Voeg [scale-out processervers](vmware-azure-set-up-process-server-scale.md#download-installation-file)toe. Gebruik de scale-out processervers om replicatieverkeer te verwerken in plaats van (of naast) de configuratieserver.

In de volgende tabel wordt dit scenario beschreven:

* U stelt een scale-out processerver in.
* U hebt beveiligde virtuele machines geconfigureerd om de scale-outprocesserver te gebruiken.
* Elke beveiligde bronmachine heeft drie schijven van elk 100 GB.

Extra processerver | Schijfgrootte cache | Gegevenswijzigingssnelheid | Beveiligde machines
--- | --- | --- | ---
4 vCPU's (2 sockets * 2 cores \@ 2,5 GHz), 8 GB geheugen | 300 GB | 250 GB of minder | Gebruiken om 85 of minder machines te repliceren.
8 vCPU's (2 sockets * 4 cores \@ 2,5 GHz), 12 GB geheugen | 600 GB | 251 GB tot 1 TB | Gebruik om 86 tot 150 machines te repliceren.
12 vCPU's (2 sockets * 6 cores \@ 2,5 GHz) 24 GB geheugen | 1 TB | >1 TB tot 2 TB | Gebruik om 151 tot 225 machines te repliceren.

Hoe u uw servers schaalt, hangt af van uw voorkeur voor een scale-up- of scale-outmodel. Als u wilt opschalen, implementeert u een aantal high-end configuratieservers en verwerkt u servers. Als u wilt uitschalen, implementeert u meer servers met minder resources. Als u bijvoorbeeld 200 machines wilt beschermen met een algemene dagelijkse gegevenswijzigingssnelheid van 1,5 TB, u een van de volgende acties uitvoeren:

* Stel één processerver in (16 vCPU, 24 GB RAM).
* Stel twee processervers in (2 x 8 vCPU, 2* 12 GB RAM).

## <a name="control-network-bandwidth"></a>Netwerkbandbreedte beheren

Nadat u [Site Recovery Deployment Planner](site-recovery-deployment-planner.md) hebt gebruikt om de bandbreedte te berekenen die u nodig hebt voor replicatie (eerste replicatie en vervolgens de delta), hebt u een aantal opties voor het beheren van de hoeveelheid bandbreedte die wordt gebruikt voor replicatie:

* **Bandbreedte met gashendel:** VMware-verkeer dat wordt gerepliceerd naar Azure, gaat via een specifieke processerver. U de bandbreedte beperken op de machines die als processervers worden uitgevoerd.
* **Bandbreedte beïnvloeden:** U de bandbreedte die wordt gebruikt voor replicatie beïnvloeden met behulp van een paar registersleutels:
  * De **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM-registerwaarde** geeft het aantal threads op dat wordt gebruikt voor gegevensoverdracht (initiële of deltareplicatie) van een schijf. Een hogere waarde verhoogt de netwerkbandbreedte die wordt gebruikt voor replicatie.
  * De **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM-registerwaarde** geeft het aantal threads op dat wordt gebruikt voor gegevensoverdracht tijdens failback.

### <a name="throttle-bandwidth"></a>Bandbreedte beperken

1. Open de Azure Backup MMC-module op de machine die u als processerver gebruikt. Standaard is er een snelkoppeling voor Back-up beschikbaar op het bureaublad of in de volgende map: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. Selecteer Eigenschappen **wijzigen**in de module .

    ![Schermafbeelding van de module MMC-module azure backup om eigenschappen te wijzigen](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Selecteer op het tabblad **Beperking** de **optie Beperking van internetbandbreedte gebruik inschakelen voor back-upbewerkingen.** Stel de limieten in voor werk- en niet-werkuren. Geldige bereiken zijn 512 Kbps tot 1.023 Mbps.

    ![Schermafbeelding van het dialoogvenster Azure Backup Properties](./media/site-recovery-vmware-to-azure/throttle2.png)

U kunt ook de cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) gebruiken om een beperking in te stellen. Hier volgt een voorbeeld:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** geeft aan dat er geen beperking is vereist.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>De netwerkbandbreedte voor een vm wijzigen

1. Ga in het register van de VM naar **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Als u het bandbreedteverkeer op een replicerende schijf wilt wijzigen, wijzigt u de waarde van **UploadThreadsPerVM**. Maak de sleutel als deze niet bestaat.
   * Als u de bandbreedte voor failbackverkeer van Azure wilt wijzigen, wijzigt u de waarde van **DownloadThreadsPerVM**.
2. De standaardwaarde voor elke toets is **4**. In netwerken met overprovisioning moeten deze registersleutels zodanig worden gewijzigd dat niet de standaardwaarden worden gebruikt. De maximale waarde die u gebruiken is **32**. Houd het verkeer in de gaten om de waarde te optimaliseren.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>De siteherstel-infrastructuur instellen om meer dan 500 VM's te beschermen

Voordat u de siteherstel-infrastructuur instelt, krijgt u toegang tot de omgeving om de volgende factoren te meten: compatibele virtuele machines, de dagelijkse snelheid van gegevenswijziging, de vereiste netwerkbandbreedte voor de RPO die u wilt bereiken, het aantal siteherstel onderdelen die nodig zijn en de tijd die nodig is om de eerste replicatie te voltooien. Voer de volgende stappen uit om de vereiste informatie te verzamelen:

1. Voer siteherstelplanner voor siteherstel op uw omgeving uit om deze parameters te meten. Zie Over Site [Recovery Deployment Planner voor VMware naar Azure](site-recovery-deployment-planner.md)voor nuttige richtlijnen.
2. Een configuratieserver implementeren die voldoet aan de [grootteaanbevelingen voor de configuratieserver.](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) Als uw productiewerkbelasting meer dan 650 virtuele machines bedraagt, implementeert u een andere configuratieserver.
3. Op basis van de gemeten dagelijkse gegevenswijzigingssnelheid implementeert u [scale-outprocesservers](vmware-azure-set-up-process-server-scale.md#download-installation-file) met behulp van [grootterichtlijnen.](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)
4. Als u verwacht dat de snelheid van gegevenswijziging voor een virtuele schijfmachine meer dan 2 MBps zal bedragen, moet u ervoor zorgen dat u premium beheerde schijven gebruikt. Site Recovery Deployment Planner wordt uitgevoerd voor een bepaalde periode. Pieken in de gegevenswijzigingssnelheid op andere momenten worden mogelijk niet vastgelegd in het rapport.
5. [Stel de netwerkbandbreedte](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) in op basis van de RPO die u wilt bereiken.
6. Wanneer de infrastructuur is ingesteld, schakelt u noodherstel in voor uw werkbelasting. Zie De [bronomgeving voor VMware instellen voor Azure-replicatie](vmware-azure-set-up-source.md)voor meer informatie.

## <a name="deploy-additional-process-servers"></a>Extra processervers implementeren

Als u uw implementatie verder uitschaalt dan 200 bronmachines of als u een totale dagelijkse churn rate van meer dan 2 TB hebt, moet u processervers toevoegen om het verkeersvolume te verwerken. We hebben het product in 9.24-versie verbeterd om [processerverwaarschuwingen](vmware-physical-azure-monitor-process-server.md#process-server-alerts) te geven over wanneer u een scale-outprocesserver moet instellen. [Stel de processerver in](vmware-azure-set-up-process-server-scale.md) om nieuwe bronmachines te beschermen of [de belasting in evenwicht te brengen.](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Machines migreren om de nieuwe processerver te gebruiken

1. Selecteer **Instellingen** > **Site Recovery-servers**. Selecteer de configuratieserver en vouw **processervers**uit.

    ![Schermafbeelding van het dialoogvenster Processerver](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Klik met de rechtermuisknop op de processerver die momenteel in gebruik is en selecteer **Switch**.

    ![Schermafbeelding van het dialoogvenster Configuratieserver](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. Selecteer **in De doelprocesserver selecteren**de nieuwe processerver die u wilt gebruiken. Selecteer vervolgens de virtuele machines die de server verwerkt. Als u informatie over de server wilt opvragen, selecteert u het informatiepictogram. Om u te helpen bij het nemen van belastingsbeslissingen, wordt de gemiddelde ruimte weergegeven die nodig is om elke geselecteerde virtuele machine te repliceren naar de nieuwe processerver. Selecteer het vinkje om te beginnen met repliceren naar de nieuwe processerver.

## <a name="deploy-additional-master-target-servers"></a>Extra hoofddoelservers implementeren

In de volgende scenario's is meer dan één hoofddoelserver vereist:

*   U wilt een linux-gebaseerde virtuele machine beschermen.
*   De hoofddoelserver die beschikbaar is op de configuratieserver heeft geen toegang tot de datastore van de VM.
*   Het totale aantal schijven op de hoofddoelserver (het aantal lokale schijven op de server plus het aantal te beveiligen schijven) is groter dan 60 schijven.

Zie [Een Linux-hoofddoelserver installeren voor failback voor](vmware-azure-install-linux-master-target.md)meer informatie over het toevoegen van een hoofddoelserver voor een virtuele linux-server.

Ga als lid van het hoofd doelserver voor een virtuele windows-machine:

1. Ga naar **Recovery Services Vault** > **Site Recovery Infrastructure** > **Configuration servers**.
2. Selecteer de vereiste configuratieserver en selecteer **Vervolgens Hoofddoelserver**.

    ![Schermafbeelding van de knop Hoofddoelserver toevoegen](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Download het uniforme installatiebestand en voer het bestand op de VM uit om de hoofddoelserver in te stellen.
4. Selecteer**Vervolgens** **het hoofddoel** > installeren .

    ![Schermafbeelding van het selecteren van de doeloptie Master-doel installeren](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Selecteer de standaardinstallatielocatie en selecteer **Installeren**.

     ![Schermafbeelding van de standaardinstallatielocatie](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Als u het hoofddoel wilt registreren bij de configuratieserver, selecteert **u Doorgaan naar configuratie**.

    ![Schermafbeelding van de knop Doorgaan naar configuratie](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Voer het IP-adres van de configuratieserver in en voer de wachtwoordzin in. Zie [Een wachtwoordzin voor configuratieserver genereren](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)voor meer informatie over het genereren van een wachtwoordzin. 

    ![Schermafbeelding van de plaats waar u het IP-adres en de wachtwoordzin voor de configuratieserver moet invoeren](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Selecteer **Registreren**. Wanneer de inschrijving is voltooid, selecteert u **Voltooien**.

Wanneer de registratie is voltooid, wordt de server weergegeven in de Azure-portal op **recovery services Vault** > **Site Recovery Infrastructure** > **Configuration Servers**, in de hoofddoelservers van de configuratieserver.

 > [!NOTE]
 > Download de nieuwste versie van het beheervan de [doelserver unified setup-bestand voor Windows.](https://aka.ms/latestmobsvc)

## <a name="next-steps"></a>Volgende stappen

Download en voer [siteherstelimplementatieplanner](https://aka.ms/asr-deployment-planner)uit .
