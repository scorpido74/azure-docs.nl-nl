---
title: Inleiding tot Azure Stream Analytics-venster functies
description: In dit artikel worden vier venster functies (tumblingvenstertriggers, verspringen, verschuiving, sessie) beschreven die worden gebruikt in Azure Stream Analytics taken.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: a0547243ddf114d5c9f7034f182a5e76d8c3e016
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369419"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Inleiding tot Stream Analytics-venster functies

In time-streaming scenario's is het uitvoeren van bewerkingen op de gegevens in tijdelijke Vensters een gemeen schappelijk patroon. Stream Analytics biedt ingebouwde ondersteuning voor windowing functies, zodat ontwikkelaars kunnen de auteur van complexe verwerking taken met een minimale inspanning.

Er zijn vier soorten tijdelijke Vensters waaruit u kunt kiezen: [**tumblingvenstertriggers**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics), [**verspringen**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics), [**schuiven**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)en [**sessie**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics) Vensters.  U gebruikt de venster functies in de [**Group by**](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) -component van de query syntaxis in uw stream Analytics-taken. U kunt ook gebeurtenissen met meerdere vensters samen voegen met behulp van de [functie **Windows ()** ](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics).

Alle uitvoer resultaten van de [venster](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) bewerkingen aan het **einde** van het venster. De uitvoer van het venster is één gebeurtenis op basis van de statistische functie die wordt gebruikt. De uitvoer gebeurtenis heeft het tijds tempel van het einde van het venster en alle venster functies worden gedefinieerd met een vaste lengte. 

![Concepten van Stream Analytics-venster functies](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Venster tumblingvenstertriggers
Functies van het tumblingvenstertriggers-venster worden gebruikt om een gegevens stroom te segmenteren in verschillende tijds segmenten en een functie uit te voeren, zoals in het onderstaande voor beeld. De belangrijkste onderscheidende factoren van een TumblingWindow zijn dat ze herhalend zijn, niet overlappend, en dat een gebeurtenis maar tot één TumblingWindow kan behoren.

![Stream Analytics venster tumblingvenstertriggers](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Venster verspringen
HoppingWindow-functies worden gebruikt om met een vaste tijdstuur vooruit te gaan in de tijd. HoppingWindows kunnen worden beschouwd als TumblingWindows die wel overlappen, wat betekent dat gebeurtenissen deel kunnen uitmaken van meer dan één resultatenset van een HoppingWindow. Als u een verspringen-venster hetzelfde wilt maken als een Tumblingvenstertriggers-venster, geeft u de grootte van de hop op die hetzelfde is als de venster grootte. 

![Stream Analytics venster verspringen](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Schuif venster
Schuivende venster functies, in tegens telling tot Tumblingvenstertriggers of verspringen Windows, produceren **alleen** uitvoer als er een gebeurtenis optreedt. Elk venster moet ten minste één gebeurtenis hebben en het venster wordt continu met een € (epsilon) naar voren verplaatst. Net als bij HoppingWindows kunnen gebeurtenissen deel uitmaken van meer dan één SlidingWindow.

![Stream Analytics sliding window](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Sessie venster
Sessie venster functioneert groeps gebeurtenissen die op vergelijk bare tijden aankomen, waarna er tijd wordt gefilterd waarbij er geen gegevens zijn. Dit trefwoord heeft drie belangrijke parameters: time-out, maximale duur en partitioneringssleutel (optioneel).

![Venster Stream Analytics sessie](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Er wordt een sessie venster gestart wanneer de eerste gebeurtenis plaatsvindt. Als er een andere gebeurtenis plaatsvindt binnen de opgegeven time-out van de laatste opgenomen gebeurtenis, wordt het venster uitgebreid om de nieuwe gebeurtenis op te nemen. Anders wordt het venster tijdens de time-out gesloten wanneer er geen gebeurtenissen binnen de time-out optreden.

Als er gebeurtenissen blijven optreden binnen de opgegeven time-out, blijft het sessie venster uitbrei ding totdat de maximale duur is bereikt. De maximum duur voor de controle intervallen is ingesteld op dezelfde grootte als de opgegeven maximale duur. Als de max-duur bijvoorbeeld 10 is, worden de controles op als het venster de maximum duur overschrijdt op t = 0, 10, 20, 30, enzovoort.

Wanneer een partitie sleutel wordt gegeven, worden de gebeurtenissen gegroepeerd op basis van het sleutel-en sessie venster, onafhankelijk van elkaar toegepast op elke groep. Deze partitionering is handig voor gevallen waarin u verschillende sessie Vensters nodig hebt voor verschillende gebruikers of apparaten.


## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

