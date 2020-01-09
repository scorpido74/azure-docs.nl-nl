---
title: bestand opnemen
description: bestand opnemen
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 11/05/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6707e844948ac76d4cec29faf69d80b3c9cb3c0f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392412"
---
| Bron | Basic | Standard | Premium |
|---|---|---|---|
| Opslag<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maximale grootte van de afbeelding slaag | 200 GiB | 200 GiB | 200 GiB |
| ReadOps per minuut<sup>2, 3</sup> | 1000 | 3\.000 | 10.000 |
| WriteOps per minuut<sup>2, 4</sup> | 100 | 500 | 2,000 |
| Down load bandbreedte MBps<sup>2</sup> | 30 | 60 | 100 |
| Upload bandbreedte MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| Geo-replicatie | N/A | N/A | [Ondersteund][geo-replication] |
| Inhoud vertrouwen | N/A | N/A | [Ondersteund][content-trust] |
| Toegang tot virtueel netwerk | N/A | N/A | [Preview][vnet] |
| Machtigingen voor opslag plaatsen-scoped | N/A | N/A | [Preview][token]|
| &bull;-tokens | N/A | N/A | 20,000 |
| &bull; Scope Maps | N/A | N/A | 20,000 |
| &bull; opslag plaatsen per scope toewijzing | N/A | N/A | 500 |


<sup>1</sup> De opgegeven opslag limieten zijn de hoeveelheid *opgenomen* opslag ruimte voor elke laag. Boven deze limieten wordt een extra dagelijks tarief per GiB in rekening gebracht voor afbeeldings opslag. Zie voor informatie over de rente [Azure container Registry prijzen][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*en *band breedte* zijn minimale schattingen. Azure Container Registry streeft naar het verbeteren van de prestaties als vereist gebruik.

<sup>3</sup> Een [docker-pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) -bewerking wordt omgezet naar meerdere Lees bewerkingen op basis van het aantal lagen in de afbeelding, plus het manifest dat wordt opgehaald.

<sup>4</sup> Een [docker-push](https://docs.docker.com/registry/spec/api/#pushing-an-image) wordt omgezet naar meerdere schrijf bewerkingen, op basis van het aantal lagen dat moet worden gepusht. Een `docker push` bevat *ReadOps* voor het ophalen van een manifest voor een bestaande installatie kopie.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md