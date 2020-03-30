---
title: Connectiviteit met Azure Sentinel valideren| Microsoft Documenten
description: Valideer de connectiviteit van uw beveiligingsoplossing om ervoor te zorgen dat CEF-berichten worden doorgestuurd naar Azure Sentinel.
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
ms.openlocfilehash: e224f6d5cfd82dfc6cb1ce107d111ee0e031247b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588430"
---
# <a name="step-3-validate-connectivity"></a>STAP 3: Connectiviteit valideren



Nadat u de agent hebt geïmplementeerd en uw beveiligingsoplossing hebt geconfigureerd om CEF-berichten door te sturen, gebruikt u dit artikel om te begrijpen hoe u de verbinding tussen Azure Sentinel en uw beveiligingsoplossing verifiëren. 

## <a name="how-to-validate-connectivity"></a>Connectiviteit valideren

1. Open Log Analytics om ervoor te zorgen dat logboeken worden ontvangen met behulp van het CommonSecurityLog-schema.<br> Het kan meer dan 20 minuten duren voordat uw logboeken worden weergegeven in Log Analytics. 

1. Voordat u het script uitvoert, raden we u aan berichten vanuit uw beveiligingsoplossing te verzenden om ervoor te zorgen dat ze worden doorgestuurd naar de syslog-proxymachine die u hebt geconfigureerd. 
1. U moet verhoogde machtigingen (sudo) op uw machine hebben. Zorg ervoor dat je Python op je machine hebt met de volgende opdracht:`python –version`
1. Voer het volgende script uit om de connectiviteit tussen de agent, Azure Sentinel en uw beveiligingsoplossing te controleren. Het controleert of de daemon forwarding goed is geconfigureerd, luistert op de juiste poorten, en dat niets de communicatie tussen de daemon en de Log Analytics-agent blokkeert. Het script stuurt ook mock-berichten 'TestCommonEventFormat' om end-to-end connectiviteit te controleren. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u CEF-apparaten verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.

