---
title: Algemene benoemde entiteiten
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 630f04bf2cc9e7de6331f9d25754a20fe2327d76
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779803"
---
De volgende entiteits categorieën worden geretourneerd bij het verzenden van aanvragen naar het `/entities/recognition/general` eind punt.

| Categorie   | Subcategorie | Beschrijving                          | Model versie starten                                                    | Opmerkingen |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| Persoon     | N.v.t.         | Namen van personen.  | `2019-10-01`  | Wordt ook geretourneerd door NER v 2.1 |
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
| Vaardigheid | N.v.t. | Een mogelijkheid, vaardigheid of deskundigheid. | `2020-02-01` |  |
| Adres | N.v.t. | Volledige mailing adressen.  | `2020-04-01` |  |
| PhoneNumber | N.v.t. | Telefoon nummers (alleen telefoon nummers voor VS en EU). | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Email | N.v.t. | E-mail adressen. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| URL | N.v.t. | Url's naar websites. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1  |
| IP | N.v.t. | IP-adressen van het netwerk. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| DateTime | N.v.t. | Datums en tijden van de dag. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 | 
| Datum/Tijd | Datum | Kalender datums. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Datum/Tijd | Tijd | Tijdstippen van de dag | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Datum/Tijd | DateRange | Datumbereiken. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Datum/Tijd | TimeRange | Tijds bereik. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Datum/Tijd | Duur | Duur. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Datum/Tijd | Ingesteld | Set, herhaalde tijden. |  `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Hoeveelheid | N.v.t. | Cijfers en numerieke aantallen. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1  |
| Hoeveelheid | Getal | Rijnummers. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Hoeveelheid | Percentage | Percentages.| `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Hoeveelheid | Rangtelwoord | Ordinale getallen. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Hoeveelheid | Leeftijd | Leeftijd. | `2019-10-01` |  Wordt ook geretourneerd door NER v 2.1 |
| Hoeveelheid | Valuta | Gelden. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Hoeveelheid | Dimensie | Dimensies en metingen. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
| Hoeveelheid | Temperatuur | Waarbij. | `2019-10-01` | Wordt ook geretourneerd door NER v 2.1 |
