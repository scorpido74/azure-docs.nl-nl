---
title: Back-uphandleiding voor SAP HANA op Azure Virtual Machines | Microsoft Documenten
description: Back-uphandleiding voor SAP HANA biedt twee belangrijke back-upmogelijkheden voor SAP HANA op Azure virtuele machines
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: bb32350597059209e5baf01d53b0c59fdc2344f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255222"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Back-uphandleiding voor SAP HANA op Azure Virtual Machines

## <a name="getting-started"></a>Aan de slag

De back-uphandleiding voor SAP HANA die wordt uitgevoerd op virtuele Azure-machines, beschrijft alleen Azure-specifieke onderwerpen. Controleer de SAP HANA-documentatie voor algemene SAP HANA-back-upgerelateerde items. We verwachten van u dat u bekend bent met de belangrijkste database back-up strategieën, de redenen en motivaties om een goede en geldige back-up strategie te hebben, en zijn zich bewust van de eisen die uw bedrijf heeft voor de back-up procedure, bewaarperiode van back-ups en herstel Procedure.

SAP HANA wordt officieel ondersteund op verschillende Azure VM-typen, zoals Azure M-series. Voor een volledige lijst van SAP HANA-gecertificeerde Azure VM's en HANA Large Instance-eenheden, [raadpleegt u Find Certified IaaS-platforms.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Microsoft Azure biedt een aantal eenheden waar SAP HANA niet-gevirtualiseerd draait op fysieke servers. Deze service heet [HANA Large Instances](hana-overview-architecture.md). Deze handleiding heeft geen betrekking op back-upprocessen en hulpprogramma's voor HANA Large Instances. Maar zal worden beperkt tot Azure virtuele machines. Lees het artikel [HLI Backup and Restore](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)voor meer informatie over back-up-/herstelprocessen met HANA Large Instances.

De focus van dit artikel ligt op drie back-upmogelijkheden voor SAP HANA op Azure virtuele machines:

