---
title: Forcepoint DLP verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Forcepoint DLP met Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 3bdb9d2b23ce05929ba5612e0c6a03fe1aab05de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588243"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Verbind uw Forcepoint DLP met Azure Sentinel

> [!IMPORTANT]
> De Forcepoint Data Loss Prevention (DLP)-gegevensconnector in Azure Sentinel bevindt zich momenteel in een openbare preview. Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.



Met de Forcepoint DLP-connector u automatisch DLP-incidentgegevens exporteren naar Azure Sentinel. U het gebruiken om inzicht te krijgen in gebruikersactiviteiten en incidenten met gegevensverlies. Het maakt ook correlaties mogelijk met gegevens uit Azure-workloads en andere feeds en verbetert de bewakingsmogelijkheden met Werkmappen in Azure Sentinel.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werkruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-forcepoint-dlp"></a>Forcepoint DLP configureren en verbinden

Configureer Forcepoint DLP om incidentgegevens in JSON-indeling door te sturen naar uw Azure-werkruimte via REST API, zoals uitgelegd in de [Forcepoint DLP-integratiehandleiding.](https://frcpnt.com/dlp-sentinel)


## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de Forcepoint DLP-connector is ingesteld, worden de gegevens weergegeven in Log Analytics onder CustomLogs **ForcepointDLPEvents_CL**.


Als u het relevante schema wilt gebruiken in Log Analytics voor Forcepoint DLP, zoekt u naar **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan meer dan 20 minuten duren voordat uw logboeken worden weergegeven in Log Analytics.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Forcepoint DLP verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.