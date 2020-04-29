---
title: Cloud App Security gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Cloud App Security gegevens met Azure Sentinel.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422144"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Verbinding maken met gegevens van Microsoft Cloud App Security 



Met de [Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) -connector (MCAS) kunt u waarschuwingen streamen en [Cloud Discovery logboeken](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) van MCAS naar Azure Sentinel. Hiermee krijgt u inzicht in uw Cloud-apps, kunt u geavanceerde analyses verkrijgen om Cyber dreigingen te identificeren en te bestrijden en te bepalen hoe uw gegevens worden verplaatst.

## <a name="prerequisites"></a>Vereisten

- Uw gebruiker moet lees-en schrijf machtigingen hebben voor de werk ruimte.
- Uw gebruiker moet over de machtigingen globale beheerder of beveiligings beheerder beschikken voor de Tenant van de werk ruimte.
- Als u Cloud Discovery-Logboeken in azure Sentinel wilt streamen, [schakelt u Azure Sentinel in als uw Siem in Microsoft Cloud app Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> Opname van Cloud Discovery Logboeken is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen.
> Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Verbinding maken met Cloud App Security

Als u Cloud App Security al hebt, controleert u of deze is [ingeschakeld in uw netwerk](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Als Cloud App Security is geïmplementeerd en opnamet van uw gegevens, kunnen de waarschuwings gegevens eenvoudig worden gestreamd naar Azure Sentinel.


1. Selecteer in het navigatie menu van de Azure-Sentinel **Data connectors**. Klik in de lijst met connectors op de tegel **Microsoft Cloud app Security** en vervolgens op de knop **pagina connector openen** aan de rechter kant.

1. Selecteer de logboeken die u wilt streamen naar Azure Sentinel; u kunt **waarschuwingen** en **Cloud Discovery logboeken** kiezen (preview-versie). 

1. Klik op **wijzigingen Toep assen**.

1. Als u het relevante schema in Log Analytics voor Cloud App Security-waarschuwingen wilt `SecurityAlert` gebruiken, typt u in het query venster. Typ `McasShadowItReporting`voor het Cloud Discovery logboeken schema.

> [!NOTE]
> Cloud Discovery helpt trends te detecteren en te identificeren door de gegevens van de onderliggende gebruikers verbindingen met Cloud-apps samen te voegen.
>
> Omdat Cloud Discovery gegevens per dag worden geaggregeerd, moet u er rekening mee houden dat Maxi maal 24 uur aan de meest recente gegevens niet in azure Sentinel wordt weer gegeven. In het geval dat een onderzoek op laag niveau meer onmiddellijke gegevens vereist, moet dit rechtstreeks worden gedaan in het bron apparaat of de service waar de onbewerkte gegevens zich bevinden.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Microsoft Cloud App Security kunt verbinden met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met het detecteren van bedreigingen met Azure Sentinel, met behulp [van ingebouwde](tutorial-detect-threats.md) of [aangepaste](tutorial-detect-threats-custom.md) regels.
