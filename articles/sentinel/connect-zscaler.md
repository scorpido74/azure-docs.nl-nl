---
title: Zscaler-gegevens verbinden met Azure Sentinel | Microsoft Docs
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77587988"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Verbinding maken met Zscaler Internet toegang tot Azure Sentinel

> [!IMPORTANT]
> De Zscaler-gegevens connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel wordt uitgelegd hoe u uw Zscaler Internet Access-apparaat verbindt met Azure Sentinel. Met de Zscaler-gegevens connector kunt u eenvoudig uw Zscaler Internet Access-Logboeken (ZIA) verbinden met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van onderzoek. Door Zscaler te gebruiken in azure Sentinel krijgt u meer inzicht in het Internet gebruik van uw organisatie en worden de mogelijkheden voor beveiligings bewerkingen verbeterd. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Uw Zscaler configureren voor het verzenden van CEF-berichten

1. Op het Zscaler-apparaat moet u deze waarden instellen zodat het apparaat de benodigde logboeken naar de Azure Sentinel syslog-agent verzendt, op basis van de Log Analytics agent. U kunt deze para meters in uw apparaat wijzigen, op voor waarde dat u ze ook wijzigt in de syslog-daemon op de onderverklikker agent van Azure.
    - Protocol = TCP
    - Poort = 514
    - Indeling = CEF
    - IP-adres: Zorg ervoor dat u de CEF-berichten verzendt naar het IP-adres van de virtuele machine die u voor dit doel hebt toegewezen.
 Zie de [implementatie handleiding voor Zscaler en Azure](https://aka.ms/ZscalerCEFInstructions)voor meer informatie.
 
   > [!NOTE]
   > Deze oplossing ondersteunt syslog RFC 3164 of RFC 5424.


1. Zoek naar om het relevante schema in Log Analytics te gebruiken voor de CEF-gebeurtenissen `CommonSecurityLog` .
1. Ga door naar [stap 3: de verbinding valideren](connect-cef-verify.md).


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u verbinding maakt met Zscaler Internet toegang tot Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.


