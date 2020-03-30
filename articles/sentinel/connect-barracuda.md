---
title: Barracuda-gegevens verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Barracuda-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 4e87bb57e6bdfea6307a166383da9dea187eea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588481"
---
# <a name="connect-your-barracuda-appliance"></a>Sluit uw Barracuda-toestel aan 



Met de WAF-connector (Barracuda Web Application Firewall) u eenvoudig uw Barracuda-logboeken verbinden met uw Azure Sentinel, dashboards bekijken, aangepaste waarschuwingen maken en onderzoek verbeteren. Dit geeft u meer inzicht in het netwerk van uw organisatie en verbetert uw beveiligingsmogelijkheden. Azure Sentinel maakt gebruik van de native integratie tussen **Barracuda** en Log Analytics-agent om naadloze integratie te bieden. 


> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werkruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-barracuda-waf"></a>Barracuda WAF configureren en verbinden
Barracuda Web Application Firewall kan logboeken rechtstreeks integreren en exporteren naar Azure Sentinel via de Log Analytics-agent.
1. Ga naar [Barracuda WAF-configuratiestroom](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)en volg de instructies voor het instellen van de verbinding met behulp van de volgende parameters:
    - **Werkruimte-id:** kopieer de waarde van uw werkruimte-id vanaf de azure Sentinel Barracuda-connectorpagina.
    - **Primaire sleutel:** kopieer de waarde van uw primaire sleutel vanaf de Azure Sentinel Barracuda-connectorpagina.
1. Als u het relevante schema wilt gebruiken in Log Analytics voor de Barracuda-gebeurtenissen, zoekt u naar **CommonSecurityLog** en **barracuda_CL**.


## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan meer dan 20 minuten duren voordat uw logboeken worden weergegeven in Log Analytics. 



## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Barracuda-apparaten verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.


