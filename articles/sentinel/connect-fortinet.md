---
title: Fortinet-gegevens verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Fortinet-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aabc95fcb8d3b32b89bb83f4f6892c3d40b03417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588192"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Fortinet verbinden met Azure Sentinel



In dit artikel wordt uitgelegd hoe u uw Fortinet-toestel verbinden met Azure Sentinel. Met de Fortinet-gegevensconnector u eenvoudig uw Fortinet-logboeken verbinden met Azure Sentinel, dashboards bekijken, aangepaste waarschuwingen maken en onderzoek verbeteren. Het gebruik van Fortinet op Azure Sentinel geeft u meer inzicht in het internetgebruik van uw organisatie en verbetert de beveiligingsmogelijkheden. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Fortinet-logboeken doorsturen naar de Syslog-agent

Configureer Fortinet om Syslog-berichten in CEF-indeling door te sturen naar uw Azure-werkruimte via de Syslog-agent.

1. Open de CLI op uw Fortinet-toestel en voer de volgende opdrachten uit:

        config log syslogd setting
        set format cef
        set port 514
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Vervang het **ip-adres** van de server door het IP-adres van de agent.
    - Stel de **syslogpoort** in op **514** of de poort die op de agent is ingesteld.
    - Als u de CEF-indeling in vroege FortiOS-versies wilt inschakelen, moet u mogelijk de opdrachtset **csv uitschakelen**uitvoeren.
 
   > [!NOTE] 
   > Ga voor meer informatie naar de [fortinet-documentbibliotheek.](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary) Selecteer uw versie en gebruik de **naslaggids** en **naslaginformatie over het logboekbericht**.

1. Als u het relevante schema wilt gebruiken in Azure Monitor `CommonSecurityLog`Log Analytics voor de Fortinet-gebeurtenissen, zoekt u naar .

1. Ga verder naar [STAP 3: Connectiviteit valideren.](connect-cef-verify.md)


## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u Fortinet-apparaten verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.


