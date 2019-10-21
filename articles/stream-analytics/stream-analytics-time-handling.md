---
title: Time handling in Azure Stream Analytics begrijpen
description: Meer informatie over hoe tijds afhandeling werkt en hoe u problemen met de verwerking van tijden in Azure Stream Analytics kunt oplossen.
author: jasonwhowell
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 10d300638f95fe275a23dfbc239f8f961f46b127
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598022"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Time handling in Azure Stream Analytics begrijpen

In dit artikel wordt beschreven hoe u ontwerp opties kunt maken voor het oplossen van praktische problemen met de tijds verwerking in de Azure Stream Analytics-service. De beslissingen met betrekking tot het afhandelen van tijden zijn nauw verwant met de factoren voor gebeurtenis volgorde.

## <a name="background-time-concepts"></a>Achtergrond tijd concepten

Voor een betere bespreking van de discussie kunt u een aantal achtergrond concepten definiëren:

- **Tijd**van de gebeurtenis: de tijd waarop de oorspronkelijke gebeurtenis heeft plaatsgevonden. Bijvoorbeeld wanneer een zwevende auto op de weg een bestaans hokje benadert.

- **Verwerkings tijd**: de tijd waarop de gebeurtenis het verwerkings systeem bereikt en wordt waargenomen. Bijvoorbeeld wanneer een sensor die de telefoon gebruikt, de auto ziet en het computer systeem even duurt om de gegevens te verwerken.

- **Water merk**: een markering voor een gebeurtenis die aangeeft tot welke punt gebeurtenissen zijn uitgevallen op de streaming-processor. Met water merken kan het systeem aangeven dat de voortgang van de gebeurtenissen duidelijk wordt geconsumeerd. Door de aard van streams, worden de binnenkomende gebeurtenis gegevens nooit gestopt, waardoor water merken de voortgang van een bepaald punt in de stroom aangeven.

   Het watermerk concept is belang rijk. Met water merken kunnen Stream Analytics bepalen wanneer het systeem volledige, juiste en herhaal bare resultaten kan produceren die niet hoeven te worden ingetrokken. De verwerking kan worden uitgevoerd op een gegarandeerde manier die voorspelbaar en herhaalbaar is. Als er bijvoorbeeld een hertelling moet worden uitgevoerd voor een bepaalde voor waarde voor fout afhandeling, zijn water merken veilig begin-en eind punten.

Zie de blog [101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) berichten van Tyler Akidau en [streaming 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102)voor meer informatie over dit onderwerp.

## <a name="choosing-the-best-starting-time"></a>De beste begin tijd kiezen

Stream Analytics biedt gebruikers twee opties voor het verzamelen van de gebeurtenis tijd:

1. **Aankomst tijd**  

   Aankomst tijd wordt toegewezen aan de invoer bron wanneer de gebeurtenis de bron bereikt. U kunt de aankomst tijd openen met behulp van de eigenschap **EventEnqueuedUtcTime** voor Event hubs invoer, de eigenschap **IoTHub. EnqueuedTime** voor IOT hub en met behulp van de eigenschap **BlobProperties. LastModified** voor BLOB-invoer.

   Het gebruik van aankomst tijd is het standaard gedrag en het meest geschikt voor scenario's voor gegevens archivering, waarbij geen tijdelijke logica nodig is.

2. **Toepassings tijd** (ook wel gebeurtenis tijd genoemd)

   De tijd van de toepassing wordt toegewezen wanneer de gebeurtenis wordt gegenereerd en maakt deel uit van de nettolading van de gebeurtenis. Als u gebeurtenissen wilt verwerken op toepassings tijd, gebruikt u de component **time stamp by** in de query selecteren. Als de **time stamp by** -component ontbreekt, worden gebeurtenissen verwerkt op aankomst tijd.

   Het is belang rijk dat u een tijds tempel in de payload gebruikt wanneer tijdelijke logica betrokken is. Op die manier kunnen vertragingen in het bron systeem of in het netwerk worden verwerkt.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Hoe tijd loopt in Azure Stream Analytics

