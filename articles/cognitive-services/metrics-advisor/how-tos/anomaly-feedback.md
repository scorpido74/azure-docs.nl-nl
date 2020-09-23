---
title: Afwijkende feedback geven aan de service voor metrische gegevens Advisor
titleSuffix: Azure Cognitive Services
description: Meer informatie over het verzenden van feedback op afwijkingen die worden gevonden door uw metrische Advisor-instantie en het afstemmen van de resultaten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 50d422edf1a4b45132d0b86eac9d4947cef5e5bf
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935431"
---
# <a name="adjust-anomaly-detection-using-feedback"></a>Anomaliedetectie aanpassen met feedback

Als u niet tevreden bent met enkele van de resultaten van de detectie van de meters, kunt u hand matig feedback toevoegen die van invloed is op het model dat op uw gegevens wordt toegepast. 

Gebruik de knoppen in de rechter bovenhoek van de pagina om de modus feedback aantekening in te scha kelen.

:::image type="content" source="../media/feedback/annotation-mode.png" alt-text="Modus voor feedback aantekening.":::

Nadat de modus voor de aantekening van de feedback is geactiveerd, kunt u feedback geven voor één punt of meerdere doorlopende punten.

## <a name="give-feedback-for-one-point"></a>Feedback geven voor één punt 

Wanneer de modus feedback aantekening is geactiveerd, klikt u op een punt om een **feedback venster toevoegen** te openen. U kunt het type feedback instellen dat u wilt Toep assen. Deze feedback wordt opgenomen in de detectie voor toekomstige punten.  

* Selecteer **afwijkingen** als u denkt dat het punt onjuist is gelabeld door de metrische monitor. U kunt opgeven of een punt moet of niet een afwijkend moeten zijn. 
* Selecteer **ChangePoint** als u denkt dat het punt het begin van een trend wijziging aangeeft.
* Selecteer **periode** om de seizoensgebondenheid aan te geven. Met metrische monitor kunnen automatisch intervallen van seizoensgebondenheid worden gedetecteerd of kunt u dit hand matig opgeven. 

Overweeg om op hetzelfde moment een opmerking in het tekstvak **Opmerking** te laten staan en klik op **Opslaan** om uw feedback op te slaan.

:::image type="content" source="../media/feedback/feedback-menu.png" alt-text="Feedback menu.":::

## <a name="give-feedback-for-multiple-continuous-points"></a>Feedback geven voor meerdere doorlopende punten

U kunt feedback geven voor meerdere doorlopende punten tegelijk door te klikken op omlaag en de muis aanwijzer te slepen op de punten waar u aantekeningen mee wilt maken. U ziet hetzelfde feedback menu als hierboven. Wanneer u op **Opslaan**klikt, wordt dezelfde feedback toegepast op alle gekozen punten.

:::image type="content" source="../media/feedback/continuous-points.png" alt-text="Meerdere punten kiezen":::

## <a name="how-to-view-my-feedback"></a>Mijn feedback weer geven

Beweeg de muis aanwijzer over het punt om te zien of de afwijkings detectie van een punt is gewijzigd. De knop Info wordt **beïnvloed door feedback: True** als de detectie is gewijzigd. Als deze **Onwaar**wordt weer gegeven, is de feedback berekening op het punt uitgevoerd, maar het resultaat van de afwijkings detectie is niet gewijzigd.

:::image type="content" source="../media/feedback/affected-point.png" alt-text="Punt beïnvloed door feedback":::

## <a name="when-should-i-annotate-an-anomaly-as-normal"></a>Wanneer moet ik een afwijkend als ' normaal ' aanwijzen

Er zijn veel redenen denkbaar waarom het een onregelmatige waarschuwing zou zijn. Als een van de volgende scenario's van toepassing is, kunt u overwegen om de volgende functies van de functie voor metrische gegevens Advisor te gebruiken:


|Scenario  |Aanbeveling |
|---------|---------|
|De afwijkende oorzaak wordt veroorzaakt door een wijziging in de gegevens bron, bijvoorbeeld een systeem wijziging.     | Maak geen aantekeningen op deze afwijkingen als dit scenario niet naar verwachting regel matig opnieuw optreedt.        |
|De afwijkende oorzaak is de feestdag.     | Gebruik [vooraf ingestelde gebeurtenissen](configure-metrics.md#preset-events) om de afwijkings detectie op bepaalde tijdstippen te markeren.       |
|Er is een regulier patroon voor het detecteren van afwijkingen (bijvoorbeeld in het weekend) en ze moeten geen afwijkingen hebben.      |Gebruik de feedback functie of de vooraf ingestelde gebeurtenissen.        |

## <a name="next-steps"></a>Volgende stappen
- [Een incident diagnosticeren](diagnose-incident.md).
- [Metrische gegevens configureren en detectieconfiguratie afstemmen](configure-metrics.md)
- [Waarschuwingen configureren en meldingen ontvangen met behulp van een hook](../how-tos/alerts.md)