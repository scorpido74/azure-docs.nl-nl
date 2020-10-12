---
title: Voorwaardelijke expressies voor de engine voor Azure CDN-Verizon Premium-regels
description: Referentie documentatie voor Azure CDN van de overeenkomst met voor waarden en functies van de Verizon Premium-regels-engine.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 25b3c1a011e39adea651a6daa1d4ab9aa3a9d186
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81253506"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Azure CDN van de voorlopige expressies van de Verizon Premium-regel engine

In dit artikel vindt u gedetailleerde beschrijvingen van de engine voor het [opstellen](cdn-verizon-premium-rules-engine.md)van voorwaardelijke expressies voor Azure Content Delivery Network (CDN).

Het eerste deel van een regel is de voorwaardelijke expressie.

Voorwaardelijke expressie | Beschrijving
-----------------------|-------------
IF | Een IF-expressie is altijd een onderdeel van de eerste instructie in een regel. Net als bij alle andere voorwaardelijke expressies moet deze IF-instructie worden gekoppeld aan een overeenkomst. Als er geen aanvullende voorwaardelijke expressies worden gedefinieerd, wordt met deze overeenkomst het criterium bepaald waaraan moet worden voldaan voordat een set functies op een aanvraag kan worden toegepast.
EN ALS | Een en als-expressie kan alleen worden toegevoegd na de volgende typen voorwaardelijke expressies: als en als. Dit geeft aan dat er een andere voor waarde is waaraan moet worden voldaan voor de eerste IF-instructie.
ELSE IF| Een ELSE IF-expressie geeft een alternatieve voor waarde aan waaraan moet worden voldaan voor een set functies die specifiek zijn voor deze ELSE als-instructie plaatsvindt. De aanwezigheid van een ELSE IF-instructie geeft het einde van de voor gaande instructie aan. De enige voorwaardelijke expressie die mag worden geplaatst na een ELSE IF-instructie, is een andere ELSE IF-instructie. Dit betekent dat een ELSE IF-instructie alleen kan worden gebruikt om een enkele aanvullende voor waarde op te geven waaraan moet worden voldaan.

**Voor beeld**: ![ CDN match-voor waarde](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Een volgende regel kan de acties overschrijven die door een vorige regel zijn opgegeven.
   > Voor beeld: met een catch-all-regel worden alle aanvragen beveiligd via Token-Based-verificatie. U kunt een andere regel direct hieronder maken om een uitzonde ring te maken voor bepaalde typen aanvragen.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure CDN](cdn-overview.md)
- [Referentie voor regels-engine](cdn-verizon-premium-rules-engine-reference.md)
- [Overeenkomende voor waarden voor regel engine](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Functies van de regelengine](cdn-verizon-premium-rules-engine-reference-features.md)
- [Standaard HTTP-gedrag negeren met de regel engine](cdn-verizon-premium-rules-engine.md)