Wanneer toepassings tijd wordt gebruikt, is de voortgang van de tijd gebaseerd op de binnenkomende gebeurtenissen. Het is moeilijk om te weten dat het systeem van de stroom verwerking weet of er geen gebeurtenissen zijn of dat gebeurtenissen worden vertraagd. Daarom genereert Azure Stream Analytics heuristische water merken op de volgende manieren voor elke invoer partitie:

1. Wanneer er een binnenkomende gebeurtenis is, is het water merk de grootste gebeurtenis tijd die we tot nu toe hebben gezien min de grootte van het verouderde tolerantie venster.

2. Wanneer er geen inkomende gebeurtenis is, is het water merk de huidige geschatte aankomst tijd (de verstreken tijd op de schermen van de VM die de gebeurtenissen van de laatste keer dat een invoer gebeurtenis wordt verwerkt, plus de aankomst tijd van de invoer gebeurtenis) min het tolerantie venster late aankomst.

   De aankomst tijd kan alleen worden geschat omdat de werkelijke aankomst tijd wordt gegenereerd op de invoer gebeurtenis Broker, zoals Event Hubs, en niet de Azure Stream Analytics VM die de gebeurtenissen verwerkt.

Het ontwerp heeft twee extra doel einden, naast het genereren van water merken:

1. Het systeem genereert de resultaten tijdig met of zonder binnenkomende gebeurtenissen.

   U hebt de controle over de tijd die ze nodig hebben om de uitvoer resultaten te zien. In de Azure Portal op de pagina **gebeurtenis volgorde** van uw stream Analytics taak, kunt u de instelling voor **niet-beschik bare gebeurtenissen** configureren. Bij het configureren van deze instelling moet u rekening houden met de afweging van de tijd lijn met de tolerantie van out-of-order gebeurtenissen in de gebeurtenis stroom.

   Het venster late aankomst tolerantie is belang rijk als u water merken wilt blijven genereren, zelfs als er geen binnenkomende gebeurtenissen zijn. Het kan zijn dat er een periode is waarin er geen binnenkomende gebeurtenissen zijn, zoals wanneer een gebeurtenis invoer stroom verspreid is. Dit probleem wordt exacerbated door het gebruik van meerdere partities in de invoer gebeurtenis Broker.

   Streaming-gegevens verwerkings systemen zonder een tolerantie venster voor late aankomst kunnen aflopen van vertraagde uitvoer wanneer de invoer sparse is en er meerdere partities worden gebruikt.

2. Het systeem gedrag moet herhaalbaar zijn. Herhaal baarheid is een belang rijke eigenschap van een systeem voor het verwerken van gegevens stromen.

   Het water merk is afgeleid van aankomst tijd en toepassings tijd. Beide worden bewaard in de Event Broker en kunnen dus worden herhaald. In het geval dat de aankomst tijd moet worden geschat als er gebeurtenissen ontbreken, Azure Stream Analytics journalen de geschatte aankomst tijd voor Herhaal baarheid tijdens het opnieuw afspelen voor het doel van het herstel van fouten.

Houd er rekening mee dat wanneer u ervoor kiest om **aankomst tijd** te gebruiken als de tijd van de gebeurtenis, het niet nodig is om de buiten-of-order tolerantie en de tolerantie voor late aankomst te configureren. Omdat **aankomst tijd** gegarandeerd gestaag toeneemt in de invoer gebeurtenis broker, Azure stream Analytics de configuraties eenvoudigweg genegeerd.

## <a name="late-arriving-events"></a>Achterstallige gebeurtenissen

Op basis van de definitie van het venster late aankomst tolerantie voor elk binnenkomend evenement Azure Stream Analytics de tijd van de **gebeurtenis** vergeleken met de **aankomst tijd**. Als de tijd van de gebeurtenis zich buiten het tolerantie venster bevindt, kunt u het systeem zo configureren dat het de gebeurtenis verwijdert of de tijd van de gebeurtenis aanpassen zodat deze binnen de tolerantie valt.

Houd er rekening mee dat nadat water merken zijn gegenereerd, de service mogelijk gebeurtenissen kan ontvangen met een lagere gebeurtenis tijd dan het water merk. U kunt de service zo configureren dat deze gebeurtenissen worden **neerzetten** of de tijd van de gebeurtenis **aanpassen** aan de watermerk waarde.

