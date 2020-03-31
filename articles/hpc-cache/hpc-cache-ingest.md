---
title: Gegevens verplaatsen naar een Azure HPC-cachecloudcontainer
description: Azure Blob-opslag invullen voor gebruik met Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: a5625341e3dd279d93a59c57cd3325245351723e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271874"
---
# <a name="move-data-to-azure-blob-storage"></a>Gegevens verplaatsen naar Azure Blob-opslag

Als uw werkstroom gegevens verplaatst naar Azure Blob-opslag, controleert u of u een efficiënte strategie gebruikt. U gegevens vooraf laden in een nieuwe Blob-container voordat u deze definieert als opslagdoel, of de container toevoegen en uw gegevens vervolgens kopiëren met Azure HPC-cache.

In dit artikel worden de beste manieren uitgelegd om gegevens naar Blob-opslag te verplaatsen voor gebruik met Azure HPC-cache.

Houd rekening met deze feiten:

* Azure HPC-cache maakt gebruik van een gespecialiseerde opslagindeling om gegevens in Blob-opslag te ordenen. Daarom moet een Blob-opslagdoel een nieuwe, lege container zijn of een Blob-container die eerder is gebruikt voor Azure HPC-cachegegevens. <!--([Avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/) also uses this cloud file system.)-->

* Het kopiëren van gegevens via de Azure HPC-cache naar een back-endopslagdoel is efficiënter wanneer u meerdere clients en parallelle bewerkingen gebruikt. Een eenvoudige kopie opdracht van een client zal gegevens langzaam verplaatsen.

