---
title: Netten
description: Definitie van mazen in het bereik van Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681764"
---
# <a name="meshes"></a>Netten

## <a name="mesh-resource"></a>Mesh-bron

Mazen zijn een onveranderlijke [gedeelde bron,](../concepts/lifetime.md)die alleen kan worden gemaakt door middel van [modelconversie](../how-tos/conversion/model-conversion.md). Mazen bevatten een of meerdere *submeshes*. Elke submesh verwijst naar een [materiaal](materials.md) waarmee het standaard moet worden weergegeven. Als u een net in de 3D-ruimte wilt plaatsen, voegt u een [MeshComponent](#meshcomponent) toe aan een [entiteit](entities.md).

### <a name="mesh-resource-properties"></a>Eigenschappen van mesh-resources

De `Mesh` klasseeigenschappen zijn:

* **Materialen:** Een scala aan materialen. Elk materiaal wordt gebruikt door een ander submesh. Meerdere vermeldingen in de array kunnen naar hetzelfde [materiaal verwijzen](materials.md). Deze gegevens kunnen niet worden gewijzigd tijdens runtime.

* **Grenzen:** Een grenslijnvak met lokale ruimteas (AABB) van de netvertices.

## <a name="meshcomponent"></a>MeshComponent

De `MeshComponent` klasse wordt gebruikt om een instantie van een netbron te plaatsen. Elke MeshComponent verwijst naar één net. Het kan overschrijven welke materialen worden gebruikt om elke submesh te renderen.

### <a name="meshcomponent-properties"></a>Eigenschappen van MeshComponent

* **Gaas:** De mesh-bron die door deze component wordt gebruikt.

* **Materialen:** De array van materialen die zijn opgegeven op de mesh-component zelf. De array heeft altijd dezelfde lengte als de array *Materialen* op de mesh-bron. Materialen die niet worden overschreven vanaf de standaardmesh, zijn ingesteld op *null* in deze array.

* **Gebruikte materialen:** De array van daadwerkelijk gebruikte materialen voor elke submesh. Zal identiek zijn aan de gegevens in de array *Materialen,* voor niet-null-waarden. Anders bevat het de waarde van de array *Materialen* in de netinstantie.

## <a name="next-steps"></a>Volgende stappen

* [Materialen](materials.md)
