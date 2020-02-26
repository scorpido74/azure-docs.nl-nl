---
title: F5-gegevens verbinden met Azure-Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van F5-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 6f33cecca1c67f91d0f2be64ab156f45ee500521
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588260"
---
# <a name="connect-f5-to-azure-sentinel"></a>F5 verbinden met Azure Sentinel

In dit artikel wordt uitgelegd hoe u uw F5-apparaat verbindt met Azure Sentinel. Met de F5 Data Connector kunt u eenvoudig uw F5-logboeken verbinden met Azure Sentinel, om Dash boards weer te geven, aangepaste waarschuwingen te maken en het onderzoek te verbeteren. Als u F5 op Azure Sentinel gebruikt, beschikt u over meer inzicht in het Internet gebruik van uw organisatie en worden de mogelijkheden voor beveiligings bewerkingen verbeterd. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Uw F5 configureren voor het verzenden van CEF-berichten

1. Ga naar [F5 logboek registratie van toepassings beveiligings gebeurtenissen configureren](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)en volg de instructies voor het instellen van externe logboek registratie met behulp van de volgende richt lijnen:
   - Stel het **externe-opslag type** in op **CEF**.
   - Stel het **protocol** in op **TCP**.
   - Stel het **IP-adres** in op het IP-adres van de syslog-server.
   - Stel het **poort nummer** in op **514**of de poort die u voor het gebruik van uw agent hebt ingesteld.
   - U kunt de **maximale grootte van de query teken reeks** instellen op de grootte die u in uw agent hebt ingesteld.

1. Als u het relevante schema in Log Analytics voor de CEF-gebeurtenissen wilt gebruiken, zoekt u naar `CommonSecurityLog`.

1. Ga door naar [stap 3: de verbinding valideren](connect-cef-verify.md).


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u F5 kunt verbinden met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.

