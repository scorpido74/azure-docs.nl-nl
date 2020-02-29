---
title: Audio content creation-Speech Service
titleSuffix: Azure Cognitive Services
description: Het maken van audio-inhoud is een online hulp programma waarmee u de tekst-naar-spraak-uitvoer van micro soft kunt aanpassen en verfijnen voor uw apps en producten.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: 4785356963e7df1d7b175ac21ceebc921f851ffb
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913647"
---
# <a name="improve-synthesis-with-audio-content-creation"></a>De synthese verbeteren met het maken van audio-inhoud

Het [maken van audio-inhoud](https://aka.ms/audiocontentcreation) is een online hulp programma waarmee u de tekst-naar-spraak-uitvoer van micro soft kunt aanpassen en verfijnen voor uw apps en producten. U kunt dit hulp programma gebruiken om open bare en aangepaste stemmen te verfijnen voor nauw keurige natuurlijke expressies en uw uitvoer in de cloud te beheren.

Het hulp programma voor het maken van de audio-inhoud is gebaseerd op [SSML (Speech synthese Markup Language)](speech-synthesis-markup.md). Om het aanpassen en afstemmen te vereenvoudigen, kunt u met het maken van audio-inhoud uw tekst-naar-spraak-uitvoer in realtime visueel controleren.

## <a name="how-does-it-work"></a>Hoe werkt het?

Dit diagram toont de stappen die nodig zijn voor het afstemmen en exporteren van aangepaste spraak-naar-tekst uitvoer. Gebruik de onderstaande koppelingen voor meer informatie over elke stap.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. De eerste stap bestaat [uit het maken van een Azure-account, het registreren van een spraak bron en het ophalen van een abonnements sleutel](#create-a-speech-resource). Nadat u een abonnements sleutel hebt, kunt u deze gebruiken om de spraak service aan te roepen en toegang te krijgen tot het [maken van audio-inhoud](https://aka.ms/audiocontentcreation).
2. [Maak een audio-afstemmings bestand](#create-an-audio-tuning-file) met onbewerkte tekst of SSML.
3. Kies de stem en de taal die u wilt afstemmen. Voor het maken van audio-inhoud [zijn alle micro soft tekst-naar-spraak-stemmen](language-support.md#text-to-speech)opgenomen. U kunt standaard, Neural of uw eigen aangepaste stem gebruiken.
   >[!NOTE]
   > Toegang voor gestuurde Neural is beschikbaar voor aangepaste stemmen op het niveau van een hoge definitie die vergelijkbaar is met spraak herkenning. Zie [beperking process](https://aka.ms/ignite2019/speech/ethics)(Engelstalig) voor meer informatie.

4. Controleer het standaard resultaat. Gebruik vervolgens het hulp programma voor het afstemmen om de uitspraak, de hoogte, de rente, de intonation, de stem stijl en meer aan te passen. Zie voor een volledige lijst met opties [spraak synthese Markup Language (Engelstalig](speech-synthesis-markup.md)).
5. [Uw afgestemde audio](#export-tuned-audio)opslaan en exporteren. Wanneer u de afstemmings track opslaat in het systeem, kunt u blijven werken en de uitvoer herhalen. Wanneer u tevreden bent met de uitvoer, kunt u een taak voor het maken van een audio maken met de export functie. U kunt de status van de export taak bekijken en de uitvoer downloaden voor gebruik met uw apps en producten.
6. De laatste stap is het gebruik van de aangepaste, afgestemde stem in uw apps en producten.

## <a name="create-a-speech-resource"></a>Een spraak bron maken

Volg deze stappen om een spraak bron te maken en deze te verbinden met Speech Studio.

1. Volg deze instructies om u aan te [melden voor een Azure-account](get-started.md#new-resource) en [een spraak bron te maken](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure). Zorg ervoor dat de prijs categorie is ingesteld op **s0**. Als u een van de Neural stemmen gebruikt, moet u ervoor zorgen dat u uw resource in een [ondersteunde regio](regions.md#standard-and-neural-voices)maakt.
2. Meld u aan voor het [maken van audio-inhoud](https://aka.ms/audiocontentcreation).
3. Selecteer een bestaand project of klik op **Nieuw maken**.
4. U kunt uw abonnement op elk gewenst moment wijzigen met de optie **instellingen** , die zich in de bovenste navigatie bevindt.

## <a name="create-an-audio-tuning-file"></a>Een audio-afstemmings bestand maken

Er zijn twee manieren om uw inhoud te verkrijgen in het hulp programma voor het maken van audio-inhoud.

**Optie 1:**

1. Nadat u zich hebt aangemeld voor het [maken van audio-inhoud](https://aka.ms/audiocontentcreation), klikt u op **geluid afstemmen** om een nieuw audio-afstemmings bestand te maken.
2. Wanneer het bewerkings venster wordt weer gegeven, kunt u Maxi maal 10.000 tekens invoeren.
3. Vergeet niet om op te slaan.

**Optie 2:**

1. Nadat u zich hebt aangemeld voor het [maken van audio-inhoud](https://aka.ms/audiocontentcreation), klikt u op **uploaden** om een of meer tekst bestanden te importeren. Zowel tekst zonder opmaak als SSML worden ondersteund.
2. Wanneer u uw tekst bestanden uploadt, moet u ervoor zorgen dat de inhoud voldoet aan deze vereisten.

   | Eigenschap | Waarde/opmerkingen |
   |----------|---------------|
   | Bestands indeling | Tekst zonder opmaak (.txt)<br/> SSML tekst (. txt)<br/> Zip-bestanden worden niet ondersteund |
   | Coderings indeling | UTF-8 |
   | Bestandsnaam | Elk bestand moet een unieke naam hebben. Dubbele waarden worden niet ondersteund. |
   | Tekst lengte | Tekst bestanden mogen niet langer zijn dan 10.000 tekens. |
   | SSML-beperkingen | Elk SSML-bestand kan slechts één SSML bevatten. |

### <a name="plain-text-example"></a>Voor beeld van tekst zonder opmaak

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>Voor beeld van SSML-tekst

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Afgestemde audio exporteren

Nadat u de audio-uitvoer hebt gecontroleerd en tevreden bent met uw afstemming en aanpassing, kunt u de audio exporteren.

1. Klik in het hulp programma voor het maken van de [audio-inhoud](https://aka.ms/audiocontentcreation) op **exporteren** om een taak voor het maken van een audio te maken.
2. Kies de uitvoer indeling voor uw afgestemde audio. Hieronder vindt u een lijst met ondersteunde indelingen en voorbeeld tarieven.
3. U kunt de status van de taak weer geven op het tabblad **taak exporteren** . Als de taak mislukt, raadpleegt u de pagina gedetailleerde informatie voor een volledig rapport.
4. Wanneer de taak is voltooid, kunt u de audio downloaden van het tabblad **audio bibliotheek** .
5. Klik op **Downloaden**. U bent nu klaar om uw aangepaste afgestemde audio in uw apps of producten te gebruiken.

### <a name="supported-audio-formats"></a>Ondersteunde audio-indelingen

| Indeling | sample frequentie van 16 kHz | 24 kHz sample frequentie |
|--------|--------------------|--------------------|
| WAV | riff-16khz-16-mono-PCM | riff-24khz-16-mono-PCM |
| MP3 | Audio-16khz-128kbitrate-mono-mp3 | Audio-24khz-160kbitrate-mono-mp3 |

## <a name="see-also"></a>Zie ook

* [Lange audio-API](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
