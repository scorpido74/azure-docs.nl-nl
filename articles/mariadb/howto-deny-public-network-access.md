---
title: Openbare netwerktoegang weigeren - Azure-portal - Azure-database voor MariaDB
description: Meer informatie over het configureren van Openbare Netwerktoegang weigeren met Azure-portal voor uw Azure-database voor MariaDB
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 87b6033e486e9009573436628d0183c8a022aced
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375237"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Openbare netwerktoegang in Azure-database voor MariaDB weigeren met Azure-portal

In dit artikel wordt beschreven hoe u een Azure Database voor MariaDB-server configureren om alle openbare configuraties te weigeren en alleen verbindingen via privéeindpunten toe te staan om de netwerkbeveiliging verder te verbeteren.

## <a name="prerequisites"></a>Vereisten

Om deze handleiding te voltooien, moet u het:

* Een [Azure-database voor MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Toegang tot openbare netwerken weigeren instellen

Volg de volgende stappen om MariaDB-server Deny Public Network Access in te stellen:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)uw bestaande Azure-database voor MariaDB-server.

1. Klik op de pagina MariaDB-server onder **Instellingen**op **Verbindingsbeveiliging** om de pagina met verbindingsbeveiliging te openen.

1. Selecteer in Openbare netwerktoegang weigeren de optie **Ja** om de toegang van het publiek voor uw MariaDB-server te weigeren.

    ![Azure Database voor MariaDB Deny-netwerktoegang](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Klik op **Opslaan** om de wijzigingen op te slaan.

1. Een melding bevestigt dat de verbindingsbeveiligingsinstelling is ingeschakeld.

    ![Azure Database voor MariaDB Deny netwerktoegang succes](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over statistieken](howto-alert-metric.md).