---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/09/2018
ms.openlocfilehash: 89c2467843d7abc7c005804fd5263fe3beb668b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74793444"
---
Als u nauw keuriger verbruiks kosten wilt ramen, moet u rekening houden met het mogelijke aantal berichten of gebeurtenissen dat op een wille keurige dag kan arriveren, in plaats van uw berekeningen alleen op het polling-interval te baseren. Wanneer een gebeurtenis of bericht voldoet aan de trigger criteria, proberen veel triggers elke en alle andere wacht gebeurtenissen of berichten die voldoen aan de criteria, direct te lezen. Dit gedrag houdt in dat zelfs wanneer u een langere polling-interval selecteert, de trigger wordt geactiveerd op basis van het aantal wachtende gebeurtenissen of berichten die in aanmerking komen voor het starten van werk stromen. Triggers die volgen op dit gedrag zijn onder andere Azure Service Bus en Azure Event hub.

Stel bijvoorbeeld dat u trigger hebt ingesteld waarmee een eind punt elke dag wordt gecontroleerd. Wanneer de trigger het eind punt controleert en er 15 gebeurtenissen vindt die voldoen aan de criteria, wordt de trigger geactiveerd en wordt de corresponderende werk stroom 15 keer uitgevoerd. Logic Apps meter alle acties die door die 15 werk stromen worden uitgevoerd, met inbegrip van de trigger aanvragen. Probeer de [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/)om uw potentiële kosten te berekenen.