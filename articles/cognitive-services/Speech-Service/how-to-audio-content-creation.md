---
title: Audio-inhoud maken - Spraakservice
titleSuffix: Azure Cognitive Services
description: Audio Content Creation is een online tool waarmee u de tekst-naar-spraak-uitvoer van Microsoft voor uw apps en producten aanpassen en verfijnen.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: ab0d2b8d95b4cb5996dd93fa0bb24085c9de26d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331533"
---
# <a name="improve-synthesis-with-audio-content-creation"></a>Verbeter de synthese met het maken van audio-inhoud

[Audio Content Creation](https://aka.ms/audiocontentcreation) is een online tool waarmee u de tekst-naar-spraak-uitvoer van Microsoft voor uw apps en producten aanpassen en verfijnen. U deze tool gebruiken om openbare en aangepaste stemmen te verfijnen voor nauwkeurigere natuurlijke expressies en uw uitvoer in de cloud te beheren.

De tool Voor het maken van audio-inhoud is gebaseerd op [de opmaaktaal voor spraaksynthese (SSML).](speech-synthesis-markup.md) Om het aanpassen en afstemmen te vereenvoudigen, u met Audio Content Creation uw tekst-naar-spraak-uitvoer in realtime visueel inspecteren.

## <a name="how-does-it-work"></a>Hoe werkt het?

Dit diagram toont de stappen die nodig zijn om aangepaste spraak-naar-tekst-uitvoer af te stemmen en te exporteren. Gebruik de onderstaande links voor meer informatie over elke stap.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. De eerste stap is het [maken van een Azure-account, het registreren van een Spraakbron en het krijgen van een abonnementssleutel.](#create-a-speech-resource) Nadat u een abonnementssleutel hebt, u deze gebruiken om de spraakservice te bellen en toegang te krijgen tot [het maken van audio-inhoud.](https://aka.ms/audiocontentcreation)
2. [Maak een audiotuningbestand](#create-an-audio-tuning-file) met platte tekst of SSML.
3. Kies de stem en de taal die u wilt afstemmen. Audio Content Creation bevat alle [tekst-naar-spraakstemmen](language-support.md#text-to-speech)van Microsoft. Je standaard, neurale of je eigen aangepaste stem gebruiken.
   >[!NOTE]
   > Gated-toegang is beschikbaar voor Aangepaste neurale stemmen, waarmee u high-definition stemmen maken die vergelijkbaar zijn met natuurlijk klinkende spraak. Zie [Gating-proces](https://aka.ms/ignite2019/speech/ethics)voor meer informatie .

4. Controleer het standaardresultaat. Gebruik vervolgens het stemmiddel om uitspraak, toonhoogte, snelheid, intonatie, spraakstijl en meer aan te passen. Zie [Speech Synthesis Markup Language](speech-synthesis-markup.md)voor een volledige lijst met opties.
5. Sla [uw afgestemde audio op](#export-tuned-audio)en exporteer deze. Wanneer u de tuningtrack in het systeem opslaat, u blijven werken en herhalen op de uitvoer. Wanneer u tevreden bent met de uitvoer, u een taak voor het maken van audio maken met de exportfunctie. U de status van de exporttaak observeren en de uitvoer downloaden voor gebruik met uw apps en producten.
6. De laatste stap is het gebruik van de aangepaste stem in uw apps en producten.

## <a name="create-a-speech-resource"></a>Een spraakbron maken

Volg deze stappen om een spraakbron te maken en deze te verbinden met Speech Studio.

1. Volg deze instructies om u aan te [melden voor een Azure-account](get-started.md#new-resource) en [een spraakbron te maken.](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource) Zorg ervoor dat uw prijscategorie is ingesteld op **S0.** Als u een van de neurale stemmen gebruikt, moet u ervoor zorgen dat u uw resource maakt in een [ondersteund gebied.](regions.md#standard-and-neural-voices)
2. Meld u aan bij [het maken van audio-inhoud](https://aka.ms/audiocontentcreation).
3. Selecteer een bestaand project of klik op **Nieuw maken**.
4. U uw abonnement op elk gewenst moment wijzigen met de optie **Instellingen** in de bovenste navigatie.

## <a name="create-an-audio-tuning-file"></a>Een audioafstemmingsbestand maken

Er zijn twee manieren om je inhoud in de tool Voor het maken van audio-inhoud te krijgen.

**Optie 1:**

1. Nadat u zich hebt aangemeld bij [het maken van audio-inhoud,](https://aka.ms/audiocontentcreation)klikt u op **Audiotuning** om een nieuw audiotuningbestand te maken.
2. Wanneer het bewerkingsvenster wordt weergegeven, u maximaal 10.000 tekens invoeren.
3. Vergeet niet te sparen.

**Optie 2:**

1. Nadat u zich hebt aangemeld bij [het maken van audio-inhoud,](https://aka.ms/audiocontentcreation)klikt u op **Uploaden** om een of meer tekstbestanden te importeren. Zowel platte tekst als SSML worden ondersteund.
2. Wanneer u uw tekstbestanden uploadt, moet u ervoor zorgen dat de inhoud aan deze vereisten voldoet.

   | Eigenschap | Waarde / Notities |
   |----------|---------------|
   | Bestandsindeling | Platte tekst (.txt)<br/> SSML-tekst (.txt)<br/> Zip-bestanden worden niet ondersteund |
   | Coderingsindeling | UTF-8 |
   | Bestandsnaam | Elk bestand moet een unieke naam hebben. Duplicaten worden niet ondersteund. |
   | Tekstlengte | Tekstbestanden mogen niet meer dan 10.000 tekens bevatten. |
   | SSML-beperkingen | Elk SSML-bestand kan slechts één stuk SSML bevatten. |

### <a name="plain-text-example"></a>Voorbeeld van platte tekst

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>Voorbeeld van SSML-tekst

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Afgestemde audio exporteren

Nadat u uw audio-uitvoer hebt beoordeeld en tevreden bent met uw afstemming en aanpassing, u de audio exporteren.

1. Klik in het gereedschap [Voor het maken van audioinhoud](https://aka.ms/audiocontentcreation) op **Exporteren** om een taak voor het maken van audio te maken.
2. Kies de uitvoerindeling voor uw afgestemde audio. Hieronder vindt u een lijst met ondersteunde indelingen en voorbeeldsnelheden.
3. U de status van de taak weergeven op het tabblad **Taak exporteren.** Als de taak mislukt, raadpleegt u de gedetailleerde informatiepagina voor een volledig rapport.
4. Wanneer de taak is voltooid, is uw audio beschikbaar om te downloaden op het tabblad **Audiobibliotheek.**
5. Klik **op Downloaden**. Nu bent u klaar om uw op maat gemaakte audio te gebruiken in uw apps of producten.

### <a name="supported-audio-formats"></a>Ondersteunde audio-indelingen

| Indeling | 16 kHz samplerate | 24 kHz samplerate |
|--------|--------------------|--------------------|
| Wav | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| Mp3 | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="see-also"></a>Zie ook

* [Lange audio-API](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Spraakstudio](https://speech.microsoft.com)
