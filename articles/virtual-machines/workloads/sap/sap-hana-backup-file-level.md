---
title: SAP HANA Azure Backup op bestandsniveau | Microsoft Documenten
description: Er zijn twee belangrijke back-upmogelijkheden voor SAP HANA op virtuele Azure-machines, dit artikel behandelt SAP HANA Azure Backup op bestandsniveau
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 93b67936166eb73db5e9a15db42c2c6135794108
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271390"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure Backup op bestandsniveau

## <a name="introduction"></a>Inleiding

Dit artikel is een gerelateerd artikel [over back-uphandleiding voor SAP HANA op Azure Virtual Machines,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)dat een overzicht en informatie biedt over aan de slag en meer details over Azure Backup-service en opslagmomentopnamen. 

Verschillende VM-typen in Azure staan een ander aantal gekoppelde VHD's toe. De exacte details zijn gedocumenteerd in [Grootte voor Virtuele Linux-machines in Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) Voor de tests waarnaar in deze documentatie wordt verwezen, hebben we een GS5 Azure VM gebruikt, waarmee 64 gekoppelde gegevensschijven kunnen worden aangesloten. Voor grotere SAP HANA-systemen kan al een aanzienlijk aantal schijven worden genomen voor gegevens en logbestanden, mogelijk in combinatie met softwarestriping voor een optimale schijf-IO-doorvoer. Lees voor meer informatie over voorgestelde schijfconfiguraties voor SAP HANA-implementaties op Azure VM's het artikel [SAP HANA Azure virtual machine storage configurations](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). De aanbevelingen zijn inclusief aanbevelingen voor schijfruimte voor lokale back-ups.

De standaardmanier om back-up/herstel op bestandsniveau te beheren is met een file-based back-up via SAP HANA Studio of via SAP HANA SQL statements. Lees voor meer informatie het artikel [SAP HANA SQL and System Views Reference](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf).

![Deze afbeelding toont het dialoogvenster van het back-upmenu-item in SAP HANA Studio](media/sap-hana-backup-file-level/backup-menue-dialog.png)

Deze afbeelding toont het dialoogvenster van het back-upmenu-item in SAP HANA Studio. Bij het &quot;kiezen&quot; van typebestand moet men een pad opgeven in het bestandssysteem waar SAP HANA de back-upbestanden schrijft. Restore werkt op dezelfde manier.

Hoewel deze keuze eenvoudig en rechttoe rechtaan klinkt, zijn er enkele overwegingen. Een Azure VM heeft een beperking van het aantal gegevensschijven dat kan worden gekoppeld. Er is mogelijk geen capaciteit om SAP HANA-back-upbestanden op te slaan op de bestandssystemen van de VM, afhankelijk van de grootte van de vereisten voor database- en schijfdoorvoer, wat mogelijk softwarestriping over meerdere gegevensschijven inhoudt. Later in dit artikel worden verschillende opties voor het verplaatsen van deze back-upbestanden en het beheren van beperkingen en prestaties van bestanden bij het verwerken van terabytes aan gegevens.

Een andere optie, die meer vrijheid biedt met betrekking tot de totale capaciteit, is Azure blob-opslag. Hoewel een enkele blob ook beperkt is tot 1 TB, is de totale capaciteit van een enkele blobcontainer momenteel 500 TB. Daarnaast geeft het klanten de keuze om &quot;&quot; zogenaamde cool blob-opslag te selecteren, wat een kostenvoordeel heeft. Zie [Azure Blob-opslag: hot-, cool- en archieftoegangslagen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal) voor meer informatie over koele blob-opslag.

Gebruik voor extra veiligheid een geo-gerepliceerd opslagaccount om de SAP HANA-back-ups op te slaan. Zie [redundantie voor Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) voor meer informatie over redundantie voor opslag en opslagreplicatie.

Men zou speciale VHD's voor SAP HANA-back-ups kunnen plaatsen in een speciaal back-upopslagaccount dat geo-gerepliceerd is. Of anders kan men de VHD's kopiëren die de SAP HANA-back-ups houden naar een geo-gerepliceerd opslagaccount of naar een opslagaccount dat zich in een andere regio bevindt.

## <a name="azure-blobxfer-utility-details"></a>Azure blobxfer-hulpprogrammagegevens

