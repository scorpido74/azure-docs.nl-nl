---
title: Problemen met Azure Internet Analyzer oplossen | Microsoft Docs
description: De naslag informatie voor probleem oplossing voor Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a7216e697680bcc049d2ceb4caec74adfc1760b0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897388"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Problemen met Azure Internet Analyzer oplossen

Dit artikel bevat probleemoplossings stappen voor veelvoorkomende problemen met Internet Analyzer.

## <a name="azure-portal"></a>Azure Portal
**Er zijn niet genoeg metingen verzameld voor deze score card in de sectie Score cards**

Houd rekening met het volgende:
- Meet gegevens worden alleen verzameld als het client script van het Internet Analyzer-profiel is inge sloten in een toepassing die werkelijk gebruikers verkeer ontvangt. Synthetische verkeer (bijvoorbeeld prestatie tests van Azure WebApp) voert doorgaans geen Inge sloten java script-code uit, dus er worden geen metingen gegenereerd door dat type verkeer.
- De tijd reeks wordt één keer per uur gegenereerd, dus u moet ten minste de tijd wachten waarop nieuwe meet gegevens moeten worden weer gegeven.
- Score cards worden dagelijks gegenereerd (aan het einde van elke dag, UTC-tijd).
- Score cards worden alleen gegenereerd als er meer dan 100 metingen zijn verzameld voor de geselecteerde filter combinatie (test, tijds periode, land, enzovoort).

## <a name="next-steps"></a>Volgende stappen
Lees de [Veelgestelde vragen over Internet analyse](internet-analyzer-faq.md)
