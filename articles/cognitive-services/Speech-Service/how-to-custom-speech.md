---
title: Aan de slag met Custom Speech-Speech-Service
titleSuffix: Azure Cognitive Services
description: Custom Speech is een set online-hulpprogram ma's waarmee u de nauw keurigheid van spraak naar tekst voor uw toepassingen, hulpprogram ma's en producten kunt evalueren en verbeteren. Alles wat u nodig hebt om aan de slag te gaan zijn een aantal test audio bestanden. Volg de onderstaande koppelingen om aan de slag te gaan met het maken van een aangepaste spraak-naar-tekst-ervaring.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: c8c849cb83ecb1db5e972c660d94c795092c458e
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806008"
---
# <a name="what-is-custom-speech"></a>Wat is Custom Speech?

[Custom speech](https://aka.ms/customspeech) is een set online-hulpprogram ma's waarmee u de nauw keurigheid van de spraak naar tekst van micro soft kunt evalueren en verbeteren voor uw toepassingen, hulpprogram ma's en producten. Alles wat u nodig hebt om aan de slag te gaan zijn een aantal test audio bestanden. Volg de onderstaande koppelingen om aan de slag te gaan met het maken van een aangepaste spraak-naar-tekst-ervaring.

## <a name="whats-in-custom-speech"></a>Wat is er in Custom Speech?

Voordat u iets met Custom Speech kunt doen, hebt u een Azure-account en een spraak service-abonnement nodig. Zodra u een account hebt, kunt u uw gegevens voorbereiden, uw modellen trainen en testen, de herkennings kwaliteit controleren, de nauw keurigheid evalueren en uiteindelijk het aangepaste spraak-naar-tekst model implementeren en gebruiken.

Dit diagram markeert de onderdelen waaruit de [Custom speech Portal](https://aka.ms/customspeech)is gemaakt. Gebruik de onderstaande koppelingen voor meer informatie over elke stap.

![Hier worden de verschillende onderdelen van de Custom Speech Portal gemarkeerd.](./media/custom-speech/custom-speech-overview.png)

1. [Abonneren en een project maken](#set-up-your-azure-account) : Maak een Azure-account en Abonneer u op de spraak service. Dit uniforme abonnement geeft u toegang tot spraak naar tekst, tekst naar spraak, spraak omzetting en de [Custom speech Portal](https://speech.microsoft.com/customspeech). Maak vervolgens uw eerste Custom Speech project met behulp van uw abonnement op de spraak service.

2. [Test gegevens uploaden](how-to-custom-speech-test-data.md) test gegevens uploaden (audio bestanden) om de spraak-naar-tekst-aanbieding van micro soft voor uw toepassingen, hulpprogram ma's en producten te evalueren.

3. [Herkennings kwaliteit inspecteren](how-to-custom-speech-inspect-data.md) : gebruik de [Custom speech Portal](https://speech.microsoft.com/customspeech) voor het afspelen van geüploade audio en controleer de kwaliteit van de spraak herkenning van uw test gegevens. Zie [gegevens controleren](how-to-custom-speech-inspect-data.md)voor kwantitatieve metingen.

4. [Nauw keurigheid evalueren](how-to-custom-speech-evaluate-data.md) : Evalueer de nauw keurigheid van het spraak-naar-tekst model. De [Custom speech-Portal](https://speech.microsoft.com/customspeech) biedt een *woord fout*, dat kan worden gebruikt om te bepalen of extra training vereist is. Als u tevreden bent met de nauw keurigheid, kunt u de speech service-Api's rechtstreeks gebruiken. Als u de nauw keurigheid wilt verbeteren met een relatief gemiddelde van 5%-20%, gebruikt u het tabblad **training** in de portal voor het uploaden van aanvullende trainings gegevens, zoals transcripten met menselijke labels en gerelateerde tekst.

5. [Train het model](how-to-custom-speech-train-model.md) : Verbeter de nauw keurigheid van uw spraak-naar-tekst model door geschreven transcripten (10-1000 uur) en gerelateerde tekst (< 200 MB) samen met uw audio test gegevens te voorzien. Deze gegevens helpen u bij het trainen van het spraak-naar-tekst-model. Wanneer u klaar bent met trainingen, testen en als u tevreden bent met het resultaat, kunt u uw model implementeren.

6. [Het model implementeren](how-to-custom-speech-deploy-model.md) : Maak een aangepast eind punt voor uw spraak-naar-tekst-model en gebruik het in uw toepassingen, hulpprogram ma's of producten.

## <a name="set-up-your-azure-account"></a>Uw Azure-account instellen

Er is een abonnement op de spraak service vereist voordat u de [Custom speech Portal](https://speech.microsoft.com/customspeech) kunt gebruiken om een aangepast model te maken. Volg deze instructies voor het maken van een standaard abonnement op spraak service: [een spraak abonnement maken](get-started.md#try-the-speech-service-using-a-new-azure-account).

> [!NOTE]
> Zorg ervoor dat u Standard (S0)-abonnementen maakt, maar gratis proef abonnementen (F0) worden niet ondersteund.

Zodra u een Azure-account en een spraak service-abonnement hebt gemaakt, moet u zich aanmelden bij [Custom speech Portal](https://speech.microsoft.com/customspeech) en uw abonnement verbinden.

1. Haal uw abonnements sleutel voor uw spraak service op uit de Azure Portal.
2. Meld u aan bij de [Custom speech Portal](https://aka.ms/custom-speech).
3. Selecteer het abonnement waarmee u wilt werken en maak een spraak project.
4. Als u uw abonnement wilt wijzigen, gebruikt u het **tandwiel** -pictogram in de bovenste navigatie balk.

## <a name="how-to-create-a-project"></a>Een project maken

Inhoud, zoals gegevens, modellen, testen en eind punten, zijn ingedeeld in **projecten** in de [Custom speech Portal](https://speech.microsoft.com/customspeech). Elk project is specifiek voor een domein en land/taal. U kunt bijvoorbeeld een project maken voor call centers die Engels gebruiken in de Verenigde Staten.

Als u uw eerste project wilt maken, selecteert u spraak **naar tekst/aangepaste spraak**en klikt u vervolgens op **Nieuw project**. Volg de instructies in de wizard om het project te maken. Nadat u een project hebt gemaakt, ziet u vier tabbladen: **gegevens**, **testen**, **training**en **implementatie**. Gebruik de koppelingen in de [volgende stappen](#next-steps) voor meer informatie over het gebruik van elk tabblad.

## <a name="next-steps"></a>Volgende stappen

* [Uw gegevens voorbereiden en testen](how-to-custom-speech-test-data.md)
* [Uw gegevens controleren](how-to-custom-speech-inspect-data.md)
* [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)
* [Uw model trainen](how-to-custom-speech-train-model.md)
* [Uw model implementeren](how-to-custom-speech-deploy-model.md)
