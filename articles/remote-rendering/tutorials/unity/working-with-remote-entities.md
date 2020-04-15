---
title: Werken met externe entiteiten in Unity
description: Zelfstudie die laat zien hoe je met ARR-entiteiten werkt.
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: db1f6a53121e05b29f7e3441af027985a141bc2e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310210"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>Zelfstudie: Werken met externe entiteiten in Unity

[Zelfstudie: Het instellen van een Unity-project vanaf nul](project-setup.md) liet zien hoe u een nieuw Unity-project configureert om te werken met Azure Remote Rendering. In deze zelfstudie bekijken we de meest voorkomende functionaliteit die elke ARR-gebruiker nodig heeft.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Kies objecten met behulp van ray casts.
> * Objectstatussen overschrijven, zoals tintkleur, selectiestatus en zichtbaarheid.
> * Externe entiteiten verwijderen.
> * Externe entiteiten verplaatsen.
> * Gebruik gesneden vlakken om in objecten te kijken.

## <a name="prerequisites"></a>Vereisten

* Deze zelfstudie bouwt voort op [tutorial: Het opzetten van een Unity-project vanaf nul.](project-setup.md)

> [!TIP]
> De [ARR-samples repository](https://github.com/Azure/azure-remote-rendering) bevat voorbereide Unity-projecten voor alle tutorials in de *Unity-map,* die u als referentie gebruiken.

## <a name="pick-objects"></a>Objecten kiezen

We willen communiceren met objecten, dus het eerste wat we nodig hebben, is het kiezen van objecten onder de muiscursor.

Maak een [nieuw script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) genaamd **RemoteRaycaster** en vervang de volledige inhoud door de onderstaande code:

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

Voeg deze component toe aan het object *RemoteRendering* in uw scène.

> [!WARNING]
>
> De *component RemoteRaycaster* vereist dat een *ARRServiceUnity-component* aan hetzelfde object wordt bevestigd. *ARRServiceUnity* is een helperklasse om gemakkelijker toegang te krijgen tot bepaalde ARR-functionaliteit. Er kan echter slechts één exemplaar van dit onderdeel in de scène zijn. Zorg er daarom voor dat u alle onderdelen die *ARRServiceUnity* vereisen, toevoegt aan hetzelfde GameObject.
> Als u toegang wilt krijgen tot ARR-functionaliteit van meerdere gameobjecten, voegt u de component *ARRServiceUnity* alleen toe aan een van deze objecten en verwijst u ernaar in de andere scripts of hebt u rechtstreeks toegang tot de ARR-functionaliteit.

Druk op afspelen, maak verbinding met een sessie en laad een model. Richt nu objecten in de scène en bekijk de console-uitvoer. Het moet de objectnaam van elk onderdeel waarop u zweeft afdrukken.

## <a name="highlight-objects"></a>Objecten markeren

Als volgende stap willen we visuele feedback geven, welke delen van een model de gebruiker aanwijst. Om dit te bereiken, koppelen we een [HiërarchischeOverrideOverrideComponent](../../overview/features/override-hierarchical-state.md) aan de entiteit die we hebben gekozen. Deze component kan worden gebruikt om verschillende functies op een object in of uit te schakelen. Hier gebruiken we het om een tintkleur in te stellen en [overzichtsweergave](../../overview/features/outlines.md)in te schakelen.

Maak een ander scriptbestand genaamd **RemoteModelEntity** en vervang de inhoud ervan door de volgende code:

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
> Wijs dit script niet toe aan een spelobject, omdat het programmatisch wordt toegewezen door de onderstaande code.

Vervolgens moeten we onze *RemoteRaycaster* uitbreiden om de *RemoteModelEntity-component* toe te voegen aan het object dat we net hebben gekozen.

Voeg de volgende code toe aan de **RemoteRaycaster-implementatie** en verwijder de dubbele functies:

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

Voer uw project uit en richt op een model, u zou moeten zien dat het een rode tint en een wit selectieoverzicht krijgt.

## <a name="isolate-the-selected-object"></a>Het geselecteerde object isoleren

Een ander gebruik van de [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) is de mogelijkheid om zichtbaarheid te overschrijven. Hiermee u een geselecteerd object isoleren van de rest van het model. Open het script **RemoteModelEntity,** voeg de volgende code toe en verwijder de dubbele functies:

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

Deze code is afhankelijk van het hebben van een component statusoverschrijven aan het bovenste object in de hiërarchie, waardoor alle objecten onzichtbaar zijn. Vervolgens wordt de zichtbaarheid bij het geselecteerde object opnieuw overschreven om dat ene object zichtbaar te maken. Daarom moeten we een component voor statusoverschrijving maken bij het hoofdobject.

Open het script **RemoteRendering** en voeg de onderstaande code boven aan de functie *LoadModel* in:

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

Tot slot hebben we een manier nodig om de zichtbaarheid te schakelen. Open het **RemoteRaycaster-script** en vervang de functie *Bijwerken:*

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

Voer de code uit en klik met de rechtermuisknop op een deel van het model. De rest van het model verdwijnt en alleen het gemarkeerde stuk blijft zichtbaar.

## <a name="remove-gameobject-instances-of-remote-entities"></a>GameObject-exemplaren van externe entiteiten verwijderen

Het is je misschien opgevallen dat de code objecten blijft maken, maar nooit opruimt. Dit is ook zichtbaar in het deelvenster objecthiërarchie. Wanneer u de externe objecthiërarchie uitvouwt tijdens de simulatie, u steeds meer objecten zien verschijnen wanneer u over een nieuw deel van het model zweeft.

Het hebben van veel objecten in een scène heeft een negatieve invloed op de prestaties. Je moet altijd opruimen van objecten die niet meer nodig zijn.

Plaats de onderstaande code in het **RemoteRaycaster-script** en verwijder de dubbele functies:

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

Als volgende stap willen we een geselecteerd object verplaatsen. Voeg in het **RemoteRaycaster-script** deze code in en verwijder de dubbele functie:

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
> Als u deze code uitvoert, zult u merken dat er niets gebeurt. Dat komt omdat het wijzigen van de transformatie van een object de statuswijziging niet automatisch synchroniseert met de server, om prestatieredenen. In plaats daarvan moet u deze statuswijziging handmatig naar de server pushen of **SyncEveryFrame** inschakelen op de component *RemoteEntitySyncObject.*

Open het script **RemoteModelEntity** en voeg deze regel toe:

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

Als u de code opnieuw uitvoert, moet u links op een object kunnen klikken en het kunnen rondslepen.

## <a name="add-a-cut-plane"></a>Een gesneden vlak toevoegen

De laatste functie die we willen uitproberen in deze tutorial, is met behulp van [gesneden vliegtuigen](../../overview/features/cut-planes.md). Een gesneden vlak snijdt delen van gerenderde objecten weg, zodat je erin kijken.

Maak een nieuw GameObject in de scène **CutPlane**. Maak een nieuw script en noem het **RemoteCutPlane**. Voeg de component toe aan het nieuwe GameObject.

Open het scriptbestand en vervang de inhoud ervan door de volgende code:

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

Wanneer u uw code nu uitvoert, moet u zien hoe het model wordt opengesneden door het vliegtuig. U het object *CutPlane* selecteren en verplaatsen en draaien in het *scènevenster.* U het afgesneden vlak in- en uitschakelen door het object cut plane uit te schakelen.

## <a name="next-steps"></a>Volgende stappen

U kent nu de belangrijkste functionaliteit voor interactie met externe objecten. In de volgende tutorial zullen we een kijkje nemen bij het aanpassen van de look van een scène.

> [!div class="nextstepaction"]
> [Zelfstudie: De omgeving en materialen wijzigen](changing-environment-and-materials.md)
