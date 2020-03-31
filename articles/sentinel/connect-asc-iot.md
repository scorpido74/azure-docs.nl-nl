---
title: Azure Security Center voor IoT verbinden met Azure Sentinel | Microsoft Documenten
description: Meer informatie over het verbinden van Azure Security Center voor IoT-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 3af51110a4c4604444573f62be65077c786db606
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588634"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Uw gegevens van Azure Security Center voor IoT verbinden met Azure Sentinel 


> [!IMPORTANT]
> De Azure Security Center for IoT-gegevensconnector bevindt zich momenteel in een openbare preview. Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Gebruik de Azure Security Center for IoT-connector om al uw Azure Security Center voor IoT-gebeurtenissen naar Azure Sentinel te streamen. 

## <a name="prerequisites"></a>Vereisten

- **Machtigingen voor** lezen en **schrijven** op de werkruimte waarop Azure Sentinel is geïmplementeerd
- **Azure Security Center voor IoT** moet zijn **ingeschakeld** op uw relevante IoT Hub(s)
- **Lees-** en **schrijfmachtigingen** op de **Azure IoT Hub** die u wilt verbinden
- **Lees-** en **schrijfmachtigingen** voor de **Azure IoT Hub-brongroep**

> [!NOTE]
> Hoewel u de Azure Security Center **Standard-licentie** voor uw abonnement moet inschakelen om IoT-bronwaarschuwingen naar Azure Sentinel te streamen, hoeft u alleen de Azure Security Center **Free-rijlicentie** op uw abonnement in te schakelen om Azure Security Center voor IoT-waarschuwingen in Azure Sentinel weer te geven. 

## <a name="connect-to-azure-security-center-for-iot"></a>Verbinding maken met Azure Security Center voor IoT

1. Selecteer in Azure Sentinel **gegevensconnectors** en klik vervolgens op de **tegel Azure Security Center for IoT.**
1. Klik in het deelvenster rechtsonder op **Connectorpagina openen**. 
1. Klik op **Verbinding**maken , naast elk IoT Hub-abonnement waarvan u waarschuwingen en apparaatwaarschuwingen wilt streamen naar Azure Sentinel. 
    - Als Azure Security Center for IoT niet is ingeschakeld op die hub, ziet u een waarschuwingsbericht **inschakelen.** Klik **op** de koppeling Inschakelen om de service te starten. 
1. U zelf bepalen of u wilt dat de waarschuwingen van Azure Security Center for IoT automatisch incidenten genereren in Azure Sentinel. Selecteer **onder Incidenten maken**de optie **Inschakelen** inschakelen om de standaardanalytische regel automatisch in te schakelen om incidenten te maken op meldingen die zijn gegenereerd in de verbonden beveiligingsservice. Deze regel kan worden gewijzigd of bewerkt onder **analytics** > **actieve** regels.

> [!NOTE]
> Het kan enige tijd duren voordat de hublijst is vernieuwd nadat de verbinding is gewijzigd. 

## <a name="log-analytics-alert-display"></a>Waarschuwingweergave van Log Analytics

Ga als u het relevante schema in Log Analytics wilt gebruiken om het Azure Security Center for IoT-waarschuwingen weer te geven:

1. Open **Logboeken** > **SecurityInsights** > **SecurityAlert**of zoek naar **SecurityAlert**. 
2. Filter om alleen Azure Security Center for IoT-gegenereerde waarschuwingen te bekijken met behulp van het volgende kql-filter:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Servicenotities

Na het aansluiten van een IoT Hub zijn de hubgegevens ongeveer 15 minuten later beschikbaar in Azure Sentinel.


## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Azure Security Center voor IoT-gegevens verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te controleren.
