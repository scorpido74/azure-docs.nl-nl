---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/05/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 726fc9cc0e4eb5a8f9bed5cca8dd4d3df588ac7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371546"
---
Azure Storage biedt verschillende soorten opslagaccounts. Elk type ondersteunt verschillende functies en heeft zijn eigen prijsmodel. Houd rekening met deze verschillen voordat u een opslagaccount maakt om te bepalen welk type account het beste is voor uw toepassingen. De typen opslagaccounts zijn:

- **V2-accounts voor algemene doeleinden:** basisopslagaccounttype voor blobs, bestanden, wachtrijen en tabellen. Aanbevolen voor de meeste scenario's met Azure Storage.
- **V1-accounts voor algemene doeleinden:** verouderd accounttype voor blobs, bestanden, wachtrijen en tabellen. Gebruik in plaats daarvan v2-accounts voor algemene doeleinden wanneer dat mogelijk is.
- **BlockBlobStorage-accounts:** opslagaccounts met hoogwaardige prestatiekenmerken voor blokblobs en het toevoegen van blobs. Aanbevolen voor scenario's met hoge transactiesnelheden of scenario's die kleinere objecten gebruiken of consistent lage opslaglatentie vereisen.
- **FileStorage-accounts:** opslagaccounts met alleen bestanden met hoogwaardige prestatiekenmerken. Aanbevolen voor toepassingen op bedrijfs- of hoge prestatiesschaal.
- **BlobStorage-accounts:** alleen accounts met blobs. Gebruik in plaats daarvan v2-accounts voor algemene doeleinden wanneer dat mogelijk is.

In de volgende tabel worden de typen opslagaccounts en hun mogelijkheden beschreven:

| Type opslagaccount | Ondersteunde services                       | Ondersteunde prestatielagen      | Ondersteunde toegangsniveaus         | Replicatieopties               | Implementatiemodel<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Versleuteling<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| V2 voor algemene doeleinden   | Blob, Bestand, Wachtrij, Tabel, Schijf en Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standaard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Hot, Cool, Archief<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (preview), RA-GZRS (preview)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Versleuteld              |
| V1 voor algemeen gebruik   | Blob, Bestand, Wachtrij, Tabel en Schijf       | Standaard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N.v.t.                            | LRS, GRS, RA-GRS                  | Resource Manager, Klassiek    | Versleuteld              |
| BlockBlobStorage   | Blob (blobs blokkeren en alleen blobs toevoegen) | Premium                       | N.v.t.                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Versleuteld              |
| Bestandsopslag   | Alleen bestand | Premium                       | N.v.t.                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Versleuteld              |
| BlobOpslag         | Blob (blobs blokkeren en alleen blobs toevoegen) | Standard                      | Hot, Cool, Archief<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Versleuteld              |

<div id="deployment-model"><sup>1.</sup> Het gebruik van het implementatiemodel azure resource manager wordt aanbevolen. Opslagaccounts met behulp van het klassieke implementatiemodel kunnen nog steeds op sommige locaties worden gemaakt en bestaande klassieke accounts worden nog steeds ondersteund. Zie Azure Resource Manager vs. klassieke implementatie voor meer <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">informatie: Inzicht in implementatiemodellen en de status van uw resources.</a></div><br/>

<div id="encryption"><sup>2.</sup> Alle opslagaccounts worden versleuteld met Storage Service Encryption (SSE) voor gegevens in rust. Zie <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Azure Storage Service Encryption for Data at Rest voor</a>meer informatie.</div><br/>

<div id="archive"><sup>3</sup> Archiefopslag en gelaagdheid op blobniveau ondersteunen alleen blokblobs. De archieflaag is alleen beschikbaar op het niveau van een afzonderlijke blob, niet op het niveau van de opslagaccount. Zie <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob-opslag: opslaglagen hot, cool en archiveren</a>voor meer informatie.</div><br/>

<div id="zone-redundant-storage"><sup>4.</sup> Zoneredundante opslag (ZRS) en geo-zoneredundante opslag (GZRS/RA-GZRS) (preview) zijn alleen beschikbaar voor standaard V2-, BlockBlobStorage- en FileStorage-accounts voor algemene doeleinden in bepaalde regio's. Zie <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Redundantie azure storage</a>voor meer informatie over redundantieopties voor Azure Storage.</div><br/>

<div id="premium-performance"><sup>5.</sup> Premium prestaties voor v2- en v1-accounts voor algemene doeleinden zijn alleen beschikbaar voor schijf- en paginablob. Premium prestaties voor blok- of toevoegende blobs zijn alleen beschikbaar op BlockBlobStorage-accounts. Premium prestaties voor bestanden zijn alleen beschikbaar op FileStorage-accounts.</div><br/>

<div id="data-lake-gen2"><sup>6.</sup> Azure Data Lake Storage Gen2 is een reeks mogelijkheden die zijn toegedeeld aan big data-analyses, gebouwd op Azure Blob-opslag. Data Lake Storage Gen2 wordt alleen ondersteund op V2-opslagaccounts voor algemene doeleinden met hiërarchische naamruimte ingeschakeld. Zie Inleiding tot Azure Data Lake Storage Gen2 voor meer informatie over Data Lake Storage <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Gen2.</a></div>
