---
title: Lees-replica's beheren - Azure portal - Azure Database for PostgreSQL - Single Server
description: Meer informatie over het beheren van leesreplica's Azure Database voor PostgreSQL - Single Server vanuit de Azure-portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: dd79618b8d9f016c92166edb9ecdb0bfb113947e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768947"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Lees-replica's maken en beheren in Azure Database voor PostgreSQL - Eén server vanuit de Azure-portal

In dit artikel leert u hoe u leesreplica's maakt en beheert in Azure Database voor PostgreSQL vanuit de Azure-portal. Zie het [overzicht](concepts-read-replicas.md)voor meer informatie over gelezen replica's.


## <a name="prerequisites"></a>Vereisten
Een [Azure-database voor PostgreSQL-server](quickstart-create-server-database-portal.md) als hoofdserver.

## <a name="prepare-the-master-server"></a>De hoofdserver voorbereiden
Deze stappen moeten worden gebruikt om een hoofdserver voor te bereiden in de lagen Algemeen Doel of Geheugengeoptimaliseerd. De hoofdserver is voorbereid op replicatie door de parameter azure.replication_support in te stellen. Wanneer de replicatieparameter wordt gewijzigd, is een serveropnieuw opstarten vereist om de wijziging van kracht te laten worden. In de Azure-portal worden deze twee stappen ingekapseld door één knop, **Replicatieondersteuning inschakelen**.

1. Selecteer in de Azure-portal de bestaande Azure-database voor PostgreSQL-server die als stramien moet worden gebruikt.

2. Selecteer **replicatie**op de zijbalk van de server onder **INSTELLINGEN**.

> [!NOTE] 
> Als u **replicatieondersteuning** grijs uitschakelen ziet, zijn de replicatieinstellingen standaard al ingesteld op uw server. U de volgende stappen overslaan en een gelezen replica maken. 

