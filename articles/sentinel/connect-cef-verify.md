---
title: Connectiviteit met Azure Sentinel valideren | Microsoft Docs
description: Valideer de connectiviteit van uw beveiligings oplossing om ervoor te zorgen dat CEF-berichten worden doorgestuurd naar Azure Sentinel.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588430"
---
# <a name="step-3-validate-connectivity"></a>STAP 3: connectiviteit valideren



Nadat u de agent hebt geïmplementeerd en uw beveiligings oplossing hebt geconfigureerd voor het door sturen van CEF-berichten, kunt u in dit artikel lezen hoe u de connectiviteit tussen Azure Sentinel en uw beveiligings oplossing kunt controleren. 

## <a name="how-to-validate-connectivity"></a>Connectiviteit valideren

1. Open Log Analytics om ervoor te zorgen dat Logboeken worden ontvangen met behulp van het CommonSecurityLog-schema.<br> Het kan 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 

1. Voordat u het script uitvoert, wordt u aangeraden berichten te verzenden vanuit uw beveiligings oplossing om er zeker van te zijn dat ze worden doorgestuurd naar de syslog-proxy computer die u hebt geconfigureerd. 
1. U moet over verhoogde machtigingen (sudo) beschikken op uw computer. Zorg ervoor dat u python op uw computer hebt met behulp van de volgende opdracht: `python –version`
1. Voer het volgende script uit om de connectiviteit tussen de agent, Azure Sentinel en uw beveiligings oplossing te controleren. Er wordt gecontroleerd of het door sturen van daemon correct is geconfigureerd, luistert naar de juiste poorten en dat er geen communicatie tussen de daemon en de Log Analytics agent wordt geblokkeerd. Het script verzendt ook beeldberichten TestCommonEventFormat om de end-to-end-connectiviteit te controleren. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u CEF-apparaten verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.

