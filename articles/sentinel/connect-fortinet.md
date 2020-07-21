---
title: Fortinet-gegevens koppelen aan Azure-Sentinel | Microsoft Docs
description: Verbind uw Fortinet-apparaat met Azure Sentinel voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van het onderzoek. 
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
ms.openlocfilehash: 8aa8599cbaab6af00d7b4122b94c9e24870881f3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511327"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Fortinet verbinden met Azure-Sentinel



In dit artikel wordt uitgelegd hoe u uw Fortinet-apparaat verbindt met Azure Sentinel. Met de Fortinet Data Connector kunt u eenvoudig uw Fortinet-logboeken verbinden met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van onderzoek. Door het gebruik van Fortinet op Azure-Sentinel krijgt u meer inzicht in het Internet gebruik van uw organisatie en worden de mogelijkheden voor beveiligings bewerkingen verbeterd. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>De Fortinet-logboeken door sturen naar de syslog-agent

Configureer Fortinet voor het door sturen van syslog-berichten in de CEF-indeling naar uw Azure-werk ruimte via de syslog-agent.

1. Open de CLI op uw Fortinet-apparaat en voer de volgende opdrachten uit:

    ```console
    config log syslogd setting
    set format cef
    set port 514
    set server <ip_address_of_Receiver>
    set status enable
    end
    ```

    - Vervang het IP **-adres** van de server door het IP-adres van de agent.
    - Stel de **syslog-poort** in op **514** of de poort die is ingesteld op de agent.
    - Als u de CEF-indeling in vroege FortiOS-versies wilt inschakelen, moet u mogelijk de opdracht set **CSV Disable**uitvoeren.
 
   > [!NOTE] 
   > Ga naar de [document bibliotheek van Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)voor meer informatie. Selecteer uw versie en gebruik de naslag **handleiding** en het **logboek bericht**.

1. Als u het relevante schema in Azure Monitor Log Analytics voor de Fortinet-gebeurtenissen wilt gebruiken, zoekt u naar `CommonSecurityLog` .

1. Ga door naar [stap 3: de verbinding valideren](connect-cef-verify.md).


## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u Fortinet-apparaten verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.


