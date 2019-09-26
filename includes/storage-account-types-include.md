---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0e5c1974ecb1b3efb8df602c76700b7db04a88d7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310523"
---
Azure Storage biedt verschillende soorten opslag accounts. Elk type ondersteunt verschillende functies en heeft een eigen prijs model. Houd rekening met deze verschillen voordat u een opslag account maakt om te bepalen welk type account het meest geschikt is voor uw toepassingen. De typen opslag accounts zijn:

- **V2-accounts voor algemeen gebruik**: Basis type opslag account voor blobs, bestanden, wacht rijen en tabellen. Wordt aanbevolen voor de meeste scenario's die gebruikmaken van Azure Storage.
- **V1-accounts voor algemeen gebruik**: Verouderd account type voor blobs, bestanden, wacht rijen en tabellen. Gebruik, indien mogelijk, v2-accounts voor algemeen doel.
- **Blob Storage-accounts blok keren**: Alleen-Blob Storage-accounts met Premium-prestatie kenmerken. Aanbevolen voor scenario's met hoge transactie tarieven, het gebruik van kleinere objecten of het vereisen van een consistente lage opslag latentie.
- **FileStorage-opslag accounts**: Bestanden: alleen opslag accounts met Premium-prestatie kenmerken. Aanbevolen voor schaal toepassingen voor ondernemingen of hoge prestaties.
- **Blob Storage-accounts**: Alleen-Blob Storage-accounts. Gebruik, indien mogelijk, v2-accounts voor algemeen doel.

In de volgende tabel worden de typen opslag accounts en de mogelijkheden ervan beschreven:

| Type opslagaccount | Ondersteunde services                       | Ondersteunde prestatie lagen      | Ondersteunde toegangs lagen         | Replicatieopties               | Implementatiemodel<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Versleuteling<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Voor algemeen gebruik v2   | BLOB, bestand, wachtrij, tabel en schijf       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Hot, cool, Archive<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (preview), RA-GZRS (preview-versie)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Versleuteld              |
| Algemeen v1   | BLOB, bestand, wachtrij, tabel en schijf       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/A                            | LRS, GRS, RA-GRS                  | Resource Manager, klassiek    | Versleuteld              |
| Blob-opslag blok keren   | BLOB (alleen blok-blobs en toevoeg-blobs) | Premium                       | N/A                            | LRS                               | Resource Manager             | Versleuteld              |
| FileStorage   | Alleen bestanden | Premium                       | N/A                            | LRS                               | Resource Manager             | Versleuteld              |
| Blob Storage         | BLOB (alleen blok-blobs en toevoeg-blobs) | Standard                      | Hot, cool, Archive<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Versleuteld              |

<div id="deployment-model"><sup>1</sup> U wordt aangeraden het Azure Resource Manager-implementatie model te gebruiken. Opslag accounts die gebruikmaken van het klassieke implementatie model, kunnen nog steeds op sommige locaties worden gemaakt en bestaande klassieke accounts blijven worden ondersteund. Zie <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. klassieke implementatie voor meer informatie: Inzicht in implementatie modellen en de status van uw</a>resources.</div>

<div id="encryption"><sup>2</sup> Alle opslag accounts zijn versleuteld met behulp van Storage Service Encryption (SSE) voor Data-at-rest. Zie <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Azure Storage-service versleuteling voor Data-at-rest</a>voor meer informatie.</div>

<div id="archive"><sup>3</sup> De opslaglaag is alleen beschikbaar op niveau van een afzonderlijke blob, niet op het niveau van het opslag account. Alleen blok-blobs en toevoeg-blobs kunnen worden gearchiveerd. Zie <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob-opslag voor meer informatie: Dynamische, coole en archief opslag lagen</a>.</div>

<div id="zone-redundant-storage"><sup>4</sup> Zone-redundante opslag (ZRS) en geo-zone-redundante opslag (GZRS) (preview) zijn alleen beschikbaar voor standaard v2-opslag accounts voor algemeen gebruik. Zie <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">zone-redundante opslag (ZRS) voor meer informatie over ZRS: Maxi maal beschik</a>bare Azure Storage toepassingen. Zie <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-gzrs">geo-zone-redundante opslag voor hoge Beschik baarheid en maximale duurzaamheid (preview)</a>voor meer informatie over GZRS. Zie <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure storage-replicatie</a>voor meer informatie over andere replicatie opties.</div>

<div id="premium-performance"><sup>5</sup> Premium-prestaties voor algemeen gebruik v2 en algemeen v1-accounts zijn alleen beschikbaar voor schijven en pagina-blobs.</div>
