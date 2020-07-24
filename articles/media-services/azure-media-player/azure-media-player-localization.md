---
title: Lokalisatie van Azure Media Player
description: Ondersteuning voor meerdere talen voor gebruikers van niet-Engelse land instellingen.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 8a459d1fba4353b4b3e092e83a759314cc455ead
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087285"
---
# <a name="azure-media-player-localization"></a>Lokalisatie van Azure Media Player #

Dankzij ondersteuning voor meerdere talen kunnen gebruikers van niet-Engelse land instellingen systeem eigen communiceren met de speler. Azure Media Player wordt geïnstantieerd met een algemene woorden lijst met talen, waarmee de fout berichten worden gelokaliseerd op basis van de pagina taal. Een ontwikkelaar kan ook een Player-exemplaar maken met een geforceerd ingestelde taal. De standaard taal is Engels (en).

> [!NOTE]
> Deze functie is nog steeds bezig met testen en is afhankelijk van fouten.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player ondersteunt momenteel de volgende talen met de bijbehorende taal codes:

| Taal            | Code | Taal                | Code   | Taal                | Code         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Engels {standaard}   | nl   | Kroatisch                | uur     | Roemeens                | Roll           |
| Arabisch              | p.a.   | Hongaars               | hu     | Slowaaks                  | SK           |
| Bulgaars           | bg   | Indonesisch              | id     | Slovene                 | lineaire           |
| Catalaans             | certificering   | IJslands               | is     | Servisch - Cyrillisch      | SR-Cyrl-CS   |
| Tsjechisch               | Support   | Italiaans                 | it     | Servisch - Latijn         | sr-latn-rs   |
| Deens              | da   | Japans                | ja     | Russisch                 | ru           |
| Duits              | de   | Kazachs                  | kk     | Zweeds                 | sv           |
| Grieks               | ei   | Koreaans                  | ko     | Thai                    | e           |
| Spaans             | Ja   | Litouws              | lt     | Tagalog                 | 't           |
| Ests            | et   | Lets                 | LV     | Turks                 | formulieren           |
| Baskisch              | EU   | Maleisische               | Mevrouw     | Oekraïens               | rijk           |
| Iraans               | volgt   | Noors-BokmÃ ¥ l     | NB     | Urdu                    | zelf           |
| Fins             | fi   | Nederlands                   | nl     | Vietnamees              | beschreven           |
| Frans              | fr   | Noors-Nynorsk     | nn     | Chinees-vereenvoudigd    | zh-Hans      |
| Galicisch            | boekhoud   | Pools                  | pl     | Chinees-traditioneel   | zh-hant      |
| Hebreeuws              | Hij   | Portuguese - Brazil     | pt-br  |                         |              |
| Hindi               | Hallo   | Portugees - Portugal   | pt-pt  |                         |              |


> [!NOTE]
> Als u niet wilt dat een lokalisatie plaatsvindt, moet u de taal afdwingen voor Engels

## <a name="next-steps"></a>Volgende stappen ##

- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)
