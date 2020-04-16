---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400792"
---
De abonnementssleutel voor spraakbronnen en het gebied zijn vereist om een spraakconfiguratieobject te maken. Het configuratieobject is nodig om een object voor spraakherkenning te instantiëren.

De instantie herkennende instantie legt meerdere manieren bloot om spraak te herkennen. In dit voorbeeld wordt spraak eenmaal herkend. Met deze functionaliteit laat de spraakservice weten dat u één woordgroep verzendt voor herkenning en dat wanneer de woordgroep is geïdentificeerd om spraak niet meer te herkennen. Zodra het resultaat is opgeleverd, zal de code de herkenningreden naar de console schrijven.

> [!TIP]
> De Spraak-SDK wordt standaard `en-us` herkend voor de taal, zie [Brontaal opgeven voor spraak naar tekst](../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.