---
title: Azure Security Center gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Azure Security Center gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: a9c210531f2c4cab1c3c023eab795023c3ad9f0c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240225"
---
# <a name="connect-data-from-azure-security-center"></a>Verbinding maken met gegevens van Azure Security Center





Met Azure Sentinel kunt u waarschuwingen van [Azure Security Center](../security-center/security-center-intro.md) verbinden en deze streamen naar Azure Sentinel. 

## <a name="prerequisites"></a>Vereisten

- Als u waarschuwingen van Azure Security Center wilt exporteren, moet u een bijdrager aan het abonnement zijn met de logboeken die u hebt gestreamd.

- U moet beschikken over de [Azure Security Center Standard-laag](../security-center/security-center-pricing.md) die op het abonnement wordt uitgevoerd. Als dat niet het geval is, [werkt u uw abonnement bij naar standaard](https://azure.microsoft.com/pricing/details/security-center/).

- U moet zich aanmelden met een gebruiker met de machtigingen globale beheerder of beveiligings beheerder voor elk abonnement dat u wilt verbinden.


## <a name="connect-to-azure-security-center"></a>Verbinding maken met Azure Security Center

1. Selecteer in azure Sentinel **Data connectors** en klik vervolgens op de tegel **Azure Security Center** .

1. Klik aan de rechter kant op **verbinding maken** naast elk abonnement waarvan u de waarschuwingen wilt streamen naar Azure Sentinel. Zorg ervoor dat u elk abonnement bijwerkt naar Azure Security Center Standard-laag om waarschuwingen naar Azure Sentinel te verzenden.

1. U kunt selecteren of u wilt dat de waarschuwingen van Azure Security Center automatisch incidenten in azure-Sentinel automatisch genereren. Selecteer **inschakelen** onder **incidenten maken** om de standaard analyse regel in te scha kelen waarmee incidenten automatisch worden gemaakt op basis van waarschuwingen die zijn gegenereerd in de verbonden beveiligings service. U kunt deze regel vervolgens bewerken onder **analyse** en vervolgens op **actieve regels**.

3. Klik op**Verbinden**.

4. Als u het relevante schema in Log Analytics voor de Azure Security Center waarschuwingen wilt gebruiken, zoekt u naar **SecurityAlert**.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Azure Security Center kunt verbinden met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
