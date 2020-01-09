---
title: Gegevens verplaatsen naar een Cloud container van een Azure HPC-cache
description: Azure Blob-opslag vullen voor gebruik met de Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: a5625341e3dd279d93a59c57cd3325245351723e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646724"
---
# <a name="move-data-to-azure-blob-storage"></a>Gegevens verplaatsen naar Azure Blob-opslag

Als uw werk stroom het verplaatsen van gegevens naar Azure Blob-opslag bevat, moet u ervoor zorgen dat u een efficiënte strategie gebruikt. U kunt gegevens in een nieuwe BLOB-container laden voordat u deze als opslag doel definieert, of de container toevoegen en uw gegevens vervolgens kopiëren met behulp van de HPC-cache van Azure.

In dit artikel worden de beste manieren beschreven om gegevens te verplaatsen naar Blob Storage voor gebruik met de Azure HPC-cache.

Houd u aan de volgende feiten:

* Azure HPC cache maakt gebruik van een speciale opslag indeling voor het organiseren van gegevens in Blob Storage. Daarom moet een Blob Storage-doel een nieuwe, lege container of een BLOB-container zijn die eerder werd gebruikt voor de gegevens in de Azure HPC-cache. <!--([Avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/) also uses this cloud file system.)-->

* Het kopiëren van gegevens via de Azure HPC-cache naar een back-end-opslag doel is efficiënter wanneer u meerdere clients en parallelle bewerkingen gebruikt. Met een eenvoudige Kopieer opdracht van één client worden gegevens langzaam verplaatst.

