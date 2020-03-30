---
title: Cisco-gegevens verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Cisco-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 264dab4fabc890ffe03d911f8ee3c08ef51375d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588396"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Cisco ASA verbinden met Azure Sentinel



In dit artikel wordt uitgelegd hoe u uw Cisco ASA-toestel verbinden met Azure Sentinel. Met de Cisco ASA-gegevensconnector u eenvoudig uw Cisco ASA-logboeken verbinden met Azure Sentinel, dashboards bekijken, aangepaste waarschuwingen maken en onderzoek verbeteren. Het gebruik van Cisco ASA op Azure Sentinel geeft u meer inzicht in het internetgebruik van uw organisatie en verbetert de beveiligingsmogelijkheden. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Cisco ASA-logboeken doorsturen naar de Syslog-agent

Cisco ASA ondersteunt geen CEF, dus de logboeken worden verzonden als Syslog en de Azure Sentinel-agent weet ze te ontwijken alsof het CEF-logboeken zijn. Configureer Cisco ASA om Syslog-berichten naar uw Azure-werkruimte door te sturen via de Syslog-agent:

1. Ga naar [Syslog-berichten verzenden naar een externe Syslog-server](https://aka.ms/asi-syslog-cisco-forwarding)en volg de instructies om de verbinding in te stellen. Gebruik deze parameters wanneer u daarom wordt gevraagd:
    - Stel **de poort** in op 514 of de poort die u in de agent instelt.
    - Stel **syslog_ip** in op het IP-adres van de agent.

1. Als u het relevante schema in Log Analytics `CommonSecurityLog`wilt gebruiken voor de Cisco-gebeurtenissen, zoekt u naar .

1. Ga verder naar [STAP 3: Connectiviteit valideren.](connect-cef-verify.md)




## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Cisco ASA-apparaten koppelen aan Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.


