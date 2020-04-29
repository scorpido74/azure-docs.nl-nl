---
title: Werken met externe entiteiten in Unity
description: Zelf studie waarin wordt uitgelegd hoe u met ARR-entiteiten werkt.
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: db1f6a53121e05b29f7e3441af027985a141bc2e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81310210"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>Zelf studie: werken met externe entiteiten in Unity

[Zelf studie: voor het instellen van een eenheids project](project-setup.md) hebt u geleerd hoe u een nieuw unit-project kunt configureren voor gebruik met de externe rendering van Azure. In deze zelf studie hebben we een overzicht van de meest voorkomende functionaliteit die elke ARR-gebruiker nodig heeft.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Objecten selecteren met behulp van Ray-casts.
> * Object statussen onderdrukken, zoals tint kleur, selectie status en zicht baarheid.
> * Externe entiteiten verwijderen.
> * Verplaats externe entiteiten rond.
> * Gebruik geknipte abonnementen om binnen objecten te zoeken.

## <a name="prerequisites"></a>Vereisten

* Deze zelf studie bouwt bovenop [een zelf studie: een geheel nieuw eenheids project instellen](project-setup.md).

> [!TIP]
> De [opslag plaats](https://github.com/Azure/azure-remote-rendering) voor de ARR-voor beelden bevat voor bereide eenheids projecten voor alle zelf studies in de map *Unity* , die u als referentie kunt gebruiken.

## <a name="pick-objects"></a>Objecten kiezen

We willen met objecten werken, dus het eerste wat u nodig hebt, is het verzamelen van objecten onder de muis aanwijzer.

Maak een [Nieuw script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) met de naam **RemoteRaycaster** en vervang de volledige inhoud door de onderstaande code:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;

[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRaycaster : MonoBehaviour
{
    public double MaxDistance = 30.0;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        arrService = GetComponent<ARRServiceUnity>();
    }

    private async Task<Entity> RemoteRayCast(Vector3 origin, Vector3 dir)
    {
        Entity entity = null;

        var raycast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), MaxDistance, HitCollectionPolicy.ClosestHit);

        var hits = await arrService.CurrentActiveSession.Actions.RayCastQueryAsync(raycast).AsTask();

        if (hits != null)
        {
            foreach (var hit in hits)
            {
                var hitEntity = hit.HitEntity;
                if (hitEntity == null)
                {
                    continue;
                }

                entity = hitEntity;
                break;
            }
        }

        return entity;
    }

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
        var ray = Camera.main.ScreenPointToRay(position);

        Raycast(ray.origin, ray.direction);
    }

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var entity = await RemoteRayCast(origin, direction);
        if (entity != null)
        {
            Debug.Log("Object Hit: " + entity.Name);
        }
    }
}
```

Voeg dit onderdeel toe aan het object *RemoteRendering* in uw scène.

> [!WARNING]
>
> Het onderdeel *RemoteRaycaster* vereist dat een *ARRServiceUnity* -onderdeel aan hetzelfde object is gekoppeld. *ARRServiceUnity* is een helperklasse waarmee u eenvoudiger toegang hebt tot bepaalde ARR-functies. Er kan echter slechts één exemplaar van dit onderdeel in de scène zijn. Zorg er daarom voor dat u alle onderdelen toevoegt waarvoor *ARRServiceUnity* is vereist voor dezelfde GameObject.
> Als u de ARR-functionaliteit van meerdere spel objecten wilt gebruiken, voegt u de *ARRServiceUnity* -component alleen toe aan een van deze elementen en verwijst u naar die in de andere scripts, of opent u de ARR-functionaliteit rechtstreeks.

Druk op afspelen, maak verbinding met een sessie en laad een model. Wijs nu objecten in de scène aan en Bekijk de uitvoer van de console. De naam van het object moet worden afgedrukt van elk deel waarvan u de muis aanwijzer over hebt.

## <a name="highlight-objects"></a>Objecten markeren

Als volgende stap willen we visuele feedback geven over de onderdelen van een model waarnaar de gebruiker verwijst. Om dit te verhalen, koppelen we een [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) aan de entiteit die we hebben gekozen. Dit onderdeel kan worden gebruikt om verschillende functies voor een object in of uit te scha kelen. Hier gebruiken we het om een tint kleur in te stellen en de [Contour weergave](../../overview/features/outlines.md)in te scha kelen.

Maak een ander script bestand met de naam **RemoteModelEntity** en vervang de inhoud door de volgende code:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

public class RemoteModelEntity : MonoBehaviour
{
    public Color HighlightColor = new Color(1.0f, 0.0f, 0.0f, 0.1f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;

    private RemoteEntitySyncObject localSyncObject = null;
    private ARRHierarchicalStateOverrideComponent localStateOverride = null;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();
        localStateOverride = GetComponent<ARRHierarchicalStateOverrideComponent>();

        if (localStateOverride == null)
        {
            localStateOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
            var remoteStateOverride = localSyncObject.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

            if (remoteStateOverride == null)
            {
                // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                localStateOverride.Create(RemoteManagerUnity.CurrentSession);
            }
            else
            {
                // otherwise, bind our local stateOverride component to the remote component
                localStateOverride.Bind(remoteStateOverride);
            }
        }

        localStateOverride.RemoteComponent.TintColor = HighlightColor.toRemote();
    }

    public void OnDisable()
    {
        SetStateOverride(false);

        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.Enabled = false;
            localStateOverride.enabled = false;
        }
    }

    private void SetStateOverride(bool on)
    {
        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.UseTintColorState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.SelectedState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);
    }
}
```
> [!CAUTION]
> Wijs dit script niet toe aan een spel object, omdat het via een programma via de onderstaande code wordt toegewezen.

