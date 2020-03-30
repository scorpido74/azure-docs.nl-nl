---
title: Azure Security Center voor IoT-handleiding voor configuratie met Azure Sentinel (voorbeeld)| Microsoft Documenten
description: Hier wordt uitgelegd hoe u Azure Sentinel configureert om gegevens van uw Azure Security Center for IoT-oplossing te ontvangen.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: 082b33332051fee9da2aebe63b0c41edb300afaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303542"
---
> [!IMPORTANT]
> De Azure Security Center voor IoT-gegevensconnector in Azure Sentinel bevindt zich momenteel in een openbare preview.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Uw gegevens van Azure Security Center voor IoT verbinden met Azure Sentinel (voorbeeld) 

In deze handleiding leest u hoe u uw Azure Security Center voor IoT-gegevens verbinden met Azure Sentinel.  

> [!div class="checklist"]
> * Vereisten 
> * Verbindingsinstellingen
> * Waarschuwingsweergave Log Analytics 

Verbind waarschuwingen van Azure Security Center voor IoT en stream ze rechtstreeks naar Azure Sentinel.

## <a name="prerequisites"></a>Vereisten

- U moet **machtigingen** voor lezen en **schrijven in** workspace hebben.
- **Azure Security Center for IoT** moet zijn **ingeschakeld** op uw relevante IoT Hub(s).
- U moet zowel **lees-** als **schrijfmachtigingen** hebben op de **Azure IoT Hub** die u wilt verbinden.
- U moet ook **lees-** en **schrijfmachtigingen** hebben voor de **Azure IoT Hub-brongroep.**

> [!NOTE]
> U moet de Azure Security Center Standard-licentieverlening op uw abonnement laten uitvoeren om algemene Azure-bronwaarschuwingen te verzenden. Met de gratis laaglicenties die vereist zijn voor Azure Security Center voor IoT, worden alleen Azure Security Center voor IoT-gerelateerde waarschuwingen doorgestuurd naar Azure Sentinel. 

## <a name="connect-to-azure-security-center-for-iot"></a>Verbinding maken met Azure Security Center voor IoT

1. Selecteer in Azure Sentinel **gegevensconnectors** en klik vervolgens op de **tegel Azure Security Center for IoT.**
1. Klik onder aan het rechterdeelvenster op **Connectorpagina openen**. 
1. Klik op **Verbinding**maken , naast elk IoT Hub-abonnement waarvan u waarschuwingen en apparaatwaarschuwingen wilt streamen naar Azure Sentinel. 
    - Als Azure Security Center for IoT niet is ingeschakeld op die hub, ziet u een waarschuwingsbericht inschakelen. Klik **op** de koppeling Inschakelen om de service te starten en in te schakelen. 
1. U zelf bepalen of u wilt dat de waarschuwingen van Azure Security Center for IoT automatisch incidenten genereren in Azure Sentinel. Selecteer **onder Incidenten maken**de optie **Inschakelen** inschakelen om de regel automatisch incidenten te maken op de gegenereerde waarschuwingen.  Deze regel kan worden gewijzigd of bewerkt onder **analytics** > **actieve** regels.

> [!NOTE]
>Het kan 10 seconden of langer duren voordat de hublijst is vernieuwd nadat de verbinding is gewijzigd. 

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

In dit document hebt u geleerd hoe u Azure Security Center voor IoT verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over bedreigingsdetectie en toegang tot beveiligingsgegevens:

- Meer informatie over het gebruik van Azure Sentinel om inzicht te [krijgen in uw gegevens en potentiële bedreigingen.](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)

- Meer informatie over hoe u [toegang krijgt tot uw IoT-beveiligingsgegevens](how-to-security-data-access.md)