---
title: Een aandelenabonnement intrekken in Azure Data Share
description: Meer informatie over het intrekken van een aandelenabonnement van een ontvanger met Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 97f672b266c2df6956282aea6dec8d5207944943
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73476378"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Het aandelenabonnement van een consument intrekken in Azure Data Share

In dit artikel wordt uitgelegd hoe u een aandelenabonnement intrekt van een of meer van uw consumenten met Azure Data Share. Dit voorkomt dat een consument nog meer snapshots activeert. Als de consument nog geen momentopname heeft geactiveerd, ontvangt hij de gegevens nooit meer zodra het aandelenabonnement is ingetrokken. Als ze eerder een momentopname hebben geactiveerd, blijven de meest recente gegevens die ze hebben in hun account.

## <a name="navigate-to-a-sent-data-share"></a>Navigeren naar een verzonden gegevensaandeel

Navigeer in Azure Data Share naar uw verzonden aandeel en selecteer het tabblad **Abonnementen delen.**

![Abonnement op delen intrekken](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Schakel de selectievakjes in naast de ontvangers van wie u de abonnementen op delen wilt verwijderen en klik op **Intrekken**. De consument krijgt geen updates meer van zijn gegevens.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [controleren van uw gegevensshares](how-to-monitor.md).