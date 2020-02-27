---
title: Azure Backup op bestands niveau SAP HANA | Microsoft Docs
description: Er zijn twee belang rijke back-upmogelijkheden voor SAP HANA op virtuele machines van Azure. dit artikel behandelt SAP HANA Azure Backup op bestands niveau
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: hermannd
ms.openlocfilehash: d1044e4cbfd59cdf58af1a132a64e7143083905c
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616157"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Azure Backup op bestands niveau SAP HANA

## <a name="introduction"></a>Inleiding

Dit maakt deel uit van een reeks verwante artikelen in drie gedeelten over SAP HANA back-up. De [back-uphandleiding voor SAP Hana op Azure virtual machines](./sap-hana-backup-guide.md) bevat een overzicht en informatie over aan de slag, en [SAP Hana back-up op basis van moment opnamen van opslag](./sap-hana-backup-storage-snapshots.md) bestrijkt de back-upoptie voor opslag momentopnamen.

Verschillende VM-typen in azure staan een ander aantal aangesloten Vhd's toe. De exacte details worden gedocumenteerd in [grootten voor virtuele Linux-machines in azure](../../linux/sizes.md). Voor de tests waarnaar in deze documentatie wordt verwezen, hebben we een GS5 Azure-VM gebruikt, waarmee 64 gekoppelde gegevens schijven kunnen worden toegevoegd. Voor grotere SAP HANA systemen is er mogelijk al een groot aantal schijven gemaakt voor gegevens-en logboek bestanden, mogelijk in combi natie met software striping voor optimale IO-door Voer van de schijf. Lees het artikel [SAP Hana in de Azure-bedienings handleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)voor meer informatie over aanbevolen schijf configuraties voor SAP Hana implementaties op virtuele machines van Azure. De aanbevelingen die worden gedaan, zijn ook inclusief schijf ruimte aanbevelingen voor lokale back-ups.

Er is op dit moment geen SAP HANA back-upintegratie beschikbaar met Azure Backup-service. De standaard methode voor het beheren van back-ups/herstellen op bestands niveau is een back-up op basis van bestanden via SAP HANA Studio of via SAP HANA SQL-instructies. Zie [SAP Hana Naslag informatie over SQL-en systeem weergaven](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) .

![In deze afbeelding ziet u het dialoog venster van het menu-item back-up in SAP HANA Studio](media/sap-hana-backup-file-level/image022.png)

In deze afbeelding ziet u het dialoog venster van het menu-item back-up in SAP HANA Studio. Bij het kiezen van het type &quot;bestand moet&quot; een pad opgeven in het bestands systeem waar SAP HANA de back-upbestanden schrijft. Herstellen werkt op dezelfde manier.

Hoewel deze keuze eenvoudig en direct klinkt, zijn er enkele overwegingen. Zoals eerder vermeld, heeft een Azure VM een beperking van het aantal gegevens schijven dat kan worden bijgevoegd. Het is mogelijk dat er geen capaciteit is om SAP HANA back-upbestanden op te slaan op de bestands systemen van de virtuele machine, afhankelijk van de grootte van de data base en de vereisten voor het door Voer van de schijf, wat software striping kan inhouden op meerdere gegevens schijven. Verderop in dit artikel vindt u diverse opties voor het verplaatsen van deze back-upbestanden en het beheren van beperkingen en prestaties voor bestands grootte bij het afhandelen van terabytes aan gegevens.

Een andere optie, die meer vrijheid biedt met betrekking tot de totale capaciteit, is Azure Blob-opslag. Hoewel één BLOB ook is beperkt tot 1 TB, is de totale capaciteit van één BLOB-container momenteel 500 TB. Daarnaast biedt klanten de keuze om dit te selecteren &quot;cool&quot; Blob-opslag, die een kosten voordelen heeft. Zie [Azure Blob Storage: warme en cool Storage-lagen](../../../storage/blobs/storage-blob-storage-tiers.md) voor meer informatie over cool Blob Storage.

Gebruik voor extra veiligheid een geografisch gerepliceerd opslag account om de SAP HANA back-ups op te slaan. Zie [Azure storage replicatie](../../../storage/common/storage-redundancy.md) voor meer informatie over replicatie van het opslag account.

