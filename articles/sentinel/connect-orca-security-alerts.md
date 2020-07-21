---
title: Orca-waarschuwingen verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van de Orca-beveiligings waarschuwings gegevens met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van het onderzoek.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 1f2f67281079142f0c4fe4985738dc48332a107d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531710"
---
# <a name="connect-your-orca-alerts-to-azure-sentinel"></a>Uw Orca-waarschuwingen verbinden met Azure Sentinel 

Met de Orca Security Alerts-connector kunt u eenvoudig waarschuwingen voor beveiligings oplossingen van uw [Orca](https://orca.security/) naar Azure-Sentinel brengen, zodat u ze in werkmappen kunt bekijken, ze met behulp van een aangepaste waarschuwing en deze op te nemen om het onderzoek te verbeteren. Integratie tussen Orca-beveiligings waarschuwingen en Azure-Sentinel maakt gebruik van REST API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-orca-security-alerts"></a>Beveiligings waarschuwingen van Orca configureren en verbinden

Orca-beveiligings waarschuwingen kunnen Logboeken rechtstreeks integreren en exporteren naar Azure Sentinel.

1. Klik in de Azure-Sentinel Portal op **Data connectors** en selecteer **Orca Security Alerts** en **Open vervolgens connector-pagina**.

2. Raadpleeg https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration voor het volt ooien van de integratie van het Orca-platform.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in Log Analytics onder **CustomLogs** in de tabel **OrcaAlerts_CL** .
Als u het relevante schema in Log Analytics voor de Orca-waarschuwingen wilt gebruiken, zoekt u naar `OrcaAlerts_CL` .

## <a name="validate-connectivity"></a>Connectiviteit valideren
Het kan 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u de beveiligings waarschuwingen van Orca kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.

