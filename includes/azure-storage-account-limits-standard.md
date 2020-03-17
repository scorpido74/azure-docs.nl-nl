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
ms.openlocfilehash: b5de45086b324006a3dafdef5c80689923650759
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79381834"
---
In de volgende tabel worden de standaardlimieten beschreven voor Azure algemeen v1, v2, Blob Storage, blok-blob-opslag en via Data Lake Storage Gen2 ingeschakelde opslagaccounts. De limiet voor *inkomend verkeer* verwijst naar alle gegevens die naar een opslagaccount worden verzonden. De limiet voor *uitgaand verkeer* verwijst naar alle gegevens die vanaf een opslagaccount worden ontvangen.

| Resource | Standaardlimiet |
| --- | --- |
| Het aantal opslagaccounts per regio per abonnement, inclusief via Standard-, Premium- en Data Lake Storage Gen2 ingeschakelde opslagaccounts.<sup>3</sup> | 250 |
| Maximale capaciteit van opslagaccount | 5 PiB <sup>1</sup>|
| Maximum aantal blobcontainers, blobs, bestandsshares, tabellen, wachtrijen, entiteiten of berichten per opslagaccount | Geen limiet |
| Maximum aantal aanvragen<sup>1</sup> per opslagaccount | 20.000 aanvragen per seconde |
| Maximale hoeveelheid inkomend verkeer<sup>1</sup> per opslagaccount (regio's Verenigde Staten en Europa) | 25 Gbps |
| Maximale hoeveelheid inkomend verkeer<sup>1</sup> per opslagaccount (overige regio's) | 5 Gbps als RA-GRS/GRS is ingeschakeld, 10 Gbps voor LRS/ZRS<sup>2</sup> |
| Maximale hoeveelheid uitgaand verkeer voor algemeen v2- en Blob Storage-accounts (alle regio's) | 50 Gbps |
| Maximale hoeveelheid uitgaand verkeer voor algemeen v1-accounts (VS-regio's) | 20 Gbps als RA-GRS/GRS is ingeschakeld, 30 Gbps voor LRS/ZRS<sup>2</sup> |
| Maximale hoeveelheid uitgaand verkeer voor algemeen v1-accounts (niet-VS-regio's) | 10 Gbps als RA-GRS/GRS is ingeschakeld, 15 Gbps voor LRS/ZRS<sup>2</sup> |
| Maximum aantal virtuele-netwerkregels per opslagaccount | 200 |
| Maximum aantal IP-adresregels per opslagaccount | 200 |

<sup>1</sup> Azure Storage-standaardaccounts ondersteunen hogere capaciteitslimieten en hogere limieten voor inkomend verkeer op aanvraag. Neem contact op met de [Azure-ondersteuning](https://azure.microsoft.com/support/faq/) om een hogere accountlimiet aan te vragen.

<sup>2</sup> Als voor uw opslagaccount leestoegang is ingeschakeld met geografisch redundante opslag (RA-GRS) of geo-zone-redundante opslag (RA-GZRS), zijn de doelen voor uitgaand verkeer voor de secundaire locatie identiek aan die van de primaire locatie. Opties voor [Azure Storage-replicatie](https://docs.microsoft.com/azure/storage/common/storage-redundancy) zijn:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) is een reeks mogelijkheden die is toegewezen aan analyse van big data, gebouwd op Azure Blob Storage. Er gelden Azure Storage- en blobopslagbeperkingen voor Data Lake Storage Gen2.

> [!NOTE]
> Microsoft raadt aan voor de meeste scenario's een algemeen v2-opslagaccount te gebruiken. U kunt een algemeen v1 of een Azure Blob Storage-account eenvoudig bijwerken naar een algemeen v2-account zonder dat u last hebt van uitval en zonder gegevens te hoeven kopiëren. Zie [Upgraden naar een algemeen v2-opslagaccount](../articles/storage/common/storage-account-upgrade.md) voor meer informatie.

Als de behoeften van uw toepassing de schaalbaarheidsdoelen van één opslagaccount te boven gaan, kunt u uw toepassing bouwen voor gebruik van meerdere opslagaccounts. Vervolgens kunt u de gegevensobjecten voor die opslagaccounts partitioneren. Zie [Prijzen van Azure Storage](https://azure.microsoft.com/pricing/details/storage/) voor meer informatie over volumeprijzen.

Alle opslagaccounts worden uitgevoerd op een platte netwerktopologie, ongeacht wanneer ze zijn gemaakt. Voor meer informatie over de platte Azure Storage-netwerkarchitectuur en schaalbaarheid raadpleegt u [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets) (Microsoft Azure Storage: een maximaal beschikbare cloudopslagservice met grote consistentie). 
