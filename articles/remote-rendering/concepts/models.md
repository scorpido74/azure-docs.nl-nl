---
title: Modellen
description: Beschrijft wat een model is in Azure Remote Rendering
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 5d737b1e85a28661a7491b8d2822e6472538c7a1
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617942"
---
# <a name="models"></a>Modellen

Een *model* in Azure Remote Rendering verwijst naar een volledige objectweergave, bestaande uit [entiteiten](entities.md) en [componenten.](components.md) Modellen zijn de belangrijkste manier om aangepaste gegevens in de remote rendering service te krijgen.

## <a name="model-structure"></a>Modelstructuur

Een model heeft precies één [entiteit](entities.md) als hoofdknooppunt. Daaronder kan het een willekeurige hiërarchie van onderliggende entiteiten hebben. Bij het laden van een model wordt een verwijzing naar deze hoofdentiteit geretourneerd.

Aan elke entiteit kunnen [onderdelen](components.md) zijn gekoppeld. In het meest voorkomende geval hebben de entiteiten *MeshComponents*, die verwijzen naar [mesh-bronnen](meshes.md).

## <a name="creating-models"></a>Modellen maken

Het maken van modellen voor runtime wordt bereikt door invoermodellen van bestandsformaten zoals FBX en GLTF [te converteren.](../how-tos/conversion/model-conversion.md) Het conversieproces haalt alle resources, zoals texturen, materialen en mazen, en zet deze om in geoptimaliseerde runtime-indelingen. Het zal ook de structurele informatie extraheren en omzetten in arr's entiteit / component grafiek structuur.

> [!IMPORTANT]
>
> [Modelconversie](../how-tos/conversion/model-conversion.md) is de enige manier om [mazen te](meshes.md)maken. Hoewel mazen kunnen worden gedeeld tussen entiteiten tijdens runtime, is er geen andere manier om een mesh in de runtime te krijgen, anders dan het laden van een model.

## <a name="loading-models"></a>Laadmodellen

Zodra een model is geconverteerd, kan het vanuit Azure blob-opslag in de runtime worden geladen.

Er zijn twee verschillende laadfuncties die verschillen door de manier waarop het element wordt aangepakt in blob-opslag:

* Het model kan worden aangepakt door de SAS URI. De relevante `LoadModelFromSASAsync` ladingsfunctie is met parameter `LoadModelFromSASParams`. Gebruik deze variant ook bij het laden [van ingebouwde modellen.](../samples/sample-model.md)
* Het model kan direct worden aangepakt door blobopslagparameters, voor het geval de [blob-opslag is gekoppeld aan het account.](../how-tos/create-an-account.md#link-storage-accounts) De relevante ladingsfunctie `LoadModelAsync` is `LoadModelParams`in dit geval met parameter .

In de volgende codefragmenten ziet u hoe u modellen met beide functies laadt. Als u een model wilt laden met de SAS URI, gebruikt u code zoals hieronder:

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelFromSASParams(modelUri, modelParent);

    var loadOp = session.Actions.LoadModelFromSASAsync(modelParams);

    loadOp.ProgressUpdated += (float progress) =>
    {
        Debug.Log($"Loading: {progress * 100.0f}%");
    };

    await loadOp.AsTask();
}
```

Als u een model wilt laden door rechtstreeks gebruik te maken van de blobopslagparameters, gebruikt u code die vergelijkbaar is met het volgende fragment:

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelParams(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Actions.LoadModelAsync(modelParams);

    // ... (identical to the SAS URI snippet above)
}
```

Daarna u de entiteitshiërarchie doorlopen en de entiteiten en componenten wijzigen. Als u hetzelfde model meerdere keren laadt, worden meerdere exemplaren gemaakt, elk met hun eigen kopie van de entiteit/componentstructuur. Aangezien mazen, materialen en texturen [gedeelde bronnen](../concepts/lifetime.md)zijn, worden hun gegevens echter niet opnieuw geladen. Daarom loopt het instantieren van een model meer dan eens relatief weinig geheugen overhead.

> [!CAUTION]
> Alle *Async-functies* in ARR retourneren asynchrone bewerkingsobjecten. U moet een verwijzing naar deze objecten opslaan totdat de bewerking is voltooid. Anders kan de C# garbage collector de bewerking vroeg verwijderen en kan deze nooit worden voltooid. In de bovenstaande voorbeeldcode garandeert het gebruik van *wachtgaranties* dat de lokale variabele 'loadOp' een referentie bevat totdat het laden van het model is voltooid. Als u de gebeurtenis *Voltooid* echter zou gebruiken, moet u de asynchrone bewerking opslaan in een lidvariabele.

## <a name="next-steps"></a>Volgende stappen

* [Entiteiten](entities.md)
* [Meshes](meshes.md)
