---
title: Camera
description: Beschrijft camera-instellingen en use cases
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: 5ad39ad9f5e585029cff5d573a026702f259607e
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564847"
---
# <a name="camera"></a>Camera

Om ervoor te zorgen dat lokaal en extern gerenderde inhoud naadloos kunnen worden samengesteld, moeten verschillende camera-eigenschappen synchroon blijven tussen de server en de client. Met name de camera transformatie en projectie. De lokaal gerenderde inhoud moet bijvoorbeeld dezelfde camera transformatie en projectie gebruiken waarmee het meest recente externe frame is gerenderd.

![Lokale en externe camera](./media/camera.png)

In de bovenstaande afbeelding is de lokale camera verplaatst, vergeleken met het externe frame dat door de server is verzonden. Als gevolg hiervan moet de lokale inhoud worden gerenderd op basis van hetzelfde perspectief als het externe frame en ten slotte wordt de resulterende afbeelding opnieuw geprojecteerd naar de ruimte van de lokale camera, die in de [laatste fase van de reprojectie](late-stage-reprojection.md)wordt uitgelegd.

De vertraging tussen de externe en de lokale rendering betekent dat elke wijziging van deze instellingen wordt toegepast met een vertraging. Daarom kan een nieuwe waarde niet direct op hetzelfde frame worden gebruikt.

De bijna-en Far-vlak afstanden kunnen worden ingesteld op de camera-instellingen. Op de HoloLens 2 worden de resterende trans formatie-en projectie gegevens gedefinieerd door de hardware. Wanneer u de [bureaublad simulatie](../../concepts/graphics-bindings.md)gebruikt, worden deze ingesteld via de invoer van de `Update` functie.

De gewijzigde waarden kunnen lokaal worden gebruikt zodra het eerste externe frame arriveert dat ermee is gerenderd. Op HoloLens 2 worden de gegevens die moeten worden gebruikt voor rendering gegeven door de *HolographicFrame* net zoals in andere Holographic-toepassingen. Bij [bureaublad simulatie](../../concepts/graphics-bindings.md)worden ze opgehaald via de uitvoer van de `Update` functie.

## <a name="camera-settings"></a>Camera-instellingen

De volgende eigenschappen kunnen worden gewijzigd in de camera-instellingen:

**Bijna en Far vlak:**

Om ervoor te zorgen dat er geen ongeldige bereiken kunnen worden ingesteld, zijn de eigenschappen **NearPlane** en **FarPlane** alleen-lezen en is er een afzonderlijke functie **SetNearAndFarPlane** aanwezig om het bereik te wijzigen. Deze gegevens worden aan het einde van het frame naar de server verzonden.

> [!IMPORTANT]
> In unit-eenheid wordt dit automatisch verwerkt bij het wijzigen van de hoofd camera in de buurt.

**EnableDepth**:

Soms is het handig om de uitgebreide buffer schrijf bewerking van de externe installatie kopie uit te scha kelen voor fout opsporing. Als u diepte uitschakelt, kan de server ook geen diepte gegevens verzenden, waardoor de band breedte wordt verminderd.

> [!TIP]
> In unit eenheid wordt een debug-onderdeel met de naam **EnableDepthComponent** weer gegeven dat kan worden gebruikt om deze functie te scha kelen in de gebruikers interface van de editor.

Het wijzigen van de camera-instellingen kan als volgt worden uitgevoerd:

```cs
void ChangeCameraSetting(AzureSession session)
{
    CameraSettings settings = session.Actions.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<CameraSettings> settings = session->Actions()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
}
```

## <a name="api-documentation"></a>API-documentatie

* [C#-CameraSettings](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++ CameraSettings](https://docs.microsoft.com/cpp/api/remote-rendering/camerasettings)
* [C# GraphicsBindingSimD3d11. update-functie](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GraphicsBindingSimD3d11:: functie update](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Volgende stappen

* [Afbeeldings binding](../../concepts/graphics-bindings.md)
* [Vertraagde fase van het project](late-stage-reprojection.md)
