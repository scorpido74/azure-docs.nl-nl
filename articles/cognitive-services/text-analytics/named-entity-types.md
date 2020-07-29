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
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: f66ed9a7d26a0c2d7def7d17820379b8e3460460
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373063"
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
