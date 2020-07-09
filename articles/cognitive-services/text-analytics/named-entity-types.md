---
title: Ondersteunde categorieën voor herkenning van benoemde entiteiten
titleSuffix: Azure Cognitive Services
description: Meer informatie over de ondersteunde entiteits categorieën vindt u in de Text Analytics-API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: e36a69be19844a75562f87d3c195494e3ef148a9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108486"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Ondersteunde entiteits categorieën in de Text Analytics-API v3

In dit artikel vindt u informatie over de entiteits categorieën die kunnen worden geretourneerd door [named entity Recognition](how-tos/text-analytics-how-to-entity-linking.md) (ner). Er is ook een preview van NER v 3.1 beschikbaar, waaronder de mogelijkheid om persoonlijke ( `PII` ) en status gegevens te detecteren `PHI` . Klik vervolgens op het tabblad **status** om een lijst met ondersteunde categorieën in Text Analytics voor de status weer te geven.

## <a name="entity-categories"></a>Entiteits Categorieën

#### <a name="general"></a>[Algemeen](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="personal"></a>[Persoonlijk](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Gezondheidszorg](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Volgende stappen

* [Benoemde entiteits herkenning gebruiken in Text Analytics](how-tos/text-analytics-how-to-entity-linking.md)
