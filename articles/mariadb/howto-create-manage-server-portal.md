---
title: Server beheren - Azure-portal - Azure-database voor MariaDB
description: Meer informatie over het beheren van een Azure-database voor MariaDB-server vanuit de Azure-portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 43004f8c52bbec17f78ed4be024cf75224dbd179
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530237"
---
# <a name="manage-an-azure-database-for-mariadb-server-using-the-azure-portal"></a>Een Azure-database voor MariaDB-server beheren met de Azure-portal
In dit artikel ziet u hoe u uw Azure Database voor MariaDB-servers beheert. Beheertaken omvatten compute- en opslagschaling, het opnieuw instellen van beheerderswachtwoorden en het weergeven van servergegevens.

## <a name="sign-in"></a>Aanmelden
Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-a-server"></a>Een server maken
Ga naar de [quickstart](quickstart-create-mariadb-server-database-using-azure-portal.md) om te leren hoe u een Azure-database voor MariaDB-server maakt en aan de slag.

## <a name="scale-compute-and-storage"></a>Rekenkracht en opslag schalen

Na het maken van de server u schalen tussen de lagen Algemeen Doel en Geheugengeoptimaliseerd naarmate uw behoeften veranderen. U ook berekenen en geheugen schalen door vCores te verhogen of te verlagen. Opslag kan worden opgeschaald (u de opslag echter niet afbreken).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Schalen tussen algemene doel- en geheugengeoptimaliseerde lagen

U schalen van Algemeen Doel naar Geheugengeoptimaliseerd en vice versa. Als u na het maken van de server van en naar de basislaag gaat, wordt deze niet ondersteund. 

1. Selecteer uw server in de Azure-portal. Selecteer **De prijslaag**, in de sectie **Instellingen.**

2. Selecteer **Algemeen doel** of geoptimaliseerd **geheugen,** afhankelijk van waarnaar u schaalt. 

    ![change-pricing-tier](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > Als u lagen wijzigt, wordt de server opnieuw opgestart.

4. Selecteer **OK** om wijzigingen op te slaan.


### <a name="scale-vcores-up-or-down"></a>VCores omhoog of omlaag schalen

1. Selecteer uw server in de Azure-portal. Selecteer **De prijslaag**, in de sectie **Instellingen.**

2. Wijzig de **vCore-instelling** door de schuifregelaar naar de gewenste waarde te verplaatsen.

    ![schaal-compute](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > Het schalen van vCores zorgt ervoor dat een server opnieuw wordt opgestart.

3. Selecteer **OK** om wijzigingen op te slaan.


### <a name="scale-storage-up"></a>Opslag opschalen

1. Selecteer uw server in de Azure-portal. Selecteer **De prijslaag**, in de sectie **Instellingen.**

2. Wijzig de **opslaginstelling** door de schuifregelaar naar de gewenste waarde te verplaatsen.

    ![schaalopslag](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > Opslag kan niet worden verkleind.

3. Selecteer **OK** om wijzigingen op te slaan.


## <a name="update-admin-password"></a>Beheerderswachtwoord bijwerken
U het wachtwoord van de beheerdersrol wijzigen met behulp van de Azure-portal.

1. Selecteer uw server in de Azure-portal. Selecteer **in** het venster Overzicht het **wachtwoord opnieuw instellen**.

   ![overview](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Voer een nieuw wachtwoord in en bevestig het wachtwoord. In het tekstvak wordt u gevraagd naar vereisten voor de complexiteit van wachtwoorden.

   ![reset-wachtwoord](./media/howto-create-manage-server-portal/reset-password.png)

3. Selecteer **OK** om het nieuwe wachtwoord op te slaan.


## <a name="delete-a-server"></a>Een server verwijderen

U uw server verwijderen als u deze niet meer nodig hebt. 

1. Selecteer uw server in de Azure-portal. Selecteer **verwijderen**in het venster **Overzicht** .

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. Typ de naam van de server in het invoervak om te bevestigen dat dit de server is die u wilt verwijderen.

    ![bevestigen-verwijderen](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > Het verwijderen van een server is onomkeerbaar.

3. Selecteer **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [back-ups en serverherstel](howto-restore-server-portal.md)
- Meer informatie over [tuning- en bewakingsopties in Azure Database voor MariaDB](concepts-monitoring.md)
