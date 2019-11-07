---
title: Gegevens opname in de Azure HPC-cache-msrsync
description: Msrsync gebruiken om gegevens te verplaatsen naar een Blob Storage-doel in azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 3e5937a036763fab57f9e37494ace33e8452b1f2
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582274"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC cache data opname-msrsync-methode

In dit artikel vindt u gedetailleerde instructies voor het gebruik van het hulp programma ``msrsync`` om gegevens te kopiëren naar een Azure Blob Storage-container voor gebruik met de Azure HPC-cache.

Lees voor meer informatie over het verplaatsen van gegevens naar Blob Storage voor uw Azure HPC-cache [gegevens verplaatsen naar Azure Blob-opslag voor Azure HPC-cache](hpc-cache-ingest.md).

Het ``msrsync``-hulp programma kan worden gebruikt om gegevens te verplaatsen naar een back-end-opslag doel voor de Azure HPC-cache. Dit hulp programma is ontworpen om het bandbreedte gebruik te optimaliseren door meerdere parallelle ``rsync`` processen uit te voeren. Het is beschikbaar via GitHub op https://github.com/jbd/msrsync.

``msrsync`` splitst de bron directory op in afzonderlijke buckets en voert vervolgens afzonderlijke ``rsync`` processen uit voor elke Bucket.

Voor bereiding van het testen met behulp van een virtuele machine met vier kernen wordt de beste efficiëntie weer gegeven wanneer u 64 processen Gebruik de optie ``msrsync`` ``-p`` om het aantal processen in te stellen op 64.

Houd er rekening mee dat ``msrsync`` alleen kunnen schrijven naar en van lokale volumes. De bron en het doel moeten toegankelijk zijn als lokale koppels op het werk station dat wordt gebruikt voor het uitgeven van de opdracht.

Volg deze instructies voor het gebruik van ``msrsync`` voor het vullen van Azure Blob-opslag met Azure HPC cache:

1. Installeer ``msrsync`` en de vereiste onderdelen (``rsync`` en Python 2,6 of hoger)
1. Bepaal het totale aantal bestanden en mappen dat moet worden gekopieerd.

   Gebruik bijvoorbeeld het hulp programma ``prime.py`` met argumenten ```prime.py --directory /path/to/some/directory``` (beschikbaar door <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>te downloaden).

   Als u ``prime.py``niet gebruikt, kunt u als volgt het aantal items met het GNU ``find``-hulp programma berekenen:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Deel het aantal items door 64 om het aantal items per proces te bepalen. Gebruik dit nummer met de optie ``-f`` om de grootte van de buckets in te stellen wanneer u de opdracht uitvoert.

1. Geef de ``msrsync`` opdracht voor het kopiëren van bestanden:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Deze opdracht is bijvoorbeeld ontworpen om 11.000-bestanden in 64 processen van/test/Source-Repository naar/mnt/hpccache/repository te verplaatsen:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
