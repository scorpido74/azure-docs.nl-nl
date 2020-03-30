---
title: Azure CDN-profiel migreren van Verizon Standard naar Verizon Premium
description: Meer informatie over de details van het migreren van een profiel van Verizon Standard naar Verizon Premium.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 8ab66117be4f05550b00defafc883108646be283
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083062"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Een Azure CDN-profiel migreren van Standard Verizon naar Premium Verizon

Wanneer u een CDN-profiel (Azure Content Delivery Network) maakt om uw eindpunten te beheren, biedt Azure CDN vier verschillende producten waaruit u kiezen. Zie [Azure CDN-productfuncties vergelijken](cdn-features.md)voor informatie over de verschillende producten en de beschikbare functies daarvan.

Als u een **Azure CDN-standaard** hebt gemaakt vanuit het Verizon-profiel en deze gebruikt om uw CDN-eindpunten te beheren, hebt u de optie om deze te upgraden naar een **Azure CDN Premium vanuit het Verizon-profiel.** Wanneer u een upgrade uitvoert, worden uw CDN-eindpunten en al uw gegevens bewaard. 

> [!IMPORTANT]
> Zodra u een upgrade hebt uitgevoerd naar een **Azure CDN Premium vanuit** Verizon-profiel, u deze niet later converteren naar een **Azure CDN-standaard vanuit het Verizon-profiel.**
> 

Neem contact op met [Microsoft Support](https://azure.microsoft.com/support/options/)om een **Azure CDN-standaard te** upgraden vanuit het Verizon-profiel.

## <a name="profile-comparison"></a>Profielvergelijking
**Azure CDN Premium van Verizon-profielen** hebben de volgende belangrijke verschillen met **Azure CDN-standaard van Verizon-profielen:**
- Voor bepaalde Azure CDN-functies, zoals [compressie,](cdn-improve-performance.md) [cachingregels](cdn-caching-rules.md)en [geofiltering,](cdn-restrict-access-by-country.md)u de Azure CDN-interface niet gebruiken, u moet de Verizon-portal gebruiken via de knop **Beheren.**
- API: In tegenstelling tot Standard Verizon u de API niet gebruiken om de functies te beheren die toegankelijk zijn via de Premium Verizon-portal. U de API echter gebruiken om andere veelvoorkomende functies te beheren, zoals het maken/verwijderen van een eindpunt, het zuiveren/laden van cache-elementen en het in- en uitschakelen van een aangepast domein.
- Prijzen: Premium Verizon heeft een andere prijsstructuur voor gegevensoverdrachten dan Standard Verizon. Zie [Content Delivery Network-prijzen voor](https://azure.microsoft.com/pricing/details/cdn/)meer informatie .

**Azure CDN Premium van Verizon-profielen** hebben de volgende extra functies:
- [Tokenverificatie:](cdn-token-auth.md)hiermee kunnen gebruikers een token verkrijgen en gebruiken om beveiligde bronnen op te halen.
- [Rules engine:](cdn-rules-engine.md)Hiermee u aanpassen hoe HTTP-aanvragen worden behandeld.
- Geavanceerde analysetools:
   - [Gedetailleerde HTTP-analyses](cdn-advanced-http-reports.md)
   - [Geavanceerde prestatieanalyses](cdn-edge-performance.md)
   - [Realtime analyses](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Volgende stappen
Zie [Azure CDN-regels engine reference](cdn-rules-engine-reference.md)voor meer informatie over de engine voor regels .

