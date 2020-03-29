---
title: Gesprek met meerdere apparaten (voorbeeld) - Spraakservice
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: b3802e66b0ba5a68c898e69ec64b01edce1541c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371355"
---
# <a name="what-is-multi-device-conversation-preview"></a>Wat is Multi-device Conversation (Preview)?

**Met een gesprek met meerdere apparaten** u eenvoudig een spraak- of tekstgesprek tussen meerdere clients maken en de berichten die tussen deze klanten worden verzonden, coördineren.

Met **multi-device gesprek,** u:

- Verbind meerdere clients in hetzelfde gesprek en beheer het verzenden en ontvangen van berichten tussen hen.
- Transcribeer eenvoudig audio van elke client en stuur de transcriptie naar de anderen, met optionele vertaling.
- Eenvoudig sms'jes verzenden tussen clients, met optionele vertaling.

U een functie of oplossing bouwen die werkt op een reeks apparaten. Elk apparaat kan onafhankelijk berichten (transcripties van audio of chatberichten) naar alle andere apparaten verzenden.

Terwijl [**Conversation Transcription**](conversation-transcription.md) werkt op één apparaat met een multichannel microfoonarray, is **Multi-device Conversation** geschikt voor scenario's met meerdere apparaten, elk met één microfoon.

>[!IMPORTANT]
> Multi-device conversation biedt **geen** ondersteuning voor het verzenden van audiobestanden tussen clients: alleen de transcriptie en/of vertaling.

## <a name="key-features"></a>Belangrijke functies