Als onderdeel van de aanpassing wordt het **systeem. time stamp** van de gebeurtenis ingesteld op de nieuwe waarde, maar het veld voor de **gebeurtenis tijd** zelf wordt niet gewijzigd. Deze aanpassing is de enige situatie waarin een gebeurtenis **systeem. time stamp** kan afwijken van de waarde in het veld gebeurtenis tijd en kan leiden tot onverwachte resultaten.

## <a name="handling-time-variation-with-substreams"></a>Verwerkings tijd variatie met substreamen

De hier beschreven methode voor het genereren van de heuristische water merk werkt goed in de meeste gevallen waarin tijd doorgaans wordt gesynchroniseerd tussen de verschillende afzenders van de gebeurtenis. In werkelijkheid, met name in veel IoT-scenario's, heeft het systeem echter weinig controle over de klok van de afzenders van de gebeurtenis. De afzenders van de gebeurtenis kunnen allerlei apparaten in het veld zijn, mogelijk op verschillende versies van hardware en software.

In plaats van een water merk globaal te gebruiken voor alle gebeurtenissen in een invoer partitie, heeft Stream Analytics een ander mechanisme met de naam substreams om u te helpen. U kunt substreams gebruiken in uw taak door een taak query te schrijven die gebruikmaakt van de component [**time stamp by**](/stream-analytics-query/timestamp-by-azure-stream-analytics) en het sleutel woord **over**. Als u de substream wilt aanwijzen, geeft u de naam van een sleutel kolom op achter het sleutel woord **over** , zoals een `deviceid`, zodat het systeem tijd beleid op basis van die kolom toepast. Elke substream krijgt een eigen onafhankelijk water merk. Dit mechanisme is handig voor het genereren van tijdige uitvoer, bij het verwerken van grote klok scheefheden of netwerk vertragingen tussen gebeurtenis afzenders.

Substreams zijn een unieke oplossing die wordt geleverd door Azure Stream Analytics en die niet worden aangeboden door andere systemen voor gegevensverwerking van gegevens stromen. Stream Analytics past het venster late aankomst tolerantie toe op binnenkomende gebeurtenissen wanneer substreams worden gebruikt. De standaard instelling (5 seconden) is waarschijnlijk te klein voor apparaten met uiteenlopende-tijds tempels. We raden u aan om te beginnen met 5 minuten en aanpassingen aan te brengen op basis van het patroon voor de klok van het apparaat.

## <a name="early-arriving-events"></a>Vroege arrivende gebeurtenissen

Mogelijk hebt u een ander concept met de naam vroege aankomst gezien, dat lijkt op het tegenovergestelde van het venster late aankomst tolerantie. Dit venster is vijf minuten opgelost en is een ander doel dan de laatste aankomst.

Omdat Azure Stream Analytics garandeert dat het altijd volledige resultaten genereert, kunt u de **begin tijd** van de taak alleen opgeven als de eerste uitvoer tijd van de taak, niet de invoer tijd. De begin tijd van de taak is vereist zodat het volledige venster wordt verwerkt, niet alleen vanaf het midden van het venster.

Stream Analytics wordt vervolgens de begin tijd van de query specificatie afgeleid. Omdat invoer gebeurtenis Broker echter alleen wordt geïndexeerd op aankomst tijd, moet het systeem de begin tijd van de gebeurtenis vertalen naar aankomst tijd. Het systeem kan beginnen met het verwerken van gebeurtenissen vanaf dat punt in de invoer gebeurtenis Broker. Met de vervroegde Window-limiet is de vertaling eenvoudig. Er wordt een tijd in het begin van de gebeurtenis min het venster vroege aankomst van 5 minuten. Deze berekening betekent ook dat het systeem alle gebeurtenissen die worden weer gegeven, vijf minuten groter dan aankomst tijd weglaat.

Dit concept wordt gebruikt om ervoor te zorgen dat de verwerking kan worden herhaald, ongeacht waar u begint met het uitvoeren van. Zonder een dergelijk mechanisme is het niet mogelijk om Herhaal baarheid te garanderen, net als bij veel andere claim van streaming Systems.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Neven effecten van tijd toleranties voor gebeurtenis ordening

