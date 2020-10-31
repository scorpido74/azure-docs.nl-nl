---
title: Verbinding maken met meer dan beSECURE-gegevens voor Azure-Sentinel | Microsoft Docs
description: Meer informatie over hoe u de meer dan Security beSECURE Data Connector kunt gebruiken om beSECURE-logboeken te verzamelen in azure Sentinel. Bekijk beSECURE-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: f6fd6920fafe4c1080cb5539e4e0222d9d6e18cd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102897"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Verbind uw beveiligings beSECURE tot Azure Sentinel

> [!IMPORTANT]
> De meer dan Security beSECURE Data Connector in azure Sentinel is momenteel beschikbaar als open bare preview. Deze functie wordt zonder service level agreement gegeven. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de beSECURE-connector voor beveiliging kunt u eenvoudig al uw beSECURE Security-oplossings logboeken koppelen aan uw Azure-Sentinel, om Dash boards weer te geven, aangepaste waarschuwingen te maken en het onderzoek te verbeteren. Integratie tussen beSECURE en Azure Sentinel maakt gebruik van REST API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-besecure"></a>BeSECURE configureren en verbinden

beSECURE kan worden geïntegreerd met en logboeken rechtstreeks naar Azure Sentinel.

1. Klik in de Azure Sentinel-Portal op **Data connectors** en selecteer meer **beveiliging beSECURE (preview)** en open vervolgens de **pagina connector** .

1. Volg de onderstaande stappen om uw beSECURE-oplossing te configureren voor het verzenden van scan resultaten, scan status en controle spoor logboeken naar Azure Sentinel.

    **Open het menu integratie:**
    1. Klik op de menu optie meer

    1. Server selecteren

    1. Integratie selecteren

    1. Azure Sentinel inschakelen 

    **Geef beSECURE op met de onderverklikker instellingen van Azure.**
      - Kopieer de *werk ruimte-id* en *primaire sleutel* waarden van de Azure Sentinel connector-pagina, plak ze in de beSECURE-configuratie en klik op **wijzigen** .

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken** , onder de sectie **CustomLogs** , in een of meer van de volgende tabellen:
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

Als u een query wilt uitvoeren voor de beSECURE-Logboeken in Log Analytics, voert u een van de bovenstaande tabel namen boven in het query venster in.

## <a name="validate-connectivity"></a>Connectiviteit valideren
Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u beSECURE kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
