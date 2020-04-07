---
title: Ruimtelijke query's
description: Ruimtelijke query's in een scène maken
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680529"
---
# <a name="spatial-queries"></a>Ruimtelijke query's

Ruimtelijke query's zijn bewerkingen waarmee u de externe renderingservice vragen welke objecten zich in een gebied bevinden. Ruimtelijke query's worden vaak gebruikt om interacties te implementeren, zoals het uitzoeken naar welk object een gebruiker wijst.

Alle ruimtelijke query's worden geëvalueerd op de server. Het zijn dus asynchrone bewerkingen en de resultaten komen met een vertraging die afhankelijk is van uw netwerklatentie. Aangezien elke ruimtelijke query netwerkverkeer genereert, moet u ervoor zorgen dat u er niet te veel tegelijk doet.

## <a name="collision-meshes"></a>De mazen van de botsing

Ruimtelijke query's worden aangedreven door de [Havok Physics-engine](https://www.havok.com/products/havok-physics) en vereisen een speciale botsing mesh aanwezig te zijn. Modelconversie [genereert](../../how-tos/conversion/model-conversion.md) standaard botsingsmazen. Als u geen ruimtelijke query's nodig hebt voor een complex model, u overwegen de generatie van botsingsgaas in de [conversieopties](../../how-tos/conversion/configure-model-conversion.md)uit te schakelen, omdat dit op meerdere manieren een impact heeft:

* [Modelconversie](../../how-tos/conversion/model-conversion.md) zal aanzienlijk langer duren.
* Geconverteerde modelbestandsformaten zijn merkbaar groter, wat van invloed is op de downloadsnelheid.
* De laadtijden zijn langer.
* Runtime CPU-geheugen verbruik is hoger.
* Er is een lichte runtime prestaties overhead voor elk model instantie.

## <a name="ray-casts"></a>Ray werpt

Een *ray cast* is een ruimtelijke query waarbij de runtime controleert welke objecten worden doorsneden door een straal, te beginnen op een bepaalde positie en in een bepaalde richting te wijzen. Als optimalisatie wordt ook een maximale straalafstand gegeven, om niet te zoeken naar objecten die te ver weg zijn.

````c#
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitEntity;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
````

Er zijn drie populaire verzamelingsmodi:

* **Dichtstbijzijnde:** In deze modus wordt alleen de dichtstbijzijnde treffer gerapporteerd.
* **Alle:** Geef de voorkeur aan deze modus wanneer alles wat je wilt weten is *of* een straal iets zou raken, maar het kan me niet schelen wat er precies werd geraakt. Deze query kan aanzienlijk goedkoper zijn om te evalueren, maar heeft ook slechts enkele toepassingen.
* **Alle:** In deze modus worden alle hits langs de straal gerapporteerd, gesorteerd op afstand. Gebruik deze modus niet, tenzij je echt meer nodig hebt dan de eerste hit. Beperk het aantal gerapporteerde hits met de `MaxHits` optie.

Om uit te sluiten dat objecten selectief worden overwogen voor straalcasts, kan de component [HierarchicalStateOverrideComponent](override-hierarchical-state.md) worden gebruikt.

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Resultaat hit

Het resultaat van een ray cast query is een array van hits. De array is leeg als er geen object is geraakt.

Een treffer heeft de volgende eigenschappen:

* **HitEntity:** Welke [entiteit](../../concepts/entities.md) werd geraakt.
* **SubPartId:** Welke *submesh* is geraakt in een [MeshComponent.](../../concepts/meshes.md) Kan worden gebruikt `MeshComponent.UsedMaterials` om te indexeren in en opzoeken van het [materiaal](../../concepts/materials.md) op dat punt.
* **HitPosition:** De wereldruimtepositie waar de straal het object kruiste.
* **HitNormal:** Het wereldoppervlak normaal van het gaas op de positie van de kruising.
* **Afstandtohit:** De afstand van de ray startpositie tot de treffer.

## <a name="next-steps"></a>Volgende stappen

* [Objectgrenzen](../../concepts/object-bounds.md)
* [Hiërarchische toestanden overschrijven](override-hierarchical-state.md)
