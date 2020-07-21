---
author: baanders
description: include-bestand voor Azure Digital Apparaatdubbels-limieten
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 21d910fb0e0992b35aa19ce65fc216734e30265c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515856"
---
### <a name="functional-limits"></a>Functionele limieten

De volgende tabel bevat de functionele limieten van Azure Digital Apparaatdubbels in de huidige preview-versie.

| Gebied | Mogelijkheid | Standaardlimiet | Aanpasbaar? |
| --- | --- | --- | --- |
| Azure-resource | Aantal Azure Digital Apparaatdubbels-instanties in een regio, per abonnement | 10 | Ja |
| Digitale apparaatdubbels | Aantal apparaatdubbels in een Azure Digital Apparaatdubbels-instantie | 200.000 | Ja |
| Routering | Aantal eind punten voor één Azure Digital Apparaatdubbels-exemplaar | 6 | Nee |
| Routering | Aantal routes voor één Azure Digital Apparaatdubbels-exemplaar | 6 | Ja |
| Modellen | Aantal modellen binnen één Azure Digital Apparaatdubbels-exemplaar | 10.000 | Ja |
| Modellen | Aantal modellen dat kan worden geüpload in één API-aanroep | 250 | Nee |
| Modellen | Aantal geretourneerde items op één pagina | 100 | Nee |
| Query’s uitvoeren | Aantal geretourneerde items op één pagina | 100 | Nee |
| Query’s uitvoeren | Aantal `AND`  /  `OR` expressies in een query | 50 | Ja |
| Query’s uitvoeren | Aantal matrix items in een `IN`  /  `NOT IN` component | 50 | Ja |
| Query’s uitvoeren | Aantal tekens in een query | 8,000 | Ja |
| Query’s uitvoeren | Aantal `JOINS` in een query | 1 | Ja |

### <a name="rate-limits"></a>Frequentielimieten

In deze tabel worden de frequentie limieten van verschillende Api's weer gegeven.

| API | Mogelijkheid | Standaardlimiet | Aanpasbaar? |
| --- | --- | --- | --- |
| Modellen-API | Aantal aanvragen per seconde | 100 | Ja |
| Digital Apparaatdubbels-API | Aantal aanvragen per seconde | 1000 | Ja |
| Query-API | Aantal aanvragen per seconde | 500 | Ja |
| Query-API | Query eenheden per seconde | 4000 | Ja |
| API voor gebeurtenis routes | Aantal aanvragen per seconde | 100 | Ja |

### <a name="other-limits"></a>Andere limieten

Limieten voor gegevens typen en velden in DTDL documenten voor Azure Digital Apparaatdubbels-modellen vindt u in de documentatie van de bijbehorende specificaties in GitHub: [Digital Apparaatdubbels Definition Language (DTDL)-versie 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Query latentie Details en andere richt lijnen voor het schrijven van query's tijdens de preview vindt [*u in de instructies: Query's uitvoeren op de dubbele grafiek*](../articles/digital-twins/how-to-query-graph.md).