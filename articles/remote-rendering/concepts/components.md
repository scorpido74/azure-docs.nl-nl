---
title: Onderdelen
description: Definitie van onderdelen in het bereik van Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681894"
---
# <a name="components"></a>Onderdelen

Azure Remote Rendering maakt gebruik van het patroon [van het entiteitscomponentsysteem.](https://en.wikipedia.org/wiki/Entity_component_system) Terwijl [entiteiten](entities.md) de positie en de hiërarchische samenstelling van objecten vertegenwoordigen, zijn componenten verantwoordelijk voor het implementeren van gedrag.

De meest gebruikte soorten componenten zijn [mesh-componenten,](meshes.md)die mazen toevoegen aan de renderingpipeline. Op dezelfde manier worden [lichtcomponenten](../overview/features/lights.md) gebruikt om verlichting toe te voegen en [gesneden vlakcomponenten](../overview/features/cut-planes.md) worden gebruikt om open mazen te snijden.

Al deze componenten gebruiken de transformatie (positie, rotatie, schaal) van de entiteit waaraan ze zijn gekoppeld, als referentiepunt.

## <a name="working-with-components"></a>Werken met componenten

U onderdelen eenvoudig programmatisch toevoegen, verwijderen en manipuleren:

```cs
// create a point light component
AzureSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Actions.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

Een component wordt op het moment van creatie aan een entiteit gekoppeld. Het kan niet achteraf naar een andere entiteit worden verplaatst. Onderdelen worden expliciet verwijderd `Component.Destroy()` met of automatisch wanneer de eigenaar entiteit van de component wordt vernietigd.

Er mag slechts één exemplaar van elk componenttype tegelijk aan een entiteit worden toegevoegd.

## <a name="unity-specific"></a>Eenheidsspecifiek

De Unity-integratie heeft extra uitbreidingsfuncties voor interactie met componenten. Zie [Unity game objecten en componenten](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>Volgende stappen

* [Objectgrenzen](object-bounds.md)
* [Netten](meshes.md)
