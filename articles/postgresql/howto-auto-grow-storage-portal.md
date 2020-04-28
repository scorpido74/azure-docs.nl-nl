---
title: Opslag Azure Portal automatisch uitbreiden-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u de automatische groei van opslag kunt configureren met behulp van de Azure Portal in Azure Database for PostgreSQL-enkele server
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 5e4f9d68d02edf456394d4ce10b7b6af5f8643d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74769231"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Opslag automatisch uitbreiden met de Azure Portal in Azure Database for PostgreSQL-enkele server
In dit artikel wordt beschreven hoe u een Azure Database for PostgreSQL Server-opslag kunt configureren om te groeien zonder dat dit van invloed is op de werk belasting.

Wanneer een server de toegewezen opslag limiet bereikt, is de server gemarkeerd als alleen-lezen. Als u automatisch verg Roten van opslag inschakelt, neemt de server opslag echter toe om de groeiende gegevens te verwerken. Voor servers met een ingerichte opslag van minder dan 100 GB wordt de ingerichte opslag grootte met 5 GB verhoogd zodra de beschik bare opslag onder het hoogste van 1 GB of 10% van de ingerichte opslag ligt. Voor servers met meer dan 100 GB ingerichte opslag wordt de ingerichte opslag grootte verhoogd met 5% wanneer de beschik bare opslag ruimte lager is dan 5% van de ingerichte opslag grootte. De maximale opslag limieten die [hier](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) zijn opgegeven, zijn van toepassing.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze hand leiding te volt ooien:
- Een [Azure database for postgresql server](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Opslag automatisch verg Roten inschakelen 

Voer de volgende stappen uit om PostgreSQL-Server opslag automatisch uitbreiden in te stellen:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)uw bestaande Azure database for postgresql-server.

2. Klik op de pagina PostgreSQL-server onder **instellingen**op **prijs categorie** om de pagina prijs categorie te openen.

3. In de sectie **automatische groei** selecteert u **Ja** om automatische groei van opslag in te scha kelen.

    ![Azure Database for PostgreSQL-Settings_Pricing_tier-automatische groei](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Klik op **OK** om de wijzigingen op te slaan.

5. Bij een melding wordt bevestigd dat automatisch uitbreiden is ingeschakeld.

    ![Azure Database for PostgreSQL-automatisch groei geslaagd](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-on-metric.md).
