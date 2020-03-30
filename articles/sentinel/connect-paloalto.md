---
title: Palo Alto Networks-gegevens verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Palo Alto Networks-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: a79b7a1448e1decb377aa0072261df068c366567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588124"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Palo Alto Networks verbinden met Azure Sentinel



In dit artikel wordt uitgelegd hoe u uw Palo Alto Networks-toestel verbinden met Azure Sentinel. Met de Palo Alto Networks-gegevensconnector u eenvoudig uw Palo Alto Networks-logboeken verbinden met Azure Sentinel, dashboards bekijken, aangepaste waarschuwingen maken en onderzoek verbeteren. Het gebruik van Palo Alto Networks op Azure Sentinel geeft u meer inzicht in het internetgebruik van uw organisatie en verbetert de beveiligingsmogelijkheden. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Doorsturen Palo Alto Networks logt uit naar de Syslog-agent

Configureer Palo Alto Networks om Syslog-berichten in CEF-indeling door te sturen naar uw Azure-werkruimte via de Syslog-agent:
1.  Ga naar [CEF-configuratiehandleidingen (Common Event Format)](https://docs.paloaltonetworks.com/resources/cef) en download de pdf voor het type toestel. Volg alle instructies in de gids om uw Palo Alto Networks-toestel in te stellen om CEF-evenementen te verzamelen. 

1.  Ga naar [Syslog-bewaking configureren](https://aka.ms/asi-syslog-paloalto-forwarding) en volg stap 2 en 3 om CEF-gebeurtenisforwarding te configureren van uw Palo Alto Networks-toestel naar Azure Sentinel.

    1. Zorg ervoor dat u de **syslog-serverindeling** instelt op **BSD.**

       > [!NOTE]
       > De kopieer-/plakbewerkingen vanuit de PDF kunnen de tekst wijzigen en willekeurige tekens invoegen. Kopieer de tekst naar een editor om dit te voorkomen en verwijder alle tekens die de logboekindeling kunnen verbreken voordat u deze plakt, zoals u in dit voorbeeld zien.
 
        ![CEF-tekstkopieerprobleem](./media/connect-cef/paloalto-text-prob1.png)

1. Als u het relevante schema wilt gebruiken in Log Analytics voor de gebeurtenissen van Palo Alto Networks, zoekt u **naar CommonSecurityLog**.

1. Ga verder naar [STAP 3: Connectiviteit valideren.](connect-cef-verify.md)




## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Palo Alto Networks-apparaten verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.


