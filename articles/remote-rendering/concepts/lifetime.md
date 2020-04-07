---
title: Object en resourcelevensduur
description: Legt lifetime management voor verschillende types uit
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681868"
---
# <a name="object-and-resource-lifetime"></a>Object en resourcelevensduur

Azure Remote Rendering maakt onderscheid tussen twee typen: **objecten** en **resources**.

## <a name="object-lifetime"></a>Objectlevensduur

*Objecten* worden beschouwd als dingen die de gebruiker kan maken, wijzigen en vernietigen naar eigen inzicht. Objecten kunnen vrij worden gedupliceerd en elke instantie kan na verloop van tijd muteren. Bijgevolg zijn [entiteiten](entities.md) en [componenten](components.md) objecten.

De levensduur van objecten staat volledig onder controle van de gebruiker. Het is niet gerelateerd aan de levensduur van de client-side vertegenwoordiging, dat wel. Klassen vinden `Entity` `Component` leuk `Destroy` en hebben een functie die moet worden aangeroepen om het object op de externe renderinghost af te wijzen. Bovendien `Entity.Destroy()` vernietigt u de entiteit, haar kinderen en alle componenten in die hiërarchie.

## <a name="resource-lifetime"></a>Levensduur van resources

*Resources* zijn dingen waarvan de levensduur volledig wordt beheerd door de externe rendering host. Resources worden intern geteld. Ze krijgen deallocated wanneer niemand verwijst ze niet meer.

De meeste bronnen kunnen alleen indirect worden gemaakt, meestal door ze uit een bestand te laden. Wanneer hetzelfde bestand meerdere keren wordt geladen, retourneert Azure Remote Rendering dezelfde verwijzing en laadt het de gegevens niet opnieuw.

Veel bronnen zijn onveranderlijk, bijvoorbeeld [mazen](meshes.md) en [texturen.](textures.md) Sommige bronnen zijn veranderlijk, hoewel, bijvoorbeeld [materialen](materials.md). Aangezien resources vaak worden gedeeld, kan het wijzigen van een resource van invloed zijn op meerdere objecten. Als u bijvoorbeeld de kleur van een materiaal wijzigt, verandert de kleur van alle objecten die mazen gebruiken, die op hun beurt verwijzen naar dat materiaal.

### <a name="built-in-resources"></a>Ingebouwde resources

Azure Remote Rendering bevat een aantal ingebouwde bronnen, die kunnen `builtin://` worden geladen `AzureSession.Actions.LoadXYZAsync()`door hun respectievelijke id vooraf af te leggen aan tijdens de aanroep naar . De beschikbare ingebouwde bronnen worden vermeld in de documentatie voor elke respectievelijke functie. In het [hoofdstuk hemel](../overview/features/sky.md) worden bijvoorbeeld de ingebouwde hemeltexturen weergegeven.

## <a name="general-lifetime"></a>Algemene levensduur

De levensduur van alle objecten en resources is gekoppeld aan de verbinding. Bij het loskoppelen wordt alles weggegooid. Wanneer u opnieuw verbinding maakt met dezelfde sessie, is de scènegrafiek leeg en worden alle resources verwijderd.

In de praktijk is het laden van dezelfde resource in een sessie, na een ontkoppeling, meestal sneller dan de eerste keer. Dit is het geval omdat de meeste resources de eerste keer moeten worden gedownload van Azure Storage, wat de tweede keer niet nodig is, wat aanzienlijke tijd bespaart.

## <a name="next-steps"></a>Volgende stappen

* [Entiteiten](entities.md)
* [Onderdelen](components.md)
* [Modellen](models.md)
