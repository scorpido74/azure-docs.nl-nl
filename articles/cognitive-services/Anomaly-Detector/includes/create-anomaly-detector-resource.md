---
title: Ondersteuning voor containers
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b40f1833f08074cb0a8d45fe3afc6bac7cbac7f0
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522689"
---
## <a name="create-an-anomaly-detector-resource"></a>Een afwijkende detector-resource maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)
1. Klik [op **anomalie detectie** resource maken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)
1. Voer alle vereiste instellingen in:

    |Instelling|Waarde|
    |--|--|
    |Naam|Gewenste naam (2-64 tekens)|
    |Abonnement|Selecteer het juiste abonnement|
    |Locatie|Selecteer een locatie in de buurt en beschik bare locaties|
    |Prijscategorie|`F0`-de minimale prijs categorie|
    |Resourcegroep|Een beschik bare resource groep selecteren|
    |Selectie vakje voor beeld bevestiging (vereist)|Of u de **Preview-versie** hebt gelezen|

1. Klik op **maken** en wacht tot de resource is gemaakt. Nadat deze is gemaakt, gaat u naar de pagina Resource
1. Geconfigureerde `endpoint` en een API-sleutel verzamelen:

    |Het tabblad resource in de portal|Instelling|Waarde|
    |--|--|--|
    |**Overzicht**|Eindpunt|Kopieer het eind punt. Het lijkt op `https://westus2.api.cognitive.microsoft.com/`|
    |**Subknooppuntsleutels**|API-sleutel|Kopieer 1 van de twee sleutels. Het is een teken reeks van 32 alfanumerieke tekens zonder spaties of streepjes `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|



