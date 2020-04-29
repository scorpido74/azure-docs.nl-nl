---
title: Patronen
description: Structuur resource werk stroom
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681660"
---
# <a name="textures"></a>Patronen

Bitmappatronen zijn een onveranderbare [gedeelde bron](../concepts/lifetime.md). Bitmappatronen kunnen worden geladen vanuit [Blob Storage](../how-tos/conversion/blob-storage.md) en worden toegepast op modellen, zoals wordt geïllustreerd in [zelf studie: de omgeving en het materiaal wijzigen](../tutorials/unity/changing-environment-and-materials.md). De meeste patronen zullen echter deel uitmaken van een [geconverteerd model](../how-tos/conversion/model-conversion.md), waar ze naar worden verwezen door het bijbehorende [materiaal](materials.md).

## <a name="texture-types"></a>Typen bitmappatronen

Verschillende typen bitmappatronen hebben verschillende use cases:

* **2D-bitmappatronen** worden voornamelijk gebruikt in [materialen](materials.md).
* **Cubemaps** kunnen worden gebruikt voor de [lucht](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Ondersteunde Texture-indelingen

Alle bitmappatronen die aan ARR zijn gegeven, moeten in [DDS-indeling](https://en.wikipedia.org/wiki/DirectDraw_Surface)zijn. Bij voor keur met mipmaps en texture compression. Zie [het opdracht regel programma TexConv](../resources/tools/tex-conv.md) als u het conversie proces wilt automatiseren.

## <a name="loading-textures"></a>Bitmappatronen laden

Wanneer u een bitmappatroon laadt, moet u het verwachte type opgeven. Als het type niet overeenkomt, mislukt het laden van de structuur.
Als u een bitmappatroon met dezelfde URI twee keer laadt, wordt hetzelfde object bitmappatroon geretourneerd, omdat het een [gedeelde bron](../concepts/lifetime.md)is.

Net als bij het laden van modellen zijn er twee varianten van het adresseren van een texture-asset in de bron-Blob-opslag:

* De Asset Texture kan worden aangepakt door de SAS-URI. Relevante laad functie is `LoadTextureFromSASAsync` met para `LoadTextureFromSASParams`meter. Gebruik deze variant ook bij het laden van [ingebouwde structuren](../overview/features/sky.md#built-in-environment-maps).
* Het bitmappatroon kan rechtstreeks worden aangepakt door de Blob Storage-para meters, [als de Blob-opslag is gekoppeld aan het account](../how-tos/create-an-account.md#link-storage-accounts). Relevante laad functie in dit geval is `LoadTextureAsync` een para `LoadTextureParams`meter.

De volgende voorbeeld code laat zien hoe u een structuur kunt laden via de SAS-URI (of het ingebouwde bitmappatroon): Houd er rekening mee dat alleen de functie/para meter voor het laden verschilt voor de andere Case:

``` cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureAsync(new LoadTextureParams(textureUri, TextureType.Texture2D));
    _textureLoad.Completed +=
        (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
            _textureLoad = null;
        };
}
```

Afhankelijk van waarvoor het bitmappatroon moet worden gebruikt, zijn er mogelijk beperkingen voor het type bitmappatroon en de inhoud. De grof toewijzing van een [PBR-materiaal](../overview/features/pbr-materials.md) moet bijvoorbeeld grijs waarden zijn.

> [!CAUTION]
> Alle *async* -functies in ARR retour neren asynchrone bewerkings objecten. U moet een verwijzing naar deze objecten opslaan totdat de bewerking is voltooid. Anders kan de C#-garbage collector de bewerking vroegtijdig verwijderen en kan deze nooit worden voltooid. In de voorbeeld code boven de lidvariabele _textureLoad wordt gebruikt om een verwijzing te bewaren totdat de *voltooide* gebeurtenis arriveert.

## <a name="next-steps"></a>Volgende stappen

* [Materialen](materials.md)
* [Heel](../overview/features/sky.md)