Stream Analytics taken hebben verschillende opties voor het **ordenen van gebeurtenissen** . Twee kunnen worden geconfigureerd in de Azure Portal: de instelling voor de niet-ingestelde **volg orde** (buiten de juiste volg orde) en de **gebeurtenissen die een te late instelling aankomen** (tolerantie voor late aankomst). De **vroege aankomst** tolerantie is vast en kan niet worden aangepast. Deze tijd beleids regels worden door Stream Analytics gebruikt om sterke garanties te bieden. Deze instellingen hebben echter enkele soms onverwachte gevolgen:

1. Per ongeluk gebeurtenissen verzenden die te vroeg zijn.

   Vroege gebeurtenissen mogen niet normaal worden gegenereerd. Het is mogelijk dat vroege gebeurtenissen worden verzonden naar de uitvoer als de klok van de afzender te snel wordt uitgevoerd. Alle vroege Aankomende gebeurtenissen worden verwijderd, zodat u deze niet in de uitvoer kunt zien.

2. Oude gebeurtenissen verzenden naar Event Hubs die moeten worden verwerkt door Azure Stream Analytics.

   Hoewel oude gebeurtenissen op de eerste onschadelijk kunnen oplopen, vanwege de toepassing van de vertraagde aankomst tolerantie, kunnen de oude gebeurtenissen worden verwijderd. Als de gebeurtenissen te oud zijn, wordt de waarde van **System. time stamp** gewijzigd tijdens gebeurtenis opname. Als gevolg van dit gedrag is momenteel Azure Stream Analytics beter geschikt voor de bijna-real-time verwerkings scenario's voor gebeurtenissen, in plaats van historische gebeurtenis verwerkings scenario's. In sommige gevallen kunt u de **gebeurtenissen die laat tijd aankomen** aan de grootste mogelijke waarde (20 dagen) instellen om dit gedrag te omzeilen.

3. De uitvoer lijkt te zijn vertraagd.

   Het eerste water merk wordt gegenereerd op de berekende tijd: de **maximale gebeurtenis tijd** die het systeem heeft waargenomen tot nu toe, min de grootte van het verouderde tolerantie venster. De out-of-order-tolerantie wordt standaard ingesteld op nul (00 minuten en 00 seconden). Wanneer u deze instelt op een hogere, niet-nul-waarde, wordt de eerste uitvoer van de streaming-taak vertraagd met die waarde (of hoger) vanwege de tijd van de eerste water merk die wordt berekend.

4. De invoer is verspreid.

   Wanneer er geen invoer is in een bepaalde partitie, wordt de watermerk tijd berekend als de **aankomst tijd** minus het venster late aankomst tolerantie. Als invoer gebeurtenissen echter niet veelvuldig en verspreid zijn, kan de uitvoer worden vertraagd met die hoeveelheid tijd. De standaard **gebeurtenissen waarbij de late** waarde wordt bereikt, is 5 seconden. Er wordt een vertraging weer geven bij het verzenden van invoer gebeurtenissen per keer. De vertraging kan erger raken wanneer u gebeurtenissen instelt **waarmee** het venster over een grote waarde wordt bereikt.

5. De waarde van het **systeem. time stamp** wijkt af van de tijd in het veld voor de **gebeurtenis tijd** .

   Zoals eerder beschreven, past het systeem de gebeurtenis tijd aan door de tolerantie voor de buiten-of nabestelling of het Windows-venster met de achterstand voor aankomst. De waarde **System. time stamp** van de gebeurtenis wordt aangepast, maar niet het veld **gebeurtenis tijd** .

## <a name="metrics-to-observe"></a>Metrische gegevens om te observeren

U kunt een aantal de tijds tolerantie-effecten van de gebeurtenis volgorde bekijken via de [metrische gegevens van stream Analytics-opdracht](stream-analytics-monitoring.md). De volgende metrische gegevens zijn relevant:

