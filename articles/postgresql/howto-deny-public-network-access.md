---
title: Openbare netwerktoegang weigeren - Azure-portal - Azure Database voor PostgreSQL - Enkele server
description: Meer informatie over het configureren van Verweertoegang voor openbare netwerken met Azure-portal voor uw Azure Database voor PostgreSQL Single-server
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4dff2321414721dbd415b468e59aea0ab4b3acee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375120"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Openbare netwerktoegang weigeren in Azure-database voor PostgreSQL Single-server met Azure-portal

In dit artikel wordt beschreven hoe u een Azure Database voor PostgreSQL Single-server configureren om alle openbare configuraties te weigeren en alleen verbindingen via privéeindpunten toe te staan om de netwerkbeveiliging verder te verbeteren.

## <a name="prerequisites"></a>Vereisten

Om deze handleiding te voltooien, moet u het:

* Een [Azure-database voor PostgreSQL Single-server](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Toegang tot openbare netwerken weigeren instellen

Volg de volgende stappen om PostgreSQL Single server Deny Public Network Access in te stellen:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)uw bestaande Azure-database voor PostgreSQL Single-server.

1. Klik op de pagina PostgreSQL Single server onder **Instellingen**op **Verbindingsbeveiliging** om de pagina met verbindingsbeveiliging te openen.

1. Selecteer in **Openbare netwerktoegang weigeren**de optie **Ja** om openbare toegang voor uw PostgreSQL Single-server te weigeren.

    ![Azure Database voor PostgreSQL Single server Deny network access Azure Azure Database for PostgreSQL Single server Deny network access Azure Database for PostgreSQL Single server Deny network access Azure](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Klik op **Opslaan** om de wijzigingen op te slaan.

1. Een melding bevestigt dat de verbindingsbeveiligingsinstelling is ingeschakeld.

    ![Azure Database voor PostgreSQL Single server Succes netwerktoegang weigeren](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over statistieken](howto-alert-on-metric.md).