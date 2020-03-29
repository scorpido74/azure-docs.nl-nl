---
title: Inleiding tot azure stream analytics-vensterfuncties
description: In dit artikel worden vier windowing-functies beschreven (tuimelen, springen, schuiven, sessie) die worden gebruikt in Azure Stream Analytics-taken.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: a0547243ddf114d5c9f7034f182a5e76d8c3e016
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75369419"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Inleiding tot vensterfuncties Stream Analytics

In tijdstreamingscenario's is het uitvoeren van bewerkingen op de gegevens in tijdelijke vensters een veelvoorkomend patroon. Stream Analytics heeft native ondersteuning voor vensterfuncties, waardoor ontwikkelaars complexe streamverwerkingstaken met minimale inspanning kunnen maken.

Er zijn vier soorten tijdelijke ramen om uit te kiezen: [**Tumbling**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics), [**Hoppen,**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics) [**Glijden**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)en [**Sessie**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics) ramen.  U gebruikt de vensterfuncties in de [**component GROEP BY**](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) van de querysyntaxis in uw Stream Analytics-taken. U ook gebeurtenissen over meerdere vensters samenvoegen met de functie [ **Windows().** ](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics)

Alle resultaten van de uitvoer [van vensterbewerkingen](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) aan het einde van **het** venster. De uitvoer van het venster is één gebeurtenis op basis van de gebruikte aggregaatfunctie. De uitvoergebeurtenis heeft de tijdstempel van het einde van het venster en alle vensterfuncties worden gedefinieerd met een vaste lengte. 

![Functieconcepten van het Venster Stream Analytics-venster](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Tuimelvenster
Tumbling-vensterfuncties worden gebruikt om een gegevensstroom te segmenteren in afzonderlijke tijdsegmenten en een functie tegen deze segmenten uit te voeren, zoals het onderstaande voorbeeld. De belangrijkste onderscheidende factoren van een TumblingWindow zijn dat ze herhalend zijn, niet overlappend, en dat een gebeurtenis maar tot één TumblingWindow kan behoren.

![Stream Analytics tumbling venster](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Hopping-venster
HoppingWindow-functies worden gebruikt om met een vaste tijdstuur vooruit te gaan in de tijd. HoppingWindows kunnen worden beschouwd als TumblingWindows die wel overlappen, wat betekent dat gebeurtenissen deel kunnen uitmaken van meer dan één resultatenset van een HoppingWindow. Als u een hopping-venster hetzelfde wilt maken als een tumbling-venster, geeft u de hopgrootte op die gelijk is aan de venstergrootte. 

![Stream Analytics-hopping-venster](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Schuifvenster
Schuifvensterfuncties produceren, in tegenstelling tot Tumbling- of Hopping-vensters, **alleen** een uitvoer wanneer een gebeurtenis optreedt. Elk venster moet ten minste één gebeurtenis hebben en het venster wordt continu met een € (epsilon) naar voren verplaatst. Net als bij HoppingWindows kunnen gebeurtenissen deel uitmaken van meer dan één SlidingWindow.

![Schuifvenster Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Sessievenster
Sessievensterfuncties groepsgebeurtenissen die op vergelijkbare tijdstippen aankomen, filteren perioden uit waarin er geen gegevens zijn. Dit trefwoord heeft drie belangrijke parameters: time-out, maximale duur en partitioneringssleutel (optioneel).

![Stream Analytics-sessievenster](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Een sessievenster begint wanneer de eerste gebeurtenis plaatsvindt. Als een andere gebeurtenis optreedt binnen de opgegeven time-out van de laatst ingenomen gebeurtenis, wordt het venster uitgebreid met de nieuwe gebeurtenis. Anders als er geen gebeurtenissen plaatsvinden binnen de time-out, wordt het venster gesloten bij de time-out.

Als gebeurtenissen binnen de opgegeven time-out blijven plaatsvinden, blijft het sessievenster verlengen totdat de maximale duur is bereikt. De maximale duurcontroleintervallen zijn ingesteld op dezelfde grootte als de opgegeven maximumduur. Als de maximale duur bijvoorbeeld 10 is, wordt bij de controle of het venster de maximale duur overschrijdt, uitgevoerd op t = 0, 10, 20, 30, enz.

Wanneer een partitiesleutel wordt geleverd, worden de gebeurtenissen gegroepeerd door de sleutel en wordt het sessievenster onafhankelijk van elkaar op elke groep toegepast. Deze partitionering is handig voor gevallen waarin u verschillende sessievensters nodig hebt voor verschillende gebruikers of apparaten.


## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

