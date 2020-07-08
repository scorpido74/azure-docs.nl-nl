---
title: Modellen
description: Beschrijft wat een model in azure remote rendering is
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 7832f999de2f6f16cfe816c061925e371f90662e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83758687"
---
# <a name="models"></a>Modellen

Een *model* in azure rendering op afstand verwijst naar een volledige object weergave, samengesteld uit [entiteiten](entities.md) en [onderdelen](components.md). Modellen vormen de belangrijkste manier om aangepaste gegevens op te halen in de externe rendering-service.

## <a name="model-structure"></a>Modelstructuur

Een model heeft precies één [entiteit](entities.md) als het hoofd knooppunt. Hieronder ziet u mogelijk een wille keurige hiërarchie van onderliggende entiteiten. Bij het laden van een model wordt een verwijzing naar deze hoofd entiteit geretourneerd.

Aan elke entiteit kunnen [onderdelen](components.md) zijn gekoppeld. In het meest voorkomende geval hebben de entiteiten *MeshComponents*, die verwijzen naar [netresources](meshes.md).

## <a name="creating-models"></a>Modellen maken

Het maken van modellen voor runtime wordt bereikt door het [converteren van invoer modellen](../how-tos/conversion/model-conversion.md) van bestands indelingen, zoals FBX en GLTF. Het conversie proces extraheert alle resources, zoals bitmappatronen, materialen en netten, en converteert deze naar geoptimaliseerde runtime-indelingen. Ook wordt de structuur informatie geëxtraheerd en wordt deze geconverteerd naar de grafiek structuur van de entiteit/het onderdeel van ARR.

> [!IMPORTANT]
>
> [Model conversie](../how-tos/conversion/model-conversion.md) is de enige manier om [netten](meshes.md)te maken. Hoewel netten kunnen worden gedeeld tussen entiteiten tijdens runtime, is er geen andere manier om een net in de runtime op te halen, met uitzonde ring van het laden van een model.

## <a name="loading-models"></a>Modellen laden

Zodra een model is geconverteerd, kan het worden geladen vanuit Azure Blob-opslag in de runtime.

Er zijn twee verschillende laad functies die verschillen van de manier waarop de Asset wordt opgelost in Blob Storage:

* Het model kan worden aangepakt door de SAS-URI. Relevante laad functie is `LoadModelFromSASAsync` met para meter `LoadModelFromSASParams` . Gebruik deze variant ook bij het laden van [ingebouwde modellen](../samples/sample-model.md).
* Het model kan rechtstreeks worden aangepakt door de Blob Storage-para meters, [als de Blob-opslag aan het account is gekoppeld](../how-tos/create-an-account.md#link-storage-accounts). Relevante laad functie in dit geval is een `LoadModelAsync` para meter `LoadModelParams` .

De volgende code fragmenten laten zien hoe u modellen met beide functies kunt laden. Als u een model wilt laden met behulp van de SAS-URI, gebruikt u de code zoals hieronder wordt beschreven:

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

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSASParams modelParams;
    modelParams.ModelUrl = modelUri;
    modelParams.Parent = modelParent;

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelFromSASAsync(modelParams);

    loadOp->Completed([](const ApiHandle<LoadModelAsync>& async)
    {
        printf("Loading: finished.");
    });
    loadOp->ProgressUpdated([](float progress)
    {
        printf("Loading: %.1f%%", progress*100.f);
    });

    return loadOp;
}
```

Als u een model wilt laden door rechtstreeks de para meters voor de Blob-opslag te gebruiken, gebruikt u code die vergelijkbaar is met het volgende fragment:

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

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelParams modelParams;
    modelParams.Parent = modelParent;
    modelParams.Blob.StorageAccountName = std::move(storageAccount);
    modelParams.Blob.BlobContainerName = std::move(containerName);
    modelParams.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelAsync(modelParams);
    // ... (identical to the SAS URI snippet above)
}
```

Daarna kunt u de entiteits hiërarchie door lopen en de entiteiten en onderdelen wijzigen. Als hetzelfde model meerdere keren wordt geladen, worden meerdere exemplaren gemaakt, elk met een eigen kopie van de entiteit/onderdeel structuur. Omdat mazen, materialen en bitmappatronen [gedeelde resources](../concepts/lifetime.md)zijn, worden hun gegevens niet opnieuw geladen. Daarom kan een model meer dan één keer worden gemodelleerd met relatief weinig geheugen overhead.

> [!CAUTION]
> Alle *async* -functies in ARR retour neren asynchrone bewerkings objecten. U moet een verwijzing naar deze objecten opslaan totdat de bewerking is voltooid. Anders kan de C#-garbage collector de bewerking vroegtijdig verwijderen en kan deze nooit worden voltooid. In de voorbeeld code hierboven is het gebruik van een *wacht* wijze gegarandeerd dat de lokale variabele ' loadOp ' een verwijzing bevat totdat het laden van het model is voltooid. Als u echter in plaats daarvan de *voltooide* gebeurtenis zou gebruiken, moet u de asynchrone bewerking opslaan in een lidvariabele.

## <a name="next-steps"></a>Volgende stappen

* [Entiteiten](entities.md)
* [Meshes](meshes.md)
