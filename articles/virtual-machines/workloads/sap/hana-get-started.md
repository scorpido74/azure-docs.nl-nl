---
title: 'Snelstartgids: hand matige installatie van single-instance SAP HANA op Azure Virtual Machines | Microsoft Docs'
description: Snelstartgids voor het hand matig installeren van SAP HANA met één exemplaar op Azure Virtual Machines
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 0090ffe977dee3e493d726c9eb4d151bcbeb503f
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617238"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Snelstartgids: hand matige installatie van single-instance SAP HANA op Azure Virtual Machines
## <a name="introduction"></a>Inleiding
Deze hand leiding helpt u bij het instellen van een single-instance SAP HANA op Azure Virtual Machines wanneer u SAP NetWeaver 7,5 en SAP HANA 1,0 SP12 hand matig installeert. Deze hand leiding is gericht op het implementeren van SAP HANA op Azure. De SAP-documentatie wordt niet vervangen. 

> [!NOTE]
> In deze hand leiding worden de implementaties van SAP HANA in azure Vm's beschreven. Zie voor meer informatie over het implementeren van SAP HANA in HANA grote instanties [SAP on Azure virtual machines gebruiken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Vereisten
In deze hand leiding wordt ervan uitgegaan dat u bekend bent met de basis principes van de infra structuur als een service (IaaS) als:
 * Virtuele machines (Vm's) of virtuele netwerken implementeren via de Azure Portal of Power shell.
 * De Azure cross-platform-opdracht regel interface (CLI), waaronder de optie voor het gebruik van JavaScript Object Notation (JSON)-Sjablonen.

In deze hand leiding wordt ervan uitgegaan dat u bekend bent met:
* SAP HANA en SAP NetWeaver en hoe ze on-premises moeten worden geïnstalleerd.
* SAP HANA en SAP-toepassings exemplaren installeren en uitvoeren op Azure.
* De volgende concepten en procedures:
   * Planning voor SAP-implementatie op Azure, waaronder Azure Virtual Network planning en Azure Storage gebruik. Zie de [hand leiding voor de planning en implementatie van de SAP net-Weaver op Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Implementatie principes en manieren om Vm's in azure te implementeren. Zie [Azure virtual machines-implementatie voor SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Hoge Beschik baarheid voor SAP NetWeaver ABAP SAP Central Services (ASCS), SAP Central Services (SCS) en (in wachtrij plaatsen) in Azure. Bekijk [hoge Beschik baarheid voor SAP NetWeaver op Azure-vm's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Meer informatie over het verbeteren van de efficiëntie van een multi-SID-installatie van ASCS/SCS op Azure. Zie [een multi-sid-configuratie voor SAP net-Weaver maken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Principes voor het uitvoeren van SAP net-Weaver op basis van door Linux gestuurde Vm's in Azure. Zie [SAP Netweave uitvoeren op Microsoft Azure SuSE Linux-vm's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Deze hand leiding bevat specifieke instellingen voor Linux in azure Vm's. Het bevat ook informatie over het correct koppelen van Azure Storage-schijven aan linux Vm's.

De Azure VM-typen die kunnen worden gebruikt voor productie scenario's, worden vermeld in de [SAP-documentatie voor IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Voor scenario's met een niet-productie is er een breder scala aan systeem eigen Azure VM-typen beschikbaar.
Zie [SAP Hana Infrastructure-configuraties en-bewerkingen op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)voor meer informatie over de configuratie en bewerkingen van de virtuele machine.
Zie [SAP Hana hoge Beschik baarheid voor Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)voor SAP Hana hoge Beschik baarheid.

Als u een SAP HANA exemplaar of S/4HANA-of BW/4HANA-systeem snel wilt implementeren, kunt u overwegen om de [SAP Cloud Appliance Library](https://cal.sap.com)te gebruiken. In [deze hand leiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)vindt u documentatie over het implementeren van een S/4HANA-systeem via de SAP Cloud Appliance Library op Azure. U hebt alleen een Azure-abonnement nodig en een SAP-gebruiker die kan worden geregistreerd met de SAP Cloud Appliance Library.

## <a name="additional-resources"></a>Aanvullende resources
### <a name="sap-hana-backup"></a>SAP HANA back-up
Zie voor informatie over het maken van een back-up van SAP HANA-data bases op virtuele machines van Azure:
* [Back-upgids voor SAP Hana op Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide).
* [SAP HANA Azure Backup op bestands niveau](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level).
* [SAP Hana back-up op basis van moment opnamen van opslag](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots).

### <a name="sap-cloud-appliance-library"></a>Bibliotheek voor SAP-Cloud apparaten
Zie [SAP S/4HANA of BW/4HANA implementeren op Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)voor meer informatie over het gebruik van de SAP Cloud Appliance Library voor het implementeren van S/4HANA of BW/4HANA.

### <a name="sap-hana-supported-operating-systems"></a>Door SAP HANA ondersteunde besturings systemen
Voor informatie over door SAP HANA ondersteunde besturings systemen raadpleegt u [SAP Note 2235581-SAP Hana: ondersteunde besturings systemen](https://launchpad.support.sap.com/#/notes/2235581/E). Azure Vm's bieden alleen ondersteuning voor een subset van deze besturings systemen. De volgende besturings systemen worden ondersteund voor de implementatie van SAP HANA op Azure: 

* SUSE Linux Enterprise Server 12. x
* Red Hat Enterprise Linux 7.2

Zie voor aanvullende SAP-documentatie over SAP HANA en verschillende Linux-besturings systemen:

* [SAP-opmerking 171356: SAP-software op Linux: algemene informatie](https://launchpad.support.sap.com/#/notes/1984787).
* [SAP-opmerking 1944799: SAP Hana richt lijnen voor de installatie van het besturings systeem SLES](http://service.sap.com/sap/support/notes/1944799).
* [SAP Note 2205917: SAP Hana DB aanbevolen besturingssysteem instellingen voor SLES 12 voor SAP-toepassingen](https://launchpad.support.sap.com/#/notes/2205917/E).
* [SAP-opmerking 1391070: Linux-uuid-oplossingen](https://launchpad.support.sap.com/#/notes/1391070).
* [SAP Note 2009879: SAP Hana Guidelines for Red Hat Enterprise Linux (RHEL)-besturings systeem](https://launchpad.support.sap.com/#/notes/2009879).
* [SAP-opmerking 2292690: SAP Hana DB: aanbevolen besturingssysteem instellingen voor RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E).

### <a name="sap-monitoring-in-azure"></a>SAP-bewaking in azure
Voor informatie over SAP-bewaking in Azure:

* [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) BEspreekt SAP-verbeterde bewaking met Linux-Vm's in Azure. 
* [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) bevat informatie over SAPOSCOL in Linux. 
* [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) bevat informatie over de belangrijkste bewakings gegevens voor SAP op Microsoft Azure.

### <a name="azure-vm-types"></a>Azure VM-typen
Azure VM-typen en SAP-ondersteunde werkbelasting scenario's die worden gebruikt met SAP HANA, worden beschreven in [SAP Certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Azure VM-typen die zijn gecertificeerd door SAP voor SAP NetWeaver of de S/4HANA-toepassingslaag worden beschreven in [SAP Note 1928533: SAP-toepassingen op Azure: ondersteunde producten en Azure VM-typen](https://launchpad.support.sap.com/#/notes/1928533/E).

> [!NOTE]
> SAP-Linux-Azure-integratie wordt alleen ondersteund op Azure Resource Manager en niet op het klassieke implementatie model. 

## <a name="manual-installation-of-sap-hana"></a>Hand matige installatie van SAP HANA

> [!IMPORTANT]
> Zorg ervoor dat het besturings systeem dat u selecteert, SAP gecertificeerd is voor SAP HANA op de specifieke VM-typen die u gebruikt. De lijst met SAP HANA gecertificeerde VM-typen en versies van het besturings systeem voor deze VM-typen kan worden opgezocht in [SAP Hana gecertificeerde IaaS-platformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Zorg ervoor dat u in de details van het opgegeven VM-type klikt om de volledige lijst met door SAP HANA ondersteunde versies van besturings systemen voor het specifieke VM-type op te halen. Voor het voor beeld in dit document hebben we een SLES-besturingssysteem versie (SUSE Linux Enterprise Server) gebruikt die niet wordt ondersteund door SAP voor SAP HANA op Vm's uit de M-serie.
>

In deze hand leiding wordt beschreven hoe u SAP HANA op twee verschillende manieren hand matig op Azure-Vm's installeert:

* Gebruik SAP software Provisioning Manager (SWPM) als onderdeel van een gedistribueerde installatie van een nettoepassing in de stap "data base-exemplaar installeren".
* Gebruik het hulp programma SAP HANA data base Lifecycle Manager (HDBLCM) en installeer netweave.

U kunt SWPM ook gebruiken voor het installeren van alle onderdelen, zoals SAP HANA, de SAP-toepassings server en de ASCS-instantie, in één enkele virtuele machine. De stappen worden beschreven in deze [SAP Hana blog aankondiging](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Deze optie wordt niet beschreven in deze Snelstartgids, maar de problemen die u moet overwegen, zijn hetzelfde.

Voordat u een installatie start, raden we u aan de sectie ' Azure-Vm's voorbereiden voor hand matige installatie van SAP HANA ' verderop in deze hand leiding te lezen. Dit kan helpen bij het voor komen van verschillende basis fouten die zich kunnen voordoen wanneer u alleen een standaard configuratie van een Azure-VM gebruikt.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Belangrijkste stappen voor het SAP HANA van de installatie wanneer u SAP SWPM gebruikt
In deze sectie vindt u de belangrijkste stappen voor een hand matige, SAP HANA installatie met één exemplaar wanneer u SAP SWPM gebruikt voor het uitvoeren van een gedistribueerde SAP NetWeaver 7,5-installatie. De afzonderlijke stappen worden verderop in deze hand leiding uitgebreid beschreven in de scherm afbeeldingen.

1. Maak een virtueel Azure-netwerk dat twee VM-test machines bevat.
2. Implementeer de twee virtuele Azure-machines met besturings systemen volgens het Azure Resource Manager model. In dit voor beeld worden SUSE Linux Enterprise Server en SLES voor SAP-toepassingen 12 SP1 gebruikt. 
3. Koppel twee Azure Standard-of Premium-opslag schijven, bijvoorbeeld 75-GB of 500-GB schijven, aan de toepassings Server-VM.
4. Sluit Premium-opslag schijven aan op de HANA DB-Server-VM. Zie de sectie ' schijf installeren ' verderop in deze hand leiding voor meer informatie.
5. Koppel meerdere schijven, afhankelijk van de vereisten voor grootte of door voer. Maak vervolgens striped volumes. Gebruik Logical Volume Management (LVM) of een hulp programma voor beheer van meerdere apparaten (mdadm) op het niveau van het besturings systeem in de virtuele machine.
6. Maak XFS-bestands systemen op de gekoppelde schijven of logische volumes.
7. Koppel de nieuwe XFS-bestands systemen op het niveau van het besturings systeem. Gebruik één bestands systeem voor alle SAP-software. Gebruik het andere bestands systeem voor de/sapmnt-map en back-ups, bijvoorbeeld. Op de SAP HANA DB-Server koppelt u de XFS-bestands systemen op de Premium Storage-schijven als/Hana en/usr/sap. Dit proces is nodig om te voor komen dat het hoofd bestandssysteem wordt gevuld. Het hoofd bestands systeem is niet groot op virtuele Linux-machines. 
8. Voer de lokale IP-adressen van de virtuele test-Vm's in het bestand/etc/hosts-bestand in.
9. Voer de para meter **niet** in het bestand/etc/fstab-bestand in.
10. Stel de Linux-kernel-para meters in volgens de Linux-versie van het besturings systeem die u gebruikt. Zie voor meer informatie de SAP-opmerkingen over HANA en de sectie kernel-para meters in deze hand leiding.
11. Voeg wisselruimte toe.
12. U kunt eventueel een grafisch bureau blad installeren op de virtuele machines die u wilt testen. Gebruik anders een installatie van een externe SAPinst.
13. Down load de SAP-software via de SAP Service Marketplace.
14. Installeer het SAP ASCS-exemplaar op de app Server-VM.
15. Deel de/sapmnt-Directory over de virtuele machines van de test met behulp van NFS. De virtuele machine van de toepassings server is de NFS-server.
16. Installeer het data base-exemplaar, dat HANA bevat, met behulp van SWPM op de DB-Server-VM.
17. Installeer de primaire toepassings server (PAS) op de toepassings Server-VM.
18. Start de SAP-beheer console (SAP MC). Maak bijvoorbeeld verbinding met SAP-GUI of HANA Studio.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Belangrijkste stappen voor het SAP HANA van de installatie wanneer u HDBLCM gebruikt
In deze sectie vindt u de belangrijkste stappen voor een hand matige, SAP HANA installatie met één exemplaar wanneer u SAP HDBLCM gebruikt voor het uitvoeren van een gedistribueerde SAP NetWeaver 7,5-installatie. De afzonderlijke stappen worden uitgebreid beschreven in de scherm afbeeldingen in deze hand leiding.

1. Maak een virtueel Azure-netwerk dat twee VM-test machines bevat.
2. Implementeer twee virtuele Azure-machines met besturings systemen volgens het Azure Resource Manager model. In dit voor beeld worden SLES en SLES voor SAP-toepassingen 12 SP1 gebruikt.
3. Koppel twee Azure Standard-of Premium-opslag schijven, bijvoorbeeld 75-GB of 500-GB schijven, aan de app Server-VM.
4. Sluit Premium-opslag schijven aan op de HANA DB-Server-VM. Zie de sectie ' schijf installeren ' verderop in deze hand leiding voor meer informatie.
5. Koppel meerdere schijven, afhankelijk van de vereisten voor grootte of door voer. Maak striped volumes met behulp van logisch volume beheer of een mdadm-hulp programma op het niveau van het besturings systeem in de virtuele machine.
6. Maak XFS-bestands systemen op de gekoppelde schijven of logische volumes.
7. Koppel de nieuwe XFS-bestands systemen op het niveau van het besturings systeem. Gebruik één bestands systeem voor alle SAP-software. Gebruik het andere bestands systeem voor de/sapmnt-map en back-ups, bijvoorbeeld. Op de SAP HANA DB-Server koppelt u de XFS-bestands systemen op de Premium Storage-schijven als/Hana en/usr/sap. Dit proces is nodig om te voor komen dat het hoofd bestandssysteem wordt gevuld. Het hoofd bestands systeem is niet groot op virtuele Linux-machines.
8. Voer de lokale IP-adressen van de virtuele test-Vm's in het bestand/etc/hosts-bestand in.
9. Voer de para meter **niet** in het bestand/etc/fstab-bestand in.
10. Stel de kernel-para meters in volgens de Linux-versie van het besturings systeem die u gebruikt. Zie voor meer informatie de SAP-opmerkingen over HANA en de sectie kernel-para meters in deze hand leiding.
11. Voeg wisselruimte toe.
12. U kunt eventueel een grafisch bureau blad installeren op de virtuele machines die u wilt testen. Gebruik anders een installatie van een externe SAPinst.
13. Down load de SAP-software via de SAP Service Marketplace.
14. Maak een groep, sapsys, met groeps-ID 1001, op de HANA DB-Server-VM.
15. Installeer SAP HANA op de DB-Server-VM met behulp van HANA-data base levenscyclus beheer.
16. Installeer het SAP ASCS-exemplaar op de app Server-VM.
17. Deel de/sapmnt-Directory over de virtuele machines van de test met behulp van NFS. De virtuele machine van de toepassings server is de NFS-server.
18. Installeer het data base-exemplaar, dat HANA bevat, met behulp van SWPM op de HANA DB-Server-VM.
19. Installeer de primaire toepassings server op de toepassings Server-VM.
20. Start SAP MC. Maak verbinding via SAP-GUI of HANA Studio.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Virtuele Azure-machines voorbereiden voor een hand matige installatie van SAP HANA
In deze sectie komen de volgende onderwerpen aan bod:

* Updates van het besturings systeem
* Schijf installatie
* Kernel-para meters
* Bestands systemen
* Het bestand/etc/hosts-bestand
* Het bestand/etc/fstab-bestand

### <a name="os-updates"></a>Updates van het besturings systeem
Controleer of er updates en oplossingen voor Linux-besturings systemen zijn voordat u extra software installeert. Als u een patch installeert, kunt u een aanroep naar de ondersteunings Desk voor komen.

Zorg ervoor dat u het volgende gebruikt:
* SUSE Linux Enterprise Server voor SAP-toepassingen.
* Red Hat Enterprise Linux voor SAP-toepassingen of Red Hat Enterprise Linux voor SAP HANA. 

Als u dit nog niet hebt gedaan, registreert u de implementatie van het besturings systeem bij uw Linux-abonnement van de Linux-leverancier. SUSE heeft installatie kopieën van het besturings systeem voor SAP-toepassingen die al services bevatten en die automatisch worden geregistreerd.

Hier volgt een voor beeld van het controleren op beschik bare patches voor SUSE Linux met behulp van de **Zypper** -opdracht:

 `sudo zypper list-patches`

Afhankelijk van het soort probleem worden patches ingedeeld op categorie en ernst. Veelgebruikte waarden voor categorie zijn: 
- Beveiliging
- Aanbevolen
- Optioneel
- Functie
- Document
- Yast

Veelgebruikte waarden voor ernst zijn:

- Kritiek
- Belangrijk
- Prioriteit
- Laag
- Opgegeven

De **Zypper** -opdracht zoekt alleen naar de updates die zijn vereist voor de geïnstalleerde pakketten. U kunt bijvoorbeeld de volgende opdracht gebruiken:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

U kunt de para meter `--dry-run` toevoegen om de update te testen zonder het systeem daad werkelijk bij te werken.


### <a name="disk-setup"></a>Schijf installatie
Het hoofd bestands systeem in een virtuele Linux-machine in azure heeft een maximale grootte. U moet dus extra schijf ruimte aan een Azure-VM koppelen om SAP uit te voeren. Voor de Azure-Vm's van de SAP-toepassings server is het gebruik van Azure Standard-opslag schijven mogelijk genoeg. Voor SAP HANA DBMS Azure-Vm's is het gebruik van Azure Premium-opslag schijven voor implementaties van productie en niet-producties verplicht.

Op basis van de vereisten voor de [SAP Hana TDI-opslag](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)wordt de volgende Azure Premium-opslag configuratie voorgesteld: 

| VM-SKU | RAM |  /Hana/data en/Hana/log <br /> Striped met LVM of mdadm | /hana/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

In de voorgestelde schijf configuratie worden het HANA-gegevens volume en het logboek volume geplaatst op dezelfde set Azure Premium Storage-schijven die zijn striped met LVM of mdadm. Het is niet nodig om een RAID-redundantie niveau te definiëren, omdat Azure Premium Storage drie installatie kopieën van de schijven houdt voor redundantie. 

Zie [SAP Hana TDI-opslag vereisten](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) en [SAP Hana server installatie en update handleiding](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)om ervoor te zorgen dat u voldoende opslag ruimte configureert. Houd ook rekening met de verschillende doorvoer volumes van de virtuele harde schijf (VHD) van de verschillende Azure Premium-opslag schijven, zoals beschreven in [hoogwaardige Premium-opslag en beheerde schijven voor vm's](../../windows/disks-types.md). 

U kunt meer Premium-opslag schijven toevoegen aan de HANA DBMS-Vm's om data base-of transactie logboek back-ups op te slaan.

Zie voor meer informatie over de twee belangrijkste hulpprogram ma's voor het configureren van striping:

* [Software-RAID configureren op Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [LVM configureren op een virtuele Linux-machine in azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Zie [een schijf toevoegen aan een virtuele Linux-machine](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie over het koppelen van schijven aan Azure-Vm's die Linux uitvoeren als een gast besturingssysteem.

Met Azure Premium Ssd's kunt u schijf cache modi definiëren. Schakel de schijf cache uit voor de Striped set met/Hana/data en/Hana/log. Voor de andere volumes, dat wil zeggen schijven, stelt u de cache modus in op **alleen-lezen**.

Zie de [Azure Quick](https://github.com/Azure/azure-quickstart-templates)start-sjablonen voor een voor beeld van JSON-sjablonen die u kunt gebruiken om vm's te maken.
De sjabloon VM-Simple-SLES is een basis sjabloon. Het bevat een opslag sectie met een extra gegevens schijf van 100 GB. Gebruik deze sjabloon als basis. U kunt de sjabloon aanpassen aan uw specifieke configuratie.

> [!NOTE]
> Het is belang rijk dat u de Azure-opslag schijf koppelt met behulp van een UUID zoals beschreven in [SAP NetWeaver uitvoeren op Microsoft Azure SuSE Linux-vm's](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

In de test omgeving zijn twee standaard opslag schijven van Azure gekoppeld aan de VM van de SAP-app-server, zoals wordt weer gegeven in de volgende scherm afbeelding. Op één schijf worden alle SAP-software, zoals NetWeaver 7,5, SAP GUI en SAP HANA, opgeslagen voor installatie. De tweede schijf zorgt ervoor dat er voldoende vrije ruimte beschikbaar is voor aanvullende vereisten. Bijvoorbeeld: back-up-en test gegevens en de/sapmnt Directory, dat wil zeggen, SAP-profielen, moeten worden gedeeld met alle virtuele machines die deel uitmaken van hetzelfde SAP-landschap.

![SAP HANA-schijven venster van de app-server met twee gegevens schijven en hun grootte](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Kernel-para meters
Voor SAP HANA zijn specifieke instellingen voor de Linux-kernel vereist. Deze kernel-instellingen maken geen deel uit van de standaard installatie kopieën van Azure Gallery en moeten hand matig worden ingesteld. Afhankelijk van of u SUSE of Red Hat gebruikt, kunnen de para meters anders zijn. De SAP-opmerkingen die in de lijst staan, geven eerder informatie over deze para meters. In de weer gegeven scherm opnamen is SUSE Linux 12 SP1 gebruikt. 

SLES for SAP-toepassingen 12 algemene Beschik baarheid en SLES voor SAP-toepassingen 12 SP1 hebben een nieuw hulp programma, afgesteld **-adm**, waarmee het oude **sapconf** -hulp programma wordt vervangen. Er is een speciaal SAP HANA profiel beschikbaar voor **afgestemd-adm**. Als u het systeem voor SAP HANA wilt afstemmen, voert u het volgende profiel in als hoofd gebruiker:

   `tuned-adm profile sap-hana`

Zie de [SuSE-documentatie over afgestemde-adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)voor meer informatie over **afgestemde-adm**.

In de volgende scherm afbeelding ziet u hoe afgesteld **-adm** de `transparent_hugepage` en `numa_balancing` waarden heeft gewijzigd op basis van de vereiste SAP Hana instellingen:

![Het afgestemde-adm-hulp programma wijzigt waarden volgens de vereiste SAP HANA instellingen](./media/hana-get-started/image005.jpg)

Als u de SAP HANA kernel-instellingen permanent wilt maken, gebruikt u **grub2** op SLES 12. Zie de sectie [configuratie bestands structuur](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) van de SuSE-documentatie voor meer informatie over **grub2**.

In de volgende scherm afbeelding ziet u hoe de kernel-instellingen zijn gewijzigd in het configuratie bestand en vervolgens worden gecompileerd met behulp van **grub2-mkconfig**:

![De kernel-instellingen zijn gewijzigd in het configuratie bestand en gecompileerd met behulp van grub2-mkconfig](./media/hana-get-started/image006.jpg)

Een andere mogelijkheid is om de instellingen te wijzigen met behulp van YaST en de **opstart lader** > instellingen voor **kernel-para meters** :

![Het tabblad Instellingen voor kernel-para meters in YaST-opstart laad programma](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Bestands systemen
In de volgende scherm afbeelding ziet u twee bestands systemen die zijn gemaakt op de virtuele machine van de SAP-app-server, boven op de twee gekoppelde Azure Standard-opslag schijven. Beide bestands systemen zijn van het type XFS en worden gekoppeld aan/sapdata en/sapsoftware.

Het is niet verplicht uw bestands systemen op deze manier te structureren. U hebt andere opties voor het structureren van de schijf ruimte. De belangrijkste overweging is om te voor komen dat het hoofd bestandssysteem geen beschik bare ruimte meer heeft.

![Twee bestands systemen die zijn gemaakt op de SAP-app Server-VM](./media/hana-get-started/image008.jpg)

Wanneer u SAPinst gebruikt met SWPM en de **standaard** installatie optie, wordt voor de SAP Hana DB-VM, tijdens de installatie van een Data Base, alle geïnstalleerd onder/Hana en/usr/sap. De standaard locatie voor de back-upSAP HANA van het logboek is onder/usr/sap. Het is belang rijk om te voor komen dat het hoofd bestandssysteem de opslag ruimte verloopt. Zorg ervoor dat er voldoende vrije ruimte is onder/Hana en/usr/sap voordat u SAP HANA installeert met behulp van SWPM.

Zie de [SAP Hana server-installatie en de update handleiding](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)voor een beschrijving van de standaard bestandssysteem indeling van SAP Hana.

![Extra bestands systemen die zijn gemaakt op de SAP-app Server-VM](./media/hana-get-started/image009.jpg)

Wanneer u SAP NetWeaver installeert op een standaard SLES/SLES voor SAP-toepassingen 12 Azure Gallery-afbeelding, wordt er een bericht weer gegeven waarin staat dat er geen wissel ruimte is, zoals wordt weer gegeven in de volgende scherm afbeelding. U kunt dit bericht negeren door hand matig een wissel bestand toe te voegen met behulp van **dd**, **mkswap**en **swapon**. Als u wilt weten hoe u een wissel bestand hand matig kunt toevoegen, gaat u naar de sectie [YaST partitioner](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) in de SuSE-documentatie.

Een andere optie is het configureren van wissel ruimte met behulp van de Linux VM-agent. Zie de [Gebruikershandleiding voor Azure Linux Agent](../../extensions/agent-linux.md) voor meer informatie.

![Pop-upbericht waarin wordt gemeld dat er onvoldoende wissel ruimte is](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>Het bestand/etc/hosts-bestand
Voordat u SAP gaat installeren, moet u de hostnamen en IP-adressen van de SAP-Vm's in het bestand/etc/hosts-bestand toevoegen. Implementeer alle SAP-Vm's binnen één virtueel Azure-netwerk. Gebruik vervolgens de interne IP-adressen, zoals hier wordt weer gegeven:

![Hostnamen en IP-adressen van de SAP-Vm's die worden vermeld in het bestand/etc/hosts-bestand](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Het bestand/etc/fstab-bestand

Het is handig om de para meter **niet** toe te voegen aan het fstab-bestand. Als er iets mis gaat met de schijven, reageert de virtuele machine niet meer in het opstart proces. Houd er echter rekening mee dat er mogelijk geen extra schijf ruimte beschikbaar is en dat processen het hoofd bestands systeem kunnen opvullen. Als/Hana ontbreekt, wordt SAP HANA niet gestart.

![Voeg de para meter niet toe aan het fstab-bestand](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Grafisch GNOME bureau blad op SLES 12/SLES voor SAP-toepassingen 12
In deze sectie wordt uitgelegd hoe u:

* Installeer de GNOME desktop en xrdp op SLES 12/SLES voor SAP-toepassingen 12.
* Voer SAP MC op basis van Java uit met behulp van Firefox op SLES 12/SLES voor SAP-toepassingen 12.

U kunt ook alternatieven zoals xterminal of VNC gebruiken, die niet in deze hand leiding worden beschreven.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Installeer de GNOME desktop en xrdp op SLES 12/SLES voor SAP-toepassingen 12
Als u een Windows-achtergrond hebt, kunt u eenvoudig een grafisch bureau blad rechtstreeks in de SAP Linux-Vm's gebruiken om Firefox, SAPinst, SAP-gebruikers interface, SAP MC of HANA Studio uit te voeren. Vervolgens kunt u verbinding maken met de virtuele machine via de Remote Desktop Protocol (RDP) vanaf een Windows-computer. Afhankelijk van uw bedrijfs beleid over het toevoegen van GUIs aan productie en niet-productgebaseerde Linux-systemen, wilt u mogelijk GNOME installeren op uw server. Voer de volgende stappen uit om het GNOME-bureau blad te installeren op een Azure SLES 12/SLES voor SAP-toepassingen 12 VM.

1. Installeer het GNOME-bureau blad door de volgende opdracht in te voeren, bijvoorbeeld in een PuTTy-venster:

   `zypper in -t pattern gnome-basic`

2. Installeer xrdp om een verbinding met de virtuele machine via RDP toe te staan:

   `zypper in xrdp`

3. Bewerk/etc/sysconfig/windowmanager en stel de standaard venster beheerder in op GNOME:

   `DEFAULT_WM="gnome"`

4. Voer **chkconfig** uit om ervoor te zorgen dat xrdp automatisch wordt gestart nadat het systeem opnieuw is opgestart:

   `chkconfig -level 3 xrdp on`

5. Als u een probleem hebt met de RDP-verbinding, probeert u de computer opnieuw op te starten, bijvoorbeeld vanuit een PuTTy-venster:

   `/etc/xrdp/xrdp.sh restart`

6. Als een xrdp opnieuw starten die in de vorige stap wordt vermeld, niet werkt, controleert u op een pid-bestand:

   `check /var/run` 

   Zoek naar `xrdp.pid`. Als u het hebt gevonden, verwijdert u deze en probeert u het opnieuw.

### <a name="start-sap-mc"></a>SAP MC starten
Nadat u het GNOME-bureau blad hebt geïnstalleerd, start u de op Java-gebaseerde SAP MC vanuit Firefox. Als de service wordt uitgevoerd in een Azure SLES 12/SLES voor SAP-toepassingen 12 VM, wordt er mogelijk een fout weer gegeven. De fout treedt op vanwege de ontbrekende Java-browser-invoeg toepassing.

De URL voor het starten van SAP MC is `<server>:5<instance_number>13`.

Zie [de web-gebaseerde SAP-beheer console starten](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)voor meer informatie.

De volgende scherm afbeelding ziet u het fout bericht dat wordt weer gegeven wanneer de invoeg toepassing voor Java-browsers ontbreekt:

![Fout bericht dat aangeeft dat een invoeg toepassing voor Java-browsers ontbreekt](./media/hana-get-started/image013.jpg)

Een manier om het probleem op te lossen is door de ontbrekende invoeg toepassing te installeren met behulp van YaST, zoals wordt weer gegeven in de volgende scherm afbeelding:

![YaST gebruiken voor het installeren van een ontbrekende invoeg toepassing](./media/hana-get-started/image014.jpg)

Wanneer u de URL van de SAP-beheer console opnieuw opgeeft, wordt u gevraagd om de invoeg toepassing te activeren:

![Dialoog venster voor activering van invoeg toepassing aanvragen](./media/hana-get-started/image015.jpg)

Er kan ook een fout bericht over een ontbrekend bestand, JavaFX. Properties, worden weer gegeven. Het is gekoppeld aan de vereiste van Oracle Java 1,8 voor SAP GUI 7,4. Zie [SAP Note 2059429](https://launchpad.support.sap.com/#/notes/2059424)voor meer informatie.
De IBM Java-versie en het openjdk-pakket dat wordt geleverd met SLES/SLES for SAP-toepassingen 12 bevatten niet het vereiste bestand JavaFX. Properties. De oplossing is Java SE 8 van Oracle te downloaden en te installeren.

Zie voor meer informatie over een soortgelijk probleem met openjdk op openSUSE de discussie thread [SAPGui 7,4 java for openSUSE 42,1 schrikkel](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Hand matige installatie van SAP HANA: SWPM
In de reeks scherm afbeeldingen in deze sectie ziet u de belangrijkste stappen voor het installeren van SAP NetWeaver 7,5 en SAP HANA SP12 wanneer u SWPM gebruikt met SAPinst. Als onderdeel van een installatie van NetWeaver 7,5 kan de HANA-data base ook als één exemplaar worden geïnstalleerd met SWPM.

In een voor beeld van een test omgeving hebben we één ABAP-app server (Advanced Business Application Programming) geïnstalleerd. Zoals weer gegeven in de volgende scherm afbeelding, hebben we de optie **gedistribueerde systeem** gebruikt voor het installeren van de ASCS-en primaire toepassings server exemplaren in één Azure-VM. We hebben SAP HANA als het database systeem gebruikt in een andere Azure-VM.

![ASCS en primaire toepassings server instanties geïnstalleerd met behulp van de gedistribueerde systeem optie](./media/hana-get-started/image012.jpg)

Nadat het ASCS-exemplaar is geïnstalleerd op de app Server-VM, wordt dit aangeduid met een groen pictogram in de SAP-beheer console. De/sapmnt-map met de SAP-profielmap moet worden gedeeld met de SAP HANA DB-Server-VM. De installatie stap van de data base moet toegang hebben tot deze gegevens. De beste manier om toegang te bieden is door gebruik te maken van NFS, dat kan worden geconfigureerd met behulp van YaST.

![SAP-beheer console met het ASCS-exemplaar dat is geïnstalleerd op de app Server-VM met behulp van een groen pictogram](./media/hana-get-started/image016.jpg)

Op de VM van de app-server wordt de/sapmnt-map gedeeld via NFS met behulp van de **rw's** en de **no_root_squash** opties. De standaard waarden zijn **ro** en **root_squash**, wat kan leiden tot problemen bij het installeren van het data base-exemplaar.

![De/sapmnt-map via NFS delen met behulp van de rw's en no_root_squash opties](./media/hana-get-started/image017b.jpg)

Als de volgende scherm afbeelding wordt weer gegeven, moet de/sapmnt-share van de virtuele machine van de app-server worden geconfigureerd op de SAP HANA DB-Server-VM met behulp van de **NFS-client** en YaST:

![De/sapmnt-share die is geconfigureerd met behulp van de NFS-client](./media/hana-get-started/image018b.jpg)

Als u een gedistribueerde NetWeaver 7,5-installatie wilt uitvoeren, dat wil zeggen een **Data Base-exemplaar**, meldt u zich aan bij de SAP Hana DB-Server-VM en start u SWPM:

![Een Data Base-exemplaar installeren door u aan te melden bij de SAP HANA DB-Server-VM en SWPM te starten](./media/hana-get-started/image019.jpg)

Nadat u de **standaard** installatie en het pad naar de installatie media hebt geselecteerd, voert u een db-sid, de hostnaam, het exemplaar nummer en het wacht woord van het DB-systeem beheerder in:

![De aanmeldings pagina van de SAP HANA database systeem beheerder](./media/hana-get-started/image035b.jpg)

Voer het wacht woord in voor het DBACOCKPIT-schema:

![Het vak voor het invoeren van een wacht woord voor het DBACOCKPIT-schema](./media/hana-get-started/image036b.jpg)

Voer een vraag in voor het SAPABAP1-schema wachtwoord:

![Voer een vraag in voor het SAPABAP1-schema wachtwoord](./media/hana-get-started/image037b.jpg)

Nadat elke taak is voltooid, wordt er een groen vinkje weer gegeven naast elke fase van het DB-installatie proces. Het bericht ' uitvoering van... Data base-exemplaar is voltooid ' wordt weer gegeven.

![Taak voltooid venster met bevestigings bericht](./media/hana-get-started/image023.jpg)

Nadat de installatie is voltooid, wordt in de SAP-beheer console ook het data base-exemplaar met een groen pictogram weer gegeven. De volledige lijst met SAP HANA processen, zoals hdbindexserver en hdbcompileserver, wordt weer gegeven.

![SAP-beheer console venster met een lijst met SAP HANA processen](./media/hana-get-started/image024.jpg)

De volgende scherm afbeelding toont de onderdelen van de bestands structuur onder de/Hana/shared-map die tijdens de HANA-installatie SWPM gemaakt. Omdat er geen optie is om een ander pad op te geven, is het belang rijk dat u na het SAP HANA-installatie van SWPM extra schijf ruimte moet koppelen aan de map/Hana. Met deze stap wordt voor komen dat het hoofd bestandssysteem geen beschik bare ruimte meer heeft.

![De/Hana/Shared Directory-bestands structuur die is gemaakt tijdens de HANA-installatie](./media/hana-get-started/image025.jpg)

In deze scherm afbeelding ziet u de bestands structuur van de map/usr/sap:

![De structuur van het/usr/sap-bestand](./media/hana-get-started/image026.jpg)

De laatste stap van de gedistribueerde ABAP-installatie is het installeren van het primaire toepassings Server exemplaar:

![ABAP-installatie met het primaire toepassings Server exemplaar als de laatste stap](./media/hana-get-started/image027b.jpg)

Nadat het primaire toepassings Server exemplaar en de SAP-gebruikers interface zijn geïnstalleerd, gebruikt u de **DBA cockpit** -trans actie om te controleren of de SAP Hana installatie correct is voltooid:

![Het DBA cockpit-venster met de geslaagde installatie bevestigen](./media/hana-get-started/image028b.jpg)

Als laatste stap is het verstandig eerst HANA Studio te installeren op de SAP-app Server VM. Maak vervolgens verbinding met de SAP HANA instantie die wordt uitgevoerd op de DB-Server-VM.

![SAP HANA Studio installeren in de SAP app Server-VM](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Hand matige installatie van SAP HANA: HDBLCM
Naast het installeren van SAP HANA als onderdeel van een gedistribueerde installatie met behulp van SWPM, kunt u de HANA-zelfstandige eerst installeren met behulp van HDBLCM. U kunt vervolgens SAP NetWeaver 7,5 installeren. De scherm afbeeldingen in deze sectie laten zien hoe dit proces werkt.

Zie voor meer informatie over het HANA HDBLCM-hulp programma:

* [Kies het juiste SAP Hana HDBLCM voor uw taak](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm).
* [SAP Hana levenscyclus beheer hulpprogramma's](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm).
* [SAP Hana server-installatie en-update handleiding](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf).

U wilt problemen voor komen met een standaard instelling voor de groeps-ID voor de `\<HANA SID\>adm user`, die wordt gemaakt door het HDBLCM-hulp programma. Voordat u SAP HANA installeert via HDBLCM, definieert u een nieuwe groep met de naam `sapsys` met behulp van groeps-ID `1001`:

![Nieuwe groep "sapsys" die is gedefinieerd met behulp van groeps-ID 1001](./media/hana-get-started/image030.jpg)

Wanneer u HDBLCM voor de eerste keer start, wordt er een eenvoudig menu Start weer gegeven. Selecteer item 1, **nieuw systeem installeren**:

![De optie ' nieuw systeem installeren ' in het Start venster van HDBLCM](./media/hana-get-started/image031.jpg)

Op de volgende scherm afbeelding ziet u alle belang rijke opties die u eerder hebt geselecteerd.

> [!IMPORTANT]
> Directory's met de naam voor HANA-logboeken en-gegevens volumes en het installatiepad, dat in dit voor beeld/Hana/Shared is, en/usr/sap mogen geen deel uitmaken van het hoofd bestands systeem. Deze mappen maken deel uit van de Azure-gegevens schijven die zijn gekoppeld aan de virtuele machine. Zie de sectie schijf installatie voor meer informatie. 

Deze methode helpt voor komen dat het hoofd bestandssysteem bijna vol is. U ziet dat de HANA-systeem beheerder gebruikers-ID heeft `1005` en deel uitmaakt van de `sapsys` groep met de ID `1001`die is gedefinieerd vóór de installatie.

![Lijst met alle belang rijke SAP HANA onderdelen die eerder zijn geselecteerd](./media/hana-get-started/image032.jpg)

Controleer de `\<HANA SID\>adm user` gegevens in de map/etc/passwd. Zoek naar `azdadm`, zoals wordt weer gegeven in de volgende scherm afbeelding:

![HANA \<HANA SID\>adm-gebruikers gegevens die worden weer gegeven in de map/etc/passwd](./media/hana-get-started/image033.jpg)

Nadat u SAP HANA hebt geïnstalleerd met behulp van HDBLCM, ziet u de bestands structuur in SAP HANA Studio, zoals wordt weer gegeven in de volgende scherm afbeelding. Het SAPABAP1-schema, dat alle SAP NetWeaver-tabellen bevat, is nog niet beschikbaar.

![SAP HANA bestands structuur in SAP HANA Studio](./media/hana-get-started/image034.jpg)

Nadat u SAP HANA hebt geïnstalleerd, kunt u de SAP NetWeaver hierop installeren. Zoals in de volgende scherm afbeelding wordt weer gegeven, is de installatie uitgevoerd als een gedistribueerde installatie met behulp van SWPM. Dit proces wordt beschreven in de vorige sectie. Wanneer u de data base-instantie installeert met behulp van SWPM, voert u dezelfde gegevens in met behulp van HDBLCM. U kunt bijvoorbeeld de hostnaam, HANA SID en het exemplaar nummer invoeren. SWPM maakt vervolgens gebruik van de bestaande HANA-installatie en voegt meer schema's toe.

![Een gedistribueerde installatie die wordt uitgevoerd met behulp van SWPM](./media/hana-get-started/image035b.jpg)

In de volgende scherm afbeelding ziet u de SWPM-installatie stap waarin u gegevens over het DBACOCKPIT-schema invoert:

![De SWPM-installatie stap waarbij DBACOCKPIT-schema gegevens worden ingevoerd](./media/hana-get-started/image036b.jpg)

Voer gegevens over het SAPABAP1-schema in:

![Gegevens over het SAPABAP1-schema invoeren](./media/hana-get-started/image037b.jpg)

Nadat de installatie van het SWPM-data base-exemplaar is voltooid, kunt u het SAPABAP1-schema zien in SAP HANA studio:

![Het SAPABAP1-schema in SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Nadat de installatie van de SAP-app-server en de SAP-GUI is voltooid, controleert u het HANA DB-exemplaar met behulp van de **DBA cockpit** -trans actie:

![Het exemplaar van de HANA-data base is geverifieerd met de DBA cockpit-trans actie](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>SAP-software downloads
U kunt software downloaden van de SAP Service Marketplace, zoals wordt weer gegeven in de volgende scherm afbeeldingen.

NetWeaver 7,5 voor Linux/HANA downloaden:

 ![Installatie-en upgrade venster van de SAP-service voor het downloaden van NetWeaver 7,5](./media/hana-get-started/image001.jpg)

HANA SP12 platform Edition downloaden:

 ![SAP-Service-installatie en-upgrade venster voor het downloaden van HANA SP12 platform Edition](./media/hana-get-started/image002.jpg)