- **Real-time transcriptie** - Iedereen ontvangt een transcript van het gesprek, zodat ze kunnen volgen langs de tekst in real-time of op te slaan voor later.
- **Real-time vertaling** – Met meer dan 60 [ondersteunde talen](language-support.md#text-languages) voor tekstvertaling kunnen gebruikers het gesprek vertalen naar hun voorkeurstaal(en).
- **Leesbare transcripties** - De transcriptie en vertaling zijn gemakkelijk te volgen, met interpunctie en zinspauzes.
- **Spraak- of tekstinvoer** – Elke gebruiker kan spreken of typen op zijn eigen apparaat, afhankelijk van de taalondersteuningsmogelijkheden die zijn ingeschakeld voor de gekozen taal van de deelnemer. Raadpleeg [taalondersteuning.](language-support.md#speech-to-text)
- **Berichtrelay** - De gespreksservice voor meerdere apparaten distribueert berichten die door de ene client worden verzonden naar alle anderen, in de taal(en) van hun keuze.
- **Berichtidentificatie** : elk bericht dat gebruikers in het gesprek ontvangen, wordt getagd met de bijnaam van de gebruiker die het bericht heeft verzonden.

## <a name="use-cases"></a>Gebruiksvoorbeelden

### <a name="lightweight-conversations"></a>Lichtgewicht gesprekken

Het maken en deelnemen aan een gesprek is eenvoudig. Eén gebruiker treedt op als de 'host' en maakt een gesprek, dat een willekeurige gesprekscode van vijf letters en een QR-code genereert. Alle andere gebruikers kunnen deelnemen aan het gesprek door de gesprekscode in te typen of de QR-code te scannen. 

Aangezien gebruikers deelnemen via de gesprekscode en geen contactgegevens hoeven te delen, is het eenvoudig om snelle gesprekken ter plaatse te maken.

### <a name="inclusive-meetings"></a>Inclusieve vergaderingen

Real-time transcriptie en vertaling kan helpen om gesprekken toegankelijk te maken voor mensen die verschillende talen spreken en/of doof of slechthorend zijn. Elke persoon kan ook actief deelnemen aan het gesprek, door het spreken van hun voorkeurstaal of het verzenden van chatberichten.

### <a name="presentations"></a>Presentaties

U ook bijschriften voor presentaties en lezingen zowel op het scherm als op de eigen apparaten van de leden van het publiek verstrekken. Nadat het publiek deelneemt aan de gesprekscode, kunnen ze het transcript in hun voorkeurstaal zien, op hun eigen apparaat.

> [!NOTE]
> Als u een voorbeeld wilt zien, raadpleegt u [Presentation Translator](https://www.microsoft.com/translator/apps/presentation-translator/), een PowerPoint-invoegtoepassing die gebruikmaakt van de gespreksservice voor meerdere apparaten. U kunt het artikel [hier](https://www.microsoft.com/download/details.aspx?id=55024) downloaden.

## <a name="how-it-works"></a>Hoe werkt het?

Alle clients gebruiken de Speech SDK om een gesprek te maken of deel te nemen. De SpraakSDK werkt samen met de gespreksservice voor meerdere apparaten, die de levensduur van een gesprek beheert, inclusief de lijst met deelnemers, de gekozen taal(en) van elke klant en verzonden berichten.  

Elke client kan audio- of chatberichten verzenden. De service gebruikt spraakherkenning om audio om te zetten in tekst en chatberichten te verzenden zoals deze is. Als clients verschillende talen kiezen, vertaalt de service alle berichten naar de opgegeven taal(en) van elke client.

![Overzichtsdiagram voor gesprekken met meerdere apparaten](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Overzicht van gesprek, gastheer en deelnemer

Een **gesprek** is een sessie waarbij de ene gebruiker begint voordat de andere deelnemende gebruikers kunnen deelnemen. Alle clients maken verbinding met het gesprek met behulp van de **gesprekscode**van vijf letters.

Elk gesprek maakt metagegevens met:
-    Tijdstempels van wanneer het gesprek begon en eindigde
-    Lijst van alle deelnemers aan het gesprek, die de gekozen bijnaam en de primaire taal van elke gebruiker voor spraak- of tekstinvoer bevat.


Er zijn twee soorten gebruikers in een gesprek: **host** en **deelnemer.**

De **host** is de gebruiker die een gesprek start en die optreedt als beheerder van dat gesprek.
- Elk gesprek kan slechts één host hebben
- De host moet gedurende de duur van het gesprek verbonden zijn met het gesprek. Als de gastheer het gesprek verlaat, eindigt het gesprek voor alle andere deelnemers.
- De host heeft een paar extra besturingselementen om het gesprek te beheren: 
    - Het gesprek vergrendelen - voorkomen dat extra deelnemers lid worden
    - Alle deelnemers dempen - voorkomen dat andere deelnemers berichten naar het gesprek sturen, of ze nu zijn getranscribeerd vanuit spraak of chatberichten
    - Individuele deelnemers dempen
    - Alle deelnemers dempen opheffen
    - Afzonderlijke deelnemers dempen opheffen

Een **deelnemer** is een gebruiker die deelneemt aan een gesprek.
- Een deelnemer kan op elk moment hetzelfde gesprek verlaten en weer aansluiten, zonder het gesprek voor andere deelnemers te beëindigen.
- Deelnemers kunnen het gesprek niet vergrendelen of anderen dempen/dempen

> [!NOTE]
> Elk gesprek kan maximaal 100 deelnemers hebben, waarvan er 10 tegelijkertijd op een bepaald moment kunnen spreken.

## <a name="language-support"></a>Taalondersteuning

Bij het maken of deelnemen aan een gesprek moet elke gebruiker een **primaire taal**kiezen: de taal waarin hij of zij spreekt en chatberichten verzendt, en ook de taal waarin hij of zij de berichten van andere gebruikers ziet.

Er zijn twee soorten talen: **spraak-naar-tekst** en **tekst-only:**
- Als de gebruiker een **spraak-naar-teksttaal** als primaire taal kiest, kunnen ze zowel spraak- als tekstinvoer gebruiken in het gesprek.

- Als de gebruiker een **taal met alleen tekst** kiest, kan hij alleen tekstinvoer gebruiken en chatberichten verzenden in het gesprek. Teksttalen zijn de talen die worden ondersteund voor tekstvertaling, maar geen spraak-naar-tekst. U beschikbare talen bekijken op de [pagina taalondersteuning.](supported-languages.md)

Naast zijn primaire taal kan elke deelnemer ook extra talen opgeven voor het vertalen van het gesprek.

Hieronder vindt u een overzicht van wat de gebruiker kan doen in een gesprek met meerdere apparaten, op basis van de gekozen primaire taal.


| Wat de gebruiker kan doen in het gesprek | Spraak naar tekst | Alleen tekst |
|-----------------------------------|----------------|------|
| Spraakinvoer gebruiken | ✔️ | ❌ |
| Chatberichten verzenden | ✔️ | ✔️ |
| Het gesprek vertalen | ✔️ | ✔️ |

> [!NOTE]
> Zie [ondersteunde talen](supported-languages.md)voor lijsten met beschikbare taalen voor spraak-naar-tekst en tekstvertaling.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gesprekken in realtime vertalen](quickstarts/multi-device-conversation.md)
