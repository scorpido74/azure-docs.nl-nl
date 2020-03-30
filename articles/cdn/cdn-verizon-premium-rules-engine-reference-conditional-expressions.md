---
title: Voorwaardelijke expressies voor Azure CDN - Verizon Premium-regelsengine
description: Referentiedocumentatie voor Azure CDN van Verizon Premium-regels engine komen overeen met voorwaarden en functies.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: d28bda28894477845c2a050666b3b4dd332b7d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082973"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Azure CDN van Verizon Premium regels engine voorwaardelijke expressies

In dit artikel worden gedetailleerde beschrijvingen weergegeven van de CDN-rules engine (Conditional Expressions for Azure Content Delivery [Network).](cdn-verizon-premium-rules-engine.md)

Het eerste deel van een regel is de voorwaardelijke expressie.

Voorwaardelijke expressie | Beschrijving
-----------------------|-------------
IF | Een ALS-expressie is altijd een onderdeel van de eerste instructie in een regel. Net als alle andere voorwaardelijke expressies moet deze IF-instructie aan een overeenkomst worden gekoppeld. Als er geen aanvullende voorwaardelijke expressies worden gedefinieerd, bepaalt deze overeenkomst het criterium waaraan moet worden voldaan voordat een reeks functies op een aanvraag kan worden toegepast.
EN ALS | Een EN ALS-expressie mag alleen worden toegevoegd na de volgende typen voorwaardelijke expressies:ALS EN ALS. Het geeft aan dat er een andere voorwaarde die moet worden voldaan voor de eerste ALS-instructie.
ANDERS ALS| Een else if-expressie geeft een alternatieve voorwaarde op waaraan moet worden voldaan voordat een reeks functies die specifiek zijn voor deze instructie ELSE IF plaatsvindt. De aanwezigheid van een INSTRUCTIE ELSE IF geeft het einde van de vorige instructie aan. De enige voorwaardelijke expressie die na een else IF-instructie kan worden geplaatst, is een andere ANDERS ALS-instructie. Dit betekent dat een anders ALS-instructie alleen mag worden gebruikt om één aanvullende voorwaarde op te geven waaraan moet worden voldaan.

**Voorbeeld** ![: CDN-overeenkomstvoorwaarde](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Een volgende regel kan de acties overschrijven die door een vorige regel zijn opgegeven.
   > Voorbeeld: Een catch-all-regel beveiligt alle aanvragen via tokenverificatie. Er kan direct een andere regel onder worden gemaakt om een uitzondering te maken voor bepaalde soorten aanvragen.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure CDN](cdn-overview.md)
- [Verwijzingen naar de motor](cdn-verizon-premium-rules-engine-reference.md)
- [Regels motor overeenkomen met voorwaarden](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regels motorfuncties](cdn-verizon-premium-rules-engine-reference-features.md)
- [StandaardHTTP-gedrag overschrijven met de rules-engine](cdn-verizon-premium-rules-engine.md)