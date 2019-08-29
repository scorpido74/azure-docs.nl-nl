---
title: Opslag configuraties voor virtuele Azure-machines SAP HANA | Microsoft Docs
description: Opslag aanbevelingen voor de virtuele machine waarop SAP HANA geïmplementeerd.
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
ms.date: 08/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 45d8844a34c5b7d9f36099304c1619e09d39305c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099632"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configuraties van SAP HANA in virtuele Azure-machineopslag

Azure biedt verschillende soorten opslag die geschikt zijn voor Azure-Vm's met SAP HANA. De Azure Storage-typen die kunnen worden overwogen voor SAP HANA implementaties lijst zoals: 

- Standard-SSD schijf stations (SSD)
- Premium Solid State-schijven (SSD)
- [Ultra Disk](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disks-enable-ultra-ssd) 

Zie het artikel [een schijf type selecteren](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types) voor meer informatie over deze schijf typen.

Azure biedt twee implementatie methoden voor Vhd's op Azure Standard en Premium Storage. Als het algemene scenario toestaat, Profiteer dan van [Azure Managed Disk](https://azure.microsoft.com/services/managed-disks/) -implementaties. 

Raadpleeg de [Azure-documentatie voor beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks/)voor een lijst met opslag typen en de bijbehorende SLA'S in IOPS en opslag doorvoer.

**Advies Azure Premium Storage gebruiken in combi natie met Azure Write Accelerator en Azure Managed Disks gebruiken voor implementatie**

In de on-premise wereld moest u zelden de I/O-subsystemen en de mogelijkheden ervan opdoen. Daarom moest de leverancier van het apparaat controleren of aan de minimale opslag vereisten wordt voldaan voor de SAP HANA. Wanneer u zelf de Azure-infra structuur bouwt, moet u rekening houden met een aantal van deze vereisten. Enkele van de minimale doorvoer kenmerken die worden gevraagd, zijn de volgende vereisten:

- Lezen/schrijven inschakelen op **/Hana/log** van een 250 MB/sec. met 1 MB I/O-grootten
- Lees activiteit van ten minste 400 MB/sec. voor **/Hana/data** met 16 mb en 64 MB I/O-grootte inschakelen
- Schrijf activiteit van ten minste 250 MB per seconde voor **/Hana/data** met 16 mb en 64 MB I/O-grootte inschakelen

Gezien de minimale opslag latentie is essentieel voor de DBMS-systemen, zelfs als DBMS, zoals SAP HANA, behoud gegevens in het geheugen. Het kritieke pad in de opslag ruimte is doorgaans rond de schrijf bewerkingen van het transactie logboek van de DBMS-systemen. Maar ook bewerkingen zoals het schrijven van opslag punten of het laden van gegevens in het geheugen nadat het herstel is gelukt, kunnen kritiek zijn. Daarom is het **verplicht** om gebruik te maken van Azure Premium-schijven voor **/Hana/data** -en **/Hana/log** -volumes. Om de minimale door Voer van **/Hana/log** en **/Hana/data** te bereiken, zoals gewenst door SAP, moet u een RAID 0 bouwen met MDADM of LVM via meerdere Azure Premium Storage-schijven. En gebruik de RAID-volumes als **/Hana/data** -en **/Hana/log** -volumes. 

**Advies Als Stripe-groottes voor de RAID 0, moet u het volgende gebruiken:**

- 64 KB of 128 KB voor **/Hana/data**
- 32 KB voor **/Hana/log**

> [!NOTE]
> U hoeft geen redundantie niveau te configureren met behulp van RAID-volumes, aangezien Azure Premium en standaard opslag drie installatie kopieën van een VHD behouden. Het gebruik van een RAID-volume is uitsluitend voor het configureren van volumes die voldoende I/O-door Voer bieden.

Het samen voegen van een aantal Azure-Vhd's onder een RAID, is cumulatief van een IOPS-en opslag doorvoer zijde. Als u dus een RAID 0 van meer dan 3 x P30 Azure Premium Storage-schijven plaatst, moet u het drie maal de IOPS en drie keer de opslag doorvoer van één Azure Premium Storage P30-schijf.

In de onderstaande aanbevelingen voor caching worden de I/O-kenmerken voor SAP HANA die lijst weer gegeven:

