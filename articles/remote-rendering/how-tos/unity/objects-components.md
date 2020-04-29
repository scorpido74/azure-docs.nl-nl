---
title: Unit-Game objecten en-onderdelen
description: Hierin worden specifieke methoden beschreven voor het werken met externe rendering-entiteiten en-onderdelen.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.openlocfilehash: a34276c73211c1d9bea291f449cbc7041a3e78a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81409861"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interactie met Unity-gameobjecten en -onderdelen

De externe rendering van Azure (ARR) is geoptimaliseerd voor grote aantallen objecten (Zie [beperkingen](../../reference/limits.md)). Hoewel het mogelijk is om grote en complexe hiërarchieën op de host te beheren, is het niet haalbaar om ze allemaal te repliceren in eenheid op apparaten met een lage voeding.

Wanneer een model op de host wordt geladen, komt de externe rendering van Azure overeen met de informatie over de model structuur op het client apparaat (waardoor netwerk verkeer wordt gedaan), maar worden de objecten en onderdelen in Unity niet gerepliceerd. In plaats daarvan moet u de benodigde unit-spel objecten en-onderdelen hand matig aanvragen, zodat u de overhead kunt beperken tot wat er echt nodig is. Op deze manier hebt u meer controle over de prestaties aan de client zijde.

De eenheids integratie van Azure remote rendering wordt daarom geleverd met extra functionaliteit voor het repliceren van de externe rendering-structuur op aanvraag.

## <a name="load-a-model-in-unity"></a>Een model in Unity laden

Wanneer u een model laadt, krijgt u een verwijzing naar het hoofd object van het geladen model. Deze verwijzing is geen unit-Game object, maar u kunt dit omzetten in een-bestand met behulp van de uitbreidings methode `Entity.GetOrCreateGameObject()`. Deze functie verwacht een argument van het `UnityCreationMode`type. Als u passeert `CreateUnityComponents`, wordt het nieuw gemaakte object Unity Game ook gevuld met proxy onderdelen voor alle onderdelen voor externe rendering die op de host bestaan. We raden `DoNotCreateUnityComponents`u echter aan om de overhead zo min mogelijk te houden.

### <a name="load-model-with-task"></a>Model laden met taak

```cs
LoadModelAsync _pendingLoadTask = null;
void LoadModelWithTask()
{
    _pendingLoadTask = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    _pendingLoadTask.Completed += (LoadModelAsync res) =>
    {
        // turn the root object into a Unity game object
        var gameObject = res.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        _pendingLoadTask = null;
    };

    // also listen to progress updates:
    _pendingLoadTask.ProgressUpdated += (float progress) =>
    {
        // progress is a fraction in [0..1] range
        int percentage = (int)(progress * 100.0f);
        // do something...
        // Since the updates are triggered by the main thread, we may access unity objects here.
    };
}
```

### <a name="load-model-with-unity-coroutines"></a>Model laden met Unit-routines

```cs
IEnumerator LoadModelWithCoroutine()
{
    LoadModelAsync task = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    while (!task.IsCompleted)
    {
        int percentage = (int)(task.Progress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }

    task = null;
}
```

### <a name="load-model-with-await-pattern"></a>Model laden met wachtend patroon

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

De bovenstaande code voorbeelden hebben het laad traject voor het model via SAS gebruikt, omdat het ingebouwde model is geladen. Het model wordt met behulp van de `LoadModelAsync` BLOB `LoadModelParams`-containers (met en) volledig vergelijkbaar.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Als u een unit-spel object maakt, `RemoteEntitySyncObject` wordt impliciet een onderdeel toegevoegd aan het spel object. Dit onderdeel wordt gebruikt om de entiteits transformatie te synchroniseren met de-server. De gebruiker `RemoteEntitySyncObject` moet standaard expliciet aanroepen `SyncToRemote()` om de status van de lokale eenheid te synchroniseren met de server. Als `SyncEveryFrame` u inschakelt, wordt het object automatisch gesynchroniseerd.

Objecten met een `RemoteEntitySyncObject` kunnen hun externe onderliggende items hebben geïnstantieerd en weer gegeven in de Unity-editor met de knop **onderliggende items weer geven** .

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Wrapper-onderdelen

[Onderdelen](../../concepts/components.md) die zijn gekoppeld aan externe rendering-entiteiten, worden blootgesteld `MonoBehavior`aan de eenheid via proxy s. Deze proxy's vertegenwoordigen het externe onderdeel in eenheid en sturen alle wijzigingen door naar de host.

Als u externe rendering-onderdelen voor proxy wilt maken, `GetOrCreateArrComponent`gebruikt u de extensie methode:

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Gekoppelde levens duur

De levens duur van een externe [entiteit](../../concepts/entities.md) en een unit-Game object wordt gekoppeld wanneer deze zijn gebonden `RemoteEntitySyncObject`via een. Als u een `UnityEngine.Object.Destroy(...)` dergelijk spel object aanroept, wordt de externe entiteit ook verwijderd.

Als u het eenheids spel object wilt vernietigen zonder dat dit van invloed is op de externe `Unbind()` entiteit, `RemoteEntitySyncObject`moet u eerst aanroepen op de.

Dit geldt ook voor alle proxy onderdelen. Als u alleen de client-side vertegenwoordiging wilt vernietigen, moet u `Unbind()` eerst het proxy-onderdeel aanroepen:

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>Volgende stappen

* [Remote Rendering voor Unity instellen](unity-setup.md)
* [Zelf studie: werken met externe entiteiten in Unity](../../tutorials/unity/working-with-remote-entities.md)
