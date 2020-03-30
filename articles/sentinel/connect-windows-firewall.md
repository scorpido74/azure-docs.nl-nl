---
title: Windows-firewallgegevens verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Windows-firewallgegevens met Azure Sentinel.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588056"
---
# <a name="connect-windows-firewall"></a>Verbinding maken met Windows-firewall



Met de Windows-firewallconnector u eenvoudig uw Windows-firewalls-logboeken aansluiten als ze zijn verbonden met uw Azure Sentinel-werkruimte. Met deze verbinding u dashboards bekijken, aangepaste waarschuwingen maken en het onderzoek verbeteren. Dit geeft u meer inzicht in het netwerk van uw organisatie en verbetert uw beveiligingsmogelijkheden. De oplossing verzamelt Windows-firewallgebeurtenissen van de Windows-machines waarop een Log Analytics-agent is geïnstalleerd. 


> [!NOTE]
> - Gegevens worden opgeslagen in de geografische locatie van de werkruimte waarop u Azure Sentinel uitvoert.
> - Als Azure Sentinel en Azure Security Center worden verzameld in dezelfde werkruimte, hoeft u de Windows Firewall-oplossing niet via deze connector in te schakelen. Als u het toch hebt ingeschakeld, veroorzaakt dit geen dubbele gegevens. 

## <a name="enable-the-connector"></a>De connector inschakelen 

1. Selecteer **gegevensconnectors** in de Azure Sentinel-portal en klik vervolgens op de **Windows-firewalltegel.** 
1.  Als uw Windows-machines zich in Azure bevinden:
    1. Klik **op Agent installeren op de virtuele machine van Azure Windows**.
    1. Selecteer **in** de lijst Virtuele machines de Windows-machine die u naar Azure Sentinel wilt streamen. Zorg ervoor dat dit een Windows VM is.
    1. Klik in het venster dat voor die virtuele machine wordt geopend op **Verbinden**.  
    1. Klik **op Inschakelen** in het venster Windows **firewall-connector.** 

2. Als uw Windows-machine geen Azure VM is:
    1. Klik **op Installatieagent op niet-Azure-machines**.
    1. Selecteer in het venster **Direct-agent** de optie **Windows-agent downloaden (64-bits)** of **Windows-agent downloaden (32-bits).**
    1. Installeer de agent op uw Windows-machine. Kopieer de **werkruimte-id**, **primaire toets**en **secundaire sleutel** en gebruik deze wanneer deze wordt gevraagd tijdens de installatie.

4. Selecteer welke gegevenstypen u wilt streamen.
5. Klik **op Oplossing installeren**.
6. Als u het relevante schema wilt gebruiken in Log Analytics voor de Windows-firewall, zoekt u naar **SecurityEvent**.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan meer dan 20 minuten duren voordat uw logboeken worden weergegeven in Log Analytics. 



## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Windows-firewall verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)

