---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f30518c3bfc9876cbddaf8295ff9e8b667a70200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74014555"
---
## <a name="overview"></a>Overzicht
Azure Storage biedt de mogelijkheid om snapshots van blobs te maken. Momentopnamen leggen de blobstatus op dat moment vast. In dit artikel beschrijven we een scenario waarin u back-ups van virtuele machineschijven onderhouden met behulp van momentopnamen. U deze methode gebruiken wanneer u ervoor kiest geen Azure Backup and Recovery Service te gebruiken en een aangepaste back-upstrategie voor uw virtuele machineschijven wilt maken.

Azure virtuele machineschijven worden opgeslagen als paginablobs in Azure Storage. Aangezien we in dit artikel een back-upstrategie voor virtuele machineschijven beschrijven, verwijzen we naar momentopnamen in de context van paginablobs. Raadpleeg Voor meer informatie over momentopnamen verwijzen naar [Een momentopname maken van een blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>Wat is een momentopname?
Een blobmomentopname is een alleen-lezen versie van een blob die op een moment wordt vastgelegd. Zodra een momentopname is gemaakt, kan deze worden gelezen, gekopieerd of verwijderd, maar niet gewijzigd. Momentopnamen bieden een manier om een back-up van een blob te maken zoals deze op een moment in de tijd wordt weergegeven. Tot REST versie 2015-04-05, had je de mogelijkheid om volledige snapshots te kopiëren. Met de REST-versie 2015-07-08 en hoger u ook incrementele momentopnamen kopiëren.

## <a name="full-snapshot-copy"></a>Volledige momentopnamekopie
Momentopnamen kunnen worden gekopieerd naar een ander opslagaccount als blob om back-ups van de basisblob te behouden. U ook een momentopname kopiëren over de basisblob, wat is als het herstellen van de blob naar een eerdere versie. Wanneer een momentopname wordt gekopieerd van het ene opslagaccount naar het andere, neemt deze dezelfde ruimte in als de blob van de basispagina. Daarom is het kopiëren van hele momentopnamen van het ene opslagaccount naar het andere traag en verbruikt het veel ruimte in het doelopslagaccount.

> [!NOTE]
> Als u de basisblob naar een andere bestemming kopieert, worden de momentopnamen van de blob niet meegekopieerd. Als u een basisblob met een kopie overschrijft, worden momentopnamen die aan de basisblob zijn gekoppeld, ook niet beïnvloed en blijven ze intact onder de naam van de basisblob.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Back-ups maken van schijven met behulp van momentopnamen
Als back-upstrategie voor uw virtuele machineschijven u periodieke momentopnamen van de schijf of paginablob maken en deze kopiëren naar een ander opslagaccount met behulp van [hulpprogramma's](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) zoals Copy Blob-bewerking of [AzCopy](../articles/storage/common/storage-use-azcopy.md). U een momentopname kopiëren naar een blob met een doelpagina met een andere naam. De resulterende doelpaginablob is een schrijfbare paginablob en geen momentopname. Later in dit artikel beschrijven we stappen om back-ups van virtuele machineschijven te maken met behulp van snapshots.

### <a name="restore-disks-using-snapshots"></a>Schijven herstellen met behulp van momentopnamen
Wanneer het tijd is om uw schijf te herstellen naar een stabiele versie die eerder is vastgelegd in een van de back-upmomentopnamen, u een momentopname kopiëren over de blob op de basispagina. Nadat de momentopname is gepromoot naar de blob op de basispagina, blijft de momentopname behouden, maar de bron wordt overschreven met een kopie die zowel kan worden gelezen als geschreven. Later in dit artikel beschrijven we stappen om een vorige versie van uw schijf te herstellen van de momentopname.

### <a name="implementing-full-snapshot-copy"></a>Volledige momentopnamekopie implementeren
U een volledige momentopname kopie implementeren door het volgende te doen,

* Maak eerst een momentopname van de basisblob met de bewerking [Momentopnameblob.](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob)
* Kopieer de momentopname vervolgens naar een doelopslagaccount met [Blob kopiëren.](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob)
* Herhaal dit proces om back-ups van uw basisblob te behouden.

## <a name="incremental-snapshot-copy"></a>Incrementele momentopnamekopie
De nieuwe functie in de [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) API biedt een veel betere manier om een back-up te maken van de momentopnamen van uw paginablobs of -schijven. De API retourneert de lijst met wijzigingen tussen de basisblob en de momentopnamen, waardoor de hoeveelheid opslagruimte die wordt gebruikt op het back-upaccount wordt verminderd. De API ondersteunt paginablobs op Premium Storage en Standard Storage. Met deze API u snellere en efficiëntere back-upoplossingen voor Azure VM's bouwen. Deze API zal beschikbaar zijn met de REST versie 2015-07-08 en hoger.

Incrementele momentopnamekopie u kopiëren van het ene opslagaccount naar het andere, het verschil tussen,

* Basisblob en de momentopname- OF
* Twee momentopnamen van de basisblob

Mits aan de volgende voorwaarden is voldaan,

* De blob is gemaakt op januari-1-2016 of later.
* De blob is niet overschreven met [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) of [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) tussen twee momentopnamen.

**Opmerking:** deze functie is beschikbaar voor Premium- en Standaard Azure Page Blobs.

Wanneer u een aangepaste back-upstrategie hebt met behulp van momentopnamen, kan het kopiëren van de momentopnamen van het ene opslagaccount naar het andere traag zijn en veel opslagruimte in beslag nemen. In plaats van de hele momentopname naar een back-upopslagaccount te kopiëren, u het verschil tussen opeenvolgende momentopnamen naar een blob met back-uppagina's schrijven. Op deze manier wordt de tijd om te kopiëren en de ruimte om back-ups op te slaan aanzienlijk verkort.

### <a name="implementing-incremental-snapshot-copy"></a>Incrementele momentopname kopiëren implementeren
U incrementele momentopnamekopiëren implementeren door het volgende te doen,

* Maak een momentopname van de basisblob met [Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob).
* Kopieer de momentopname naar het doelback-opslagaccount in dezelfde of een andere Azure-regio met [Kopiëren Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob). Dit is de back-up pagina blob. Maak een momentopname van de blob van de back-uppagina en sla deze op in het back-upaccount.
* Maak een andere momentopname van de basisblob met Snapshot Blob.
* Krijg het verschil tussen de eerste en tweede momentopnamen van de basisblob met [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges). Gebruik de nieuwe **parameterprevsnapshot**om de Datumtijdwaarde op te geven van de momentopname waarmee u het verschil wilt maken. Wanneer deze parameter aanwezig is, bevat het REST-antwoord alleen de pagina's die zijn gewijzigd tussen doelmomentopname en vorige momentopname, inclusief duidelijke pagina's.
* Gebruik [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) om deze wijzigingen toe te passen op de blob van de back-uppagina.
* Tot slot u een momentopname maken van de blob van de back-uppagina en deze opslaan in het back-upopslagaccount.

In het volgende gedeelte beschrijven we in meer detail hoe u back-ups van schijven onderhouden met incrementele momentopnamekopie

## <a name="scenario"></a>Scenario
In deze sectie beschrijven we een scenario dat een aangepaste back-upstrategie voor virtuele machineschijven met behulp van snapshots omvat.

Overweeg een Azure VM uit de DS-serie met een premium opslag-P30-schijf. De P30 schijf genaamd *mypremiumdisk* wordt opgeslagen in een premium opslag account genaamd *mypremiumaccount*. Een standaard opslagaccount genaamd *mybackupstdaccount* wordt gebruikt voor het opslaan van de back-up van *mypremiumdisk.* We willen graag elke 12 uur een momentopname van *mypremiumdisk* bewaren.

Zie [Een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)voor meer informatie over het maken van een opslagaccount .

Raadpleeg [Azure VM-back-ups plannen](../articles/backup/backup-azure-vms-introduction.md)voor meer informatie over het maken van back-ups van Azure VM's.

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Stappen om back-ups van een schijf te behouden met incrementele momentopnamen
De volgende stappen beschrijven hoe u snapshots van *mypremiumdisk kunt* maken en de back-ups in *mybackupstdaccount kunt*onderhouden. De back-up is een standaard pagina blob genaamd *mybackupstdpageblob*. De blob van de back-uppagina weerspiegelt altijd dezelfde status als de laatste momentopname van *mypremiumdisk*.

1. Maak de blob met back-uppagina's voor uw premium-opslagschijf door een momentopname te maken van *mypremiumdisk* genaamd *mypremiumdisk_ss1*.
2. Kopieer deze momentopname naar mybackupstdaccount als een paginablob genaamd *mybackupstdpageblob*.
3. Maak een momentopname van *mybackupstdpageblob* genaamd *mybackupstdpageblob_ss1,* met behulp van [Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) en sla deze op in *mybackupstdaccount*.
4. Maak tijdens het back-upvenster een andere momentopname van *mypremiumdisk,* *zeg mypremiumdisk_ss2*en sla deze op in *mypremiumaccount*.
5. Download de incrementele wijzigingen tussen de twee *momentopnamen, mypremiumdisk_ss2* en *mypremiumdisk_ss1,* met behulp van [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) op *mypremiumdisk_ss2* met de **parameter prevsnapshot** ingesteld op de tijdstempel van *mypremiumdisk_ss1*. Schrijf deze incrementele wijzigingen naar de back-up pagina blob *mybackupstdpageblob* in *mybackupstdaccount*. Als er verwijderde bereiken zijn in de incrementele wijzigingen, moeten deze worden gewist van de blob met back-uppagina's. Gebruik [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) om incrementele wijzigingen te schrijven in de blob van de back-uppagina.
6. Maak een momentopname van de back-uppagina blob *mybackupstdpageblob*, genaamd *mybackupstdpageblob_ss2*. Verwijder de vorige momentopname *mypremiumdisk_ss1* uit het premium opslagaccount.
7. Herhaal stap 4-6 elk back-upvenster. Op deze manier u back-ups van *mypremiumdisk* in een standaard opslagaccount behouden.

![Back-ups maken van de schijf met incrementele momentopnamen](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Stappen om een schijf te herstellen van momentopnamen
De volgende stappen, beschrijven hoe de premium schijf te herstellen, *mypremiumdisk* naar een eerdere momentopname van de back-up opslag account *mybackupstdaccount*.

1. Identificeer het tijdstip waarop u de premiumschijf wilt herstellen. Laten we zeggen dat het momentopname *mybackupstdpageblob_ss2,* die is opgeslagen in de back-up opslag account *mybackupstdaccount*.
2. Promoot in mybackupstdaccount de momentopname *mybackupstdpageblob_ss2* als de nieuwe back-upbasispagina *blob mybackupstdpageblobrestored*.
3. Maak een momentopname van deze herstelde blob van de back-uppagina, *mybackupstdpageblobrestored_ss1*genaamd.
4. Kopieer de herstelde pagina blob *mybackupstdpageblobrestored* van *mybackupstdaccount* naar *mypremiumaccount* als de nieuwe premium schijf *mypremiumdiskrestored*.
5. Neem een momentopname van *mypremiumdiskrestored,* genaamd *mypremiumdiskrestored_ss1* voor het maken van toekomstige incrementele back-ups.
6. Wijs de DS-serie VM op de herstelde schijf *mypremiumdiskrestored* en los de oude *mypremiumdisk* van de VM.
7. Begin het back-upproces dat in de vorige sectie is beschreven voor de herstelde schijf *mypremiumdiskrestored,* met behulp van de *mybackupstdpageblobrestored* als de blob van de back-uppagina.

![Schijf herstellen van momentopnamen](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Volgende stappen
Gebruik de volgende koppelingen voor meer informatie over het maken van momentopnamen van een blob en het plannen van uw VM-back-upinfrastructuur.

* [Een momentopname van een blob maken](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Uw VM-back-upinfrastructuur plannen](../articles/backup/backup-azure-vms-introduction.md)

