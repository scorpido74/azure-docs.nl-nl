---
title: Azure Database for MySQL beheren-Azure Portal
description: Meer informatie over het beheren van een Azure Database for MySQL-server vanuit de Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 286209673e5743d08ddaa2fed2f507f84d622ea6
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534052"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Een Azure Database for MySQL-server beheren met behulp van de Azure Portal
Dit artikel laat u zien hoe u uw Afire-Data Base voor MySQL-servers kunt beheren. Beheer taken zijn onder andere berekening en opslag schalen, beheerders wachtwoord opnieuw instellen en server details weer geven.

## <a name="sign-in"></a>Aanmelden
Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-server"></a>Een server maken
Ga naar de [Quick](quickstart-create-mysql-server-database-using-azure-portal.md) start voor informatie over het maken en aan de slag met een Azure database for mysql-server.

## <a name="scale-compute-and-storage"></a>Schaal berekening en opslag

Nadat de server is gemaakt, kunt u de schaal aanpassen tussen de lagen Algemeen en geoptimaliseerd voor geheugen als uw behoeften veranderen. U kunt reken kracht en geheugen ook schalen door de vCores te verg Roten of te verkleinen. Opslag kan omhoog worden geschaald (u kunt de opslag echter niet omlaag schalen).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Schaal tussen Algemeen en geoptimaliseerde geheugen lagen

U kunt schalen van Algemeen naar geoptimaliseerd voor geheugen en vice versa. Wijzigen van en van de Basic-laag nadat het maken van de server niet wordt ondersteund. 

1. Selecteer uw server in de Azure Portal. Selecteer de **prijs categorie**die u in de sectie **instellingen** bevindt.

2. Selecteer **Algemeen** of **geoptimaliseerd geheugen**, afhankelijk van wat u wilt schalen. 

    ![wijziging-prijs categorie](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > Wanneer u de lagen wijzigt, wordt de server opnieuw opgestart.

4. Selecteer **OK** om de wijzigingen op te slaan.


### <a name="scale-vcores-up-or-down"></a>VCores omhoog of omlaag schalen

1. Selecteer uw server in de Azure Portal. Selecteer de **prijs categorie**die u in de sectie **instellingen** bevindt.

2. Wijzig de instelling **vCore** door de schuif regelaar naar de gewenste waarde te verplaatsen.

    ![schalen-compute](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > Het schalen van vCores zorgt ervoor dat de server opnieuw wordt opgestart.

3. Selecteer **OK** om de wijzigingen op te slaan.


### <a name="scale-storage-up"></a>Opslag omhoog schalen

1. Selecteer uw server in de Azure Portal. Selecteer de **prijs categorie**die u in de sectie **instellingen** bevindt.

2. Wijzig de **opslag** instelling door de schuif regelaar omhoog te verplaatsen naar de gewenste waarde.

    ![schaal-opslag](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > De opslag kan niet omlaag worden geschaald.

3. Selecteer **OK** om de wijzigingen op te slaan.


## <a name="update-admin-password"></a>Beheerders wachtwoord bijwerken
U kunt het wacht woord van de beheerdersrol wijzigen met behulp van de Azure Portal.

1. Selecteer uw server in de Azure Portal. Selecteer **wacht woord opnieuw instellen**in het venster **overzicht** .

   ![overview](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Voer een nieuw wacht woord in en bevestig het wacht woord. In het tekstvak wordt u gevraagd om de vereisten voor wachtwoord complexiteit.

   ![Opnieuw instellen-wacht woord](./media/howto-create-manage-server-portal/reset-password.png)

3. Selecteer **OK** om het nieuwe wacht woord op te slaan.


## <a name="delete-a-server"></a>Een server verwijderen

U kunt uw server verwijderen als u deze niet meer nodig hebt. 

1. Selecteer uw server in de Azure Portal. Selecteer **verwijderen**in het venster **overzicht** .

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. Typ de naam van de server in het invoervak om te bevestigen dat dit de server is die u wilt verwijderen.

    ![bevestigen-verwijderen](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > Het verwijderen van een server is onomkeerbaar.

3. Selecteer **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [back-ups en server herstel](howto-restore-server-portal.md)
- Meer informatie over [Opties voor afstemmen en controleren in azure database for MySQL](concepts-monitoring.md)