Daarna moeten we onze *RemoteRaycaster* uitbreiden om de *RemoteModelEntity* -component toe te voegen aan het object dat we zojuist hebben gekozen.

Voeg de volgende code toe aan de **RemoteRaycaster** -implementatie en verwijder de dubbele functies:

```csharp
    private RemoteModelEntity focusedModel = null;

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var remoteEntity = await RemoteRayCast(origin, direction);

        if (focusedModel != null)
        {
            if (focusedModel.Entity == remoteEntity)
            {
                // picked the same object as before
                return;
            }

            ClearFocus();
        }

        if (remoteEntity == null)
        {
            // picked no object at all
            return;
        }

        // get the instance of a Unity GameObject for the ARR entity
        var entityGO = remoteEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the game object has the RemoteModelEntity component
        focusedModel = entityGO.GetComponent<RemoteModelEntity>();
        if (focusedModel == null)
        {
            focusedModel = entityGO.AddComponent<RemoteModelEntity>();
        }

        focusedModel.SetFocus(true);
    }

    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        focusedModel = null;
    }
```

Voer uw project uit en wijs een model aan. u ziet nu een rode tint en een wit selectie kader.

## <a name="isolate-the-selected-object"></a>Het geselecteerde object isoleren

Een ander gebruik van de [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) is de mogelijkheid om zicht baarheid te negeren. Zo kunt u een geselecteerd object isoleren van de rest van het model. Open het script **RemoteModelEntity** , voeg de volgende code toe en verwijder de dubbele functies:

```csharp
    private bool isolated = false;
    private HierarchicalStateOverrideComponent parentOverride = null;

    public void ToggleIsolate()
    {
        SetIsolated(!isolated);
    }

    public void SetIsolated(bool on)
    {
        if (localStateOverride == null || !localStateOverride.IsComponentValid || isolated == on)
        {
            return;
        }

        // find the top most parent object that has a HierarchicalStateOverrideComponent
        if (parentOverride == null)
        {
            var modelRoot = transform;

            while (modelRoot.parent != null)
            {
                modelRoot = modelRoot.parent;

                var parentSyncObject = modelRoot.GetComponent<RemoteEntitySyncObject>();

                var stateOverrideComp = parentSyncObject?.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                if (stateOverrideComp != null)
                {
                    parentOverride = stateOverrideComp;
                }
            }
        }

        if (parentOverride != null)
        {
            isolated = on;

            parentOverride.HiddenState = isolated ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.HiddenState = isolated ? HierarchicalEnableState.ForceOff : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);

        if (!on)
        {
            SetIsolated(false);
        }
    }
```

Deze code is afhankelijk van een status onderdrukkings onderdeel op het bovenste object in de hiërarchie, waardoor alle objecten onzichtbaar worden. Vervolgens wordt de zicht baarheid van het geselecteerde object overschreven om ervoor te zorgen dat het ene object zichtbaar is. Daarom moeten we een onderdeel voor status onderdrukking maken op het hoofd object.

Open het script **RemoteRendering** en voeg de volgende code toe boven aan de *LoadModel* -functie:

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

Ten slotte is er een manier nodig om zicht baarheid te scha kelen. Open het **RemoteRaycaster** -script en vervang de functie *Update* :

```csharp
    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        var ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Raycast(ray.origin, ray.direction);

        if (Input.GetMouseButtonDown(1) && focusedModel != null)
        {
            focusedModel.ToggleIsolate();
        }
    }
```

Voer de code uit en klik met de rechter muisknop op een deel van het model. De rest van het model verdwijnt en alleen het gemarkeerde onderdeel blijft zichtbaar.

## <a name="remove-gameobject-instances-of-remote-entities"></a>GameObject exemplaren van externe entiteiten verwijderen

Mogelijk hebt u opgemerkt dat de code blijft bezig met het maken van objecten, maar u kunt ze nooit opschonen. Dit wordt ook weer gegeven in het deel venster object hiërarchie. Wanneer u de externe object hiërarchie uitvouwt tijdens simulatie, ziet u dat er meer objecten worden weer gegeven telkens wanneer u de muis aanwijzer boven een nieuw deel van het model houdt.

