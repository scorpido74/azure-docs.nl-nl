---
title: IBM Db2 HADR instellen op virtuele Azure-machines (VM's) op RHEL | Microsoft Documenten
description: Stel een hoge beschikbaarheid van IBM Db2 LUW vast op RHEL (Azure Virtual Machines) RHEL.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.openlocfilehash: c6a230f6abeab45c56aab2db40b8b1defcc06d90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598694"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Hoge beschikbaarheid van IBM Db2 LUW in Azure-VM's in Red Hat Enterprise Linux Server

IBM Db2 voor Linux, UNIX en Windows (LUW) in [hadr-configuratie (high availability and disaster recovery)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) bestaat uit één knooppunt waarop een primaire database-instantie wordt uitgevoerd en ten minste één knooppunt waarop een secundaire database-instantie wordt uitgevoerd. Wijzigingen in de primaire database-instantie worden synchroon of asynchroon gerepliceerd naar een secundaire databaseinstantie, afhankelijk van uw configuratie. 

In dit artikel wordt beschreven hoe u de virtuele Azure-machines (VM's) implementeert en configureert, het clusterframework installeert en de IBM Db2 LUW met HADR-configuratie installeert. 

Het artikel gaat niet over het installeren en configureren van IBM Db2 LUW met HADR- of SAP-softwareinstallatie. Om u te helpen deze taken uit te voeren, bieden we verwijzingen naar SAP- en IBM-installatiehandleidingen. Dit artikel richt zich op onderdelen die specifiek zijn voor de Azure-omgeving. 

De ondersteunde IBM Db2-versies zijn 10,5 en hoger, zoals gedocumenteerd in SAP-notitie [1928533].

Raadpleeg de volgende SAP-notities en -documentatie voordat u met een installatie begint:

| SAP-notitie | Beschrijving |
| --- | --- |
| [1928533] | SAP-toepassingen op Azure: ondersteunde producten en Azure VM-typen |
| [2015553] | SAP op Azure: vereisten voor ondersteuning |
| [2178632] | Belangrijkste monitoringstatistieken voor SAP op Azure |
| [2191498] | SAP op Linux met Azure: verbeterde monitoring |
| [2243692] | Linux op Azure (IaaS) VM: SAP-licentieproblemen |
| [2002167] | Red Hat Enterprise Linux 7.x: Installatie en upgrade |
| [2694118] | Red Hat Enterprise Linux HA-add-on op Azure |
| [1999351] | Probleemoplossing voor verbeterde Azure-bewaking voor SAP |
| [2233094] | DB6: SAP-toepassingen op Azure die IBM Db2 voor Linux, UNIX en Windows gebruiken - aanvullende informatie |
| [1612105] | DB6: VEELGESTELDE VRAGEN over Db2 met HADR |


| Documentatie | 
| --- |
| [SAP Community Wiki:](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)Heeft alle vereiste SAP Notes voor Linux |
| [Azure Virtual Machines planning en implementatie voor SAP op Linux-handleiding][planning-guide] |
| [Azure Virtual Machines deployment voor SAP op Linux][deployment-guide] (dit artikel) |
| [Azure Virtual Machines database management system (DBMS) deployment for SAP on Linux guide Azure Virtual Machines database management system (DBMS) deployment for SAP on Linux guide Azure Virtual Machines database management system (DBMS) deployment for SAP on Linux][dbms-guide] guide Azure Virtual |
| [SAP-werkbelasting op azure-plannings- en implementatiechecklist][azr-sap-plancheck] |
| [Overzicht van de high availability add-on voor Red Hat Enterprise Linux 7][rhel-ha-addon] |
| [Add-On-beheer met hoge beschikbaarheid][rhel-ha-admin] |
| [Invoegtoepassing met hoge beschikbaarheid][rhel-ha-ref] |
| [Ondersteuningsbeleid voor RHEL-clusters met hoge beschikbaarheid - Microsoft Azure Virtual Machines als clusterleden][rhel-azr-supp]
| [Een Red Hat Enterprise Linux 7.4 (en hoger) cluster met hoge beschikbaarheid installeren en configureren op Microsoft Azure][rhel-azr-inst]
| [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor IBM Db2][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [Ondersteuningsbeleid voor RHEL-clusters met hoge beschikbaarheid - Beheer van IBM Db2 voor Linux, Unix en Windows in een cluster][rhel-db2-supp]



## <a name="overview"></a>Overzicht
Om een hoge beschikbaarheid te bereiken, is IBM Db2 LUW met HADR geïnstalleerd op ten minste twee virtuele Azure-machines, die worden geïmplementeerd in een [Azure-beschikbaarheidsset](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) of in [Azure Availability Zones.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) 

Op de volgende afbeeldingen worden twee Azure VM's van databaseserver weergegeven. Beide databaseserver Azure VM's hebben hun eigen opslag gekoppeld en zijn up and running. In HADR heeft één database-instantie in een van de Azure VM's de rol van de primaire instantie. Alle clients zijn verbonden met primaire instantie. Alle wijzigingen in databasetransacties blijven lokaal bestaan in het Db2-transactielogboek. Aangezien de transactielogboekrecords lokaal worden gehandhaafd, worden de records via TCP/IP overgebracht naar de database-instantie op de tweede databaseserver, de stand-byserver of stand-byinstantie. De stand-by-instantie werkt de lokale database bij door de overgedragen transactielogboekrecords door te sturen. Op deze manier wordt de stand-by server gesynchroniseerd met de primaire server.

HADR is slechts een replicatiefunctionaliteit. Het heeft geen storingdetectie en geen automatische overname- of failoverfaciliteiten. Een overname of overdracht naar de stand-byserver moet handmatig worden gestart door een databasebeheerder. Om een automatische overname- en storingsdetectie te bereiken, u de clusterfunctie Linux Pacemaker gebruiken. Pacemaker controleert de twee databaseserverexemplaren. Wanneer de primaire databaseserverinstantie vastloopt, start Pacemaker een *automatische* HADR-overname door de stand-byserver. Pacemaker zorgt er ook voor dat het virtuele IP-adres wordt toegewezen aan de nieuwe primaire server.

![IBM Db2 overzicht van hoge beschikbaarheid](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Als u SAP-toepassingsservers verbinding wilt laten maken met de primaire database, hebt u een virtuele hostnaam en een virtueel IP-adres nodig. In het geval van een failover maken de SAP-toepassingsservers verbinding met nieuwe primaire database-instantie. In een Azure-omgeving moet een [Azure-load balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) een virtueel IP-adres gebruiken op de manier die vereist is voor HADR van IBM Db2. 

Om u volledig te helpen begrijpen hoe IBM Db2 LUW met HADR en Pacemaker past in een zeer beschikbare SAP-systeemsetup, geeft de volgende afbeelding een overzicht van een zeer beschikbare installatie van een SAP-systeem op basis van ibm Db2-database. Dit artikel heeft alleen betrekking op IBM Db2, maar het biedt verwijzingen naar andere artikelen over het opzetten van andere componenten van een SAP-systeem.

![IBM DB2 volledige omgevingsoverzicht met hoge beschikbaarheid](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Overzicht op hoog niveau van de vereiste stappen
Als u een IBM Db2-configuratie wilt implementeren, moet u de volgende stappen volgen:

  + Plan uw omgeving.
  + Implementeer de VM's.
  + Update RHEL Linux en configureer bestandssystemen.
  + Pacemaker installeren en configureren.
  + [Glusterfs-cluster][glusterfs] of [Azure NetApp-bestanden instellen][anf-rhel]
  + Installeer [ASCS/ERS op een apart cluster][ascs-ha-rhel].
  + Installeer IBM Db2-database met distributed/high availability option (SWPM).
  + Installeer en maak een secundair databaseknooppunt en -instantie en configureer HADR.
  + Bevestig dat HADR werkt.
  + Pas de pacemakerconfiguratie toe om IBM Db2 te bedienen.
  + Azure Load Balancer configureren.
  + Primaire en dialoogtoepassingsservers installeren.
  + Controleer en pas de configuratie van SAP-toepassingsservers aan.
  + Voer failover- en overnametests uit.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Azure-infrastructuur plannen voor hosting van IBM Db2 LUW met HADR

Voltooi het planningsproces voordat u de implementatie uitvoert. Planning bouwt de basis voor het implementeren van een configuratie van Db2 met HADR in Azure. De belangrijkste elementen die deel moeten uitmaken van de planning voor IMB Db2 LUW (databaseonderdeel van de SAP-omgeving) worden in de volgende tabel vermeld:

| Onderwerp | Korte beschrijving |
| --- | --- |
| Azure-brongroepen definiëren | Resourcegroepen waarbij u VM, VNet, Azure Load Balancer en andere resources implementeert. Kan bestaand of nieuw zijn. |
| Virtuele netwerk / Subnetdefinitie | Waar VM's voor IBM Db2 en Azure Load Balancer worden geïmplementeerd. Kan bestaan of nieuw worden gemaakt. |
| Virtuele machines die IBM Db2 LUW hosten | VM-grootte, opslag, netwerken, IP-adres. |
| Virtuele hostnaam en virtuele IP voor IBM Db2-database| De virtuele IP- of hostnaam die wordt gebruikt voor de verbinding van SAP-toepassingsservers. **db-virt-hostname**, **db-virt-ip**. |
| Azure-schermen | Methode om gespleten hersensituaties te voorkomen wordt voorkomen. |
| Azure Load Balancer | Gebruik van Basic of Standard (aanbevolen), sondepoort voor Db2-database (onze aanbeveling 62500) **sonde-poort**. |
| Naamomzetting| Hoe naamomzetting werkt in de omgeving. DNS-service wordt ten zeerste aanbevolen. Het bestand lokale hosts kan worden gebruikt. |
    
Zie Pacemaker instellen op Red [Hat Enterprise Linux instellen in Azure][rhel-pcs-azr]voor meer informatie over Linux Pacemaker in Azure.

## <a name="deployment-on-red-hat-enterprise-linux"></a>Implementatie op Red Hat Enterprise Linux

De resource agent voor IBM Db2 LUW is opgenomen in Red Hat Enterprise Linux Server HA Addon. Voor de installatie die in dit document wordt beschreven, moet u Red Hat Enterprise Linux gebruiken voor SAP. De Azure Marketplace bevat een afbeelding voor Red Hat Enterprise Linux 7.4 voor SAP of hoger die u gebruiken om nieuwe Virtuele Azure-machines te implementeren. Wees op de hoogte van de verschillende ondersteunings- of servicemodellen die Red Hat via de Azure Marketplace aanbiedt wanneer u een VM-afbeelding kiest in de Azure VM Marketplace.

### <a name="hosts-dns-updates"></a>Hosts: DNS-updates
Maak een lijst met alle hostnamen, inclusief namen van virtuele hosten, en werk uw DNS-servers bij om een goed IP-adres in te schakelen voor de oplossing van de hostnaam. Als een DNS-server niet bestaat of als u geen DNS-vermeldingen bijwerken en maken, moet u de lokale hostbestanden van de afzonderlijke VM's gebruiken die aan dit scenario deelnemen. Als u hostbestanden gebruikt, moet u ervoor zorgen dat de vermeldingen worden toegepast op alle VM's in de SAP-systeemomgeving. We raden u echter aan uw DNS te gebruiken die zich idealiter uitstrekt tot Azure


### <a name="manual-deployment"></a>Handmatige implementatie

Zorg ervoor dat het geselecteerde besturingssysteem wordt ondersteund door IBM/SAP voor IBM Db2 LUW. De lijst met ondersteunde OS-versies voor Azure VM's en Db2-releases is beschikbaar in SAP note [1928533]. De lijst met OS-releases van afzonderlijke Db2-releases is beschikbaar in de SAP-productbeschikbaarheidsmatrix. We raden een minimum aan Red Hat Enterprise Linux 7.4 voor SAP aan vanwege Azure-gerelateerde prestatieverbeteringen in deze of latere Red Hat Enterprise Linux-versies.

1. Een resourcegroep maken of selecteren.
1. Een virtueel netwerk en subnet maken of selecteren.
1. Maak een Azure-beschikbaarheidsset of implementeer een beschikbaarheidszone.
    + Stel voor de beschikbaarheidsset de maximale updatedomeinen in op 2.
1. Virtuele machine maken 1.
    + Gebruik Red Hat Enterprise Linux voor SAP-afbeelding in de Azure Marketplace.
    + Selecteer de Azure-beschikbaarheidsset die u in stap 3 hebt gemaakt of selecteer Beschikbaarheidszone.
1.  Virtuele machine maken 2.
    + Gebruik Red Hat Enterprise Linux voor SAP-afbeelding in de Azure Marketplace.
    + Selecteer de Azure-beschikbaarheidsset waarin u bent gemaakt in stap 3 of selecteer Beschikbaarheidszone (niet dezelfde zone als in stap 3).
1. Voeg gegevensschijven toe aan de VM's en controleer de aanbeveling van een bestandssysteemsetup in het artikel [IBM Db2 Azure Virtual Machines DBMS-implementatie voor SAP-workload][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Het cluster Pacemaker maken
    
Zie Pacemaker instellen op Red Hat Enterprise [Linux instellen in Azure][rhel-pcs-azr]als u een basiscluster met pacemaker voor deze IBM Db2-server wilt maken. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Installeer de IBM Db2 LUW en SAP-omgeving

Voordat u begint met de installatie van een SAP-omgeving op basis van IBM Db2 LUW, bekijkt u de volgende documentatie:

+ Azure-documentatie
+ SAP-documentatie
+ IBM-documentatie

Links naar deze documentatie vindt u in het inleidende gedeelte van dit artikel.

Bekijk de SAP-installatiehandleidingen voor het installeren van NetWeaver-gebaseerde toepassingen op IBM Db2 LUW.
U de gidsen vinden op de SAP Help-portal met behulp van de [SAP Installation Guide Finder.][sap-instfind]

U het aantal hulplijnen dat in de portal wordt weergegeven, verminderen door de volgende filters in te stellen:
- Ik wil: "Installeer een nieuw systeem"
- Mijn database: "IBM Db2 voor Linux, Unix en Windows"
- Extra filters voor SAP NetWeaver-versies, stackconfiguratie of besturingssysteem

#### <a name="red-hat-firewall-rules"></a>Red Hat firewall regels
Red Hat Enterprise Linux heeft standaard een firewall ingeschakeld. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Installatietips voor het opzetten van IBM Db2 LUW met HADR

Ga als het gaat om het instellen van de primaire IBM Db2 LUW-database:

- Gebruik de optie hoge beschikbaarheid of gedistribueerde optie.
- Installeer de instantie SAP ASCS/ERS en Database.
- Neem een back-up van de nieuw geïnstalleerde database.

> [!IMPORTANT] 
> Noteer de "Database Communicatie poort" die is ingesteld tijdens de installatie. Het moet hetzelfde poortnummer zijn voor beide database-exemplaren.
>![DEFINITIE van SAP SWPM-poort](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>IBM Db2 HADR-instellingen voor Azure

   Wanneer u een Azure Pacemaker-afrasteringsagent gebruikt, stelt u de volgende parameters in:

   - HADR-peervensterduur (seconden) (HADR_PEER_WINDOW) = 240  
   - HADR-time-outwaarde (HADR_TIMEOUT) = 45

Wij raden de voorgaande parameters op basis van de eerste failover / overname testen. Het is verplicht dat u test op de juiste functionaliteit van failover en overname met deze parameterinstellingen. Omdat afzonderlijke configuraties kunnen variëren, moeten de parameters mogelijk worden aangepast. 

> [!NOTE]
> Specifiek voor IBM Db2 met HADR-configuratie bij normaal opstarten: de secundaire of stand-by database-instantie moet operationeel zijn voordat u de primaire database-instantie starten.

   
> [!NOTE]
> Voor installatie en configuratie die specifiek is voor Azure en Pacemaker: Tijdens de installatieprocedure via SAP Software Provisioning Manager is er een expliciete vraag over hoge beschikbaarheid voor IBM Db2 LUW:
>+ Selecteer **geen IBM Db2 pureScale.**
>+ Selecteer niet **IBM Tivoli System Automation installeren voor multiplatforms.**
>+ Selecteer **clusterconfiguratiebestanden genereren**niet .
>![SAP SWPM - DB2 HA opties](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Voer de volgende stappen uit om de stand-bydatabaseserver in te stellen met behulp van de SAP homogene systeemkopieerprocedure:

1. Selecteer de optie **Systeemkopie** > **Doelsystemen** > **Gedistribueerde** > **database instantie**.
1. Selecteer als kopieermethode **Homogeen systeem,** zodat u back-up gebruiken om een back-up op de stand-byserverinstantie te herstellen.
1. Wanneer u de uitgangsstap bereikt om de database te herstellen voor homogene systeemkopie, verlaat u de installatieprogramma. Herstel de database vanuit een back-up van de primaire host. Alle volgende installatiefasen zijn al uitgevoerd op de primaire databaseserver.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Red Hat firewall regels voor DB2 HADR
Firewallregels toevoegen om verkeer naar DB2 en tussen DB2 te laten werken:
+ Database communicatie poort. Als u partities gebruikt, voegt u deze poorten ook toe.
+ HADR-poort (waarde van DE parameter DB2 HADR_LOCAL_SVC)
+ Azure-sondepoort
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR check
Voor demonstratiedoeleinden en de procedures die in dit artikel worden beschreven, is de database SID **ID2**.

Nadat u HADR hebt geconfigureerd en de status PEER en VERBONDEN op de primaire en stand-byknooppunten is, voert u de volgende controle uit:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N



#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N

</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker-configuratie

Wanneer u Pacemaker gebruikt voor automatische failover in het geval van een knooppuntstoring, moet u uw Db2-exemplaren en pacemaker dienovereenkomstig configureren. In deze sectie wordt dit type configuratie beschreven.

De volgende items zijn vooraf vastgelegd met:

- **[A]**: Van toepassing op alle knooppunten
- **[1]**: Alleen van toepassing op knooppunt 1 
- **[2]**: Alleen van toepassing op knooppunt 2

**[A]** Voorwaarde voor pacemakerconfiguratie:
1. Sluit beide databaseservers met\<db2 sid> van de gebruiker af met db2stop.
1. Wijzig de shell-omgeving\<voor db2 sid> gebruiker in */bin/ksh:*
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Pacemakerconfiguratie

**[1]** IBM Db2 HADR-specifieke Pacemaker-configuratie:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** Ibm Db2-bronnen maken:
<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]** Start IBM Db2 resources:
* Zet Pacemaker uit de onderhoudsmodus.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk welk knooppunt de resources draaien.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Volledige lijst met bronnen:

 rsc_st_azure (stonith:fence_azure_arm): Gestart met az-idb01 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2] Masters: [ az-idb01 ] Slaves: [ az-idb02 ] Resource Group: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (ocf::heartbeat:IPaddr2): Gestart met az-idb01 nc_db2id2_ID2 (ocf::heartbeatdb:azure-lb): Gestart met az-i01

Daemon Status: corosync: active/disabled pacemaker: active/disabled pcsd: active/enabled
</pre>

> [!IMPORTANT]
> U moet de geclusterde Db2-instantie met pacemakerbeheer beheren met behulp van pacemakergereedschappen. Als u db2-opdrachten zoals db2stop gebruikt, detecteert Pacemaker de actie als een fout van de bron. Als u onderhoud uitvoert, u de knooppunten of resources in de onderhoudsmodus zetten. Pacemaker schort bewakingsbronnen op en u vervolgens normale db2-beheeropdrachten gebruiken.


### <a name="configure-azure-load-balancer"></a>Azure Load Balancer configureren
Als u Azure Load Balancer wilt configureren, raden we u aan de [SKU azure standard load balancer te](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) gebruiken en vervolgens het volgende te doen.

> [!NOTE]
> De Standard Load Balancer SKU heeft beperkingen toegang tot openbare IP-adressen van de knooppunten onder de Load Balancer. In het artikel [Public endpoint-connectiviteit voor virtuele machines met Azure Standard Load Balancer in SAP-scenario's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) met hoge beschikbaarheid wordt beschreven hoe u deze knooppunten toegang geven tot openbare IP-adressen



1. Een ip-pool aan de voorkant maken:

   a. Open in de Azure-portal de Azure Load Balancer, selecteer **frontend IP-groep**en selecteer **Toevoegen**.

   b. Voer de naam in van de nieuwe front-end IP-pool (bijvoorbeeld **Db2-verbinding).**

   c. Stel de **toewijzing** in **op Statisch**en voer het **IP-adres in dat** aan het begin is gedefinieerd.

   d. Selecteer **OK**.

   e. Nadat de nieuwe front-end IP-pool is gemaakt, moet u het IP-adres van de groep noteren.

1. Maak een back-endpool:

   a. Open in de Azure-portal de Azure Load Balancer, selecteer **backendpools**en selecteer **Vervolgens Toevoegen**.

   b. Voer de naam in van de nieuwe back-endpool (bijvoorbeeld **Db2-backend).**

   c. Selecteer **Een virtuele machine toevoegen**.

   d. Selecteer de beschikbaarheidsset of de virtuele machines die hosten als IBM Db2-database die in de vorige stap is gemaakt.

   e. Selecteer de virtuele machines van het IBM Db2-cluster.

   f. Selecteer **OK**.

1. Maak een statussonde:

   a. Open in de Azure-portal de Azure Load Balancer, selecteer **statussondes**en selecteer **Toevoegen**.

   b. Voer de naam van de nieuwe health probe (bijvoorbeeld **Db2-hp**).

   c. Selecteer **TCP** als protocol en poort **62500**. Houd de **intervalwaarde** ingesteld op **5**en houd de waarde van de **drempelwaarde Niet in orde** ingesteld op **2**.

   d. Selecteer **OK**.

1. Maak de regels voor het balanceren van de lastenverdeling:

   a. Open in de Azure-portal de Azure Load Balancer, selecteer **Taakbalanceringsregels**en selecteer **Toevoegen**.

   b. Voer de naam in van de nieuwe regel Load Balancer (bijvoorbeeld **Db2-SID).**

   c. Selecteer het front-end IP-adres, de back-endpool en de statussonde die u eerder hebt gemaakt (bijvoorbeeld **Db2-frontend).**

   d. Houd de **protocolset** op **TCP**en voer de *poortdatabasecommunicatiepoort in*.

   e. Verhoog de **idle time-out** tot 30 minuten.

   f. Zorg ervoor dat **zwevend IP wordt ingeschakeld.**

   g. Selecteer **OK**.

**[A]** Firewallregel toevoegen voor sondepoort:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Wijzigingen aanbrengen in SAP-profielen om virtuele IP te gebruiken voor verbinding
Als u verbinding wilt maken met de primaire instantie van de HADR-configuratie, moet de SAP-toepassingslaag het virtuele IP-adres gebruiken dat u hebt gedefinieerd en geconfigureerd voor de Azure Load Balancer. De volgende wijzigingen zijn vereist:

/sapmnt/\<SID>/profile/DEFAULT. PFL (PFL)
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Primaire en dialoogvenstertoepassingsservers installeren

Wanneer u primaire en dialoogvenstertoepassingsservers installeert tegen een Db2 HADR-configuratie, gebruikt u de virtuele hostnaam die u voor de configuratie hebt gekozen. 

Als u de installatie hebt uitgevoerd voordat u de DB2 HADR-configuratie hebt gemaakt, voert u de wijzigingen aan zoals beschreven in de vorige sectie en als volgt voor SAP Java-stapels.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP+Java- of Java-stacksystemen JDBC URL-controle

Gebruik het gereedschap J2EE Config om de JDBC-URL te controleren of bij te werken. Omdat de J2EE Config tool een grafisch hulpmiddel is, moet u de X-server hebben geïnstalleerd:
 
1. Meld u aan bij de primaire toepassingsserver van de J2EE-instantie en voer het uitvoeren uit:
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. Kies in het linkerframe **het beveiligingsarchief**.
1. Kies in het juiste `jdbc/pool/\<SAPSID>/url`frame de sleutel.
1. Wijzig de hostnaam in de JDBC-URL in de naam van de virtuele host.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. Selecteer **Toevoegen**.
1. Als u de wijzigingen wilt opslaan, selecteert u het schijfpictogram linksboven.
1. Sluit het configuratiegereedschap.
1. Start de Java-instantie opnieuw.

## <a name="configure-log-archiving-for-hadr-setup"></a>Logboekarchivering configureren voor HADR-installatie
Als u de Db2-logboekarchivering wilt configureren voor hadr-instellingen, raden we u aan zowel de primaire als de stand-bydatabase te configureren om automatische logboekophaalmogelijkheden te hebben van alle logarchieflocaties. Zowel de primaire als de stand-bydatabase moeten logarchiefbestanden kunnen ophalen uit alle logarchieflocaties waar naar een van de database-instanties logbestanden kan worden gearchiveerd. 

De logboekarchivering wordt alleen uitgevoerd door de primaire database. Als u de HADR-rollen van de databaseservers wijzigt of als er een fout optreedt, is de nieuwe primaire database verantwoordelijk voor logboekarchivering. Als u meerdere logarchieflocaties hebt ingesteld, worden uw logboeken mogelijk twee keer gearchiveerd. In het geval van een lokale of externe inhaalslag moet u de gearchiveerde logboeken mogelijk ook handmatig kopiëren van de oude primaire server naar de actieve logboeklocatie van de nieuwe primaire server.

We raden u aan een gemeenschappelijk NFS-aandeel of GlusterFS te configureren, waarbij logboeken vanaf beide knooppunten worden geschreven. Het NFS-aandeel of GlusterFS moet zeer beschikbaar zijn. 

U bestaande zeer beschikbare NFS-shares of GlusterFS gebruiken voor transporten of een profielmap. Zie voor meer informatie:

- [GlusterFS op Azure VM's op Red Hat Enterprise Linux voor SAP NetWeaver][glusterfs] 
- [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op Red Hat Enterprise Linux met Azure NetApp-bestanden voor SAP-toepassingen][anf-rhel]
- [Azure NetApp-bestanden](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (nfs-shares maken)

## <a name="test-the-cluster-setup"></a>De clusterinstelling testen

In dit gedeelte wordt beschreven hoe u uw Db2 HADR-installatie testen. Elke test gaat ervan uit dat IBM Db2 primair draait op de *virtuele az-idb01-machine.* Gebruiker met sudo-bevoegdheden of root (niet aanbevolen) moet worden gebruikt.

De initiële status voor alle testcases wordt hier uitgelegd: (crm_mon -r- of pcs-status)

- **pcs-status** is een momentopname van de pacemakerstatus bij de uitvoering 
- **crm_mon -r** is continue output van pacemakerstatus

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

De oorspronkelijke status in een SAP-systeem wordt gedocumenteerd in Transaction DBACOCKPIT > Configuration > Overview, zoals weergegeven in de volgende afbeelding:

![DBACockpit - Pre Migratie](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Test overname van IBM Db2


> [!IMPORTANT] 
> Controleer voordat u met de test begint:
> * Pacemaker heeft geen mislukte acties (pcs-status).
> * Er zijn geen locatiebeperkingen (restjes migratietest)
> * De IBM Db2 HADR synchronisatie werkt. Neem contact op\<met gebruiker db2 sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migreer het knooppunt waarop de primaire Db2-database wordt uitgevoerd door de volgende opdracht uit te voeren:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

Nadat de migratie is uitgevoerd, ziet de crm-statusuitvoer eruit als volgt:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

De oorspronkelijke status in een SAP-systeem wordt gedocumenteerd in Transaction DBACOCKPIT > Configuration > Overview, zoals weergegeven in de volgende afbeelding:

![DBACockpit - Post Migratie](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

Resourcemigratie met 'pcs-bronverplaatsing' zorgt voor locatiebeperkingen. Locatiebeperkingen in dit geval verhinderen het uitvoeren van IBM Db2-instantie op az-idb01. Als locatiebeperkingen niet worden verwijderd, kan de bron niet terugvallen.

Verwijder de locatie beperken en stand-by knooppunt zal worden gestart op az-idb01.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
En wijzigingen in de clusterstatus in:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit - Verwijderde locatiebeperken](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Migreer de resource terug naar *az-idb01* en maak de locatiebeperkingen vrij
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **pcs resource \<move <host>res_name>:** Creëert locatiebeperkingen en kan problemen veroorzaken bij overname
- **pcs-bron \<duidelijk res_name>**: Wist locatiebeperkingen
- **pcs resource \<cleanup res_name>: **Ruimt alle fouten van de bron

### <a name="test-a-manual-takeover"></a>Test een handmatige overname

U een handmatige overname testen door de *Pacemaker-service op het az-idb01-knooppunt* te stoppen:
<pre><code>systemctl stop pacemaker</code></pre>

status op *az-ibdb02*
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

Na de failover u de service opnieuw starten op *az-idb01.*
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Dood het Db2-proces op het knooppunt waarop de hadr primaire database wordt uitgevoerd

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

De Db2-instantie gaat mislukken en Pacemaker verplaatst het hoofdknooppunt en rapporteert de volgende status:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Pacemaker start de primaire database-instantie Db2 opnieuw op hetzelfde knooppunt of het mislukt niet naar het knooppunt waarop de secundaire database-instantie wordt uitgevoerd en er wordt een fout gerapporteerd.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Dood het Db2-proces op het knooppunt waarop de secundaire database-instantie wordt uitgevoerd

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Het knooppunt krijgt in mislukte vermeld en fout gemeld
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

De instantie Db2 wordt opnieuw gestart in de secundaire rol die hij eerder had toegewezen.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Stop DB via db2stop-kracht op het knooppunt waarop de primaire database-instantie HADR wordt uitgevoerd

Als gebruiker\<db2 sid> uitvoeren opdracht db2stop kracht:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Fout gedetecteerd:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

De db2 HADR secundaire database instantie werd gepromoveerd in de primaire rol.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Crash de VM waarop de primaire HADR-database-instantie wordt uitgevoerd met 'halt'

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

In een dergelijk geval detecteert Pacemaker dat het knooppunt waarop de primaire database-instantie wordt uitgevoerd, niet reageert.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

De volgende stap is om te controleren op een *Split hersensituatie.* Nadat het overlevende knooppunt heeft vastgesteld dat het knooppunt dat de primaire databaseinstantie voor het laatst heeft uitgevoerd, is uitgeschakeld, wordt een failover van resources uitgevoerd.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


In het geval van een kernel paniek, zal de mislukte knoop worden uitgerust door hekwerk agent. Nadat het mislukte knooppunt weer online is, moet u het pacemakercluster starten door
<pre><code>sudo pcs cluster start</code></pre> het begint de Instantie Db2 in de secundaire rol.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>Volgende stappen
- [Architectuur en scenario's met hoge beschikbaarheid voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Pacemaker instellen op Red Hat Enterprise Linux in Azure][rhel-pcs-azr]
