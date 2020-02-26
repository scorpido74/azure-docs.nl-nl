---
title: Windows Firewall-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over hoe u Windows Firewall-gegevens verbindt met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5d2f68261143c3fc5bbcda0b739af17251eeee63
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588056"
---
# <a name="connect-windows-firewall"></a>Verbinding maken met Windows-firewall



Met de Windows Firewall-connector kunt u eenvoudig uw Windows firewalls-logboeken verbinden als ze zijn verbonden met uw Azure Sentinel-werk ruimte. Met deze verbinding kunt u Dash boards weer geven, aangepaste waarschuwingen maken en het onderzoek verbeteren. Dit geeft u meer inzicht in het netwerk van uw organisatie en verbetert de mogelijkheden van beveiligings bewerkingen. De oplossing verzamelt Windows Firewall-gebeurtenissen van de Windows-computers waarop een Log Analytics-agent is geïnstalleerd. 


> [!NOTE]
> - Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.
> - Als Azure Sentinel en Azure Security Center worden verzameld in dezelfde werk ruimte, is het niet nodig om de Windows Firewall oplossing via deze connector in te scha kelen. Als u dit toch hebt ingeschakeld, worden er geen dubbele gegevens weer gegeven. 

## <a name="enable-the-connector"></a>De connector inschakelen 

1. Selecteer in de Azure-Sentinel-Portal de optie **gegevens connectors** en klik vervolgens op de tegel **Windows Firewall** . 
1.  Als uw Windows-computers zich in azure bevinden:
    1. Klik op **agent installeren op virtuele machine van Azure Windows**.
    1. Selecteer in de lijst **virtuele machines** de Windows-machine die u wilt streamen naar Azure Sentinel. Zorg ervoor dat dit een Windows-VM is.
    1. Klik in het venster dat wordt geopend voor die VM op **verbinden**.  
    1. Klik op **inschakelen** in het venster **Windows Firewall connector** . 

2. Als uw Windows-computer geen Azure-VM is:
    1. Klik op **agent installeren op niet-Azure-machines**.
    1. Selecteer in het venster **direct agent** de optie **Windows-agent (64 bits) downloaden** of **Windows-agent (32 bits) downloaden**.
    1. Installeer de agent op de Windows-computer. Kopieer de **werk ruimte-id**, **primaire sleutel**en **secundaire sleutel** en gebruik deze wanneer u hierom wordt gevraagd tijdens de installatie.

4. Selecteer welke gegevens typen u wilt streamen.
5. Klik op **oplossing installeren**.
6. Als u het relevante schema in Log Analytics voor Windows Firewall wilt gebruiken, zoekt u naar **SecurityEvent**.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 



## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Windows Firewall verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).

