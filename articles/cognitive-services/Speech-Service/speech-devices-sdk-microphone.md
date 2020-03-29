---
title: Aanbevelingen voor sdk-microfoonarray voor spraakapparaten
titleSuffix: Azure Cognitive Services
description: Aanbevelingen voor de MICROFOONarray van Spraakapparaten SDK. Deze matrixgeometrieën worden aanbevolen voor gebruik met de Microsoft Audio Stack.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: a87bdd7a55036e8b70f0bc5816d2b587c1569202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168141"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Aanbevelingen voor SDK-microfoonvoorspraakapparaten

In dit artikel leert u hoe u een microfoonarray ontwerpt voor de Spraakapparaten SDK.

De SpraakherkenningsSDK werkt het beste met een microfoonarray die is ontworpen volgens de volgende richtlijnen, inclusief de microfoongeometrie en componentselectie. Er wordt ook begeleiding gegeven op het gebied van integratie en elektrische overwegingen.

## <a name="microphone-geometry"></a>Microfoongeometrie

De volgende matrixgeometrieën worden aanbevolen voor gebruik met de Microsoft Audio Stack. De locatie van geluidsbronnen en de afwijzing van omgevingsgeluid wordt verbeterd met een groter aantal microfoons met afhankelijkheden van specifieke toepassingen, gebruikersscenario's en de apparaatvormfactor.

|     | Cirkelvormig array |     | Lineaire array |     |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \#Mics | 7 | 4 | 4 | 2 |
| Geometrie | 6 Buitenste, 1 Centrum, Straal = 42,5 mm, Gelijkmatig verdeeld | 3 Buitenste, 1 Centrum, Straal = 42,5 mm, Gelijkmatig verdeeld | Lengte = 120 mm, Afstand = 40 mm | Afstand = 40 mm |

Microfoonkanalen moeten worden besteld op basis van de nummering die voor elke bovenstaande array wordt afgebeeld, die toeneemt van 0. De Microsoft Audio Stack vereist een extra referentiestream van audioweergave om echo-onderdrukking uit te voeren.

## <a name="component-selection"></a>Componentselectie

Microfooncomponenten moeten worden geselecteerd om een signaal zonder ruis en vervorming nauwkeurig te reproduceren.

De aanbevolen eigenschappen bij het selecteren van microfoons zijn:

| Parameter | Aanbevolen |
| --------- | ----------- |
| Snr | \>= 65 dB (1 kHz signaal 94 dBSPL, A-gewogen ruis) |
| Amplitude Matching | ± 1 dB bij 1 kHz |
| Fasematching | ± 2° bij 1 kHz |
| Akoestisch overbelastingspunt (AOP) | \>= 120 dBSPL (THD = 10%) |
| Bitsnelheid | Minimaal 24-bits |
| Samplefrequentie | Minimaal 16 kHz\* |
| Frequentiebereik | ± 3 dB, 200-8000 Hz zwevend masker\* |
| Betrouwbaarheid | Opslagtemperatuur -40°C tot 70°C<br />Bedrijfstemperatuur bereik -20°C tot 55°C |

\*_Hogere bemonsteringssnelheden of "bredere" frequentiebereiken kunnen nodig zijn voor hoogwaardige communicatietoepassingen (VoIP)-toepassingen_

Een goede componentselectie moet gepaard gaan met een goede elektro-akoestische integratie om te voorkomen dat de prestaties van de gebruikte componenten worden aangetast. Unieke use cases kunnen ook aanvullende eisen vereisen (bijvoorbeeld bedrijfstemperatuurbereiken).

## <a name="microphone-array-integration"></a>Integratie van microfoonarrays

De prestaties van de microfoonarray wanneer deze in een apparaat worden geïntegreerd, verschillen van de componentspecificatie. Het is belangrijk om ervoor te zorgen dat de microfoons goed op elkaar zijn afgestemd na integratie. Daarom moeten de prestaties van het apparaat gemeten na een vaste winst of EQ voldoen aan de volgende aanbevelingen:

| Parameter          | Aanbevolen                                        |
| ------------------ | -------------------------------------------------- |
| Snr                | \>63 dB (1 kHz signaal 94 dBSPL, A-gewogen ruis) |
| Uitvoergevoeligheid | -26 dBFS/Pa @ 1 kHz (aanbevolen)                  |
| Amplitude Matching | ± 2 dB, 200-8000 Hz                                |
| THD%\*             | ≤ 1%, 200-8000 Hz, 94 dBSPL, 5e orde             |
| Frequentiebereik | ± 6 dB, 200-8000 Hz zwevend masker\*\*              |

