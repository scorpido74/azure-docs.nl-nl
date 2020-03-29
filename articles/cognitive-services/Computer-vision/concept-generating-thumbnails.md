---
title: Slim bijgesneden miniaturen - Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot het genereren van miniaturen voor afbeeldingen met behulp van de Computer Vision API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4874910f37b49990a659b48af0cf27921c3fcd5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68945220"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Slim bijgesneden miniaturen genereren met Computer Vision

Een miniatuur is een weergave van een afbeelding met een kleiner formaat. Miniaturen worden gebruikt om afbeeldingen en andere gegevens op een meer economische, lay-outvriendelijke manier weer te geven. De Computer Vision API maakt gebruik van slimme bijsnijden, samen met het aanpassen van de grootte van de afbeelding, om intuïtieve miniaturen voor een bepaalde afbeelding te maken.

Het algoritme voor het genereren van miniatuurminiaturen van Computer Vision werkt als volgt:

1. Verwijder afleidende elementen uit de afbeelding en identificeer het _interessegebied_&mdash;van het gebied van de afbeelding waarin het hoofdobject(en) wordt weergegeven.
1. Snijd de afbeelding bij op basis van het geïdentificeerde _interessegebied_.
1. Wijzig de beeldverhouding die past bij de afmetingen van de doelminiatuur.

## <a name="area-of-interest"></a>Interessegebied

Wanneer u een afbeelding uploadt, analyseert de Computer Vision API deze om het *interessegebied*te bepalen. Het kan deze regio vervolgens gebruiken om te bepalen hoe de afbeelding moet worden bijgestoond. De bijsnijdbewerking komt echter altijd overeen met de gewenste beeldverhouding als deze is opgegeven.

U ook de raw bounding box coördinaten van hetzelfde *interessegebied* krijgen door in plaats daarvan de **areaOfInterest** API aan te roepen. U deze informatie vervolgens gebruiken om de oorspronkelijke afbeelding te wijzigen zoals u dat wilt.

## <a name="examples"></a>Voorbeelden

De gegenereerde miniatuur kan sterk variëren, afhankelijk van wat u opgeeft voor hoogte, breedte en slimme bijsnijding, zoals in de volgende afbeelding wordt weergegeven.

![Een bergafbeelding naast verschillende bijsnijdconfiguraties](./Images/thumbnail-demo.png)

In de volgende tabel worden typische miniaturen weergegeven die door Computer Vision zijn gegenereerd voor de voorbeeldafbeeldingen. De miniaturen zijn gegenereerd voor een opgegeven doelhoogte en breedte van 50 pixels, waarbij slimme bijsnijden is ingeschakeld.

| Installatiekopie | Miniatuur |
|-------|-----------|
|![OpenluchtBerg bij zonsondergang, met het silhouet van een persoon](./Images/mountain_vista.png) | ![Miniatuur van OpenluchtBerg bij zonsondergang, met het silhouet van een persoon](./Images/mountain_vista_thumbnail.png) |
|![Een witte bloem met een groene achtergrond](./Images/flower.png) | ![Vision Analyseer bloemminiatuur](./Images/flower_thumbnail.png) |
|![Een vrouw op het dak van een flatgebouw](./Images/woman_roof.png) | ![miniatuur van een vrouw op het dak van een flatgebouw](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het taggen van afbeeldingen](concept-tagging-images.md) en het [categoriseren van afbeeldingen](concept-categorizing-images.md).
