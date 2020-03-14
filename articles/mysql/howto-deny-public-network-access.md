---
title: Open bare toegang tot het netwerk weigeren-Azure Portal-Azure Database for MySQL
description: Meer informatie over het configureren van toegang tot open bare netwerken met behulp van Azure Portal voor uw Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b5f93f3a3583900810ca75f925c6a88df9102652
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79374951"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Open bare netwerk toegang in Azure Database for MySQL weigeren met Azure Portal

In dit artikel wordt beschreven hoe u een Azure Database for MySQL server kunt configureren om alle open bare configuraties te weigeren en alleen verbindingen via persoonlijke eind punten toe te staan om de netwerk beveiliging verder uit te breiden.

## <a name="prerequisites"></a>Vereisten

Voor deze handleiding, hebt u het volgende nodig:

* Een [Azure database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Instellen van open bare netwerk toegang weigeren

Voer de volgende stappen uit om de MySQL-server toegang tot het open bare netwerk te geven:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)uw bestaande Azure database for mysql-server.

1. Klik op de pagina MySQL-server onder **instellingen**op **verbindings beveiliging** om de pagina verbindings beveiliging configureren te openen.

1. Selecteer in **open bare netwerk toegang weigeren**de optie **Ja** om open bare toegang voor uw MySQL-server in te scha kelen.

    ![Netwerk toegang Azure Database for MySQL weigeren](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. Klik op **Opslaan** om de wijzigingen op te slaan.

1. Bij een melding wordt bevestigd dat de instelling verbindings beveiliging is ingeschakeld.

    ![Azure Database for MySQL netwerk toegang weigeren](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-on-metric.md).