---
title: Late fase herprojectie
description: Informatie over late stage reprojection en hoe deze te gebruiken.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 4aa1148e544ff3451aa1cb956bc4a5fb932b9611
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680984"
---
# <a name="late-stage-reprojection"></a>Late fase herprojectie

*Late Stage Reprojection* (LSR) is een hardwarefunctie die hologrammen helpt stabiliseren wanneer de gebruiker beweegt.

Statische modellen worden verwacht dat ze hun positie visueel behouden wanneer u zich eromheen beweegt. Als ze onstabiel lijken te zijn, kan dit gedrag wijzen op LSR-problemen. Houd er rekening mee dat extra dynamische transformaties, zoals animaties of explosieweergaven, dit gedrag kunnen maskeren.

U kiezen uit twee verschillende LSR-modi, namelijk **Planar LSR** of **Depth LSR.** Welke actief is, wordt bepaald door of de clientaanvraag een dieptebuffer indient.

Beide LSR-modi verbeteren de stabiliteit van het hologram, hoewel ze hun verschillende beperkingen hebben. Begin met het proberen Diepte LSR, want het is aantoonbaar het geven van betere resultaten in de meeste gevallen.

## <a name="choose-lsr-mode-in-unity"></a>Kies lsr-modus in Unity

Ga in de Unity-editor naar *Bestand > Build-instellingen*. Selecteer *Player-instellingen* linksonder en controleer onder *Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality* of Depth Buffer Sharing **inschakelen** is ingeschakeld:

![Markeren voor delen van diepte](./media/unity-depth-buffer-sharing-enabled.png)

Als dit het geval is, gebruikt uw app Depth LSR, anders gebruikt het Planar LSR.

## <a name="depth-lsr"></a>Diepte LSR

Als diepte LSR werkt, moet de clienttoepassing een geldige dieptebuffer leveren die alle relevante geometrie bevat waarmee u tijdens LSR rekening moet houden.

Diepte LSR probeert het videoframe te stabiliseren op basis van de inhoud van de meegeleverde dieptebuffer. Als gevolg hiervan kan inhoud die er niet is weergegeven, zoals transparante objecten, niet worden aangepast door LSR en kan het instabiliteits- en herprojectieartefacten weergeven.

## <a name="planar-lsr"></a>Vlakke LSR

Planar LSR heeft geen diepte-informatie per pixel, zoals Depth LSR doet. In plaats daarvan herprojecteert het alle inhoud op basis van een vlak dat u elk frame moet opgeven.

Planar LSR herprojecteert die objecten het beste die dicht bij het geleverde vlak liggen. Hoe verder een object is, hoe instabieler het eruit ziet. Hoewel Depth LSR beter is in het opnieuw projecteren van objecten op verschillende diepten, kan Planar LSR beter werken voor inhoud die goed is uitgelijnd met een vlak.

### <a name="configure-planar-lsr-in-unity"></a>Planar LSR in eenheid configureren

De vlakparameters zijn afgeleid van een zogenaamd *focuspunt,* waar `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`je elk frame doorheen moet geven. Zie de [Unity Focus Point API](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) voor meer informatie. Als u geen focuspunt instelt, wordt er een terugval voor u gekozen. Maar die automatische terugval leidt vaak tot suboptimale resultaten.

U het focuspunt zelf berekenen, maar het kan zinvol zijn om het te baseren op het focuspunt dat is berekend door de extern renderende host. Bel `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` om dat te verkrijgen. U wordt gevraagd om een coördinaatkader te geven waarin u het focuspunt uitdrukken. In de meeste gevallen wilt u alleen `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` het resultaat van hier.

Meestal maken zowel de client als de host inhoud weer waarvan de andere kant zich niet bewust is, zoals UI-elementen op de client. Daarom is het zinvol om het externe focuspunt te combineren met een lokaal berekend punt.

De focuspunten berekend in twee opeenvolgende frames kunnen heel verschillend zijn. Gewoon met behulp van hen as-is kan leiden tot hologrammen lijken te springen rond. Om dit gedrag te voorkomen, interpoleren tussen de vorige en huidige focuspunten is raadzaam.

## <a name="next-steps"></a>Volgende stappen

* [Prestatiequery's aan de serverzijde](performance-queries.md)