Als u mappen en bestanden op Azure-opslag wilt opslaan, kan men CLI of PowerShell gebruiken of een hulpprogramma ontwikkelen met een van de [Azure SDK's.](https://azure.microsoft.com/downloads/) Er is ook een kant-en-klaar hulpprogramma, AzCopy, voor het kopiëren van gegevens naar Azure-opslag. (zie [Gegevens overbrengen met het AzCopy Command-Line Utility).](../../../storage/common/storage-use-azcopy.md)

Daarom werd blobxfer gebruikt voor het kopiëren van SAP HANA back-upbestanden. Het is open source, gebruikt door veel klanten in productieomgevingen, en beschikbaar op [GitHub.](https://github.com/Azure/blobxfer) Met deze tool u gegevens rechtstreeks kopiëren naar Azure Blob-opslag of Azure-bestandsshare. Het biedt ook een scala aan handige functies, zoals md5-hash, of automatische parallellisme bij het kopiëren van een directory met meerdere bestanden.

## <a name="sap-hana-backup-performance"></a>Back-upprestaties van SAP HANA
In dit hoofdstuk worden prestatieoverwegingen besproken. De bereikte aantallen vertegenwoordigen mogelijk niet de meest recente status, omdat er een gestage ontwikkeling is om een betere doorvoer naar Azure-opslag te bereiken. Als gevolg hiervan moet u afzonderlijke tests uitvoeren voor uw configuratie en Azure-regio.

![Deze screenshot is van de SAP HANA back-up console in SAP HANA Studio](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Deze screenshot toont de SAP HANA back-up console van SAP HANA Studio. Het duurde ongeveer 42 minuten om een back-up van 230 GB uit te voeren op één Azure Standard HDD-opslagschijf die is gekoppeld aan de HANA VM met behulp van het XFS-bestandssysteem op de ene schijf.

![Deze screenshot is van YaST op de SAP HANA test VM](media/sap-hana-backup-file-level/one-backup-disk.png)

Deze screenshot is van YaST op de SAP HANA test VM. U de 1 TB enkele schijf voor SAP HANA back-up. Het duurde ongeveer 42 minuten om back-up 230 GB. Daarnaast zijn er vijf schijven van 200 GB aangesloten en is er software-RAID md0 gemaakt, met striping bovenop deze vijf Azure-gegevensschijven.

![Dezelfde back-up op software-RAID herhalen met striping over vijf aangesloten Azure-standaardopslaggegevensschijven](media/sap-hana-backup-file-level/five-backup-disks.png)

Het herhalen van dezelfde back-up op software RAID met striping over vijf aangesloten Azure standaard opslaggegevens schijven bracht de back-up tijd van 42 minuten naar 10 minuten. De schijven werden bevestigd zonder caching aan vm. Deze oefening toont het belang van schijf schrijfdoorvoer voor het bereiken van een goede back-up tijd. U overschakelen naar Azure Standard SSD-opslag of Azure Premium Storage om het proces verder te versnellen voor optimale prestaties. In het algemeen wordt Azure-standaard HDD-opslag niet aanbevolen en alleen voor demonstratiedoeleinden gebruikt. Aanbeveling is om een minimum aan Azure Standard SSD-opslag of Azure Premium Storage te gebruiken voor productiesystemen.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>SAP HANA-back-upbestanden kopiëren naar Azure blob-opslag
De vermelde prestatienummers, back-upduurnummers en nummers met de kopieerduur vertegenwoordigen mogelijk niet de meest recente status van Azure-technologie. Microsoft verbetert azure-opslag gestaag om meer doorvoer en lagere latencies te leveren. Daarom zijn de aantallen slechts voor demonstratiedoeleinden. U moet testen op uw individuele behoefte in de Azure-regio van uw keuze om te kunnen beoordelen met methode is het beste voor u.

Een andere optie om SAP HANA-back-upbestanden snel op te slaan, is Azure blob-opslag. Eén blobcontainer heeft een limiet van ongeveer 500 TB, genoeg voor SAP HANA-systemen, met behulp van M32ts, M32ls, M64ls en GS5 VM-typen Azure, om voldoende SAP HANA-back-ups te behouden. Klanten hebben de &quot;&quot; keuze &quot;&quot; tussen hot en cold blob-opslag (zie [Azure Blob-opslag: hot, cool en archieftoegangslagen).](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)

Met het blobxfer-hulpprogramma is het eenvoudig om de SAP HANA-back-upbestanden rechtstreeks naar Azure blob-opslag te kopiëren.

![Hier kan men de bestanden van een volledige SAP HANA file backup zien](media/sap-hana-backup-file-level/list-of-backups.png)

U de bestanden van een volledige SAP HANA-bestandsback-up bekijken. Van de vier bestanden, de grootste heeft ongeveer 230 GB groot.

![Het duurde ongeveer 3000 seconden om de 230 GB te kopiëren naar een Azure-standaardopslagaccountblobcontainer](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

Het gebruik van md5-hash in de eerste test duurde ongeveer 3000 seconden om de 230 GB te kopiëren naar een Azure-standaardopslagaccountblobcontainer.

Met de HANA Studio-back-upconsole kan men de maximale bestandsgrootte van HANA-back-upbestanden beperken. In de voorbeeldomgeving verbeterde het de prestaties door meerdere kleinere back-upbestanden te hebben, in plaats van één groot bestand van 230 GB.

Het instellen van de limiet voor de grootte van het back-upbestand aan de HANA-kant&#39;de back-uptijd niet verbeteren, omdat de bestanden opeenvolgend worden geschreven. De limiet voor de bestandsgrootte is ingesteld op 60 GB, dus de back-up heeft vier grote gegevensbestanden gemaakt in plaats van het enkele bestand van 230 GB. Het gebruik van meerdere back-upbestanden kan een noodzaak worden voor het maken van back-ups van HANA-databases als uw back-updoelen beperkingen hebben op bestandsgroottes van blobformaten.

![Om parallellisme van het blobxfer-gereedschap te testen, is de maximale bestandsgrootte voor HANA-back-ups vervolgens ingesteld op 15 GB](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

Om parallellisme van het blobxfer-gereedschap te testen, werd de maximale bestandsgrootte voor HANA-back-ups ingesteld op 15 GB, wat resulteerde in 19 back-upbestanden. Deze configuratie bracht de tijd voor blobxfer om de 230 GB naar Azure blob-opslag te kopiëren van 3000 seconden naar 875 seconden.

Houd er bij het verkennen van het kopiëren van back-ups die zijn uitgevoerd ten opzichte van lokale schijven naar andere locaties, zoals Azure blob-opslag, er rekening mee dat de bandbreedte die wordt gebruikt door een eventueel parallel kopieerproces, wordt verwerkt in vergelijking met het netwerk- of opslagquotum van uw afzonderlijke VM-type. Als gevolg hiervan moet u de duur van de kopie in evenwicht brengen met de netwerk- en opslagbandbreedte die de normale werkbelasting in de VM vereist. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>SAP HANA-back-upbestanden kopiëren naar NFS-share

Microsoft Azure biedt native NFS-shares aan via [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). U verschillende volumes van tientallen tbs's maken die back-ups kunnen opslaan en beheren. U ook momentopname die volumes op basis van de technologie van NetApp. Azure NetApp Files (ANF) wordt aangeboden in drie verschillende serviceniveaus die verschillende opslagdoorvoer bieden. Lees voor meer informatie het artikel [Serviceniveaus voor Azure NetApp-bestanden](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). U een NFS-volume maken en monteren vanuit ANF zoals beschreven in het artikel [Quickstart: Azure NetApp-bestanden instellen en een NFS-volume maken.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes?tabs=azure-portal)

Naast het gebruik van native NFS-volumes van Azure via ANF, zijn er verschillende mogelijkheden om eigen implementaties te maken die NFS-shares op Azure bieden. Allen hebben het nadeel dat u deze oplossingen zelf moet implementeren en beheren. Sommige van deze mogelijkheden zijn gedocumenteerd in deze artikelen:

- [Hoge beschikbaarheid voor NFS op Azure VM's op SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- [GlusterFS op Azure VM's op Red Hat Enterprise Linux voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)

NFS-aandelen die zijn gemaakt met de hierboven beschreven middelen, kunnen worden gebruikt om HANA-back-ups rechtstreeks uit te voeren tegen of om back-ups die tegen lokale schijven zijn uitgevoerd, te kopiëren naar die NFS-shares.

> [!NOTE]
> SAP HANA ondersteunt NFS v3 en NFS v4.x. Een ander formaat zoals SMB met CIFS-bestandssysteem wordt niet ondersteund om HANA-back-ups tegen te schrijven. Zie ook [SAP support note #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>SAP HANA-back-upbestanden kopiëren naar Azure-bestanden

Het is mogelijk om een Azure Files-share te monteren in een Azure Linux VM. In het artikel [Hoe azure bestandsopslag met Linux te gebruiken,](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux) vindt u meer informatie over het uitvoeren van de configuratie. Lees het artikel Azure Files schaalbaarheid en [prestatiedoelen](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets)voor beperkingen van azure-bestanden of Azure premium-bestanden.

> [!NOTE]
> SMB met CIFS-bestandssysteem wordt niet ondersteund door SAP HANA om HANA back-ups tegen te schrijven. Zie ook [SAP support note #1820529](https://launchpad.support.sap.com/#/notes/1820529). Als gevolg hiervan u deze oplossing alleen gebruiken als eindbestemming van een HANA-databaseback-up die rechtstreeks is uitgevoerd tegen lokale aangesloten schijven
> 

In een test uitgevoerd tegen Azure Files, niet Azure Premium Files duurde het ongeveer 929 seconden om 19 back-upbestanden met een totaal volume van 230 GB te kopiëren. We verwachten dat de tijd waarop Azure Premium Files wordt gebruikt, veel beter is. U moet er echter rekening mee houden dat u de belangen van een snelle kopie in evenwicht moet brengen met de vereisten die uw werkbelasting heeft voor de netwerkbandbreedte. Aangezien elk Azure VM-type een netwerkbandbreedtequotum afdwingt, moet u binnen het bereik van dat quotum blijven met uw werkbelasting plus de kopie van de back-upbestanden.

![Dit cijfer toont aan dat het ongeveer 929 seconden duurde om 19 SAP HANA back-upbestanden te kopiëren](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Het opslaan van SAP HANA-back-upbestanden op Azure-bestanden kan een interessante optie zijn. Vooral met de verbeterde latentie en doorvoer van Azure Premium Files.

## <a name="next-steps"></a>Volgende stappen
* [Back-uphandleiding voor SAP HANA op Azure Virtual Machines](sap-hana-backup-guide.md) geeft een overzicht en informatie over aan de slag.
* Zie [SAP HANA (grote exemplaren) hoge beschikbaarheid en noodherstel op Azure](hana-overview-high-availability-disaster-recovery.md)voor meer informatie over het instellen van hoge beschikbaarheid en het plannen van noodherstel van SAP HANA op Azure.
