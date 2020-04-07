---
title: Z-gevechten mitigatie
description: Beschrijft technieken om z-gevechten artefacten te beperken
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: bc06deafe3f589fce9a9178fefdb22388254929d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680451"
---
# <a name="z-fighting-mitigation"></a>Z-gevechten mitigatie

Wanneer twee oppervlakken elkaar overlappen, is het niet duidelijk welke op de andere moet worden weergegeven. Het resultaat varieert zelfs per pixel, wat resulteert in beeldafhankelijke artefacten. Bijgevolg, wanneer de camera of het gaas beweegt, deze patronen flikkeren merkbaar. Dit artefact heet *z-gevechten.* Voor AR- en VR-toepassingen wordt het probleem versterkt omdat hoofdapparaten van nature altijd bewegen. Om ongemak van kijkers te voorkomen is z-fighting mitigatiefunctionaliteit beschikbaar in Azure Remote Rendering.

## <a name="z-fighting-mitigation-modes"></a>Z-fighting mitigatie modi

|Situatie                        | Resultaat                               |
|---------------------------------|:-------------------------------------|
|Regelmatige z-gevechten               |![Z-gevechten](./media/zfighting-0.png)|
|Z-gevechten mitigatie ingeschakeld    |![Z-gevechten](./media/zfighting-1.png)|
|Controlebordmarkering ingeschakeld|![Z-gevechten](./media/zfighting-2.png)|

De volgende code maakt z-fighting mitigatie mogelijk:

``` cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

> [!NOTE]
> Z-fighting mitigatie is een wereldwijde instelling die alle weergegeven mazen beïnvloedt.

## <a name="reasons-for-z-fighting"></a>Redenen voor z-gevechten

Z-gevechten gebeurt vooral om twee redenen:

1. wanneer oppervlakken zijn zeer ver weg van de camera, de precisie van hun diepte waarden degraderen en de waarden worden niet te onderscheiden
1. wanneer oppervlakken in een mesh fysiek overlappen

Het eerste probleem kan altijd gebeuren en is moeilijk te elimineren. Als dit gebeurt in uw toepassing, zorg ervoor dat de verhouding van de *nabije vlakafstand* tot de *verre vlakafstand* zo laag is als praktisch. Bijvoorbeeld, een bijna vliegtuig op afstand 0,01 en ver vliegtuig op afstand 1000 zal dit probleem veel eerder veroorzaken, dan met het nabije vliegtuig op 0,1 en het verre vlak op afstand 20.

Het tweede probleem is een indicator voor slecht geschreven inhoud. In de echte wereld kunnen twee objecten niet op dezelfde plaats tegelijk zijn. Afhankelijk van de toepassing willen gebruikers mogelijk weten of overlappende oppervlakken bestaan en waar ze zich bevinden. Bijvoorbeeld, een CAD-scène van een gebouw dat is de basis voor een echte wereld constructie, mag niet bevatten fysiek onmogelijke oppervlakte kruispunten. Om visuele inspectie mogelijk te maken, is de markeringsmodus beschikbaar, die potentiële z-gevechten weergeeft als een geanimeerd dambordpatroon.

## <a name="limitations"></a>Beperkingen

De meegeleverde z-gevechten mitigatie is een beste inspanning. Er is geen garantie dat het alle z-gevechten verwijdert. Ook zal het automatisch de voorkeur aan een oppervlak boven het andere. Dus als je oppervlakken hebt die te dicht bij elkaar staan, kan het gebeuren dat het "verkeerde" oppervlak bovenaan terechtkomt. Een veelvoorkomend probleem geval is wanneer tekst en andere stickers worden toegepast op een oppervlak. Met z-vechten mitigatie ingeschakeld deze details kon gemakkelijk gewoon verdwijnen.

## <a name="performance-considerations"></a>Prestatieoverwegingen

* Het inschakelen van z-gevechten mitigatie maakt weinig tot geen prestaties overhead.
* Bovendien het inschakelen van de z-gevechten overlay maakt een niet-triviale prestaties overhead, maar het kan variëren afhankelijk van de scène.

## <a name="next-steps"></a>Volgende stappen

* [Weergavemodi](../../concepts/rendering-modes.md)
* [Late fase herprojectie](late-stage-reprojection.md)
