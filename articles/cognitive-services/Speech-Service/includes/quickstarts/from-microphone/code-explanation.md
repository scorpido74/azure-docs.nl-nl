---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3125b6203f04b4cabd8cd0822a97317185cbf194
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638091"
---
De abonnementssleutel voor spraakbronnen en het gebied zijn vereist om een spraakconfiguratieobject te maken. Het configuratieobject is nodig om een object voor spraakherkenning te instantiëren.

De instantie herkennende instantie legt meerdere manieren bloot om spraak te herkennen. In dit voorbeeld wordt spraak eenmaal herkend. Met deze functionaliteit laat de spraakservice weten dat u één woordgroep verzendt voor herkenning en dat wanneer de woordgroep is geïdentificeerd om spraak niet meer te herkennen. Zodra het resultaat is opgeleverd, zal de code de herkenningreden naar de console schrijven.

> [!TIP]
> De Spraak-SDK wordt standaard `en-us` herkend voor de taal, zie [Brontaal opgeven voor spraak naar tekst](../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.