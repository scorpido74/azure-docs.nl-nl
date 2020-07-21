---
title: Verbinding met Pulse beveiligde gegevens maken met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Pulse-beveiligde gegevens met Azure Sentinel.
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
ms.openlocfilehash: 01d41b20c543262280e215cf4e31fd0cccce877f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531462"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>Verbinding maken met uw Pulse verbinding beveiligen met Azure Sentinel

In dit artikel wordt uitgelegd hoe u verbinding kunt maken tussen uw [Pulse en beveiligde](https://www.pulsesecure.net/products/pulse-connect-secure/) apparaten met Azure Sentinel. Met de Pulse Connected Data Connector kunt u eenvoudig verbinding maken met beveiligde logboeken met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van onderzoek. Integratie tussen Pulse Connect beveiligde en Azure Sentinel maakt gebruik van syslog.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="forward-pulse-connect-secure-logs-to-the-syslog-agent"></a>Pulse-verbinding met beveiligde logboeken door sturen naar de syslog-agent  

Pulse-verbinding beveiligen configureren voor het door sturen van syslog-berichten naar uw Azure-werk ruimte via de syslog-agent.

1. Klik in de Azure-Sentinel-Portal op **gegevens connectors** en selecteer **Pulse Connect Secure** connector.

1. Selecteer de **pagina connector openen**.

1. Volg de instructies op de pagina **Pulse Connect Secure** .

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in Log Analytics onder syslog.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u verbinding maken met Pulse Connect voor Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.