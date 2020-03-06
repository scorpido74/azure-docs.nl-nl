---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/27/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e29cdd56d1c43b3d0e8fc6ca233ac19d8b0004ff
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355855"
---
De volgende tabel beschrijft de standaard limieten voor Azure General-Purpose v1 (GPv1), v2 (GPv2), blob, Premium BlockBlob en Data Lake Gen2 ingeschakelde opslag accounts. De *ingangs* limiet verwijst naar alle gegevens die worden verzonden naar een opslag account. De *limiet voor* uitgaand verkeer verwijst naar alle gegevens die worden ontvangen van een opslag account.

| Resource | Standaardlimiet |
| --- | --- |
| Aantal opslag accounts per regio per abonnement, inclusief Standard-, Premium-en data Lake Gen2-accounts<sup>3</sup> | 250 |
| Maximale capaciteit van opslag account | 5 PiB <sup>1</sup>|
| Maximum aantal BLOB-containers, blobs, bestands shares, tabellen, wacht rijen, entiteiten of berichten per opslag account | Geen limiet |
| Maximum<sup>aantal aanvragen per opslag</sup> account | aantal aanvragen per seconde voor 20.000 |
| Maximum aantal binnenkomend<sup>1</sup> per opslag account (VS, Europa Regio's) | 25 Gbps |
| Maximum aantal ingangen<sup>1</sup> per opslag account (andere REGIO'S dan VS en Europa) | 5 Gbps als RA-GRS/GRS is ingeschakeld, 10 Gbps voor LRS/ZRS<sup>2</sup> |
| Maximale uitgaand verkeer voor de v2-en Blob Storage-accounts voor algemeen gebruik (alle regio's) | 50 Gbps |
| Maximum aantal uitgangen voor v1-opslag accounts voor algemeen gebruik (regio's in de VS) | 20 Gbps als RA-GRS/GRS is ingeschakeld, 30 Gbps voor LRS/ZRS<sup>2</sup> |
| Maximum aantal uitgangen voor v1-opslag accounts voor algemeen gebruik (niet-Amerikaanse regio's) | 10 Gbps als RA-GRS/GRS is ingeschakeld, 15 Gbps voor LRS/ZRS<sup>2</sup> |
| Maximum aantal regels voor virtuele netwerken per opslag account | 200 |
| Maximum aantal IP-adres regels per opslag account | 200 |

<sup>1</sup> Azure Storage standaard accounts ondersteunen hogere capaciteits limieten en hogere limieten voor inkomend verkeer per aanvraag. Neem contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/faq/)om een verhoging van de account limieten aan te vragen.

<sup>2</sup> als voor uw opslag account lees toegang is ingeschakeld met geo-redundante opslag (RA-GRS) of geo-zone-redundante opslag (RA-GZRS), zijn de uitgangs doelen voor de secundaire locatie identiek aan die van de primaire locatie. [Azure storage replicatie](https://docs.microsoft.com/azure/storage/common/storage-redundancy) opties zijn:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) is een set mogelijkheden die is toegewezen aan Big Data Analytics, gebouwd op Azure Blob Storage. Beperkingen voor Azure Storage en Blob Storage zijn van toepassing op Data Lake Gen2.

> [!NOTE]
> Micro soft raadt u aan om voor de meeste scenario's een v2-opslag account voor algemeen gebruik te gebruiken. U kunt eenvoudig een algemene versie van v1 of een Azure Blob Storage-account bijwerken naar een v2-account voor algemeen gebruik zonder uitval tijd en hoeft u geen gegevens te kopiëren. Zie [een upgrade uitvoeren naar een v2-opslag account voor algemeen gebruik](../articles/storage/common/storage-account-upgrade.md)voor meer informatie.

Als de behoeften van uw toepassing de schaalbaarheids doelen van één opslag account overschrijden, kunt u uw toepassing bouwen voor het gebruik van meerdere opslag accounts. U kunt vervolgens de gegevens objecten in die opslag accounts partitioneren. Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over de prijzen van volumes.

Alle opslag accounts worden uitgevoerd op een vlakke netwerk topologie, ongeacht wanneer deze zijn gemaakt. Zie [Microsoft Azure Storage: een Maxi maal beschik bare Cloud opslag service met sterke consistentie](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)voor meer informatie over de Azure Storage platte netwerk architectuur en de schaal baarheid. Een [hiërarchische naam ruimte kan worden ingeschakeld voor een Data Lake Gen2-account](../articles/storage/blobs/data-lake-storage-namespace.md) naast de platte naam ruimte voor multi protocol-toegang. Zowel platte als hiërarchische naam ruimte opslag accounts ondersteunen dezelfde schaal baarheid en prestatie doelen als beschreven in dit artikel.