|Gegevens  | Beschrijving  |
|---------|---------|
| **Out-of-order gebeurtenissen** | Hiermee wordt het aantal gebeurtenissen aangegeven dat niet in de juiste volg orde is ontvangen, die zijn verwijderd of een aangepast tijds tempel hebben gekregen. Deze metrische gegevens worden rechtstreeks beïnvloed door de configuratie van de instelling voor **niet-bestel gebeurtenissen** op de pagina **gebeurtenis volgorde** van de taak in de Azure Portal. |
| **Late invoer gebeurtenissen** | Hiermee wordt het aantal gebeurtenissen aangegeven dat te laat arriveren vanaf de bron. Deze metriek bevat gebeurtenissen die zijn verwijderd of waarvoor de tijds tempel ervan is aangepast. Deze metrische gegevens worden rechtstreeks beïnvloed door de configuratie van de **gebeurtenissen die een te late instelling aankomen** op de pagina **gebeurtenis volgorde** van de taak in de Azure Portal. |
| **Vroege invoer gebeurtenissen** | Hiermee wordt het aantal gebeurtenissen aangegeven dat zich vroeg van de bron bevindt die zijn verwijderd, of hun tijds tempel is aangepast als deze na vijf minuten te vroeg zijn. |
| **Watermerk vertraging** | Hiermee wordt de vertraging van de verwerkings taak voor streaming-gegevens aangegeven. Zie de volgende sectie voor meer informatie.|

## <a name="watermark-delay-details"></a>Details van watermerk vertraging

De metrische gegevens van het **watermerk vertraging** worden berekend als de klok tijd van de wand van het verwerkings knooppunt min het grootste water merk dat tot nu toe is gezien. Zie de [blog post water merk vertraging](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)voor meer informatie.

Er kunnen verschillende redenen zijn waarom deze metrische waarde groter is dan 0 onder normale werking:

1. De inherente verwerkings vertraging van de streaming-pijp lijn. Normaal gesp roken is deze vertraging nominaal.

2. Het venster voor de buiten-volg orde van de tolerantie heeft een vertraging geïntroduceerd, omdat het water merk wordt verminderd met de grootte van het tolerantie venster.

3. Het venster late aankomst heeft vertraging geïntroduceerd, omdat het water merk wordt verminderd met het formaat van het tolerantie venster.

4. Klok scheefheid van het verwerkings knooppunt dat de metriek genereert.

Er zijn een aantal andere resource beperkingen waardoor de streaming pijp lijn kan vertragen. De metriek voor het watermerk vertraging kan de volgende oorzaken hebben:

1. Er zijn onvoldoende verwerkings resources in Stream Analytics om het volume van invoer gebeurtenissen te verwerken. Zie [informatie over streaming-eenheden begrijpen en aanpassen](stream-analytics-streaming-unit-consumption.md)voor meer informatie over het schalen van resources.

2. Onvoldoende door Voer in de invoer gebeurtenis-Brokers, zodat ze worden beperkt. Zie [Azure-Event hubs doorvoer eenheden automatisch opschalen](../event-hubs/event-hubs-auto-inflate.md)voor mogelijke oplossingen.

3. Uitvoer-sinks worden niet ingericht met voldoende capaciteit, zodat ze worden beperkt. De mogelijke oplossingen variëren op basis van de gebruikte uitvoer service.

## <a name="output-event-frequency"></a>Frequentie van uitvoer gebeurtenis

Azure Stream Analytics gebruikt de voortgang van het water merk als enige trigger voor het produceren van uitvoer gebeurtenissen. Omdat het water merk is afgeleid van invoer gegevens, kan het worden herhaald tijdens het herstellen van de fout en wordt het proces ook gestart met het opnieuw verwerken van de gebruiker.

Wanneer u gebruikmaakt van [statistische functies in Vensters](stream-analytics-window-functions.md), produceert de service alleen uitvoer aan het einde van de Windows. In sommige gevallen willen gebruikers mogelijk gedeeltelijke aggregaties zien die zijn gegenereerd door de Windows. Gedeeltelijke aggregaties worden momenteel niet ondersteund in Azure Stream Analytics.

In andere streaming-oplossingen kunnen uitvoer gebeurtenissen op verschillende trigger punten worden gerealiseerd, afhankelijk van externe omstandigheden. Het is mogelijk dat in sommige oplossingen de uitvoer gebeurtenissen voor een bepaald tijd venster meerdere keren kunnen worden gegenereerd. Naarmate de invoer waarden worden geraffineerd, worden de statistische resultaten nauw keuriger. Gebeurtenissen kunnen op het eerst worden speculatief en na verloop van tijd worden gewijzigd. Wanneer bijvoorbeeld een bepaald apparaat offline is van het netwerk, kan een geschatte waarde door een systeem worden gebruikt. Later is hetzelfde apparaat online voor het netwerk. Vervolgens kunnen de werkelijke gebeurtenis gegevens worden opgenomen in de invoer stroom. De uitvoer resultaten van de verwerking van dat tijd venster genereren nauw keurige uitvoer.

