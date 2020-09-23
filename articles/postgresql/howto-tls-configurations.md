---
title: TLS-configuratie-Azure Portal-Azure Database for PostgreSQL-één server
description: Meer informatie over het instellen van TLS-configuratie met Azure Portal voor uw Azure Database for PostgreSQL enkele server
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 26470709b37c2623c581499ec55572da402e96cb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906453"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql-single---server-using-azure-portal"></a>TLS-instellingen configureren in Azure Database for PostgreSQL één server met behulp van Azure Portal

In dit artikel wordt beschreven hoe u een Azure Database for PostgreSQL kunt configureren om de minimale TLS-versie die voor verbindingen is toegestaan af te dwingen en alle verbindingen met een lagere TLS-versie dan de geconfigureerde minimale TLS-versie te weigeren, waardoor de netwerk beveiliging wordt verbeterd.

U kunt TLS-versie afdwingen om verbinding te maken met hun Azure Database for PostgreSQL. Klanten hebben nu de keuze om de minimale TLS-versie voor hun database server in te stellen. Als u bijvoorbeeld de minimale TLS-instellings versie instelt op TLS 1,0, betekent dit dat uw server verbindingen toestaat van clients met behulp van TLS 1,0, 1,1 en 1.2 +. In plaats daarvan is het instellen van de minimale TLS-versie op 1,2 + betekent dat u alleen verbindingen van clients met TLS 1,2 wilt toestaan en dat alle verbindingen met TLS 1,0 en TLS 1,1 worden geweigerd.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

* Een [Azure database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>TLS-configuraties instellen voor Azure Database for PostgreSQL-één server

Voer de volgende stappen uit om de PostgreSQL minimum versie van TLS in te stellen:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)uw bestaande Azure database for PostgreSQL.

1.  Klik op de pagina Azure Database for PostgreSQL-één server onder **instellingen**op **verbindings beveiliging** om de pagina verbindings beveiliging configureren te openen.

1. In **minimale TLS-versie**selecteert u **1,2** om verbindingen met een TLS-versie van minder dan TLS 1,2 voor uw postgresql-server te weigeren.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="Azure Database for PostgreSQL TLS-configuratie met één server":::

1. Klik op **Opslaan** om de wijzigingen op te slaan.

1. Bij een melding wordt bevestigd dat de instelling verbindings beveiliging is ingeschakeld.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Azure Database for PostgreSQL-TLS-configuratie met één server is voltooid":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-on-metric.md)