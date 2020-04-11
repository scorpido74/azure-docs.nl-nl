---
title: Inzicht in time handling in Azure Stream Analytics
description: Lees hoe time handling werkt in Azure Stream Analytics, zoals hoe u de beste begintijd kiest, hoe u late en vroege gebeurtenissen afhandelen en statistieken voor tijdverwerking.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 55537fb923b26de4e02be35fdb817dee147584d7
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115126"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Inzicht in time handling in Azure Stream Analytics

In dit artikel bespreken we hoe u ontwerpkeuzes maken om praktische problemen met het afhandelen van tijd in de Azure Stream Analytics-service op te lossen. Time handling ontwerpbeslissingen zijn nauw verbonden met factoren voor het bestellen van evenementen.

## <a name="background-time-concepts"></a>Achtergrondtijdconcepten

Om de discussie beter te kaderen, definiëren we een aantal achtergrondconcepten:

- **Gebeurtenistijd**: het tijdstip waarop de oorspronkelijke gebeurtenis is gebeurd. Bijvoorbeeld wanneer een rijdende auto op de snelweg een tolhuisje nadert.

- **Verwerkingstijd**: Het tijdstip waarop de gebeurtenis het verwerkingssysteem bereikt en wordt waargenomen. Bijvoorbeeld wanneer een tolpoortsensor de auto ziet en het computersysteem enkele ogenblikken nodig heeft om de gegevens te verwerken.

- **Watermerk:** een gebeurtenistijdmarkering die aangeeft tot welk punt gebeurtenissen zijn binnengevallen aan de streamingprocessor. Watermerken laten het systeem duidelijke vooruitgang aangeven bij het innemen van de gebeurtenissen. Door de aard van stromen, de inkomende gebeurtenis gegevens nooit stopt, zodat watermerken geven de voortgang tot een bepaald punt in de stroom.

   Het watermerkconcept is belangrijk. Met watermerken kan Stream Analytics bepalen wanneer het systeem volledige, correcte en herhaalbare resultaten kan produceren die niet hoeven te worden ingetrokken. De verwerking kan worden gedaan op een gegarandeerde manier die voorspelbaar en herhaalbaar is. Als er bijvoorbeeld een hertelling moet worden uitgevoerd voor een foutafhandelingstoestand, zijn watermerken veilige begin- en eindpunten.

Als extra middelen over dit onderwerp, zie Tyler Akidau's blog posts [Streaming 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) en [Streaming 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choosing-the-best-starting-time"></a>De beste starttijd kiezen

Stream Analytics geeft gebruikers twee opties voor het kiezen van gebeurtenistijd:

1. **Aankomsttijd**  

   Aankomsttijd wordt toegewezen aan de invoerbron wanneer de gebeurtenis de bron bereikt. U hebt toegang tot de aankomsttijd door de eigenschap **EventEnqueuedUtcTime** te gebruiken voor gebeurtenishubs-invoer, de eigenschap **IoTHub.EnqueuedTime** voor IoT Hub en de eigenschap **BlobProperties.LastModified** te gebruiken voor blob-invoer.

   Het gebruik van aankomsttijd is het standaardgedrag en het beste gebruikt voor scenario's voor gegevensarchivering, waarbij er geen tijdelijke logica nodig is.

2. **Toepassingstijd** (ook wel gebeurtenistijd genoemd)

   De toepassingstijd wordt toegewezen wanneer de gebeurtenis wordt gegenereerd en maakt deel uit van de gebeurtenispayload. Als u gebeurtenissen wilt verwerken op toepassingstijd, gebruikt u de component **Tijdstempel op** in de selectiequery. Als de **tijdstempel per** clausule afwezig is, worden gebeurtenissen verwerkt tegen aankomsttijd.

   Het is belangrijk om een tijdstempel in de payload te gebruiken wanneer temporele logica betrokken is. Op die manier kunnen vertragingen in het bronsysteem of in het netwerk worden verantwoord.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Hoe de tijd vordert in Azure Stream Analytics

