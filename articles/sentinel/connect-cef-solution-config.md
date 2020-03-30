---
title: Uw beveiligingsoplossing configureren om CEF-gegevens te verbinden met Azure Sentinel Preview| Microsoft Documenten
description: Meer informatie over het configureren van uw beveiligingsoplossing om CEF-gegevens te verbinden met Azure Sentinel.
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
ms.openlocfilehash: bdb76954b1db8135d8a36d6658bb7fff274ac126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588447"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>STAP 2: Uw beveiligingsoplossing configureren om CEF-berichten te verzenden

In deze stap voert u de nodige configuratiewijzigingen uit op uw beveiligingsoplossing zelf om logboeken naar de CEF-agent te verzenden.

## <a name="configure-a-solution-with-a-connector"></a>Een oplossing configureren met een connector

Als uw beveiligingsoplossing al een bestaande connector heeft, gebruikt u de verbindingsspecifieke instructies als volgt:

- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 F5](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Deep Security van Trend Micro](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Een andere oplossing configureren
Als er geen connector bestaat voor uw specifieke beveiligingsoplossing, gebruikt u de volgende algemene instructies voor het doorsturen van logboeken naar de CEF-agent.

1. Ga naar het specifieke configuratieartikel voor stappen voor het configureren van uw oplossing om CEF-berichten te verzenden. Als uw oplossing niet wordt vermeld, moet u deze waarden op het toestel zo instellen dat het toestel de benodigde logboeken in de benodigde indeling naar de Azure Sentinel Syslog-agent stuurt, op basis van de agent Log Analytics. U deze parameters in uw toestel wijzigen, zolang u ze ook wijzigt in de Syslog-daemon op de Azure Sentinel-agent.
    - Protocol = TCP
    - Poort = 514
    - Opmaak = CEF
    - IP-adres - zorg ervoor dat u de CEF-berichten naar het IP-adres van de virtuele machine die u voor dit doel hebt toegewezen, verzendt.

   > [!NOTE]
   > Deze oplossing ondersteunt Syslog RFC 3164 of RFC 5424.


1. Als u het relevante schema in Log Analytics `CommonSecurityLog`wilt gebruiken voor de CEF-gebeurtenissen, zoekt u naar .

1. Ga verder naar STAP 3: [valideer de connectiviteit.](connect-cef-verify.md)

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u CEF-apparaten verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats.md)

