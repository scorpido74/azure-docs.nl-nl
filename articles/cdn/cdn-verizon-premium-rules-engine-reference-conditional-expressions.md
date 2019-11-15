---
title: Voorwaardelijke expressies voor de engine voor Azure CDN-Verizon Premium-regels
description: Referentie documentatie voor Azure CDN van de overeenkomst met voor waarden en functies van de Verizon Premium-regels-engine.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: d28bda28894477845c2a050666b3b4dd332b7d50
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082973"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Azure CDN van de voorlopige expressies van de Verizon Premium-regel engine

In dit artikel vindt u gedetailleerde beschrijvingen van de engine voor het [opstellen](cdn-verizon-premium-rules-engine.md)van voorwaardelijke expressies voor Azure Content Delivery Network (CDN).

Het eerste deel van een regel is de voorwaardelijke expressie.

Voorwaardelijke expressie | Beschrijving
-----------------------|-------------
IF | Een IF-expressie is altijd een onderdeel van de eerste instructie in een regel. Net als bij alle andere voorwaardelijke expressies moet deze IF-instructie worden gekoppeld aan een overeenkomst. Als er geen aanvullende voorwaardelijke expressies worden gedefinieerd, wordt met deze overeenkomst het criterium bepaald waaraan moet worden voldaan voordat een set functies op een aanvraag kan worden toegepast.
EN ALS | Een en als-expressie kan alleen worden toegevoegd na de volgende typen voorwaardelijke expressies: als en als. Dit geeft aan dat er een andere voor waarde is waaraan moet worden voldaan voor de eerste IF-instructie.
ELSE IF| Een ELSE IF-expressie geeft een alternatieve voor waarde aan waaraan moet worden voldaan voor een set functies die specifiek zijn voor deze ELSE als-instructie plaatsvindt. De aanwezigheid van een ELSE IF-instructie geeft het einde van de voor gaande instructie aan. De enige voorwaardelijke expressie die mag worden geplaatst na een ELSE IF-instructie, is een andere ELSE IF-instructie. Dit betekent dat een ELSE IF-instructie alleen kan worden gebruikt om een enkele aanvullende voor waarde op te geven waaraan moet worden voldaan.

**Voor beeld**: ![CDN-voor waarde](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Een volgende regel kan de acties overschrijven die door een vorige regel zijn opgegeven.
   > Voor beeld: met een catch-all-regel worden alle aanvragen beveiligd via verificatie op basis van tokens. U kunt een andere regel direct hieronder maken om een uitzonde ring te maken voor bepaalde typen aanvragen.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure CDN](cdn-overview.md)
- [Verwijzing regelengine](cdn-verizon-premium-rules-engine-reference.md)
- [Criteria voor overeenkomst regelengine](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Functies regelengine](cdn-verizon-premium-rules-engine-reference-features.md)
- [Standaard HTTP-gedrag negeren met de regel engine](cdn-verizon-premium-rules-engine.md)