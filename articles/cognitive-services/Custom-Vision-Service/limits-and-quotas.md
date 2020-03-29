---
title: Limieten en quota - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: In dit artikel worden de verschillende typen licentiesleutels en de limieten en quota voor de Custom Vision-service uitgelegd.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 5f481ebf219eea8ae31e9802144bbf771a24e8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79081774"
---
# <a name="limits-and-quotas"></a>Limieten en quota

Er zijn twee lagen sleutels voor de Custom Vision-service. U zich aanmelden voor een F0-abonnement (gratis) of S0 (standaard) via de Azure-portal. Zie de bijbehorende [pagina Cognitive Services Pricing](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) voor meer informatie over prijzen en transacties.

Het aantal trainingsafbeeldingen per project en tags per project zal naar verwachting in de loop van de tijd toenemen voor S0-projecten.

||**F0**|**S0**|
|-----|-----|-----|
|Projecten|2|100|
|Trainingsbeelden per project |5.000|100.000|
|Voorspellingen / maand|10.000 |Onbeperkt|
|Tags / project|50|500|
|Iteraties |10|10|
|Min gelabelde afbeeldingen per tag, classificatie (50+ aanbevolen) |5|5|
|Min gelabelde afbeeldingen per tag, objectdetectie (50+ aanbevolen)|15|15|
|Hoe lang voorspellingsafbeeldingen zijn opgeslagen|30 dagen|30 dagen|
|[Voorspellingsbewerkingen](https://go.microsoft.com/fwlink/?linkid=865445) met opslag (transacties per seconde)|2|10|
|[Voorspellingsbewerkingen](https://go.microsoft.com/fwlink/?linkid=865445) zonder opslag (transacties per seconde)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (API-aanroepen per seconde)|2|10|
|[Andere API-aanroepen](https://go.microsoft.com/fwlink/?linkid=865446) (transacties per seconde)|10|10|
|Geaccepteerde afbeeldingstypen|jpg, png, bmp, gif|jpg, png, bmp, gif|
|Min-afbeeldingshoogte/-breedte in pixels|256 (zie noot)|256 (zie noot)|
|Maximale afbeeldingshoogte/breedte in pixels|onbeperkt|onbeperkt|
|Maximale afbeeldingsgrootte (uploadvan trainingsafbeelding) |6 MB|6 MB|
|Maximale afbeeldingsgrootte (voorspelling)|4 MB|4 MB|
|Maximale regio's per trainingsafbeelding voor objectdetectie|300|300|
|Max-tags per classificatieafbeelding|100|100|

> [!NOTE]
> Afbeeldingen kleiner dan 256 pixels worden geaccepteerd, maar opgeschaald.
