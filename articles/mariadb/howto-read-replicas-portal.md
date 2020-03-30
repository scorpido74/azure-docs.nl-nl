---
title: Lees-replica's beheren - Azure portal - Azure Database voor MariaDB
description: In dit artikel wordt beschreven hoe u leesreplica's in Azure Database voor MariaDB instellen en beheren met behulp van de portal
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5c28697b27e9cf910302b7379e1443f7e78e96b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530611"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Lees-replica's maken en beheren in Azure Database voor MariaDB met behulp van de Azure-portal

In dit artikel leert u hoe u leesreplica's maakt en beheert in de Azure Database for MariaDB-service met behulp van de Azure-portal.

## <a name="prerequisites"></a>Vereisten

- Een [Azure-database voor MariaDB-server](quickstart-create-mariadb-server-database-using-azure-portal.md) die wordt gebruikt als hoofdserver.

> [!IMPORTANT]
> De functie leesreplica is alleen beschikbaar voor Azure Database voor MariaDB-servers in de prijzenlagen Algemeen Doel of Geheugengeoptimaliseerd. Zorg ervoor dat de hoofdserver zich in een van deze prijsniveaus bevindt.

## <a name="create-a-read-replica"></a>Een gelezen replica maken

Een leesreplicaserver kan worden gemaakt met de volgende stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer de bestaande Azure Database voor MariaDB-server die u als stramien wilt gebruiken. Met deze actie wordt de pagina **Overzicht** geopend.

3. Selecteer **Replicatie** in het menu onder **INSTELLINGEN**.

4. Selecteer **Replica toevoegen**.

   ![Azure-database voor MariaDB - Replicatie](./media/howto-read-replica-portal/add-replica.png)

5. Voer een naam in voor de replicaserver.

    ![Azure Database voor MariaDB - Replicanaam](./media/howto-read-replica-portal/replica-name.png)

6. Selecteer de locatie voor de replicaserver. De standaardlocatie is hetzelfde als die van de hoofdserver.

    ![Azure Database voor MariaDB - Replicalocatie](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Replicatie tussen regio's is in preview. Ga voor meer informatie over welke regio's u een replica maken in het [artikel Voor replicaconcepten](concepts-read-replicas.md). 

7. Selecteer **OK** om het maken van de replica te bevestigen.

> [!NOTE]
> Leesreplica's worden gemaakt met dezelfde serverconfiguratie als de stramien. De configuratie van de replicaserver kan worden gewijzigd nadat deze is gemaakt. Het wordt aanbevolen dat de configuratie van de replicaserver op gelijke of grotere waarden wordt gehouden dan de master om ervoor te zorgen dat de replica het model kan bijhouden.

Zodra de replicaserver is gemaakt, kan deze worden bekeken vanaf het **replicatieblad.**

   ![Azure Database voor MariaDB - Replica's weergeven](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replicaserver stoppen

> [!IMPORTANT]
> Het stoppen van replicatie naar een server is onomkeerbaar. Zodra replicatie is gestopt tussen een stramien en replica, kan deze niet ongedaan worden gemaakt. De replicaserver wordt dan een standalone server en ondersteunt nu zowel lezen als schrijven. Deze server kan niet opnieuw worden omgezet in een replica.

Als u de replicatie tussen een stramien en een replicaserver van de Azure-portal wilt stoppen, gebruikt u de volgende stappen:

1. Selecteer in de Azure-portal uw hoofdAzure Database voor MariaDB-server. 

2. Selecteer **Replicatie** in het menu onder **INSTELLINGEN**.

3. Selecteer de replicaserver waarvoor u de replicatie wilt stoppen.

   ![Azure Database voor MariaDB - Replicatie selecteren server stoppen](./media/howto-read-replica-portal/stop-replication-select.png)

4. Selecteer **Replicatie stoppen**.

   ![Azure Database voor MariaDB - Replicatie stoppen](./media/howto-read-replica-portal/stop-replication.png)

5. Bevestig dat u de replicatie wilt stoppen door op **OK**te klikken.

   ![Azure Database voor MariaDB - Replicatie stoppen bevestigen](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Een replicaserver verwijderen

Als u een gelezen replicaserver uit de Azure-portal wilt verwijderen, gebruikt u de volgende stappen:

1. Selecteer in de Azure-portal uw hoofdAzure Database voor MariaDB-server.

2. Selecteer **Replicatie** in het menu onder **INSTELLINGEN**.

3. Selecteer de replicaserver die u wilt verwijderen.

   ![Azure Database voor MariaDB - Replica select server verwijderen](./media/howto-read-replica-portal/delete-replica-select.png)

4. Replica **verwijderen selecteren**

   ![Azure-database voor MariaDB - Replica verwijderen](./media/howto-read-replica-portal/delete-replica.png)

5. Typ de naam van de replica en klik op **Verwijderen** om de verwijdering van de replica te bevestigen.  

   ![Azure Database voor MariaDB - Replica bevestigen verwijderen](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Een hoofdserver verwijderen

> [!IMPORTANT]
> Als u een hoofdserver verwijdert, wordt de replicatie naar alle replicaservers gestopt en wordt de hoofdserver zelf verwijderd. Replicaservers worden zelfstandige servers die nu zowel lees-als schrijfbewerkingen ondersteunen.

Als u een hoofdserver uit de Azure-portal wilt verwijderen, gebruikt u de volgende stappen:

1. Selecteer in de Azure-portal uw hoofdAzure Database voor MariaDB-server.

2. Selecteer **Verwijderen**in het **overzicht**.

   ![Azure Database voor MariaDB - Master verwijderen](./media/howto-read-replica-portal/delete-master-overview.png)

3. Typ de naam van de hoofdserver en klik op **Verwijderen** om de verwijdering van de hoofdserver te bevestigen.  

   ![Azure Database voor MariaDB - Master verwijderen](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Replicatie controleren

1. Selecteer in de [Azure-portal](https://portal.azure.com/)de replica Azure Database voor MariaDB-server die u wilt controleren.

2. Selecteer onder de sectie **Controle** van de zijbalk de optie **Metrische gegevens:**

3. Selecteer **Replicatievertraging in enkele seconden** in de vervolgkeuzelijst met beschikbare statistieken.

   ![Replicatievertraging selecteren](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Selecteer het tijdsbereik dat u wilt weergeven. De onderstaande afbeelding selecteert een tijdsbereik van 30 minuten.

   ![Tijdbereik selecteren](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. De replicatievertraging voor het geselecteerde tijdsbereik weergeven. De afbeelding hieronder toont de laatste 30 minuten voor een grote werkbelasting.

   ![Tijdbereik selecteren](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gelezen replica's](concepts-read-replicas.md)