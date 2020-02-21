---
title: Azure Security Center voor IoT verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Azure Security Center voor IoT-gegevens naar Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: rkarlin
ms.openlocfilehash: b68745f04a49565090b0f9b14331036f76e5f132
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77501288"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Uw gegevens verbinden met Azure Security Center voor IoT naar Azure Sentinel 


> [!IMPORTANT]
> De Azure Security Center voor IoT data connector bevindt zich momenteel in de open bare preview. Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Gebruik de Azure Security Center voor IoT-connector om al uw Azure Security Center voor IoT-gebeurtenissen in azure Sentinel te streamen. 

## <a name="prerequisites"></a>Vereisten

- **Lees** -en **Schrijf** machtigingen voor de werk ruimte waarop Azure Sentinel wordt geïmplementeerd
- **Azure Security Center voor IOT** moet zijn **ingeschakeld** op uw relevante IOT hub (s)
- **Lees** -en **Schrijf** machtigingen op de **Azure IOT hub** u verbinding wilt maken
- **Lees** -en **Schrijf** machtigingen voor de **resource groep Azure IOT hub**

> [!NOTE]
> Hoewel u de Azure Security Center **Standard** -laag licentie voor uw abonnement moet inschakelen om IOT-resource waarschuwingen naar Azure Sentinel te streamen, hoeft u alleen de Azure Security Center **gratis** laag-licentie voor uw abonnement in te scha kelen om Azure Security Center voor IOT-waarschuwingen in azure Sentinel weer te geven. 

## <a name="connect-to-azure-security-center-for-iot"></a>Verbinding maken met Azure Security Center voor IoT

1. Selecteer in azure Sentinel **Data connectors** en klik vervolgens op de tegel **Azure Security Center voor IOT** .
1. Klik in het rechterdeel venster aan de rechter kant op **connector pagina openen**. 
1. Klik op **verbinding maken**naast elk IOT hub abonnement waarvan u de waarschuwingen en de waarschuwingen van apparaten wilt streamen naar Azure Sentinel. 
    - Als Azure Security Center voor IoT niet is ingeschakeld op die hub, wordt **er een waarschuwings** bericht weer gegeven. Klik op de koppeling **inschakelen** om de service te starten. 
1. U kunt beslissen of u de waarschuwingen van Azure Security Center voor IoT automatisch incidenten wilt genereren in azure Sentinel. Selecteer onder **incidenten maken**de optie **inschakelen** om de standaard analyse regel in te scha kelen om incidenten automatisch te maken op basis van waarschuwingen die zijn gegenereerd in de verbonden beveiligings service. Deze regel kan worden gewijzigd of bewerkt onder **analyse** > **actieve** regels.

> [!NOTE]
> Het kan enige tijd duren voordat de lijst met hubs is gewijzigd. 

## <a name="log-analytics-alert-display"></a>Log Analytics waarschuwings weergave

Als u het relevante schema in Log Analytics wilt gebruiken om de Azure Security Center voor IoT-waarschuwingen weer te geven:

1. Open **logboeken** > **SecurityInsights** > **SecurityAlert**, of zoek naar **SecurityAlert**. 
2. Filter om alleen Azure Security Center voor IoT-waarschuwingen weer te geven met behulp van het volgende kql-filter:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Opmerkingen bij de service

Nadat u verbinding hebt gemaakt met een IoT Hub, zijn de hub-gegevens ongeveer 15 minuten later beschikbaar in azure Sentinel.


## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Azure Security Center voor IoT-gegevens verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