## <a name="illustrated-example-of-watermarks"></a>Geïllustreerd voor beeld van water merken

De volgende afbeeldingen laten zien hoe water merken in verschillende omstandigheden worden uitgevoerd.

In deze tabel worden de voorbeeld gegevens weer gegeven die hieronder zijn gediagrameerd. U ziet dat de tijd van de gebeurtenis en de aankomst tijd variëren, soms overeenkomend en soms niet.

| Tijd van gebeurtenis | Aankomst tijd | DeviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | device3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | device3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | device3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | device3

In deze afbeelding worden de volgende toleranties gebruikt:

- Vroege aankomst Windows is vijf minuten
- Het venster late aankomst is vijf minuten
- Het venster opnieuw rangschikken is 2 minuten

1. Afbeelding van het water merk dat door deze gebeurtenissen wordt uitgevoerd:

   ![Afbeelding van Azure Stream Analytics watermerk](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Belang rijke processen die in de voor gaande afbeelding worden geïllustreerd:

   1. De eerste gebeurtenis (device1) en tweede gebeurtenis (device2) hebben uitgelijnde tijden en worden zonder aanpassingen verwerkt. Het water merk loopt op elke gebeurtenis.

   2. Wanneer de derde gebeurtenis (device1) wordt verwerkt, wordt de aankomst tijd (12:11) voorafgaat aan de gebeurtenis tijd (12:17). De gebeurtenis is zes minuten te vroeg gearriveerd, waardoor de gebeurtenis wordt verwijderd als gevolg van de verval datum van 5 minuten voor de vroege aankomst.

      Het water merk wordt niet in dit geval voor een vroege gebeurtenis uitgevoerd.

   3. De vierde gebeurtenis (device3) en vijfde gebeurtenis (device1) hebben uitgelijnde tijden en worden zonder aanpassing verwerkt. Het water merk loopt op elke gebeurtenis.

   4. Wanneer de zesde gebeurtenis (device3) wordt verwerkt, wordt de aankomst tijd (12:17) en de tijd van de gebeurtenis (12:12) lager dan het watermerk niveau. De tijd van de gebeurtenis wordt aangepast aan het water merk niveau (12:17).

   5. Wanneer de twaalfde gebeurtenis (device3) wordt verwerkt, is de aankomst tijd (12:27) 6 minuten vóór de tijd van de gebeurtenis (12:21). Het beleid voor late aankomst wordt toegepast. De tijd van de gebeurtenis wordt aangepast (12:22), boven het water merk (12:21), zodat er geen verdere aanpassing wordt toegepast.

2. Tweede afbeelding van het water merk dat wordt uitgevoerd zonder een early-ontvangst beleid:

   ![Azure Stream Analytics geen voor beeld van eerste beleids watermerk](media/stream-analytics-time-handling/watermark-graph-2.png)

   In dit voor beeld wordt geen beleid voor vroegtijdige ontvangst toegepast. Uitschieter gebeurtenissen waarmee het water merk vroegtijdig wordt verhoogd. De derde gebeurtenis (deviceId1 op tijd 12:11) wordt in dit scenario niet verwijderd en het water merk wordt verhoogd naar 12:15. De vierde gebeurtenis tijd wordt ingesteld op 7 minuten (12:08 tot 12:15).

3. In de laatste afbeelding worden substreams gebruikt (via de DeviceId). Meerdere water merken worden bijgehouden, één per stroom. Er zijn minder gebeurtenissen waarbij de tijden worden aangepast als resultaat.

   ![Afbeelding van water merken Azure Stream Analytics substreamen](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Volgende stappen

- [Overwegingen voor het Azure Stream Analytics van de gebeurtenis volgorde](stream-analytics-out-of-order-and-late-events.md)
- [Metrische gegevens van Stream Analytics-taak](stream-analytics-monitoring.md)
