---
title: Entiteiten
description: Definitie van entiteiten in het bereik van de Azure Remote Rendering API
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d7b9ecd048b080ae0ec9fd3fb7a4fb35009551b8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681946"
---
# <a name="entities"></a>Entiteiten

Een *entiteit* vertegenwoordigt een roerend object in de ruimte en is de fundamentele bouwsteen van op afstand gerenderde inhoud.

## <a name="entity-properties"></a>Entiteitseigenschappen

Entiteiten hebben een transformatie gedefinieerd door een positie, rotatie en schaal. Op zichzelf hebben entiteiten geen waarneembare functionaliteit. In plaats daarvan wordt gedrag toegevoegd via componenten, die zijn gekoppeld aan entiteiten. Als u bijvoorbeeld een [CutPlaneComponent](../overview/features/cut-planes.md) koppelt, wordt een cut-vlak gemaakt op de positie van de entiteit.

Het belangrijkste aspect van de entiteit zelf is de hiërarchie en de daaruit voortvloeiende hiërarchische transformatie. Wanneer bijvoorbeeld meerdere entiteiten als kinderen aan een gedeelde bovenliggende entiteit zijn gekoppeld, kunnen al deze entiteiten worden verplaatst, geroteerd en in samenschalen door de transformatie van de bovenliggende entiteit te wijzigen.

Een entiteit is uniek eigendom van de bovenliggende, wat `Entity.Destroy()`betekent dat wanneer de ouder wordt vernietigd met , zo zijn de kinderen en alle verbonden [componenten](components.md). Zo wordt het verwijderen van een model `Destroy` van de scène bereikt door `AzureSession.Actions.LoadModelAsync()` een beroep te `AzureSession.Actions.LoadModelFromSASAsync()`doen op de root-node van een model, geretourneerd door of de SAS-variant .

Entiteiten worden gemaakt wanneer de server inhoud laadt of wanneer de gebruiker een object aan de scène wil toevoegen. Als een gebruiker bijvoorbeeld een cutplane wil toevoegen om het interieur van een net te visualiseren, kan de gebruiker een entiteit maken waar het vlak moet bestaan en er vervolgens de component cut-vlak aan toevoegen.

## <a name="query-functions"></a>Queryfuncties

Er zijn twee typen queryfuncties op entiteiten: synchrone en asynchrone aanroepen. Synchrone query's kunnen alleen worden gebruikt voor gegevens die aanwezig zijn op de client en niet veel berekeningen met zich meebrengen. Voorbeelden hiervan zijn query's voor componenten, relatieve objecttransformaties of bovenliggende/onderliggende relaties. Asynchrone query's worden gebruikt voor gegevens die zich alleen op de server bevinden of extra berekeningen met zich meebrengt die te duur zijn om op de client uit te voeren. Voorbeelden zijn ruimtelijke grenzen query's of meta gegevens query's.

### <a name="querying-components"></a>Onderdelen opvragen

Als u een onderdeel van `FindComponentOfType`een bepaald type wilt vinden, gebruikt u :

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Transformaties opvragen

Transformatiequery's zijn synchrone aanroepen op het object. Het is belangrijk op te merken dat transformaties die via de API worden opgevraagd, lokale ruimtetransformaties zijn ten opzichte van de bovenliggende objecten. Uitzonderingen zijn wortelobjecten, waarvoor de lokale ruimte en de wereldruimte identiek zijn.

> [!NOTE]
> Er is geen speciale API voor het opvragen van de wereldruimtetransformatie van willekeurige objecten.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### <a name="querying-spatial-bounds"></a>Ruimtelijke grenzen opvragen

Grenzenquery's zijn asynchrone aanroepen die werken op een volledige objecthiërarchie, met één entiteit als hoofdmap. Zie het speciale hoofdstuk over [objectgrenzen.](object-bounds.md)

### <a name="querying-metadata"></a>Metagegevens opvragen

Metagegevens zijn aanvullende gegevens die zijn opgeslagen op objecten, die door de server worden genegeerd. Objectmetagegevens zijn in wezen een set (naam, waarde) paren, waarbij _waarde_ van numeriek, booleaan- of snaartype kan zijn. Metagegevens kunnen met het model worden geëxporteerd.

Metagegevensquery's zijn asynchrone aanroepen op een specifieke entiteit. De query retourneert alleen de metagegevens van één entiteit, niet de samengevoegde informatie van een subgrafiek.

```cs
MetadataQueryAsync metaDataQuery = entity.QueryMetaDataAsync();
metaDataQuery.Completed += (MetadataQueryAsync query) =>
{
    if (query.IsRanToCompletion)
    {
        ObjectMetaData metaData = query.Result;
        ObjectMetaDataEntry entry = metaData.GetMetadataByName("MyInt64Value");
        System.Int64 intValue = entry.AsInt64;

        // ...
    }
};
```

De query zal slagen, zelfs als het object geen metagegevens bevat.

## <a name="next-steps"></a>Volgende stappen

* [Onderdelen](components.md)
* [Objectgrenzen](object-bounds.md)
