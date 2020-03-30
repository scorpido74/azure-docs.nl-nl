---
title: Automatische opslag voor het opslaan van producten - Azure portal - Azure Database voor MySQL
description: In dit artikel wordt beschreven hoe u automatische opslag voor Azure Database voor MySQL inschakelen met Azure-portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ddbcb0143a4f84d9e0f3dc2fedf780fca6637692
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062505"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Opslag automatisch laten groeien in Azure Database voor MySQL met behulp van de Azure-portal
In dit artikel wordt beschreven hoe u een Azure Database voor MySQL-serveropslag configureren om te groeien zonder dat dit gevolgen heeft voor de werkbelasting.

Wanneer een server de toegewezen opslaglimiet bereikt, wordt de server gemarkeerd als alleen-lezen. Als u echter de opslagautomatisch inschakelt, neemt de serveropslag toe om de groeiende gegevens te kunnen verwerken. Voor servers met minder dan 100 GB ingerichte opslag wordt de ingerichte opslaggrootte met 5 GB verhoogd zodra de gratis opslag lager is dan 1 GB of 10% van de ingerichte opslag. Voor servers met meer dan 100 GB ingerichte opslag wordt de ingerichte opslaggrootte met 5% vergroot wanneer de vrije opslagruimte minder dan 5% van de ingerichte opslaggrootte bedraagt. De maximale opslaglimieten zoals [hier](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) gespecificeerd zijn van toepassing.

## <a name="prerequisites"></a>Vereisten
Om deze handleiding te voltooien, moet u het:
- Een [Azure-database voor MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Opslag automatisch laten groeien inschakelen 

Volg de volgende stappen om de automatische groei van MySQL-serveropslag in te stellen:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)uw bestaande Azure-database voor MySQL-server.

2. Klik op de pagina MySQL-server onder Kop **Instellingen** op **Prijslaag** om de pagina Prijslaag te openen.

3. Selecteer in de sectie Autogroei de optie **Ja** om opslagautomatisch te laten groeien.

    ![Azure Database voor MySQL - Settings_Pricing_tier - Autogroei](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Klik op **OK** om de wijzigingen op te slaan.

5. Een melding bevestigt dat automatisch groeien is ingeschakeld.

    ![Azure Database voor MySQL - succes met autogroei](./media/howto-auto-grow-storage-portal/5-auto-grow-success.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over statistieken](howto-alert-on-metric.md).