\*\*_Een lage vervormingsluidspreker is vereist om THD te meten (bijv. Neumann KH120)_

\*\*_"Bredere" frequentiebereiken kunnen nodig zijn voor hoogwaardige communicatie (VoIP)-toepassingen_

## <a name="speaker-integration-recommendations"></a>Aanbevelingen voor integratie van sprekers

Aangezien echo-onderdrukking noodzakelijk is voor spraakherkenningsapparaten die luidsprekers bevatten, worden aanvullende aanbevelingen gedaan voor luidsprekerselectie en -integratie.

| Parameter | Aanbevolen |
| --------- | ----------- |
| Lineairheidoverwegingen | Geen niet-lineaire verwerking na naverwijzing naar de luidspreker, anders is een op hardware gebaseerde loopback-referentiestream vereist |
| Luidspreker Loopback | Geleverd via WASAPI, private API's, aangepaste ALSA plug-in (Linux), of geleverd via firmware kanaal |
| THD% | 3e Octaafbanden minimaal 5e orde, 70 dBA-afspelen @ 0,8 m ≤ 6,3%, 315-500 Hz ≤ 5%, 630-5000 Hz |
| Echokoppeling aan microfoons | \>-10 dB TCLw met itu-T G.122 bijlage B.4-methode, genormaliseerd tot mic-niveau<br />TCLw = TCLwmeasured \+ (Gemeten niveau - Doeloutputgevoeligheid)<br />TCLw = TCLwmeasured \+ (Gemeten Niveau - (-26)) |

## <a name="integration-design-architecture"></a>Integratieontwerparchitectuur

De volgende richtlijnen voor architectuur zijn nodig bij het integreren van microfoons in een apparaat:

| Parameter | Aanbeveling |
| --------- | -------------- |
| Mic Port Gelijkenis | Alle microfoonpoorten hebben dezelfde lengte in array |
| Afmetingen micpoort | Poortgrootte Ø0,8-1,0 mm. Poortlengte / \< Poortdiameter 2 |
| Mic Sealing         | Afdichtingspakkingen uniform geïmplementeerd in stack-up. Beveel \> 70% compressieverhouding aan voor schuimpakkingen |
| Betrouwbaarheid van de microfoon     | Gaas moet worden gebruikt om stof en binnendringen te voorkomen (tussen PCB voor bodemgeporteerde microfoons en afdichtingspakking/bovendeksel) |
| Mic Isolatie       | Rubberen pakkingen en trillingsontkoppeling door structuur, met name voor het isoleren van trillingspaden dankzij geïntegreerde luidsprekers |
| Bemonsteringsklok      | Apparaat audio moet vrij zijn van jitter en drop-outs met een lage drift |
| Recordvermogen   | Het apparaat moet in staat zijn om individuele kanaalruwe stromen tegelijkertijd op te nemen |
| USB                 | Alle USB-audio-invoerapparaten moeten descriptoren instellen volgens de [REV3-spec van USB-audioapparaten](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Microfoongeometrie | Stuurprogramma's moeten [de beschrijvingen van de microfoonarray](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) correct implementeren |
| Vindbaarheid     | Apparaten mogen geen onvindbare of oncontroleerbare hardware, firmware of softwaregebaseerde niet-lineaire audioverwerkingsalgoritmen van derden hebben van/naar het apparaat |
| Opname-indeling      | Capture formaten moeten een minimale sampling rate van 16 kHz en aanbevolen 24-bits diepte gebruiken |

## <a name="electrical-architecture-considerations"></a>Elektrische architectuur overwegingen

Indien van toepassing kunnen arrays worden aangesloten op een USB-host (zoals een SoC waarop de Microsoft Audio Stack wordt uitgevoerd) en interfaces met spraakservices of andere toepassingen.

Hardwarecomponenten zoals PDM-to-TDM-conversie moeten ervoor zorgen dat het dynamisch bereik en de SNR van de microfoons behouden blijven in resamplers.

High-speed USB Audio Class 2.0 moet worden ondersteund in alle audio-MCU's om de nodige bandbreedte te bieden voor maximaal zeven kanalen bij hogere sample rates en bitdiepten.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over de SDK voor spraakapparaten](speech-devices-sdk.md)