3. Selecteer **Replicatieondersteuning inschakelen**. 

   ![Replicatieondersteuning inschakelen](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Bevestig dat u replicatieondersteuning wilt inschakelen. Met deze bewerking wordt de hoofdserver opnieuw opgestart. 

   ![Replicatieondersteuning inschakelen bevestigen](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. U ontvangt twee Azure-portalmeldingen zodra de bewerking is voltooid. Er is één melding voor het bijwerken van de serverparameter. Er is nog een melding voor de server opnieuw opstarten die onmiddellijk volgt.

   ![Succesmeldingen - inschakelen](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Vernieuw de Azure-portalpagina om de werkbalk Replicatie bij te werken. U nu leesreplica's voor deze server maken.

   ![Bijgewerkte werkbalk](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Replicatieondersteuning inschakelen is een eenmalige bewerking per hoofdserver. Voor uw gemak is er een knop **Replicatieondersteuning uitschakelen.** We raden u aan replicatieondersteuning uit te schakelen, tenzij u er zeker van bent dat u nooit een replica op deze hoofdserver zult maken. U replicatieondersteuning niet uitschakelen terwijl de hoofdserver bestaande replica's heeft.


## <a name="create-a-read-replica"></a>Een gelezen replica maken
Voer de volgende stappen uit om een gelezen replica te maken:

1. Selecteer de bestaande Azure Database voor PostgreSQL-server die u als hoofdserver wilt gebruiken. 

2. Selecteer **replicatie**op de zijbalk van de server onder **INSTELLINGEN**.

3. Selecteer **Replica toevoegen**.

   ![Een replica toevoegen](./media/howto-read-replicas-portal/add-replica.png)

4. Voer een naam in voor de gelezen replica. 

    ![De replica een naam geven](./media/howto-read-replicas-portal/name-replica.png)

5. Selecteer een locatie voor de replica. De standaardlocatie is hetzelfde als die van de hoofdserver.

    ![Een locatie selecteren](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > Ga voor meer informatie over welke regio's u een replica maken in het [artikel Voor replicaconcepten](concepts-read-replicas.md). 

6. Selecteer **OK** om de creatie van de replica te bevestigen.

Er wordt een replica gemaakt met dezelfde reken- en opslaginstellingen als het stramien. Nadat een replica is gemaakt, kunnen verschillende instellingen onafhankelijk van de hoofdserver worden gewijzigd: rekengeneratie, vCores, opslag en back-upbewaarperiode. De prijscategorie kan ook onafhankelijk worden gewijzigd, behalve van of naar de basislaag.

> [!IMPORTANT]
> Voordat een hoofdserverinstelling wordt bijgewerkt naar een nieuwe waarde, werkt u de replica-instelling bij naar een evengrote of grotere waarde. Met deze actie kan de replica gelijke tred houden met eventuele wijzigingen in het model.

Nadat de gelezen replica is gemaakt, kan deze worden weergegeven vanuit het **replicatievenster:**

![De nieuwe replica weergeven in het replicatievenster](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Replicatie stoppen
U de replicatie tussen een hoofdserver en een gelezen replica stoppen.

> [!IMPORTANT]
> Nadat u de replicatie naar een hoofdserver en een gelezen replica hebt gestopt, kan deze niet ongedaan worden gemaakt. De gelezen replica wordt een standalone server die zowel leest als schrijft ondersteunt. De standalone server kan niet opnieuw worden omgezet in een replica.

Voer de volgende stappen uit om de replicatie tussen een hoofdserver en een gelezen replica van de Azure-portal te stoppen:

1. Selecteer in de Azure-portal uw hoofdAzure Database voor PostgreSQL-server.

2. Selecteer **Replicatie**in het servermenu onder **INSTELLINGEN**.

3. Selecteer de replicaserver waarvoor u de replicatie wilt stoppen.

   ![De replica selecteren](./media/howto-read-replicas-portal/select-replica.png)
 
4. Selecteer **Replicatie stoppen**.

   ![Replicatie stoppen selecteren](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Selecteer **OK** om de replicatie te stoppen.

   ![Replicatie bevestigen om de replicatie te stoppen](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Een hoofdserver verwijderen
Als u een hoofdserver wilt verwijderen, gebruikt u dezelfde stappen als het verwijderen van een zelfstandige Azure-database voor PostgreSQL-server. 

> [!IMPORTANT]
> Wanneer u een hoofdserver verwijdert, wordt replicatie naar alle gelezen replica's gestopt. De gelezen replica's worden standalone servers die nu ondersteuning voor zowel leest en schrijft.

Voer de volgende stappen uit om een server uit de Azure-portal te verwijderen:

1. Selecteer in de Azure-portal uw hoofdAzure Database voor PostgreSQL-server.

2. Open de **pagina Overzicht** voor de server. Selecteer **Verwijderen**.

   ![Selecteer op de pagina serveroverzicht de optie om de hoofdserver te verwijderen](./media/howto-read-replicas-portal/delete-server.png)
 
3. Voer de naam in van de hoofdserver die u wilt verwijderen. Selecteer **Verwijderen** om verwijdering van de hoofdserver te bevestigen.

   ![Bevestigen dat de hoofdserver moet worden verwijderd](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Een replica verwijderen
U een gelezen replica verwijderen die vergelijkbaar is met de manier waarop u een hoofdserver verwijdert.

- Open in de Azure-portal de pagina **Overzicht** voor de gelezen replica. Selecteer **Verwijderen**.

   ![Selecteer op de pagina replicaoverzicht om de replica te verwijderen](./media/howto-read-replicas-portal/delete-replica.png)
 
U de gelezen replica ook uit het **replicatievenster** verwijderen door de volgende stappen te volgen:

1. Selecteer in de Azure-portal uw hoofdAzure Database voor PostgreSQL-server.

2. Selecteer **Replicatie**in het servermenu onder **INSTELLINGEN**.

3. Selecteer de gelezen replica die u wilt verwijderen.

   ![Selecteer de replica die u wilt verwijderen](./media/howto-read-replicas-portal/select-replica.png)
 
4. Selecteer **Replica verwijderen**.

   ![Replica verwijderen selecteren](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Voer de naam in van de replica die u wilt verwijderen. Selecteer **Verwijderen** om de verwijdering van de replica te bevestigen.

   ![Te replica verwijderen](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Een replica controleren
Er zijn twee statistieken beschikbaar om gelezen replica's te controleren.

### <a name="max-lag-across-replicas-metric"></a>Maximale vertraging over replica's statistiek
De statistiek **Max Lag Across Replicas** toont de vertraging in bytes tussen de hoofdserver en de meest achterblijvende replica. 

1.  Selecteer in de Azure-portal de hoofdAzure Database voor PostgreSQL-server.

2.  Selecteer **Metrische gegevens**. Selecteer **in** het venster Statistieken de optie **Max Lag Across Replica's**.

    ![De maximale vertraging tussen replica's bewaken](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Selecteer **Max**voor uw **aggregatie.**


### <a name="replica-lag-metric"></a>Replica Lag-statistiek
De statistiek **Replica Lag** toont de tijd sinds de laatst afgespeelde transactie op een replica. Als er geen transacties plaatsvinden op uw master, weerspiegelt de statistiek deze vertraging.

1. Selecteer in de Azure-portal de Azure Database voor PostgreSQL-leesreplica.

2. Selecteer **Metrische gegevens**. Selecteer **Replicalag** in **Replica Lag**het venster Statistieken .

   ![De replicavertraging controleren](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Selecteer **Max**voor uw **aggregatie.** 
 
## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [leesreplica's in Azure Database voor PostgreSQL](concepts-read-replicas.md).
* Meer informatie over het [maken en beheren van leesreplica's in de Azure CLI- en REST-API.](howto-read-replicas-cli.md)