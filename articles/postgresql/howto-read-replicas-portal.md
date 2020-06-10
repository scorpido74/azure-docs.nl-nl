---
title: Lees replica's beheren-Azure Portal-Azure Database for PostgreSQL-één server
description: Meer informatie over het beheren van Lees replica's Azure Database for PostgreSQL-één server van de Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: c7d55a7b10f0c874fd84f32db1dcf21fb60c231f
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636623"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Lees replica's maken en beheren in Azure Database for PostgreSQL-één server van de Azure Portal

In dit artikel leert u hoe u in Azure Database for PostgreSQL Lees replica's maakt en beheert via de Azure Portal. Zie het [overzicht](concepts-read-replicas.md)voor meer informatie over het lezen van replica's.


## <a name="prerequisites"></a>Vereisten
Een [Azure database for postgresql-server](quickstart-create-server-database-portal.md) als de hoofd server.

## <a name="azure-replication-support"></a>Ondersteuning voor Azure-replicatie

Het [lezen van replica's](concepts-read-replicas.md) en [logische decodering](concepts-logical.md) is beide afhankelijk van het post gres-logboek (write-Ahead) (Wal) voor informatie. Deze twee functies hebben verschillende niveaus van logboek registratie nodig van post gres. Voor logische decodering is een hoger niveau van logboek registratie vereist dan bij het lezen van replica's.

Als u het juiste niveau van logboek registratie wilt configureren, gebruikt u de Azure Replication support-para meter. Ondersteuning voor Azure-replicatie heeft drie instellings opties:

* **Uit** : Hiermee wordt de minste informatie in de wal geplaatst. Deze instelling is niet beschikbaar op de meeste Azure Database for PostgreSQL-servers.  
* **Replica** : uitgebreidere dan **uit**. Dit is het minimale registratie niveau dat nodig is voor het werken met [replica's](concepts-read-replicas.md) . Deze instelling is de standaard waarde op de meeste servers.
* **Logisch** -uitgebreidere dan de **replica**. Dit is het minimale registratie niveau voor het werken met logische code ring. Het lezen van replica's werkt ook bij deze instelling.

De server moet opnieuw worden opgestart na het wijzigen van deze para meter. Intern worden met deze para meter de post gres-para meters `wal_level` , `max_replication_slots` en ingesteld `max_wal_senders` .

## <a name="prepare-the-master-server"></a>De hoofd server voorbereiden

1. Selecteer in de Azure Portal een bestaande Azure Database for PostgreSQL-server die als een Master moet worden gebruikt.

2. Selecteer in het menu van de server de optie **replicatie**. Als ondersteuning voor Azure-replicatie is ingesteld op ten minste een **replica**, kunt u lees replica's maken. 

3. Als Azure-replicatie ondersteuning niet is ingesteld op ten minste een **replica**, stelt u deze in. Selecteer **Opslaan**.

   ![Azure Database for PostgreSQL replicatie: replica instellen en opslaan](./media/howto-read-replicas-portal/set-replica-save.png)

4. Start de server opnieuw op om de wijziging toe te passen door **Ja**te selecteren.

   ![Azure Database for PostgreSQL-replicatie: bevestig opnieuw opstarten](./media/howto-read-replicas-portal/confirm-restart.png)

5. Zodra de bewerking is voltooid, ontvangt u twee meldingen over Azure Portal. Er is één melding voor het bijwerken van de server parameter. Er is een andere melding voor de herstart van de server die onmiddellijk volgt.

   ![Geslaagde meldingen](./media/howto-read-replicas-portal/success-notifications.png)

6. Vernieuw de Azure Portal pagina om de werk balk replicatie bij te werken. U kunt nu lees replica's maken voor deze server.
   

## <a name="create-a-read-replica"></a>Een lees replica maken
Voer de volgende stappen uit om een lees replica te maken:

1. Selecteer een bestaande Azure Database for PostgreSQL-server om te gebruiken als de hoofd server. 

2. Selecteer op de zijbalk van de server onder **instellingen**de optie **replicatie**.

3. Selecteer **replica toevoegen**.

   ![Een replica toevoegen](./media/howto-read-replicas-portal/add-replica.png)

4. Voer een naam in voor de Lees replica. 

    ![De replica een naam](./media/howto-read-replicas-portal/name-replica.png)

5. Selecteer een locatie voor de replica. De standaard locatie is dezelfde als die van de hoofd server.

    ![Een locatie selecteren](./media/howto-read-replicas-portal/location-replica.png)

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