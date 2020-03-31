---
title: F5 BIG-IP-gegevens verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van F5 BIG-IP-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: ae361c74b261bdd6a5673040f868392282b573ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588277"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Sluit uw F5 BIG-IP-toestel aan 

> [!IMPORTANT]
> De F5 BIG-IP-gegevensconnector in Azure Sentinel is momenteel in openbare preview.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Met de F5 BIG-IP-connector u eenvoudig al uw F5 BIG-IP-logboeken verbinden met Azure Sentinel, werkmappen bekijken, aangepaste waarschuwingen maken en onderzoek verbeteren. Dit geeft u meer inzicht in het netwerk van uw organisatie en verbetert uw beveiligingsmogelijkheden. Integratie tussen F5 BIG-IP en Azure Sentinel maakt gebruik van REST API.


> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werkruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-f5-big-ip"></a>F5 BIG-IP configureren en verbinden 

F5 BIG-IP kan logboeken rechtstreeks integreren en exporteren naar Azure Sentinel.

1. Klik in de Azure Sentinel-portal op **Gegevensconnectoren** en selecteer **F5 BIG-IP** en open de **verbindingspagina**. 
1. Om uw F5 BIG-IP aan te sluiten, moet u een JSON-verklaring plaatsen op het API-eindpunt van het systeem. Zie [De F5 BIG-IP integreren met Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)voor instructies over hoe u dit doen.
8. Kopieer op de pagina F5 BIG-IP-connector de werkruimte-id en primaire sleutel en plak ze zoals aangegeven onder [Streaming-gegevens naar Azure Log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics).
1. Nadat u de F5 BIG-IP-instructies hebt voltooid, ziet u op de azure sentinel-connectorpagina de verbonden gegevenstypen.
1. Als u het relevante schema in Log Analytics wilt gebruiken voor de F5 BIG-IP-gebeurtenissen, zoekt u naar **F5Telemetry_LTM_CL,** **F5Telemetry_system_CL**en **F5Telemetry_ASM_CL**.


## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan meer dan 20 minuten duren voordat uw logboeken worden weergegeven in Log Analytics. 



## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u F5 BIG-IP verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.