Eén kan specifieke Vhd's voor SAP HANA back-ups worden geplaatst in een toegewezen back-upopslag account dat geo-repliceerbaar is. Het is ook mogelijk dat de Vhd's die de SAP HANA back-ups naar een geografisch gerepliceerd opslag account bewaren of naar een opslag account in een andere regio worden gekopieerd.

## <a name="azure-backup-agent"></a>Azure backup-agent

Azure Backup biedt de mogelijkheid om niet alleen back-ups te maken van volledige Vm's, maar ook bestanden en mappen via de back-upagent, die moet worden geïnstalleerd op het gast besturingssysteem. Deze agent wordt echter alleen ondersteund in Windows (Zie [een back-up maken van een Windows-Server of-client naar Azure met behulp van het Resource Manager-implementatie model](../../../backup/backup-configure-vault.md)).

Een tijdelijke oplossing is om eerst SAP HANA back-upbestanden naar een Windows-VM in azure te kopiëren (bijvoorbeeld via SAMBA share) en vervolgens de Azure backup-agent van daaruit te gebruiken. Hoewel het technisch mogelijk is, zou het een complexiteit toevoegen en een back-up-of herstel proces vertragen vanwege het kopiëren tussen Linux en de Windows-VM. Het wordt afgeraden deze benadering te volgen.

## <a name="azure-blobxfer-utility-details"></a>Details van het hulp programma Azure blobxfer