Het hebben van veel objecten in een scène heeft een negatieve invloed op de prestaties. U moet altijd objecten opschonen die niet meer nodig zijn.

Voeg de onderstaande code toe aan het script **RemoteRaycaster** en verwijder de dubbele functies:

```csharp
    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        CleanHierarchy(focusedModel.gameObject);
        focusedModel = null;
    }

    private void CleanHierarchy(GameObject focusedGO)
    {
        var sync = focusedGO?.GetComponent<RemoteEntitySyncObject>();
        if (sync == null || !sync.IsEntityValid)
        {
            return;
        }

        sync.Entity.DestroyGameObject(EntityExtensions.DestroyGameObjectFlags.DestroyEmptyParents | EntityExtensions.DestroyGameObjectFlags.KeepRemoteRoot);
    }
```

## <a name="move-objects"></a>Objecten verplaatsen

Als volgende stap willen we een geselecteerd object verplaatsen. In het script **RemoteRaycaster** voegt u deze code in en verwijdert u de functie duplicate:

```csharp
    private Vector3 lastPosition = Vector3.zero;

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        if (Input.GetMouseButton(0))
        {
            if (focusedModel)
            {
                // note: mousePosition is in 2D screen-space coordinates and has no relation with
                // the 3D object position. This just happens to work good enough for demonstration.
                var delta = Input.mousePosition - lastPosition;
                focusedModel.transform.position += delta * Time.deltaTime;
            }
        }
        else
        {
            Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
            var ray = Camera.main.ScreenPointToRay(position);

            Raycast(ray.origin, ray.direction);

            if (Input.GetMouseButtonDown(1) && focusedModel != null)
            {
                focusedModel.ToggleIsolate();
            }
        }

        lastPosition = Input.mousePosition;
    }
```

> [!IMPORTANT]
> Als u deze code uitvoert, zult u merken dat er niets gebeurt. Dat komt doordat het wijzigen van de trans formatie van een object de status wijziging niet automatisch synchroniseert naar de server, om prestatie redenen. In plaats daarvan moet u deze status wijziging hand matig pushen naar de-server of **SyncEveryFrame** inschakelen voor het onderdeel *RemoteEntitySyncObject* .

Open het script **RemoteModelEntity** en voeg deze regel toe:

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

Als u de code opnieuw uitvoert, kunt u met de rechter muisknop op een object klikken en dit slepen.

## <a name="add-a-cut-plane"></a>Een knip vlak toevoegen

De laatste functie die we willen uitproberen in deze zelf studie, gebruikt [geknipte abonnementen](../../overview/features/cut-planes.md). Een knip vlak vermindert delen van gerenderde objecten, zodat u erin kunt zoeken.

Maak een nieuwe GameObject in de scène- **CutPlane**. Maak een nieuw script en noem het **RemoteCutPlane**. Voeg het onderdeel toe aan het nieuwe GameObject.

Open het script bestand en vervang de inhoud door de volgende code:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteCutPlane : MonoBehaviour
{
    private ARRCutPlaneComponent localCutPlaneComponent = null;
    private RemoteEntitySyncObject remoteEntitySync = null;

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            // can't do anything while we are not connected
            return;
        }

        if (localCutPlaneComponent == null)
        {
            localCutPlaneComponent = gameObject.CreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
        }

        if (remoteEntitySync == null)
        {
            remoteEntitySync = gameObject.GetComponent<RemoteEntitySyncObject>();
            remoteEntitySync.SyncEveryFrame = true;
        }

        localCutPlaneComponent.RemoteComponent.Normal = Axis.X;
        localCutPlaneComponent.RemoteComponent.FadeLength = 0.025f;
        localCutPlaneComponent.RemoteComponent.FadeColor = new Color4Ub(255, 128, 0, 255);
        localCutPlaneComponent.RemoteComponent.Enabled = true;
    }

    void OnDisable()
    {
        if (localCutPlaneComponent && localCutPlaneComponent.IsComponentValid)
        {
            localCutPlaneComponent.RemoteComponent.Enabled = false;
        }

        if (remoteEntitySync && remoteEntitySync.IsEntityValid)
        {
            remoteEntitySync.SyncEveryFrame = false;
        }
    }
}
```

Wanneer u de code nu uitvoert, ziet u hoe het model geopend is door het vlak. U kunt het *CutPlane* -object selecteren en dit verplaatsen en roteren in het *scène* venster. U kunt het Knip vlak in-en uitschakelen door het Knip vlak object uit te scha kelen.

## <a name="next-steps"></a>Volgende stappen

U kent nu de belangrijkste functionaliteit voor interactie met externe objecten. In de volgende zelf studie ziet u hoe u het uiterlijk van een scène kunt aanpassen.

> [!div class="nextstepaction"]
> [Zelf studie: de omgeving en het materiaal wijzigen](changing-environment-and-materials.md)
