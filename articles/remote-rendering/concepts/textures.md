---
title: Texturen
description: Werkstroom voor structuurbronnen
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681660"
---
# <a name="textures"></a>Texturen

Texturen zijn een [onveranderlijke gedeelde bron.](../concepts/lifetime.md) Texturen kunnen worden geladen vanuit [blobopslag](../how-tos/conversion/blob-storage.md) en direct op modellen worden toegepast, zoals aangetoond in [Zelfstudie: De omgeving en materialen wijzigen.](../tutorials/unity/changing-environment-and-materials.md) Meestal, hoewel, texturen zal deel uitmaken van een [geconverteerd model](../how-tos/conversion/model-conversion.md), waar ze worden verwezen door de [materialen](materials.md).

## <a name="texture-types"></a>Structuurtypen

Verschillende structuurtypen hebben verschillende use cases:

* **2D Textures** worden voornamelijk gebruikt in [materialen.](materials.md)
* **Cubemaps** kunnen worden gebruikt voor de [hemel.](../overview/features/sky.md)

## <a name="supported-texture-formats"></a>Ondersteunde structuurindelingen

Alle texturen die aan ARR worden gegeven, moeten in [DDS-formaat](https://en.wikipedia.org/wiki/DirectDraw_Surface)zijn. Bij voorkeur met mipmaps en textuurcompressie. Raadpleeg [het texconv-opdrachtregelgereedschap](../resources/tools/tex-conv.md) als u het conversieproces wilt automatiseren.

## <a name="loading-textures"></a>Texturen laden

Wanneer u een structuur laadt, moet u het verwachte type opgeven. Als het type niet overeenkomt, mislukt de textuurbelasting.
Als u een structuur met dezelfde URI twee keer laadt, wordt hetzelfde structuurobject als een [gedeelde bron weergegeven.](../concepts/lifetime.md)

Net als bij laadmodellen zijn er twee varianten van het aanpakken van een structuurasset in bronblobopslag:

* De structuurasset kan worden aangepakt door de SAS URI. De relevante `LoadTextureFromSASAsync` ladingsfunctie is met parameter `LoadTextureFromSASParams`. Gebruik deze variant ook bij het laden [van ingebouwde texturen.](../overview/features/sky.md#built-in-environment-maps)
* De structuur kan direct worden aangepakt door blobopslagparameters, voor het geval de [blobopslag is gekoppeld aan het account.](../how-tos/create-an-account.md#link-storage-accounts) De relevante ladingsfunctie `LoadTextureAsync` is `LoadTextureParams`in dit geval met parameter .

De volgende voorbeeldcode laat zien hoe u een textuur laadt via de SAS URI (of ingebouwde structuur) - merk op dat alleen de laadfunctie/parameter verschilt voor het andere geval:

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

Afhankelijk van waarvoor de textuur moet worden gebruikt, kunnen er beperkingen zijn voor het type textuur en de inhoud. De ruwheidskaart van een [PBR-materiaal](../overview/features/pbr-materials.md) moet bijvoorbeeld grijswaarden zijn.

> [!CAUTION]
> Alle *Async-functies* in ARR retourneren asynchrone bewerkingsobjecten. U moet een verwijzing naar deze objecten opslaan totdat de bewerking is voltooid. Anders kan de C# garbage collector de bewerking vroeg verwijderen en kan deze nooit worden voltooid. In de voorbeeldcode hierboven wordt de lidvariabele '_textureLoad' gebruikt om een verwijzing vast te houden totdat de *voltooide* gebeurtenis aankomt.

## <a name="next-steps"></a>Volgende stappen

* [Materialen](materials.md)
* [Sky](../overview/features/sky.md)
