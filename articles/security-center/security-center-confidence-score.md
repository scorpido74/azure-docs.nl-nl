---
title: Betrouwbaarheidsscore in Azure Security Center | Microsoft Documenten
description: Security Center genereert vertrouwensscores om uw team te helpen bepalen of een bedreiging legitiem is en hoe u waarschuwingen kan triageen en prioriteren.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: memildin
ms.openlocfilehash: 8aa17f473c550c99d91862754e6a746575aca202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604379"
---
# <a name="alert-confidence-score-preview"></a>Waarschuwingsbetrouwbaarheidsscore (Voorbeeld)

Azure Security Center biedt u inzicht in de resources die u in Azure uitvoert en waarschuwt u wanneer het potentiële problemen detecteert. Het aantal waarschuwingen kan een uitdaging zijn voor een beveiligingsteam om individueel aan te pakken en het wordt noodzakelijk om prioriteit te geven aan welke waarschuwingen u moet onderzoeken. Het onderzoeken van waarschuwingen kan complex en tijdrovend zijn, en als gevolg hiervan worden sommige waarschuwingen genegeerd.

De vertrouwensscore (momenteel in preview) in Security Center kan uw team helpen bij het triageen en prioriteren van waarschuwingen. Security Center past automatisch best practices, intelligente algoritmen en processen toe die door analisten worden gebruikt om te bepalen of een bedreiging legitiem is en biedt u zinvolle inzichten in de vorm van een vertrouwensscore.

## <a name="how-the-confidence-score-is-triggered"></a>Hoe de vertrouwensscore wordt geactiveerd

Waarschuwingen worden gegenereerd wanneer verdachte processen worden gedetecteerd die op uw virtuele machines worden uitgevoerd. Security Center controleert en analyseert deze waarschuwingen op virtuele Windows-machines die in Azure worden uitgevoerd. Het voert geautomatiseerde controles en correlaties uit met behulp van geavanceerde algoritmen voor meerdere entiteiten en gegevensbronnen in de hele organisatie, en al uw Azure-resources, en presenteert een betrouwbaarheidsscore die een maat is voor hoe zeker Security Center is dat de waarschuwing echt is en moet worden onderzocht.

## <a name="understanding-the-confidence-score"></a>Inzicht in de betrouwbaarheidsscore

De betrouwbaarheidsscore varieert tussen 1 en 100 en vertegenwoordigt het vertrouwen dat Security Center heeft dat de waarschuwing moet worden onderzocht. Hoe hoger de score is, hoe zelfverzekerder Security Center is dat de waarschuwing echte schadelijke activiteiten aangeeft. De betrouwbaarheidsscore bevat een lijst met de belangrijkste redenen waarom de waarschuwing zijn vertrouwensscore heeft ontvangen. De betrouwbaarheidsscore maakt het gemakkelijker voor beveiligingsanalisten om hun reactie op waarschuwingen te prioriteren en de meest urgente aanvallen eerst aan te pakken, waardoor uiteindelijk de hoeveelheid tijd die nodig is om te reageren op aanvallen en inbreuken wordt verminderd.

Ga als het gaat om het bekijken van de betrouwbaarheidsscore:
- Open het mes beveiligingswaarschuwingen in de portal van het beveiligingscentrum.
-  De waarschuwingen en incidenten worden georganiseerd van hoog naar laag, wat betekent dat de meer vertrouwen Security Center is dat een waarschuwing een bedreiging vertegenwoordigt, hoe dichter het is aan de bovenkant van de pagina. 


 ![Betrouwbaarheidsscore][1]

Ga als instellen voor de gegevens die hebben bijgedragen aan het vertrouwen van Security Center in een waarschuwing:
- Bekijk in het beveiligingsalarmblad onder **Vertrouwen**de waarnemingen die hebben bijgedragen aan de betrouwbaarheidsscore en verkrijg inzichten met betrekking tot de waarschuwing. Dit geeft u meer inzicht in de aard van de activiteiten die de waarschuwing hebben veroorzaakt.

  ![Verdachte vertrouwensscore][2]

Gebruik de vertrouwensscore van Security Center om prioriteit te geven aan waarschuwingstriage in uw omgeving. De vertrouwensscore bespaart u tijd en moeite door automatisch waarschuwingen te onderzoeken, best practices en intelligente algoritmen uit de branche toe te passen en als virtuele analist op te treden om te bepalen welke bedreigingen echt zijn en waar u uw aandacht moet richten.


<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
