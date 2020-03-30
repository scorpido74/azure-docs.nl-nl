---
title: Problemen met Azure Internet Analyzer | Microsoft Documenten
description: De informatie over probleemoplossing voor Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069214"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Azure Internet Analyzer probleemoplossing

Dit artikel bevat stappen voor het oplossen van problemen met veelvoorkomende problemen met Internet Analyzer.

## <a name="things-to-keep-in-mind"></a>Zaken om rekening mee te houden
- Het clientscript moet worden ingesloten in een **HTTPS-website.** Metingen worden niet verzameld als het script wordt uitgevoerd in een sitetekst **(http://)** of lokale **(file://)** website.
- Meetgegevens worden alleen verzameld als het clientscript van het Internet Analyzer-profiel is ingesloten in een toepassing die echt gebruikersverkeer ontvangt. Synthetisch verkeer (bijvoorbeeld Azure WebApp Performance Tests) voert doorgaans geen ingesloten Javascript-code uit, dus er worden geen metingen gegenereerd door dat type verkeer.

## <a name="azure-portal"></a>Azure Portal
**"Er is geen scorecard gegenereerd voor de geselecteerde filtercombinatie" in de sectie Scorecards**
- Scorecards worden dagelijks gegenereerd (aan het einde van elke dag UTC-tijd).
- Scorecards worden alleen gegenereerd als er meer dan 100 metingen zijn verzameld voor de geselecteerde filtercombinatie (Test, Tijdsperiode, Land, enz.).

**"Totaal aantal metingen" is nul voor één of beide eindpunten in een test**
- Tijdreeksen en meetaantallen worden eenmaal per uur berekend, dus u moet minstens die tijd wachten voordat nieuwe meetgegevens worden weergegeven.
- Internet Analyzer telt alleen succesvolle metingen (d.w.z. HTTP 200-reacties) voor de analyse ervan. Als een of beide eindpunten in een test onbereikbaar zijn of een niet-200 HTTP-code retourneren, worden ze weergegeven met nul totale metingen.

## <a name="next-steps"></a>Volgende stappen
Lees de [veelgestelde vragen over internetanalyse](internet-analyzer-faq.md)
