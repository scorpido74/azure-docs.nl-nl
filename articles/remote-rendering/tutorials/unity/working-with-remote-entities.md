---
title: Werken met externe entiteiten in Unity
description: Zelfstudie waarin wordt uitgelegd hoe u met ARR-entiteiten werkt.
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: 5d995e9a5cdb6fc18532e0c3533959e9feece908
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021242"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>Zelfstudie: Werken met externe entiteiten in Unity

[Zelfstudie: een volledig nieuw Unity-project opstarten](project-setup.md) liet zien hoe u een nieuw Unity-project configureert om te kunnen werken met Azure Remote Rendering. In deze zelfstudie bekijken we de meest voorkomende functionaliteit die elke ARR-gebruiker nodig heeft.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Objecten kiezen met behulp van ray casts.
> * Objectstatussen overschrijven zoals tint, selectiestatus en zichtbaarheid.
> * Externe entiteiten verwijderen.
> * Externe entiteiten verplaatsen.
> * Geknipte vlakken gebruiken om in objecten te kijken.

## <a name="prerequisites"></a>Vereisten

* Deze zelfstudie borduurt voort op de [zelfstudie: een volledig nieuw Unity-project opstarten](project-setup.md).

> [!TIP]
> De [ARR-voorbeeldopslagplaats](https://github.com/Azure/azure-remote-rendering) bevat voorbereide Unity-projecten voor alle zelfstudies in de map *Unity*, die u als referentie kunt gebruiken.

## <a name="pick-objects"></a>Objecten kiezen

We willen interactie hebben met objecten, dus het eerste dat we nodig hebben, is het kiezen van objecten onder de muiscursor.

Maak een [nieuw script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) met de naam **RemoteRaycaster** en vervang de volledige inhoud door de onderstaande code:

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
> Voor het onderdeel *RemoteRaycaster* moet een *ARRServiceUnity-* onderdeel aan hetzelfde object zijn gekoppeld. *ARRServiceUnity* is een hulpklasse waarmee u eenvoudiger toegang krijgt tot bepaalde ARR-functies. Er kan echter maar één exemplaar van dit onderdeel in de scène zijn. Zorg er daarom voor dat u alle onderdelen waarvoor *ARRServiceUnity* is vereist aan hetzelfde GameObject toevoegt.
> Als u vanuit meerdere game-objecten toegang wilt krijgen tot de ARR-functionaliteit, voegt u het onderdeel *ARRServiceUnity* toe aan één van deze objecten en verwijst u daarnaar in de andere scripts of u benadert de ARR-functionaliteit rechtstreeks.

Druk op afspelen, maak verbinding met een sessie en laad een model. Wijs nu naar objecten in de scène en bekijk de uitvoer van de console. Deze moet de objectnaam afdrukken van elke tegel waar u met de muis overheen beweegt.

## <a name="highlight-objects"></a>Objecten markeren

Als volgende stap willen we visuele feedback geven over de tegels van een model waarnaar de gebruiker wijst. Hiervoor voegen we een [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) toe aan de entiteit die we hebben geselecteerd. Dit onderdeel kan worden gebruikt om verschillende functies van een object in of uit te schakelen. Hier gebruiken we het om een tint in te stellen en [contourweergave](../../overview/features/outlines.md) in te schakelen.

Maak een ander scriptbestand met de naam **RemoteModelEntity** en vervang de inhoud door de volgende code:

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
> Wijs dit script niet toe aan een Unity-gameobject, aangezien het door de onderstaande code programmatisch wordt toegewezen.

Daarna moeten we onze *RemoteRaycaster* uitbreiden om het onderdeel *RemoteModelEntity* toe te voegen aan het object dat we zojuist hebben geselecteerd.

Voeg de volgende code toe aan de implementatie van **RemoteRaycaster** en verwijder de dubbele functies:

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

Voer uw project uit en wijs naar een model. U zou moeten zien dat het een rode tint en witte contouren krijgt.

## <a name="isolate-the-selected-object"></a>Het geselecteerde object isoleren

Een ander manier waarop de [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) kan worden gebruikt is voor het overschrijven van zichtbaarheid. Hierdoor kunt u een geselecteerd object isoleren van de rest van het model. Open het **RemoteModelEntity**-script, voeg de volgende code toe en verwijder de dubbele functies:

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

Deze code is afhankelijk van het feit dat hij in het hoogste object in de hiërarchie moet beschikken over een statusoverschrijvend onderdeel, waardoor alle objecten onzichtbaar worden. Vervolgens wordt de zichtbaarheid van het geselecteerde object opnieuw overschreven zodat dat ene object zichtbaar wordt. Daarom moeten we een statusoverschrijvend onderdeel maken voor het object in de hoofdmap.

Open het **RemoteRendering**-script en voeg onderstaande code toe bovenaan de *LoadModel*-functie:

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

Eindelijk hebben we nog een manier nodig om de zichtbaarheid te veranderen. Open het **RemoteRaycaster**-script en vervang de functie *Bijwerken*:

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

Voer de code uit en klik met de rechtermuisknop op een tegel van het model. De rest van het model verdwijnt en alleen het gemarkeerde deel blijft zichtbaar.

## <a name="remove-gameobject-instances-of-remote-entities"></a>GameObject-exemplaren van externe entiteiten verwijderen

U hebt misschien gemerkt dat de code objecten blijft maken, maar ze nooit opruimt. Dit wordt ook weergegeven in het deelvenster objecthiërarchie. Wanneer u tijdens simulatie de hiërarchie van externe objecten uitbreidt,ziet u iedere keer dat u met een muis over een tegel van het model beweegt steeds meer objecten verschijnen.

Als er zich veel objecten in een scène bevinden, heeft dit een negatieve invloed op de prestaties. Objecten die niet meer worden gebruikt, moeten worden opgeschoond.

Voer onderstaande code in in het script **RemoteRaycaster**en verwijder de dubbele functies:

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

Als volgende stap willen we een geselecteerd object verplaatsen. Voer deze code in in het script **RemoteRaycaster**en verwijder de dubbele functie:

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
> als u deze code uitvoert, zult u merken dat er niets gebeurt. Dat komt omdat het wijzigen van de transformatie van een object om prestatieredenen niet automatisch de statuswijziging naar de server synchroniseert. In plaats daarvan moet u deze statuswijziging handmatig naar de server pushen of u schakelt **SyncEveryFrame** in op het onderdeel *RemoteEntitySyncObject*.

Open het **RemoteModelEntity**-script en voeg deze regel toe:

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

Als u de code opnieuw uitvoert, moet u daarna met de linkermuisknop op een object kunnen klikken en het object rond kunnen slepen.

## <a name="add-a-cut-plane"></a>Een snijvlak toevoegen

De laatste functie die we in deze zelfstudie willen behandelen, is het gebruik van [snijvlakken](../../overview/features/cut-planes.md). Een snijvlak snijdt tegels van weergegeven objecten weg zodat u in de objecten kunt kijken.

Maak een nieuw GameObject aan in de scène **CutPlane**. Maak een nieuw script en noem dit **RemoteCutPlane**. Voeg het onderdeel toe aan het nieuwe GameObject.

Open het scriptbestand en vervang de inhoud door de volgende code:

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

Wanneer u de code uitvoert, zou u moeten zien dat het model door het vlak is opengesneden. U kunt het *CutPlane*-object selecteren en verplaatsen en roteren in het venster *Scène*. U kunt het snijvlak in- en uitschakelen door het snijvlakobject uit te schakelen.

## <a name="next-steps"></a>Volgende stappen

U kent nu de belangrijkste functionaliteit voor interactie met externe objecten. In de volgende zelfstudie besteden we aandacht aan het aanpassen van het uiterlijk van een scène.

> [!div class="nextstepaction"]
> [Zelfstudie: de omgeving en de materialen wijzigen](changing-environment-and-materials.md)
