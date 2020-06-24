---
title: Servicebeperkingen van de openbare preview
titleSuffix: Azure Digital Twins
description: Grafiek met de limieten van de Azure Digital Apparaatdubbels-service tijdens de open bare preview.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: article
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 7847964d77e5dc09d2e09f207c47f9504c48e1db
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84729197"
---
# <a name="service-limits-in-public-preview"></a>Servicebeperkingen van de openbare preview

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Dit zijn de service limieten van Azure Digital Apparaatdubbels tijdens de open bare preview-versie.

> [!NOTE]
> Sommige services hebben aanpas bare limieten, die in de onderstaande tabellen worden weer gegeven met de kolom *aanpasbaar?* . Wanneer de limiet kan worden aangepast, is de *aanpas bare?* -waarde *Ja*.
>
> Als uw bedrijf een aanpas bare limiet of een quotum moet verhogen dat hoger is dan de standaard limiet, kunt u aanvullende resources aanvragen door [een ondersteunings ticket te openen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="functional-limits"></a>Functionele limieten

De volgende tabel bevat de functionele limieten van Azure Digital Apparaatdubbels in de huidige preview-versie.

| Gebied | Mogelijkheid | Limiet | Aanpasbaar? |
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

## <a name="rate-limits"></a>Frequentie limieten

In deze tabel worden de frequentie limieten van verschillende Api's weer gegeven.

| API | Mogelijkheid | Limiet | Aanpasbaar? |
| --- | --- | --- | --- |
| Modellen-API | Aantal aanvragen per seconde | 100 | Yes |
| Digital Apparaatdubbels-API | Aantal aanvragen per seconde | 1000 | Yes |
| Query-API | Aantal aanvragen per seconde | 500 | Yes |
| Query-API | Query eenheden per seconde | 4000 | Yes |
| API voor gebeurtenis routes | Aantal aanvragen per seconde | 100 | Yes |

## <a name="other-limits"></a>Andere limieten

Limieten voor gegevens typen en velden in DTDL documenten voor Azure Digital Apparaatdubbels-modellen vindt u in de documentatie van de bijbehorende specificaties in GitHub: [Digital Apparaatdubbels Definition Language (DTDL)-versie 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Query latentie Details en andere richt lijnen voor het schrijven van query's tijdens de preview vindt [u in de instructies: Query's uitvoeren op de dubbele grafiek](how-to-query-graph.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de huidige preview-versie van Azure Digital Apparaatdubbels vindt u in het overzicht van de service:
* [Overzicht: wat is Azure Digital Apparaatdubbels?](overview.md)
