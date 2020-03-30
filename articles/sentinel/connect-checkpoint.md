---
title: Schakel controlepuntgegevens aan voor Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Check Point-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 70836ec557eff1be035d92e8e7db30a882e05fc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588413"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Schakel controlepunt aan voor Azure Sentinel



In dit artikel wordt uitgelegd hoe u uw Check Point-toestel aansluit op Azure Sentinel. Met de gegevensconnector Van Check Point u eenvoudig uw Check Point-logboeken verbinden met Azure Sentinel, dashboards bekijken, aangepaste waarschuwingen maken en onderzoek verbeteren. Als u Check Point op Azure Sentinel gebruikt, krijgt u meer inzicht in het internetgebruik van uw organisatie en worden de beveiligingsmogelijkheden van uw organisatie versterkt. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Check Point-logboeken doorsturen naar de Syslog-agent

Configureer uw Check Point-toestel om Syslog-berichten in CEF-indeling door te sturen naar uw Azure-werkruimte via de Syslog-agent.

1. Ga naar [Check Point Log Export](https://aka.ms/asi-syslog-checkpoint-forwarding).
1. Schuif omlaag naar **Basisimplementatie** en volg de instructies om de verbinding in te stellen met de volgende richtlijnen:
   - Stel de **Syslog-poort** in op **514** of de poort die u op de agent hebt ingesteld.
     - Vervang de **naam** en **het IP-adres van** de doelserver in de CLI door de naam en het IP-adres van de Syslog-agent.
     - Stel de indeling in op **CEF**.
1. Als u versie R77.30 of R80.10 gebruikt, schuift u omhoog naar **Installaties** en volgt u de instructies om een logboekexporteur voor uw versie te installeren.
1. Ga verder naar [STAP 3: Connectiviteit valideren.](connect-cef-verify.md)
 

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Check Point-apparaten verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- [Connectiviteit valideren](connect-cef-verify.md).
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.


