---
title: IBM Db2 HADR instellen op virtuele Azure-machines (VM's) | Microsoft Documenten
description: Stel een hoge beschikbaarheid van IBM Db2 LUW vast op virtuele Azure-machines (VM's).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2020
ms.author: juergent
ms.openlocfilehash: 614ac8b651224a3b6ec605a6bffd520449a1d793
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926748"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Hoge beschikbaarheid van IBM Db2 LUW op Azure VM's op SUSE Linux Enterprise Server met Pacemaker

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
| [1984787] | SUSE LINUX Enterprise Server 12: Installatienotities |
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
| [SUSE Linux Enterprise Server voor SAP Applications 12 SP4 best practices handleidingen][sles-for-sap-bp] |
| [SUSE Linux Enterprise High Availability Extension 12 SP4][sles-ha-guide] |
| [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor IBM Db2][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Overzicht
Om een hoge beschikbaarheid te bereiken, is IBM Db2 LUW met HADR geïnstalleerd op ten minste twee virtuele Azure-machines, die worden geïmplementeerd in een [Azure-beschikbaarheidsset](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) of in [Azure Availability Zones.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) 

Op de volgende afbeeldingen worden twee Azure VM's van databaseserver weergegeven. Beide databaseserver Azure VM's hebben hun eigen opslag gekoppeld en zijn up and running. In HADR heeft één database-instantie in een van de Azure VM's de rol van de primaire instantie. Alle clients zijn verbonden met deze primaire instantie. Alle wijzigingen in databasetransacties blijven lokaal bestaan in het Db2-transactielogboek. Aangezien de transactielogboekrecords lokaal worden gehandhaafd, worden de records via TCP/IP overgebracht naar de database-instantie op de tweede databaseserver, de stand-byserver of stand-byinstantie. De stand-by-instantie werkt de lokale database bij door de overgedragen transactielogboekrecords door te sturen. Op deze manier wordt de stand-by server gesynchroniseerd met de primaire server.

HADR is slechts een replicatiefunctionaliteit. Het heeft geen storingdetectie en geen automatische overname- of failoverfaciliteiten. Een overname of overdracht naar de stand-byserver moet handmatig worden gestart door een databasebeheerder. Om een automatische overname- en storingsdetectie te bereiken, u de clusterfunctie Linux Pacemaker gebruiken. Pacemaker controleert de twee databaseserverexemplaren. Wanneer de primaire databaseserverinstantie vastloopt, start Pacemaker een *automatische* HADR-overname door de stand-byserver. Pacemaker zorgt er ook voor dat het virtuele IP-adres wordt toegewezen aan de nieuwe primaire server.

![IBM Db2 overzicht van hoge beschikbaarheid](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Als u SAP-toepassingsservers verbinding wilt laten maken met de primaire database, hebt u een virtuele hostnaam en een virtueel IP-adres nodig. In het geval van een failover maken de SAP-toepassingsservers verbinding met nieuwe primaire database-instantie. In een Azure-omgeving moet een [Azure-load balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) een virtueel IP-adres gebruiken op de manier die vereist is voor HADR van IBM Db2. 

Om u volledig te helpen begrijpen hoe IBM Db2 LUW met HADR en Pacemaker past in een zeer beschikbare SAP-systeemsetup, geeft de volgende afbeelding een overzicht van een zeer beschikbare installatie van een SAP-systeem op basis van ibm Db2-database. Dit artikel heeft alleen betrekking op IBM Db2, maar het biedt verwijzingen naar andere artikelen over het opzetten van andere componenten van een SAP-systeem.

![IBM DB2 volledige omgevingsoverzicht met hoge beschikbaarheid](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Overzicht op hoog niveau van de vereiste stappen
Als u een IBM Db2-configuratie wilt implementeren, moet u de volgende stappen volgen:

  + Plan uw omgeving.
  + Implementeer de VM's.
  + SUSE Linux bijwerken en bestandssystemen configureren.
  + Pacemaker installeren en configureren.
  + Installeer [zeer beschikbare NFS.][nfs-ha]
  + Installeer [ASCS/ERS op een apart cluster][ascs-ha].
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
| Azure-schermen | Azure hekwerk of SBD schermen (sterk aanbevolen). Methode om gespleten hersensituaties te voorkomen. |
| SBD VM | SBD virtuele machine grootte, opslag, netwerk. |
| Azure Load Balancer | Gebruik van Basic of Standard (aanbevolen), sondepoort voor Db2-database (onze aanbeveling 62500) **sonde-poort**. |
| Naamomzetting| Hoe naamomzetting werkt in de omgeving. DNS-service wordt ten zeerste aanbevolen. Het bestand lokale hosts kan worden gebruikt. |
    
Zie [Pacemaker instellen op SUSE Linux Enterprise Server instellen in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)voor meer informatie over Linux Pacemaker in Azure.

## <a name="deployment-on-suse-linux"></a>Implementatie op SUSE Linux

De resource agent voor IBM Db2 LUW is opgenomen in SUSE Linux Enterprise Server voor SAP Applications. Voor de installatie die in dit document wordt beschreven, moet u SUSE Linux Server voor SAP-toepassingen gebruiken. De Azure Marketplace bevat een afbeelding voor SUSE Enterprise Server voor SAP-toepassingen 12 die u gebruiken om nieuwe virtuele Azure-machines te implementeren. Wees op de hoogte van de verschillende ondersteunings- of servicemodellen die Door SUSE worden aangeboden via de Azure Marketplace wanneer u een VM-afbeelding kiest in de Azure VM Marketplace. 

### <a name="hosts-dns-updates"></a>Hosts: DNS-updates
Maak een lijst met alle hostnamen, inclusief namen van virtuele hosten, en werk uw DNS-servers bij om een goed IP-adres in te schakelen voor de oplossing van de hostnaam. Als een DNS-server niet bestaat of als u geen DNS-vermeldingen bijwerken en maken, moet u de lokale hostbestanden van de afzonderlijke VM's gebruiken die aan dit scenario deelnemen. Als u hostbestanden gebruikt, moet u ervoor zorgen dat de vermeldingen worden toegepast op alle VM's in de SAP-systeemomgeving. We raden u echter aan uw DNS te gebruiken die zich idealiter uitstrekt tot Azure


### <a name="manual-deployment"></a>Handmatige implementatie

Zorg ervoor dat het geselecteerde besturingssysteem wordt ondersteund door IBM/SAP voor IBM Db2 LUW. De lijst met ondersteunde OS-versies voor Azure VM's en Db2-releases is beschikbaar in SAP note [1928533]. De lijst met OS-releases van afzonderlijke Db2-releases is beschikbaar in de SAP-productbeschikbaarheidsmatrix. We raden ten zeerste een minimum aan SLES 12 SP4 aan vanwege Azure-gerelateerde prestatieverbeteringen in deze of latere SUSE Linux-versies.

1. Een resourcegroep maken of selecteren.
1. Een virtueel netwerk en subnet maken of selecteren.
1. Maak een Azure-beschikbaarheidsset of implementeer een beschikbaarheidszone.
    + Stel voor de beschikbaarheidsset de maximale updatedomeinen in op 2.
1. Virtuele machine maken 1.
    + Gebruik SLES voor SAP-afbeelding in de Azure Marketplace.
    + Selecteer de Azure-beschikbaarheidsset die u in stap 3 hebt gemaakt of selecteer Beschikbaarheidszone.
1.  Virtuele machine maken 2.
    + Gebruik SLES voor SAP-afbeelding in de Azure Marketplace.
    + Selecteer de Azure-beschikbaarheidsset waarin u bent gemaakt in stap 3 of selecteer Beschikbaarheidszone (niet dezelfde zone als in stap 3).
1. Voeg gegevensschijven toe aan de VM's en controleer de aanbeveling van een bestandssysteemsetup in het artikel [IBM Db2 Azure Virtual Machines DBMS-implementatie voor SAP-workload][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Het cluster Pacemaker maken
    
Zie [Pacemaker instellen op SUSE Linux Enterprise Server instellen in Azure][sles-pacemaker]als u een basiscluster met pacemaker voor deze IBM Db2-server wilt maken. 

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

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Installatietips voor het opzetten van IBM Db2 LUW met HADR

Ga als het gaat om het instellen van de primaire IBM Db2 LUW-database:

- Gebruik de optie hoge beschikbaarheid of gedistribueerde optie.
- Installeer de instantie SAP ASCS/ERS en Database.
- Neem een back-up van de nieuw geïnstalleerde database.


> [!IMPORTANT] 
> Noteer de "Database Communicatie poort" die is ingesteld tijdens de installatie. Het moet hetzelfde poortnummer zijn voor beide database-exemplaren

Voer de volgende stappen uit om de stand-bydatabaseserver in te stellen met behulp van de SAP homogene systeemkopieerprocedure:

1. Selecteer de optie **Systeemkopie** > **Doelsystemen** > **Gedistribueerde** > **database instantie**.
1. Selecteer als kopieermethode **Homogeen systeem,** zodat u back-up gebruiken om een back-up op de stand-byserverinstantie te herstellen.
1. Wanneer u de uitgangsstap bereikt om de database te herstellen voor homogene systeemkopie, verlaat u de installatieprogramma. Herstel de database vanuit een back-up van de primaire host. Alle volgende installatiefasen zijn al uitgevoerd op de primaire databaseserver.
1. HADR instellen voor IBM Db2.

   > [!NOTE]
   > Voor installatie en configuratie die specifiek is voor Azure en Pacemaker: Tijdens de installatieprocedure via SAP Software Provisioning Manager is er een expliciete vraag over hoge beschikbaarheid voor IBM Db2 LUW:
   >+ Selecteer **geen IBM Db2 pureScale.**
   >+ Selecteer niet **IBM Tivoli System Automation installeren voor multiplatforms.**
   >+ Selecteer **clusterconfiguratiebestanden genereren**niet .

   Wanneer u een SBD-apparaat voor Linux Pacemaker gebruikt, stelt u de volgende Db2 HADR-parameters in:
   + HADR-peervensterduur (seconden) (HADR_PEER_WINDOW) = 300  
   + HADR-time-outwaarde (HADR_TIMEOUT) = 60

   Wanneer u een Azure Pacemaker-afrasteringsagent gebruikt, stelt u de volgende parameters in:
   + HADR-peervensterduur (seconden) (HADR_PEER_WINDOW) = 900  
   + HADR-time-outwaarde (HADR_TIMEOUT) = 60

Wij raden de voorgaande parameters op basis van de eerste failover / overname testen. Het is verplicht dat u test op de juiste functionaliteit van failover en overname met deze parameterinstellingen. Omdat afzonderlijke configuraties kunnen variëren, moeten de parameters mogelijk worden aangepast. 

> [!IMPORTANT]
> Specifiek voor IBM Db2 met HADR-configuratie bij normaal opstarten: de secundaire of stand-by database-instantie moet operationeel zijn voordat u de primaire database-instantie starten.

Voor demonstratiedoeleinden en de procedures beschreven in dit artikel, de database SID is **PTR**.

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR check
Nadat u HADR hebt geconfigureerd en de status PEER en VERBONDEN op de primaire en stand-byknooppunten is, voert u de volgende controle uit:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker-configuratie

Wanneer u Pacemaker gebruikt voor automatische failover in het geval van een knooppuntstoring, moet u uw Db2-exemplaren en pacemaker dienovereenkomstig configureren. In deze sectie wordt dit type configuratie beschreven.

De volgende items zijn vooraf vastgelegd met:

- **[A]**: Van toepassing op alle knooppunten
- **[1]**: Alleen van toepassing op knooppunt 1 
- **[2]**: Alleen van toepassing op knooppunt 2

**[A]** Voorwaarden voor pacemakerconfiguratie:
1. Sluit beide databaseservers met\<db2 sid> van de gebruiker af met db2stop.
1. Wijzig de shell-omgeving\<voor db2 sid> gebruiker in */bin/ksh*. Wij raden u aan de Yast tool te gebruiken. 


### <a name="pacemaker-configuration"></a>Pacemakerconfiguratie

> [!IMPORTANT]
> Recente tests brachten situaties aan het licht, waarin netcat stopt met reageren op verzoeken vanwege achterstand en de beperking van de afhandeling van slechts één verbinding. De netcat-bron luistert niet meer naar de aanvragen voor Azure Load balancer en het zwevende IP-adres is niet meer beschikbaar.  
> Voor bestaande Pacemaker clusters, raden we in het verleden ter vervanging van netcat door socat. Momenteel raden we u aan azure-lb resource agent, die deel uitmaakt van pakket resource-agents, met de volgende pakketversie vereisten:
> - Voor SLES 12 SP4/SP5 moet de versie ten minste resource-agents-4.3.018.a7fb5035-3.30.1 zijn.  
> - Voor SLES 15/15 SP1 moet de versie ten minste resourceagents-4.3.0184.6ee15eb2-4.13.1 zijn.  
>
> Houd er rekening mee dat de wijziging korte downtime vereist.  
> Als de configuratie al is gewijzigd om socat te gebruiken zoals beschreven in [Azure Load-Balancer Detection Hardening,](https://www.suse.com/support/kb/doc/?id=7024128)is er geen verplichting om onmiddellijk over te schakelen naar azure-lb resource agent.

**[1]** IBM Db2 HADR-specifieke Pacemaker-configuratie:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** Ibm Db2-bronnen maken:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** Start IBM Db2 resources:
* Zet Pacemaker uit de onderhoudsmodus.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** Zorg ervoor dat de clusterstatus in orde is en dat alle resources zijn gestart. Het is niet belangrijk welk knooppunt de resources draaien.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 knooppunten geconfigureerd
# <a name="5-resources-configured"></a>5 resources geconfigureerd

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>Volledige lijst met bronnen:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd (stonith:external/sbd): Gestart met azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Resourcegroep: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (ocf::heartbeat:IPaddr2): Gestart azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat:azure-lb): Gestart azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Masters: [ azibmdb02 ]
#      <a name="slaves--azibmdb01-"></a>Slaven: [ azibmdb01 ]
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
 
1. Meld u aan bij de primaire toepassingsserver van de J2EE-instantie en voer het uitvoeren uit:`sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. Kies in het linkerframe **het beveiligingsarchief**.
1. Kies in het juiste frame de belangrijkste\<jdbc/pool/ SAPSID>/url.
1. Wijzig de hostnaam in de JDBC-URL in de naam van de virtuele host.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Selecteer **Toevoegen**.
1. Als u de wijzigingen wilt opslaan, selecteert u het schijfpictogram linksboven.
1. Sluit het configuratiegereedschap.
1. Start de Java-instantie opnieuw.

## <a name="configure-log-archiving-for-hadr-setup"></a>Logboekarchivering configureren voor HADR-installatie
Als u de Db2-logboekarchivering wilt configureren voor hadr-instellingen, raden we u aan zowel de primaire als de stand-bydatabase te configureren om automatische logboekophaalmogelijkheden te hebben van alle logarchieflocaties. Zowel de primaire als de stand-bydatabase moeten logarchiefbestanden kunnen ophalen uit alle logarchieflocaties waar naar een van de database-instanties logbestanden kan worden gearchiveerd. 

De logboekarchivering wordt alleen uitgevoerd door de primaire database. Als u de HADR-rollen van de databaseservers wijzigt of als er een fout optreedt, is de nieuwe primaire database verantwoordelijk voor logboekarchivering. Als u meerdere logarchieflocaties hebt ingesteld, worden uw logboeken mogelijk twee keer gearchiveerd. In het geval van een lokale of externe inhaalslag moet u de gearchiveerde logboeken mogelijk ook handmatig kopiëren van de oude primaire server naar de actieve logboeklocatie van de nieuwe primaire server.

We raden u aan een gemeenschappelijk NFS-aandeel te configureren waarbij logboeken van beide knooppunten worden geschreven. Het NFS-aandeel moet zeer beschikbaar zijn. 

U bestaande zeer beschikbare NFS-shares gebruiken voor transporten of een profielmap. Zie voor meer informatie:

- [Hoge beschikbaarheid voor NFS op Azure VM's op SUSE Linux Enterprise Server][nfs-ha] 
- [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server met Azure NetApp-bestanden voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Azure NetApp-bestanden](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (nfs-shares maken)


## <a name="test-the-cluster-setup"></a>De clusterinstelling testen

In dit gedeelte wordt beschreven hoe u uw Db2 HADR-installatie testen. *Elke test gaat ervan uit dat u bent ingelogd als user root* en de IBM Db2 primaire draait op de *azibmdb01* virtuele machine.

De initiële status voor alle testcases wordt hier uitgelegd: (crm_mon -r of crm-status)

- **crm-status** is een momentopname van de pacemakerstatus bij de uitvoering 
- **crm_mon -r** is continue output van pacemakerstatus

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

De oorspronkelijke status in een SAP-systeem wordt gedocumenteerd in Transaction DBACOCKPIT > Configuration > Overview, zoals weergegeven in de volgende afbeelding:

![DBACockpit - Pre Migratie](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Test overname van IBM Db2


> [!IMPORTANT] 
> Controleer voordat u met de test begint:
> * Pacemaker heeft geen mislukte acties (crm-status).
> * Er zijn geen locatiebeperkingen (restjes migratietest)
> * De IBM Db2 HADR synchronisatie werkt. Neem contact op\<met gebruiker db2 sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migreer het knooppunt waarop de primaire Db2-database wordt uitgevoerd door de volgende opdracht uit te voeren:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Nadat de migratie is uitgevoerd, ziet de crm-statusuitvoer eruit als volgt:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

De oorspronkelijke status in een SAP-systeem wordt gedocumenteerd in Transaction DBACOCKPIT > Configuration > Overview, zoals weergegeven in de volgende afbeelding:

![DBACockpit - Post Migratie](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Resourcemigratie met 'crm-bron migreren' zorgt voor locatiebeperkingen. Locatiebeperkingen moeten worden verwijderd. Als locatiebeperkingen niet worden verwijderd, kan de resource niet meer mislukken of u ongewenste overnames ervaren. 

Migreer de resource terug naar *azibmdb01* en maak de locatiebeperkingen vrij
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **crm-bron \<migreert res_name> \<host->:** Creëert locatiebeperkingen en kan problemen veroorzaken bij overname
- **crm-bron \<wissen res_name>**: Wist locatiebeperkingen
- **crm resource \<cleanup res_name>**: Alle fouten van de resource gewist

### <a name="test-the-fencing-agent"></a>Test de hekwerkagent

In dit geval testen we SBD-schermen, wat we u aanraden te doen wanneer u SUSE Linux gebruikt.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Clusterknooppunt *azibmdb01* moet opnieuw worden opgestart. De IBM Db2 primaire HADR rol zal worden verplaatst naar *azibmdb02*. Wanneer *azibmdb01* weer online is, gaat de Db2-instantie bewegen in de rol van een secundaire database-instantie. 

Als de pacemakerservice niet automatisch wordt gestart op de opnieuw opgestarte voormalige primaire, moet u deze handmatig starten met:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Test een handmatige overname

U een handmatige overname testen door de Pacemaker-service op *het azibmdb01-knooppunt te* stoppen:
<pre><code>service pacemaker stop</code></pre>

status op *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Na de failover u de service opnieuw starten op *azibmdb01.*
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Dood het Db2-proces op het knooppunt waarop de hadr primaire database wordt uitgevoerd

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

De Db2-instantie gaat mislukken en Pacemaker rapporteert de volgende status:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker start de primaire database-instantie Db2 opnieuw op hetzelfde knooppunt of het mislukt niet naar het knooppunt waarop de secundaire database-instantie wordt uitgevoerd en er wordt een fout gerapporteerd.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Dood het Db2-proces op het knooppunt waarop de secundaire database-instantie wordt uitgevoerd

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Het knooppunt krijgt in mislukte vermeld en fout gemeld
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

De instantie Db2 wordt opnieuw gestart in de secundaire rol die hij eerder had toegewezen.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Stop DB via db2stop-kracht op het knooppunt waarop de primaire database-instantie HADR wordt uitgevoerd

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Als gebruiker\<db2 sid> uitvoeren opdracht db2stop kracht:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Fout gedetecteerd
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

De db2 HADR secundaire database instantie werd gepromoveerd in de primaire rol
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>CrashVM met opnieuw opstarten op het knooppunt waarop de primaire database-instantie HADR wordt uitgevoerd

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker promoot de secundaire instantie naar de primaire instantierol. De oude primaire instantie wordt naar de secundaire rol verplaatst nadat de VM en alle services volledig zijn hersteld nadat de VM opnieuw is opgestart:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Crash de VM waarop de primaire HADR-database-instantie wordt uitgevoerd met 'halt'

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

In een dergelijk geval detecteert Pacemaker dat het knooppunt waarop de primaire database-instantie wordt uitgevoerd, niet reageert.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

De volgende stap is om te controleren op een *Split hersensituatie.* Nadat het overlevende knooppunt heeft vastgesteld dat het knooppunt dat de primaire databaseinstantie voor het laatst heeft uitgevoerd, is uitgeschakeld, wordt een failover van resources uitgevoerd.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


In het geval van een "halt" van het knooppunt moet het mislukte knooppunt opnieuw worden gestart via Azure Management-hulpprogramma's (in de Azure-portal, PowerShell of azure cli). Nadat het mislukte knooppunt weer online is, start het de Db2-instantie in de secundaire rol.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Volgende stappen
- [Architectuur en scenario's met hoge beschikbaarheid voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Pacemaker instellen op SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

