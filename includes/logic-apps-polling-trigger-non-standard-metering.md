---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/09/2018
ms.openlocfilehash: 89c2467843d7abc7c005804fd5263fe3beb668b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74793444"
---
Als u nauwkeurigere verbruikskosten wilt schatten, moet u rekening houden met het mogelijke aantal berichten of gebeurtenissen dat op een bepaalde dag kan aankomen, in plaats van uw berekeningen te baseren op alleen het steminterval. Wanneer een gebeurtenis of bericht voldoet aan de triggercriteria, proberen veel triggers onmiddellijk alle andere wachtgebeurtenissen of berichten te lezen die aan de criteria voldoen. Dit gedrag betekent dat zelfs wanneer u een langer steminterval selecteert, de trigger wordt geactiveerd op basis van het aantal wachtgebeurtenissen of berichten die in aanmerking komen voor het starten van werkstromen. Triggers die dit gedrag volgen, zijn Azure Service Bus en Azure Event Hub.

Stel dat u bijvoorbeeld een trigger instelt die elke dag een eindpunt controleert. Wanneer de trigger het eindpunt controleert en 15 gebeurtenissen vindt die aan de criteria voldoen, wordt de trigger 15 keer geactiveerd en uitgevoerd. Logic Apps meet alle acties die deze 15 werkstromen uitvoeren, inclusief de triggeraanvragen. Als u uw potentiële kosten wilt berekenen, probeert u de [azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/).