Een python-gebaseerd hulp programma is beschikbaar voor het laden van inhoud in een BLOB storage-container. Lees [vooraf geladen gegevens in Blob Storage](#pre-load-data-in-blob-storage-with-clfsload) voor meer informatie.

Als u het laad hulpprogramma niet wilt gebruiken of als u inhoud wilt toevoegen aan een bestaand opslag doel, volgt u de tips voor het opnemen van parallelle gegevens in [gegevens kopiëren via de Azure HPC-cache](#copy-data-through-the-azure-hpc-cache).

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>Gegevens vooraf laden in Blob Storage met CLFSLoad

Kunt u de parameters <!--[Avere CLFSLoad](https://aka.ms/avere-clfsload)--> AVERE CLFSLoad-hulp programma voor het kopiëren van gegevens naar een nieuwe Blob Storage-container voordat u deze toevoegt als een opslag doel. Dit hulp programma wordt uitgevoerd op één Linux-systeem en schrijft gegevens in de eigen indeling die nodig is voor de Azure HPC-cache. CLFSLoad is de meest efficiënte manier om een BLOB storage-container te vullen voor gebruik met de cache.

Het avere CLFSLoad-hulp programma is beschikbaar op verzoek van uw Azure HPC-cache team. Vraag uw team contact op te nemen of open een [ondersteunings ticket](hpc-cache-support-ticket.md) om hulp aan te vragen.

Deze optie werkt met nieuwe, lege containers. Maak de container voordat u avere CLFSLoad gebruikt.

Gedetailleerde informatie is opgenomen in de avere CLFSLoad-distributie, die beschikbaar is op aanvraag vanuit het team van de HPC-cache van Azure. <!-- [Avere CLFSLoad readme](https://github.com/microsoft/Avere-CLFSLoad/blob/master/README.md). --><!-- caution literal link -->

Een algemeen overzicht van het proces:

1. Bereid een Linux-systeem (VM of fysiek) voor met python versie 3,6 of hoger. Python 3,7 wordt aanbevolen voor betere prestaties.
1. Installeer de avere-CLFSLoad-software op het Linux-systeem.
1. Voer de overdracht uit vanaf de Linux-opdracht regel.

Het hulp programma avere CLFSLoad heeft de volgende informatie nodig:

* De ID van het opslag account die uw Blob Storage-container bevat
* De naam van de lege Blob Storage-container
* Een SAS-token (Shared Access Signature) waarmee het hulp programma naar de container kan schrijven
* Een lokaal pad naar de gegevens Bron: een lokale map die de te kopiëren gegevens bevat of een lokaal pad naar een gekoppeld extern systeem met de gegevens

<!-- The requirements are explained in detail in the [Avere CLFSLoad readme](https://aka.ms/avere-clfsload). -->

## <a name="copy-data-through-the-azure-hpc-cache"></a>Gegevens kopiëren via de Azure HPC-cache

Als u het hulp programma avere CLFSLoad niet wilt gebruiken of als u een grote hoeveelheid gegevens wilt toevoegen aan een bestaand Blob Storage-doel, kunt u deze kopiëren via de cache. De Azure HPC-cache is zodanig ontworpen dat meerdere clients tegelijk kunnen worden gebruikt om gegevens te kopiëren via de cache, maar u moet parallelle schrijf bewerkingen van meerdere clients gebruiken.

![Diagram van het proces van het verplaatsen van meerdere clients en gegevens verplaatsing met meerdere threads: linksboven, een pictogram voor on-premises hardwarematige opslag bevat meerdere pijlen. De pijlen verwijzen naar vier client machines. Vanaf elke client computer worden drie pijlen naar de Azure HPC-cache gericht. Vanuit de Azure HPC-cache verwijzen meerdere pijlen naar Blob Storage.](media/hpc-cache-parallel-ingest.png)

De opdrachten ``cp`` of ``copy`` die u doorgaans gebruikt voor het overdragen van gegevens van het ene opslag systeem naar het andere, zijn processen met één thread waarmee slechts één bestand tegelijk wordt gekopieerd. Dit betekent dat de bestands server slechts één bestand tegelijkertijd bijwerkt. Dit is een afval van de bronnen in de cache.

In deze sectie worden strategieën uitgelegd voor het maken van een multi-client, multi-threaded bestand voor het kopiëren van gegevens naar Blob Storage met Azure HPC-cache. Hierin worden de concepten van bestands overdracht en beslissings punten uitgelegd die kunnen worden gebruikt voor het efficiënt kopiëren van gegevens met meerdere clients en eenvoudige Kopieer opdrachten.

Er wordt ook een aantal hulpprogram ma's beschreven die kunnen helpen. Het hulp programma ``msrsync`` kan worden gebruikt om het proces voor het delen van een gegevensset in buckets gedeeltelijk te automatiseren en rsync-opdrachten te gebruiken. Het ``parallelcp`` script is een ander hulp programma waarmee de bron directory wordt gelezen en automatisch Kopieer opdrachten worden gekopieerd.

### <a name="strategic-planning"></a>Strategische planning

Bij het bouwen van een strategie voor het parallel kopiëren van gegevens, moet u inzicht hebben in de bestands grootte, het aantal bestanden en de diepte van de map.

* Wanneer bestanden klein zijn, zijn de belang rijke gegevens bestanden per seconde.
* Wanneer bestanden groot zijn (10MiBi of hoger), is de belang rijke hoeveelheid bytes per seconde.

Elk kopieer proces heeft een doorvoer snelheid en een snelheid waarmee bestanden worden overgedragen, wat kan worden gemeten door de tijds duur van de Kopieer opdracht te bepalen en de bestands grootte en het aantal bestanden te bepalen. Uitleg over het meten van de tarieven valt buiten het bereik van dit document, maar het is wel belang rijk om te begrijpen of u met kleine of grote bestanden gaat omgaan.

Strategieën voor het opnemen van parallelle gegevens opname met Azure HPC cache zijn onder andere:

* Hand matig kopiëren: u kunt hand matig een kopie met meerdere threads maken op een client door meer dan één Kopieer opdracht tegelijk op de achtergrond uit te voeren op basis van vooraf gedefinieerde sets van bestanden of paden. Lees de [Azure HPC cache-gegevens opname-hand matige Kopieer methode](hpc-cache-ingest-manual.md) voor meer informatie.

* Gedeeltelijk geautomatiseerd kopiëren met ``msrsync`` - ``msrsync`` is een wrapper-hulp programma dat meerdere parallelle ``rsync`` processen uitvoert. Lees voor meer informatie de [methode Azure HPC cache data opname-msrsync](hpc-cache-ingest-msrsync.md).

* Kopiëren via script met ``parallelcp``-meer informatie over het maken en uitvoeren van een script voor parallelle kopieën in [Azure HPC cache gegevens opname-parallelle kopie script methode](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>Volgende stappen

Nadat u uw opslag hebt ingesteld, leert u hoe clients de cache kunnen koppelen.

* [Toegang tot het systeem voor de HPC-cache van Azure](hpc-cache-mount.md)
