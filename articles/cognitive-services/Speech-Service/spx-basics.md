---
title: Basisbeginselen van de Speech CLI
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van het opdrachtregelprogramma Speech CLI om met de Spraakservice te werken zonder code en met minimale setup.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: e859ac13c72ed07d3f57da6e61fd6d9f827f0fca
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88854887"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Meer informatie over de basisbeginselen van de Speech CLI

In dit artikel leert u de basisgebruikspatronen van de Speech CLI, een opdrachtregelprogramma om de Spraakservice te gebruiken zonder code te schrijven. U kunt snel de belangrijkste functies van de Spraakservice testen zonder ontwikkelomgevingen te maken of code te schrijven om te controleren of op passende wijze aan uw gebruiksgevallen kan worden voldaan. Daarnaast is de Speech CLI gereed voor productie en kan deze worden gebruikt voor het automatiseren van eenvoudige werkstromen in de Spraakservice, met behulp van `.bat` of shell-scripts.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Basisgebruik

In deze sectie ziet u enkele eenvoudige SPX-opdrachten die vaak nuttig zijn als u voor de eerste keer gaat testen en experimenteren. Begin met het bekijken van de Help die is ingebouwd in het hulpprogramma door de volgende opdracht uit te voeren.

```shell
spx
```

Kijk ook naar de helponderwerpen onder **Zie:** rechts van de opdrachtparameters. U kunt deze opdrachten invoeren om gedetailleerde helpinformatie over subopdrachten te krijgen.

U kunt helponderwerpen doorzoeken op trefwoord. Voer bijvoorbeeld de volgende opdracht in om een lijst met gebruiksvoorbeelden van de Spraak-CLI te bekijken:

```shell
spx help find --topics "examples"
```

Voer de volgende opdracht in om de opties voor de opdracht 'herkennen' te bekijken:

```shell
spx help recognize
```

Gebruik nu de spraak-service om de volgende opdracht uit te voeren om te beginnen met spraakherkenning met behulp van de standaardmicrofoon.

```shell
spx recognize --microphone
```

Nadat u de opdracht hebt ingevoerd, luistert SPX op het huidige actieve invoerapparaat naar de audio en stopt wanneer u op `ENTER` drukt. De opgenomen spraak wordt vervolgens herkend en geconverteerd naar tekst in de console-uitvoer. Tekst-naar-spraak-synthese is ook eenvoudig met de Speech CLI. 

Als u de volgende opdracht uitvoert, wordt de ingevoerde tekst als invoer genomen en wordt de gesynthetiseerde spraak uitgevoerd naar het huidige actieve uitvoerapparaat.

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Naast spraakherkenning en synthese kunt u ook spraakomzetting uitvoeren met de Speech CLI. Net als bij de opdracht voor spraakherkenning hierboven voert u de volgende opdracht uit om audio van de standaardmicrofoon vast te leggen en voert u een omzetting naar tekst uit in de doeltaal.

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

In deze opdracht geeft u zowel de brontaal (taal om **vanaf** om te zetten) op als de doeltaal (de taal om **naar** om te zetten). Als u het argument `--microphone` gebruikt, wordt geluisterd naar audio op het huidige actieve invoerapparaat en wordt gestopt u nadat u op `ENTER` hebt gedrukt. De uitvoer is een tekstomzetting naar de doeltaal, geschreven naar een tekstbestand.

> [!NOTE]
> Zie het artikel [Taal en landinstelling](language-support.md) voor een lijst met alle ondersteunde talen met de bijbehorende landinstellingcodes.

## <a name="batch-operations"></a>Batchbewerkingen

De opdrachten in de vorige sectie zijn handig om snel te zien hoe de Spraakservice werkt. Wanneer u wilt beoordelen of wel of niet aan uw gebruiksgevallen kan worden voldaan, moet u echter waarschijnlijk batchbewerkingen uitvoeren op basis van een bereik met ingevoerde gegevens. Zo kunt u zien hoe de service een verscheidenheid aan scenario's afhandelt. In deze sectie leest u het volgende:

* Batchgewijs spraakherkenning uitvoeren op een map met audiobestanden
* Een `.tsv`-bestand herhalen en batchgewijs tekst-naar-spraak-synthese uitvoeren

## <a name="batch-speech-recognition"></a>Batchgewijze spraakherkenning

Als u een map met audiobestanden hebt, kunt u met de Speech CLI eenvoudig en snel batchgewijze spraakherkenning uitvoeren. Voer eenvoudig de volgende opdracht uit en wijs naar uw map met de opdracht `--files`. In dit voorbeeld voegt u `\*.wav` toe aan de map om alle `.wav`-bestanden in de map te herkennen. Geef daarnaast het argument `--threads` op om de herkenning uit te voeren op tien parallelle threads.

> [!NOTE]
> Het argument `--threads` kan ook worden gebruikt in de volgende sectie voor `spx synthesize`-opdrachten; de beschikbare threads zijn afhankelijk van de CPU en het huidige belastingspercentage.

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

De herkende spraakuitvoer wordt naar `speech_output.tsv` geschreven met behulp van het argument `--output file`. Hierna volgt een voorbeeld van de bestandsstructuur van de uitvoer.

```output
audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-synthesis"></a>Batchgewijze tekst-naar-spraak-synthese

De eenvoudigste manier om batchgewijze tekst-naar-spraak uit te voeren, is door een nieuw `.tsv`-bestand (door tabs gescheiden waarden) te maken en in de Speech CLI gebruik te maken van de opdracht `--foreach`. Kijk eens naar het volgende bestand `text_synthesis.tsv`:

```output
audio.output    text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

 Vervolgens voert u een opdracht uit om naar `text_synthesis.tsv` te verwijzen, voert u synthese uit op elk `text`-veld en schrijft u het resultaat naar het overeenkomstige `audio.output`-pad als een `.wav`-bestand. 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Deze opdracht is gelijk aan het uitvoeren van `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav` **voor elk** record in het bestand `.tsv`. Enkele opmerkingen:

* De kolomkoppen, `audio.output` en `text`, komen overeen met respectievelijk de opdrachtregelargumenten `--audio output` en `--text`. Opdrachtregelargumenten met meerdere delen, zoals `--audio output`, moeten in het bestand worden opgemaakt zonder spaties, geen voorloopstreepjes en punten die tekenreeksen scheiden, bijvoorbeeld `audio.output`. Andere bestaande opdrachtregelargumenten kunnen worden toegevoegd aan het bestand als aanvullende kolommen met dit patroon.
* Wanneer het bestand op deze manier wordt geformatteerd, hoeven er geen aanvullende argumenten te worden doorgegeven aan `--foreach`.
* Vergeet niet elke waarde in het `.tsv` te scheiden door een **tab**.

Als u echter een `.tsv`-bestand hebt, zoals in het volgende voorbeeld, met kolomkoppen die **niet overeenkomen met** opdrachtregelargumenten:

```output
wav_path    str_text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

U kunt deze veldnamen vervangen door de juiste argumenten met behulp van de volgende syntaxis in de aanroep `--foreach`. Dit is dezelfde aanroep als hierboven.

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Volgende stappen

* Voltooi de quickstarts over [spraakherkenning](./quickstarts/speech-to-text-from-microphone.md) of [spraaksynthese](./quickstarts/text-to-speech.md) met behulp van de SDK.
