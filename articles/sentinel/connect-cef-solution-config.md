---
title: Uw beveiligings oplossing configureren om CEF-gegevens te verbinden met Azure Sentinel preview | Microsoft Docs
description: Meer informatie over het configureren van uw beveiligings oplossing voor het verbinden van CEF-gegevens met Azure Sentinel.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588447"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>STAP 2: uw beveiligings oplossing configureren voor het verzenden van CEF-berichten

In deze stap voert u de nood zakelijke configuratie wijzigingen in uw beveiligings oplossing zelf uit om logboeken te verzenden naar de CEF-agent.

## <a name="configure-a-solution-with-a-connector"></a>Een oplossing met een connector configureren

Als uw beveiligings oplossing al een bestaande connector heeft, gebruikt u de volgende instructies voor de connector:

- [Controle punt](connect-checkpoint.md)
- [Airone](connect-cisco.md)
- [ExtraHop Reveal (x)](connect-extrahop.md)
- [Drukt](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [Eén identiteits beveiliging](connect-one-identity.md)
- [Palo Alto-netwerken](connect-paloalto.md)
- [Trend Micro diepe beveiliging](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Andere oplossingen configureren
Als er geen connector bestaat voor uw specifieke beveiligings oplossing, gebruikt u de volgende algemene instructies voor het door sturen van logboeken naar de CEF-agent.

1. Ga naar het artikel specifieke configuratie voor de stappen voor het configureren van uw oplossing voor het verzenden van CEF-berichten. Als uw oplossing niet wordt weer gegeven, moet u op het apparaat deze waarden instellen, zodat het apparaat de benodigde logboeken naar de Azure Sentinel syslog-agent verzendt, op basis van de Log Analytics agent. U kunt deze para meters in uw apparaat wijzigen, op voor waarde dat u ze ook wijzigt in de syslog-daemon op de onderverklikker agent van Azure.
    - Protocol = TCP
    - Poort = 514
    - Indeling = CEF
    - IP-adres: Zorg ervoor dat u de CEF-berichten verzendt naar het IP-adres van de virtuele machine die u voor dit doel hebt toegewezen.

   > [!NOTE]
   > Deze oplossing ondersteunt syslog RFC 3164 of RFC 5424.


1. Als u het relevante schema in Log Analytics voor de CEF-gebeurtenissen wilt gebruiken, zoekt u naar `CommonSecurityLog`.

1. Ga door naar stap 3: de [verbinding valideren](connect-cef-verify.md).

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u CEF-apparaten verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats.md).

