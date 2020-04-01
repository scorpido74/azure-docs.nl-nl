---
title: Cloud App-beveiligingsgegevens verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Cloud App Security-gegevens met Azure Sentinel.
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
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 266d97e834247088d40837cbec1436e00d0f4be2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422144"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Gegevens van Microsoft Cloud App Security verbinden 



Met de [MCAS-connector (Microsoft Cloud App Security)](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) u waarschuwingen en [Cloud Discovery-logboeken](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) van MCAS streamen naar Azure Sentinel. Dit stelt u in staat om inzicht te krijgen in uw cloud-apps, geavanceerde analyses te krijgen om cyberbedreigingen te identificeren en te bestrijden en te bepalen hoe uw gegevens worden weergegeven.

## <a name="prerequisites"></a>Vereisten

- Uw gebruiker moet lees- en schrijfmachtigingen op de werkruimte hebben.
- Uw gebruiker moet machtigingen voor globale beheerder of beveiligingsbeheerder op de tenant van de werkruimte hebben.
- Als u Cloud Discovery-logboeken wilt streamen naar Azure Sentinel, [schakelt u Azure Sentinel in als uw SIEM in Microsoft Cloud App Security.](https://aka.ms/AzureSentinelMCAS)

> [!IMPORTANT]
> Inname van Cloud Discovery-logboeken is momenteel in openbare preview.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.
 
## <a name="connect-to-cloud-app-security"></a>Verbinding maken met cloud-appbeveiliging

Als u al cloud-appbeveiliging hebt, controleert u of deze is [ingeschakeld in uw netwerk.](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
Als Cloud App Security wordt geïmplementeerd en uw gegevens wordt opgenomen, kunnen de waarschuwingsgegevens eenvoudig worden gestreamd naar Azure Sentinel.


1. Selecteer **gegevensconnectoren**in het navigatiemenu van Azure Sentinel . Klik in de lijst met connectors op de tegel Beveiliging van **Microsoft Cloud-apps** en vervolgens op de knop **Connector openen** rechtsonder.

1. Selecteer welke logboeken u wilt streamen naar Azure Sentinel; u **Waarschuwingen** en **clouddetectielogboeken kiezen** (voorbeeld). 

1. Klik **op Wijzigingen toepassen**.

1. Als u het relevante schema wilt gebruiken in `SecurityAlert` Logboekanalyse voor beveiligingswaarschuwingen voor cloudapps, typt u het queryvenster. Typ voor het logboekenschema voor clouddetectie `McasShadowItReporting`.

> [!NOTE]
> Cloud Discovery helpt trends te detecteren en te identificeren door de gegevens die ten grondslag liggen aan de verbindingen van gebruikers met cloud-apps te aggregeren.
>
> Aangezien Cloud Discovery-gegevens per dag worden samengevoegd, moet u er rekening mee houden dat de gegevens tot 24 uur van de meest recente gegevens niet worden weergegeven in Azure Sentinel. In het geval dat een onderzoek op laag niveau meer onmiddellijke gegevens vereist, moet dit rechtstreeks worden gedaan in het brontoestel of de brondienst waar de ruwe gegevens zich bevinden.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Microsoft Cloud App Security verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Ga aan de slag met het detecteren van bedreigingen met Azure Sentinel, met behulp van [ingebouwde](tutorial-detect-threats.md) of [aangepaste](tutorial-detect-threats-custom.md) regels.
