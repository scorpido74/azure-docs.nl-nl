---
title: NIOS-gegevens (Infoblox Network Identity Operating System) verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Infoblox-gegevens (NIOS) voor netwerk identiteiten aan Azure Sentinel.
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
ms.openlocfilehash: ed4f2d769dbda3dec7b353fddfd1e5e0f3d00f9b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531638"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>Uw Infoblox NIOS verbinden met Azure Sentinel

In dit artikel wordt uitgelegd hoe u uw [Nios-apparaat (Infoblox Network Identity Operating System)](https://www.infoblox.com/glossary/network-identity-operating-system-nios/) verbindt met Azure Sentinel. Met de Infoblox NIOS Data Connector kunt u eenvoudig uw Infoblox-logboeken verbinden met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van het onderzoek. Integratie tussen Infoblox NIOS en Azure Sentinel maakt gebruik van syslog.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>Infoblox-logboeken door sturen naar de syslog-agent  

Infoblox configureren voor het door sturen van syslog-berichten naar uw Azure-werk ruimte via de syslog-agent.

1. Klik in de Azure-Sentinel-Portal op **Data connectors** en selecteer **Infoblox Nios** -connector.

1. Selecteer de **pagina connector openen**.

1. Volg de instructies op de pagina **INFOBLOX Nios** .

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in Log Analytics onder syslog.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Infoblox NIOS verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.