Om directory's en bestanden op te slaan in azure Storage, kan de ene CLI of Power shell gebruiken of een hulp programma ontwikkelen met behulp van een van de [Azure sdk's](https://azure.microsoft.com/downloads/). Er is ook een gebruiks klare hulp programma, AzCopy, voor het kopiëren van gegevens naar Azure Storage. (Zie [gegevens overdragen met het opdracht regel programma AzCopy](../../../storage/common/storage-use-azcopy.md)).

Daarom is blobxfer gebruikt voor het kopiëren van SAP HANA back-upbestanden. Het is open source en wordt gebruikt door veel klanten in productie omgevingen en is beschikbaar op [github](https://github.com/Azure/blobxfer). Met dit hulp programma kunt u gegevens rechtstreeks naar Azure Blob-opslag of een Azure-bestands share kopiëren. Het biedt ook een aantal nuttige functies, zoals MD5-hash of automatische parallellisme bij het kopiëren van een map met meerdere bestanden.

## <a name="sap-hana-backup-performance"></a>Back-upprestaties van SAP HANA

![Deze scherm afbeelding is van de SAP HANA back-upconsole in SAP HANA Studio](media/sap-hana-backup-file-level/image023.png)

Deze scherm afbeelding is van de SAP HANA back-upconsole in SAP HANA Studio. Het duurde ongeveer 42 minuten om de back-up te maken van de 230 GB op een enkele Azure Standard-opslag schijf die is gekoppeld aan de HANA-VM met behulp van XFS-bestands systeem.

![Deze scherm afbeelding is van YaST op de SAP HANA test-VM](media/sap-hana-backup-file-level/image024.png)

Deze scherm afbeelding is van YaST op de SAP HANA test-VM. De ene schijf van 1 TB kan worden weer geven voor SAP HANA back-up zoals eerder vermeld. Het duurde ongeveer 42 minuten om een back-up te maken van 230 GB. Daarnaast werden er 5 200 GB-schijven aangesloten en de software-RAID-md0 gemaakt, met Striping boven op deze vijf Azure-gegevens schijven.

![Dezelfde back-up op de software-RAID herhalen met Striping over vijf gekoppelde Azure Standard-opslag gegevens schijven](media/sap-hana-backup-file-level/image025.png)

Herhaal dezelfde back-up op software-RAID met Striping over vijf gekoppelde Azure Standard-opslag gegevens schijven de back-uptijd van 42 minuten tot 10 minuten. De schijven zijn gekoppeld zonder caching naar de virtuele machine. Het is dus duidelijk hoe belang rijk schrijf doorvoer van de schijf voor de back-uptijd is. Er kan vervolgens worden overgeschakeld naar Azure Premium Storage om het proces voor optimale prestaties te versnellen. In het algemeen moet Azure Premium Storage worden gebruikt voor productie systemen.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>SAP HANA back-upbestanden kopiëren naar Azure Blob-opslag

Een andere mogelijkheid om snel SAP HANA back-upbestanden op te slaan is Azure Blob-opslag. Eén enkele BLOB-container heeft een limiet van 500 TB, voldoende voor sommige kleinere SAP HANA systemen, met behulp van M32ts, M32ls, M64ls en GS5 VM-typen van Azure, om voldoende SAP HANA back-ups te blijven. Klanten hebben de keuze tussen &quot;warme&quot; en &quot;koud&quot; Blob Storage (Zie [Azure Blob Storage: warme en cool Storage-lagen](../../../storage/blobs/storage-blob-storage-tiers.md)).

Met het blobxfer-hulp programma kunt u eenvoudig de SAP HANA back-upbestanden rechtstreeks naar Azure Blob-opslag kopiëren.

![Hier ziet u een overzicht van de bestanden van een volledige back-up van SAP HANA bestand](media/sap-hana-backup-file-level/image026.png)

Hier ziet u een overzicht van de bestanden van een volledige back-up van SAP HANA bestand. Er zijn vier bestanden en de grootste versie is ongeveer 230 GB.

![Het duurde ongeveer 3000 seconden om de 230 GB naar een BLOB-container van het Azure Standard-opslag account te kopiëren](media/sap-hana-backup-file-level/image027.png)

De MD5-hash niet gebruiken in de eerste test, het duurde ongeveer 3000 seconden om de 230 GB te kopiëren naar een BLOB-container van het Azure Standard-opslag account.

![In deze scherm afbeelding ziet u een op de Azure Portal](media/sap-hana-backup-file-level/image028.png)

In deze scherm afbeelding ziet u een op de Azure Portal. Er is een BLOB-container met de naam &quot;SAP-Hana-back-ups&quot; gemaakt en bevat de vier blobs, die de SAP HANA back-upbestanden vertegenwoordigen. Een van deze heeft een grootte van ongeveer 230 GB.

Met de HANA Studio-back-upconsole kan één de maximale bestands grootte van HANA-back-upbestanden beperken. In de voorbeeld omgeving zijn de prestaties verbeterd doordat er meerdere kleinere back-upbestanden kunnen worden gemaakt, in plaats van één groot 230 GB.

![De maximale grootte van het back-upbestand voor de HANA&#39;-kant instellen, de back-uptijd verbeteren](media/sap-hana-backup-file-level/image029.png)

Als u de maximale grootte van het back-upbestand instelt&#39;op de Hana-zijde, wordt de back-uptijd verbeterd, omdat de bestanden opeenvolgend worden geschreven zoals in deze afbeelding wordt weer gegeven. De maximale bestands grootte is ingesteld op 60 GB, waardoor de back-up vier grote gegevens bestanden heeft gemaakt in plaats van het bestand 230-GB. Het gebruik van meerdere back-upbestanden is een nood zaak voor het maken van back-ups van HANA-data bases die gebruikmaken van het geheugen van grotere virtuele Azure-machines, zoals M64s, M64ms, M128s en M128ms.

![Als u de parallelle uitvoering van het blobxfer-hulp programma wilt testen, is de maximale bestands grootte voor HANA-back-ups vervolgens ingesteld op 15 GB](media/sap-hana-backup-file-level/image030.png)

Voor het testen van de parallellisme van het hulp programma blobxfer is de maximale bestands grootte voor HANA-back-ups ingesteld op 15 GB, wat heeft geleid tot 19 back-upbestanden. Met deze configuratie is de tijd ingesteld voor blobxfer om de 230 GB naar Azure Blob-opslag te kopiëren van 3000 seconden naar meer dan 875 seconden.

Dit resultaat wordt veroorzaakt door de limiet van 60 MB per seconde voor het schrijven van een Azure-Blob. Parallelle uitvoering via meerdere blobs lost het knel punt op, maar er is een nadeel: het verhogen van de prestaties van het blobxfer-hulp programma om al deze HANA-back-upbestanden naar Azure Blob-opslag te kopiëren, wordt geladen op de HANA-VM en op het netwerk. De werking van het HANA-systeem wordt beïnvloed.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>BLOB-kopie van toegewezen Azure-gegevens schijven in backup software RAID

In tegens telling tot de hand matige back-up van de VM-gegevens schijf, in deze methode heeft één geen back-up van alle gegevens schijven op een virtuele machine om de volledige SAP-installatie op te slaan, met inbegrip van HANA-gegevens, HANA-logboek bestanden en configuratie bestanden. In plaats daarvan is het verstandig specifieke software-RAID te hebben met Striping over meerdere Azure-gegevens-Vhd's voor het opslaan van een volledige back-up van SAP HANA-bestand. Eén kopieert alleen deze schijven, die de SAP HANA back-up hebben. Ze kunnen eenvoudig worden bewaard in een toegewezen HANA-back-upopslag account of zijn gekoppeld aan een toegewezen &quot;back-upbeheer van de virtuele machine&quot; voor verdere verwerking.

![Alle betrokken Vhd's zijn gekopieerd met de Power shell-opdracht * * start-azurestorageblobcopy * *](media/sap-hana-backup-file-level/image031.png)

Nadat de back-up naar de lokale software-RAID is voltooid, zijn alle virtuele harde schijven gekopieerd met behulp van de **Start-azurestorageblobcopy** Power shell-opdracht (Zie [Start-azurestorageblobcopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Aangezien dit alleen van invloed is op het speciale bestands systeem voor het bewaren van de back-upbestanden, zijn er geen problemen met de consistentie van SAP HANA gegevens of logboek bestanden op de schijf. Een voor deel van deze opdracht is dat deze werkt terwijl de virtuele machine online blijft. Als u er zeker van wilt zijn dat er geen proces wordt geschreven naar de back-Stripe-set, moet u deze ontkoppelen vóór de BLOB-kopie en daarna opnieuw koppelen. Het is ook mogelijk dat een geschikte manier wordt gebruikt om&quot; het bestands systeem te &quot;blok keren. Bijvoorbeeld via xfs\_bevriezen voor het bestands systeem XFS.

![Deze scherm afbeelding toont de lijst met blobs in de vhd's container op de Azure Portal](media/sap-hana-backup-file-level/image032.png)

Deze scherm afbeelding toont de lijst met blobs in de &quot;vhd's&quot; container op de Azure Portal. In de scherm afbeelding ziet u de vijf Vhd's die zijn gekoppeld aan de SAP HANA Server-VM die als de software-RAID moeten worden gebruikt om SAP HANA back-upbestanden te bewaren. Ook worden de vijf exemplaren weer gegeven die zijn gemaakt via de opdracht voor het kopiëren van de blob.

![Voor test doeleinden zijn de kopieën van de SAP HANA backup software RAID-schijven gekoppeld aan de app Server-VM](media/sap-hana-backup-file-level/image033.png)

Voor test doeleinden zijn de kopieën van de SAP HANA backup software RAID-schijven gekoppeld aan de app Server-VM.

![De app Server-VM is afgesloten om de schijf kopieën te koppelen](media/sap-hana-backup-file-level/image034.png)

De app Server-VM is afgesloten om de schijf kopieën te koppelen. Nadat de VM is gestart, zijn de schijven en de RAID correct gedetecteerd (gekoppeld via UUID). Alleen het koppel punt ontbreekt, dat is gemaakt via de partitie-YaST. Daarna werden de kopieën van het SAP HANA-back-upbestand zichtbaar op het niveau van het besturings systeem.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>SAP HANA back-upbestanden kopiëren naar NFS-share

Om de potentiële impact op het SAP HANA systeem van een oogpunt van prestaties of schijf ruimte te beperken, kunt u overwegen de SAP HANA back-upbestanden op te slaan op een NFS-share. Technisch het werkt, maar betekent dat een tweede virtuele machine van Azure wordt gebruikt als host van de NFS-share. Het mag geen kleine VM-grootte zijn, vanwege de band breedte van het VM-netwerk. Het is dan ook belang rijk om deze &quot;back-up van de VM af te sluiten&quot; en alleen de back-up van de SAP HANA uit te voeren. Wanneer u een NFS-share schrijft, wordt de belasting op het netwerk beïnvloed en heeft dit invloed op het SAP HANA systeem, maar het beheer van de back-upbestanden achteraf op de &quot;back-up-VM&quot; geen invloed op het SAP HANA systeem.

![Een NFS-share van een andere Azure-VM is gekoppeld aan de SAP HANA Server-VM](media/sap-hana-backup-file-level/image035.png)

Als u de NFS-use-case wilt controleren, is een NFS-share van een andere Azure-VM gekoppeld aan de SAP HANA Server-VM. Er is geen speciale NFS-afstemming toegepast.

![Het duurde 1 uur en 46 minuten om de back-up rechtstreeks uit te voeren](media/sap-hana-backup-file-level/image036.png)

De NFS-share was een snelle Stripe-set, zoals de verzameling op de SAP HANA-server. Het duurt echter 1 uur en 46 minuten om de back-up rechtstreeks uit te voeren op de NFS-share in plaats van tien minuten, wanneer u naar een lokale Stripe-set schrijft.

![Het alternatief was niet veel sneller om 1 uur en 43 minuten](media/sap-hana-backup-file-level/image037.png)

Het alternatief voor het uitvoeren van een back-up naar een lokale Stripe-set en het kopiëren naar de NFS-share op het besturingssysteem niveau (een eenvoudige **CP-AVR** opdracht) is veel sneller. Het duurde 1 uur en 43 minuten.

Het werkt daarom, maar de prestaties zijn niet geschikt voor de back-uptest van 230 GB. Het zou er nog erger van zijn voor multi terabytes.

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>SAP HANA back-upbestanden kopiëren naar Azure Files

Het is mogelijk een Azure Files share te koppelen aan een virtuele machine in azure Linux. In het artikel [Azure File Storage gebruiken met Linux](../../../storage/files/storage-how-to-use-files-linux.md) vindt u meer informatie over hoe u dit doet. Houd er wel voor dat er momenteel een quotum limiet van 5 TB van één Azure-bestands share is en een bestands grootte limiet van 1 TB per bestand. Zie [Azure files schaal baarheid en prestatie doelen](../../../storage/files/storage-files-scale-targets.md)voor meer informatie.

Er zijn echter tests weer gegeven die SAP HANA back-up&#39;dit nu rechtstreeks met dit soort CIFS-koppeling werkt. Het wordt ook vermeld in [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529) dat CIFS niet wordt aanbevolen.

![In deze afbeelding ziet u een fout in het dialoog venster back-up in SAP HANA Studio](media/sap-hana-backup-file-level/image038.png)

In deze afbeelding ziet u een fout in het dialoog venster back-up in SAP HANA Studio, wanneer u rechtstreeks een back-up probeert te maken naar een op CIFS gekoppelde Azure-bestands share. Daarom moet u eerst een standaard SAP HANA back-up in een VM-bestands systeem maken en vervolgens de back-upbestanden van daaruit naar Azure file service kopiëren.

![In deze afbeelding ziet u dat het ongeveer 929 seconden duurde om 19 SAP HANA back-upbestanden te kopiëren](media/sap-hana-backup-file-level/image039.png)

In deze afbeelding ziet u dat het ongeveer 929 seconden duurde om 19 SAP HANA back-upbestanden te kopiëren met een totale grootte van ongeveer 230 GB naar de Azure-bestands share.

![De bron directory structuur op de SAP HANA virtuele machine is gekopieerd naar de Azure-bestands share](media/sap-hana-backup-file-level/image040.png)

In deze scherm afbeelding ziet u dat de bron directory structuur op de SAP HANA virtuele machine is gekopieerd naar de Azure-bestands share: One Directory (Hana\_backup\_fsl\_15gb) en 19 afzonderlijke back-upbestanden.

Het opslaan van SAP HANA back-upbestanden in azure files kan in de toekomst een interessante optie zijn wanneer de back-ups van SAP HANA bestand direct worden ondersteund. Of wanneer het mogelijk is om Azure files te koppelen via NFS en de maximum quotum limiet aanzienlijk hoger is dan 5 TB.

## <a name="next-steps"></a>Volgende stappen
* In de [back-upgids voor SAP Hana op Azure virtual machines](sap-hana-backup-guide.md) vindt u een overzicht en informatie over aan de slag.
* Met [SAP Hana back-up op basis van opslag momentopnamen](sap-hana-backup-storage-snapshots.md) wordt de back-upoptie gebaseerd op opslag momentopnamen beschreven.
* Zie [SAP Hana (grote instanties) hoge Beschik baarheid en herstel na nood gevallen op Azure](hana-overview-high-availability-disaster-recovery.md)voor meer informatie over het tot stand brengen van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure (grote exemplaren).
