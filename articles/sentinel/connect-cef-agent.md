---
title: De agent implementeren om CEF-gegevens te verbinden met Azure Sentinel preview | Microsoft Docs
description: Meer informatie over het implementeren van de agent voor het verbinden van CEF-gegevens met Azure Sentinel.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588464"
---
# <a name="step-1-deploy-the-agent"></a>Stap 1: de agent implementeren


In deze stap moet u de Linux-computer selecteren die zal fungeren als een proxy tussen Azure Sentinel en uw beveiligings oplossing. U moet een script uitvoeren op de proxy computer die:
- Installeert de Log Analytics agent en configureert deze naar behoefte om syslog-berichten te belui Steren.
- Hiermee configureert u de syslog-daemon om te Luis teren naar syslog-berichten via TCP-poort 514 en stuurt u vervolgens alleen de CEF-berichten naar de Log Analytics-agent via TCP-poort 25226.
- Hiermee stelt u de syslog-agent in om de gegevens te verzamelen en veilig te verzenden naar Azure Sentinel, waar ze worden geparseerd en verrijkt.
 
## <a name="deploy-the-agent"></a>De agent implementeren
 
1. Klik in de Azure-Sentinel-Portal op **gegevens connectors** en selecteer **algemene gebeurtenis indeling (CEF)** en open vervolgens de **pagina connector**. 

1. Onder **installeren en configureren van de syslog-agent**selecteert u het type computer, azure, andere Cloud of on-premises. 
   > [!NOTE]
   > Omdat met het script in de volgende stap de Log Analytics-agent wordt geïnstalleerd en de computer wordt verbonden met uw Azure-Sentinel-werk ruimte, moet u ervoor zorgen dat deze computer niet is verbonden met een andere werk ruimte.
1. U moet over verhoogde machtigingen (sudo) beschikken op uw computer. Zorg ervoor dat u python op uw computer hebt met behulp van de volgende opdracht: `python –version`

1. Voer het volgende script uit op de proxy computer.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Zorg ervoor dat het script wordt uitgevoerd en controleer of er geen fout-of waarschuwings berichten worden weer gegeven.

Ga verder met [stap 2: uw beveiligings oplossing configureren voor het door sturen van CEF-berichten](connect-cef-solution-config.md) .


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u CEF-apparaten verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats.md).

