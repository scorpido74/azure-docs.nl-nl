---
title: SAP HANA Azure-opslagconfiguraties voor virtuele machines | Microsoft Documenten
description: Opslagaanbevelingen voor VM waarop SAP HANA is geïmplementeerd.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b469c098db4f8d90147b491bcb54bd55d326b03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080305"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configuraties van SAP HANA in virtuele Azure-machineopslag

Azure biedt verschillende soorten opslag die geschikt zijn voor Azure VM's waarop SAP HANA wordt uitgevoerd. De **SAP HANA-gecertificeerde Azure-opslagtypen** die in aanmerking kunnen komen voor SAP HANA-implementaties, zijn als: 

- Azure Premium SSD  
- [Ultraschijven](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Zie het artikel [Een schijftype selecteren](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types) voor meer informatie over deze schijftypen

Azure biedt twee implementatiemethoden voor VHD's op Azure Standard en Premium Storage. Als het algemene scenario dit toelaat, profiteert u van [door Azure beheerde](https://azure.microsoft.com/services/managed-disks/) schijfimplementaties. 

Voor een lijst met opslagtypen en hun SLA's in IOPS en opslagdoorvoer controleert u de [Azure-documentatie voor beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Onafhankelijk van het gekozen Azure-opslagtype moet het bestandssysteem dat op die opslag wordt gebruikt, worden ondersteund door SAP voor het specifieke besturingssysteem en DBMS. [SAP-ondersteuningsnotitie #405827](https://launchpad.support.sap.com/#/notes/405827) de ondersteunde bestandssystemen voor verschillende besturingssystemen en databases, waaronder SAP HANA, weergeeft. Dit geldt voor alle volumes SAP HANA kan toegang hebben voor lezen en schrijven voor welke taak dan ook. Met name het gebruik van NFS op Azure voor SAP HANA, zijn aanvullende beperkingen van NFS-versies van toepassing zoals later in dit artikel wordt vermeld 


De minimale SAP HANA gecertificeerde voorwaarden voor de verschillende opslagtypen zijn: 

- Azure Premium SSD - /hana/log moet in de cache worden opgeslagen met Azure [Write Accelerator.](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) Het /hana/datavolume kan op Premium SSD worden geplaatst zonder Azure Write Accelerator of op Ultra disk
- Azure Ultra-schijf in ieder geval voor het /hana/log-volume. Het /hana/datavolume kan op Premium SSD worden geplaatst zonder Azure Write Accelerator of om snellere herstarttijden Ultra disk te krijgen
- **NFS v4.1-volumes** bovenop Azure NetApp-bestanden voor /hana/log **en** /hana/data. Het volume /hana/shared kan NFS v3- of NFS v4.1-protocol gebruiken. Het NFS v4.1 protocol is verplicht voor /hana/data en/hana/log volumes.

Sommige van de opslagtypen kunnen worden gecombineerd. Zo is het bijvoorbeeld mogelijk om /hana/data op Premium Storage en /hana/log te plaatsen op Ultra schijfopslag om de vereiste lage latency te krijgen. Als u echter een NFS v4.1-volume op basis van ANF gebruikt voor /hana/gegevens, moet u een ander NFS v4.1-volume op basis van ANF gebruiken voor /hana/log. Nfs gebruiken bovenop ANF voor een van de volumes (zoals /hana/data) en Azure Premium Storage of Ultra disk voor het andere volume (zoals /hana/log) **wordt niet ondersteund.**

In de on-premises wereld, je zelden moest zorgen over de I / O subsystemen en haar mogelijkheden. Reden was dat de leverancier van het apparaat ervoor moest zorgen dat aan de minimale opslagvereisten voor SAP HANA wordt voldaan. Wanneer u de Azure-infrastructuur zelf bouwt, moet u op de hoogte zijn van een aantal van deze door SAP uitgegeven vereisten. Sommige van de minimale doorvoerkenmerken die van SAP vereist zijn, resulteren in de noodzaak om:

- Lees/schrijf inschakelen op **/hana/log** van een 250 MB/sec met 1 MB I/O-formaten
- Leesactiviteit van ten minste 400 MB/s inschakelen voor **/hana/gegevens** voor 16 MB en 64 MB I/O-formaten
- Schrijfactiviteit van ten minste 250 MB/s inschakelen voor **/hana/gegevens** met 16 MB en 64 MB I/O-formaten

Gezien het feit dat lage opslaglatentie van cruciaal belang is voor DBMS-systemen, zelfs als DBMS, zoals SAP HANA, gegevens in het geheugen houdt. Het kritieke pad in opslag is gewoonlijk rond het transactielogboek schrijft van de systemen DBMS. Maar ook bewerkingen zoals het schrijven van savepoints of het laden van gegevens in het geheugen na crash recovery kunnen van cruciaal belang zijn. Daarom is het **verplicht** om Azure Premium Disks te gebruiken voor **/hana/data** en **/hana/log** volumes. Om de minimale doorvoer van **/hana/log** en **/hana/data** te bereiken zoals gewenst door SAP, moet u een RAID 0 bouwen met MDADM of LVM over meerdere Azure Premium Storage-schijven. En gebruik de RAID volumes als **/hana/data** en **/hana/log** volumes. 

**Aanbeveling: Als streep maten voor de RAID 0 de aanbeveling is om te gebruiken:**

- 256 KB voor **/hana/data**
- 32 KB voor **/hana/log**

> [!IMPORTANT]
> De streep grootte voor / hana / gegevens kreeg veranderd van eerdere aanbevelingen waarin wordt opgeroepen tot 64 KB of 128 KB tot 256 KB op basis van klantervaringen met meer recente Linux-versies. De grootte van 256 KB zorgt voor iets betere prestaties

> [!NOTE]
> U hoeft geen redundantieniveau te configureren met RAID-volumes, omdat Azure Premium- en Standard-opslag drie afbeeldingen van een VHD bewaren. Het gebruik van een RAID-volume is puur om volumes te configureren die voldoende I/O-doorvoer bieden.

Het accumuleren van een aantal Azure VHD's onder een RAID, wordt cumulatief van een IOPS- en opslagdoorvoerzijde. Dus als u een RAID 0 meer dan 3 x P30 Azure Premium Storage-schijven plaatst, moet dit u drie keer de IOPS en drie keer de opslagdoorvoer van één Azure Premium Storage P30-schijf geven.

Houd ook de algehele VM I/O-doorvoer in gedachten bij het dimensioneren of beslissen voor een VM. De totale VM-opslagdoorvoer wordt gedocumenteerd in het artikel [Geheugengeoptimaliseerde virtuele machineformaten.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

## <a name="linux-io-scheduler-mode"></a>Linux I/O Scheduler-modus
Linux heeft verschillende I/O-planningsmodi. Algemene aanbeveling via Linux-leveranciers en SAP is het opnieuw configureren van de I/O scheduler-modus voor schijfvolumes van de **cfq-modus** naar de **noop** (niet-multiqueue) of **geen** voor (multiqueue)modus. Details worden verwezen in [SAP Note #1984787](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Oplossingen met Premium Storage en Azure Write Accelerator voor virtuele azure m-serie virtuele machines
Azure Write Accelerator is een functionaliteit die exclusief beschikbaar is voor Azure M-series VM's. Zoals de naam al aangeeft, is het doel van de functionaliteit om de I/O-latentie van schrijfbewerkingen ten opzichte van de Azure Premium Storage te verbeteren. Voor SAP HANA, Write Accelerator wordt verondersteld te worden gebruikt tegen de **/ hana / log** volume alleen. Daarom zijn de **/hana/data** en **/hana/log** afzonderlijke volumes met Azure Write Accelerator die alleen het **/hana/log-volume** ondersteunt. 

> [!IMPORTANT]
> Bij het gebruik van Azure Premium Storage is het gebruik van Azure [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) of het /hana/log-volume verplicht. Write Accelerator is alleen beschikbaar voor premium storage- en M-series en Mv2-serie VM's.

De caching aanbevelingen hieronder zijn uitgaande van de I / O-kenmerken voor SAP HANA die lijst als:

- Er is nauwelijks leeswerk last tegen de HANA databestanden. Uitzonderingen zijn grote I/O's na het opnieuw opstarten van het HANA-exemplaar of wanneer gegevens in HANA worden geladen. Een ander geval van grotere lees-I / O's tegen gegevensbestanden kunnen HANA database back-ups. Als gevolg lees caching meestal geen zin, omdat in de meeste gevallen, alle gegevens bestand volumes moeten volledig worden gelezen.
- Schrijven tegen de databestanden wordt ervaren in uitbarstingen gebaseerd door HANA savepoints en HANA crash recovery. Het schrijven van savepoints is asynchroon en houdt geen gebruikerstransacties tegen. Het schrijven van gegevens tijdens crash recovery is prestatiekritisch om het systeem weer snel te laten reageren. Echter, crash herstel moet nogal uitzonderlijke situaties
- Er zijn nauwelijks reads uit de HANA redo bestanden. Uitzonderingen zijn grote I/O's bij het uitvoeren van transactielogboekback-ups, crashherstel of in de herstartfase van een HANA-instantie.  
- Hoofdbelasting ten opzichte van het SAP HANA-redo-logboekbestand wordt geschreven. Afhankelijk van de aard van de werkbelasting, u I / O zo klein als 4 KB of in andere gevallen I / O maten van 1 MB of meer. Schrijf latentie tegen de SAP HANA redo log is prestatiekritisch.
- Alle schrijft moet worden volgehouden op schijf in een betrouwbare manier

**Aanbeveling: Als gevolg van deze waargenomen I/O-patronen door SAP HANA, moet de caching voor de verschillende volumes met Azure Premium Storage worden ingesteld als:**

- **/hana/data** - geen caching
- **/ hana / log** - geen caching - uitzondering voor M- en Mv2-serie waar Write Accelerator moet worden ingeschakeld zonder te lezen caching. 
- **/hana/shared** - lees caching

### <a name="production-recommended-storage-solution"></a>Productieaanbevolen opslagoplossing

> [!IMPORTANT]
> SAP HANA-certificering voor virtuele azure-machines uit de M-serie is exclusief met Azure Write Accelerator voor het **/hana/log-volume.** Als gevolg hiervan wordt verwacht dat sap HANA-implementaties op virtuele Azure M-series worden geconfigureerd met Azure Write Accelerator voor het **/hana/log-volume.**  

> [!NOTE]
> Controleer voor productiescenario's of een bepaald VM-type wordt ondersteund voor SAP HANA door SAP in de [SAP-documentatie voor IAAS.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)

De aanbevelingen overschrijden vaak de SAP-minimumvereisten zoals eerder in dit artikel. De vermelde aanbevelingen zijn een compromis tussen de grootteaanbevelingen van SAP en de maximale opslagdoorvoer die de verschillende VM-typen bieden.

**Aanbeveling: De aanbevolen configuraties voor productiescenario's zien eruit als:**

| VM-SKU | RAM | Met maximaal VM I/O<br /> Doorvoer | /hana/data | /hana/log | /hana/gedeeld | /wortelvolume | /usr/sap | hana/back-up |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64's | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128's | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Controleer of de opslagdoorvoer voor de verschillende voorgestelde volumes voldoet aan de werkbelasting die u wilt uitvoeren. Als de werkbelasting hogere volumes vereist voor **/hana/data** en **/hana/log,** moet u het aantal Azure Premium Storage VHD's verhogen. Als u een volume met meer VHD's dan vermeld, wordt de IOPS- en I/O-doorvoer binnen de grenzen van het type Azure-virtuele machine verhoogd.

Azure Write Accelerator werkt alleen in combinatie met [door Azure beheerde schijven.](https://azure.microsoft.com/services/managed-disks/) Dus ten minste de Azure Premium Storage schijven die de **/ hana / log** volume moeten worden ingezet als beheerde schijven.

Er zijn limieten voor Azure Premium Storage VHD's per VM die kunnen worden ondersteund door Azure Write Accelerator. De huidige limieten zijn:

- 16 VHD's voor een M128xx en M416xx VM
- 8 VHD's voor een M64xx en M208xx VM
- 4 VHD's voor een M32xx VM

Meer gedetailleerde instructies over het inschakelen van Azure Write Accelerator vindt u in het artikel [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Details en beperkingen voor Azure Write Accelerator zijn te vinden in dezelfde documentatie.

**Aanbeveling: U moet Write Accelerator gebruiken voor schijven die het /hana/log-volume vormen**


### <a name="cost-conscious-azure-storage-configuration"></a>Kostenbewuste Azure Storage-configuratie
In de volgende tabel ziet u een configuratie van VM-typen die klanten ook gebruiken om SAP HANA op Azure VM's te hosten. Er zijn mogelijk enkele VM-typen die mogelijk niet voldoen aan alle minimale opslagcriteria voor SAP HANA of die niet officieel worden ondersteund met SAP HANA door SAP. Maar tot nu toe die VM's leek uit te voeren prima voor niet-productie scenario's. De **/hana/data** en **/hana/log** worden gecombineerd tot één volume. Als gevolg hiervan kan het gebruik van Azure Write Accelerator een beperking worden in termen van IOPS.

> [!NOTE]
> Controleer voor sap-ondersteunde scenario's of een bepaald VM-type wordt ondersteund voor SAP HANA door SAP in de [SAP-documentatie voor IAAS.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)

> [!NOTE]
> Een verandering van eerdere aanbevelingen voor een kostenbewuste oplossing, is om over te stappen van [Azure Standard HDD-schijven](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) naar beter presterende en [betrouwbaardere Azure Standard SSD-schijven](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)

De aanbevelingen overschrijden vaak de SAP-minimumvereisten zoals eerder in dit artikel. De vermelde aanbevelingen zijn een compromis tussen de grootteaanbevelingen van SAP en de maximale opslagdoorvoer die de verschillende VM-typen bieden.

| VM-SKU | RAM | Met maximaal VM I/O<br /> Doorvoer | /hana/data en /hana/log<br /> gestreept met LVM of MDADM | /hana/gedeeld | /wortelvolume | /usr/sap | hana/back-up |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1.200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2.000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1.000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64's | 1.000 GiB | 1.000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1.750 GiB | 1.000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128's | 2.000 GiB | 2.000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3.800 GiB | 2.000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2.000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


De schijven aanbevolen voor de kleinere VM types met 3 x P20 oversized de volumes met betrekking tot de ruimte aanbevelingen volgens de [SAP TDI Storage Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). De keuze zoals weergegeven in de tabel werd echter gemaakt om voldoende schijfdoorvoer te bieden voor SAP HANA. Als u wijzigingen in het **/hana/back-upvolume** nodig hebt, dat is aangepast voor het bewaren van back-ups die twee keer het geheugenvolume vertegenwoordigen, u zich gerust aanpassen.   
Controleer of de opslagdoorvoer voor de verschillende voorgestelde volumes voldoet aan de werkbelasting die u wilt uitvoeren. Als de werkbelasting hogere volumes vereist voor **/hana/data** en **/hana/log,** moet u het aantal Azure Premium Storage VHD's verhogen. Als u een volume met meer VHD's dan vermeld, wordt de IOPS- en I/O-doorvoer binnen de grenzen van het type Azure-virtuele machine verhoogd. 

> [!NOTE]
> De bovenstaande configuraties zouden NIET profiteren van [Azure virtual machine single VM SLA,](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) omdat het een mengsel van Azure Premium Storage en Azure Standard Storage gebruikt. De selectie is echter gekozen om de kosten te optimaliseren. U moet Premium Storage kiezen voor alle bovenstaande schijven die worden vermeld als Azure Standard Storage (Sxx) om de VM-configuratie compatibel te maken met de Azure Single VM SLA.


> [!NOTE]
> De aanbevelingen voor schijfconfiguratie zijn gericht op minimumvereisten die SAP uitdrukt ten opzichte van hun infrastructuurproviders. In echte klantimplementaties en werkbelastingscenario's werden situaties aangetroffen waarin deze aanbevelingen nog steeds niet voldoende mogelijkheden bieden. Dit kunnen situaties zijn waarin een klant de gegevens sneller moest herladen na een herstart van de HANA of waar back-upconfiguraties een hogere bandbreedte voor de opslag vereisen. Andere gevallen opgenomen **/hana/log** waarbij 5000 IOPS niet voldoende waren voor de specifieke werkbelasting. Neem deze aanbevelingen dus als uitgangspunt en pas je aan op basis van de vereisten van de werklast.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Azure Ultra-schijfopslagconfiguratie voor SAP HANA
Microsoft is bezig met de uitrol van een nieuw Azure-opslagtype genaamd [Azure Ultra-schijf.](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) Het belangrijke verschil tussen Azure-opslag tot nu toe en Ultra-schijf is dat de schijfmogelijkheden niet meer gebonden zijn aan de schijfgrootte. Als klant u deze mogelijkheden definiëren voor Ultra disk:

- Grootte van een schijf variërend van 4 GiB tot 65.536 GiB
- IOPS variëren van 100 IOPS tot 160K IOPS (maximaal afhankelijk van VM-typen)
- Opslagdoorvoer van 300 MB/s tot 2.000 MB/s

Ultra disk geeft u de mogelijkheid om een enkele schijf te definiëren die voldoet aan uw grootte, IOPS en schijfdoorvoerbereik. In plaats van logische volumemanagers zoals LVM of MDADM bovenop Azure Premium Storage te gebruiken om volumes te construeren die voldoen aan de IOPS- en opslagdoorvoervereisten. U een configuratiemix uitvoeren tussen Ultra-schijf en Premium-opslag. Als gevolg hiervan u het gebruik van Ultra-schijf beperken tot de prestatiekritieke /hana/gegevens en /hana/logvolumes en de andere volumes dekken met Azure Premium Storage

Andere voordelen van Ultra disk kan de beter leeslatentie in vergelijking met Premium Storage. De snellere leeslatentie kan voordelen hebben wanneer u de HANA-opstarttijden en de daaropvolgende belasting van de gegevens in het geheugen wilt verminderen. Voordelen van Ultra schijfopslag zijn ook voelbaar wanneer HANA savepoints schrijft. Aangezien Premium Storage-schijven voor /hana/data meestal niet write accelerator in de cache zijn opgeslagen, is de schrijflatentie naar /hana/data op Premium Storage in vergelijking met de Ultra-schijf hoger. Het is te verwachten dat savepoint schrijven met Ultra schijf beter presteert op Ultra schijf.

> [!IMPORTANT]
> Ultra disk is nog niet aanwezig in alle Azure-regio's en ondersteunt ook nog niet alle onderstaande VM-typen. Voor gedetailleerde informatie over waar Ultra-schijf beschikbaar is en welke VM-families worden ondersteund, raadpleegt u het artikel [Welke schijftypen beschikbaar zijn in Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Productieaanbevolen opslagoplossing met pure Ultra-schijfconfiguratie
In deze configuratie bewaar je de /hana/data en /hana/log volumes apart. De voorgestelde waarden zijn afgeleid van de KPI's die SAP heeft om VM-typen te certificeren voor SAP HANA en opslagconfiguraties zoals aanbevolen in de [SAP TDI Storage Whitepaper.](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)

De aanbevelingen overschrijden vaak de SAP-minimumvereisten zoals eerder in dit artikel. De vermelde aanbevelingen zijn een compromis tussen de grootteaanbevelingen van SAP en de maximale opslagdoorvoer die de verschillende VM-typen bieden.

| VM-SKU | RAM | Met maximaal VM I/O<br /> Doorvoer | /hana/datavolume | /hana/data I/O doorvoer | /hana/data IOPS | /hana/log volume | /hana/log I/O-doorvoer | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1.200 MB/s | 600 GB | 700 MBps | 7.500 | 512 GB | 500 MBps  | 2.000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBps | 7.500 | 256 GB | 250 MBps  | 2.000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 7.500 | 256 GB | 250 MBps  | 2.000 |
| M64ls | 512 GiB | 1.000 MB/s | 600 GB | 600 MBps | 7.500 | 512 GB | 400 MBps  | 2500 |
| M64's | 1.000 GiB | 1.000 MB/s |  1.200 GB | 600 MBps | 7.500 | 512 GB | 400 MBps  | 2500 |
| M64ms | 1.750 GiB | 1.000 MB/s | 2.100 GB | 600 MBps | 7.500 | 512 GB | 400 MBps  | 2500 |
| M128's | 2.000 GiB | 2.000 MB/s |2.400 GB | 1.200 MBps |9000 | 512 GB | 800 MBps  | 3000 | 
| M128ms | 3.800 GiB | 2.000 MB/s | 4.800 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 3000 | 
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 3.500 GB | 1.000 MBps | 9000 | 512 GB | 400 MBps  | 2500 | 
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 7.200 GB | 1.000 MBps | 9000 | 512 GB | 400 MBps  | 2500 | 
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 7.200 GB | 1.500 MBps | 9000 | 512 GB | 800 MBps  | 3000 | 
| M416ms_v2 | 11.400 GiB | 2.000 MB/s | 14.400 GB | 1.500 MBps | 9000 | 512 GB | 800 MBps  | 3000 |   

De genoemde waarden zijn bedoeld als uitgangspunt en moeten worden beoordeeld aan de hand van de werkelijke eisen. Het voordeel van Azure Ultra disk is dat de waarden voor IOPS en doorvoer kunnen worden aangepast zonder de noodzaak om de VM af te sluiten of de werkbelasting die op het systeem wordt toegepast te stoppen.   

> [!NOTE]
> Tot nu toe zijn opslagmomentopnamen met Ultra-schijfopslag niet beschikbaar. Dit blokkeert het gebruik van VM-momentopnamen met Azure Backup Services

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>Kostenbewuste opslagoplossing met pure Ultra-schijfconfiguratie
In deze configuratie, u de / hana / gegevens en / hana / log volumes op dezelfde schijf. De voorgestelde waarden zijn afgeleid van de KPI's die SAP heeft om VM-typen te certificeren voor SAP HANA en opslagconfiguraties zoals aanbevolen in de [SAP TDI Storage Whitepaper.](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 

De aanbevelingen overschrijden vaak de SAP-minimumvereisten zoals eerder in dit artikel. De vermelde aanbevelingen zijn een compromis tussen de grootteaanbevelingen van SAP en de maximale opslagdoorvoer die de verschillende VM-typen bieden.

| VM-SKU | RAM | Met maximaal VM I/O<br /> Doorvoer | Volume voor /hana/data en /log | /hana/data en log I/O-doorvoer | /hana/data en log IOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1.200 MB/s | 1.200 GB | 1.200 MBps | 9,500 | 
| M32ts | 192 GiB | 500 MB/s | 512 GB | 400 MBps | 9,500 | 
| M32ls | 256 GiB | 500 MB/s | 600 GB | 400 MBps | 9,500 | 
| M64ls | 512 GiB | 1.000 MB/s | 1.100 GB | 900 MBps | 10.000 | 
| M64's | 1.000 GiB | 1.000 MB/s |  1.700 GB | 900 MBps | 10.000 | 
| M64ms | 1.750 GiB | 1.000 MB/s | 2.600 GB | 900 MBps | 10.000 | 
| M128's | 2.000 GiB | 2.000 MB/s |2.900 GB | 1.800 MBps |12,000 | 
| M128ms | 3.800 GiB | 2.000 MB/s | 5.300 GB | 1.800 MBps |12,000 |  
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 4.000 GB | 900 MBps | 10.000 |  
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 7.700 GB | 900 MBps | 10.000 | 
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 7.700 GB | 1800 MBps | 12,000 |  
| M416ms_v2 | 11.400 GiB | 2.000 MB/s | 15.000 GB | 1.800 MBps | 12,000 |    

De genoemde waarden zijn bedoeld als uitgangspunt en moeten worden beoordeeld aan de hand van de werkelijke eisen. Het voordeel van Azure Ultra disk is dat de waarden voor IOPS en doorvoer kunnen worden aangepast zonder de noodzaak om de VM af te sluiten of de werkbelasting die op het systeem wordt toegepast te stoppen.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>NFS v4.1-volumes op Azure NetApp-bestanden
Azure NetApp Files biedt native NFS-shares die kunnen worden gebruikt voor /hana/shared, /hana/data en /hana/logvolumes. Het gebruik van ANF-gebaseerde NFS-aandelen voor de /hana/data- en /hana/logvolumes vereist het gebruik van het v4.1 NFS-protocol. Het NFS-protocol v3 wordt niet ondersteund voor het gebruik van /hana/data en /hana/log volumes bij het baseren van de aandelen op ANF. 

> [!IMPORTANT]
> Het NFS v3-protocol dat is geïmplementeerd op Azure NetApp Files wordt **niet** ondersteund voor /hana/data en /hana/log. Het gebruik van de NFS 4.1 is vanuit functioneel oogpunt verplicht voor /hana/data en /hana/log volumes. Terwijl voor het /hana/gedeelde volume de NFS v3 of het NFS v4.1 protocol functioneel gebruikt kan worden.

### <a name="important-considerations"></a>Belangrijke overwegingen
Wanneer u Azure NetApp-bestanden voor de SAP Netweaver en SAP HANA overweegt, moet u rekening houden met de volgende belangrijke overwegingen:

- De minimale capaciteit pool is 4 TiB.  
- De minimale volumegrootte is 100 GiB
- Azure NetApp-bestanden en alle virtuele machines, waar Azure NetApp-bestandenvolumes worden gemonteerd, moeten zich in hetzelfde Azure Virtual Network bevinden of in [peered virtuele netwerken](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) in dezelfde regio.  
- Het geselecteerde virtuele netwerk moet een subnet hebben dat is gedelegeerd aan Azure NetApp-bestanden.
- De doorvoer van een Azure NetApp-volume is een functie van het volumequotum en serviceniveau, zoals gedocumenteerd in [Serviceniveau voor Azure NetApp-bestanden.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) Controleer bij het dimensioneren van de HANA Azure NetApp-volumes of de resulterende doorvoer voldoet aan de HANA-systeemvereisten.  
- Azure NetApp Files biedt [exportbeleid:](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)u de toegestane clients, het toegangstype (Lees&schrijven, alleen lezen, enz.) beheren. 
- De functie Azure NetApp-bestanden is nog niet zonebewust. Momenteel wordt de functie Azure NetApp-bestanden niet geïmplementeerd in alle beschikbaarheidszones in een Azure-gebied. Wees je bewust van de mogelijke latentie-implicaties in sommige Azure-regio's.  
- Het is belangrijk om de virtuele machines in de nabijheid van de Azure NetApp-opslag te hebben voor lage latentie. 
- De gebruikersnaam voor <b>sid</b>adm en `sapsys` de groeps-id voor op de virtuele machines moeten overeenkomen met de configuratie in Azure NetApp-bestanden. 

> [!IMPORTANT]
> Voor SAP HANA-workloads is lage latentie van cruciaal belang. Werk samen met uw Microsoft-vertegenwoordiger om ervoor te zorgen dat de virtuele machines en de Azure NetApp-bestanden in de nabijheid worden geïmplementeerd.  

> [!IMPORTANT]
> Als er een mismatch is <b>sid</b>tussen gebruikers-id `sapsys` voor sid adm en de groeps-id voor tussen de virtuele machine en de Azure NetApp-configuratie, worden de machtigingen voor bestanden op Azure NetApp-volumes, gemonteerd op virtuele machines, weergegeven als `nobody`. Zorg ervoor dat u de <b>sid</b>juiste gebruikersnaam voor `sapsys`sid adm en de groeps-id opgeeft voor , wanneer [u een nieuw systeem instapt naar](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) Azure NetApp-bestanden.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Grootte voor HANA-database op Azure NetApp-bestanden

De doorvoer van een Azure NetApp-volume is een functie van de volumegrootte en het serviceniveau, zoals gedocumenteerd in [Serviceniveau voor Azure NetApp-bestanden.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) 

Bij het ontwerpen van de infrastructuur voor SAP in Azure moet u zich bewust zijn van een aantal minimale vereisten voor opslagdoorvoer door SAP, wat zich vertaalt in minimale doorvoerkenmerken van:

- Lees/schrijf inschakelen op /hana/log van een 250 MB/sec met 1 MB I/O-formaten  
- Leesactiviteit van ten minste 400 MB/s inschakelen voor /hana/gegevens voor 16 MB en 64 MB I/O-formaten  
- Schrijfactiviteit van ten minste 250 MB/s inschakelen voor /hana/gegevens met 16 MB en 64 MB I/O-formaten  

De [doorvoerlimieten voor Azure NetApp-bestanden](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) per volumequotum van 1 TiB zijn:
- Premium Storage Tier - 64 MiB/s  
- Ultra Storage Tier - 128 MiB/s  

> [!IMPORTANT]
> Onafhankelijk van de capaciteit die u implementeert op een enkel NFS-volume, de doorvoer, zal naar verwachting plateau in het bereik van 1,2-1,4 GB/sec bandbreedte leveraged door een consument in een virtuele machine. Dit heeft te maken met de onderliggende architectuur van het ANF-aanbod en gerelateerde Linux-sessielimieten rond NFS. De prestatie- en doorvoernummers zoals gedocumenteerd in het artikel [Prestatiebenchmarktestresultaten voor Azure NetApp-bestanden](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-benchmarks) werden uitgevoerd tegen één gedeeld NFS-volume met meerdere client-VM's en als gevolg daarvan met meerdere sessies. Dat scenario is anders dan het scenario dat we in SAP meten. Waar we de doorvoer van één VM meten ten opzichte van een NFS-volume. gehost op ANF.

Om te voldoen aan de SAP minimale doorvoervereisten voor `/hana/shared`gegevens en logboek, en volgens de richtlijnen voor , zouden de aanbevolen maten eruit zien als:

| Volume | Grootte<br /> Premium opslaglaag | Grootte<br /> Ultraopslaglaag | Ondersteund NFS-protocol |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/gedeeld | Max(512 GB, 1xRAM) per 4 werknemersknooppunten | Max(512 GB, 1xRAM) per 4 werknemersknooppunten | v3 of v4.1 |


> [!NOTE]
> De aanbevelingen voor het dimensioneren van Azure NetApp Files die hier worden vermeld, zijn gericht op de minimale vereisten die SAP ten opzichte van hun infrastructuurproviders uitdrukt. In echte klantimplementaties en werkbelastingscenario's is dat misschien niet genoeg. Gebruik deze aanbevelingen als uitgangspunt en pas deze aan op basis van de vereisten van uw specifieke werkbelasting.  

Daarom u overwegen om vergelijkbare doorvoer voor de ANF-volumes te implementeren die al worden vermeld voor Ultra-schijfopslag. Houd ook rekening met de maten voor de maten die worden vermeld voor de volumes voor de verschillende VM SKU's, zoals al in de Ultra-schijftabellen.

> [!TIP]
> U de volumes van Azure NetApp-bestanden `unmount` dynamisch opnieuw vergroten, zonder dat de volumes nodig zijn, de virtuele machines stoppen of SAP HANA stoppen. Dat maakt flexibiliteit mogelijk om aan uw toepassing te voldoen, zowel de verwachte als onvoorziene doorvoereisen.

Documentatie over het implementeren van een SAP HANA scale-out configuratie met stand-by node met NFS v4.1 volumes die worden gehost in ANF wordt gepubliceerd in [SAP HANA scale-out met stand-by node op Azure VM's met Azure NetApp Files op SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse).


## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie:

- [SAP HANA-handleiding voor hoge beschikbaarheid voor virtuele Azure-machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
