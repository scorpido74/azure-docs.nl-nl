---
title: Een abonnement voor delen intrekken in een Azure-gegevens share
description: Meer informatie over het intrekken van een share-abonnement van een ontvanger met Azure data share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 97f672b266c2df6956282aea6dec8d5207944943
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "73476378"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Het delen van een gebruikers abonnement in azure data share intrekken

In dit artikel wordt uitgelegd hoe u een share abonnement intrekt van een of meer van uw consumenten met behulp van Azure data share. Zo voor komt u dat een gebruiker meer moment opnamen kan activeren. Als de gebruiker nog geen moment opname heeft geactiveerd, worden de gegevens nooit meer ontvangen zodra het abonnement van de share is ingetrokken. Als ze eerder een moment opname hebben geactiveerd, blijven de meest recente gegevens in hun account.

## <a name="navigate-to-a-sent-data-share"></a>Navigeer naar een verzonden gegevens share

In azure data share gaat u naar de verzonden share en selecteert u het tabblad **abonnementen delen** .

![Abonnement op delen intrekken](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Schakel de selectie vakjes in naast de ontvangers die de abonnementen delen die u wilt verwijderen en klik vervolgens op **intrekken**. De consument ontvangt geen updates meer voor hun gegevens.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [bewaken van uw gegevens shares](how-to-monitor.md).