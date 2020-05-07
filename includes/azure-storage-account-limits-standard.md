---
title: Include-bestand
description: Include-bestand
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/13/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6cd883289513091ff1a57a130b12e25e012c1160
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334956"
---
In de volgende tabel worden de standaard limieten beschreven voor v1-, v2-, Blob Storage-en Data Lake Storage Gen2 ingeschakelde opslag accounts in azure voor algemeen gebruik. De *ingangs* limiet verwijst naar alle gegevens die worden verzonden naar een opslag account. De *limiet voor* uitgaand verkeer verwijst naar alle gegevens die worden ontvangen van een opslag account.

| Resource | Limiet |
| --- | --- |
| Het aantal opslag accounts per regio per abonnement, inclusief Standard-, Premium-en Data Lake Storage Gen2 ingeschakelde opslag accounts. <sup>3</sup> | 250 |
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

<sup>3</sup> [Azure data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) is een set mogelijkheden die is toegewezen aan Big Data Analytics, gebouwd op Azure Blob Storage. Er gelden beperkingen voor Azure Storage en Blob Storage voor Data Lake Storage Gen2.

> [!NOTE]
> Micro soft raadt u aan om voor de meeste scenario's een v2-opslag account voor algemeen gebruik te gebruiken. U kunt eenvoudig een algemene versie van v1 of een Azure Blob Storage-account bijwerken naar een v2-account voor algemeen gebruik zonder uitval tijd en hoeft u geen gegevens te kopiëren. Zie [een upgrade uitvoeren naar een v2-opslag account voor algemeen gebruik](../articles/storage/common/storage-account-upgrade.md)voor meer informatie.

Als de behoeften van uw toepassing de schaalbaarheids doelen van één opslag account overschrijden, kunt u uw toepassing bouwen voor het gebruik van meerdere opslag accounts. U kunt vervolgens de gegevens objecten in die opslag accounts partitioneren. Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over de prijzen van volumes.

Alle opslag accounts worden uitgevoerd op een vlakke netwerk topologie, ongeacht wanneer deze zijn gemaakt. Zie [Microsoft Azure Storage: een Maxi maal beschik bare Cloud opslag service met sterke consistentie](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets)voor meer informatie over de Azure Storage platte netwerk architectuur en de schaal baarheid. 
