---
title: Azure Security Center voor IoT-gids voor configuratie met Azure Sentinel (preview) | Microsoft Docs
description: Hierin wordt uitgelegd hoe u Azure Sentinel configureert om gegevens te ontvangen van uw Azure Security Center voor IoT-oplossing.
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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303542"
---
> [!IMPORTANT]
> De Azure Security Center voor IoT Data Connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Uw gegevens verbinden met Azure Security Center voor IoT naar Azure Sentinel (preview) 

In deze hand leiding vindt u informatie over het verbinden van uw Azure Security Center voor IoT-gegevens naar Azure Sentinel.  

> [!div class="checklist"]
> * Vereisten 
> * Verbindingsinstellingen
> * Waarschuwings weergave Log Analytics 

Verbinding maken met waarschuwingen van Azure Security Center voor IoT en deze rechtstreeks streamen naar Azure Sentinel.

## <a name="prerequisites"></a>Vereisten

- U moet **Lees** -en **Schrijf** machtigingen voor de werk ruimte hebben.
- **Azure Security Center voor IOT** moet zijn **ingeschakeld** op uw relevante IOT hub (s).
- U moet de machtigingen **lezen** en **schrijven** hebben op de **Azure-IOT hub** u verbinding wilt maken.
- U moet ook **Lees** -en **Schrijf** machtigingen hebben voor de **resource groep Azure IOT hub**.

> [!NOTE]
> U moet beschikken over de Azure Security Center Standard-laag licenties die worden uitgevoerd op uw abonnement om algemene Azure-resource waarschuwingen te verzenden. Als de gratis-laag licentie is vereist voor Azure Security Center voor IoT, worden alleen Azure Security Center voor IoT-gerelateerde waarschuwingen doorgestuurd naar Azure Sentinel. 

## <a name="connect-to-azure-security-center-for-iot"></a>Verbinding maken met Azure Security Center voor IoT

1. Selecteer in azure Sentinel **Data connectors** en klik vervolgens op de tegel **Azure Security Center voor IOT** .
1. Klik onder aan het rechterdeel venster op **connector pagina openen**. 
1. Klik op **verbinding maken**naast elk IOT hub abonnement waarvan u de waarschuwingen en de waarschuwingen van apparaten wilt streamen naar Azure Sentinel. 
    - Als Azure Security Center voor IoT niet op die hub is ingeschakeld, wordt er een waarschuwings bericht weer gegeven. Klik op de koppeling **inschakelen** om de service te starten en in te scha kelen. 
1. U kunt beslissen of u de waarschuwingen van Azure Security Center voor IoT automatisch incidenten wilt genereren in azure Sentinel. Selecteer onder **incidenten maken**de optie **inschakelen** om de regel in te scha kelen om automatisch incidenten te maken op basis van de gegenereerde waarschuwingen.  Deze regel kan worden gewijzigd of bewerkt onder **analyse** > **actieve** regels.

> [!NOTE]
>Het kan 10 seconden of langer duren om de hub-lijst te vernieuwen nadat de verbinding is gewijzigd. 

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

In dit document hebt u geleerd hoe u Azure Security Center kunt verbinden met IoT naar Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over het detecteren van bedreigingen en toegang tot de beveiliging van gegevens:

- Leer hoe u Azure Sentinel kunt gebruiken om [inzicht te krijgen in uw gegevens en mogelijke bedreigingen](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility).

- Meer informatie over [het openen van uw IOT-beveiligings gegevens](how-to-security-data-access.md)