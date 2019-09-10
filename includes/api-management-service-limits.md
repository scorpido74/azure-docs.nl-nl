---
title: bestand opnemen
description: bestand opnemen
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: fc945a7e9389c8aec48a6a1dba969fbf92002d3a
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "67176438"
---
| Resource | Limiet |
| --- | --- |
| Maximum aantal schaal eenheden | 10 per regio<sup>1</sup> |
| Cachegrootte | 5 GB per eenheid<sup>2</sup> |
| Gelijktijdige back-end-verbindingen<sup>3</sup> per http-instantie | 2\.048 per eenheid<sup>4</sup> |
| Maximum grootte van antwoord in cache | 2 MB |
| Maximale grootte van beleids document | 256 KB<sup>5</sup> | 
| Maximum aantal aangepaste gateway domeinen per service-exemplaar<sup>6</sup> | 20 |
| Maximum aantal CA-certificaten per service-exemplaar | 10 | 
| Maximum aantal service-exemplaren per abonnement<sup>7</sup> | 20 | 
| Maximum aantal abonnementen per service-exemplaar<sup>7</sup> | 500 |
| Maximum aantal client certificaten per service-exemplaar<sup>7</sup> | 50 | 
| Maximum aantal Api's per service-exemplaar<sup>7</sup> | 50 | 
| Maximum aantal API-bewerkingen per service-exemplaar<sup>7</sup> | 1000 | 
| Maximum totale aanvraag duur<sup>7</sup> | 30 seconden | 
| Maximale grootte van de gebufferde nettolading<sup>7</sup> | 2 MB | 


<sup>1</sup> De schaal limieten zijn afhankelijk van de prijs categorie. Zie [API Management prijzen](https://azure.microsoft.com/pricing/details/api-management/)voor een overzicht van de prijs categorieën en hun schaal limieten.<br/>
<sup>2</sup> De grootte van de cache per eenheid is afhankelijk van de prijs categorie. Zie [API Management prijzen](https://azure.microsoft.com/pricing/details/api-management/)voor een overzicht van de prijs categorieën en hun schaal limieten.<br/>
<sup>3</sup> Verbindingen worden gegroepeerd en opnieuw gebruikt, tenzij dit expliciet is gesloten door de back-end.<br/>
<sup>4</sup> Deze limiet geldt per eenheid van de lagen Basic, Standard en Premium. De Developer-laag is beperkt tot 1.024. Deze limiet is niet van toepassing op de laag verbruik.<br/> 
<sup>5</sup> Deze limiet is van toepassing op de lagen Basic, Standard en Premium. In de laag verbruik is de document grootte van het beleid beperkt tot 4 KB.<br/>
<sup>6</sup> Deze resource is alleen beschikbaar in de laag Premium.<br/>
<sup>7</sup> Deze resource is alleen van toepassing op de laag verbruik.<br/>



