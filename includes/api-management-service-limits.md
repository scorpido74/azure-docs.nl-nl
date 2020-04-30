---
title: bestand opnemen
description: bestand opnemen
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 04/14/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 072f13f5a0884cf95fe760e17ff0d770111f4da0
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204580"
---
| Resource | Limiet |
| ---------------------------------------------------------------------- | -------------------------- |
| Maximum aantal schaal eenheden | 10 per regio<sup>1</sup> |
| Cachegrootte | 5 GiB per eenheid<sup>2</sup> |
| Gelijktijdige back-end-verbindingen<sup>3</sup> per http-instantie | 2.048 per eenheid<sup>4</sup> |
| Maximum grootte van antwoord in cache | 2 MiB |
| Maximale grootte van beleids document | 256 KiB<sup>5</sup> |
| Maximum aantal aangepaste gateway domeinen per service-exemplaar<sup>6</sup> | 20 |
| Maximum aantal CA-certificaten per service-exemplaar<sup>7</sup> | 10 |
| Maximum aantal service-exemplaren per abonnement<sup>8</sup> | 20 |
| Maximum aantal abonnementen per service-exemplaar<sup>8</sup> | 500 |
| Maximum aantal client certificaten per service-exemplaar<sup>8</sup> | 50 |
| Maximum aantal Api's per service-exemplaar<sup>8</sup> | 50 |
| Maximum aantal API-bewerkingen per service-exemplaar<sup>8</sup> | 1000 |
| Maximum duur van de totale aanvraag<sup>8</sup> | 30 seconden |
| Maximale grootte van de gebufferde nettolading<sup>8</sup> | 2 MiB |
| Maximum grootte van de aanvraag-URL<sup>9</sup> | 4096 bytes |
| Maximum aantal zelf-hostende gateways<sup>10</sup> | 25 |

<sup>1</sup> De schaal limieten zijn afhankelijk van de prijs categorie. Zie [API Management-prijzen](https://azure.microsoft.com/pricing/details/api-management/)voor meer informatie over de prijs categorieën en hun schaal limieten.<br/>
<sup>2</sup> De grootte van de cache per eenheid is afhankelijk van de prijs categorie. Zie [API Management prijzen](https://azure.microsoft.com/pricing/details/api-management/)voor een overzicht van de prijs categorieën en hun schaal limieten.<br/>
<sup>3</sup> Verbindingen worden gegroepeerd en opnieuw gebruikt, tenzij dit expliciet is gesloten door de back-end.<br/>
<sup>4</sup> Deze limiet geldt per eenheid van de lagen Basic, Standard en Premium. De Developer-laag is beperkt tot 1.024. Deze limiet is niet van toepassing op de laag verbruik.<br/>
<sup>5</sup> Deze limiet is van toepassing op de lagen Basic, Standard en Premium. In de laag verbruik is de document grootte van het beleid beperkt tot 4 KiB.<br/>
<sup>6</sup> Meerdere aangepaste domeinen worden alleen ondersteund in de lagen ontwikkel aars en Premium.<br/>
<sup>7</sup> CA-certificaten worden niet ondersteund in de laag verbruik.<br/>
<sup>8</sup> Deze resource is alleen van toepassing op de laag verbruik.<br/>
<sup>9</sup> Is alleen van toepassing op de laag verbruik. Bevat een reeks van Maxi maal 2048 bytes lang.<br/>
<sup>10</sup> Zelf-hostende gateways worden alleen ondersteund in de lagen ontwikkel aars en Premium. De limiet is van toepassing op het aantal [zelf-hostende gateway bronnen](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway). Neem contact op met de [ondersteuning](https://azure.microsoft.com/support/options/)om deze limiet te verhogen. Houd er rekening mee dat het aantal knoop punten (of replica's) dat is gekoppeld aan een zelf-hostende gateway resource onbeperkt is in de Premium-laag en is afgesteld op één knoop punt in de Developer-laag.
