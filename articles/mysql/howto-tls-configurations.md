---
title: TLS-configuratie-Azure Portal-Azure Database for MySQL
description: Meer informatie over het instellen van TLS-configuratie met behulp van Azure Portal voor uw Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: d94e589a19b29e68883c0217b62b883f9d026789
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902741"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>TLS-instellingen in Azure Database for MySQL configureren met Azure Portal

In dit artikel wordt beschreven hoe u een Azure Database for MySQL server kunt configureren voor het afdwingen van de minimale TLS-versie die is toegestaan voor verbindingen om alle verbindingen met een lagere TLS-versie dan de geconfigureerde minimum versie van TLS te vervolledigen en zodoende de netwerk beveiliging te verbeteren.

U kunt TLS-versie afdwingen om verbinding te maken met hun Azure Database for MySQL. Klanten hebben nu de keuze om de minimale TLS-versie voor hun database server in te stellen. Als u bijvoorbeeld deze minimale TLS-versie instelt op 1,0, betekent dit dat clients verbinding kunnen maken met behulp van TLS 1.0, 1.1 en 1,2. Als u dit instelt op 1,2, betekent dit dat u alleen clients toestaat die verbinding maken met behulp van TLS 1.2 + en alle binnenkomende verbindingen met TLS 1,0 en TLS 1,1 worden geweigerd.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

* Een [Azure database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>TLS-configuraties instellen voor Azure Database for MySQL

Volg deze stappen om de minimale TLS-versie van het MySQL-server in te stellen:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)uw bestaande Azure database for mysql-server.

1. Klik op de pagina MySQL-server onder **instellingen**op **verbindings beveiliging** om de pagina verbindings beveiliging configureren te openen.

1. Selecteer in **minimale TLS**-versie **1,2** om verbindingen met een TLS-versie kleiner dan TLS 1,2 voor uw MySQL-server te weigeren.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="Azure Database for MySQL TLS-configuratie":::

1. Klik op **Opslaan** om de wijzigingen op te slaan.

1. Bij een melding wordt bevestigd dat de instelling verbindings beveiliging is ingeschakeld.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Azure Database for MySQL TLS-configuratie geslaagd":::

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-on-metric.md)