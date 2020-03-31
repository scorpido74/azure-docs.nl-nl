---
title: De agent implementeren om CEF-gegevens te verbinden met Azure Sentinel Preview| Microsoft Documenten
description: Meer informatie over het implementeren van de agent om CEF-gegevens te verbinden met Azure Sentinel.
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: b0c9335357cb793ea76e1dbe68575f716a50372a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588464"
---
# <a name="step-1-deploy-the-agent"></a>Stap 1: De agent implementeren


In deze stap moet u de Linux-machine selecteren die fungeert als een proxy tussen Azure Sentinel en uw beveiligingsoplossing. U moet een script uitvoeren op de proxy machine die:
- Installeert de Log Analytics-agent en configureert deze zo nodig om naar Syslog-berichten te luisteren.
- Hiermee configureert u de Syslog-daemon om naar Syslog-berichten te luisteren met TCP-poort 514 en stuurt u vervolgens alleen de CEF-berichten door naar de Log Analytics-agent via TCP-poort 25226.
- Hiermee stelt u de Syslog-agent in om de gegevens te verzamelen en deze veilig naar Azure Sentinel te verzenden, waar deze wordt ontleed en verrijkt.
 
## <a name="deploy-the-agent"></a>De agent implementeren
 
1. Klik in de Azure Sentinel-portal op **Gegevensconnectoren** en selecteer **Common Event Format (CEF)** en open de **verbindingspagina**. 

1. Selecteer **onder De Syslog-agent installeren en configureren**uw machinetype, Azure, andere cloud of on-premises. 
   > [!NOTE]
   > Omdat het script in de volgende stap de Log Analytics-agent installeert en de machine verbindt met uw Azure Sentinel-werkruimte, controleert u of deze machine niet is verbonden met een andere werkruimte.
1. U moet verhoogde machtigingen (sudo) op uw machine hebben. Zorg ervoor dat je Python op je machine hebt met de volgende opdracht:`python –version`

1. Voer het volgende script uit op uw proxymachine.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Terwijl het script wordt uitgevoerd, controleert u of u geen foutmeldingen of waarschuwingsberichten ontvangt.

Ga verder naar [STAP 2: Configureer uw beveiligingsoplossing om CEF-berichten door te sturen.](connect-cef-solution-config.md)


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u CEF-apparaten verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats.md)

