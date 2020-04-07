---
title: Het milieu en de materialen veranderen
description: Zelfstudie die laat zien hoe u de hemelkaart en objectmaterialen in een Unity-scène wijzigen.
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679616"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>Zelfstudie: De omgeving en materialen wijzigen

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * De omgevingskaart van een scène wijzigen.
> * Materiaalparameters wijzigen.
> * Aangepaste texturen laden.

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat u bekend bent met [Zelfstudie: Werken met externe entiteiten in Unity.](working-with-remote-entities.md) U hebt echter alleen een Unity-project nodig waarmee u verbinding maken met sessies en een model laden, zoals wordt weergegeven in [Zelfstudie: Het opzetten van een Unity-project vanaf nul.](project-setup.md)

> [!TIP]
> De [ARR-samples repository](https://github.com/Azure/azure-remote-rendering) bevat voorbereide Unity-projecten voor alle tutorials in de *Unity-map,* die u als referentie gebruiken.

## <a name="change-the-environment-map"></a>De omgevingskaart wijzigen

Azure Remote Rendering ondersteunt het gebruik van [sky boxes](../../overview/features/sky.md) (ook wel 'omgevingskaarten' genoemd) om sfeerverlichting te simuleren. Dit is vooral handig wanneer uw objecten *[fysiek gebaseerde rendering](../../overview/features/pbr-materials.md)* gebruiken, zoals onze voorbeeldmodellen doen. ARR wordt ook geleverd met een verscheidenheid aan ingebouwde luchttexturen, die we in deze tutorial zullen gebruiken.

Maak een nieuw script genaamd **RemoteSky** en voeg het toe aan een nieuw GameObject. Open het scriptbestand en vervang het door de volgende code:

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

Houd er `LoadTextureFromSASAsync` rekening mee dat de variant hierboven wordt gebruikt omdat ingebouwde texturen zijn geladen. In het geval van laden vanuit `LoadTextureAsync` [gekoppelde blob-opslag,](../../how-tos/create-an-account.md#link-storage-accounts)gebruikt u de variant. Een voorbeeld voor hoe dit werkt voor modellen is te vinden in [model laden sectie](../../concepts/models.md#loading-models).

Wanneer u de code uitvoert en door de hemelkaarten schakelt, zult u drastisch verschillende verlichting op uw model opmerken. De achtergrond blijft echter zwart en u de werkelijke luchttextuur niet zien. Dit is opzettelijk, omdat het renderen van een achtergrond storend zou zijn met een Augmented Reality-apparaat. In een goede toepassing, moet u gebruik maken van sky texturen die vergelijkbaar zijn met je echte wereld omgeving, want dit zal helpen om objecten lijken meer echte.

## <a name="modify-materials"></a>Materialen wijzigen

In de vorige zelfstudie hebben we [onderdelen voor het overschrijven](../../overview/features/override-hierarchical-state.md) van de status gebruikt om de tintkleur van geselecteerde objecten te wijzigen. Nu willen we een vergelijkbaar effect bereiken, maar doen dit door het [materiaal](../../concepts/materials.md) van een object aan te passen.

Eerst hebben we een script nodig om objecten te kiezen, zoals we deden in [de tweede tutorial.](working-with-remote-entities.md) Als u nog geen **RemoteRaycaster-script** hebt, maakt u het nu. Vervang de inhoud ervan door de volgende code:

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

Voeg de component toe aan het *object RemoteRendering.* Het is verantwoordelijk voor het kiezen van objecten onder de muis en het toevoegen *van RemoteModelEntity* componenten aan de geplukte objecten. Die componentklasse is waar we de werkelijke material change functionaliteit implementeren.

Als u nog geen **RemoteModelEntity-script** hebt, maakt u deze en vervangt u de inhoud ervan door deze code:

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

Wanneer u deze code uitvoert, worden objecten waarvan u met de muis zweeft gemarkeerd. Het effect is vergelijkbaar met wat we deden in tutorial 2, maar de manier waarop het wordt bereikt is anders. Hier krijgen we de lijst van materialen op het gekozen object en vervolgens wijzigen de eerste om een andere albedo kleur hebben.

> [!IMPORTANT]
> Houd er rekening mee dat of deze methode de juiste onderdelen van een model markeert, afhankelijk is van hoe een model wordt geschreven. Het zal perfect werken, als elk object precies één materiaal gebruikt. Echter, als het model niet beschikt over een 1:1 relatie tussen onderdelen en materialen, de naïeve code hierboven zal niet het juiste doen.

## <a name="use-a-different-texture"></a>Een andere structuur gebruiken

[Texturen](../../concepts/textures.md) maken doorgaans deel uit van een bronmodel. Tijdens [modelconversie](../../quickstarts/convert-model.md)worden alle texturen geconverteerd naar de benodigde runtime-indeling en verpakt in het uiteindelijke modelbestand. Als u een structuur wilt vervangen tijdens runtime, moet u deze opslaan in [de DDS-bestandsindeling](https://en.wikipedia.org/wiki/DirectDraw_Surface) en deze uploaden naar Azure blob-opslag. Raadpleeg [deze quickstart-handleiding](../../quickstarts/convert-model.md) voor het maken van een Azure blob-container. Zodra u een blobcontainer hebt, u deze openen in Azure Storage Explorer en uw bestand uploaden via slepen en neerzetten.

Aan de runtime-kant u een structuuritem in blobopslag op twee verschillende manieren aanpakken:

* Adrestextuur door de SAS URI. Klik daarvoor met de rechtermuisknop op het geüploade bestand en selecteer "**Gedeelde toegangshandtekening ophalen...**" in het contextmenu. Gebruik deze SAS `LoadTextureFromSASAsync` URI met de functievariant (zie voorbeeldcode hieronder).
* Pak de structuur rechtstreeks aan op blobopslagparameters, voor het geval de [blobopslag is gekoppeld aan het account](../../how-tos/create-an-account.md#link-storage-accounts). Relevante laadfunctie in `LoadTextureAsync`dit geval is .

Open nu het script **RemoteModelEntity,** voeg de volgende code toe en verwijder dubbele functies:

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

Voer deze code uit en zweef over uw model. Als uw model de juiste UV-coördinaten heeft, moet u uw textuur zien verschijnen. Anders ziet u mogelijk alleen een kleurverandering.

## <a name="next-steps"></a>Volgende stappen

Hiermee wordt onze introductiereeks over het gebruik van Azure Remote Rendering with Unity afgesloten. Als volgende stap moet je jezelf vertrouwd te maken een aantal fundamentele concepten van ARR, zoals [sessies,](../../concepts/sessions.md) [entiteiten,](../../concepts/entities.md)en [modellen](../../concepts/models.md) om een dieper begrip op te bouwen. Er zijn ook verschillende functies zoals [lichten,](../../overview/features/lights.md) [overzichtrendering,](../../overview/features/outlines.md) [hiërarchische statusoverschrijvingen](../../overview/features/override-hierarchical-state.md)en [materialen](../../concepts/materials.md) die u in meer detail moet verkennen.

> [!div class="nextstepaction"]
> [Unity game objecten en componenten](../../how-tos/unity/objects-components.md)