Bij het gebruik van de toepassingstijd is de tijdsprogressie gebaseerd op de binnenkomende gebeurtenissen. Het is moeilijk voor het streamverwerkingssysteem om te weten of er geen gebeurtenissen zijn of dat gebeurtenissen zijn vertraagd. Azure Stream Analytics genereert daarom heuristische watermerken op de volgende manieren voor elke invoerpartitie:

1. Wanneer er een inkomende gebeurtenis, het watermerk is de grootste gebeurtenis tijd die we tot nu toe hebben gezien minus de out-of-order tolerantie venster grootte.

2. Wanneer er geen binnenkomende gebeurtenis is, is het watermerk de huidige geschatte aankomsttijd (de verstreken tijd achter de schermen VM die de gebeurtenissen verwerkt vanaf de laatste keer dat een invoergebeurtenis wordt gezien plus de aankomsttijd van die invoergebeurtenis) minus het venster voor de late aankomsttolerantie.

   De aankomsttijd kan alleen worden geschat, omdat de werkelijke aankomsttijd wordt gegenereerd op de broker voor invoergebeurtenissen, zoals gebeurtenishubs, en niet de Azure Stream Analytics VM die de gebeurtenissen verwerkt.

Het ontwerp dient twee extra doeleinden, naast het genereren van watermerken:

1. Het systeem genereert resultaten op een tijdige manier met of zonder inkomende gebeurtenissen.

   U hebt controle over hoe tijdig ze de uitvoerresultaten willen zien. In de Azure-portal u op de pagina **Gebeurtenisvolgorde** van uw Stream Analytics-taak de instelling **Out of order-gebeurtenissen** configureren. Houd bij het configureren van die instelling rekening met de afweging van tijdigheid met tolerantie voor out-of-order-gebeurtenissen in de gebeurtenisstream.

   De late aankomst tolerantie venster is belangrijk om te blijven genereren watermerken, zelfs bij afwezigheid van inkomende gebeurtenissen. Soms kan er een periode zijn waarin er geen binnenkomende gebeurtenissen binnenkomen, bijvoorbeeld wanneer een invoerstream voor gebeurtenissen schaars is. Dat probleem wordt verergerd door het gebruik van meerdere partities in de input event broker.

   Streaming gegevensverwerkingssystemen zonder een late aankomsttolerantievenster kunnen last hebben van vertraagde uitvoer wanneer ingangen schaars zijn en meerdere partities worden gebruikt.

2. Het systeemgedrag moet herhaalbaar zijn. Herhaalbaarheid is een belangrijke eigenschap van een streaming data processing systeem.

   Het watermerk is afgeleid van aankomsttijd en toepassingstijd. Beide zijn blijven bestaan in de gebeurtenis makelaar, en dus herhaalbaar. In het geval dat de aankomsttijd moet worden geschat bij afwezigheid van gebeurtenissen, neemt Azure Stream Analytics de geschatte aankomsttijd op voor herhaalbaarheid tijdens het opnieuw afspelen met het oog op herstel van fouten.

Wanneer u ervoor kiest om **de aankomsttijd** als gebeurtenistijd te gebruiken, is het niet nodig om de tolerantie buiten de bestelling en de tolerantie voor late aankomst te configureren. Aangezien **de aankomsttijd** in de broker voor invoergebeurtenissen gegarandeerd monotonisch toeneemt, negeert Azure Stream Analytics eenvoudigweg de configuraties.

## <a name="late-arriving-events"></a>Late aankomst evenementen

Azure Stream Analytics vergelijkt per definitie het venster tolerantie voor late aankomst voor elke binnenkomende gebeurtenis **met** de **aankomsttijd;** Als de gebeurtenistijd zich buiten het tolerantievenster bevindt, u het systeem configureren om de gebeurtenis te laten vallen of de tijd van de gebeurtenis aan te passen om binnen de tolerantie te vallen.

Houd er rekening mee dat nadat watermerken zijn gegenereerd, de service mogelijk gebeurtenissen kan ontvangen met een gebeurtenistijd die lager is dan het watermerk. U de service zo configureren dat deze gebeurtenissen **worden neergestoof** d.m.v. de tijd van de gebeurtenis **aanpassen** aan de watermerkwaarde.

