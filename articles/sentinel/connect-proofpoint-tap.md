---
title: Verbinding maken met Proofpoint gerichte aanvals beveiliging (tik) gegevens naar Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Proofpoint gerichte aanvals beveiliging (tik)-gegevens naar Azure Sentinel.
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
ms.openlocfilehash: e4185de879fa6136531e6d4c64908befa1d3bc45
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531469"
---
# <a name="connect-your-proofpoint-tap-to-azure-sentinel-with-azure-function"></a>Uw Proofpoint-taak tikken met Azure Sentinel met Azure function

Met de Proofpoint-connector (tikken) kunt u eenvoudig al uw [Proofpoint tikken op](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection) beveiligings oplossingen zoeken met Azure Sentinel, om Dash boards weer te geven, aangepaste waarschuwingen te maken en het onderzoek te verbeteren. Integratie tussen Proofpoint TAP en Azure Sentinel maakt gebruik van Azure Functions om logboek gegevens te verzamelen met behulp van REST API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-proofpoint-tap"></a>Proofpoint-tikken configureren en verbinden

Azure Functions kunnen gebeurtenissen en logboeken rechtstreeks van Proofpoint integreren en ophalen en door sturen naar Azure Sentinel.

1. Klik in de Azure-Sentinel Portal op **Data connectors** en selecteer **Proofpoint Tik** op connector.

1. Selecteer de **pagina connector openen**.

1. Volg de instructies op de pagina **PROOFPOINT Tik** .

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in Log Analytics onder de tabellen **ProofpointTAPMessagesBlocked_CL**, **ProofpointTAPMessagesDelivered_CL**, **ProofpointTAPClicksPermitted_CL** en **ProofpointTAPClicksBlocked_CL** .

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Proofpoint kunt verbinden met Azure Sentinel met behulp van Azure function-apps. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
