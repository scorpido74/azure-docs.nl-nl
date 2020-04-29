---
title: Info
description: Inleiding tot de externe rendering van Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: overview
ms.openlocfilehash: a06c63152cb56be6d94cccc472d2e1d65651d6ce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679952"
---
# <a name="about-azure-remote-rendering"></a>Over Azure Remote Rendering

> [!IMPORTANT]
> De **externe rendering van Azure** is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Azure remote rendering* (arr) is een service waarmee u kwalitatief hoogwaardige, interactieve 3D-inhoud in de cloud kunt weer geven en deze in realtime kunt streamen naar apparaten, zoals HoloLens 2.

![Voorbeeld model](../media/arr-engine.png)

Apparaten die niet zijn gebonden, hebben een beperkte reken kracht voor het weer geven van complexe modellen. Voor veel toepassingen zou het echter onaanvaardbaar zijn, maar om de visuele betrouw baarheid op een wille keurige manier te verminderen.

*Externe rendering* lost dit probleem op door de werk belasting voor rendering te verplaatsen naar high-end gpu's in de Cloud. Met een in de Cloud gehoste grafische engine wordt de installatie kopie weer gegeven, gecodeerd als een video stroom en stromen die naar het doel apparaat worden verzonden.

## <a name="hybrid-rendering"></a>Hybride Rendering

In de meeste toepassingen is het niet voldoende om alleen een complex model weer te geven. U hebt ook aangepaste gebruikers interface nodig om functionaliteit aan de gebruiker toe te voegen. Met Azure remote rendering wordt u niet gedwongen een speciaal UI-Framework te gebruiken. in plaats daarvan wordt *hybride rendering*ondersteund. Dit betekent dat u elementen op het apparaat kunt weer geven met behulp van uw voorkeurs methode, zoals [MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/GettingStartedWithTheMRTK.html).

Aan het einde van een frame combineert Azure remote rendering automatisch uw lokaal gerenderde inhoud met de externe installatie kopie. Dit kan zelfs met de juiste bedekking.

## <a name="multi-gpu-rendering"></a>Multi-GPU weer geven

Sommige modellen zijn te complex om te worden weer gegeven met interactieve frame snelheden, zelfs voor een geavanceerde GPU. Met name in de industriële visualisatie is dit een veelvoorkomend probleem. Als u de limieten verder wilt pushen, kan de werk belasting door Azure remote rendering naar meerdere Gpu's worden gedistribueerd. De resultaten worden samengevoegd in één installatie kopie, waardoor het proces volledig transparant is voor de gebruiker.

## <a name="high-level-architecture"></a>Architectuur op hoog niveau

Dit diagram illustreert de architectuur voor externe Rendering:

![Architectuur](./media/arr-high-level-architecture.png)

Een volledige cyclus voor het genereren van installatie kopieën omvat de volgende stappen:

1. Aan client zijde: frame-instelling
    1. Uw code: de gebruikers invoer wordt verwerkt, de scène grafiek wordt bijgewerkt
    1. ARR-code: scène grafiek updates en voorspelde Head pose worden verzonden naar de server
1. Server zijde: externe Rendering
    1. Rendering-Engine distribueert rendering over beschik bare Gpu's
    1. De uitvoer van meerdere Gpu's wordt samengesteld in één afbeelding
    1. Afbeelding is gecodeerd als video stroom, terug naar de client verzonden
1. Client zijde: volt ooien
    1. Uw code: optionele lokale inhoud (UI, markers,...) wordt weer gegeven
    1. ARR-code: bij aanwezig, lokaal gerenderde inhoud wordt automatisch samengevoegd met video stroom

Netwerk latentie is het belangrijkste probleem. De tijd tussen het verzenden van een aanvraag en het ontvangen van het resultaat is doorgaans te lang voor interactieve frame snelheden. Daarom kan meer dan één frame op elk gewenst moment in de vlucht zijn.

## <a name="next-steps"></a>Volgende stappen

* [Systeemvereisten](system-requirements.md)
* [Quick Start: een model met Unit renderen](../quickstarts/render-model.md)
