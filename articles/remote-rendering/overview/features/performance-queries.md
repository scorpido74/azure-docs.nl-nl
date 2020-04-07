---
title: Prestatiequery's aan de serverzijde
description: Prestatiequery's aan de serverzijde uitvoeren via API-aanroepen
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682024"
---
# <a name="server-side-performance-queries"></a>Prestatiequery's aan de serverzijde

Goede rendering prestaties op de server is van cruciaal belang voor stabiele framerates en een goede gebruikerservaring. Het is belangrijk om de prestatiekenmerken op de server zorgvuldig te controleren en waar nodig te optimaliseren. Prestatiegegevens kunnen worden opgevraagd via speciale API-functies.

Meest impactvol voor de rendering prestaties is het model invoergegevens. U de invoergegevens aanpassen zoals beschreven in [Het configureren van de modelconversie.](../../how-tos/conversion/configure-model-conversion.md)

De prestaties van de clientzijde van toepassingen kunnen ook een knelpunt zijn. Voor een diepgaande analyse van de prestaties aan de clientzijde is het raadzaam om een [prestatietracering](../../how-tos/performance-tracing.md)te nemen.

## <a name="clientserver-timeline"></a>Client/servertijdlijn

Voordat u in detail gaat over de verschillende latentiewaarden, is het de moeite waard om de synchronisatiepunten tussen client en server op de tijdlijn te bekijken:

![Pijplijntijdlijn](./media/server-client-timeline.png)

De illustratie laat zien hoe:

* een *Pose-schatting* wordt afgetrapt door de client bij een constante framesnelheid van 60 Hz (elke 16,6 ms)
* de server begint vervolgens met renderen, op basis van de pose
* de server stuurt de gecodeerde videoafbeelding terug
* de client decodeert de afbeelding, voert wat CPU- en GPU-werk uit en geeft de afbeelding weer

## <a name="frame-statistics-queries"></a>Query's voor framestatistieken

Framestatistieken bieden informatie op hoog niveau voor het laatste frame, zoals latentie. De gegevens in `FrameStatistics` de structuur worden gemeten aan de clientzijde, dus de API is een synchrone aanroep:

````c#
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
````

Het opgehaalde `FrameStatistics` object bevat de volgende leden:

| Lid | Uitleg |
|:-|:-|
| latencyPoseToReceive | Latentie van camerapose schatting op het clientapparaat totdat een serverframe voor deze pose volledig beschikbaar is voor de clienttoepassing. Deze waarde omvat netwerkretour, serverrendertijd, videodecode en jittercompensatie. Zie **interval 1 in de bovenstaande afbeelding.**|
| latentieReceiveToPresent | Latentie vanaf de beschikbaarheid van een ontvangen extern frame totdat de client-app PresentFrame op de CPU aanroept. |
| latencyPresentToDisplay  | Latentie van het presenteren van een frame op de CPU totdat het display oplicht. Deze waarde omvat de GPU-tijd van de client, eventuele framebuffering die wordt uitgevoerd door het besturingssysteem, hardwareherprojectie en apparaatafhankelijke weergavesstijd. Zie **interval 2 in de bovenstaande afbeelding.**|
| timeSinceLastPresent timeSinceLastPresent | De tijd tussen de daaropvolgende oproepen naar PresentFrame op de CPU. Waarden die groter zijn dan de weergaveduur (bijvoorbeeld 16,6 ms op een clientapparaat van 60 Hz) geven problemen aan die worden veroorzaakt doordat de clienttoepassing de CPU-werkbelasting niet op tijd afmaakt. Zie **interval 3 in de bovenstaande afbeelding.**|
| videoFrames ontvangen | Het aantal frames dat in de laatste seconde van de server is ontvangen. |
| videoFrameReusedCount | Aantal ontvangen frames in de laatste seconde die meer dan één keer op het apparaat zijn gebruikt. Niet-nulwaarden geven aan dat frames opnieuw moesten worden gebruikt en opnieuw moeten worden geprojecteerd vanwege netwerkjitter of overmatige weergavetijd van de server. |
| videoFramesOvergeslagen | Aantal ontvangen frames in de laatste seconde die zijn gedecodeerd, maar niet worden weergegeven omdat er een nieuwer frame is aangekomen. Niet-nulwaarden geven aan dat netwerkjittering ervoor zorgde dat meerdere frames werden vertraagd en vervolgens samen op het clientapparaat aankomen in een burst. |
| videoFramesVerwijderd | Zeer vergelijkbaar met **videoFramesSkipped,** maar de reden voor wordt weggegooid is dat een frame kwam zo laat dat het niet eens kan worden gecorreleerd met een hangende pose meer. Als dit gebeurt, is er een aantal ernstige netwerk stelling.|
| videoFrameMinDelta | Minimale tijd tussen twee opeenvolgende frames die aankomen tijdens de laatste seconde. Samen met videoFrameMaxDelta geeft dit bereik een indicatie van jitter veroorzaakt door het netwerk of video codec. |
| videoFrameMaxDelta | Maximale tijd tussen twee opeenvolgende frames die aankomen tijdens de laatste seconde. Samen met videoFrameMinDelta geeft dit bereik een indicatie van jitter veroorzaakt door het netwerk of video codec. |

De som van alle latentiewaarden is doorgaans veel groter dan de beschikbare frametijd op 60 Hz. Dit is OK, omdat meerdere frames parallel aan de vlucht zijn en nieuwe frameaanvragen worden afgetrapt met de gewenste framesnelheid, zoals in de afbeelding wordt weergegeven. Als de latentie echter te groot wordt, beïnvloedt het de kwaliteit van de [late fase-herprojectie](../../overview/features/late-stage-reprojection.md)en kan het de algehele ervaring in gevaar brengen.

