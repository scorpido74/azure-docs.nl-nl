---
title: Azure ATP-gegevens verbinden met Azure-Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Azure ATP-gegevens met Azure Sentinel.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 387d04cbbb125006efcc4efc53a02015fe3f5919
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588583"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Gegevens verbinden vanuit Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> De Azure Advanced Threat Protection-gegevens connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

U kunt Logboeken van [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) streamen naar Azure Sentinel met één klik.

## <a name="prerequisites"></a>Vereisten

- Gebruiker met de machtigingen globale beheerder of beveiligings beheerder
- U moet een preview-klant zijn van Azure ATP en de integratie tussen Azure ATP en Microsoft Cloud App Security inschakelen. Zie [integratie van Azure Advanced Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration)voor meer informatie.

## <a name="connect-to-azure-atp"></a>Verbinding maken met Azure ATP

Zorg ervoor dat de preview-versie van Azure ATP is [ingeschakeld in uw netwerk](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Als Azure ATP is geïmplementeerd en uw gegevens worden opgenomen, kunnen verdachte waarschuwingen eenvoudig worden gestreamd naar Azure Sentinel. Het kan tot 24 uur duren voordat de waarschuwingen streamen naar Azure Sentinel starten.


1. Als u Azure ATP wilt verbinden met Azure Sentinel, moet u eerst integratie tussen Azure ATP en Microsoft Cloud App Security inschakelen. Zie [integratie van Azure Advanced Threat Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration)voor informatie over hoe u dit doet.

1. Selecteer in azure Sentinel **Data connectors** en klik vervolgens op de tegel **Azure Advanced Threat Protection (preview)** .

1. U kunt selecteren of u wilt dat de waarschuwingen van Azure ATP automatisch incidenten genereren in azure Sentinel automatisch. Selecteer **inschakelen** onder **incidenten maken** om de standaard analyse regel in te scha kelen waarmee incidenten automatisch worden gemaakt op basis van waarschuwingen die zijn gegenereerd in de verbonden beveiligings service. U kunt deze regel vervolgens bewerken onder **analyse** en vervolgens op **actieve regels**.

1. Klik op **Verbinden**.

1. Als u het relevante schema in Log Analytics voor de Azure ATP-waarschuwingen wilt gebruiken, zoekt u naar **SecurityAlert**.

> [!NOTE]
> Als de waarschuwingen groter zijn dan 30 KB, wordt in azure Sentinel stopt het veld entities in de waarschuwingen weer gegeven.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Azure Advanced Threat Protection kunt verbinden met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).

