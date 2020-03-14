---
title: TLS-configuratie-Azure Portal-Azure Database for MySQL
description: Meer informatie over het instellen van TLS-configuratie met behulp van Azure Portal voor uw Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 495fea21c4fca12cd1ec6c1ea45105178d72fe1b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375289"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>TLS-instellingen in Azure Database for MySQL configureren met Azure Portal

In dit artikel wordt beschreven hoe u een Azure Database for MySQL-server kunt configureren om verbindingen voor een minimale TLS-versie af te dwingen en alle verbindingen met een lagere TLS-versie te weigeren, waardoor de netwerk beveiliging wordt verbeterd.

Klanten kunnen nu TLS-versie afdwingen om verbinding te maken met hun Azure Database for MySQL. Klanten hebben nu de keuze om de minimale TLS-versie voor hun database server in te stellen. Als u bijvoorbeeld deze minimale TLS-versie instelt op 1,0, betekent dit dat clients verbinding kunnen maken met behulp van TLS 1.0, 1.1 en 1,2. Als u dit instelt op 1,2, betekent dit dat u alleen clients toestaat die verbinding maken met behulp van TLS 1,2 en alle binnenkomende verbindingen met TLS 1,0 en TLS 1,1 worden geweigerd.

## <a name="prerequisites"></a>Vereisten

Voor deze handleiding, hebt u het volgende nodig:

* Een [Azure database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>TLS-configuraties instellen voor Azure Database for MySQL

Volg deze stappen om de minimale TLS-versie van het MySQL-server in te stellen:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)uw bestaande Azure database for mysql-server.

1. Klik op de pagina MySQL-server onder **instellingen**op **verbindings beveiliging** om de pagina verbindings beveiliging configureren te openen.

1. Selecteer in **minimale TLS**-versie **1,2** om verbindingen met een TLS-versie kleiner dan TLS 1,2 voor uw MySQL-server te weigeren.

    ![Azure Database for MySQL TLS-configuratie](./media/howto-tls-configurations/setting-tls-value.png)

1. Klik op **Opslaan** om de wijzigingen op te slaan.

1. Bij een melding wordt bevestigd dat de instelling verbindings beveiliging is ingeschakeld.

    ![Azure Database for MySQL TLS-configuratie geslaagd](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-on-metric.md).
