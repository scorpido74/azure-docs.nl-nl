---
title: Forcepoint-producten verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Forcepoint-producten met Azure Sentinel.
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
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588226"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Verbind uw Forcepoint-producten met Azure Sentinel

> [!IMPORTANT]
> De gegevensconnector Forcepoint-producten in Azure Sentinel is momenteel in openbare preview. Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.


In dit artikel wordt uitgelegd hoe u uw Forcepoint-producten koppelen aan Azure Sentinel. 

Met de Forcepoint-gegevensconnectors u Forcepoint Cloud Access Security Broker- en Forcepoint Next Generation Firewall-logboeken verbinden met Azure Sentinel. Op deze manier u automatisch door de gebruiker gedefinieerde logboeken in realtime exporteren naar Azure Sentinel. De connector geeft u verrijkt inzicht in gebruikersactiviteiten die door Forcepoint-producten zijn geregistreerd. Het maakt ook verdere correlatie mogelijk met gegevens van Azure-workloads en andere feeds en verbetert de bewakingsmogelijkheden met Werkmappen in Azure Sentinel.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werkruimte waarop u Azure Sentinel uitvoert.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Forcepoint-productlogboeken doorsturen naar de Syslog-agent 

Configureer het Forcepoint-product om Syslog-berichten in CEF-indeling door te sturen naar uw Azure-werkruimte via de Syslog-agent.

1. Stel het Forcepoint-product in op Azure Sentinel-integratie zoals beschreven in de volgende installatiehandleidingen:
 - [Forcepoint CASB-integratiegids](https://frcpnt.com/casb-sentinel)
 - [Forcepoint NGFW-integratiegids](https://frcpnt.com/ngfw-sentinel)

2. Zoek naar CommonSecurityLog om het relevante schema in Log Analytics met devicevendor naam te gebruiken bevat 'Forcepoint'. 

3. Ga verder naar [STAP 3: Connectiviteit valideren.](connect-cef-verify.md)



## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Forcepoint-producten koppelen aan Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)

- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)

- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.