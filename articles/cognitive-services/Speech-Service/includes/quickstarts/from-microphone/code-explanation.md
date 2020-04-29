---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400792"
---
De sleutel en de regio van het spraak bron abonnement zijn vereist voor het maken van een spraak configuratie object. Het configuratie object is vereist voor het instantiëren van een spraak herkennings object.

Het Recognizer-exemplaar biedt meerdere manieren om spraak te herkennen. In dit voor beeld wordt spraak eenmaal herkend. Met deze functionaliteit kan de speech-service weten dat u één woord groep verstuurt voor herkenning en dat zodra de woord groep is geïdentificeerd om te stoppen met het herkennen van spraak. Zodra het resultaat is verkregen, wordt de herkennings reden door de code naar de console geschreven.

> [!TIP]
> De spraak-SDK wordt standaard herkend voor gebruik `en-us` voor de taal. Zie de [bron taal voor spraak opgeven voor tekst](../../../how-to-specify-source-language.md) voor informatie over het kiezen van de bron taal.