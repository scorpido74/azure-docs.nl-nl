---
title: Azure HPC-cachegegevens inbeslagnemen - msrsync
description: Msrsync gebruiken om gegevens te verplaatsen naar een Blob-opslagdoel in Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4f8863d706d623d613ac156cf202c3b7b12f2ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168423"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC-cachegegevens inbeslagnemen - msrsync-methode

In dit artikel vindt ``msrsync`` u gedetailleerde instructies voor het gebruik van het hulpprogramma om gegevens te kopiëren naar een Azure Blob-opslagcontainer voor gebruik met Azure HPC-cache.

Lees [Gegevens verplaatsen naar Azure Blob-opslag voor](hpc-cache-ingest.md)meer informatie over het verplaatsen van gegevens naar Blob-opslag voor uw Azure HPC-cache.

De ``msrsync`` tool kan worden gebruikt om gegevens te verplaatsen naar een backend-opslagdoel voor de Azure HPC-cache. Deze tool is ontworpen om het ``rsync`` bandbreedtegebruik te optimaliseren door meerdere parallelle processen uit te voeren. Het is verkrijgbaar bij https://github.com/jbd/msrsyncGitHub op .

``msrsync``breekt de bronmap op in afzonderlijke "buckets" en voert vervolgens afzonderlijke ``rsync`` processen uit op elke bucket.

Voorlopige tests met behulp van een vier-core VM toonde de beste efficiëntie bij het gebruik van 64 processen. Gebruik ``msrsync`` de ``-p`` optie om het aantal processen in te stellen op 64.

Houd ``msrsync`` er rekening mee dat u alleen naar en van lokale volumes schrijven. De bron en bestemming moeten toegankelijk zijn als lokale bevestigingen op het werkstation dat wordt gebruikt om de opdracht uit te geven.

Volg de volgende ``msrsync`` instructies om Azure Blob-opslag te vullen met Azure HPC-cache:

1. Installeren ``msrsync`` en de``rsync`` vereisten (en Python 2.6 of hoger)
1. Bepaal het totale aantal bestanden en mappen dat moet worden gekopieerd.

   Gebruik bijvoorbeeld het ``prime.py`` hulpprogramma ```prime.py --directory /path/to/some/directory``` met argumenten (beschikbaar door te <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>downloaden).

   Als u ``prime.py``dit niet gebruikt, u ``find`` het aantal items met het gereedschap GNU als volgt berekenen:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Verdeel het aantal items door 64 om het aantal items per proces te bepalen. Gebruik dit nummer ``-f`` met de optie om de grootte van de buckets in te stellen wanneer u de opdracht uitvoert.

1. Geef ``msrsync`` de opdracht uit om bestanden te kopiëren:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Deze opdracht is bijvoorbeeld ontworpen om 11.000 bestanden in 64 processen te verplaatsen van /test/source-repository naar /mnt/hpccache/repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
