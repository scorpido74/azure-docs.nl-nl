---
title: Automatische opslag voor het opslaan van zelfstandigen - Azure-portal - Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt beschreven hoe u opslagautomatisch configureren met behulp van de Azure-portal in Azure Database voor PostgreSQL - Single Server
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 5e4f9d68d02edf456394d4ce10b7b6af5f8643d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769231"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Automatisch opslag laten groeien met de Azure-portal in Azure Database voor PostgreSQL - Enkele server
In dit artikel wordt beschreven hoe u een Azure Database voor PostgreSQL-serveropslag configureren om te groeien zonder dat dit gevolgen heeft voor de werkbelasting.

Wanneer een server de toegewezen opslaglimiet bereikt, wordt de server gemarkeerd als alleen-lezen. Als u echter de opslagautomatisch inschakelt, neemt de serveropslag toe om de groeiende gegevens te kunnen verwerken. Voor servers met minder dan 100 GB ingerichte opslag wordt de ingerichte opslaggrootte met 5 GB verhoogd zodra de gratis opslag lager is dan 1 GB of 10% van de ingerichte opslag. Voor servers met meer dan 100 GB ingerichte opslag wordt de ingerichte opslaggrootte met 5% vergroot wanneer de vrije opslagruimte minder dan 5% van de ingerichte opslaggrootte bedraagt. De maximale opslaglimieten zoals [hier](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) gespecificeerd zijn van toepassing.

## <a name="prerequisites"></a>Vereisten
Om deze handleiding te voltooien, moet u het:
- Een [Azure-database voor PostgreSQL-server](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Opslag automatisch laten groeien inschakelen 

Volg de volgende stappen om postgreSQL-serveropslag automatisch in te stellen:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)uw bestaande Azure-database voor PostgreSQL-server.

2. Klik op de pagina PostgreSQL-server onder **Instellingen**op **Prijslaag** om de pagina prijslaag te openen.

3. Selecteer in de sectie **Autogroei** de optie **Ja** om opslagautomatisch te laten groeien.

    ![Azure Database voor PostgreSQL - Settings_Pricing_tier - Autogroei](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Klik op **OK** om de wijzigingen op te slaan.

5. Een melding bevestigt dat automatisch groeien is ingeschakeld.

    ![Azure Database voor PostgreSQL - succes met autogroei](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over statistieken](howto-alert-on-metric.md).
