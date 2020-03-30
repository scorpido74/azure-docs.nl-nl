---
title: Zscaler-gegevens verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Zscaler-gegevens met Azure Sentinel.
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
ms.openlocfilehash: cc784afe5db64ccc4aad13fae7a2fa748e4befa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587988"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Zscaler-internettoegang verbinden met Azure Sentinel

> [!IMPORTANT]
> De Zscaler-gegevensconnector in Azure Sentinel is momenteel in openbare preview.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

In dit artikel wordt uitgelegd hoe u uw Zscaler Internet Access-toestel verbinden met Azure Sentinel. Met de Zscaler-gegevensconnector u eenvoudig uw Zscaler Internet Access -logboeken (ZIA) aansluiten op Azure Sentinel, dashboards bekijken, aangepaste waarschuwingen maken en onderzoek verbeteren. Het gebruik van Zscaler op Azure Sentinel geeft u meer inzicht in het internetgebruik van uw organisatie en verbetert de beveiligingsmogelijkheden. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Uw Zscaler configureren om CEF-berichten te verzenden

1. Op het Zscaler-toestel moet u deze waarden zo instellen dat het toestel de benodigde logboeken in de benodigde indeling naar de Azure Sentinel Syslog-agent stuurt, op basis van de loganalytics-agent. U deze parameters in uw toestel wijzigen, zolang u ze ook wijzigt in de Syslog-daemon op de Azure Sentinel-agent.
    - Protocol = TCP
    - Poort = 514
    - Opmaak = CEF
    - IP-adres - zorg ervoor dat u de CEF-berichten naar het IP-adres van de virtuele machine die u voor dit doel hebt toegewezen, verzendt.
 Zie de [Zscaler- en Azure Sentinel-implementatiehandleiding](https://aka.ms/ZscalerCEFInstructions)voor meer informatie.
 
   > [!NOTE]
   > Deze oplossing ondersteunt Syslog RFC 3164 of RFC 5424.


1. Als u het relevante schema in Log Analytics `CommonSecurityLog`wilt gebruiken voor de CEF-gebeurtenissen, zoekt u naar .
1. Ga verder naar [STAP 3: Connectiviteit valideren.](connect-cef-verify.md)


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Zscaler Internet Access verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.


