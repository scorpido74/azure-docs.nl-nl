---
title: Ondersteuning voor containers
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: aahi
ms.openlocfilehash: 0a330c2401412e1e3d5e2e49bf8121a681342808
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80878359"
---
## <a name="create-an-face-resource"></a>Een gezichts bron maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)
1. Klik [op **gezichts** bron maken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
1. Voer alle vereiste instellingen in:

    |Instelling|Waarde|
    |--|--|
    |Naam|Gewenste naam (2-64 tekens)|
    |Abonnement|Selecteer het juiste abonnement|
    |Locatie|Selecteer een locatie in de buurt en beschik bare locaties|
    |Prijscategorie|`F0` -de minimale prijs categorie|
    |Resource Group|Een beschik bare resource groep selecteren|

1. Klik op **maken** en wacht tot de resource is gemaakt. Nadat deze is gemaakt, gaat u naar de pagina Resource
1. Geconfigureerde `endpoint` en API-sleutel verzamelen:

    |Het tabblad resource in de portal|Instelling|Waarde|
    |--|--|--|
    |**Overzicht**|Eindpunt|Kopieer het eind punt. Deze lijkt op `https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Sleutels**|API-sleutel|Kopieer 1 van de twee sleutels. Het is een teken reeks van 32 alfanumerieke tekens zonder spaties of streepjes `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|
