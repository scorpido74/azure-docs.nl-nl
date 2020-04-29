---
title: De omgeving en het materiaal wijzigen
description: Zelf studie waarin wordt uitgelegd hoe u het lucht schema en object materialen in een eenheids scène wijzigt.
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679616"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>Zelf studie: de omgeving en het materiaal wijzigen

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Wijzig de omgevings kaart van een scène.
> * Material-para meters wijzigen.
> * Aangepaste bitmappatronen laden.

## <a name="prerequisites"></a>Vereisten

In deze zelf studie wordt ervan uitgegaan dat u bekend bent met de [zelf studie: werken met externe entiteiten in eenheid](working-with-remote-entities.md). U hebt echter alleen een unit-project nodig waarmee u verbinding kunt maken met sessies en een model laden, zoals wordt weer gegeven in [zelf studie: een geheel nieuw eenheids project instellen](project-setup.md).

> [!TIP]
> De [opslag plaats](https://github.com/Azure/azure-remote-rendering) voor de ARR-voor beelden bevat voor bereide eenheids projecten voor alle zelf studies in de map *Unity* , die u als referentie kunt gebruiken.

## <a name="change-the-environment-map"></a>De omgevings kaart wijzigen

De externe rendering van Azure biedt ondersteuning voor het gebruik van [lucht vakken](../../overview/features/sky.md) (ook wel ' environment Maps ' genoemd) om omgevings verlichting te simuleren. Dit is vooral handig wanneer uw objecten gebruikmaken *[van fysieke rendering](../../overview/features/pbr-materials.md)*, zoals in de voorbeeld modellen. ARR wordt ook geleverd met een groot aantal ingebouwde lucht patronen, die we in deze zelf studie zullen gebruiken.

Maak een nieuw script met de naam **RemoteSky** en voeg dit toe aan een nieuwe GameObject. Open het script bestand en vervang dit door de volgende code:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteSky : MonoBehaviour
{
    private int skyIndex = 0;

    private LoadTextureFromSASParams[] builtIns =
    {
        new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)
    };

    public async void ToggleSky()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        skyIndex = (skyIndex + 1) % builtIns.Length;

        var texture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(builtIns[skyIndex]).AsTask();

        Debug.Log($"Switching sky to: {builtIns[skyIndex].TextureId}");

        var settings = RemoteManagerUnity.CurrentSession.Actions.SkyReflectionSettings;

        settings.SkyReflectionTexture = texture;
    }

    private void OnGUI()
    {
        if (RemoteManagerUnity.IsConnected)
        {
            if (GUI.Button(new Rect(10, Screen.height - 50, 175, 30), "Toggle Sky"))
            {
                ToggleSky();
            }
        }
    }
}
```

Houd er rekening `LoadTextureFromSASAsync` mee dat de variant hierboven wordt gebruikt omdat ingebouwde bitmappatronen worden geladen. Gebruik de variant in het `LoadTextureAsync` geval van het laden vanuit [gekoppelde Blob-opslag](../../how-tos/create-an-account.md#link-storage-accounts). Een voor beeld van hoe dit werkt voor modellen, vindt u in de [sectie Model laden](../../concepts/models.md#loading-models).

Wanneer u de code uitvoert en de plattegronden in de lucht inschakelt, ziet u een aanzienlijk andere belichting in uw model. De achtergrond blijft echter zwart en u kunt het echte lucht patroon niet zien. Dit is opzettelijk, omdat het weer geven van een achtergrond zou leiden tot een uitbrei ding van een reality-apparaat. In de juiste toepassing moet u lucht patronen gebruiken die vergelijkbaar zijn met uw echte wereld omgeving, omdat dit ervoor zorgt dat objecten realistischer worden weer gegeven.

## <a name="modify-materials"></a>Materialen wijzigen

In de vorige zelf studie hebben we de [status override-onderdelen](../../overview/features/override-hierarchical-state.md) gebruikt om de tint kleur van geselecteerde objecten te wijzigen. Nu willen we een vergelijkbaar effect hebben, maar dat doen door het [materiaal](../../concepts/materials.md) van een object te wijzigen.

Eerst hebt u een script nodig om objecten te selecteren, zoals in [de tweede zelf studie](working-with-remote-entities.md). Als u nog geen **RemoteRaycaster** -script hebt, moet u dit nu maken. Vervang de inhoud door de volgende code:

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
}
```

Voeg het onderdeel toe aan uw *RemoteRendering* Game-object. Het is verantwoordelijk voor het verzamelen van objecten onder de muis en het toevoegen van *RemoteModelEntity* -onderdelen aan de verzamelde objecten. Deze component klasse implementeert de werkelijke materiaal wijzigings functionaliteit.

Als u nog geen **RemoteModelEntity** -script hebt, moet u dit maken en de inhoud vervangen door deze code:

```csharp
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

// to prevent namespace conflicts
using ARRTexture = Microsoft.Azure.RemoteRendering.Texture;

public class RemoteModelEntity : MonoBehaviour
{
    private Color HighlightColor = new Color(1.0f, 0.4f, 0.1f, 1.0f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;
    private RemoteEntitySyncObject localSyncObject = null;
    private ARRMeshComponent meshComponent = null;
    private Color4 originalColor;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();

        meshComponent = GetComponent<ARRMeshComponent>();
        if (meshComponent == null)
        {
            var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
            if (mesh != null)
            {
                gameObject.BindArrComponent<ARRMeshComponent>(mesh);
                meshComponent = gameObject.GetComponent<ARRMeshComponent>();
            }
        }
        meshComponent.enabled = true;
    }

    public void SetFocus(bool on)
    {
        SetMaterialColor(on);
    }

    private void SetMaterialColor(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        // get the list of materials from the picked mesh
        // we only modify the first material; depending on how the model is authored, this may
        // work exactly as desired, or not ;-)
        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        // there are different types of materials, cast as necessary
        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
    }
}
```

Wanneer u deze code uitvoert, krijgen de objecten waarop u de muis aanwijzer hebt gemarkeerd de markering. Het resultaat is vergelijkbaar met wat we in de zelf studie 2 hebben gedaan, maar de manier waarop deze wordt behaald, is anders. Hier ziet u de lijst met materialen op het gepickte object en wijzigt u vervolgens de eerste in een andere albedo kleur.

> [!IMPORTANT]
> Houd er rekening mee dat deze methode de juiste onderdelen van een model markeert, afhankelijk van de manier waarop een model is gemaakt. Het werkt perfect als elk object precies één materiaal gebruikt. Als het model echter geen 1:1-relatie heeft tussen onderdelen en materialen, zal de bovenstaande Naïve-code niet het juiste effect hebben.

## <a name="use-a-different-texture"></a>Een ander patroon gebruiken

[Bitmappatronen](../../concepts/textures.md) maken meestal deel uit van een bron model. Tijdens de [model conversie](../../quickstarts/convert-model.md)worden alle structuren geconverteerd naar de benodigde runtime-indeling en verpakt in het definitieve model bestand. Als u een structuur tijdens runtime wilt vervangen, moet u deze opslaan in de [DDS-bestands indeling](https://en.wikipedia.org/wiki/DirectDraw_Surface) en uploaden naar Azure Blob-opslag. Raadpleeg [deze Snelstartgids](../../quickstarts/convert-model.md) voor het maken van een Azure Blob-container. Zodra u een BLOB-container hebt, kunt u deze openen in Azure Storage Explorer en het bestand uploaden via slepen en neerzetten.

Aan de kant van de runtime kunt u op twee verschillende manieren een structuur element in Blob Storage adresseren:

* De structuur van het adres van de SAS-URI. Klik hiervoor met de rechter muisknop op het geüploade bestand en selecteer**ophalen Shared Access Signature...** in het context menu. Gebruik deze SAS-URI met `LoadTextureFromSASAsync` de functie variant (zie voorbeeld code hieronder).
* Adresseer de textuur rechtstreeks aan de Blob Storage-para meters voor het geval de [Blob-opslag is gekoppeld aan het account](../../how-tos/create-an-account.md#link-storage-accounts). In dit geval moet `LoadTextureAsync`u een relevante laad functie gebruiken.

Open nu het script **RemoteModelEntity** , voeg de volgende code toe en verwijder dubbele functies:

```csharp
    private string textureFile = ""; //<SAS URI for your texture>
    private ARRTexture originalTexture = null;

    private async void SetMaterialTexture(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        ARRTexture newTexture = originalTexture;

        if (on)
        {
            var textureParams = new LoadTextureFromSASParams(textureFile, TextureType.Texture2D);

            newTexture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(textureParams).AsTask();
        }

        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
    }

    public void SetFocus(bool on)
    {
        if (string.IsNullOrEmpty(textureFile))
        {
            SetMaterialColor(on);
        }
        else
        {
            SetMaterialTexture(on);
        }
    }
```

Voer deze code uit en beweeg de muis aanwijzer over uw model. Als uw model juiste UV-coördinaten heeft, ziet u dat uw structuur wordt weer gegeven. Anders is het mogelijk dat u alleen een kleur wijziging ziet.

## <a name="next-steps"></a>Volgende stappen

Op die manier wordt de inleidende serie beëindigd over het gebruik van de externe rendering van Azure met eenheid. Als volgende stap moet u een aantal basis concepten van ARR, zoals [sessies](../../concepts/sessions.md), [entiteiten](../../concepts/entities.md)en [modellen](../../concepts/models.md) , leren kennen om meer inzicht te krijgen. Er zijn ook verschillende functies, zoals [lampen](../../overview/features/lights.md), [overzichts rendering](../../overview/features/outlines.md), [hiërarchische status onderdrukkingen](../../overview/features/override-hierarchical-state.md)en [materialen](../../concepts/materials.md) die u uitvoeriger kunt verkennen.

> [!div class="nextstepaction"]
> [Unit-Game objecten en-onderdelen](../../how-tos/unity/objects-components.md)
