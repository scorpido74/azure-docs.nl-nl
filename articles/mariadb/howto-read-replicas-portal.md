---
title: Lees replica's maken en beheren in Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u in Azure Database for MariaDB Lees replica's instelt en beheert met behulp van de portal
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a7e7beb3cdef7551a1bce2a693b8044ea6ed8946
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907685"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Lees replica's maken en beheren in Azure Database for MariaDB met behulp van de Azure Portal

In dit artikel leert u hoe u in de Azure Database for MariaDB-service Lees replica's maakt en beheert met behulp van de Azure Portal.

> [!IMPORTANT]
> U kunt een lees replica maken in dezelfde regio als uw hoofd server of in andere Azure-regio's van uw keuze. Het lezen van replica's (dezelfde regio en kruis regio) bevindt zich momenteel in de open bare preview.

## <a name="prerequisites"></a>Vereisten

- Een [Azure database for MariaDB-server](quickstart-create-mariadb-server-database-using-azure-portal.md) die wordt gebruikt als de hoofd server.

> [!IMPORTANT]
> De functie voor het lezen van replica's is alleen beschikbaar voor Azure Database for MariaDB-servers in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen. Controleer of de hoofd-server in een van deze Prijscategorieën.

## <a name="create-a-read-replica"></a>Maken van een replica lezen

Een lees replica-server kan worden gemaakt met behulp van de volgende stappen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

2. Selecteer de bestaande Azure Database for MariaDB-server die u wilt gebruiken als een Master. Deze actie opent u de **overzicht** pagina.

3. Selecteer **replicatie** in het menu onder **instellingen**.

4. Selecteer **replica toevoegen**.

   ![Azure Database for MariaDB-replicatie](./media/howto-read-replica-portal/add-replica.png)

5. Voer een naam in voor de replica server.

    ![Azure Database for MariaDB-replica naam](./media/howto-read-replica-portal/replica-name.png)

6. Selecteer de locatie voor de replica server. De standaard locatie is dezelfde als die van de hoofd server.

    ![Azure Database for MariaDB-replica locatie](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Ga naar het [artikel concepten van replica's lezen](concepts-read-replicas.md)voor meer informatie over de regio's die u kunt maken in de replica. 

7. Selecteer **OK** om te bevestigen dat u de replica wilt maken.

> [!NOTE]
> Lezen-replica's worden gemaakt met de configuratie van de dezelfde server als de master. De configuratie van de replica-server kan worden gewijzigd nadat deze is gemaakt. Het wordt aanbevolen dat de configuratie van de replica-server moet worden opgeslagen op de waarden gelijk zijn aan of groter zijn dan het model om te controleren of dat de replica kan houden met de master.

Zodra de replica server is gemaakt, kan deze worden weer gegeven op de Blade **replicatie** .

   ![Azure Database for MariaDB-lijst replica's](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replica-server stoppen

> [!IMPORTANT]
> Replicatie naar een server stoppen is niet ongedaan worden gemaakt. Wanneer u replicatie tussen een model en de replica is gestopt, kunnen deze kan niet ongedaan worden gemaakt. De replica-server vervolgens wordt een zelfstandige server en biedt nu ondersteuning voor zowel lees- en schrijfbewerkingen. Deze server kan niet opnieuw worden gemaakt in een replica.

Voer de volgende stappen uit om de replicatie tussen een Master en een replica server te stoppen met de Azure Portal:

1. Selecteer in de Azure Portal uw Master Azure Database for MariaDB-server. 

2. Selecteer **replicatie** in het menu onder **instellingen**.

3. Selecteer de replica server waarvoor u de replicatie wilt stoppen.

   ![Azure Database for MariaDB-replicatie stoppen server selecteren](./media/howto-read-replica-portal/stop-replication-select.png)

4. Selecteer **Replicatie stoppen**.

   ![Azure Database for MariaDB-replicatie stoppen](./media/howto-read-replica-portal/stop-replication.png)

5. Bevestig dat u de replicatie wilt stoppen door op **OK**te klikken.

   ![Azure Database for MariaDB-replicatie stoppen bevestigen](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Een replica-server verwijderen

Voer de volgende stappen uit om een lees replica-server te verwijderen uit de Azure Portal:

1. Selecteer in de Azure Portal uw Master Azure Database for MariaDB-server.

2. Selecteer **replicatie** in het menu onder **instellingen**.

3. Selecteer de replica server die u wilt verwijderen.

   ![Azure Database for MariaDB-replica verwijderen server selecteren](./media/howto-read-replica-portal/delete-replica-select.png)

4. **Replica verwijderen** selecteren

   ![Azure Database for MariaDB-replica verwijderen](./media/howto-read-replica-portal/delete-replica.png)

5. Typ de naam van de replica en klik op **verwijderen** om de verwijdering van de replica te bevestigen.  

   ![Azure Database for MariaDB-replica verwijderen bevestigen](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Een hoofd-server verwijderen

> [!IMPORTANT]
> Verwijderen van een hoofd-server-replicatie naar alle replicaservers stopt en Hiermee verwijdert u de hoofd-server zelf. Replica-servers worden zelfstandige servers die bieden nu ondersteuning voor zowel lees- en schrijfbewerkingen.

Als u een master-server wilt verwijderen uit de Azure Portal, gebruikt u de volgende stappen:

1. Selecteer in de Azure Portal uw Master Azure Database for MariaDB-server.

2. Selecteer **verwijderen**in het **overzicht**.

   ![Azure Database for MariaDB-Master verwijderen](./media/howto-read-replica-portal/delete-master-overview.png)

3. Typ de naam van de hoofd server en klik op **verwijderen** om de verwijdering van de hoofd server te bevestigen.  

   ![Azure Database for MariaDB-Master verwijderen](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Replicatie controleren

1. Selecteer in de [Azure Portal](https://portal.azure.com/)de replica Azure database for MariaDB server die u wilt bewaken.

2. Onder de sectie **bewaking** van de zijbalk selecteert u **metrische gegevens**:

3. Selecteer **replicatie vertraging in seconden in** de vervolg keuzelijst met beschik bare metrische gegevens.

   ![Replicatie vertraging selecteren](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Selecteer het tijds bereik dat u wilt weer geven. In de onderstaande afbeelding wordt een tijds bereik van 30 minuten geselecteerd.

   ![Tijds bereik selecteren](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. De replicatie vertraging voor het geselecteerde tijds bereik weer geven. In de onderstaande afbeelding wordt de laatste 30 minuten voor een grote werk belasting weer gegeven.

   ![Tijds bereik selecteren](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [lezen replica's](concepts-read-replicas.md)