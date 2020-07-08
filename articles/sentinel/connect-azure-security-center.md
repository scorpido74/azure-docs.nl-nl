---
title: Azure Security Center gegevens verbinden met Azure Sentinel
description: Meer informatie over het verbinden van waarschuwingen van de Standard-laag Azure Security Center (ASC) en het streamen naar Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 2fc7744600a9652ad43fd0aae8d886dc94acd58f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559150"
---
# <a name="connect-data-from-azure-security-center-asc"></a>Verbinding maken met gegevens van Azure Security Center (ASC)

Met Azure Sentinel kunt u waarschuwingen van [Azure Security Center](../security-center/security-center-intro.md) verbinden en deze streamen naar Azure Sentinel. 

## <a name="prerequisites"></a>Vereisten

- Als u waarschuwingen van Azure Security Center wilt exporteren, moet u de rol van beveiligings lezer hebben in het abonnement van de logboeken die u streamen.

- U moet beschikken over de [Azure Security Center Standard-laag](../security-center/security-center-pricing.md) die op het abonnement wordt uitgevoerd. Als dat niet het geval is, [werkt u uw abonnement bij naar standaard](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="connect-to-azure-security-center"></a>Verbinding maken met Azure Security Center

1. Selecteer in azure Sentinel **Data connectors** in het navigatie menu.

1. Selecteer in de galerie met gegevens connectors de optie **Azure Security Center**en klik op de knop **pagina connector openen** .

1. Klik onder **configuratie**op **verbinden** naast elk abonnement waarvan u de waarschuwingen wilt streamen naar Azure Sentinel. De knop verbinding maken is alleen beschikbaar als u de vereiste machtigingen en het ASC-abonnement voor de Standard-laag hebt.

1. U kunt selecteren of u wilt dat de waarschuwingen van Azure Security Center automatisch incidenten genereren in azure Sentinel. Schakel onder **incidenten maken**de optie **ingeschakeld** in om de standaard analyse regel in te scha kelen waarmee automatisch incidenten worden gemaakt op basis van waarschuwingen. U kunt deze regel vervolgens onder **analyse**bewerken op het tabblad **actieve regels** .

1. Als u het relevante schema in Log Analytics voor de Azure Security Center waarschuwingen wilt gebruiken, zoekt u naar **SecurityAlert**.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Azure Security Center kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
