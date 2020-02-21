---
title: Squadra Technologies secRMM-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Squadra Technologies secRMM-gegevens met Azure Sentinel.
services: sentinel
author: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: rkarlin
ms.openlocfilehash: 1e35cd4a5b27b87fd5a022c5e798f4590b0bba1c
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77501236"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Verbind uw Squadra Technologies secRMM-gegevens met de Azure-Sentinel 

> [!IMPORTANT]
> De Squadra Technologies Security Removable media manager (secRMM)-gegevens connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


Met de Squadra Technologies secRMM-connector kunt u uw Squadra Technologies secRMM-logboeken voor beveiligings oplossingen eenvoudig verbinden met Azure Sentinel. U kunt Dash boards weer geven, aangepaste waarschuwingen maken en het onderzoek verbeteren. Met deze connector beschikt u over inzicht in USB-Verwissel bare opslag gebeurtenissen. Integratie tussen Squadra Technologies secRMM en Azure Sentinel maakt gebruik van REST API.


> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Squadra Technologies configureren en verbinden secRMM 

Squadra Technologies secRMM kunnen Logboeken rechtstreeks integreren en exporteren naar Azure Sentinel.
1. Klik in de Azure-Sentinel Portal op Data connectors en selecteer Squadra Technologies secRMM en open vervolgens de pagina connector.

2. Volg de stappen die worden beschreven in de [hand leiding voor de onboarding van Squadra Technologies voor Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) om Squadra secRMM-gegevens op te halen in azure Sentinel.   


## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in Log Analytics onder CustomLogs secRMM_CL.
Als u het relevante schema in Log Analytics wilt gebruiken voor de Squadra Technologies secRMM, zoekt u naar secRMM_CL.

## <a name="validate-connectivity"></a>Connectiviteit valideren
Het kan 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Squadra Technologies secRMM kunt verbinden met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.