`videoFramesReceived`, `videoFrameReusedCount`en `videoFramesDiscarded` kan worden gebruikt om de prestaties van het netwerk en de server te meten. Als `videoFramesReceived` het `videoFrameReusedCount` laag is en hoog is, kan dit wijzen op netwerkcongestie of slechte serverprestaties. Een `videoFramesDiscarded` hoge waarde duidt ook op netwerkcongestie.

Tot`timeSinceLastPresent`slot, `videoFrameMinDelta`, `videoFrameMaxDelta` en geven een idee van de variantie van inkomende videoframes en lokale huidige oproepen. Hoge variantie betekent instabiele framesnelheid.

Geen van de bovenstaande waarden geeft een duidelijke indicatie van pure netwerklatentie (de rode pijlen in de illustratie), omdat `latencyPoseToReceive`de exacte tijd dat de server bezig is met renderen moet worden afgetrokken van de waarde rond de reis . Het servergedeelte van de algehele latentie is informatie die niet beschikbaar is voor de client. In de volgende alinea wordt echter uitgelegd hoe deze waarde wordt `networkLatency` benaderd door extra invoer van de server en wordt blootgesteld via de waarde.

## <a name="performance-assessment-queries"></a>Query's voor prestatiebeoordeling

*Prestatiebeoordelingsquery's* bieden meer diepgaande informatie over de CPU- en GPU-workload op de server. Aangezien de gegevens van de server worden opgevraagd, volgt het opvragen van een prestatiemomentopname het gebruikelijke async-patroon:

``` cs
PerformanceAssessmentAsync _assessmentQuery = null;

void QueryPerformanceAssessment(AzureSession session)
{
    _assessmentQuery = session.Actions.QueryServerPerformanceAssessmentAsync();
    _assessmentQuery.Completed += (PerformanceAssessmentAsync res) =>
    {
        // do something with the result:
        PerformanceAssessment result = res.Result;
        // ...

        _assessmentQuery = null;
    };
}
```

In tegenstelling `FrameStatistics` tot `PerformanceAssessment` het object bevat het object informatie aan de serverzijde:

| Lid | Uitleg |
|:-|:-|
| timeCPU timeCPU | Gemiddelde server-CPU-tijd per frame in milliseconden |
| timeGPU | Gemiddelde gpu-tijd voor server per frame in milliseconden |
| gebruikCPU | Totaal cpu-gebruik server in procenten |
| gebruikGPU | Totaal gpu-gebruik server in procenten |
| geheugenCPU | Totaal gebruik van serverhoofdgeheugen in procenten |
| geheugenGPU | Totaal gebruik van toegewijd videogeheugen in procenten van de server-GPU |
| netwerkLatentie | De geschatte gemiddelde retournetwerklatentie in milliseconden. In de bovenstaande afbeelding komt dit overeen met de som van de rode pijlen. De waarde wordt berekend door de werkelijke `latencyPoseToReceive` weergavetijd `FrameStatistics`van de server af te trekken van de waarde van . Hoewel deze benadering niet nauwkeurig is, geeft het een indicatie van de netwerklatentie, geïsoleerd van de latentiewaarden die op de client worden berekend. |
| veelhoekenGerendered | Het aantal driehoeken dat in één frame wordt weergegeven. Dit getal bevat ook de driehoeken die later tijdens het renderen worden geruimd. Dat betekent dat dit aantal niet veel verschilt over verschillende cameraposities, maar de prestaties kunnen drastisch variëren, afhankelijk van de driehoeksruimingspercentage.|

Om u te helpen de waarden te beoordelen, elk gedeelte wordt geleverd met een kwaliteitsclassificatie zoals **Groot,** **Goed,** **Middelmatig**of **Slecht**.
Deze beoordelingsstatistiek geeft een ruwe indicatie van de status van de server, maar moet niet als absoluut worden gezien. Stel dat u een 'middelmatige' score voor de GPU-tijd ziet. Het wordt beschouwd als middelmatig omdat het dicht bij de limiet voor de totale frame tijdsbudget. In uw geval kan het echter toch een goede waarde zijn, omdat u een complex model rendert.

## <a name="statistics-debug-output"></a>Statistische foutopsporing uitvoer

De `ARRServiceStats` klasse omsluit zowel de framestatistieken als de query's voor prestatiebeoordeling en biedt handige functionaliteit om statistieken als geaggregeerde waarden of als een vooraf gebouwde tekenreeks te retourneren. De volgende code is de eenvoudigste manier om serverstatistieken weer te geven in uw clienttoepassing.

``` cs
ARRServiceStats _stats = null;

void OnConnect()
{
    _stats = new ARRServiceStats();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

De bovenstaande code vult het tekstlabel aan met de volgende tekst:

![ArrServiceStats-tekenreeksuitvoer](./media/arr-service-stats.png)

De `GetStatsString` API maakt een tekenreeks van alle waarden, maar elke waarde kan `ARRServiceStats` ook programmatisch worden opgevraagd vanuit de instantie.

Er zijn ook varianten van de leden, die de waarden in de loop van de tijd samenvoegen. Zie leden met `*Avg` `*Max`achtervoegsel , of `*Total`. Het `FramesUsedForAverage` lid geeft aan hoeveel frames zijn gebruikt voor deze aggregatie.

## <a name="next-steps"></a>Volgende stappen

* [Prestatiesporen maken](../../how-tos/performance-tracing.md)
* [De modelconversie configureren](../../how-tos/conversion/configure-model-conversion.md)