- Er is nauwelijks Lees werk belasting voor de HANA-gegevens bestanden. Uitzonde ringen zijn grote I/O's na het opnieuw opstarten van het HANA-exemplaar of wanneer gegevens in HANA worden geladen. Een ander geval van grotere Lees-I/O's tegen gegevens bestanden kan HANA-data base-back-ups zijn. Als gevolg hiervan is het meestal niet zinvol om in de meeste gevallen alle volumes van het gegevens bestand volledig te lezen.
- Het schrijven van de gegevens bestanden wordt uitgevoerd in bursts op basis van HANA opslag punten en HANA-crash herstel. Het schrijven van opslag punten is asynchroon en houdt geen gebruikers transacties in handen. Het schrijven van gegevens tijdens het crash herstel is van essentieel belang om het systeem weer snel te laten reageren. Crash herstel moet echter in plaats daarvan uitzonderlijke situaties
- Er zijn nauwelijks Lees bewerkingen van de HANA-bestanden opnieuw. Uitzonde ringen zijn grote I/O's bij het uitvoeren van back-ups van transactie logboeken, crash herstel of in de fase voor het opnieuw opstarten van een HANA-exemplaar.  
- Hoofd belasting op basis van het SAP HANA opnieuw uitvoeren van het logboek bestand wordt geschreven. Afhankelijk van de aard van de werk belasting, kunt u een I/O's-grootte hebben van Maxi maal 4 KB of in andere I/O-bewerkingen van 1 MB of meer. Schrijf latentie voor het SAP HANA opnieuw uitvoeren van het logboek is essentieel voor de prestaties.
- Alle schrijf bewerkingen moeten op een betrouw bare manier op de schijf worden bewaard

**Advies Als gevolg van deze waargenomen I/O-patronen door SAP HANA, moet de cache voor de verschillende volumes met behulp van Azure Premium Storage als volgt worden ingesteld:**

- **/Hana/data** -geen caching
- **/Hana/log** -geen caching-uitzonde ring voor M-serie (zie verderop in dit document)
- **/Hana/Shared** -cache lezen


