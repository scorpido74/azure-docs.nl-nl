---
title: bestand opnemen
description: bestand opnemen
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 03/11/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0090f02382e024e5539383328b55d58798002d63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117135"
---
| Resource | Basic | Standard | Premium |
|---|---|---|---|
| Opslag<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maximale grootte van de afbeeldingslaag | 200 GiB | 200 GiB | 200 GiB |
| ReadOps per minuut<sup>2, 3</sup> | 1000 | 3000 | 10.000 |
| WriteOps per minuut<sup>2, 4</sup> | 100 | 500 | 2.000 |
| Download bandbreedte MBps<sup>2</sup> | 30 | 60 | 100 |
| Uploadbandbreedte MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| Geo-replicatie | N.v.t. | N.v.t. | [Ondersteund][geo-replication] |
| Inhoud vertrouwen | N.v.t. | N.v.t. | [Ondersteund][content-trust] |
| Toegang tot virtuele netwerken | N.v.t. | N.v.t. | [Voorbeeld][vnet] |
| Integratie van privékoppelingen | N.v.t. | N.v.t. | [Voorbeeld][plink] |
| Door klant beheerde sleutels | N.v.t. | N.v.t. | [Voorbeeld][cmk] |
| Machtigingen met opslagplaatsen | N.v.t. | N.v.t. | [Voorbeeld][token]|
| &bull;Tokens | N.v.t. | N.v.t. | 20.000 |
| &bull;Scope-kaarten | N.v.t. | N.v.t. | 20.000 |
| &bull;Repositories per scope map | N.v.t. | N.v.t. | 500 |


<sup>1.</sup> De opgegeven opslaglimieten zijn de hoeveelheid *opgenomen* opslag voor elke laag. Er wordt een extra dagtarief per GiB in rekening gebracht voor beeldopslag boven deze limieten. Zie Prijzen voor [Azure Container Registry voor][pricing]tariefinformatie.

<sup>2</sup>*ReadOps*, *WriteOps*en *Bandbreedte* zijn minimale schattingen. Azure Container Registry streeft ernaar de prestaties te verbeteren zoals het gebruik vereist.

<sup>3.</sup> Een [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) vertaalt naar meerdere leesbewerkingen op basis van het aantal lagen in de afbeelding, plus het manifest ophalen.

<sup>4.</sup> Een [dockerpush](https://docs.docker.com/registry/spec/api/#pushing-an-image) vertaalt zich naar meerdere schrijfbewerkingen, op basis van het aantal lagen dat moet worden gepusht. A `docker push` bevat *ReadOps* om een manifest voor een bestaande afbeelding op te halen.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md