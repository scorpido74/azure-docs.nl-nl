---
title: Info
description: Inleiding in Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: overview
ms.openlocfilehash: a06c63152cb56be6d94cccc472d2e1d65651d6ce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679952"
---
# <a name="about-azure-remote-rendering"></a>Informatie over Azure Remote Rendering

> [!IMPORTANT]
> **Azure Remote Rendering** is momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

*Azure Remote Rendering* (ARR) is een service waarmee u interactieve 3D-inhoud van hoge kwaliteit in de cloud renderen en deze in realtime streamen naar apparaten, zoals de HoloLens 2.

![Voorbeeldmodel](../media/arr-engine.png)

Ongebonden apparaten hebben beperkte rekenkracht voor het renderen van complexe modellen. Voor veel toepassingen zou het echter onaanvaardbaar zijn om de visuele trouw op enigerlei wijze te verminderen.

*Remote Rendering* lost dit probleem op door de renderingworkload te verplaatsen naar high-end GPU's in de cloud. Een cloud-hosted graphics engine maakt het beeld, codeert het als een videostream, en streams dat naar het doelapparaat.

## <a name="hybrid-rendering"></a>Hybride rendering

In de meeste toepassingen volstaat het niet om alleen een complex model weer te geven. U hebt ook aangepaste gebruikersinterface nodig om functionaliteit aan de gebruiker te bieden. Azure Remote Rendering dwingt u niet om een speciaal UI-framework te gebruiken, maar ondersteunt *hybride rendering.* Dit betekent dat u elementen op het apparaat renderen met behulp van uw voorkeursmethode, zoals [MRTK.](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/GettingStartedWithTheMRTK.html)

Aan het einde van een frame combineert Azure Remote Rendering vervolgens automatisch uw lokaal gerenderde inhoud met de externe afbeelding. Het is zelfs in staat om dit te doen met de juiste occlusie.

## <a name="multi-gpu-rendering"></a>Multi-GPU rendering

Sommige modellen zijn te complex om te renderen met interactieve framesnelheden, zelfs voor een high-end GPU. Vooral in industriële visualisatie is dit een veel voorkomend probleem. Om de limieten verder te verleggen, kan Azure Remote Rendering de werkbelasting distribueren naar meerdere GPU's. De resultaten worden samengevoegd tot één afbeelding, waardoor het proces volledig transparant is voor de gebruiker.

## <a name="high-level-architecture"></a>Architectuur op hoog niveau

Dit diagram illustreert de externe renderingarchitectuur:

![Architectuur](./media/arr-high-level-architecture.png)

Een volledige cyclus voor het genereren van afbeeldingen omvat de volgende stappen:

1. Clientzijde: Frame-setup
    1. Uw code: gebruikersinvoer wordt verwerkt, scènegrafiek wordt bijgewerkt
    1. ARR-code: Scènegrafiek-updates en voorspelde hoofdpose worden naar de server verzonden
1. Serverzijde: externe rendering
    1. Rendering engine distribueert rendering over beschikbare GPU's
    1. Uitvoer van meerdere GPU's wordt samengesteld in één afbeelding
    1. Afbeelding is gecodeerd als videostream en teruggestuurd naar client
1. Client-kant: Finalisatie
    1. Uw code: Optionele lokale inhoud (gebruikersinterface, markeringen, ...) wordt weergegeven
    1. ARR-code: Op 'aanwezig' wordt lokaal gerenderde inhoud automatisch samengevoegd met videostream

Netwerklatentie is het grootste probleem. De doorlooptijd tussen het verzenden van een aanvraag en het ontvangen van het resultaat is meestal te lang voor interactieve framerates. Daarom kan meer dan één frame op elk moment in de vlucht zijn.

## <a name="next-steps"></a>Volgende stappen

* [Systeemvereisten](system-requirements.md)
* [Snelstart: een model weergeven met Unity](../quickstarts/render-model.md)
