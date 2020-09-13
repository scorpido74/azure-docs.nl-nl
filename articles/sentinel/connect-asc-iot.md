---
title: Verbinding maken tussen Azure Defender voor IoT en Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Azure Defender (voorheen Azure Security Center) voor IoT-gegevens naar Azure Sentinel.
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
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: 822d0c742bbd54b5bab0c69e82652743584a0696
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89659622"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Uw gegevens verbinden met Azure Defender (voorheen Azure Security Center) voor een IoT-naar-Azure-Sentinel 


> [!IMPORTANT]
> De Azure Defender voor IoT-gegevens connector bevindt zich momenteel in de open bare preview. Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Gebruik de Azure Defender voor IoT-connector om al uw Azure Defender voor IoT-gebeurtenissen in azure Sentinel te streamen. 

## <a name="prerequisites"></a>Vereisten

- **Lees** -en **Schrijf** machtigingen voor de werk ruimte waarop Azure Sentinel wordt geïmplementeerd
- **Azure Defender voor IOT** moet zijn **ingeschakeld** op uw relevante IOT hub (s)
- **Lees** -en **Schrijf** machtigingen op de **Azure IOT hub** u verbinding wilt maken
- **Lees** -en **Schrijf** machtigingen voor de **resource groep Azure IOT hub**

## <a name="connect-to-azure-defender-for-iot"></a>Verbinding maken met Azure Defender voor IoT

1. Selecteer in azure Sentinel **Data connectors** en selecteer vervolgens **Azure Defender voor IOT** (kan nog steeds Azure Security Center voor IOT worden genoemd) in de galerie.
1. Klik in het rechterdeel venster aan de rechter kant op **connector pagina openen**. 
1. Klik op **verbinding maken**naast elk IOT hub abonnement waarvan u de waarschuwingen en de waarschuwingen van apparaten wilt streamen naar Azure Sentinel. 
    - Als Azure Defender voor IoT niet is ingeschakeld op die hub, wordt **er een waarschuwings** bericht weer gegeven. Klik op de koppeling **inschakelen** om de service te starten. 
1. U kunt beslissen of u wilt dat de waarschuwingen van Azure Defender voor IoT automatisch incidenten genereren in azure Sentinel. Selecteer onder **incidenten maken**de optie **inschakelen** om de standaard analyse regel in te scha kelen om incidenten automatisch te maken op basis van waarschuwingen die zijn gegenereerd in de verbonden beveiligings service. Deze regel kan worden gewijzigd of bewerkt onder **Analytics**  >  **actieve** regels voor analyse.

> [!NOTE]
> Het kan enige tijd duren voordat de lijst met hubs is gewijzigd. 

## <a name="log-analytics-alert-display"></a>Log Analytics waarschuwings weergave

Het relevante schema in Log Analytics gebruiken om de Azure Defender voor IoT-waarschuwingen weer te geven:

1. Open **Logboeken**  >  **SecurityInsights**  >  **SecurityAlert**of zoek naar **SecurityAlert**. 
2. Filter om alleen Azure Defender voor IoT gegenereerde waarschuwingen weer te geven met behulp van het volgende kql-filter:

```kusto
SecurityAlert | where ProductName == "Azure Defender for IoT"
``` 

### <a name="service-notes"></a>Opmerkingen bij de service

Nadat u verbinding hebt gemaakt met een IoT Hub, zijn de hub-gegevens ongeveer 15 minuten later beschikbaar in azure Sentinel.


## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Azure Defender voor IoT-gegevens verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