- HANA-back-up via [Azure Backup Services](https://docs.microsoft.com/azure/backup/backup-overview) 
- HANA-back-up naar het bestandssysteem in een Azure Linux Virtual Machine (zie [SAP HANA Azure Backup op bestandsniveau)](sap-hana-backup-file-level.md)
- HANA-back-up op basis van opslagmomentopnamen met de azure-opslagblobmomentopnamefunctie handmatig of Azure Backup-service


SAP HANA biedt een back-up-API, waarmee back-uptools van derden rechtstreeks kunnen worden geïntegreerd met SAP HANA. Producten zoals Azure Backup-service of [Commvault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) gebruiken deze eigen interface om SAP HANA-database te activeren of logboekback-ups opnieuw te gebruiken. 


Informatie over hoe u vinden welke SAP-software wordt ondersteund op Azure, vindt u in het artikel [Welke SAP-software wordt ondersteund voor Azure-implementaties.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)

## <a name="azure-backup-service"></a>Azure Backup Service

Het eerste scenario dat wordt weergegeven, is een scenario `backint` waarin Azure Backup Service de SAP HANA-interface gebruikt om een streamingback-up uit te voeren vanuit een SAP HANA-database. Of u gebruikt een meer algemene mogelijkheid van Azure Backup-service om een consistente schijfmomentopname voor toepassingen te maken en die te laten overzetten naar de Azure Backup-service.

Azure Backup integreert en is gecertificeerd als back-upoplossing voor SAP HANA met behulp van de eigen SAP HANA-interface genaamd [backint.](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) Lees de [artikelondersteuningsmatrix voor back-ups van SAP HANA-databases in Azure VM's voor](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)meer informatie over de oplossing, de mogelijkheden en de Azure-regio's waar deze beschikbaar is. Lees het artikel [Over SAP HANA-databaseback-up in Azure VM's](https://docs.microsoft.com/azure/backup/sap-hana-db-about)voor meer informatie en principes over Azure Backup-service voor HANA. 

De tweede mogelijkheid om gebruik te maken van Azure Backup-service is het maken van een consistente back-up van een toepassing met schijfmomentopnamen van Azure Premium Storage. Andere HANA-gecertificeerde Azure-opslagen, zoals [Azure Ultra disk](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd) en Azure [NetApp Files,](https://azure.microsoft.com/services/netapp/) ondersteunen dit soort momentopnamen niet via Azure Backup-service. Het lezen van deze artikelen:

- [De infrastructuur voor back-ups van virtuele Azure-machines plannen](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Toepassingsconsistente back-up van Azure Linux VM's](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent) 

deze opeenvolging van activiteit ontstaat:

- Azure Backup moet een pre-snapshot script uitvoeren dat de toepassing, in dit geval SAP HANA, in een consistente status plaatst
- Aangezien deze consistente status is bevestigd, voert Azure Backup de schijfmomentopnamen uit
- Na het voltooien van de momentopnamen maakt Azure Backup de activiteit ongedaan die het heeft uitgevoerd in het pre-snapshotscript
- Na een succesvolle uitvoering wordt de gegevens van Azure Backup naar de kluis Back-up gestreamd

In het geval van SAP HANA gebruiken de meeste klanten Azure Write Accelerator voor de volumes die het SAP HANA-redo-logboek bevatten. Azure Backup-service sluit deze volumes automatisch uit van de momentopnamen. Deze uitsluiting schaadt het vermogen van hana om te herstellen niet. Hoewel het zou blokkeren de mogelijkheid om te herstellen met bijna alle andere SAP ondersteund DBMS.

Het nadeel van deze mogelijkheid is het feit dat je nodig hebt om je eigen pre- en post-snapshot script te ontwikkelen. Het pre-snapshot script moet een HANA-momentopname maken en eventuele uitzonderingsgevallen afhandelen. Terwijl het post-snapshot script de HANA-momentopname opnieuw moet verwijderen. Voor meer informatie over de vereiste logica, begint u met [SAP-ondersteuningsnotitie #2039883](https://launchpad.support.sap.com/#/notes/2039883). De overwegingen van de sectie 'SAP HANA data consistency when taking storage snapshots' in dit artikel zijn volledig van toepassing op dit soort back-ups.

> [!NOTE]
> Schijfmomentopnamen op basis van back-ups voor SAP HANA in implementaties waarbij meerdere databasecontainers worden gebruikt, vereisen een minimale release van HANA 2.0 SP04
> 

Zie details over opslagmomentopnamen later in dit document.

![Dit cijfer toont twee mogelijkheden voor het opslaan van de huidige VM-status](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>Andere HANA-back-upmethoden
Er zijn drie andere back-upmethoden of paden die kunnen worden overwogen:

- Een back-up maken van een NFS-share dat is gebaseerd op Azure NetApp Files (ANF). ANF heeft opnieuw de mogelijkheid om snapshots te maken van de volumes waarop u back-ups opslaat. Gezien de doorvoer die u uiteindelijk nodig hebt om de back-ups te schrijven, kan deze oplossing een dure methode worden. Hoewel eenvoudig vast te stellen, omdat HANA de back-ups rechtstreeks kan schrijven naar het Azure native NFS-aandeel
- Het uitvoeren van de HANA Backup tegen gekoppelde schijven van standaard SSD of Azure Premium Storage. Als volgende stap u deze back-upbestanden kopiëren tegen Azure Blob-opslag. Deze strategie kan prijs verstandig aantrekkelijk
- Het uitvoeren van de HANA Backup tegen gekoppelde schijven van standaard SSD of Azure Premium Storage. Als volgende stap wordt de schijf regelmatig gemomentopnameereerd. Na de eerste momentopname kunnen incrementele momentopnamen worden gebruikt om de kosten te verlagen

![Dit cijfer toont opties voor het nemen van een SAP HANA-bestandsback-up in de VM](media/sap-hana-backup-guide/other-hana-backup-paths.png)

Dit cijfer toont opties voor het nemen van een SAP HANA-bestandsback-up in de VM, en vervolgens op te slaan HANA back-upbestanden ergens anders met behulp van verschillende tools. Alle oplossingen die geen back-upservice van derden of Azure Backup-service behoeven, hebben echter verschillende obstakels met elkaar gemeen. Sommige van hen kunnen worden vermeld, zoals bewaarbeheer, automatisch herstelproces en automatisch point-in-time herstel als Azure Backup-service of andere gespecialiseerde back-upsuites en -services van derden bieden. Veel van die services van derden kunnen worden uitgevoerd op Azure. 


## <a name="sap-resources-for-hana-backup"></a>SAP-bronnen voor HANA-back-up

### <a name="sap-hana-backup-documentation"></a>SAP HANA back-updocumentatie

- [Inleiding tot SAP HANA Administration](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Uw back-up- en herstelstrategie plannen](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [HANA Backup plannen met ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Data back-ups plannen (SAP HANA Cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Veelgestelde vragen over SAP HANA-back-up in [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Veelgestelde vragen over SAP HANA-database en storage snapshots in [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Ongeschikte netwerkbestandssystemen voor back-up en herstel in [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Hoe de juistheid van SAP HANA-back-up te verifiëren
Onafhankelijk van uw back-upmethode is het uitvoeren van een testherstel tegen een ander systeem een absolute noodzaak. Deze aanpak biedt een manier om ervoor te zorgen dat een back-up correct is, en interne processen voor back-up en herstel werk zoals verwacht. Hoewel het herstellen van back-ups een on-premises hindernis kan zijn vanwege de infrastructuurvereisten, is het veel gemakkelijker om in de cloud te bereiken door hiervoor tijdelijk de nodige resources te leveren. Het is juist dat er tools zijn die zijn voorzien van HANA die back-upbestanden kunnen controleren op de mogelijkheid om te herstellen. Het doel van frequente hersteloefeningen is echter om het proces van het herstellen van een database te testen en dat proces te trainen met het operationele personeel.

Houd er rekening mee dat het doen van een eenvoudige herstellen en controleren of HANA is up and running is niet voldoende. U moet een tabelconsistentiecontrole uitvoeren om er zeker van te zijn dat de herstelde database in orde is. SAP HANA biedt verschillende soorten consistentiecontroles beschreven in [SAP Note 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Informatie over de consistentiecontrole van de tabel is ook te vinden op de SAP-website van [tabel- en catalogusconsistentiecontroles.](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Voor- en nadelen van HANA-back-up versus storage snapshot

SAP geeft&#39;geen voorkeur aan HANA-back-up versus opslagmomentopname. Het bevat hun voors en tegens, zodat men kan bepalen welke te gebruiken, afhankelijk van de situatie en de beschikbare opslagtechnologie (zie [Planning Your Backup and Recovery Strategy).](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)

Houd er op Azure rekening mee dat de azure blob-momentopnamefunctie&#39;geen consistentie van het bestandssysteem over meerdere schijven biedt (zie [Blob-momentopnamen gebruiken met PowerShell).](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) 

Bovendien moet men de implicaties voor facturering begrijpen wanneer u vaak met blob-momentopnamen werkt, zoals beschreven in dit artikel: [Inzicht in hoe momentopnamen kosten genereren](/rest/api/storageservices/understanding-how-snapshots-accrue-charges), het is&#39;niet zo voor de hand liggend als het gebruik van virtuele Azure-schijven.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA-gegevensconsistentie bij het maken van opslagmomentopnamen

Zoals eerder gedocumenteerd, is het beschrijven van de snapshot back-upmogelijkheden van Azure Backup, bestandssysteem en toepassingsconsistentie verplicht bij het maken van opslagmomentopnamen. De eenvoudigste manier om problemen te voorkomen zou zijn om af te sluiten SAP HANA, of misschien zelfs de hele virtuele machine. Iets wat niet haalbaar is voor een productie-exemplaar.

> [!NOTE]
> Schijfmomentopnamen op basis van back-ups voor SAP HANA in implementaties waarbij meerdere databasecontainers worden gebruikt, vereisen een minimale release van HANA 2.0 SP04
> 

Azure-opslag biedt geen consistentie in het bestandssysteem over meerdere schijven of volumes die tijdens het momentopnameproces aan een vm zijn gekoppeld. Dat betekent dat de consistentie van de toepassing tijdens de momentopname moet worden geleverd door de toepassing, in dit geval SAP HANA zelf. [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883) bevat belangrijke informatie over SAP HANA-back-ups door opslagmomentopnamen. Met XFS-bestandssystemen is het bijvoorbeeld noodzakelijk om **xfs-blokkering\_** uit te voeren voordat u een opslagmomentopname start om consistentie van de toepassing te bieden (zie [de freeze van\_de toepassing(8) - Linux-manpagina](https://linux.die.net/man/8/xfs_freeze) voor details over het bevriezen van **xfs).\_**

Ervan uitgaande dat er een XFS-bestandssysteem is dat vier Virtuele Azure-schijven bevat, bieden de volgende stappen een consistente momentopname die het HANA-gegevensgebied vertegenwoordigt:

1. HanA-gegevensmomentopname voorbereiden
1. De bestandssystemen van alle schijven/volumes blokkeren (bijvoorbeeld het **bevriezen van\_xfs)**
1. Alle benodigde blobmomentopnamen maken op Azure
1. Het bestandssysteem deblokkering ontlopen
1. De HANA-gegevensmomentopname bevestigen (verwijdert de momentopname)

Wanneer u de mogelijkheid van azure backup gebruikt om consistente momentopnameback-ups van toepassingen uit te voeren, moeten stappen #1 door u worden gecodeerd/gescript voor het pre-snapshotscript. Azure Backup-service voert stappen uit #2 en #3. Stappen #4 en #5 moeten opnieuw worden verstrekt door uw code in het script na momentopname. Als u geen Azure-back-upservice gebruikt, moet u ook de #2 en #3 op uw eigen code/scriptstap.
Meer informatie over het maken van HANA data snapshots is te vinden in deze artikelen:

- [HANA-gegevensmomentopnamen] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- Meer details om stap #1 uit te voeren vindt u in [artikel Een datamomentopname maken (Native SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 
- Details om HANA-gegevensmomentopnamen te bevestigen/verwijderen als dat nodig is in stap #5 zijn te vinden in het artikel [Een gegevensmomentopname maken (Native SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 

Het is belangrijk om de HANA momentopname te bevestigen. Vanwege de &quot;Copy-on-Write&quot; heeft SAP HANA mogelijk geen extra schijfruimte nodig in deze snapshot-voorbereidingsmodus. Het&#39;ook niet mogelijk om nieuwe back-ups te starten totdat de SAP HANA snapshot is bevestigd.


### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA back-up planning strategie

In het SAP HANA-artikel [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) staat een basisplan om back-ups te maken. Vertrouw op SAP documentatie rond HANA en uw ervaringen met andere DBMS bij het definiëren van de back-up /restore strategie en het proces voor SAP HANA. De volgorde van verschillende typen back-ups en de bewaartermijn zijn sterk afhankelijk van de SLA's die u moet leveren.


### <a name="sap-hana-backup-encryption"></a>SAP HANA back-upversleuteling

SAP HANA biedt versleuteling van gegevens en logboek. Als SAP HANA-gegevens en -logboeken niet zijn versleuteld, worden de back-ups niet standaard versleuteld. SAP HANA biedt echter een aparte back-upversleuteling zoals gedocumenteerd in [SAP HANA Backup Encryption.](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html) Als u oudere versies van SAP HANA uitvoert, moet u mogelijk controleren of back-upversleuteling al deel uitmaakte van de functionaliteit die al is verstrekt.  


## <a name="next-steps"></a>Volgende stappen
* [SAP HANA Azure Backup op bestandsniveau](sap-hana-backup-file-level.md) beschrijft de op bestanden gebaseerde back-upoptie.
* Zie [SAP HANA (grote exemplaren) hoge beschikbaarheid en noodherstel op Azure](hana-overview-high-availability-disaster-recovery.md)voor meer informatie over het instellen van hoge beschikbaarheid en het plannen van noodherstel van SAP HANA op Azure.
