---
title: Squadra Technologies secRMM-gegevens verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Squadra Technologies secRMM-gegevens met Azure Sentinel.
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
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588107"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Verbind uw Squadra Technologies secRMM-gegevens met Azure Sentinel 

> [!IMPORTANT]
> De squadra Technologies Security Removable Media Manager (secRMM) dataconnector in Azure Sentinel is momenteel in openbare preview.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.


Met de Squadra Technologies secRMM-connector u eenvoudig uw Squadra Technologies secRMM-beveiligingsoplossinglogboeken verbinden met Azure Sentinel. Hiermee u dashboards bekijken, aangepaste waarschuwingen maken en het onderzoek verbeteren. Deze connector geeft u inzicht in verwisselbare opslaggebeurtenissen via USB. Integratie tussen Squadra Technologies secRMM en Azure Sentinel maakt gebruik van REST API.


> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werkruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Squadra Technologies secRMM configureren en verbinden 

Squadra Technologies secRMM kan logboeken rechtstreeks integreren en exporteren naar Azure Sentinel.
1. Klik in de Azure Sentinel-portal op Gegevensconnectoren en selecteer Squadra Technologies secRMM en open de verbindingspagina.

2. Volg de stappen die zijn beschreven in de [onboarding-handleiding van Squadra Technologies voor Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) om Squadra-secRMM-gegevens in Azure Sentinel te krijgen.   


## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat een succesvolle verbinding is gemaakt, worden de gegevens weergegeven in Log Analytics onder CustomLogs secRMM_CL.
Zoek naar secRMM_CL.

## <a name="validate-connectivity"></a>Connectiviteit valideren
Het kan meer dan 20 minuten duren voordat uw logboeken worden weergegeven in Log Analytics. 


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Squadra Technologies secRMM verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.

