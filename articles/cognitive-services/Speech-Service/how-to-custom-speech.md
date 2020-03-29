---
title: Aan de slag met Aangepaste spraak - Spraakservice
titleSuffix: Azure Cognitive Services
description: Custom Speech is een set online tools waarmee u onze spraak-naar-tekst nauwkeurigheid voor uw toepassingen, tools en producten evalueren en verbeteren. Alles wat nodig is om te beginnen zijn een handvol test audiobestanden. Volg de onderstaande links om een aangepaste spraak-naar-tekst-ervaring te maken.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: dapine
ms.openlocfilehash: e30587ed4d09ba792516ebc7b16529a6ee5ac57c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918951"
---
# <a name="what-is-custom-speech"></a>Wat is aangepaste spraak?

[Aangepaste spraak](https://aka.ms/customspeech) is een set online tools waarmee u de spraak-naar-tekstnauwkeurigheid van Microsoft voor uw toepassingen, hulpprogramma's en producten evalueren en verbeteren. Alles wat nodig is om te beginnen zijn een handvol test audiobestanden. Volg de onderstaande links om een aangepaste spraak-naar-tekst-ervaring te maken.

## <a name="whats-in-custom-speech"></a>Wat staat er in aangepaste spraak?

Voordat u iets doen met Aangepaste spraak, hebt u een Azure-account en een spraakserviceabonnement nodig. Zodra u een account hebt, u uw gegevens voorbereiden, trainen en testen van uw modellen, de herkenningskwaliteit inspecteren, de nauwkeurigheid evalueren en uiteindelijk het aangepaste spraak-naar-tekstmodel implementeren en gebruiken.

In dit diagram worden de stukken belicht die deel uitmaken van de [portal Aangepaste spraak](https://aka.ms/customspeech). Gebruik de onderstaande links voor meer informatie over elke stap.

![Hiermee worden de verschillende componenten belicht die deel uitmaken van de portal Aangepaste spraak.](./media/custom-speech/custom-speech-overview.png)

1. [Abonneer u op en maak een project](#set-up-your-azure-account) - Maak een Azure-account en abonneer u op de spraakservice. Dit uniforme abonnement geeft u toegang tot spraak-naar-tekst, tekst-naar-spraak, spraakvertaling en de [aangepaste spraakportal.](https://speech.microsoft.com/customspeech) Maak vervolgens met uw spraakserviceabonnement uw eerste aangepaste spraakproject.

2. [Testgegevens uploaden](how-to-custom-speech-test-data.md) - Testgegevens (audiobestanden) uploaden om het spraak-naar-tekst-aanbod van Microsoft voor uw toepassingen, hulpprogramma's en producten te evalueren.

3. [Controleer de herkenningskwaliteit](how-to-custom-speech-inspect-data.md) - Gebruik de [aangepaste spraakportal](https://speech.microsoft.com/customspeech) om geüploade audio af te spelen en de kwaliteit van de spraakherkenning van uw testgegevens te inspecteren. Zie [Gegevens inspecteren voor](how-to-custom-speech-inspect-data.md)kwantitatieve metingen.

4. [Nauwkeurigheid evalueren](how-to-custom-speech-evaluate-data.md) - Evalueer de nauwkeurigheid van het spraak-naar-tekstmodel. De [aangepaste spraakportal](https://speech.microsoft.com/customspeech) biedt een *Word-foutpercentage,* dat kan worden gebruikt om te bepalen of extra training vereist is. Als u tevreden bent met de nauwkeurigheid, u de API's van spraakservice rechtstreeks gebruiken. Als u de nauwkeurigheid met een relatief gemiddelde van 5% - 20% wilt verbeteren, gebruikt u het tabblad **Training** in de portal om aanvullende trainingsgegevens te uploaden, zoals transcripties met een menselijk label en gerelateerde tekst.

5. [Train het model](how-to-custom-speech-train-model.md) - Verbeter de nauwkeurigheid van uw spraak-naar-tekstmodel door schriftelijke transcripties (10-1.000 uur) en gerelateerde tekst (<200 MB) samen met uw audiotestgegevens te verstrekken. Deze gegevens helpen bij het trainen van het spraak-naar-tekstmodel. Na de training, opnieuw testen, en als je tevreden bent met het resultaat, u uw model implementeren.

6. [Het model implementeren](how-to-custom-speech-deploy-model.md) - Maak een aangepast eindpunt voor uw spraak-naar-tekstmodel en gebruik het in uw toepassingen, hulpprogramma's of producten.

## <a name="set-up-your-azure-account"></a>Uw Azure-account instellen

Een abonnement op de spraakservice is vereist voordat u de [portal Aangepaste spraak](https://speech.microsoft.com/customspeech) gebruiken om een aangepast model te maken. Volg deze instructies om een standaard abonnement op de spraakservice te maken: [Een spraakabonnement maken](get-started.md#new-resource).

> [!NOTE]
> Zorg ervoor dat u standaard (S0)-abonnementen maakt, gratis proefabonnementen (F0) worden niet ondersteund.

Zodra u een Azure-account en een spraakserviceabonnement hebt gemaakt, moet u zich aanmelden bij [de portal Aangepaste spraak](https://speech.microsoft.com/customspeech) en uw abonnement verbinden.

1. Haal uw abonnementssleutel voor spraakservice op van de Azure-portal.
2. Aanmelden bij de [portal Aangepaste spraak](https://aka.ms/custom-speech).
3. Selecteer het abonnement waaraan u moet werken en maak een spraakproject.
4. Als u uw abonnement wilt wijzigen, gebruikt u het **tandwielpictogram** in de bovenste navigatienavigatie.

## <a name="how-to-create-a-project"></a>Een project maken

Inhoud zoals gegevens, modellen, tests en eindpunten worden georganiseerd in **Projecten** in de [portal Aangepaste spraak.](https://speech.microsoft.com/customspeech) Elk project is specifiek voor een domein en land/taal. U bijvoorbeeld een project maken voor callcenters die Engels gebruiken in de Verenigde Staten.

Als u uw eerste project wilt maken, selecteert u de **spraak-naar-tekst/Aangepaste spraak**en klikt u op **Nieuw project**. Volg de instructies van de wizard om uw project te maken. Nadat u een project hebt gemaakt, ziet u vier tabbladen: **Gegevens,** **Testen,** **Training**en **Implementatie**. Gebruik de koppelingen in [volgende stappen](#next-steps) om te leren hoe u elk tabblad gebruiken.

> [!IMPORTANT]
> De [Custom Speech portal](https://aka.ms/custom-speech) is onlangs bijgewerkt! Als u eerdere gegevens, modellen, tests en gepubliceerde eindpunten hebt gemaakt in de CRIS.ai portal of met API's, moet u een nieuw project maken in de nieuwe portal om verbinding te maken met deze oude entiteiten.

## <a name="next-steps"></a>Volgende stappen

* [Uw gegevens voorbereiden en testen](how-to-custom-speech-test-data.md)
* [Uw gegevens inspecteren](how-to-custom-speech-inspect-data.md)
* [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)
* [Uw model trainen](how-to-custom-speech-train-model.md)
* [Uw model implementeren](how-to-custom-speech-deploy-model.md)
