---
author: baanders
description: include-bestand voor Azure Digital Apparaatdubbels-limieten
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 76ccd6f42f2cfd83512a2e817e8c2ac9aa61a28b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391474"
---
### <a name="functional-limits"></a>Functionele limieten

De volgende tabel bevat de functionele limieten van Azure Digital Apparaatdubbels in de huidige preview-versie.

| Onderwerp | Mogelijkheid | Standaardlimiet | Aanpasbaar? |
| --- | --- | --- | --- |
| Azure-resource | Aantal Azure Digital Apparaatdubbels-instanties in een regio, per abonnement | 10 | Yes |
| Digitale apparaatdubbels | Aantal apparaatdubbels in een Azure Digital Apparaatdubbels-instantie | 200.000 | Yes |
| Routering | Aantal eind punten voor één Azure Digital Apparaatdubbels-exemplaar | 6 | No |
| Routering | Aantal routes voor één Azure Digital Apparaatdubbels-exemplaar | 6 | Yes |
| Modellen | Aantal modellen binnen één Azure Digital Apparaatdubbels-exemplaar | 10.000 | Yes |
| Modellen | Aantal modellen dat kan worden geüpload in één API-aanroep | 250 | No |
| Modellen | Aantal geretourneerde items op één pagina | 100 | No |
| Query’s uitvoeren | Aantal geretourneerde items op één pagina | 100 | No |
| Query’s uitvoeren | Aantal `AND`  /  `OR` expressies in een query | 50 | Ja |
| Query’s uitvoeren | Aantal matrix items in een `IN`  /  `NOT IN` component | 50 | Ja |
| Query’s uitvoeren | Aantal tekens in een query | 8,000 | Ja |
| Query’s uitvoeren | Aantal `JOINS` in een query | 1 | Yes |

### <a name="rate-limits"></a>Frequentielimieten

In deze tabel worden de frequentie limieten van verschillende Api's weer gegeven.

| API | Mogelijkheid | Standaardlimiet | Aanpasbaar? |
| --- | --- | --- | --- |
| Modellen-API | Aantal aanvragen per seconde | 100 | Yes |
| Digital Apparaatdubbels-API | Aantal aanvragen per seconde | 1000 | Yes |
| Query-API | Aantal aanvragen per seconde | 500 | Yes |
| Query-API | Query eenheden per seconde | 4000 | Yes |
| API voor gebeurtenis routes | Aantal aanvragen per seconde | 100 | Yes |

### <a name="other-limits"></a>Andere limieten

Limieten voor gegevens typen en velden in DTDL documenten voor Azure Digital Apparaatdubbels-modellen vindt u in de documentatie van de bijbehorende specificaties in GitHub: [Digital Apparaatdubbels Definition Language (DTDL)-versie 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Query latentie Details en andere richt lijnen voor het schrijven van query's tijdens de preview vindt [u in de instructies: Query's uitvoeren op de dubbele grafiek](../articles/digital-twins/how-to-query-graph.md).