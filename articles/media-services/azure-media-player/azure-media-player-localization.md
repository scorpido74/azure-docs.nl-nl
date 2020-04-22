---
title: Lokalisatie van Azure Media Player
description: Ondersteuning voor meerdere talen voor gebruikers van niet-Engelse landlocaties.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 34805c4eaae5d969fc2338c24f9f92404e065d15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727175"
---
# <a name="localization"></a>Lokalisatie #

Met ondersteuning voor meerdere talen kunnen gebruikers van niet-Engelse landgenoten native communiceren met de speler. Azure Media Player zal instantiate met een algemeen woordenboek van talen, die de foutmeldingen zal lokaliseren op basis van de paginataal. Een ontwikkelaar kan ook een spelerinstantie maken met een geforceerde settaal. De standaardtaal is Engels (nl).

> [!NOTE]
> Deze functie is nog steeds gaan door middel van een aantal testen en als zodanig is onderworpen aan bugs.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player ondersteunt momenteel de volgende talen met de bijbehorende taalcodes:

| Taal            | Code | Taal                | Code   | Taal                | Code         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Engels {default}   | nl   | Kroatisch                | uur     | Roemeens                | Ro           |
| Arabisch              | Ar   | Hongaars               | Hu     | Slowaaks                  | Sk           |
| Bulgaars           | Bg   | Indonesisch              | id     | Sloveens                 | Sl           |
| Catalaans             | Ca   | IJslands               | is     | Servisch - Cyrillisch      | sr-cyrl-cs   |
| Tsjechisch               | Cs   | Italiaans                 | it     | Servisch - Latijn         | sr-latn-rs   |
| Deens              | da   | Japans                | ja     | Russisch                 | ru           |
| Duits              | de   | Kazachs                  | Kk     | Zweeds                 | sv           |
| Grieks               | El   | Koreaans                  | Ko     | Thai                    | Th           |
| Spaans             | Ja   | Litouws              | Lt     | Philipijns                 | Tl           |
| Ests            | Et   | Lets                 | Lv     | Turks                 | Tr           |
| Baskisch              | Eu   | Maleisische               | Mevrouw     | Oekraïens               | Uk           |
| Farsi               | Fa   | Noors - BokmÃ¥l     | nb nb     | Urdu                    | Uw           |
| Fins             | fi   | Nederlands                   | nl     | Vietnamees              | Vi           |
| Frans              | fr   | Noors - Nynorsk     | nn     | Chinees - vereenvoudigd    | zh-hans zh-hans      |
| Galicisch            | Gl   | Pools                  | Pl     | Chinees - traditioneel   | zh-hant zh-hant      |
| Hebreeuws              | Hge   | Portuguese - Brazil     | pt-br  |                         |              |
| Hindi               | Hu   | Portugees - Portugal   | pt-pt  |                         |              |


> [!NOTE]
> Als u niet wilt dat er lokalisatie optreedt, moet u de taal naar het Engels dwingen

## <a name="next-steps"></a>Volgende stappen ##

- [Snel start azure mediaspeler](azure-media-player-quickstart.md)