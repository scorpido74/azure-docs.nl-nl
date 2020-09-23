---
title: Azure Sentinel configureren voor Defender voor IoT (preview-versie)
description: Hierin wordt uitgelegd hoe u Azure Sentinel configureert om gegevens te ontvangen van uw Defender voor IoT-oplossing.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 71147352c5b75195ed0dff2b05acc5315f3183cc
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936472"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Uw gegevens verbinden vanuit Defender voor IoT naar Azure Sentinel (preview)

De Azure Security Center voor IoT Data Connector in azure Sentinel is momenteel beschikbaar als open bare preview. Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/) voor meer informatie.

In deze hand leiding leert u hoe u uw Defender voor IoT-gegevens verbindt met Azure Sentinel.

> [!div class="checklist"]
> * Vereisten
> * Verbindingsinstellingen
> * Waarschuwings weergave Log Analytics

Verbind waarschuwingen van Defender voor IoT en stream ze rechtstreeks naar Azure Sentinel.

## <a name="prerequisites"></a>Vereisten

- U moet **Lees** -en **Schrijf** machtigingen voor de werk ruimte hebben.
- **Defender voor IOT** moet zijn **ingeschakeld** op uw relevante IOT hub (s).
- U moet de machtigingen **lezen** en **schrijven** hebben op de **Azure-IOT hub** u verbinding wilt maken.
- U moet ook **Lees** -en **Schrijf** machtigingen hebben voor de **resource groep Azure IOT hub**.

> [!NOTE]
> U moet beschikken over de Azure Security Center Standard-laag licenties die worden uitgevoerd op uw abonnement om algemene Azure-resource waarschuwingen te verzenden. Met de gratis laag licenties die vereist zijn voor Defender voor IoT, worden alleen Defender voor IoT-gerelateerde waarschuwingen doorgestuurd naar Azure Sentinel.

## <a name="connect-to-defender-for-iot"></a>Verbinding maken met Defender voor IoT

1. Selecteer in azure Sentinel **Data connectors** en klik vervolgens op de tegel **Defender voor IOT** .
1. Klik onder aan het rechterdeel venster op **connector pagina openen**.
1. Klik op **verbinding maken**naast elk IOT hub abonnement waarvan u de waarschuwingen en de waarschuwingen van apparaten wilt streamen naar Azure Sentinel.
    - Als Defender voor IoT niet op die hub is ingeschakeld, wordt er een waarschuwings bericht weer gegeven. Klik op de koppeling **inschakelen** om de service te starten en in te scha kelen.
1. U kunt beslissen of u wilt dat de waarschuwingen van Defender voor IoT automatisch incidenten genereren in azure Sentinel. Selecteer onder **incidenten maken**de optie **inschakelen** om de regel in te scha kelen om automatisch incidenten te maken op basis van de gegenereerde waarschuwingen.  Deze regel kan worden gewijzigd of bewerkt onder **Analytics**  >  **actieve** regels voor analyse.

> [!NOTE]
>Het kan 10 seconden of langer duren om de hub-lijst te vernieuwen nadat de verbinding is gewijzigd.

## <a name="log-analytics-alert-display"></a>Log Analytics waarschuwings weergave

Het relevante schema in Log Analytics gebruiken om de Defender voor IoT-waarschuwingen weer te geven:

1. Open **Logboeken**  >  **SecurityInsights**  >  **SecurityAlert**of zoek naar **SecurityAlert**.
1. Filter om alleen Defender voor IoT gegenereerde waarschuwingen weer te geven met behulp van het volgende kql-filter:

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>Opmerkingen bij de service

Nadat u verbinding hebt gemaakt met een IoT Hub, zijn de hub-gegevens ongeveer 15 minuten later beschikbaar in azure Sentinel.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Defender voor IoT verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over het detecteren van bedreigingen en toegang tot de beveiliging van gegevens:

- Leer hoe u Azure Sentinel kunt gebruiken om [inzicht te krijgen in uw gegevens en mogelijke bedreigingen](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility).

- Meer informatie over [het openen van uw IOT-beveiligings gegevens](how-to-security-data-access.md)
