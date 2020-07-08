---
title: Open bare toegang tot het netwerk weigeren-Azure Portal-Azure Database for PostgreSQL-één server
description: Meer informatie over het configureren van toegang tot open bare netwerken met behulp van Azure Portal voor uw Azure Database for PostgreSQL enkele server
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4dff2321414721dbd415b468e59aea0ab4b3acee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79375120"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Open bare netwerk toegang tot Azure Database for PostgreSQL één server weigeren met Azure Portal

In dit artikel wordt beschreven hoe u een Azure Database for PostgreSQL één server kunt configureren om alle open bare configuraties te weigeren en alleen verbindingen via persoonlijke eind punten toe te staan om de netwerk beveiliging verder uit te breiden.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

* Een [Azure database for PostgreSQL enkele server](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Instellen van open bare netwerk toegang weigeren

Volg deze stappen voor het instellen van PostgreSQL single server toegang tot open bare netwerken weigeren:

1. Selecteer uw bestaande Azure Database for PostgreSQL enkele server in de [Azure Portal](https://portal.azure.com/).

1. Klik op de pagina PostgreSQL Eén server onder **instellingen**op **verbindings beveiliging** om de pagina verbindings beveiliging configureren te openen.

1. Selecteer in **open bare netwerk toegang weigeren**de optie **Ja** om open bare toegang voor uw postgresql één server in te scha kelen.

    ![Azure Database for PostgreSQL afzonderlijke server netwerk toegang weigeren](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Klik op **Opslaan** om de wijzigingen op te slaan.

1. Bij een melding wordt bevestigd dat de instelling verbindings beveiliging is ingeschakeld.

    ![Azure Database for PostgreSQL één server netwerk toegang is geweigerd](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-on-metric.md).