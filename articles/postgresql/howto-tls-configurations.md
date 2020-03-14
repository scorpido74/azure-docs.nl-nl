---
title: TLS-configuratie-Azure Portal-Azure Database for PostgreSQL-één server
description: Meer informatie over het instellen van TLS-configuratie met Azure Portal voor uw Azure Database for PostgreSQL enkele server
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 049b30689c86881ce89d4fbcd54175ee1ee5497e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375107"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql---single-server-using-azure-portal"></a>TLS-instellingen configureren in Azure Database for PostgreSQL-één server met behulp van Azure Portal

In dit artikel wordt beschreven hoe u een Azure Database for PostgreSQL-één-server kunt configureren om een minimum versie van TLS af te dwingen voor alle binnenkomende verbindingen, waarmee u de netwerk beveiliging kunt verbeteren.

Klanten kunnen nu TLS-versie afdwingen om verbinding te maken met hun Azure Database for PostgreSQL-één server. Klanten hebben nu de keuze om de minimale TLS-versie voor hun database server in te stellen. Als u bijvoorbeeld de minimale TLS-instellings versie instelt op TLS 1,0, betekent dit dat uw server verbindingen toestaat van clients met behulp van TLS 1,0, 1,1 en 1.2 +. U kunt dit ook instellen op 1,2 zodat u alleen verbindingen van clients toestaat die gebruikmaken van TLS 1,2 en alle verbindingen met TLS 1,0 en TLS 1,1 worden geweigerd.

## <a name="prerequisites"></a>Vereisten

Voor deze handleiding, hebt u het volgende nodig:

* Een [Azure database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>TLS-configuraties instellen voor Azure Database for PostgreSQL-één server

Voer de volgende stappen uit om de minimale TLS-versie van PostgreSQL één server in te stellen:

1. Selecteer uw bestaande Azure Database for PostgreSQL-één server in de [Azure Portal](https://portal.azure.com/).

1.  Klik op de pagina Azure Database for PostgreSQL-één server onder **instellingen**op **verbindings beveiliging** om de pagina verbindings beveiliging configureren te openen.

1. In **minimale TLS-versie**selecteert u **1,2** om verbindingen met een TLS-versie van minder dan TLS 1,2 voor uw postgresql-server te weigeren.

    ![Azure Database for PostgreSQL TLS-configuratie met één server](./media/howto-tls-configurations/setting-tls-value.png)

1. Klik op **Opslaan** om de wijzigingen op te slaan.

1. Bij een melding wordt bevestigd dat de instelling verbindings beveiliging is ingeschakeld.

    ![Azure Database for PostgreSQL-TLS-configuratie met één server is voltooid](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-on-metric.md).
