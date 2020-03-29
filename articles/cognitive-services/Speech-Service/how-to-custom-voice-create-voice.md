---
title: Een aangepaste spraakservice maken - Spraakservice
titleSuffix: Azure Cognitive Services
description: Wanneer u klaar bent om uw gegevens te uploaden, gaat u naar de Aangepaste Voice-portal. Een aangepast spraakproject maken of selecteren. Het project moet de juiste taal/landint en de gendereigenschappen delen als de gegevens die u wilt gebruiken voor uw spraaktraining.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: bbe1d651a7d2d2cac1b1aa78b815b2797ad185c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717323"
---
# <a name="create-a-custom-voice"></a>Een aangepaste stem maken

In [Gegevens voorbereiden voor aangepaste spraak](how-to-custom-voice-prepare-data.md)hebben we de verschillende gegevenstypen beschreven die u gebruiken om een aangepaste stem en de verschillende indelingsvereisten te trainen. Zodra u uw gegevens hebt voorbereid, u beginnen ze te uploaden naar de [Aangepaste Voice-portal](https://aka.ms/custom-voice-portal)of via de Aangepaste Voice-trainings-API. Hier beschrijven we de stappen van het trainen van een aangepaste stem via het portaal.

> [!NOTE]
> Op deze pagina wordt ervan uitgegaan dat u aan de slag bent [gegaan met Aangepaste spraak](how-to-custom-voice.md) en gegevens voorbereiden voor aangepaste [spraak](how-to-custom-voice-prepare-data.md)en een aangepast spraakproject hebt gemaakt.

Controleer de talen die worden ondersteund voor aangepaste spraak: [taal voor aanpassing](language-support.md#customization).

## <a name="upload-your-datasets"></a>Uw gegevenssets uploaden

Wanneer u klaar bent om uw gegevens te uploaden, gaat u naar de [aangepaste voice-portal](https://aka.ms/custom-voice-portal). Een aangepast spraakproject maken of selecteren. Het project moet de juiste taal/landint en de gendereigenschappen delen als de gegevens die u wilt gebruiken voor uw spraaktraining. Selecteer `en-GB` bijvoorbeeld of de audio-opnamen die u hebt, in het Engels worden uitgevoerd met een Brits accent.

Ga naar het tabblad **Gegevens** en klik op **Gegevens uploaden**. Selecteer in de wizard het juiste gegevenstype dat overeenkomt met wat u hebt voorbereid.

Elke gegevensset die u uploadt, moet voldoen aan de vereisten voor het gegevenstype dat u kiest. Het is belangrijk om uw gegevens correct op te maken voordat deze worden geüpload. Dit zorgt ervoor dat de gegevens nauwkeurig worden verwerkt door de Custom Voice-service. Ga naar [Gegevens voorbereiden voor aangepaste spraak](how-to-custom-voice-prepare-data.md) en zorg ervoor dat uw gegevens correct zijn opgemaakt.

> [!NOTE]
> Gratis abonnement (F0) gebruikers kunnen twee datasets tegelijk uploaden. S0-gebruikers met een standaardabonnement (S0) kunnen vijf gegevenssets tegelijk uploaden. Als u de limiet bereikt, wacht u tot ten minste één van uw gegevenssets is voltooid. Probeer het dan opnieuw.

> [!NOTE]
> Het maximum aantal gegevenssets dat per abonnement mag worden geïmporteerd, is 10 .zip-bestanden voor gratis abonnementsgebruikers (F0) en 500 voor gebruikers met een standaardabonnement (S0).

Gegevenssets worden automatisch gevalideerd zodra u op de uploadknop drukt. Gegevensvalidatie omvat reekscontroles op de audiobestanden om de bestandsindeling, -grootte en -samplingsnelheid te verifiëren. Los de eventuele fouten op en verzend opnieuw. Wanneer de aanvraag voor het importeren van gegevens is gestart, ziet u een vermelding in de gegevenstabel die overeenkomt met de gegevensset die u zojuist hebt geüpload.

In de volgende tabel worden de verwerkingsstatussen voor geïmporteerde gegevenssets weergegeven:

| Status | Betekenis |
| ----- | ------- |
| Wordt verwerkt | Uw gegevensset is ontvangen en wordt verwerkt. |
| Geslaagd | Uw gegevensset is gevalideerd en kan nu worden gebruikt om een spraakmodel te maken. |
| Mislukt | Uw gegevensset is mislukt tijdens de verwerking vanwege vele redenen, bijvoorbeeld bestandsfouten, gegevensproblemen of netwerkproblemen. |

Nadat de validatie is voltooid, u het totale aantal overeenkomende uitingen voor elk van uw gegevenssets zien in de kolom **Utterances.** Als het geselecteerde gegevenstype een lange audiosegmentatie vereist, geeft deze kolom alleen de uitingen weer die we voor u hebben gesegmenteerd op basis van uw transcripties of via de spraaktranscriptieservice. U de gevalideerde gegevensset verder downloaden om de detailresultaten van de geïmporteerde uitingen en de toewijzingstranscripties ervan weer te geven. Tip: lange audio-segmentatie kan meer dan een uur duren om de gegevensverwerking te voltooien.

Voor en-US en zh-CN datasets u verder een rapport downloaden om de uitspraakscores en het geluidsniveau voor elk van uw opnamen te controleren. De uitspraakscore varieert van 0 tot 100. Een score onder de 70 duidt normaal gesproken op een spraakfout of scriptmismatch. Een zwaar accent kan uw uitspraakscore verminderen en de gegenereerde digitale stem beïnvloeden.

Een hogere signaal-ruisverhouding (SNR) geeft minder ruis in uw audio aan. U meestal een 50 + SNR bereiken door op te nemen in professionele studio's. Audio met een SNR onder de 20 kan resulteren in duidelijke ruis in uw gegenereerde stem.

Overweeg om uitingen met lage uitspraakscores of slechte signaal-ruisverhoudingen opnieuw op te nemen. Als u deze uitingen niet opnieuw opnemen, u deze uitingen uitsluiten van uw gegevensset.

## <a name="build-your-custom-voice-model"></a>Bouw uw aangepaste spraakmodel

Nadat uw gegevensset is gevalideerd, u deze gebruiken om uw aangepaste spraakmodel te bouwen.

1.  Navigeer naar **tekst-naar-spraak > aangepaste spraaktraining >**.

2.  Klik **op Treinmodel**.

3.  Voer vervolgens een **naam** en **beschrijving** in om u te helpen dit model te identificeren.

    Kies een naam zorgvuldig. De naam die u hier invoert, is de naam die u gebruikt om de stem in uw verzoek om spraaksynthese op te geven als onderdeel van de SSML-invoer. Alleen letters, cijfers en een paar interpunctietekens \_zoals -, en (', ') zijn toegestaan. Gebruik verschillende namen voor verschillende spraakmodellen.

    Een veelgebruikt gebruik van het veld **Beschrijving** is het registreren van de namen van de gegevenssets die zijn gebruikt om het model te maken.

4.  Kies op de pagina **Trainingsgegevens selecteren** een of meerdere gegevenssets die u wilt gebruiken voor training. Controleer het aantal uitingen voordat u ze indient. U beginnen met een willekeurig aantal uitingen voor en-US en zh-CN spraakmodellen. Voor andere landlocaties moet u meer dan 2.000 uitingen selecteren om een stem te kunnen trainen.

    > [!NOTE]
    > Dubbele audionamen worden uit de training verwijderd. Zorg ervoor dat de gegevenssets die u selecteert niet dezelfde audionamen bevatten in meerdere .zip-bestanden.

    > [!TIP]
    > Het gebruik van de gegevenssets van dezelfde luidspreker is vereist voor kwaliteitsresultaten. Wanneer de gegevenssets die u hebt ingediend voor training een totaal aantal van minder dan 6.000 verschillende uitingen bevatten, traint u uw spraakmodel via de statistische parametrische synthesetechniek. In het geval dat uw trainingsgegevens een totaal aantal van 6.000 verschillende uitingen overschrijden, start u een trainingsproces met de Concatenation Synthesis-techniek. Normaal gesproken kan de concatenatie technologie resulteren in meer natuurlijke, en hogere getrouwe stem resultaten. [Neem contact op met het Custom Voice-team](https://go.microsoft.com/fwlink/?linkid=2108737) als u een model wilt trainen met de nieuwste NeuralE TTS-technologie die een digitale stem kan produceren die gelijkwaardig is aan de openbaar [beschikbare neurale stemmen.](language-support.md#neural-voices)

5.  Klik **op Trainen** om te beginnen met het maken van uw spraakmodel.

In de trainingstabel wordt een nieuw item weergegeven dat overeenkomt met dit nieuw gemaakte model. In de tabel wordt ook de status weergegeven: Verwerking, Geslaagd, Mislukt.

De status die wordt weergegeven, weerspiegelt het proces om uw gegevensset om te zetten naar een spraakmodel, zoals hier wordt weergegeven.

| Status | Betekenis |
| ----- | ------- |
| Wordt verwerkt | Uw spraakmodel wordt gemaakt. |
| Geslaagd | Uw spraakmodel is gemaakt en kan worden geïmplementeerd. |
| Mislukt | Uw spraakmodel is mislukt in de training vanwege vele redenen, bijvoorbeeld ongeziene gegevensproblemen of netwerkproblemen. |

De trainingstijd is afhankelijk van het volume van de verwerkte audiogegevens. Typische tijden variëren van ongeveer 30 minuten voor honderden uitingen tot 40 uur voor 20.000 uitingen. Zodra uw modeltraining is geslaagd, u beginnen met het testen ervan.

> [!NOTE]
> Gratis abonnement (F0) gebruikers kunnen trainen een spraaklettertype tegelijk. Gebruikers van een standaardabonnement (S0) kunnen drie stemmen tegelijk trainen. Als u de limiet bereikt, wacht u tot ten minste één van uw spraaklettertypen de training heeft voltooid en probeert u het opnieuw.

> [!NOTE]
> Het maximum aantal spraakmodellen dat per abonnement mag worden getraind, is 10 modellen voor gratis abonnement (F0) gebruikers en 100 voor gebruikers met een standaardabonnement (S0).

Als u de neurale stemtrainingsmogelijkheid gebruikt, u ervoor kiezen om een model te trainen dat is geoptimaliseerd voor real-time streamingscenario's, of een HD-neuralmodel dat is geoptimaliseerd voor asynchrone [lange-audiosynthese.](long-audio-api.md)  

## <a name="test-your-voice-model"></a>Test uw spraakmodel

Nadat uw spraaklettertype is gebouwd, u het testen voordat u het implementeert voor gebruik.

1.  Navigeer naar **tekst-naar-spraak > aangepaste spraak> testen**.

2.  Klik **op Test toevoegen**.

3.  Selecteer een of meerdere modellen die u wilt testen.

4.  Geef de tekst op die de stem(en) moet spreken. Als u meerdere modellen tegelijk wilt testen, wordt dezelfde tekst gebruikt voor het testen voor verschillende modellen.

    > [!NOTE]
    > De taal van uw tekst moet dezelfde zijn als de taal van uw spraaklettertype. Alleen met succes getrainde modellen kunnen worden getest. Alleen platte tekst wordt in deze stap ondersteund.

5.  Klik **op Maken**.

Zodra u uw testaanvraag hebt ingediend, keert u terug naar de testpagina. De tabel bevat nu een item dat overeenkomt met uw nieuwe aanvraag en de statuskolom. Het kan een paar minuten duren om spraak te synthetiseren. Wanneer de statuskolom **Geslaagd**zegt, u de audio afspelen of de tekstinvoer (een .txt-bestand) en audio-uitvoer (een .wav-bestand) downloaden en de laatste voor kwaliteit openen.

U de testresultaten ook vinden op de detailpagina van elke modellen die u hebt geselecteerd voor tests. Ga naar het tabblad **Training** en klik op de modelnaam om de pagina modeldetails in te voeren.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Een aangepast spraakeindpunt maken en gebruiken

Nadat u uw spraakmodel hebt gemaakt en getest, implementeert u het in een aangepast tekst-naar-spraak eindpunt. U gebruikt dit eindpunt vervolgens in plaats van het gebruikelijke eindpunt wanneer u tekst-naar-spraakaanvragen maakt via de REST API. Uw aangepaste eindpunt kan alleen worden aangeroepen door het abonnement dat u hebt gebruikt om het lettertype te implementeren.

Als u een nieuw aangepast spraakeindpunt wilt maken, gaat u naar **Tekst-naar-spraak > Aangepaste spraak> implementatie**. Selecteer **Eindpunt toevoegen** en voer een **naam** en **beschrijving** in voor uw aangepaste eindpunt. Selecteer vervolgens het aangepaste spraakmodel dat u aan dit eindpunt wilt koppelen.

Nadat u op de knop **Toevoegen** hebt geklikt, ziet u in de eindpunttabel een vermelding voor uw nieuwe eindpunt. Het kan enkele minuten duren om een nieuw eindpunt te instantiëren. Wanneer de status van de implementatie is **geslaagd,** is het eindpunt klaar voor gebruik.

> [!NOTE]
> Gratis abonnement (F0) gebruikers kunnen slechts een model geïmplementeerd. S0-gebruikers (Standard Subscription) kunnen tot 50 eindpunten maken, elk met een eigen aangepaste stem.

> [!NOTE]
> Als u uw aangepaste stem wilt gebruiken, moet u de naam van het spraakmodel opgeven, de aangepaste URI rechtstreeks in een HTTP-aanvraag gebruiken en hetzelfde abonnement gebruiken om de verificatie van de TTS-service door te geven.

Nadat uw eindpunt is geïmplementeerd, wordt de naam van het eindpunt weergegeven als koppeling. Klik op de koppeling om informatie weer te geven die specifiek is voor uw eindpunt, zoals de eindpuntsleutel, de URL van eindpunt en voorbeeldcode.

Online testen van het eindpunt is ook beschikbaar via de aangepaste voice portal. Als u uw eindpunt wilt testen, kiest **u Eindpunt controleren** op de detailpagina **Eindpunt.** De pagina eindpunttesten wordt weergegeven. Voer de tekst in die moet worden gesproken (in platte tekst of [SSML-indeling](speech-synthesis-markup.md) in het tekstvak. Als u de tekst wilt horen die in uw aangepaste spraaklettertype wordt gesproken, selecteert u **Afspelen**. Deze testfunctie wordt in rekening gebracht tegen uw aangepaste spraaksynthesegebruik.

Het aangepaste eindpunt is functioneel identiek aan het standaardeindpunt dat wordt gebruikt voor tekst-naar-spraakaanvragen. Zie [REST API](rest-text-to-speech.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Gids: Neem uw stemvoorbeelden op](record-custom-voice-samples.md)
* [Naslaginformatie voor Text to Speech-API](rest-text-to-speech.md)
* [Lange audio-API](long-audio-api.md)
