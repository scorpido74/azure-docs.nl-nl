---
title: Azure-activiteit gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Azure-activiteit gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 28d5acd80deef193c7d2fea46f682f26abd756aa
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588600"
---
# <a name="connect-data-from-azure-activity-log"></a>Gegevens verbinden vanuit Azure-activiteiten logboek



U kunt Logboeken van [Azure-activiteiten logboek](../azure-monitor/platform/platform-logs-overview.md) streamen naar Azure Sentinel met één klik. Het activiteiten logboek is een abonnements logboek dat inzicht biedt in gebeurtenissen op abonnements niveau die zich hebben voorgedaan in Azure. Dit omvat een reeks gegevens, van Azure Resource Manager operationele gegevens naar updates op Service Health-gebeurtenissen. Met het activiteiten logboek kunt u de ' wat, wie en wanneer ' bepalen voor elke schrijf bewerking (PUT, POST, DELETE) die is gemaakt op de resources in uw abonnement. U kunt ook de status van de bewerking en andere relevante eigenschappen begrijpen. Het activiteiten logboek bevat geen lees bewerkingen (GET) of bewerkingen voor resources die gebruikmaken van het klassieke/' RDFE-model '. 


## <a name="prerequisites"></a>Vereisten

- Gebruiker met Inzender machtigingen voor Log Analytics werk ruimte 


## <a name="connect-to-azure-activity-log"></a>Verbinding maken met Azure-activiteiten logboek

1. Selecteer in azure Sentinel **Data connectors** en klik vervolgens op de tegel **Azure Activity Log** .

2. Selecteer in het deel venster activiteiten logboek van Azure de abonnementen die u wilt streamen naar Azure Sentinel. 

3. Klik op **Verbinding maken**.

4. Als u het relevante schema in Log Analytics voor de Azure-activiteiten waarschuwingen wilt gebruiken, zoekt u naar **AzureActivity**.


 

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Azure-activiteiten logboek verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
