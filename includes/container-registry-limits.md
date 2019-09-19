---
title: bestand opnemen
description: bestand opnemen
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/14/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ee8ff3529524a63ca2e54a64327570197f363538
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "67176399"
---
| Resource | Basic | Standard | Premium |
|---|---|---|---|
| Opslag<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maximale grootte van de afbeelding slaag | 200 GiB | 200 GiB | 200 GiB |
| ReadOps per minuut<sup>2, 3</sup> | 1000 | 3,000 | 10.000 |
| WriteOps per minuut<sup>2, 4</sup> | 100 | 500 | 2,000 |
| Down load bandbreedte MBps<sup>2</sup> | 30 | 60 | 100 |
| Upload bandbreedte MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Geo-replicatie | N/A | N/A | [Ondersteund][geo-replication] |
| Inhoud vertrouwen | N/A | N/A | [Ondersteund][content-trust] |

<sup>1</sup> De opgegeven opslag limieten zijn de hoeveelheid *opgenomen* opslag ruimte voor elke laag. Boven deze limieten wordt een extra dagelijks tarief per GiB in rekening gebracht voor afbeeldings opslag. Zie voor informatie over de rente [Azure container Registry prijzen][pricing].

<sup>2</sup> *ReadOps*, *WriteOps*en *band breedte* zijn minimale schattingen. Azure Container Registry streeft naar het verbeteren van de prestaties als vereist gebruik.

<sup>3</sup> Een [docker-pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) -bewerking wordt omgezet naar meerdere Lees bewerkingen op basis van het aantal lagen in de afbeelding, plus het manifest dat wordt opgehaald.

<sup>4</sup> Een [docker-push](https://docs.docker.com/registry/spec/api/#pushing-an-image) wordt omgezet naar meerdere schrijf bewerkingen, op basis van het aantal lagen dat moet worden gepusht. A `docker push` bevat *ReadOps* voor het ophalen van een manifest voor een bestaande installatie kopie.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
