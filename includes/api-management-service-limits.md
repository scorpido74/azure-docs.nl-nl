---
title: bestand opnemen
description: bestand opnemen
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 01/10/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 5608683578a796ae0fffff7544a749043e8698fd
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656175"
---
| Resource | Limiet |
| ---------------------------------------------------------------------- | -------------------------- |
| Maximum aantal schaaleenheden | 10 per regio<sup>1</sup> |
| Cachegrootte | 5 GiB per eenheid<sup>2</sup> |
| Gelijktijdige back-endverbindingen<sup>3</sup> per HTTP-autoriteit | 2.048 per eenheid<sup>4</sup> |
| Maximale responstijd in cache | 2 MiB |
| Maximale beleidsdocumentgrootte | 256 KiB<sup>5</sup> |
| Maximale aangepaste gatewaydomeinen per serviceinstantie<sup>6</sup> | 20 |
| Maximum aantal CA-certificaten per service-instantie | 10 |
| Maximaal aantal service-exemplaren per abonnement<sup>7</sup> | 20 |
| Maximum aantal abonnementen per serviceinstantie<sup>7</sup> | 500 |
| Maximum aantal clientcertificaten per service-instantie<sup>7</sup> | 50 |
| Maximum aantal API's per serviceinstantie<sup>7</sup> | 50 |
| Maximum aantal API-bewerkingen per serviceinstantie<sup>7</sup> | 1000 |
| Maximale totale aanvraagduur<sup>7</sup> | 30 seconden |
| Maximale gebufferde laadvermogen grootte<sup>7</sup> | 2 MiB |
| Maximum aanvraag URL-grootte<sup>8</sup> | 4096 bytes |

<sup>1.</sup> Schaallimieten zijn afhankelijk van de prijscategorie. Zie [API Management-prijzen](https://azure.microsoft.com/pricing/details/api-management/)voor de prijsniveaus en de schalingslimieten daarvan.<br/>
<sup>2.</sup> De grootte van de cache per eenheid is afhankelijk van de prijscategorie. Zie [API Management-prijzen](https://azure.microsoft.com/pricing/details/api-management/)voor de prijsniveaus en de schalingslimieten daarvan.<br/>
<sup>3.</sup> Verbindingen worden samengevoegd en hergebruikt, tenzij deze expliciet door de back-end worden gesloten.<br/>
<sup>4.</sup> Deze limiet is per eenheid van de laagBasis-, Standaard- en Premium-lagen. De projectlaag is beperkt tot 1.024. Deze limiet is niet van toepassing op de laag Verbruik.<br/>
<sup>5.</sup> Deze limiet is van toepassing op de laagBasis-, Standaard- en Premium-niveaus. In de categorie Verbruik is de beleidsdocumentgrootte beperkt tot 16 KiB.<br/>
<sup>6.</sup> Deze resource is alleen beschikbaar in de laag Premium.<br/>
<sup>7.</sup> Deze resource is alleen van toepassing op de laag Verbruik.<br/>
<sup>8.</sup> Geldt alleen voor de verbruikslaag. Bevat een lange queryreeks van maximaal 2048 bytes.<br/>
