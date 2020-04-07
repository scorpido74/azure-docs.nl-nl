---
title: Prestatiesporen aan clientzijde maken
description: Aanbevolen procedures voor prestatieprofilering aan de clientzijde met WPF
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1f4207a11f3ae3664023fccf6178b6db7cf253b9
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681309"
---
# <a name="create-client-side-performance-traces"></a>Prestatiesporen aan clientzijde maken

Er zijn vele redenen waarom de prestaties van Azure Remote Rendering mogelijk niet zo goed zijn als gewenst. Naast pure rendering prestaties op de cloud server, vooral de kwaliteit van de netwerkverbinding heeft een aanzienlijke invloed op de ervaring. Als u de prestaties van de server wilt profileren, raadpleegt u [prestatiequery's aan de serverzijde](../overview/features/performance-queries.md)van het hoofdstuk .

Dit hoofdstuk richt zich op het identificeren van potentiële knelpunten aan de klantzijde door middel *van prestatiesporen.*

## <a name="getting-started"></a>Aan de slag

Als u nieuw bent in de Windows-functionaliteit voor het traceren van prestaties, worden in dit gedeelte de meest fundamentele termen en toepassingen vermeld om u op weg te helpen.

### <a name="installation"></a>Installeren

De toepassingen die worden gebruikt om tracering te doen met ARR zijn tools voor algemeen gebruik die kunnen worden gebruikt voor alle Windows-ontwikkeling. Ze worden geleverd via de [Windows Performance Toolkit.](https://docs.microsoft.com/windows-hardware/test/wpt/) Download de [Windows Assessment and Deployment Kit](https://docs.microsoft.com/windows-hardware/get-started/adk-install)om deze toolkit te downloaden.

### <a name="terminology"></a>Terminologie

Bij het zoeken naar informatie over prestatiesporen, zult u onvermijdelijk komen over een scala van termen. De belangrijkste zijn:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW** staat voor [ **E**vent **T**racing voor **W**indows](https://docs.microsoft.com/windows/win32/etw/about-event-tracing). Het is gewoon de overkoepelende naam voor de efficiënte kernel-level tracing faciliteit die is ingebouwd in Windows. Het wordt *event* tracing genoemd, omdat toepassingen die ETW ondersteunen gebeurtenissen uitzenden om acties te registreren die kunnen helpen om prestatieproblemen op te sporen. Standaard zendt het besturingssysteem al gebeurtenissen uit voor zaken als schijftoegangen, taakswitches en dergelijke. Toepassingen zoals ARR zenden bovendien aangepaste gebeurtenissen uit, bijvoorbeeld over gedropte frames, netwerklag etc.

**ETL** staat voor **E**vent **T**race **L**ogging. Het betekent gewoon dat een spoor is verzameld (ingelogd) en wordt daarom meestal gebruikt als de bestandsextensie voor bestanden die de traceringsgegevens opslaan. Dus als je een trace maakt, \*heb je meestal achteraf een .etl-bestand.

**WPR** staat voor [ **W**indows **P**erformance **R**ecorder](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) en is de naam van de applicatie die de registratie van gebeurtenissporen start en stopt. WPR neemt een\*profielbestand (.wprp) dat configureert welke exacte gebeurtenissen moeten worden logboeken. Een `wprp` dergelijk bestand is voorzien van de ARR SDK. Wanneer u sporen op een desktop-pc doet, u WPR rechtstreeks starten. Wanneer u een trace op de HoloLens doet, gaat u meestal via de webinterface.

**WPA** staat voor [ **W**indows **P**erformance **A**nalyzer](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) en is \*de naam van de GUI-toepassing die wordt gebruikt om .etl-bestanden te openen en de gegevens te doorzoeken om prestatieproblemen te identificeren. Met WPA u gegevens sorteren op verschillende criteria, de gegevens op verschillende manieren weergeven, in details graven en informatie correleren.

Hoewel ETL-sporen kunnen worden gemaakt op elk Windows-apparaat (lokale pc, HoloLens, cloudserver, enz.), worden ze meestal opgeslagen op schijf en geanalyseerd met WPA op een desktop-pc. ETL-bestanden kunnen worden verzonden naar andere ontwikkelaars voor hen om een kijkje te nemen. Houd er rekening mee dat gevoelige informatie, zoals bestandspaden en IP-adressen, kan worden vastgelegd in ETL-sporen. U ETW op twee manieren gebruiken: om sporen op te nemen of om sporen te analyseren. Het opnemen van sporen is rechttoe rechtaan en vereist een minimale setup. Het analyseren van sporen aan de andere kant vereist een fatsoenlijk begrip van zowel de WPA tool en het probleem dat u onderzoekt. Algemeen materiaal voor het leren van WPA zal hieronder worden gegeven, evenals richtlijnen voor het interpreteren van ARR-specifieke sporen.

## <a name="recording-a-trace-on-a-local-pc"></a>Een trace opnemen op een lokale pc

Om ARR-prestatieproblemen te identificeren, moet u liever rechtstreeks een trace op een HoloLens doen, want dat is de enige manier om een momentopname van de werkelijke prestatiekenmerken te krijgen. Als u echter specifiek een spoor wilt maken zonder de HoloLens-prestatiebeperkingen of als u gewoon wilt leren hoe u WPA moet gebruiken en geen realistisch spoor nodig hebt, u dit als geen realistisch onderzoek uitvoeren.

### <a name="wpr-configuration"></a>WPR-configuratie

1. Start de [Windows Performance Recorder](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) in het *startmenu.*
1. **Meer opties** uitbreiden
1. Klik **op Profielen toevoegen...**
1. Selecteer het bestand *AzureRemoteRenderingNetworkProfiling.wprp*. U dit bestand vinden in de ARR SDK onder *Extra/ETLProfiles*.
   Het profiel wordt nu vermeld in WPR onder *Aangepaste metingen*. Zorg ervoor dat dit het enige ingeschakelde profiel is.
1. Uitbreiden *Eerste niveau triage:*
    * Als u alleen maar een snel spoor van de ARR-netwerkgebeurtenissen wilt vastleggen, schakelt u deze optie **uit.**
    * Als u ARR-netwerkgebeurtenissen moet correleren met andere systeemkenmerken, zoals CPU- of geheugengebruik, schakelt u deze optie **in.**
    * Als u deze optie inschakelt, zal het spoor waarschijnlijk meerdere gigabytes groot zijn en duurt het lang om op te slaan en te openen in WPA.

Daarna moet uw WPR-configuratie er als volgt uitzien:

![WPR-configuratie](./media/wpr.png)

### <a name="recording"></a>Opnemen

Klik **op Start** om een trace op te nemen. U de opname op elk gewenst moment starten en stoppen; u hoeft uw aanvraag niet te sluiten voordat u dit doet. Zoals u zien hoeft u niet aan te geven welke toepassing moet worden getraceerd, omdat ETW altijd een trace voor het hele systeem registreert. Het `wprp` bestand geeft aan welke typen gebeurtenissen moeten worden geregistreerd.

Klik **op Opslaan** om de opname te stoppen en geef op waar het ETL-bestand moet worden opgeslagen.

U hebt nu een ETL-bestand dat u rechtstreeks in WPA openen of naar iemand anders verzenden.

## <a name="recording-a-trace-on-a-hololens"></a>Een spoor opnemen op een HoloLens

Als u een trace op een HoloLens wilt opnemen, start u het apparaat op en voert u het IP-adres in een browser in om de Apparaatportal te *openen.*

![Apparaatportal](./media/wpr-hl.png)

1. Navigeer aan de linkerkant naar *Prestaties > Prestatietracering*.
1. **Aangepaste profielen selecteren**
1. Klik **op Bladeren...**
1. Selecteer het bestand *AzureRemoteRenderingNetworkProfiling.wprp*. U dit bestand vinden in de ARR SDK onder *Extra/ETLProfiles*.
1. Klik **op Traceerstarten**
1. De HoloLens is nu bezig met het opnemen van een spoor. Zorg ervoor dat u de prestatieproblemen activeert die u wilt onderzoeken. Klik vervolgens op **Trace stoppen.**
1. Het spoor wordt onderaan de webpagina weergegeven. Klik op het schijfpictogram aan de rechterkant om het ETL-bestand te downloaden.

U hebt nu een ETL-bestand dat u rechtstreeks in WPA openen of naar iemand anders verzenden.

## <a name="analyzing-traces-with-wpa"></a>Traceersporen analyseren met WPA

### <a name="wpa-basics"></a>WPA-basisprincipes

Windows Performance Analyzer is de standaardtool om ETL-bestanden te openen en de sporen te inspecteren. Een uitleg hoe WPA werkt is buiten het bereik van dit artikel. Om aan de slag te gaan, bekijk deze bronnen:

* Bekijk de [introductievideo's](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) voor een eerste overzicht.
* WPA zelf heeft een *tabblad Aan de slag,* waarin algemene stappen worden uitgelegd. Bekijk de beschikbare onderwerpen. Vooral onder "Gegevens bekijken" krijg je een snelle introductie hoe je grafieken maakt voor specifieke gegevens.
* Er is uitstekende informatie [op deze website,](https://randomascii.wordpress.com/2015/09/24/etw-central/)echter, niet alles is relevant voor beginners.

### <a name="graphing-data"></a>Grafiekgegevens

Om te beginnen met ARR tracing, de volgende stukken zijn goed om te weten.

![Prestatiegrafiek](./media/wpa-graph.png)

De afbeelding hierboven toont een tabel met traceringsgegevens en een grafiekweergave van dezelfde gegevens.

Let in de tabel onderaan op de gele (gouden) balk en de blauwe balk. U deze balken slepen en op elke positie plaatsen.

Alle **kolommen links van de gele balk** worden geïnterpreteerd als **toetsen**. Toetsen worden gebruikt om de structuur van de boom in de linkerbovenhoek venster. Hier hebben we twee *belangrijke* kolommen, "Provider Naam" en "Taak naam". Bijgevolg is de boomstructuur in het venster linksboven twee niveaus diep. Als u de volgorde van de kolommen wijzigt of kolommen toevoegt of verwijdert uit het sleutelgebied, verandert de structuur in de structuurweergave.

**Kolommen rechtsonder van de blauwe balk** worden gebruikt voor de **grafiekweergave** in het venster rechtsboven. Meestal wordt alleen de eerste kolom gebruikt, maar voor sommige grafiekmodi zijn meerdere kolommen met gegevens vereist. Als lijngrafieken werken, moet de *aggregatiemodus* op die kolom worden ingesteld. Gebruik 'Avg' of 'Max'. De aggregatiemodus wordt gebruikt om de waarde van de grafiek op een bepaalde pixel te bepalen, wanneer een pixel een bereik met meerdere gebeurtenissen bestrijkt. Dit kan worden waargenomen door aggregatie in te stellen op 'Som' en vervolgens in en uit te zoomen.

De kolommen in het midden hebben geen speciale betekenis.

![Weergave gebeurtenissen](./media/wpa-event-view.png)

In de *Editor voor de weergave algemene gebeurtenissen* u alle kolommen configureren die moeten worden weergegeven, de aggregatiemodus, sorteren en welke kolommen worden gebruikt als toetsen of voor grafieken. In het bovenstaande voorbeeld is **Veld 2** ingeschakeld en veld 3 - 6 uitgeschakeld. Veld 2 is meestal het eerste *aangepaste gegevensveld* van een ETW-gebeurtenis en dus voor ARR-gebeurtenissen voor "FrameStatistics", die een aantal netwerklatentiewaarde vertegenwoordigen. Andere kolommen 'Veld' inschakelen om verdere waarden van deze gebeurtenis te zien.

### <a name="presets"></a>Voorinstellingen

Om een trace goed te analyseren, moet u uw eigen workflow en voorkeursgegevensweergeven achterhalen. Om echter snel een overzicht te krijgen van de ARR-specifieke gebeurtenissen, nemen we het profiel van het Windows Software Protection Platform en voorinstellingen in de map *Tools/ETLProfiles*op. Als u een volledig profiel wilt laden, selecteert u *Profielen > Toepassen...* in de WPA-menubalk of opent u het deelvenster *Mijn voorinstellingen* *(Venster > Mijn voorinstellingen)* en selecteert *u Importeren*. De eerste zal het opzetten van een volledige WPA configuratie zoals in de afbeelding hieronder. Deze laatste maakt alleen presets voor de verschillende weergaveconfiguraties beschikbaar en stelt u in staat om snel een weergave te openen om een specifiek stuk ARR-gebeurtenisgegevens te bekijken.

![Voorinstellingen](./media/wpa-arr-trace.png)

De afbeelding hierboven toont weergaven van verschillende ARR-specifieke gebeurtenissen plus een weergave van het totale CPU-gebruik.

## <a name="next-steps"></a>Volgende stappen

* [Prestatiequery's aan de serverzijde](../overview/features/performance-queries.md)
