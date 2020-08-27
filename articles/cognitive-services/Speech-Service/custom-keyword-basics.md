---
title: Aangepaste tref woorden maken-spraak service
titleSuffix: Azure Cognitive Services
description: Het apparaat luistert altijd naar een tref woord (of woord groep). Wanneer de gebruiker het tref woord heeft gestaan, stuurt het apparaat alle volgende audio naar de Cloud totdat de gebruiker stopt met spreken. Het aanpassen van uw tref woord is een efficiënte manier om uw apparaat te onderscheiden en uw huis stijl te versterken.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/20/2019
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: d80f244f7b5e17d730451093070b971e9aa041b9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919009"
---
# <a name="custom-keyword-basics"></a>Basisbeginselen van aangepaste trefwoorden

In dit artikel leert u de basis beginselen van het werken met aangepaste tref woorden, met behulp van speech Studio en de Speech SDK. Een tref woord is een woord of korte woord groep waarmee uw product spraak kan worden geactiveerd. Trefwoord modellen maakt u in speech Studio en vervolgens exporteert u een model bestand dat u gebruikt met de spraak-SDK in uw toepassingen.

## <a name="prerequisites"></a>Vereisten

Voor de stappen in dit artikel is een spraak abonnement en de spraak-SDK vereist. Als u nog geen abonnement hebt, [kunt u de speech-service gratis uitproberen](get-started.md). Als u de SDK wilt downloaden, raadpleegt u de [installatie handleiding](quickstarts/setup-platform.md) voor uw platform.

## <a name="create-a-keyword-in-speech-studio"></a>Een tref woord maken in speech Studio

Voordat u een aangepast tref woord kunt gebruiken, moet u een tref woord maken met behulp van de [aangepaste trefwoord](https://aka.ms/sdsdk-wakewordportal) pagina in [Speech Studio](https://aka.ms/sdsdk-speechportal). Nadat u een tref woord hebt verstrekt, produceert het een `.table` bestand dat u kunt gebruiken met de spraak-SDK.

> [!IMPORTANT]
> Aangepaste trefwoord modellen en de resulterende `.table` bestanden kunnen **alleen** worden gemaakt in speech Studio.
> U kunt geen aangepaste tref woorden maken op basis van de SDK of met REST-aanroepen.

1. Ga naar de [Speech Studio](https://aka.ms/sdsdk-speechportal) en **Meld** u aan of als u nog geen abonnement op spraak hebt, kiest u [**een abonnement maken**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Maak een **Nieuw project**op de pagina [aangepast tref woord](https://aka.ms/sdsdk-wakewordportal) . 

1. Voer een **naam**, een optionele **Beschrijving**en selecteer de taal. U hebt één project per taal nodig en de ondersteuning is momenteel beperkt tot de `en-US` taal.

    ![Uw trefwoord project beschrijven](media/custom-keyword/custom-kws-portal-new-project.png)

1. Selecteer uw project in de lijst. 

    ![Selecteer uw trefwoord project](media/custom-keyword/custom-kws-portal-project-list.png)

1. Als u een nieuw trefwoord model wilt maken, klikt u op **model trainen**.

1. Voer een **naam** in voor het model, een optionele **Beschrijving**en het **tref woord** van uw keuze en klik vervolgens op **volgende**. Zie de [richt lijnen](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) voor het kiezen van een effectief tref woord.

    ![Voer uw tref woord in](media/custom-keyword/custom-kws-portal-new-model.png)

1. De portal maakt de uitspraak van kandidaten voor uw tref woord. Luister naar elke kandidaat door te klikken op de afspeel knoppen en de controles te verwijderen naast eventuele uitstaande uitspraaken. Als er alleen goede uitspraaken zijn ingeschakeld, klikt u op **trainen** om het trefwoord model te genereren. 

    ![Je tref woord controleren](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Het kan tot dertig minuten duren voordat het model is gegenereerd. De lijst met tref woorden wordt gewijzigd van de **verwerking** naar **geslaagd** wanneer het model is voltooid. Vervolgens kunt u het bestand downloaden.

    ![Je tref woord controleren](media/custom-keyword/custom-kws-portal-download-model.png)

1. Het gedownloade bestand is een `.zip` Archief. Pak het archief uit en u ziet een bestand met de `.table` extensie. Dit is het bestand dat u met de SDK gebruikt in de volgende sectie, dus noteer het pad. de bestands naam komt overeen met de naam van uw tref woord, bijvoorbeeld een trefwoord **Activering apparaat** heeft de bestands naam `Activate_device.table` .

## <a name="use-a-keyword-model-with-the-sdk"></a>Een trefwoord model gebruiken met de SDK

Laad eerst uw trefwoorden model bestand met behulp van de `FromFile()` statische functie. dit retourneert een `KeywordRecognitionModel` . Gebruik het pad naar het `.table` bestand dat u hebt gedownload vanuit speech Studio. Daarnaast maakt u een `AudioConfig` met de standaard microfoon en vervolgens een nieuw exemplaar `KeywordRecognizer` van de audio configuratie.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Vervolgens wordt het uitvoeren van trefwoord herkenning uitgevoerd met één aanroep naar `RecognizeOnceAsync()` door uw model object door te geven. Hiermee wordt een sessie voor het herkennen van een tref woord gestart totdat het sleutel woord is herkend. Daarom gebruikt u dit ontwerp patroon doorgaans in toepassingen met meerdere threads of in gevallen waarin u voor onbepaalde tijd kunt wachten op een Wake-woord.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> In het voor beeld dat hier wordt weer gegeven, wordt gebruikgemaakt van lokale woord herkenning, omdat hiervoor geen `SpeechConfig` object voor verificatie context is vereist en er geen contact wordt gemaakt met de back-end. U kunt echter zowel trefwoord herkenning als verificatie uitvoeren waarbij [een continue back-endverbinding wordt gebruikt](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword).

## <a name="next-steps"></a>Volgende stappen

Test uw aangepaste tref woord met de Quick start voor de [Speech-apparaten SDK](https://aka.ms/sdsdk-quickstart).
