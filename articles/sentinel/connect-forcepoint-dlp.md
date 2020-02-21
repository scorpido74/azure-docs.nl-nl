---
title: Force Point DLP verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Force Point DLP met Azure Sentinel.
services: sentinel
author: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: rkarlin
ms.openlocfilehash: bd3940af1553ec65731ed743fd23027403e9ec53
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500950"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Uw Force Point DLP verbinden met Azure Sentinel

> [!IMPORTANT]
> De DLP-gegevens connector (Force Point data verlies preventie) in azure Sentinel is momenteel beschikbaar als open bare preview. Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.



Met de Force Point DLP-connector kunt u automatisch DLP-incident gegevens exporteren naar Azure Sentinel. U kunt deze gebruiken om inzicht te krijgen in de gebruikers activiteiten en incidenten voor gegevens verlies. Het schakelt ook correlaties uit met gegevens van Azure-workloads en andere feeds, en verbetert de bewakings mogelijkheden met werkmappen binnen Azure Sentinel.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-forcepoint-dlp"></a>Force Point DLP configureren en verbinden

Configureer Force Point DLP om incident gegevens in JSON-indeling door te sturen naar uw Azure-werk ruimte via REST API zoals wordt uitgelegd in de [Force Point DLP-integratie handleiding](https://frcpnt.com/dlp-sentinel).


## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de Force Point DLP-connector is ingesteld, worden de gegevens weer gegeven in Log Analytics onder CustomLogs **ForcepointDLPEvents_CL**.


Als u het relevante schema in Log Analytics voor Force Point DLP wilt gebruiken, zoekt u naar **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Force Point DLP kunt verbinden met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.