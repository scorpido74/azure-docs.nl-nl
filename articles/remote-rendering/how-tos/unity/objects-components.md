---
title: Unity game objecten en componenten
description: Beschrijft Unity-specifieke methoden om te werken met entiteiten en componenten voor externe rendering.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.openlocfilehash: a34276c73211c1d9bea291f449cbc7041a3e78a2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409861"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interactie met Unity-gameobjecten en -onderdelen

Azure Remote Rendering (ARR) is geoptimaliseerd voor grote aantallen objecten (zie [Beperkingen).](../../reference/limits.md) Hoewel het mogelijk is om grote en complexe hiërarchieën op de host te beheren, is het niet haalbaar om ze allemaal te repliceren in Unity op apparaten met een laag effect.

Wanneer een model op de host wordt geladen, weerspiegelt Azure Remote Rendering daarom de informatie over de modelstructuur op het clientapparaat (waardoor netwerkverkeer wordt uitgevoerd), maar worden de objecten en componenten in Unity niet gerepliceerd. In plaats daarvan verwacht het dat u de benodigde Unity-gameobjecten en -componenten handmatig opvraagt, zodat u de overhead beperken tot wat er daadwerkelijk nodig is. Op deze manier hebt u meer controle over de prestaties aan de clientzijde.

Daarom wordt de Unity-integratie van Azure Remote Rendering geleverd met extra functionaliteit om de remote rendering-structuur op aanvraag te repliceren.

## <a name="load-a-model-in-unity"></a>Een model laden in Unity

Wanneer u een model laadt, krijgt u een verwijzing naar het hoofdobject van het geladen model. Deze verwijzing is geen Unity-spelobject, maar je `Entity.GetOrCreateGameObject()`er wel een maken met behulp van de extensiemethode. Die functie verwacht een `UnityCreationMode`argument van het type . Als u `CreateUnityComponents`slaagt, wordt het nieuw gemaakte Unity-spelobject bovendien gevuld met proxycomponenten voor alle externe renderingcomponenten die op de host bestaan. Het wordt echter aanbevolen om `DoNotCreateUnityComponents`de overhead minimaal te houden.

### <a name="load-model-with-task"></a>Laadmodel met taak

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

### <a name="load-model-with-unity-coroutines"></a>Laadmodel met Unity-coroutines

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

### <a name="load-model-with-await-pattern"></a>Laadmodel met wachtpatroon

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

De bovenstaande codevoorbeelden gebruikten het laadpad van het model via SAS omdat het ingebouwde model is geladen. Het aanpakken van het `LoadModelAsync` model `LoadModelParams`via blob containers (met behulp van en) werkt volledig analoog.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Als u een Unity-spelobject maakt, voegt u impliciet een `RemoteEntitySyncObject` component toe aan het spelobject. Deze component wordt gebruikt om de entiteitstransformatie naar de server te synchroniseren. Standaard `RemoteEntitySyncObject` moet de gebruiker expliciet `SyncToRemote()` bellen om de lokale Unity-status met de server te synchroniseren. Als `SyncEveryFrame` u het object inschakelt, wordt het object automatisch gesynchroniseerd.

Objecten met `RemoteEntitySyncObject` een kunnen hun afgelegen kinderen laten instantiaten en getoond worden in de Unity-editor via de knop **Kinderen weergeven.**

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Wrappercomponenten

[Componenten](../../concepts/components.md) die zijn gekoppeld aan entiteiten met `MonoBehavior`externe rendering worden blootgesteld aan Unity via proxy s. Deze volmachten vertegenwoordigen de externe component in Unity en sturen alle wijzigingen door naar de host.

Als u proxy-componenten voor externe `GetOrCreateArrComponent`rendering wilt maken, gebruikt u de extensiemethode:

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Gekoppelde levens

De levensduur van een externe [entiteit](../../concepts/entities.md) en een Unity-spelobject is gekoppeld terwijl ze gebonden zijn door een `RemoteEntitySyncObject`. Als u `UnityEngine.Object.Destroy(...)` belt met een dergelijk spelobject, wordt de externe entiteit ook verwijderd.

Om het Unity-spelobject te vernietigen, zonder de `Unbind()` externe `RemoteEntitySyncObject`entiteit te beïnvloeden, moet u eerst een beroep doen op de .

Hetzelfde geldt voor alle proxy-componenten. Als u alleen de vertegenwoordiging aan `Unbind()` de clientzijde wilt vernietigen, moet u eerst een beroep doen op de proxycomponent:

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
* [Zelfstudie: Werken met externe entiteiten in Unity](../../tutorials/unity/working-with-remote-entities.md)
