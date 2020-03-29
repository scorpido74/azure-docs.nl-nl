---
title: SAP HANA installeren op SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: SAP HANA installeren op een SAP HANA op Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca59305b22fcf1e81ef518612910731cb6edea5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617094"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>SAP HANA (Large Instances) installeren en configureren op Azure

Voor het lezen van dit artikel, vertrouwd raken met [HANA Large Instances algemene termen](hana-know-terms.md) en de [HANA Large Instances SKU's](hana-available-skus.md).

De installatie van SAP HANA is uw verantwoordelijkheid. U beginnen met het installeren van een nieuwe SAP HANA op Azure (Large Instances) server nadat u de verbinding hebt vastgesteld tussen uw Azure virtuele netwerken en de HANA Large Instance unit(s). 

> [!Note]
> Volgens sap-beleid moet de installatie van SAP HANA worden uitgevoerd door een persoon die geslaagd is voor het Certified SAP Technology Associate-examen, het SAP HANA-certificeringsexamen of die een SAP-gecertificeerde systeemintegrator (SI) is.

Wanneer u VAN plan bent HANA 2.0 te installeren, raadpleegt u [SAP-ondersteuningsnotitie #2235581 - SAP HANA: ondersteunde besturingssystemen](https://launchpad.support.sap.com/#/notes/2235581/E) om ervoor te zorgen dat het besturingssysteem wordt ondersteund met de SAP HANA-release die u installeert. Het ondersteunde besturingssysteem voor HANA 2.0 is restrictiever dan het ondersteunde besturingssysteem voor HANA 1.0. U moet ook controleren of de OS-release waarin u geïnteresseerd bent, wordt vermeld als ondersteund voor de specifieke HLI-eenheid op deze gepubliceerde [lijst.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Klik op het apparaat om de volledige details te krijgen met de ondersteunde OS-lijst van dat apparaat. 

Valideer het volgende voordat u met de HANA-installatie begint:
- [HLI-eenheid(en)](#validate-the-hana-large-instance-units)
- [Configuratie van het besturingssysteem](#operating-system)
- [Netwerkconfiguratie](#networking)
- [Opslagconfiguratie](#storage)


## <a name="validate-the-hana-large-instance-units"></a>De EENHEID GROTE Instantie(en) van HANA-eenheden voor grote instanties valideren

Nadat u de HANA Large Instance-eenheid van Microsoft hebt ontvangen, valideert u de volgende instellingen en past u deze zo nodig aan.

De **eerste stap** nadat u de HANA Large Instance hebt ontvangen en toegang en connectiviteit tot de instanties hebt ingesteld, is om in Azure-portal te controleren of de instantie(s) worden weergegeven met de juiste SKU's en OS. Lees [Azure HANA Large Instances besturingselement via Azure portal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal) voor de stappen die nodig zijn om de controles uit te voeren.

De **tweede stap** nadat u de HANA Large Instance ontvangt en toegang en connectiviteit tot de instanties tot stand hebt gebracht, is het registreren van het besturingssysteem van de instantie bij uw OS-provider. Deze stap omvat het registreren van uw SUSE Linux OS in een exemplaar van SUSE SMT dat is geïmplementeerd in een VM in Azure. 

De HANA Large Instance-eenheid kan verbinding maken met deze SMT-instantie. (Zie Voor meer informatie hoe u [de SMT-server voor SUSE Linux instelt).](hana-setup-smt.md) Je Red Hat OS moet ook zijn geregistreerd bij de Red Hat Subscription Manager waarmee je verbinding moet maken. Zie voor meer informatie de opmerkingen in [Wat is SAP HANA op Azure (Large Instances)?](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Deze stap is nodig voor het patchen van het OS, dat is de verantwoordelijkheid van de klant. Voor SUSE vindt u de documentatie voor het installeren en configureren van SMT op deze pagina over [SMT-installatie.](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html)

De **derde stap** is om te controleren op nieuwe patches en fixes van de specifieke OS release / versie. Controleer of het patchniveau van de HANA Large Instance zich in de nieuwste status bevindt. Er kunnen gevallen zijn waarin de nieuwste patches niet zijn opgenomen. Na het overnemen van een HANA Large Instance-eenheid is het verplicht om te controleren of patches moeten worden toegepast.

De **vierde stap** is het controleren van de relevante SAP notes voor het installeren en configureren van SAP HANA op de specifieke OS release / versie. Als gevolg van wijzigingen in sap-notities of configuraties die afhankelijk zijn van individuele installatiescenario's, kan Microsoft niet altijd een HANA Large Instance-eenheid perfect configureren. 

Daarom is het verplicht voor u als klant om de SAP-notities met betrekking tot SAP HANA te lezen voor uw exacte Linux-release. Controleer ook de configuraties van de os-release/-versie en pas de configuratie-instellingen toe als u dat nog niet hebt gedaan.

Controleer specifiek de volgende parameters en pas u uiteindelijk aan:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Vanaf SLES12 SP1 en RHEL 7.2 moeten deze parameters worden ingesteld in een configuratiebestand in de map /etc/sysctl.d. Er moet bijvoorbeeld een configuratiebestand met de naam 91-NetApp-HANA.conf worden gemaakt. Voor oudere SLES- en RHEL-releases moeten deze parameters worden ingesteld in/etc/sysctl.conf.

Voor alle RHEL releases vanaf RHEL 6.3, houd in gedachten: 
- De parameter sunrpc.tcp_slot_table_entries = 128 moet worden ingesteld in/etc/modprobe.d/sunrpc-local.conf. Als het bestand niet bestaat, moet u het eerst maken door de vermelding toe te voegen: 
    - opties sunrpc tcp_max_slot_table_entries=128

De **vijfde stap** is het controleren van de systeemtijd van uw HANA Large Instance-eenheid. De instanties worden geïmplementeerd met een systeemtijdzone. Deze tijdzone vertegenwoordigt de locatie van het Azure-gebied waarin de HANA-stempel voor grote instanties zich bevindt. U de systeemtijd of -tijdzone wijzigen van de instanties die u bezit. 

Als u meer exemplaren in uw tenant bestelt, moet u de tijdzone van de nieuw geleverde exemplaren aanpassen. Microsoft heeft geen inzicht in de systeemtijdzone die u na de overdracht met de instanties hebt ingesteld. Nieuw geïmplementeerde exemplaren worden dus mogelijk niet in dezelfde tijdzone ingesteld als de exemplaren waarin u bent gewijzigd. Het is uw verantwoordelijkheid als klant om de tijdzone van de instantie(s) die werden overhandigd, indien nodig aan te passen. 

De **zesde stap** is om etc / hosts te controleren. Als de messen worden overhandigd, ze hebben verschillende IP-adressen die zijn toegewezen voor verschillende doeleinden. Controleer het bestand etc/hosts. Wanneer eenheden worden toegevoegd aan een bestaande tenant, verwacht dan niet dat de nieuwe geïmplementeerde systemen correct worden onderhouden met de IP-adressen van systemen die eerder zijn geleverd. Het is uw verantwoordelijkheid als klant om ervoor te zorgen dat een nieuw geïmplementeerdexemplaar kan communiceren en de namen van de eenheden kan oplossen die u eerder in uw tenant hebt geïmplementeerd. 


## <a name="operating-system"></a>Besturingssysteem

De swapruimte van de geleverde OS-afbeelding is ingesteld op 2 GB volgens de [SAP-ondersteuningsnotitie #1999997 - FAQ: SAP HANA-geheugen](https://launchpad.support.sap.com/#/notes/1999997/E). Als klant, als je een andere instelling wilt, moet je het zelf instellen.

[SUSE Linux Enterprise Server 12 SP1 voor SAP-toepassingen](https://www.suse.com/products/sles-for-sap/download/) is de distributie van Linux die is geïnstalleerd voor SAP HANA op Azure (Large Instances). Deze specifieke distributie biedt SAP-specifieke mogelijkheden "out of the box" (inclusief vooraf ingestelde parameters voor het effectief uitvoeren van SAP op SLES).

Zie [Resourcebibliotheek/whitepapers](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) op de SUSE-website en [SAP op SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) op het SAP Community Network (SCN) voor verschillende nuttige bronnen met betrekking tot de implementatie van SAP HANA op SLES (inclusief het instellen van hoge beschikbaarheid, beveiligingsverharding die specifiek is voor SAP-activiteiten en meer).

Hieronder volgt aanvullende en nuttige SAP op SUSE-gerelateerde links:

- [SAP HANA op SUSE Linux site](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Aanbevolen procedures voor SAP: enqueue-replicatie – SAP NetWeaver op SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP – SLES virusbescherming voor SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (inclusief SLES 12 voor SAP-toepassingen)

Hieronder volgen SAP-ondersteuningsnotities die van toepassing zijn op de implementatie van SAP HANA op SLES 12:

- [SAP support note #1944799 – SAP HANA richtlijnen voor SLES-installatie van besturingssysteem](http://service.sap.com/sap/support/notes/1944799)
- [SAP support note #2205917 – SAP HANA DB aanbevolen OS-instellingen voor SLES 12 voor SAP-toepassingen](https://launchpad.support.sap.com/#/notes/2205917/E)
- [SAP support note #1984787 – SUSE Linux Enterprise Server 12: installatienotities](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP support note #171356 – SAP software op Linux: Algemene informatie](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP support note #1391070 – Linux UUID-oplossingen](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux voor SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) is een andere aanbieding voor het uitvoeren van SAP HANA op HANA Large Instances. Releases van RHEL 7.2 en 7.3 zijn beschikbaar en ondersteund. 

Hieronder volgen extra nuttige SAP op Red Hat gerelateerde links:
- [SAP HANA op Red Hat Linux site](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Hieronder volgen SAP-ondersteuningsnotities die van toepassing zijn op de implementatie van SAP HANA op Red Hat:

- [SAP support note #2009879 - SAP HANA richtlijnen voor Red Hat Enterprise Linux (RHEL) besturingssysteem](https://launchpad.support.sap.com/#/notes/2009879/E)
- [SAP support note #2292690 - SAP HANA DB: Aanbevolen OS-instellingen voor RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [SAP support note #1391070 – Linux UUID-oplossingen](https://launchpad.support.sap.com/#/notes/1391070)
- [SAP support note #2228351 - Linux: SAP HANA Database SPS 11 revisie 110 (of hoger) op RHEL 6 of SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [SAP support note #2397039 - FAQ: SAP op RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [SAP support note #2002167 - Red Hat Enterprise Linux 7.x: Installatie en upgrade](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Tijdsynchronisatie

SAP-toepassingen die zijn gebouwd op de SAP NetWeaver-architectuur zijn gevoelig voor tijdsverschillen voor de verschillende componenten die het SAP-systeem omvatten. SAP ABAP korte dumps met de\_\_fout\_titel van ZDATE GROTE TIJD DIFF zijn waarschijnlijk bekend. Dat komt omdat deze korte dumps verschijnen wanneer de systeemtijd van verschillende servers of VM's te ver uit elkaar drijft.

Voor SAP HANA op Azure (Large Instances) is tijdsynchronisatie die in Azure wordt uitgevoerd niet van toepassing op de compute units in de large instance-stempels. Deze synchronisatie is niet van toepassing voor het uitvoeren van SAP-toepassingen in native Azure VM's, omdat Azure ervoor zorgt dat de tijd van een systeem correct is gesynchroniseerd. 

Als gevolg hiervan moet u een aparte tijdserver instellen die kan worden gebruikt door SAP-toepassingsservers die worden uitgevoerd op Azure VM's en door de SAP HANA-database-exemplaren die worden uitgevoerd op HANA Large Instances. De opslaginfrastructuur in large instance-stempels is tijdsgesynchroniseerd met NTP-servers.


## <a name="networking"></a>Netwerken
We gaan ervan uit dat u de aanbevelingen hebt opgevolgd bij het ontwerpen van uw virtuele Azure-netwerken en het verbinden van deze virtuele netwerken met de HANA Large Instances, zoals beschreven in de volgende documenten:

- [SAP HANA (Large Instance) overzicht en architectuur op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [SAP HANA (Large Instances) infrastructuur en connectiviteit op Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Er zijn enkele details vermeldenswaard over het netwerken van de enkele eenheden. Elke HANA Large Instance-eenheid wordt geleverd met twee of drie IP-adressen die zijn toegewezen aan twee of drie NIC-poorten. Drie IP-adressen worden gebruikt in HANA scale-out configuraties en de HANA systeem replicatie scenario. Een van de IP-adressen die is toegewezen aan de NIC van het apparaat is uit de IP-groep van de server die wordt beschreven in [het SAP HANA-overzicht (Large Instances) en architectuur op Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

Zie de [door HLI ondersteunde scenario's voor](hana-supported-scenario.md)meer informatie over Ethernet-details voor uw architectuur.

## <a name="storage"></a>Storage

De opslagindeling voor SAP HANA op Azure (Large Instances) `service management` wordt door SAP HANA op Azure geconfigureerd via door SAP aanbevolen richtlijnen. Deze richtlijnen zijn vastgelegd in de [whitepaper over SAP HANA-opslagvereisten.](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 

De ruwe grootte van de verschillende volumes met de verschillende HANA Large Instances SKU's is gedocumenteerd in [SAP HANA (Large Instances) overzicht en architectuur op Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

De naamgevingsconventies van de opslagvolumes worden vermeld in de volgende tabel:

| Opslaggebruik | Naam monteren | Volumenaam | 
| --- | --- | ---|
| HANA-gegevens | /hana/data/SID/mnt0000\<m> | Opslag-IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA-logboek | /hana/log/SID/mnt0000\<m> | Opslag-IP:/hana_log_SID_mnt00001_tenant_vol |
| HANA-logboekback-up | /hana/log/backups | Opslag-IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA gedeeld | /hana/shared/SID | Opslag-IP:/hana_shared_SID_mnt00001_tenant_vol/gedeeld |
| usr/sap | /usr/sap/SID | Opslag-IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* is de HANA instance System ID. 

*Tenant* is een interne opsomming van bewerkingen bij het implementeren van een tenant.

HANA usr/sap delen hetzelfde volume. De nomenclatuur van de mountpoints omvat de systeem-ID van de HANA-exemplaren en het mountnummer. Bij scale-upimplementaties is er slechts één mount, zoals mnt00001. In scale-out implementaties ziet u daarentegen evenveel mounts als werknemers- en masterknooppunten. 

Voor scale-outomgevingen worden gegevens-, logboek- en logboekbackbackvolumes gedeeld en gekoppeld aan elk knooppunt in de scale-outconfiguratie. Voor configuraties die meerdere SAP-exemplaren zijn, wordt een andere set volumes gemaakt en gekoppeld aan de eenheid GROTE Instantie HANA. Zie [HLI-ondersteunde scenario's](hana-supported-scenario.md)voor opslaglay-outdetails voor uw scenario.

Als je kijkt naar een HANA Large Instance unit, realiseer je je dat de eenheden worden geleverd met royale schijfvolume voor HANA / data, en dat er een volume HANA / log / back-up. De reden dat we de HANA/data zo groot hebben gemaakt, is dat de storage snapshots die wij u als klant aanbieden hetzelfde schijfvolume gebruiken. Hoe meer opslagmomentopnamen u uitvoert, hoe meer ruimte wordt verbruikt door momentopnamen in uw toegewezen opslagvolumes. 

Het HANA/log/back-upvolume is niet bedoeld als het volume voor databaseback-ups. Het is formaat om te worden gebruikt als het back-upvolume voor de HANA transactielogback-ups. Zie [SAP HANA (Large Instances) hoge beschikbaarheid en herstel na noodgevallen op Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie. 

Naast de opslagruimte die wordt geleverd, u extra opslagcapaciteit kopen in stappen van 1 TB. Deze extra opslag kan worden toegevoegd als nieuwe volumes aan een HANA Large Instance.

Tijdens het onboarden met `service management`SAP HANA op Azure geeft de klant een gebruikers-ID (UID) en groeps-ID (GID) op voor de sidadm-gebruiker en sapsys-groep (bijvoorbeeld: 1000.500). Tijdens de installatie van het SAP HANA-systeem moet u dezelfde waarden gebruiken. Omdat u meerdere HANA-exemplaren op een eenheid wilt implementeren, krijgt u meerdere sets volumes (één set voor elke instantie). Als gevolg hiervan moet u bij implementatietijd definiëren:

- Sid van de verschillende instanties HANA (sidadm wordt afgeleid van het).
- De geheugengroottes van de verschillende HANA-exemplaren. De geheugengrootte per instantie definieert de grootte van de volumes in elke afzonderlijke volumeset.

Op basis van aanbevelingen van de opslagprovider zijn de volgende bevestigingsopties geconfigureerd voor alle gemonteerde volumes (exclusief boot LUN):

- nfs rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0

Deze bevestigingspunten zijn geconfigureerd in /etc/fstab, zoals weergegeven in de volgende afbeeldingen:

![fstab van gemonteerde volumes in HANA Large Instance unit](./media/hana-installation/image1_fstab.PNG)

De uitvoer van de opdracht df-h op een S72m HANA Large Instance-eenheid ziet eruit als volgt:

![fstab van gemonteerde volumes in HANA Large Instance unit](./media/hana-installation/image2_df_output.PNG)


De opslagcontroller en knooppunten in de large instance-stempels worden gesynchroniseerd met NTP-servers. Wanneer u de SAP HANA op Azure -eenheden (Large Instances) en Azure VM's synchroniseert met een NTP-server, mag er geen significante tijdafwijking zijn tussen de infrastructuur en de compute units in Azure- of Large Instance-stempels.

Als u SAP HANA wilt optimaliseren voor de opslag die hieronder wordt gebruikt, stelt u de volgende SAP HANA-configuratieparameters in:

- max_parallel_io_requests 128
- async_read_submit op
- async_write_submit_active op
- async_write_submit_blocks alle
 
Voor SAP HANA 1.0-versies tot SPS12 kunnen deze parameters worden ingesteld tijdens de installatie van de SAP HANA-database, zoals beschreven in [SAP-notitie #2267798 - Configuratie van de SAP HANA-database](https://launchpad.support.sap.com/#/notes/2267798).

U de parameters ook configureren na de SAP HANA-databaseinstallatie met behulp van het hdbparam-framework. 

De opslag die wordt gebruikt in HANA Large Instances heeft een beperking van de bestandsgrootte. De [groottebeperking is 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) per bestand. In tegenstelling tot bestandsgroottebeperkingen in de EXT3-bestandssystemen, is HANA zich impliciet niet bewust van de opslagbeperking die wordt afgedwongen door de OPSLAG van HANA Large Instances. Als gevolg hiervan zal HANA niet automatisch een nieuw gegevensbestand maken wanneer de bestandsgroottelimiet van 16 TB is bereikt. Als HANA probeert om het bestand te groeien dan 16 TB, HANA zal fouten melden en de index server zal crashen aan het einde.

> [!IMPORTANT]
> Om te voorkomen dat HANA gegevensbestanden probeert te laten groeien boven de 16 TB-bestandsgroottelimiet van HANA Large Instance-opslag, moet u de volgende parameters instellen in het SAP HANA global.ini-configuratiebestand
> 
> - datavolume_striping=waar
> - datavolume_striping_size_gb = 15000
> - Zie ook SAP note [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Wees je bewust van SAP-notitie [#2631285](https://launchpad.support.sap.com/#/notes/2631285)


Met SAP HANA 2.0 is het hdbparam framework afgeschaft. Als gevolg hiervan moeten de parameters worden ingesteld met SQL-opdrachten. Zie voor meer informatie [SAP note #2399079: Eliminatie van hdbparam in HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Raadpleeg [door HLI-ondersteunde scenario's](hana-supported-scenario.md) voor meer informatie over de opslagindeling voor uw architectuur.


**Volgende stappen**

- Raadpleeg [HANA-installatie op HLI](hana-example-installation.md)










































 







 




