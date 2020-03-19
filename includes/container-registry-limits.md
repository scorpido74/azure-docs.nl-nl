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
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117135"
---
| Resource | Basic | Standard | Premium |
|---|---|---|---|
| Opslag<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maximale grootte van de installatiekopielaag | 200 GiB | 200 GiB | 200 GiB |
| ReadOps per minuut<sup>2, 3</sup> | 1000 | 3000 | 10.000 |
| WriteOps per minuut<sup>2, 4</sup> | 100 | 500 | 2000 |
| Downloadbandbreedte MBps<sup>2</sup> | 30 | 60 | 100 |
| Uploadbandbreedte MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| Geo-replicatie | N.v.t. | N.v.t. | [Ondersteund][geo-replication] |
| Inhoud vertrouwen | N.v.t. | N.v.t. | [Ondersteund][content-trust] |
| Toegang tot virtueel netwerk | N.v.t. | N.v.t. | [Preview][vnet] |
| Integratie van persoonlijke koppelingen | N.v.t. | N.v.t. | [Preview][plink] |
| Door klant beheerde sleutels | N.v.t. | N.v.t. | [Preview][cmk] |
| Machtigingen voor opslagplaatsen | N.v.t. | N.v.t. | [Preview][token]|
| &bull; Tokens | N.v.t. | N.v.t. | 20.000 |
| &bull; Bereiktoewijzingen | N.v.t. | N.v.t. | 20.000 |
| &bull; Opslagplaatsen per bereiktoewijzing | N.v.t. | N.v.t. | 500 |


<sup>1</sup>De opgegeven opslaglimieten zijn de hoeveelheid opslag die voor elke laag is *inbegrepen*. Boven deze limieten wordt voor opslag van installatiekopieën een extra dagelijks tarief per GiB in rekening gebracht. Zie voor meer tariefinformatie [Prijzen voor Azure Container Registry][pricing].

<sup>2</sup>*ReadOps*, *WriteOps* en *bandbreedte* zijn minimale schattingen. Azure Container Registry streeft ernaar de prestaties te verbeteren op basis van de gebruiksvereisten.

<sup>3</sup>Een [Docker-pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) bestaat uit meerdere leesbewerkingen, afhankelijk van het aantal lagen in de installatiekopie, plus het ophalen van het manifest.

<sup>4</sup>Een [Docker-push](https://docs.docker.com/registry/spec/api/#pushing-an-image) bestaat uit meerdere schrijfbewerkingen, afhankelijk van het aantal lagen dat moet worden gepusht. Een `docker push` omvat de *ReadOps* om een manifest voor een bestaande installatiekopie op te halen.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md