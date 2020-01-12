---
ms.openlocfilehash: ee44cd4ee5ec6fedfec46cbbb68b0c30118a4b9f
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904456"
---
---

Titel: include file description: include file services: API-Management Author: vladvino

MS. AssetID: 1b813833-39c8-46be-8666-fd0960cfbf04 MS. service: API-Management MS. topic: include MS. date: 01/10/2020 MS. Author: vlvinogr MS. Custom: include file
---| Bron | Limiet |
| ---------------------------------------------------------------------- | -------------------------- |
| Maximum aantal schaal eenheden | 10 per regio<sup>1</sup> |
| Cachegrootte | 5 GiB per eenheid<sup>2</sup> |
| Gelijktijdige back-end-verbindingen<sup>3</sup> per http-instantie | 2\.048 per eenheid<sup>4</sup> |
| Maximum grootte van antwoord in cache | 2 MiB |
| Maximale grootte van beleids document | 256 KiB<sup>5</sup> |
| Maximum aantal aangepaste gateway domeinen per service-exemplaar<sup>6</sup> | 20 |
| Maximum aantal CA-certificaten per service-exemplaar | 10 |
| Maximum aantal service-exemplaren per abonnement<sup>7</sup> | 20 |
| Maximum aantal abonnementen per service-exemplaar<sup>7</sup> | 500 |
| Maximum aantal client certificaten per service-exemplaar<sup>7</sup> | 50 |
| Maximum aantal Api's per service-exemplaar<sup>7</sup> | 50 |
| Maximum aantal API-bewerkingen per service-exemplaar<sup>7</sup> | 1000 |
| Maximum totale aanvraag duur<sup>7</sup> | 30 seconden |
| Maximale grootte van de gebufferde nettolading<sup>7</sup> | 2 MiB |
| Maximum grootte van de aanvraag-URL<sup>8</sup> | 4096 bytes |

<sup>1</sup> De schaal limieten zijn afhankelijk van de prijs categorie. Zie [API Management prijzen](https://azure.microsoft.com/pricing/details/api-management/)voor een overzicht van de prijs categorieën en hun schaal limieten.<br/>
<sup>2</sup> De grootte van de cache per eenheid is afhankelijk van de prijs categorie. Zie [API Management prijzen](https://azure.microsoft.com/pricing/details/api-management/)voor een overzicht van de prijs categorieën en hun schaal limieten.<br/>
<sup>3</sup> Verbindingen worden gegroepeerd en opnieuw gebruikt, tenzij dit expliciet is gesloten door de back-end.<br/>
<sup>4</sup> Deze limiet geldt per eenheid van de lagen Basic, Standard en Premium. De Developer-laag is beperkt tot 1.024. Deze limiet is niet van toepassing op de laag verbruik.<br/>
<sup>5</sup> Deze limiet is van toepassing op de lagen Basic, Standard en Premium. In de laag verbruik is de document grootte van het beleid beperkt tot 4 KiB.<br/>
<sup>6</sup> Deze resource is alleen beschikbaar in de laag Premium.<br/>
<sup>7</sup> Deze resource is alleen van toepassing op de laag verbruik.<br/>
<sup>8</sup> Is alleen van toepassing op de laag verbruik. Bevat een reeks van Maxi maal 2048 bytes lang.<br/>
