---
title: Verbeter de synthese met Aangepaste Spraak - Spraakservice
titleSuffix: Azure Cognitive Services
description: Custom Voice is een set van online tools waarmee u een herkenbare, unieke stem voor uw merk maken. Alles wat nodig is om te beginnen zijn een handvol audiobestanden en de bijbehorende transcripties. Volg de onderstaande links om een aangepaste spraak-naar-tekst-ervaring te maken.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: dapine
ms.openlocfilehash: 786cf687e569af172eabfaa9068ae61ffd6b2988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220477"
---
# <a name="get-started-with-custom-voice"></a>Aan de slag met Custom Voice

[Custom Voice](https://aka.ms/customvoice) is een set van online tools waarmee u een herkenbare, unieke stem voor uw merk maken. Alles wat nodig is om te beginnen zijn een handvol audiobestanden en de bijbehorende transcripties. Volg de onderstaande links om een aangepaste tekst-naar-spraak-ervaring te maken.

## <a name="whats-in-custom-voice"></a>Wat zit er in Custom Voice?

Voordat u begint met Aangepaste spraak, hebt u een Azure-account en een spraakserviceabonnement nodig. Zodra u een account hebt gemaakt, u uw gegevens voorbereiden, trainen en testen van uw modellen, de spraakkwaliteit evalueren en uiteindelijk uw aangepaste spraakmodel implementeren.

In het onderstaande diagram worden de stappen beschreven om een aangepast spraakmodel te maken met behulp van de [aangepaste spraakportal.](https://aka.ms/customvoice) Gebruik de koppelingen voor meer informatie.

![Aangepast spraakarchitectuurdiagram](media/custom-voice/custom-voice-diagram.png)

1. [Abonneer u op en maak een project](#set-up-your-azure-account) - Maak een Azure-account en maak een spraakserviceabonnement. Dit uniforme abonnement geeft u toegang tot spraak-naar-tekst, tekst-naar-spraak, spraakvertaling en de Aangepaste Voice-portal. Maak vervolgens met uw spraakserviceabonnement uw eerste Aangepaste Spraakproject.

2. [Gegevens uploaden](how-to-custom-voice-create-voice.md#upload-your-datasets) - Gegevens uploaden (audio en tekst) met behulp van de Aangepaste Voice-portal of de Aangepaste Voice API. Vanuit het portaal u uitspraakscores en signaal-ruisverhoudingen onderzoeken en evalueren. Zie [Gegevens voorbereiden op Aangepaste spraak voor](how-to-custom-voice-prepare-data.md)meer informatie.

3. [Train uw model](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – Gebruik uw gegevens om een aangepast tekst-naar-spraakspraakmodel te maken. U een model trainen in verschillende talen. Test na de training uw model en als u tevreden bent met het resultaat, u het model implementeren.

4. [Uw model implementeren](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) - Maak een aangepast eindpunt voor uw tekst-naar-spraakspraakmodel en gebruik het voor spraaksynthese in uw producten, hulpprogramma's en toepassingen.

## <a name="custom-neural-voices"></a>Aangepaste neurale stemmen

De neurale stem aanpassingsmogelijkheid is momenteel in openbare preview, beperkt tot geselecteerde klanten. Vul dit [aanvraagformulier](https://go.microsoft.com/fwlink/?linkid=2108737) in om aan de slag te gaan.

> [!NOTE]
> Als onderdeel van microsoft's inzet voor het ontwerpen van verantwoorde AI, is het onze bedoeling om de rechten van individuen en de samenleving te beschermen en transparante interacties tussen mens en computer te bevorderen. Om deze reden is Custom Neural Voice niet algemeen beschikbaar voor alle klanten. U pas toegang krijgen tot de technologie nadat uw toepassingen zijn beoordeeld en u hebt toegezegd deze te gebruiken in overeenstemming met onze ethische principes. Meer informatie over ons [aanvraaggatingsproces](https://aka.ms/custom-neural-gating-overview).

## <a name="set-up-your-azure-account"></a>Uw Azure-account instellen

Een abonnement op de spraakservice is vereist voordat u de portal Aangepaste spraak gebruiken om een aangepast model te maken. Volg deze instructies om een spraakserviceabonnement in Azure te maken. Als u geen Azure-account hebt, u zich aanmelden voor een nieuw account.  

Zodra u een Azure-account en een spraakserviceabonnement hebt gemaakt, moet u zich aanmelden bij de Aangepaste Voice-portal en uw abonnement verbinden.

1. Haal uw abonnementssleutel voor spraakservice op van de Azure-portal.
2. Meld u aan bij de [aangepaste voice-portal](https://aka.ms/custom-voice).
3. Selecteer uw abonnement en maak een spraakproject.
4. Als u wilt overschakelen naar een ander spraakabonnement, gebruikt u het tandwielpictogram in de bovenste navigatienavigatie.

> [!NOTE]
> De Custom Voice-service ondersteunt de gratis testsleutel van 30 dagen NIET. U moet een F0- of S0-sleutel hebben gemaakt in Azure voordat u de service gebruiken.

## <a name="how-to-create-a-project"></a>Een project maken

Inhoud zoals gegevens, modellen, tests en eindpunten worden ingedeeld in **Projecten** in de Aangepaste Voice-portal. Elk project is specifiek voor een land/taal en het geslacht van de stem die u wilt maken. U bijvoorbeeld een project maken voor een vrouwelijke stem voor de chatbots van uw callcenter die Engels gebruiken in de Verenigde Staten (en-VS).

Als u uw eerste project wilt maken, selecteert u het tabblad **Tekst naar spraak/Aangepaste spraak** en klikt u op Nieuw **project**. Volg de instructies van de wizard om uw project te maken. Nadat u een project hebt gemaakt, ziet u vier tabbladen: **Gegevens,** **Training,** **Testen**en **Implementatie**. Gebruik de koppelingen in [volgende stappen](#next-steps) om te leren hoe u elk tabblad gebruiken.

> [!IMPORTANT]
> De [Custom Voice portal](https://aka.ms/custom-voice) is onlangs bijgewerkt! Als u eerdere gegevens, modellen, tests en gepubliceerde eindpunten hebt gemaakt in de CRIS.ai portal of met API's, moet u een nieuw project maken in de nieuwe portal om verbinding te maken met deze oude entiteiten.

## <a name="next-steps"></a>Volgende stappen

- [Aangepaste spraakgegevens voorbereiden](how-to-custom-voice-prepare-data.md)
- [Een aangepaste stem maken](how-to-custom-voice-create-voice.md)
- [Gids: Neem uw stemvoorbeelden op](record-custom-voice-samples.md)
