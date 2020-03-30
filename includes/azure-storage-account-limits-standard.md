---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/13/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6cd883289513091ff1a57a130b12e25e012c1160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334956"
---
In de volgende tabel worden standaardlimieten beschreven voor azure v1-, v2-, blob-opslag, opslag met blokblobs en opslagaccounts met gegevensopslag met Gegevensmeeropslag Gen2. De *invallenlimiet* verwijst naar alle gegevens die naar een opslagaccount worden verzonden. De *uitgangslimiet* verwijst naar alle gegevens die van een opslagaccount worden ontvangen.

| Resource | Limiet |
| --- | --- |
| Aantal opslagaccounts per regio per abonnement, inclusief standaard-, premium- en Data Lake Storage Gen2-opslagaccounts. <sup>3.</sup> | 250 |
| Maximale opslagcapaciteit voor opslagaccount | 5 PiB <sup>1</sup>|
| Maximum aantal blobcontainers, blobs, bestandsshares, tabellen, wachtrijen, entiteiten of berichten per opslagaccount | Geen limiet |
| Maximum aanvraagtarief<sup>1</sup> per opslagaccount | 20.000 aanvragen per seconde |
| Maximale ingress<sup>1</sup> per opslagaccount (VS, Europa regio's) | 25 Gbps |
| Maximale in- en toerekening<sup>1</sup> per opslagaccount (andere regio's dan de VS en Europa) | 5 Gbps als RA-GRS/GRS is ingeschakeld, 10 Gbps voor LRS/ZRS<sup>2</sup> |
| Maximale uitgang voor v2- en Blob-opslagaccounts voor algemene doeleinden (alle regio's) | 50 Gbps |
| Maximale uitgang voor v1-opslagaccounts voor algemene doeleinden (Amerikaanse regio's) | 20 Gbps als RA-GRS/GRS is ingeschakeld, 30 Gbps voor LRS/ZRS<sup>2</sup> |
| Maximale uitgang voor v1-opslagaccounts voor algemene doeleinden (niet-Amerikaanse regio's) | 10 Gbps als RA-GRS/GRS is ingeschakeld, 15 Gbps voor LRS/ZRS<sup>2</sup> |
| Maximum aantal virtuele netwerkregels per opslagaccount | 200 |
| Maximum aantal IP-adresregels per opslagaccount | 200 |

<sup>1</sup> Azure Storage-standaardaccounts ondersteunen hogere capaciteitslimieten en hogere limieten voor binnendringen op aanvraag. Neem contact op met [Azure Support](https://azure.microsoft.com/support/faq/)om een toename van accountlimieten aan te vragen.

<sup>2</sup> Als uw opslagaccount read-access heeft ingeschakeld met georedundante opslag (RA-GRS) of geo-zoneredundante opslag (RA-GZRS), zijn de uitgangsdoelen voor de secundaire locatie identiek aan die van de primaire locatie. [De replicatieopties voor Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) omvatten:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) is een reeks mogelijkheden die zijn toegedeeld aan big data-analyses, gebouwd op Azure Blob-opslag. Azure Storage en blob storage beperkingen zijn van toepassing op Data Lake Storage Gen2.

> [!NOTE]
> Microsoft raadt u aan voor de meeste scenario's een v2-opslagaccount voor algemene doeleinden te gebruiken. U eenvoudig een v1 voor algemene doeleinden of een Azure Blob-opslagaccount upgraden naar een v2-account voor algemene doeleinden zonder downtime en zonder dat u gegevens hoeft te kopiëren. Zie [Upgraden naar een v2-opslagaccount voor algemene doeleinden voor](../articles/storage/common/storage-account-upgrade.md)meer informatie.

Als de behoeften van uw toepassing de schaalbaarheidsdoelstellingen van één opslagaccount overschrijden, u uw toepassing bouwen om meerdere opslagaccounts te gebruiken. U uw gegevensobjecten vervolgens over deze opslagaccounts verdelen. Zie [Azure Storage-prijzen](https://azure.microsoft.com/pricing/details/storage/)voor informatie over volumeprijzen.

Alle opslagaccounts worden uitgevoerd op een platte netwerktopologie, ongeacht wanneer ze zijn gemaakt. Zie [Microsoft Azure Storage: Een zeer beschikbare cloudopslagservice met sterke consistentie](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets)voor meer informatie over de flatnetwerkarchitectuur van Azure Storage en over schaalbaarheid. 
