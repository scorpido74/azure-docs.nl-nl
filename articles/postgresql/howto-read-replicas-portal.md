---
title: Lees replica's beheren-Azure Portal-Azure Database for PostgreSQL-één server
description: Meer informatie over het beheren van Lees replica's Azure Database for PostgreSQL-één server van de Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 5e031354d4695a1d7eb6f199e23e74b796273230
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770217"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Lees replica's maken en beheren in Azure Database for PostgreSQL-één server van de Azure Portal

In dit artikel leert u hoe u in Azure Database for PostgreSQL Lees replica's maakt en beheert via de Azure Portal. Zie het [overzicht](concepts-read-replicas.md)voor meer informatie over het lezen van replica's.


## <a name="prerequisites"></a>Vereisten
Een [Azure database for postgresql-server](quickstart-create-server-database-portal.md) als de hoofd server.

## <a name="prepare-the-master-server"></a>De hoofd server voorbereiden
Deze stappen moeten worden gebruikt om een hoofd server voor te bereiden in de lagen Algemeen of geoptimaliseerd voor geheugen. De master server wordt voor bereid voor replicatie door de para meter Azure. replication_support in te stellen. Wanneer de replicatie parameter wordt gewijzigd, moet de server opnieuw worden opgestart om de wijziging van kracht te laten worden. In de Azure Portal worden deze twee stappen ingekapseld door één knop, **ondersteuning voor replicatie inschakelen**.

1. Selecteer in de Azure Portal de bestaande Azure Database for PostgreSQL-server die als een Master moet worden gebruikt.

2. Selecteer op de zijbalk van de server onder **instellingen**de optie **replicatie**.

