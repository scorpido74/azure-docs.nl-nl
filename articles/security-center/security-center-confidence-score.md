---
title: Betrouwbaarheids Score in Azure Security Center | Microsoft Docs
description: Security Center genereert betrouwbaarheids scores om uw team te helpen bepalen of een bedreiging authentiek is en hoe u waarschuwingen kunt sorteren en prioriteiten.
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
ms.openlocfilehash: 5b7e53291e10db17f848553b17dae2f84bdb08b7
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558966"
---
# <a name="alert-confidence-score"></a>Betrouwbaarheidsscore waarschuwing 

Azure Security Center biedt u inzicht in de resources die u in azure uitvoert en waarschuwt u wanneer er potentiële problemen worden gedetecteerd. Het volume van waarschuwingen kan lastig zijn voor een afzonderlijk adres van een beveiligings team, en het is nodig om te bepalen welke waarschuwingen moeten worden onderzocht. Het onderzoeken van waarschuwingen kan ingewikkeld zijn en tijdrovende, waardoor sommige waarschuwingen worden genegeerd.

De betrouwbaarheids Score in Security Center kan bijdragen aan uw team sorteren en prioriteiten voor waarschuwingen geven. Security Center past automatisch de best practices van de branche, intelligente algoritmen en processen toe die door analisten worden gebruikt om te bepalen of een bedreiging authentiek is en geeft u duidelijke inzichten in de vorm van een betrouw bare Score.

## <a name="how-the-confidence-score-is-triggered"></a>Hoe de betrouwbaarheids score wordt geactiveerd

Er worden waarschuwingen gegenereerd wanneer er verdachte processen worden gedetecteerd die worden uitgevoerd op uw virtuele machines. Security Center controleert en analyseert deze waarschuwingen op virtuele Windows-machines die in Azure worden uitgevoerd. Het voert geautomatiseerde controles en correlaties uit met behulp van geavanceerde algoritmen voor meerdere entiteiten en gegevens bronnen in de hele organisatie, en al uw Azure-resources, en biedt een betrouwbaarheids Score die een meting vormt van de mate van vertrouwen Security Center of de waarschuwing legitiem is en moet worden onderzocht.

## <a name="understanding-the-confidence-score"></a>Meer informatie over de betrouwbaarheids Score

Het bereik van de betrouwbaarheids score tussen 1 en 100 en vertegenwoordigt de betrouw baarheid Security Center dat de waarschuwing moet worden onderzocht. Hoe hoger de score is, hoe meer vertrouwens Security Center is dat de waarschuwing de legitieme schadelijke activiteiten aangeeft. De betrouwbaarheids Score bevat een lijst van de belangrijkste redenen waarom de waarschuwing de betrouwbaarheids score heeft ontvangen. De betrouwbaarheids Score maakt het gemakkelijker voor beveiligings analisten om op de hoogte te zijn van de reactie op waarschuwingen en de meeste indruk aanvallen eerst te verkorten, waardoor de tijd die nodig is om te reageren op aanvallen en inbreuken te verminderen.

De betrouwbaarheids Score weer geven:
- Open de Blade beveiligings waarschuwingen in de Security Center Portal.
-  De waarschuwingen en incidenten zijn ingedeeld van hoog naar laag, wat betekent dat de meer vertrouwens Security Center is dat een waarschuwing een bedreiging vormt, des te dichter bij de bovenkant van de pagina. 


 ![Betrouwbaarheidsscore][1]

De gegevens weer geven die hebben bijgedragen aan het vertrouwen van het Security Center in een waarschuwing:
- Bekijk in de Blade beveiligings waarschuwing, onder **vertrouwen**, de waarnemingen die hebben bijgedragen aan de betrouwbaarheids Score en krijg inzicht in verband met de waarschuwing. Dit biedt u meer inzicht in de aard van de activiteiten die de waarschuwing hebben veroorzaakt.

  ![Verdachte betrouwbaarheids Score][2]

Gebruik de betrouwbaarheids Score van Security Center om de sorteren van waarschuwingen in uw omgeving te bepalen. De betrouwbaarheids Score bespaart u tijd en moeite door waarschuwingen automatisch te onderzoeken, best practices van de branche en intelligente algoritmen toe te passen en te fungeren als een virtuele analist om te bepalen welke bedreigingen echt zijn en waar u uw aandacht moet richten.


## <a name="next-steps"></a>Volgende stappen
In dit artikel wordt uitgelegd hoe u de betrouwbaarheids Score kunt gebruiken om te bepalen of u waarschuwingen wilt onderzoeken. Zie de volgende onderwerpen voor meer informatie over het Beveiligingscentrum:

* [Azure Security Center FAQ](security-center-faq.md): raadpleeg veelgestelde vragen over het gebruik van de service.
* [Security health monitoring in Azure Security Center](security-center-monitoring.md): ontdek hoe u de status van uw Azure-resources kunt monitoren.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