Als onderdeel van de aanpassing is de **system.timestamp** van de gebeurtenis ingesteld op de nieuwe waarde, maar het **gebeurtenistijdveld** zelf wordt niet gewijzigd. Deze aanpassing is de enige situatie waarin de **System.Timestamp** van een gebeurtenis kan verschillen van de waarde in het gebeurtenistijdveld en kan leiden tot het genereren van onverwachte resultaten.

## <a name="handling-time-variation-with-substreams"></a>Tijdvariatie verwerken met substreams

De heuristische watermerk generatie mechanisme hier beschreven werkt goed in de meeste gevallen waar de tijd is meestal gesynchroniseerd tussen de verschillende gebeurtenis afzenders. Echter, in het echte leven, vooral in veel IoT-scenario's, het systeem heeft weinig controle over de klok op de gebeurtenis afzenders. De gebeurtenisafzenders zouden allerlei apparaten in het gebied, misschien op verschillende versies van hardware en software kunnen zijn.

In plaats van een watermerk globaal te gebruiken voor alle gebeurtenissen in een invoerpartitie, heeft Stream Analytics een ander mechanisme genaamd substreams om u te helpen. U substreams in uw taak gebruiken door een taakquery te schrijven die de [**timestamp per-component**](/stream-analytics-query/timestamp-by-azure-stream-analytics) en het zoekwoord **OVER**gebruikt. Als u de substream wilt aanwijzen, geeft u `deviceid`een sleutelkolomnaam op naar het **trefwoord OVER,** zoals een , zodat het systeem tijdbeleidsregels toepast met die kolom. Elke substroom krijgt zijn eigen onafhankelijke watermerk. Dit mechanisme is handig om tijdige productie van uitvoer mogelijk te maken, wanneer het gaat om grote klokscheeftrekkingen of netwerkvertragingen tussen gebeurtenisafzenders.

Substreams zijn een unieke oplossing van Azure Stream Analytics en worden niet aangeboden door andere streaming gegevensverwerkingssystemen. Stream Analytics past het venster voor late aankomsttolerantie toe op binnenkomende gebeurtenissen wanneer substreams worden gebruikt. De standaardinstelling (5 seconden) is waarschijnlijk te klein voor apparaten met uiteenlopende tijdstempels. We raden u aan om te beginnen met 5 minuten en aanpassingen aan te brengen op basis van het patroon van de apparaatklokscheefheid.

## <a name="early-arriving-events"></a>Vroege aankomst evenementen

U een ander concept genoemd vroege aankomstvenster opgemerkt hebben, dat als het tegengestelde van het recente venster van de aankomsttolerantie kijkt. Dit venster is vastgesteld op 5 minuten, en dient een ander doel van late aankomst een.

Omdat Azure Stream Analytics garandeert dat het altijd volledige resultaten genereert, u alleen de **begintijd** van de taak opgeven als de eerste uitvoertijd van de taak, niet als de invoertijd. De begintijd van de taak is vereist, zodat het volledige venster wordt verwerkt, niet alleen vanuit het midden van het venster.

Stream Analytics ontleent vervolgens de begintijd aan de queryspecificatie. Omdat de broker voor invoergebeurtenissen echter alleen wordt geïndexeerd aan de aankomsttijd, moet het systeem de begingebeurtenistijd vertalen naar de aankomsttijd. Het systeem kan vanaf dat moment beginnen met het verwerken van gebeurtenissen in de broker voor invoergebeurtenissen. Met de vroege vensterlimiet is de vertaling eenvoudig. Het is de starttijd minus het 5 minuten vooraankomst venster. Deze berekening betekent ook dat het systeem alle gebeurtenissen laat vallen die worden gezien als een gebeurtenistijd 5 minuten ealier dan de aankomsttijd.

Dit concept wordt gebruikt om ervoor te zorgen dat de verwerking herhaalbaar is, ongeacht waar u begint met de uitvoer van. Zonder een dergelijk mechanisme zou het niet mogelijk zijn om herhaalbaarheid te garanderen, zoals veel andere streamingsystemen beweren dat ze doen.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Bijwerkingen van tijdtoleranties voor het bestellen van evenementen