3. Selecteer **ondersteuning voor replicatie inschakelen**. 

   ![Ondersteuning voor replicatie inschakelen](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Bevestig dat u de ondersteuning voor replicatie wilt inschakelen. Met deze bewerking wordt de hoofd server opnieuw gestart. 

   ![Ondersteuning voor replicatie inschakelen bevestigen](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. Zodra de bewerking is voltooid, ontvangt u twee meldingen over Azure Portal. Er is één melding voor het bijwerken van de server parameter. Er is een andere melding voor de herstart van de server die onmiddellijk volgt.

   ![Geslaagde meldingen-inschakelen](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Vernieuw de Azure Portal pagina om de werk balk replicatie bij te werken. U kunt nu lees replica's maken voor deze server.

   ![Bijgewerkte werk balk](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Het inschakelen van ondersteuning voor replicatie is een eenmalige bewerking per hoofd server. Er wordt een **ondersteunings** knop voor het uitschakelen van de replicatie voor uw gemak geboden. Het is niet raadzaam om replicatie ondersteuning uit te scha kelen, tenzij u zeker weet dat u nooit een replica maakt op deze hoofd server. U kunt ondersteuning voor replicatie niet uitschakelen als uw hoofd server bestaande replica's heeft.


## <a name="create-a-read-replica"></a>Een lees replica maken
Voer de volgende stappen uit om een lees replica te maken:

1. Selecteer de bestaande Azure Database for PostgreSQL-server om te gebruiken als de hoofd server. 

2. Selecteer op de zijbalk van de server onder **instellingen**de optie **replicatie**.

3. Selecteer **replica toevoegen**.

   ![Een replica toevoegen](./media/howto-read-replicas-portal/add-replica.png)

4. Voer een naam in voor de Lees replica. 

    ![De replica een naam](./media/howto-read-replicas-portal/name-replica.png)

5. Selecteer een locatie voor de replica. De standaard locatie is dezelfde als die van de hoofd server.

    ![Selecteer een locatie](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > Ga naar het [artikel concepten van replica's lezen](concepts-read-replicas.md)voor meer informatie over de regio's die u kunt maken in de replica. 

6. Selecteer **OK** om te bevestigen dat de replica is gemaakt.

Een replica wordt gemaakt met behulp van dezelfde berekenings-en opslag instellingen als de hoofd server. Nadat een replica is gemaakt, kunnen verschillende instellingen onafhankelijk van de hoofd server worden gewijzigd: generatie van compute, vCores, opslag en back-up van Bewaar periode. De prijs categorie kan ook onafhankelijk worden gewijzigd, met uitzonde ring van of van de Basic-laag.

> [!IMPORTANT]
> Werk de replica-instelling bij naar een gelijke of grotere waarde voordat een master server-instelling wordt bijgewerkt naar een nieuwe waarde. Met deze actie wordt de replica zo aangepast dat er wijzigingen in de master worden aangebracht.

Nadat de Lees replica is gemaakt, kan deze worden weer gegeven in het venster **replicatie** :

![De nieuwe replica weer geven in het venster replicatie](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Replicatie stoppen
U kunt de replicatie tussen een hoofd server en een lees replica stoppen.

> [!IMPORTANT]
> Nadat u de replicatie naar een hoofd server en een lees replica hebt gestopt, kunt u deze niet meer ongedaan maken. De Lees replica wordt een zelfstandige server die zowel lees-als schrijf bewerkingen ondersteunt. De zelfstandige server kan niet opnieuw in een replica worden gemaakt.

Voer de volgende stappen uit om de replicatie tussen een hoofd server en een lees replica van de Azure Portal te stoppen:

1. Selecteer in de Azure Portal uw Master Azure Database for PostgreSQL-server.

2. Selecteer in het menu Server onder **instellingen**de optie **replicatie**.

3. Selecteer de replica server waarvoor u de replicatie wilt stoppen.

   ![De replica selecteren](./media/howto-read-replicas-portal/select-replica.png)
 
4. Selecteer **Replicatie stoppen**.

   ![Replicatie stoppen selecteren](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Selecteer **OK** om de replicatie te stoppen.

   ![Bevestigen om replicatie te stoppen](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Een hoofd server verwijderen
Als u een master server wilt verwijderen, gebruikt u dezelfde stappen als voor het verwijderen van een zelfstandige Azure Database for PostgreSQL-server. 

> [!IMPORTANT]
> Wanneer u een master-server verwijdert, wordt de replicatie naar alle Lees replica's gestopt. De Lees replica's worden zelfstandige servers die nu zowel lees-als schrijf bewerkingen ondersteunen.

Voer de volgende stappen uit om een server te verwijderen uit de Azure Portal:

1. Selecteer in de Azure Portal uw Master Azure Database for PostgreSQL-server.

2. Open de pagina **overzicht** voor de-server. Selecteer **Verwijderen**.

   ![Selecteer op de pagina overzicht van de server om de hoofd server te verwijderen](./media/howto-read-replicas-portal/delete-server.png)
 
3. Voer de naam in van de hoofd server die u wilt verwijderen. Selecteer **verwijderen** om te bevestigen dat de hoofd server moet worden verwijderd.

   ![Bevestigen dat de hoofd server moet worden verwijderd](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Een replica verwijderen
U kunt een lees replica verwijderen die vergelijkbaar is met de manier waarop u een hoofd server verwijdert.

- Open in de Azure Portal de pagina **overzicht** voor de Lees replica. Selecteer **Verwijderen**.

   ![Selecteer op de pagina overzicht van replica de optie om de replica te verwijderen](./media/howto-read-replicas-portal/delete-replica.png)
 
U kunt ook de replica lezen uit het venster **replicatie** verwijderen door de volgende stappen uit te voeren:

1. Selecteer in de Azure Portal uw Master Azure Database for PostgreSQL-server.

2. Selecteer in het menu Server onder **instellingen**de optie **replicatie**.

3. Selecteer de replica lezen die u wilt verwijderen.

   ![Selecteer de replica die u wilt verwijderen](./media/howto-read-replicas-portal/select-replica.png)
 
4. Selecteer **replica verwijderen**.

   ![Replica verwijderen selecteren](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Voer de naam in van de replica die u wilt verwijderen. Selecteer **verwijderen** om te bevestigen dat de replica moet worden verwijderd.

   ![Bevestigen dat de replica moet worden verwijderd](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Een replica bewaken
Er zijn twee metrische gegevens beschikbaar om Lees replica's te bewaken.

### <a name="max-lag-across-replicas-metric"></a>Maximale vertraging voor de metrische gegevens over Replica's
De **maximale vertraging** voor de metrische gegevens van replica's toont de vertraging in bytes tussen de hoofd server en de meest bewaarde replica. 

1.  Selecteer in de Azure Portal de Master Azure Database for PostgreSQL-server.

2.  Selecteer **Metrische gegevens**. In het venster **metrische gegevens** selecteert u **Max. vertraging in replica's**.

    ![De maximale vertraging voor verschillende replica's bewaken](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Voor uw **aggregatie**selecteert u **Max**.


### <a name="replica-lag-metric"></a>Metrische gegevens van replica vertraging
De metriek van de **replica vertraging** toont de tijd sinds de laatste opnieuw geplayte trans actie op een replica. Als er geen trans acties plaatsvinden in uw Master, weerspiegelt de metriek deze tijds periode.

1. Selecteer in de Azure Portal de Azure Database for PostgreSQL replica lezen.

2. Selecteer **Metrische gegevens**. Selecteer **replica vertraging**in het venster **metrische gegevens** .

   ![De replica vertraging bewaken](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Voor uw **aggregatie**selecteert u **Max**. 
 
## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [lezen van replica's in azure database for PostgreSQL](concepts-read-replicas.md).
* Meer informatie over het [maken en beheren van Lees replica's in azure CLI en rest API](howto-read-replicas-cli.md).