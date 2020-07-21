---
title: Include-bestand
description: bestand opnemen
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 06/18/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3f68ca0fc577e6cf3f896ede0418f11f59756701
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512607"
---
| Resource | Basic | Standard | Premium |
|---|---|---|---|
| Inbegrepen opslag<sup>1</sup> (GIB) | 10 | 100 | 500 |
| Opslag limiet (TiB) | 20| 20 | 20 |
| Maximale grootte van de afbeelding slaag (GiB) | 200 | 200 | 200 |
| ReadOps per minuut<sup>2, 3</sup> | 1000 | 3000 | 10.000 |
| WriteOps per minuut<sup>2, 4</sup> | 100 | 500 | 2.000 |
| Down load bandbreedte MBps<sup>2</sup> | 30 | 60 | 100 |
| Upload bandbreedte MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| Geo-replicatie | N.v.t. | N.v.t. | [Ondersteund][geo-replication] |
| Inhoud vertrouwen | N.v.t. | N.v.t. | [Ondersteund][content-trust] |
| Persoonlijke koppeling met persoonlijke eind punten | N.v.t. | N.v.t. | [Ondersteund][plink] |
| &bull;Privé-eind punten | N.v.t. | N.v.t. | 10 |
| Toegang tot de service Endpoint VNet | N.v.t. | N.v.t. | [Preview][vnet] |
| Door klant beheerde sleutels | N.v.t. | N.v.t. | [Ondersteund][cmk] |
| Machtigingen voor opslag plaatsen-scoped | N.v.t. | N.v.t. | [Preview][token]|
| &bull;Tokens | N.v.t. | N.v.t. | 20.000 |
| &bull;Scope toewijzingen | N.v.t. | N.v.t. | 20.000 |
| &bull;Opslag plaatsen per scope toewijzing | N.v.t. | N.v.t. | 500 |


<sup>1</sup> opslag inbegrepen in het dagelijkse tempo van elke laag. Voor extra opslag wordt een extra dagelijks tarief per GiB in rekening gebracht, tot aan de opslag limiet. Zie voor informatie over de rente [Azure container Registry prijzen][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*en *band breedte* zijn minimale schattingen. Azure Container Registry streeft naar het verbeteren van de prestaties als vereist gebruik.

<sup>3</sup> Een [docker-pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) -bewerking wordt omgezet naar meerdere Lees bewerkingen op basis van het aantal lagen in de afbeelding, plus het manifest dat wordt opgehaald.

<sup>4</sup> Een [docker-push](https://docs.docker.com/registry/spec/api/#pushing-an-image) wordt omgezet naar meerdere schrijf bewerkingen, op basis van het aantal lagen dat moet worden gepusht. A `docker push` bevat *ReadOps* voor het ophalen van een manifest voor een bestaande installatie kopie.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md