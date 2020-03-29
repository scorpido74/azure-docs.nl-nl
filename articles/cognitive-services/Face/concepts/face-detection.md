---
title: Concepten voor gezichtsherkenning en kenmerken
titleSuffix: Azure Cognitive Services
description: Gezichtsherkenning is de actie van het lokaliseren van menselijke gezichten in een afbeelding en optioneel het retourneren van verschillende soorten gezichtsgerelateerde gegevens.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 15e39eb9f5b8dd3556ea9ff8240bc2c9d252cd31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743052"
---
# <a name="face-detection-and-attributes"></a>Gezichtsherkenning en kenmerken

In dit artikel worden de concepten van gezichtsherkenning en gezichtskenmerkgegevens uitgelegd. Gezichtsherkenning is de actie van het lokaliseren van menselijke gezichten in een afbeelding en optioneel het retourneren van verschillende soorten gezichtsgerelateerde gegevens.

U gebruikt de [bewerking Gezicht - Detecteer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) de bewerking om gezichten in een afbeelding te detecteren. Ten minste komt elk gedetecteerd gezicht overeen met een faceRectangle-veld in het antwoord. Deze set pixelcoördinaten voor links, boven, breedte en hoogte markeert het gelokaliseerde vlak. Met behulp van deze coördinaten, u de locatie van het gezicht en de grootte ervan. In het API-antwoord worden gezichten weergegeven in groottevolgorde van grootste tot kleinste.

## <a name="face-id"></a>Face ID

De face ID is een unieke id-tekenreeks voor elk gedetecteerd gezicht in een afbeelding. U een face-id aanvragen in uw [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-aanroep.

## <a name="face-landmarks"></a>Gezichtsoriëntatiepunten

Gezicht oriëntatiepunten zijn een set van gemakkelijk te vinden punten op een gezicht, zoals de leerlingen of het puntje van de neus. Standaard zijn er 27 oriëntatiepunten vooraf gedefinieerde punten. Het volgende cijfer toont alle 27 punten:

![Een gezichtsdiagram met alle 27 oriëntatiepunten gelabeld](../Images/landmarks.1.jpg)

De coördinaten van de punten worden geretourneerd in eenheden pixels.

## <a name="attributes"></a>Kenmerken

Kenmerken zijn een set functies die optioneel kunnen worden gedetecteerd door de [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API. De volgende kenmerken kunnen worden gedetecteerd:

* **Leeftijd**. De geschatte leeftijd in jaren van een bepaald gezicht.
* **Vervagen**. De wazigheid van het gezicht in de afbeelding. Met dit kenmerk wordt een waarde geretourneerd tussen nul en één en een informele beoordeling van laag, gemiddeld of hoog.
* **Emotie.** Een lijst van emoties met hun detectie vertrouwen voor het gegeven gezicht. Vertrouwen scores worden genormaliseerd, en de scores over alle emoties optellen tot een. De emoties die terugkeren zijn geluk, verdriet, neutraal, woede, minachting, walging, verrassing en angst.
* **Blootstelling**. De belichting van het gezicht in het beeld. Met dit kenmerk wordt een waarde geretourneerd tussen nul en één en een informele beoordeling van underExposure, goodExposure of overExposure.
* **Gezichtshaar.** De geschatte aanwezigheid van gezichtshaar en de lengte voor het gegeven gezicht.
* **Geslacht**. Het geschatte geslacht van het gegeven gezicht. Mogelijke waarden zijn mannelijk, vrouwelijk en genderloos.
* **Bril.** Of het gegeven gezicht een bril heeft. Mogelijke waarden zijn NoGlasses, ReadingGlasses, Zonnebrillen en Zwembrillen.
* **Haar.** Het haartype van het gezicht. Dit attribuut laat zien of het haar zichtbaar is, of kaalheid wordt gedetecteerd en welke haarkleuren worden gedetecteerd.
* **Hoofd poseren**. De oriëntatie van het gezicht in de 3D-ruimte. Dit attribuut wordt beschreven door de toonhoogte, rol en gierhoeken in graden. De waarde bereiken zijn -90 graden tot 90 graden, -180 graden tot 180 graden, en -90 graden tot 90 graden, respectievelijk. Zie het volgende diagram voor hoektoewijzingen:

    ![Een hoofd met de toonhoogte, rol, en yaw assen geëtiketteerd](../Images/headpose.1.jpg)
* **Make-up.** Of het gezicht make-up heeft. Dit attribuut geeft een Booleaanse waarde voor oogMake-up en lipMakeup.
* **Lawaai**. De visuele ruis gedetecteerd in het gezichtsbeeld. Met dit kenmerk wordt een waarde geretourneerd tussen nul en één en een informele beoordeling van laag, gemiddeld of hoog.
* **Occlusie**. Of er objecten zijn die delen van het gezicht blokkeren. Dit attribuut geeft een Booleaanse waarde voor oogOccluded, voorhoofdOccluded, en mondOccluded.
* **Glimlach**. De glimlachuitdrukking van het gegeven gezicht. Deze waarde is tussen nul voor geen glimlach en een voor een duidelijke glimlach.

> [!IMPORTANT]
> Gezichtskenmerken worden voorspeld door het gebruik van statistische algoritmen. Ze zijn misschien niet altijd accuraat. Wees voorzichtig wanneer u beslissingen neemt op basis van kenmerkgegevens.

## <a name="input-data"></a>Invoergegevens

Gebruik de volgende tips om ervoor te zorgen dat uw invoerafbeeldingen de meest nauwkeurige detectieresultaten opleveren:

* De ondersteunde invoerafbeeldingsindelingen zijn JPEG, PNG, GIF voor het eerste frame en BMP.
* De bestandsgrootte van de afbeelding mag niet groter zijn dan 4 MB.
* Het detecteerbare bereik van de gezichtsgrootte is 36 x 36 tot 4096 x 4096 pixels. Gezichten buiten dit bereik worden niet gedetecteerd.
* Sommige gezichten worden mogelijk niet gedetecteerd vanwege technische problemen. Extreme gezichtshoeken (hoofdhouding) of gezichtsafzondering (voorwerpen zoals zonnebrillen of handen die een deel van het gezicht blokkeren) kunnen de detectie beïnvloeden. Frontale en bijna frontale gezichten geven de beste resultaten.

Als je gezichten uit een videofeed detecteert, kun je de prestaties mogelijk verbeteren door bepaalde instellingen op je videocamera aan te passen:

* **Vloeiend maken**: Veel videocamera's passen een vloeiend effect toe. U moet dit uitschakelen als je, omdat het een onscherpte tussen frames creëert en de helderheid vermindert.
* **Sluitertijd:** Een snellere sluitertijd vermindert de hoeveelheid beweging tussen de frames en maakt elk frame duidelijker. Wij raden sluitertijden van 1/60 seconde of sneller aan.
* **Sluiterhoek**: Sommige camera's geven sluiterhoek in plaats van sluitertijd. U moet indien mogelijk een lagere sluiterhoek gebruiken. Dit zal resulteren in duidelijkere videoframes.

    >[!NOTE]
    > Een camera met een lagere sluiterhoek krijgt minder licht in elk frame, zodat het beeld donkerder wordt. U moet het juiste niveau bepalen om te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met gezichtsherkenningsconcepten, leert u hoe u een script schrijven dat gezichten in een bepaalde afbeelding detecteert.

* [Gezichten in een afbeelding detecteren](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)