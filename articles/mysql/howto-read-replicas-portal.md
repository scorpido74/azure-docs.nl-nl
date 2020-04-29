---
title: Lees replica's beheren-Azure Portal-Azure Database for MySQL
description: Meer informatie over het instellen en beheren van Lees replica's in Azure Database for MySQL met behulp van de Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: b7226e5ae8c468339e02dbe87e279266e4609da8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063476"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Lees replica's maken en beheren in Azure Database for MySQL met behulp van de Azure Portal

In dit artikel leert u hoe u in de Azure Database for MySQL-service Lees replica's maakt en beheert met behulp van de Azure Portal.

## <a name="prerequisites"></a>Vereisten

- Een [Azure database for mysql-server](quickstart-create-mysql-server-database-using-azure-portal.md) die wordt gebruikt als de hoofd server.

> [!IMPORTANT]
> De functie voor het lezen van replica's is alleen beschikbaar voor Azure Database for MySQL-servers in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen. Zorg ervoor dat de hoofd server zich in een van deze prijs categorieën bevindt.

## <a name="create-a-read-replica"></a>Een lees replica maken

Een lees replica-server kan worden gemaakt met behulp van de volgende stappen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

2. Selecteer de bestaande Azure Database for MySQL-server die u wilt gebruiken als een Master. Met deze actie wordt de pagina **overzicht** geopend.

3. Selecteer **replicatie** in het menu onder **instellingen**.

4. Selecteer **replica toevoegen**.

   ![Azure Database for MySQL-replicatie](./media/howto-read-replica-portal/add-replica.png)

5. Voer een naam in voor de replica server.

    ![Azure Database for MySQL-replica naam](./media/howto-read-replica-portal/replica-name.png)

6. Selecteer de locatie voor de replica server. De standaard locatie is dezelfde als die van de hoofd server.

    ![Azure Database for MySQL-replica locatie](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Ga naar het [artikel concepten van replica's lezen](concepts-read-replicas.md)voor meer informatie over de regio's die u kunt maken in de replica. 

7. Selecteer **OK** om te bevestigen dat u de replica wilt maken.

> [!NOTE]
> Lees replica's worden gemaakt met dezelfde server configuratie als de Master. De configuratie van de replica server kan worden gewijzigd nadat deze is gemaakt. Het is raadzaam om de configuratie van de replica server te behouden in gelijke of hogere waarden dan de Master om ervoor te zorgen dat de replica kan blijven werken met de Master.

Zodra de replica server is gemaakt, kan deze worden weer gegeven op de Blade **replicatie** .

   ![Azure Database for MySQL-lijst replica's](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replica server stoppen

> [!IMPORTANT]
> Het stoppen van de replicatie naar een server is onomkeerbaar. Zodra de replicatie tussen een hoofd-en replica is gestopt, kan deze niet meer ongedaan worden gemaakt. De replica-server wordt vervolgens een zelfstandige server en ondersteunt nu lezen en schrijven. Deze server kan niet opnieuw in een replica worden gemaakt.

Voer de volgende stappen uit om de replicatie tussen een Master en een replica server te stoppen met de Azure Portal:

1. Selecteer in de Azure Portal uw Master Azure Database for MySQL-server. 

2. Selecteer **replicatie** in het menu onder **instellingen**.

3. Selecteer de replica server waarvoor u de replicatie wilt stoppen.

   ![Azure Database for MySQL-replicatie stoppen server selecteren](./media/howto-read-replica-portal/stop-replication-select.png)

4. Selecteer **Replicatie stoppen**.

   ![Azure Database for MySQL-replicatie stoppen](./media/howto-read-replica-portal/stop-replication.png)

5. Bevestig dat u de replicatie wilt stoppen door op **OK**te klikken.

   ![Azure Database for MySQL-replicatie stoppen bevestigen](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Een replica server verwijderen

Voer de volgende stappen uit om een lees replica-server te verwijderen uit de Azure Portal:

1. Selecteer in de Azure Portal uw Master Azure Database for MySQL-server.

2. Selecteer **replicatie** in het menu onder **instellingen**.

3. Selecteer de replica server die u wilt verwijderen.

   ![Azure Database for MySQL-replica verwijderen server selecteren](./media/howto-read-replica-portal/delete-replica-select.png)

4. **Replica verwijderen** selecteren

   ![Azure Database for MySQL-replica verwijderen](./media/howto-read-replica-portal/delete-replica.png)

5. Typ de naam van de replica en klik op **verwijderen** om de verwijdering van de replica te bevestigen.  

   ![Azure Database for MySQL-replica verwijderen bevestigen](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Een hoofd server verwijderen

> [!IMPORTANT]
> Als u een hoofdserver verwijdert, wordt de replicatie naar alle replicaservers gestopt en wordt de hoofdserver zelf verwijderd. Replicaservers worden zelfstandige servers die nu zowel lees-als schrijfbewerkingen ondersteunen.

Als u een master-server wilt verwijderen uit de Azure Portal, gebruikt u de volgende stappen:

1. Selecteer in de Azure Portal uw Master Azure Database for MySQL-server.

2. Selecteer **verwijderen**in het **overzicht**.

   ![Azure Database for MySQL-Master verwijderen](./media/howto-read-replica-portal/delete-master-overview.png)

3. Typ de naam van de hoofd server en klik op **verwijderen** om de verwijdering van de hoofd server te bevestigen.  

   ![Azure Database for MySQL-Master verwijderen](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Replicatie controleren

1. Selecteer in de [Azure Portal](https://portal.azure.com/)de replica Azure database for mysql server die u wilt bewaken.

2. Onder de sectie **bewaking** van de zijbalk selecteert u **metrische gegevens**:

3. Selecteer **replicatie vertraging in seconden in** de vervolg keuzelijst met beschik bare metrische gegevens.

   ![Replicatie vertraging selecteren](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Selecteer het tijds bereik dat u wilt weer geven. In de onderstaande afbeelding wordt een tijds bereik van 30 minuten geselecteerd.

   ![Tijds bereik selecteren](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. De replicatie vertraging voor het geselecteerde tijds bereik weer geven. In de onderstaande afbeelding wordt de laatste 30 minuten weer gegeven.

   ![Tijds bereik selecteren](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [lezen van replica's](concepts-read-replicas.md)