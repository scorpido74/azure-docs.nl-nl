---
title: Openbare netwerktoegang weigeren - Azure-portal - Azure-database voor MySQL
description: Meer informatie over het configureren van Verweertoegang voor openbare netwerken met Azure-portal voor uw Azure Database voor MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b5f93f3a3583900810ca75f925c6a88df9102652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79374951"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Openbare netwerktoegang weigeren in Azure-database voor MySQL met Azure-portal

In dit artikel wordt beschreven hoe u een Azure Database voor MySQL-server configureren om alle openbare configuraties te weigeren en alleen verbindingen via privéeindpunten toe te staan om de netwerkbeveiliging verder te verbeteren.

## <a name="prerequisites"></a>Vereisten

Om deze handleiding te voltooien, moet u het:

* Een [Azure-database voor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Toegang tot openbare netwerken weigeren instellen

Volg de volgende stappen om MySQL-server Deny Public Network Access in te stellen:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)uw bestaande Azure-database voor MySQL-server.

1. Klik op de pagina MySQL-server onder **Instellingen**op **Verbindingsbeveiliging** om de pagina met verbindingsbeveiliging te openen.

1. Selecteer In **Openbare netwerktoegang weigeren**de optie **Ja** om openbare toegang voor uw MySQL-server te weigeren.

    ![Azure Database voor MySQL Deny-netwerktoegang](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. Klik op **Opslaan** om de wijzigingen op te slaan.

1. Een melding bevestigt dat de verbindingsbeveiligingsinstelling is ingeschakeld.

    ![Azure Database voor succes met MySQL Deny-netwerktoegang](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over statistieken](howto-alert-on-metric.md).