Stream Analytics-vacatures hebben verschillende opties **voor het bestellen van gebeurtenissen.** Twee daarvan kunnen worden geconfigureerd in de Azure-portal: de instelling Out of **order-gebeurtenissen** (out-of-order-tolerantie) en de **gebeurtenissen die te laat aankomen** (late aankomsttolerantie). De **early arrival** tolerantie is vast en kan niet worden aangepast. Dit tijdbeleid wordt door Stream Analytics gebruikt om sterke garanties te bieden. Deze instellingen hebben echter soms wel onverwachte gevolgen:

1. Stuur per ongeluk gebeurtenissen die te vroeg zijn.

   Vroege gebeurtenissen mogen niet normaal worden uitgevoerd. Het is mogelijk dat vroege gebeurtenissen worden verzonden naar de uitvoer als de klok van de afzender wordt uitgevoerd te snel wel. Alle vroege aankomende gebeurtenissen worden geschrapt, dus je zult ze niet zien van de output.

2. Oude gebeurtenissen verzenden naar gebeurtenishubs die worden verwerkt door Azure Stream Analytics.

   Hoewel oude gebeurtenissen in eerste instantie misschien onschuldig lijken, vanwege de toepassing van de late aankomsttolerantie, kunnen de oude gebeurtenissen worden geschrapt. Als de gebeurtenissen te oud zijn, wordt de waarde **System.Timestamp** gewijzigd tijdens het innemen van gebeurtenissen. Vanwege dit gedrag is Azure Stream Analytics momenteel meer geschikt voor scenario's voor het verwerken van bijna realtime gebeurtenissen, in plaats van historische scenario's voor het verwerken van gebeurtenissen. U de **gebeurtenissen die te laat aankomen** in sommige gevallen instellen op de grootst mogelijke waarde (20 dagen) om dit gedrag te omzeilen.

3. De output lijkt te zijn vertraagd.

   Het eerste watermerk wordt gegenereerd op de berekende tijd: de **maximale gebeurtenistijd** die het systeem tot nu toe heeft waargenomen, verminderd met de grootte van de tolerantiestatus buiten de orde. Standaard is de out-of-ordertolerantie geconfigureerd op nul (00 minuten en 00 seconden). Wanneer u deze instelt op een hogere, niet-nultijdwaarde, wordt de eerste uitvoer van de streamingtaak vertraagd met die waarde van tijd (of meer) als gevolg van de eerste watermerktijd die wordt berekend.

4. Ingangen zijn schaars.

   Wanneer er geen invoer in een bepaalde partitie is, wordt de watermerktijd berekend als de **aankomsttijd** minus het venster voor late aankomsttolerantie. Als invoergebeurtenissen niet frequent en schaars zijn, kan de uitvoer met die tijd worden vertraagd. De **standaardgebeurtenissen die te laat aankomen,** zijn 5 seconden. U bijvoorbeeld enige vertraging verwachten bij het verzenden van invoergebeurtenissen. De vertragingen kunnen erger worden wanneer u **gebeurtenissen die laat in** het venster komen instelt op een grote waarde.

5. De waarde **van System.Timestamp** is anders dan de tijd in het **tijdsveld van** de gebeurtenis.

   Zoals eerder beschreven, past het systeem de gebeurtenistijd aan aan de status van de tolerantie of de late periodevan aankomsttolerantie. De waarde **System.Timestamp** van de gebeurtenis wordt aangepast, maar niet het **gebeurtenistijdveld.**

## <a name="metrics-to-observe"></a>Statistieken om te observeren

U een aantal effecten van de gebeurtenistijdtolerantie waarnemen via [de taakstatistieken van Stream Analytics.](stream-analytics-monitoring.md) De volgende statistieken zijn relevant:

|Gegevens  | Beschrijving  |
|---------|---------|
| **Out-of-order-evenementen** | Geeft het aantal gebeurtenissen aan dat buiten de orde is ontvangen en die zijn verwijderd of een aangepaste tijdstempel hebben gekregen. Deze statistiek wordt rechtstreeks beïnvloed door de configuratie van de instelling **Out of order-gebeurtenissen** op de pagina **Evenementvolgorde** op de taak in de Azure-portal. |
| **Gebeurtenissen voor late invoer** | Geeft het aantal gebeurtenissen aan dat te laat van de bron komt. Deze statistiek bevat gebeurtenissen die zijn verwijderd of waarvan hun tijdstempel is aangepast. Deze statistiek wordt rechtstreeks beïnvloed door de configuratie van de **gebeurtenissen die te laat aankomen** op de pagina **Gebeurtenisvolgorde** op de taak in de Azure-portal. |
| **Gebeurtenissen voor vroege invoer** | Geeft het aantal gebeurtenissen aan dat vroeg van de bron komt en die zijn verwijderd of hun tijdstempel is aangepast als ze langer zijn dan 5 minuten te vroeg. |
| **Watermerk vertraging** | Geeft de vertraging aan van de streaming gegevensverwerkingstaak. Zie meer informatie in de volgende sectie.|

## <a name="watermark-delay-details"></a>Gegevens watermerk vertraging

