---
title: Een abonnement voor delen intrekken in een Azure-gegevens share
description: Meer informatie over het intrekken van een share-abonnement van een ontvanger met Azure data share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: c1c049d467cdf07962719f0dc257acbf47631aaf
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119681"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Het delen van een gebruikers abonnement in azure data share intrekken

In dit artikel wordt uitgelegd hoe u een share abonnement intrekt van een of meer van uw consumenten met behulp van Azure data share. Zo voor komt u dat een gebruiker meer moment opnamen kan activeren. Als de gebruiker nog geen moment opname heeft geactiveerd, worden de gegevens nooit meer ontvangen zodra het abonnement van de share is ingetrokken. Als ze eerder een moment opname hebben geactiveerd, blijven de meest recente gegevens in hun account.

## <a name="navigate-to-a-sent-data-share"></a>Navigeer naar een verzonden gegevens share

In azure data share gaat u naar de verzonden share en selecteert u het tabblad **abonnementen delen** .

![Abonnement op delen intrekken](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Schakel de selectie vakjes in naast de ontvangers die de abonnementen delen die u wilt verwijderen en klik vervolgens op **intrekken**. De consument ontvangt geen updates meer voor hun gegevens.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [bewaken van uw gegevens shares](how-to-monitor.md).