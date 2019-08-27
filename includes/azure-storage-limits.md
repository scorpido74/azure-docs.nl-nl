---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d5ce4c094da3a411168c7fe4c282b15ceac7bb86
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036692"
---
In de volgende tabel worden de standaard limieten voor v1-, v2-en Blob Storage-accounts voor Azure voor algemeen gebruik beschreven. De *ingangs* limiet verwijst naar alle gegevens van aanvragen die worden verzonden naar een opslag account. De limiet voor uitgaand verkeer verwijst naar alle gegevens van antwoorden die worden ontvangen van een opslag account.

| Resource | Standaardlimiet |
| --- | --- |
| Aantal opslag accounts per regio per abonnement, inclusief standaard-en Premium-accounts | 250 |
| Maximale capaciteit van opslag account | 2 PB voor VS en Europa, en 500 TB voor alle andere regio's (inclusief het Verenigd Konink rijk)<sup>1</sup>|
| Maximum aantal BLOB-containers, blobs, bestands shares, tabellen, wacht rijen, entiteiten of berichten per opslag account | Geen limiet |
| Maximum aantal aanvragen<sup></sup> per opslag account | aantal aanvragen per seconde voor 20.000 |
| Maximum aantal binnenkomend<sup>1</sup> per opslag account (VS, Europa Regio's) | 25 Gbps |
| Maximum aantal ingangen<sup>1</sup> per opslag account (andere REGIO'S dan VS en Europa) | 5 Gbps als RA-GRS/GRS is ingeschakeld, 10 Gbps voor LRS/ZRS<sup>2</sup> |
| Maximale uitgaand verkeer voor de v2-en Blob Storage-accounts voor algemeen gebruik (alle regio's) | 50 Gbps |
| Maximum aantal uitgangen voor v1-opslag accounts voor algemeen gebruik (regio's in de VS) | 20 Gbps als RA-GRS/GRS is ingeschakeld, 30 Gbps voor LRS/ZRS<sup>2</sup> |
| Maximum aantal uitgangen voor v1-opslag accounts voor algemeen gebruik (niet-Amerikaanse regio's) | 10 Gbps als RA-GRS/GRS is ingeschakeld, 15 Gbps voor LRS/ZRS<sup>2</sup> |

<sup>1</sup> Azure Standard-opslag accounts ondersteunen hogere capaciteits limieten en hogere limieten voor binnenkomend op aanvraag. Neem contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/faq/)om een verhoging van de account limieten voor inkomend verkeer aan te vragen. Zie voor meer informatie aankondigen van [grotere opslag accounts met hogere schaal](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>2</sup> [Azure storage replicatie](https://docs.microsoft.com/azure/storage/common/storage-redundancy) opties zijn:

- **RA-GRS**: Geografisch redundante opslag met lees toegang. Als RA-GRS is ingeschakeld, zijn de uitgangs doelen voor de secundaire locatie identiek aan die voor de primaire locatie.
- **GRS**: Geografisch redundante opslag.
- **ZRS**: Zone-redundante opslag.
- **LRS**: Lokaal redundante opslag.

> [!NOTE]
> We raden u aan om voor de meeste scenario's een v2-opslag account voor algemeen gebruik te gebruiken. U kunt eenvoudig een algemene versie van v1 of een Azure Blob Storage-account bijwerken naar een v2-account voor algemeen gebruik zonder uitval tijd en hoeft u geen gegevens te kopiëren.
>
> Zie [overzicht van opslag accounts](../articles/storage/common/storage-account-overview.md)voor meer informatie over Azure Storage-accounts.

Als de behoeften van uw toepassing de schaalbaarheids doelen van één opslag account overschrijden, kunt u uw toepassing bouwen voor het gebruik van meerdere opslag accounts. U kunt vervolgens de gegevens objecten in die opslag accounts partitioneren. Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over de prijzen van volumes.

Alle opslag accounts worden uitgevoerd op een vlakke netwerk topologie en bieden ondersteuning voor de schaal baarheid en prestatie doelen die in dit artikel worden beschreven, ongeacht wanneer ze zijn gemaakt. Zie [Microsoft Azure Storage voor meer informatie over de Azure Storage platte netwerk architectuur en de schaal baarheid: Een Maxi maal beschik bare Cloud opslag service](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)met sterke consistentie.