De **watermark vertraging** metrische wordt berekend als de wandklok tijd van de verwerking knooppunt minus het grootste watermerk dat het tot nu toe heeft gezien. Zie voor meer informatie het [blogbericht watermerkvertraging](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Er kunnen verschillende redenen zijn waarom deze metrische waarde groter is dan 0 bij normaal gebruik:

1. Inherente verwerkingsvertraging van de streamingpijplijn. Normaal gesproken is deze vertraging nominaal.

2. Het out-of-order tolerantievenster introduceerde vertraging, omdat het watermerk wordt verminderd met de grootte van het tolerantievenster.

3. Het late aankomstvenster introduceerde vertraging, omdat het watermerk wordt verminderd door de grootte van het tolerantievenster.

4. Klokscheefheid van het verwerkingsknooppunt dat de statistiek genereert.

Er zijn een aantal andere resourcebeperkingen die ervoor kunnen zorgen dat de streamingpijplijn vertraagt. De vertragingsstatistiek van het watermerk kan stijgen als gevolg van:

1. Onvoldoende verwerkingsresources in Stream Analytics om het volume van invoergebeurtenissen te verwerken. Zie [Streamingeenheden begrijpen en aanpassen](stream-analytics-streaming-unit-consumption.md)als u resources wilt opschalen.

2. Niet genoeg doorvoer binnen de input event brokers, dus ze zijn beperkt. Zie Voor mogelijke oplossingen [de doorvoereenheden azure event hubs automatisch opschalen.](../event-hubs/event-hubs-auto-inflate.md)

3. Output putten zijn niet voorzien van voldoende capaciteit, dus ze zijn beperkt. De mogelijke oplossingen variëren sterk op basis van de smaak van de output service wordt gebruikt.

## <a name="output-event-frequency"></a>Frequentie van uitvoergebeurtenis

Azure Stream Analytics gebruikt de voortgang van watermerken als enige trigger om uitvoergebeurtenissen te produceren. Omdat het watermerk is afgeleid van invoergegevens, is het herhaalbaar tijdens het herstel van de storing en ook bij de door de gebruiker geïnitieerde opwerking.

Bij het gebruik van [gevensterde aggregaten](stream-analytics-window-functions.md)produceert de service alleen uitgangen aan het einde van de vensters. In sommige gevallen willen gebruikers mogelijk gedeeltelijke aggregaten zien die uit de vensters worden gegenereerd. Gedeeltelijke aggregaten worden momenteel niet ondersteund in Azure Stream Analytics.

In andere streamingoplossingen kunnen uitvoergebeurtenissen worden gematerialiseerd op verschillende triggerpoints, afhankelijk van externe omstandigheden. Het is mogelijk in sommige oplossingen dat de uitvoergebeurtenissen voor een bepaald tijdvenster meerdere keren kunnen worden gegenereerd. Naarmate de invoerwaarden worden verfijnd, worden de geaggregeerde resultaten nauwkeuriger. Gebeurtenissen kunnen in eerste instantie worden gespeculeerd en herzien in de tijd. Wanneer een bepaald apparaat bijvoorbeeld offline is van het netwerk, kan een geschatte waarde door een systeem worden gebruikt. Later komt hetzelfde apparaat online op het netwerk. Vervolgens kunnen de werkelijke gebeurtenisgegevens worden opgenomen in de invoerstroom. De uitvoer resultaten van de verwerking van dat tijdvenster produceert nauwkeuriger output.

## <a name="illustrated-example-of-watermarks"></a>Geïllustreerd voorbeeld van watermerken

De volgende afbeeldingen laten zien hoe watermerken zich in verschillende omstandigheden ontwikkelen.

In deze tabel worden de onderstaande voorbeeldgegevens weergegeven. Merk op dat de gebeurtenistijd en de aankomsttijd variëren, soms overeenkomend en soms niet.

| Gebeurtenistijd | Aankomsttijd | DeviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | apparaat3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | apparaat3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | apparaat3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | apparaat3

In deze illustratie worden de volgende toleranties gebruikt:

- Vroege aankomst ramen is 5 minuten
- Laat aankomen venster is 5 minuten
- Venster opnieuw bestellen is 2 minuten

1. Illustratie van het watermerk dat door deze gebeurtenissen vordert:

   ![Afbeelding van het watermerk Azure Stream Analytics](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Opmerkelijke processen geïllustreerd in de voorgaande afbeelding:

   1. De eerste gebeurtenis (device1) en tweede gebeurtenis (device2) hebben de tijden uitgelijnd en worden zonder aanpassingen verwerkt. Het watermerk vordert op elke gebeurtenis.

   2. Wanneer de derde gebeurtenis (apparaat1) wordt verwerkt, gaat de aankomsttijd (12:11) vooraf aan de gebeurtenistijd (12:17). Het evenement kwam 6 minuten te vroeg, dus het evenement is gedaald als gevolg van de 5 minuten vroege aankomst tolerantie.

      Het watermerk gaat niet verder in dit geval van een vroege gebeurtenis.

   3. De vierde gebeurtenis (device3) en de vijfde gebeurtenis (device1) hebben de tijden uitgelijnd en worden zonder aanpassing verwerkt. Het watermerk vordert op elke gebeurtenis.

   4. Wanneer de zesde gebeurtenis (apparaat3) wordt verwerkt, ligt de aankomsttijd (12:17) en de gebeurtenistijd (12:12) onder het watermerkniveau. De gebeurtenistijd wordt aangepast aan het waterpeil (12:17).

   5. Wanneer de twaalfde gebeurtenis (apparaat3) wordt verwerkt, ligt de aankomsttijd (12:27) 6 minuten voor op de gebeurtenistijd (12:21). Het beleid voor late aankomst wordt toegepast. De gebeurtenistijd wordt aangepast (12:22), die boven het watermerk (12:21) is zodat geen verdere aanpassing wordt toegepast.

2. Tweede illustratie van watermerk vooruitgang zonder een vroege aankomst beleid:

   ![Azure Stream Analytics geen illustratie van het watermerk voor een vroeg beleid](media/stream-analytics-time-handling/watermark-graph-2.png)

   In dit voorbeeld wordt geen beleid voor vervroegde aankomst toegepast. Uitschieter evenementen die vroeg aankomen verhogen het watermerk aanzienlijk. Let op de derde gebeurtenis (deviceId1 op tijd 12:11) wordt in dit scenario niet geschrapt en het watermerk wordt verhoogd tot 12:15. De vierde gebeurtenistijd wordt hierdoor 7 minuten (12:08 tot 12:15) naar voren bijgesteld.

3. In de laatste illustratie worden substreams gebruikt (OVER de DeviceId). Meerdere watermerken worden bijgehouden, één per stroom. Er zijn minder gebeurtenissen met hun tijden aangepast als gevolg.

   ![Afbeelding van watermerk Azure Stream Analytics](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Volgende stappen

- [Overwegingen voor gebeurtenisvolgorde van Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md)
- [Jobmetrics van Stream Analytics](stream-analytics-monitoring.md)
