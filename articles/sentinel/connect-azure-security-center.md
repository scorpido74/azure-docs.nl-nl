---
title: Azure Security Center-gegevens verbinden met Azure Sentinel
description: Meer informatie over het verbinden van Azure Security Center-gegevens met Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588532"
---
# <a name="connect-data-from-azure-security-center"></a>Gegevens verbinden vanuit Azure Security Center





Met Azure Sentinel u waarschuwingen van [Azure Security Center](../security-center/security-center-intro.md) verbinden en streamen naar Azure Sentinel. 

## <a name="prerequisites"></a>Vereisten

- Als u waarschuwingen wilt exporteren vanuit Azure Security Center, moet u de rol Beveiligingslezer hebben in het abonnement van de logboeken die u streamt.

- U moet de [azure security center standaardlaag](../security-center/security-center-pricing.md) op het abonnement laten draaien. Zo niet, [upgrade uw abonnement naar standaard.](https://azure.microsoft.com/pricing/details/security-center/)



## <a name="connect-to-azure-security-center"></a>Verbinding maken met Azure Security Center

1. Selecteer in Azure Sentinel **gegevensconnectors** en klik vervolgens op de tegel **Azure Security Center.**

1. Klik rechts op **Verbinding maken** naast elk abonnement waarvan u de waarschuwingen wilt streamen naar Azure Sentinel. Zorg ervoor dat u elk abonnement upgradet naar azure security center standard-laag om waarschuwingen naar Azure Sentinel te streamen.

1. U selecteren of u wilt dat de waarschuwingen van Azure Security Center automatisch incidenten genereren in Azure Sentinel. Selecteer **onder Incidenten maken** **inschakelen** om de standaardanalytische regel in te schakelen die incidenten automatisch maakt op uit waarschuwingen die zijn gegenereerd in de verbonden beveiligingsservice. U deze regel vervolgens bewerken onder **Analytics** en vervolgens **Actieve regels**.

3. Klik op **Verbinden**.

4. Als u het relevante schema wilt gebruiken in Log Analytics voor de azure security center-waarschuwingen, zoekt u **naar SecurityAlert**.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Azure Security Center verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
