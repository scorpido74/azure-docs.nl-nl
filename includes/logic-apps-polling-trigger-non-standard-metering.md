---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/09/2018
ms.openlocfilehash: 89c2467843d7abc7c005804fd5263fe3beb668b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74793444"
---
U kunt de verbruikskosten nog nauwkeuriger schatten door te kijken naar het mogelijke aantal berichten of gebeurtenissen dat op een willekeurige dag kan arriveren, in plaats van uw berekeningen alleen op het polling-interval te baseren. Wanneer een gebeurtenis of bericht voldoet aan de criteria voor triggers, zullen de triggers onmiddellijk een poging doen alle gebeurtenissen die in de wacht staan of berichten die aan de criteria voldoen, te lezen. Dit gedrag houdt in dat ook als u een langere polling-interval selecteert, de trigger wordt geactiveerd op basis van het aantal gebeurtenissen in de wacht of berichten die in aanmerking komen voor het starten van werkstromen. Triggers die dit gedrag volgen, zijn onder andere Azure Service Bus en Azure Event Hub.

Stel bijvoorbeeld dat u een trigger hebt ingesteld waarmee elke dag een eindpunt wordt gecontroleerd. Wanneer de trigger het eindpunt controleert en er vijftien gebeurtenissen vindt die aan de criteria voldoen, wordt de trigger geactiveerd en wordt de corresponderende werkstroom vijftien keer uitgevoerd. Logic Apps meet alle acties die door die vijftien werkstromen worden uitgevoerd, inclusief de triggeraanvragen. Gebruik de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/) om uw potentiële kosten te berekenen.