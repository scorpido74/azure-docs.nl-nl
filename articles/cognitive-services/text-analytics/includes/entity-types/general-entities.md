---
title: Algemene benoemde entiteiten
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 32e80c50ff6f543679852cbd7e5ce9bda92d01e1
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140916"
---
De volgende entiteits categorieën worden geretourneerd bij het verzenden van aanvragen naar het `/entities/recognition/general` eind punt.

| Categorie   | Subcategory | Beschrijving                          | Model versie starten                                                    | Opmerkingen |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| Person     | N.v.t.         | Namen van personen.  | `2019-10-01`  | Wordt ook geretourneerd door NER v 2.1 |
| PersonType | N.v.t.         | Taak typen of-rollen die door een persoon worden beheerd. | `2020-02-01` | |
|Locatie    | N.v.t.         | Natuurlijke en door de mens gemaakte bezienswaardigheden, structuren, geografische functies en geopolitieke entiteiten     |  `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
|Locatie     | Geopolitieke entiteit (GPE)        | Steden, landen/regio's, statussen.      | `2020-02-01` | |
|Locatie     | Verwant                       | Manmade-structuren. | `2020-04-01` | |
|Locatie     | Geografische       | Geografische en natuurlijke functies, zoals rivieren, oceanen en woestijns. |  `2020-04-01` | |
|Organisatie  | N.v.t. | Bedrijven, politieke groepen, muziek banden, sport clubs, overheids instanties en open bare organisaties.  | `2019-10-01` | Nationale en religions zijn niet opgenomen in dit entiteits type. Wordt ook geretourneerd door NER v 2.1 |
|Organisatie | Medisch | Medische bedrijven en groepen. | `2020-04-01` |  |
|Organisatie | Beurs | Voorraad wisselkoers groepen. | `2020-04-01` | |
| Organisatie | Sporten | Sport organisaties. | `2020-04-01` |  |
| Gebeurtenis  | N.v.t. | Historische, sociale en natuurlijke gebeurtenissen die optreden. | `2020-02-01` |  |
| Gebeurtenis  | Culturele | Culturele evenementen en feest dagen. | `2020-04-01` | |
| Gebeurtenis  | Schrift | Natuurlijk optredende gebeurtenissen. | `2020-04-01` |  |
| Gebeurtenis  | Sporten | Sport gebeurtenissen.  | `2020-04-01` | |
| Product | N.v.t. | Fysieke objecten van verschillende categorieën. | `2020-02-01` | |
| Product | Computing-producten | Computing-producten. |  `2020-02-01 ` | |
| Eigen | N.v.t. | Een mogelijkheid, vaardigheid of deskundigheid. | `2020-02-01` |  |
| Adres | N.v.t. | Volledige mailing adressen.  | `2020-04-01` |  |
| PhoneNumber | N.v.t. | Telefoon nummers (alleen telefoon nummers voor VS en EU). | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| E-mail | N.v.t. | E-mail adressen. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| URL | N.v.t. | Url's naar websites. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1  |
| IP | N.v.t. | IP-adressen van het netwerk. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| DateTime | N.v.t. | Datums en tijden van de dag. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 | 
| DateTime | Datum | Kalender datums. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| DateTime | Tijd | Tijdstippen van de dag | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| DateTime | DateRange | Datumbereiken. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| DateTime | Time Range | Tijds bereik. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| DateTime | Duur | Duur. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| DateTime | Instellen | Set, herhaalde tijden. |  `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Aantal | N.v.t. | Cijfers en numerieke aantallen. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1  |
| Aantal | Aantal | Rijnummers. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Aantal | Percentage | Percentages.| `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Aantal | Rang telwoord | Ordinale getallen. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Aantal | Leeftijd | Leeftijd. | `2019-10-01` |  Wordt ook geretourneerd door NER v 2.1 |
| Aantal | Valuta | Gelden. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Aantal | Dimensie | Dimensies en metingen. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Aantal | Temperatuur | Waarbij. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