Houd ook rekening met de totale I/O-door Voer van de virtuele machine bij het aanpassen van de grootte of het kiezen van een virtuele machine. De totale opslag doorvoer voor de VM wordt beschreven in de grootte van het door het artikel [geoptimaliseerde virtuele machines](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Scheduler-modus Linux I/O
Linux heeft verschillende I/O-plannings modi. Algemene aanbeveling via Linux-leveranciers en SAP is om de I/O scheduler-modus voor schijf volumes van de modus **cfq** opnieuw te configureren in de modus **nooperation** . Er wordt naar Details verwezen in [SAP Note #1984798](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Oplossing voor productie opslag met Azure Write Accelerator voor virtuele machines uit de M-serie van Azure
Azure Write Accelerator is een functionaliteit die uitsluitend wordt geïmplementeerd voor Vm's uit de M-serie van Azure. Als de naam statussen is het doel van de functionaliteit om de I/O-latentie van schrijf bewerkingen te verbeteren voor de Azure-Premium Storage. Voor SAP HANA moet Write Accelerator alleen worden gebruikt voor het **/Hana/log** -volume. Daarom zijn de **/Hana/data** en **/Hana/log** afzonderlijke volumes met Azure write Accelerator alleen het **/Hana/log** -volume ondersteunen. 

> [!IMPORTANT]
> SAP HANA certificering voor virtuele machines uit de M-serie van Azure bestaat uitsluitend met Azure Write Accelerator voor het **/Hana/log** -volume. Als gevolg hiervan wordt het productie scenario SAP HANA implementaties op virtuele machines uit de d-serie van Azure M naar verwachting geconfigureerd met Azure Write Accelerator voor het **/Hana/log** -volume.  

> [!NOTE]
> Controleer voor productie scenario's of een bepaald VM-type wordt ondersteund voor SAP HANA door SAP in de [SAP-documentatie voor IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Advies De aanbevolen configuraties voor productie scenario's zien er als volgt uit:**

| VM-SKU | RAM | Met maximaal VM-I/O<br /> Doorvoer | /hana/data | /hana/log | /hana/shared | /root volume | /usr/sap | Hana/back-up |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

M416xx_v2 VM-typen zijn nog niet voor het publiek beschikbaar gemaakt door micro soft. Controleer of de opslag doorvoer voor de verschillende voorgestelde volumes voldoet aan de werk belasting die u wilt uitvoeren. Als voor de werk belasting hogere volumes zijn vereist voor **/Hana/data** en **/Hana/log**, moet u het aantal Azure Premium Storage vhd's verg Roten. Het formaat van een volume met meer Vhd's dan wordt weer gegeven, verhoogt de IOPS en I/O-door Voer binnen de limieten van het type virtuele machine van Azure.

Azure Write Accelerator werkt alleen in combi natie met [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Ten minste de Azure Premium Storage-schijven die het **/Hana/log** -volume vormen, moeten worden geïmplementeerd als managed disks.

Er zijn limieten van Azure Premium Storage Vhd's per VM die door Azure Write Accelerator kunnen worden ondersteund. De huidige limieten zijn:

- 16 Vhd's voor een M128xx-en M416xx-VM
- 8 Vhd's voor een M64xx-en M208xx-VM
- 4 Vhd's voor een M32xx-VM

Meer gedetailleerde instructies over het inschakelen van Azure Write Accelerator vindt u in het artikel [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Details en beperkingen voor Azure Write Accelerator zijn te vinden in dezelfde documentatie.

**Advies U moet Write Accelerator gebruiken voor schijven die het/Hana/log-volume vormen**


## <a name="cost-conscious-azure-storage-configuration"></a>Configuratie van kosten bewuste Azure Storage
De volgende tabel toont een configuratie van VM-typen die klanten ook gebruiken om SAP HANA te hosten op virtuele machines van Azure. Mogelijk zijn er enkele VM-typen die mogelijk niet voldoen aan alle minimale opslag criteria voor SAP HANA of niet officieel worden ondersteund met SAP HANA door SAP. Maar tot nu toe leek deze Vm's prima voor niet-productie scenario's. De **/Hana/data** en **/Hana/log** worden gecombineerd tot één volume. Als gevolg hiervan kan het gebruik van Azure Write Accelerator een beperking zijn in de termen van IOPS.

> [!NOTE]
> Controleer voor door SAP ondersteunde scenario's of een bepaald VM-type wordt ondersteund voor SAP HANA door SAP in de [SAP-documentatie voor IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Een wijziging ten opzichte van eerdere aanbevelingen voor een kostenbesparende oplossing is om van [azure Standard-HDD schijven](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) te verplaatsen naar betere prestaties en betrouwbaardere [Azure Standard-SSD-schijven](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


| VM-SKU | RAM | Met maximaal VM-I/O<br /> Doorvoer | /Hana/data en/Hana/log<br /> Striped met LVM of MDADM | /hana/shared | /root volume | /usr/sap | Hana/back-up |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


M416xx_v2 VM-typen zijn nog niet voor het publiek beschikbaar gemaakt door micro soft. De schijven die worden aanbevolen voor de kleinere VM-typen met 3 x P20, oversizen de volumes met betrekking tot de ruimte aanbevelingen volgens het [technisch document SAP TDI-opslag](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). De keuze zoals die in de tabel wordt weer gegeven, is echter ingesteld om voldoende schijf doorvoer te bieden voor SAP HANA. Als u wijzigingen wilt aanbrengen aan het **/Hana/backup** -volume dat het formaat heeft gewijzigd voor het bewaren van back-ups die twee maal het geheugen volume vertegenwoordigen, hoeft u niet meer aan te passen.   
Controleer of de opslag doorvoer voor de verschillende voorgestelde volumes voldoet aan de werk belasting die u wilt uitvoeren. Als voor de werk belasting hogere volumes zijn vereist voor **/Hana/data** en **/Hana/log**, moet u het aantal Azure Premium Storage vhd's verg Roten. Het formaat van een volume met meer Vhd's dan wordt weer gegeven, verhoogt de IOPS en I/O-door Voer binnen de limieten van het type virtuele machine van Azure. 

> [!NOTE]
> De configuratie hierboven heeft geen voor deel van de SLA van de [virtuele Azure-machine](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) , omdat deze een combi natie van Azure Premium Storage en Azure Standard-opslag gebruikt. De selectie werd echter gekozen om de kosten te optimaliseren. U moet Premium Storage kiezen voor alle schijven hierboven die worden vermeld als Azure Standard-opslag (Sxx) om ervoor te zorgen dat de VM-configuratie compatibel is met de Azure-SLA voor één VM.


> [!NOTE]
> De opgegeven aanbevelingen voor schijf configuratie zijn gericht op minimale vereisten SAP is gericht op hun infrastructuur providers. In echte klant implementaties en werkbelasting scenario's zijn situaties aangetroffen waar deze aanbevelingen nog niet voldoende mogelijkheden hebben. Dit kunnen situaties zijn waarin een klant een snellere herstart van de gegevens vereist nadat een HANA opnieuw is opgestart of waarbij de back-upconfiguratie een hogere band breedte nodig heeft voor de opslag. Andere gevallen zijn opgenomen in **/Hana/log** waarbij 5000 IOPS niet voldoende was voor de specifieke werk belasting. Doe deze aanbevelingen dus als uitgangs punt en past zich aan op basis van de vereisten van de werk belasting.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Configuratie van de Azure Ultra Disk-opslag voor SAP HANA
Micro soft is bezig met het implementeren van een nieuw Azure-opslag type met de naam [Azure Ultra Disk](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#ultra-disk). Het grote verschil tussen Azure Storage dat tot nu toe wordt aangeboden en ultra disk is dat de schijf mogelijkheden niet meer zijn gebonden aan de schijf grootte. Als klant kunt u deze mogelijkheden voor Ultra Disk definiëren:

- De grootte van een schijf, variërend van 4 GiB tot 65.536 GiB
- IOPS variëren van 100 IOPS tot 160K IOPS (maximum is ook afhankelijk van VM-typen)
- Opslag doorvoer van 300 MB/sec tot 2.000 MB/sec.

Ultra Disk biedt u de mogelijkheid om één schijf te definiëren die voldoet aan uw grootte, IOPS en bereik voor schijf doorvoer. In plaats van logische volume managers zoals LVM of MDADM boven op Azure Premium Storage te gebruiken om volumes te maken die voldoen aan de vereisten voor IOPS en opslag doorvoer. U kunt een configuratie mix uitvoeren tussen ultra disk en Premium Storage. Als gevolg hiervan kunt u het gebruik van ultra Disk beperken tot de essentiële/Hana/data-en/Hana/log-volumes en de andere volumes bedekken met Azure Premium Storage

> [!IMPORTANT]
> Ultra disk is nog niet in alle Azure-regio's aanwezig en biedt nog geen ondersteuning voor alle VM-typen. Raadpleeg het artikel wat zijn de [schijf typen die beschikbaar zijn in azure?](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#ultra-disk)voor meer informatie over hoe Ultra Disk beschikbaar is en welke VM-families worden ondersteund.

| VM-SKU | RAM | Met maximaal VM-I/O<br /> Doorvoer | /Hana/data volume | I/O-door Voer van/Hana/data | /Hana/data IOPS | /Hana/log volume | I/O-door Voer van/Hana/log | /Hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 600 GB | 700 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M64ls | 512 GiB | 1000 MB/s | 600 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64s | 1000 GiB | 1\.000 MB/s |  1200 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64ms | 1750 GiB | 1\.000 MB/s | 2100 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M128s | 2000 GiB | 2\.000 MB/s |2400 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M128ms | 3800 GiB | 2\.000 MB/s | 4800 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M208s_v2 | 2850 GiB | 1\.000 MB/s | 3500 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M208ms_v2 | 5700 GiB | 1\.000 MB/s | 7200 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M416s_v2 | 5700 GiB | 2\.000 MB/s | 7200 GB | 1500M bps | 9000 | 512 GB | 800 MBps  | 2000 | 
| M416ms_v2 | 11400 GiB | 2\.000 MB/s | 14400 GB | 1500 MBps | 9000 | 512 GB | 800 MBps  | 2000 |   

M416xx_v2 VM-typen zijn nog niet voor het publiek beschikbaar gemaakt door micro soft. De vermelde waarden zijn bedoeld als uitgangs punt en moeten worden geëvalueerd op basis van de werkelijke vereisten. Het voor deel van Azure Ultra disk is dat de waarden voor IOPS en door Voer kunnen worden aangepast zonder de nood zaak om de virtuele machine af te sluiten of de werk belasting die op het systeem wordt toegepast, te onderbreken.   

> [!NOTE]
> Tot nu toe is opslag momentopnamen met ultra Disk Storage niet beschikbaar. Hiermee wordt het gebruik van VM-moment opnamen met Azure Backup services geblokkeerd

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie:

- [SAP Hana hand leiding voor maximale Beschik baarheid voor virtuele machines van Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).