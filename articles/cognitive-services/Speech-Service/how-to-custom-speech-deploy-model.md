---
title: Een model implementeren voor aangepaste spraak - spraakservice
titleSuffix: Azure Cognitive Services
description: In dit document leert u hoe u een eindpunt maken en implementeren met de portal Aangepaste spraak.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: e41653e4644b09cc357510223e5ee86c3806ae2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806110"
---
# <a name="deploy-a-custom-model"></a>Aangepaste model implementeren

Nadat u gegevens hebt geüpload en geïnspecteerd, de nauwkeurigheid hebt geëvalueerd en een aangepast model hebt getraind, u een aangepast eindpunt implementeren om te gebruiken met uw apps, hulpprogramma's en producten. In dit document leert u hoe u een eindpunt maken en implementeren met behulp van de [portal Aangepaste spraak.](https://speech.microsoft.com/customspeech)

## <a name="create-a-custom-endpoint"></a>Een aangepast eindpunt maken

Als u een nieuw aangepast eindpunt wilt maken, meldt u zich aan bij de [portal Aangepaste spraak](https://speech.microsoft.com/customspeech) en selecteert u **Implementatie** in het menu Aangepaste spraak boven aan de pagina. Als dit je eerste run is, zul je merken dat er geen eindpunten in de tabel staan. Nadat u een eindpunt hebt gemaakt, gebruikt u deze pagina om elk geïmplementeerd eindpunt bij te houden.

Selecteer vervolgens **Eindpunt toevoegen** en voer een **naam** en **beschrijving** in voor uw aangepaste eindpunt. Selecteer vervolgens het aangepaste model dat u aan dit eindpunt wilt koppelen. Vanaf deze pagina u ook logboekregistratie inschakelen. Met logboekregistratie u het eindpuntverkeer controleren. Als het verkeer is uitgeschakeld, wordt het verkeer niet opgeslagen.

![Een model implementeren](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Vergeet niet om de gebruiksvoorwaarden en prijsdetails te accepteren.

Selecteer vervolgens **Maken**. Met deze actie keert u terug naar de **pagina Implementatie.** De tabel bevat nu een vermelding die overeenkomt met uw aangepaste eindpunt. De status van het eindpunt geeft de huidige status weer. Het kan tot 30 minuten duren voordat een nieuw eindpunt wordt geinstantt met uw aangepaste modellen. Wanneer de status van de implementatie wordt gewijzigd in **Voltooien,** is het eindpunt klaar voor gebruik.

Nadat uw eindpunt is geïmplementeerd, wordt de naam van het eindpunt weergegeven als koppeling. Klik op de koppeling om informatie weer te geven die specifiek is voor uw eindpunt, zoals de eindpuntsleutel, de URL van eindpunt en voorbeeldcode.

## <a name="view-logging-data"></a>Logboekregistratiegegevens weergeven

Logboekregistratiegegevens kunnen worden gedownload onder **> Details**.

## <a name="next-steps"></a>Volgende stappen

* Uw aangepaste eindpunt gebruiken met de [SpraakSDK](speech-sdk.md)

## <a name="additional-resources"></a>Aanvullende bronnen

* [Uw gegevens voorbereiden en testen](how-to-custom-speech-test-data.md)
* [Uw gegevens inspecteren](how-to-custom-speech-inspect-data.md)
* [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)
* [Uw model trainen](how-to-custom-speech-train-model.md)
* [Uw model implementeren](how-to-custom-speech-deploy-model.md)
