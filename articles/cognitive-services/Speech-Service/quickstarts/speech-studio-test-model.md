---
title: 'Snelstartgids: een model testen met audio bestanden-speech Studio'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start gebruikt u Speech Studio om herkenning van spraak in een audio bestand te testen.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: v-demjoh
ms.openlocfilehash: 76fb933893b624e9b289ddf51d2a0373b51e58d1
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391533"
---
# <a name="quickstart-test-a-model-using-an-audio-file-in-speech-studio"></a>Snelstartgids: een model testen met behulp van een audio bestand in speech Studio

In deze Quick Start gebruikt u Speech Studio om spraak van een audio bestand naar tekst te converteren. Met Speech Studio kunt u spraakherkennings modellen testen, vergelijken, verbeteren en implementeren met behulp van gerelateerde tekst, audio met zelfvoorziene transcripten en uitspraak richtlijnen die u opgeeft.

## <a name="prerequisites"></a>Vereisten

Voordat u de spraak Portal gebruikt, [volgt u deze instructies om een Azure-account te maken en u te abonneren op de spraak service](../how-to-custom-speech.md#set-up-your-azure-account). Dit uniforme abonnement geeft u toegang tot spraak naar tekst, tekst naar spraak, spraak omzetting en de Custom Speech Portal.

## <a name="download-an-audio-file"></a>Een audio bestand downloaden

Volg deze stappen om een audio bestand met spraak te downloaden en in een zip-bestand te pakken.

1. Down load het voor **[beeld-WAV-bestand van deze koppeling](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)** door met de rechter muisknop op de koppeling te klikken en **Koppeling opslaan als**te selecteren. Klik op **Opslaan** om het `whatstheweatherlike.wav` bestand te downloaden.
2. Een bestand Verkenner of een Terminal venster met een zip-hulp programma maken een zip-bestand met `whatstheweatherlike.zip` de naam dat het bestand bevat dat `whatstheweatherlike.wav` u hebt gedownload. In Windows opent u Windows Verkenner, navigeert u naar de map, klikt u met de rechter muisknop op verzenden, klikt `Downloads` `whatstheweatherliike.wav` **u**op **gecomprimeerde (gezipte) map**en drukt u op ENTER om de standaard bestandsnaam te accepteren.

## <a name="create-a-project-in-the-custom-speech-portal"></a>Een project maken in de Custom Speech Portal

Volg deze stappen om een project te maken dat uw zip-bestand bevat.

1. Open [Speech Studio](https://speech.microsoft.com/)en klik op **New Project**. Typ een naam voor dit project en klik op **maken**. Uw project wordt weer gegeven in de lijst Custom Speech.
2. Klik op de naam van uw project. Klik op **gegevens uploaden**op het tabblad gegevens.
3. Het speech-gegevens type wordt standaard ingesteld op **Audio**, dus klik op **volgende**.
4. Geef uw nieuwe gegevensset een naam `MyZipOfAudio` en klik op **volgende**.
5. Klik op **Bladeren bestanden...**, navigeer naar het `whatstheweatherlike.zip` bestand en klik op **openen**.
6. Klik op de knop **Uploaden**. De browser uploadt uw zip-bestand naar speech Studio en de inhoud wordt verwerkt door Speech Studio.

## <a name="test-a-model"></a>Een model testen

Nadat de inhoud van uw zip-bestand door Speech Studio is verwerkt, kunt u de bron audio afspelen tijdens het controleren van de transcriptie om fouten of omissies op te sporen. Volg deze stappen om de transcriptie kwaliteit in de browser te controleren.

1. Klik op het tabblad **testen** en klik op **test toevoegen**.
2. In deze test wordt de kwaliteit van alleen audio gegevens geïnspecteerd, dus klik op **volgende** om dit test type te accepteren.
3. Geef deze test een naam `MyModelTest` en klik op **volgende**.
4. Klik op het keuze rondje links van en `MyZipOfAudio` Klik op **volgende**.
5. De vervolg keuzelijst **model 1** wordt standaard ingesteld op het meest recente herkennings model, dus klik op **maken**. Nadat de inhoud van uw audio gegevensset is verwerkt, wordt de test status gewijzigd in **geslaagd**.
6. Klik op **MyModelTest**. De resultaten van spraak herkenning worden weer gegeven. Klik op het rechter drie hoekje binnen de cirkel om de audio te belui Steren en vergelijk wat u hoort aan de tekst van de cirkel.

## <a name="download-detailed-results"></a>Gedetailleerde resultaten downloaden

U kunt bestanden die transcripties beschrijven in veel meer details downloaden. De bestanden bevatten lexicale vorm van spraak in uw audio bestanden en JSON-bestanden met gegevens over de betrouw baarheid van de offset, duur en transcriptie van elk woord. Volg deze stappen om deze bestanden weer te geven.

1. Klik op **Downloaden**.
2. Selecteer in het dialoog venster downloaden de optie **Audio**en klik op **downloaden**.
3. Pak het gedownloade zip-bestand uit en controleer de uitgepakte bestanden.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het verbeteren van de nauw keurigheid van spraak herkenning door [een aangepast model te trainen](../how-to-custom-speech-test-and-train.md).