Er is een op Python gebaseerd hulpprogramma beschikbaar om inhoud in een Blob-opslagcontainer te laden. Lees [Gegevens vooraf laden in blobopslag](#pre-load-data-in-blob-storage-with-clfsload) voor meer informatie.

Als u het laadhulpprogramma niet wilt gebruiken of als u inhoud wilt toevoegen aan een bestaand opslagdoel, volgt u de parallelle gegevensinnametips in [Gegevens kopiëren via de Azure HPC-cache.](#copy-data-through-the-azure-hpc-cache)

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>Gegevens vooraf laden in Blob-opslag met CLFSLoad

U de <!--[Avere CLFSLoad](https://aka.ms/avere-clfsload)--> Avere CLFSLoad-hulpprogramma om gegevens naar een nieuwe Blob-opslagcontainer te kopiëren voordat u deze toevoegt als opslagdoel. Dit hulpprogramma draait op één Linux-systeem en schrijft gegevens in de eigen indeling die nodig is voor Azure HPC-cache. CLFSLoad is de meest efficiënte manier om een Blob-opslagcontainer in te vullen voor gebruik met de cache.

Het Avere CLFSLoad-hulpprogramma is op verzoek beschikbaar van uw Azure HPC-cacheteam. Vraag uw team contact voor, of open een [support ticket](hpc-cache-support-ticket.md) om hulp te vragen.

Deze optie werkt alleen met nieuwe, lege containers. Maak de container voordat u Avere CLFSLoad gebruikt.

Gedetailleerde informatie is opgenomen in de Avere CLFSLoad-distributie, die op verzoek beschikbaar is van het Azure HPC-cacheteam. <!-- [Avere CLFSLoad readme](https://github.com/microsoft/Avere-CLFSLoad/blob/master/README.md). --><!-- caution literal link -->

Een algemeen overzicht van het proces:

1. Bereid een Linux-systeem (VM of fysiek) voor met Python versie 3.6 of hoger. Python 3.7 wordt aanbevolen voor betere prestaties.
1. Installeer de Avere-CLFSLoad software op het Linux systeem.
1. Voer de overdracht uit vanaf de Linux-opdrachtregel.

Het Hulpprogramma van Avere CLFSLoad heeft de volgende informatie nodig:

* De opslagaccount-id die uw Blob-opslagcontainer bevat
* De naam van de lege Blob-opslagcontainer
* Een SAS-token (shared access signature) waarmee het hulpprogramma naar de container kan schrijven
* Een lokaal pad naar de gegevensbron - een lokale map met de gegevens die u wilt kopiëren, of een lokaal pad naar een opgezet extern systeem met de gegevens

<!-- The requirements are explained in detail in the [Avere CLFSLoad readme](https://aka.ms/avere-clfsload). -->

## <a name="copy-data-through-the-azure-hpc-cache"></a>Gegevens kopiëren via de Azure HPC-cache

Als u het hulpprogramma Avere CLFSLoad niet wilt gebruiken of als u een grote hoeveelheid gegevens wilt toevoegen aan een bestaand Blob-opslagdoel, u deze kopiëren via de cache. Azure HPC Cache is ontworpen om meerdere clients tegelijk te bedienen, dus om gegevens door de cache te kopiëren, moet u parallelle schrijfbewerkingen van meerdere clients gebruiken.

![Diagram met gegevensbewegingen met meerdere clientnen: linksboven heeft een pictogram voor on-premises hardwareopslag meerdere pijlen die daaruit komen. De pijlen wijzen naar vier clientmachines. Van elke clientmachine wijzen drie pijlen naar de Azure HPC-cache. Vanuit de Azure HPC-cache wijzen meerdere pijlen naar Blob-opslag.](media/hpc-cache-parallel-ingest.png)

De ``cp`` ``copy`` of opdrachten die u doorgaans gebruikt om gegevens van het ene opslagsysteem naar het andere over te zetten, zijn processen met één thread die slechts één bestand tegelijk kopiëren. Dit betekent dat de bestandsserver slechts één bestand tegelijk inneemt - wat een verspilling is van de bronnen van de cache.

In dit gedeelte worden strategieën uitgelegd voor het maken van een multi-client, multi-threaded bestandskopieersysteem om gegevens naar Blob-opslag te verplaatsen met Azure HPC-cache. Het verklaart bestandsoverdracht concepten en beslissingspunten die kunnen worden gebruikt voor efficiënte gegevens kopiëren met behulp van meerdere clients en eenvoudige kopieeropdrachten.

Het verklaart ook een aantal hulpprogramma's die kunnen helpen. Het ``msrsync`` hulpprogramma kan worden gebruikt om het proces van het verdelen van een gegevensset in buckets en het gebruik van rsync-opdrachten gedeeltelijk te automatiseren. Het ``parallelcp`` script is een ander hulpprogramma dat de bronmap leest en automatisch kopieeropdrachten uitgeeft.

### <a name="strategic-planning"></a>Strategische planning

Wanneer u een strategie bouwt om gegevens parallel te kopiëren, moet u de afwegingen in bestandsgrootte, bestandstelling en mapdiepte begrijpen.

* Wanneer bestanden klein zijn, is de statistiek van belang bestanden per seconde.
* Wanneer bestanden groot zijn (10MiBi of hoger), is de statistiek van belang bytes per seconde.

Elk kopieerproces heeft een doorvoersnelheid en een door bestanden overgedragen snelheid, die kan worden gemeten aan de doortiming van de lengte van de kopieeropdracht en factoring van de bestandsgrootte en het aantal bestanden. Uitleggen hoe de tarieven te meten is buiten het bereik van dit document, maar het is noodzakelijk om te begrijpen of je te maken hebt met kleine of grote bestanden.

Strategieën voor parallelle gegevens inname met Azure HPC Cache zijn:

* Handmatig kopiëren - U handmatig een kopiëren met meerdere threaden op een client maken door meer dan één kopieeropdracht tegelijk op de achtergrond uit te voeren tegen vooraf gedefinieerde sets bestanden of paden. Lees [Azure HPC Cache gegevens inname - handmatige kopie methode](hpc-cache-ingest-manual.md) voor meer informatie.

* Gedeeltelijk geautomatiseerd kopiëren ``msrsync``  -  ``msrsync`` met is een wrapper ``rsync`` utility die meerdere parallelle processen uitvoert. Lees voor meer informatie [Azure HPC Cache-gegevens inname - msrsync-methode](hpc-cache-ingest-msrsync.md).

* Gescript kopiëren ``parallelcp`` met - Meer informatie over het maken en uitvoeren van een parallel kopieerscript in [Azure HPC Cache-gegevensopname - parallelle kopieerscriptmethode](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>Volgende stappen

Nadat u uw opslag hebt ingesteld, leest u hoe clients de cache kunnen monteren.

* [Toegang tot het Azure HPC-cachesysteem](hpc-cache-mount.md)
