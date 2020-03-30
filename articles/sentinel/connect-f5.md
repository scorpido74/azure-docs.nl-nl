---
title: F5-gegevens verbinden met Azure Sentinel| Microsoft Documenten
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588260"
---
# <a name="connect-f5-to-azure-sentinel"></a>F5 verbinden met Azure Sentinel

In dit artikel wordt uitgelegd hoe u uw F5-toestel aansluit op Azure Sentinel. Met de F5-gegevensconnector u uw F5-logboeken eenvoudig verbinden met Azure Sentinel, dashboards bekijken, aangepaste waarschuwingen maken en onderzoek verbeteren. Het gebruik van F5 op Azure Sentinel geeft u meer inzicht in het internetgebruik van uw organisatie en verbetert de beveiligingsmogelijkheden. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Uw F5 configureren om CEF-berichten te verzenden

1. Ga naar [F5 Het configureren van toepassingsbeveiligingsgebeurtenislogboekregistratie](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)en volg de instructies voor het instellen van logboekregistratie op afstand met behulp van de volgende richtlijnen:
   - Stel het **type externe opslag in** op **CEF**.
   - Stel het protocol in **op** **TCP**.
   - Stel het **IP-adres** in op het IP-adres van de Syslog-server.
   - Stel het **poortnummer** in op **514**of de poort die u uw agent wilt gebruiken.
   - U de **maximale querytekenreeksgrootte** instellen op de grootte die u in uw agent hebt ingesteld.

1. Als u het relevante schema in Log Analytics `CommonSecurityLog`wilt gebruiken voor de CEF-gebeurtenissen, zoekt u naar .

1. Ga verder naar [STAP 3: Connectiviteit valideren.](connect-cef-verify.md)


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u F5 verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.

