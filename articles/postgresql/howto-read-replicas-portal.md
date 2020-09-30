---
title: Lees replica's beheren-Azure Portal-Azure Database for PostgreSQL-één server
description: Meer informatie over het beheren van Lees replica's Azure Database for PostgreSQL-één server van de Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: d0de485158477496419dc394aaf6bdd1d28e772a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535811"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Lees replica's maken en beheren in Azure Database for PostgreSQL-één server van de Azure Portal

In dit artikel leert u hoe u in Azure Database for PostgreSQL Lees replica's maakt en beheert via de Azure Portal. Zie het [overzicht](concepts-read-replicas.md)voor meer informatie over het lezen van replica's.


## <a name="prerequisites"></a>Vereisten
Een [Azure database for postgresql-server](quickstart-create-server-database-portal.md) als primaire server.

## <a name="azure-replication-support"></a>Ondersteuning voor Azure-replicatie

Het [lezen van replica's](concepts-read-replicas.md) en [logische decodering](concepts-logical.md) is beide afhankelijk van het post gres-logboek (write-Ahead) (Wal) voor informatie. Deze twee functies hebben verschillende niveaus van logboek registratie nodig van post gres. Voor logische decodering is een hoger niveau van logboek registratie vereist dan bij het lezen van replica's.

Als u het juiste niveau van logboek registratie wilt configureren, gebruikt u de Azure Replication support-para meter. Ondersteuning voor Azure-replicatie heeft drie instellings opties:

* **Uit** : Hiermee wordt de minste informatie in de wal geplaatst. Deze instelling is niet beschikbaar op de meeste Azure Database for PostgreSQL-servers.  
* **Replica** : uitgebreidere dan **uit**. Dit is het minimale registratie niveau dat nodig is voor het werken met [replica's](concepts-read-replicas.md) . Deze instelling is de standaard waarde op de meeste servers.
* **Logisch** -uitgebreidere dan de **replica**. Dit is het minimale registratie niveau voor het werken met logische code ring. Het lezen van replica's werkt ook bij deze instelling.

De server moet opnieuw worden opgestart na het wijzigen van deze para meter. Intern worden met deze para meter de post gres-para meters `wal_level` , `max_replication_slots` en ingesteld `max_wal_senders` .

## <a name="prepare-the-primary-server"></a>De primaire server voorbereiden

1. Selecteer in de Azure Portal een bestaande Azure Database for PostgreSQL-server die als een Master moet worden gebruikt.

2. Selecteer in het menu van de server de optie **replicatie**. Als ondersteuning voor Azure-replicatie is ingesteld op ten minste een **replica**, kunt u lees replica's maken. 

3. Als Azure-replicatie ondersteuning niet is ingesteld op ten minste een **replica**, stelt u deze in. Selecteer **Opslaan**.

   :::image type="content" source="./media/howto-read-replicas-portal/set-replica-save.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::

4. Start de server opnieuw op om de wijziging toe te passen door **Ja**te selecteren.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-restart.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::

5. Zodra de bewerking is voltooid, ontvangt u twee meldingen over Azure Portal. Er is één melding voor het bijwerken van de server parameter. Er is een andere melding voor de herstart van de server die onmiddellijk volgt.

   :::image type="content" source="./media/howto-read-replicas-portal/success-notifications.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::

6. Vernieuw de Azure Portal pagina om de werk balk replicatie bij te werken. U kunt nu lees replica's maken voor deze server.
   

## <a name="create-a-read-replica"></a>Een leesreplica maken
Voer de volgende stappen uit om een lees replica te maken:

1. Selecteer een bestaande Azure Database for PostgreSQL-server om te gebruiken als de primaire server. 

2. Selecteer op de zijbalk van de server onder **instellingen**de optie **replicatie**.

3. Selecteer **replica toevoegen**.

   :::image type="content" source="./media/howto-read-replicas-portal/add-replica.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::

4. Voer een naam in voor de Lees replica. 

    :::image type="content" source="./media/howto-read-replicas-portal/name-replica.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::

5. Selecteer een locatie voor de replica. De standaard locatie is dezelfde als die van de primaire server.

    :::image type="content" source="./media/howto-read-replicas-portal/location-replica.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::

   > [!NOTE]
   > Ga naar het [artikel concepten van replica's lezen](concepts-read-replicas.md)voor meer informatie over de regio's die u kunt maken in de replica. 

6. Selecteer **OK** om te bevestigen dat de replica is gemaakt.

Nadat de Lees replica is gemaakt, kan deze worden weer gegeven in het venster **replicatie** :

:::image type="content" source="./media/howto-read-replicas-portal/list-replica.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::
 

> [!IMPORTANT]
> Raadpleeg de [sectie overwegingen in het overzicht van het lezen van replica's](concepts-read-replicas.md#considerations).
>
> Werk de replica-instelling bij naar een gelijke of grotere waarde voordat een primaire server instelling is bijgewerkt naar een nieuwe waarde. Met deze actie wordt de replica zo aangepast dat er wijzigingen in de master worden aangebracht.

## <a name="stop-replication"></a>Replicatie stoppen
U kunt de replicatie tussen een primaire server en een lees replica stoppen.

> [!IMPORTANT]
> Nadat u de replicatie naar een primaire server en een lees replica hebt gestopt, kunt u deze niet meer ongedaan maken. De Lees replica wordt een zelfstandige server die zowel lees-als schrijf bewerkingen ondersteunt. De zelfstandige server kan niet opnieuw in een replica worden gemaakt.

Voer de volgende stappen uit om de replicatie tussen een primaire server en een lees replica van de Azure Portal te stoppen:

1. Selecteer in de Azure Portal uw primaire Azure Database for PostgreSQL-server.

2. Selecteer in het menu Server onder **instellingen**de optie **replicatie**.

3. Selecteer de replica server waarvoor u de replicatie wilt stoppen.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::
 
4. Selecteer **Replicatie stoppen**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-stop-replication.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::
 
5. Selecteer **OK** om de replicatie te stoppen.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-stop-replication.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::
 

## <a name="delete-a-primary-server"></a>Een primaire server verwijderen
Als u een primaire server wilt verwijderen, gebruikt u dezelfde stappen als voor het verwijderen van een zelfstandige Azure Database for PostgreSQL-server. 

> [!IMPORTANT]
> Wanneer u een primaire server verwijdert, wordt replicatie naar alle Lees replica's gestopt. De Lees replica's worden zelfstandige servers die nu zowel lees-als schrijf bewerkingen ondersteunen.

Voer de volgende stappen uit om een server te verwijderen uit de Azure Portal:

1. Selecteer in de Azure Portal uw primaire Azure Database for PostgreSQL-server.

2. Open de pagina **overzicht** voor de-server. Selecteer **Verwijderen**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-server.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::
 
3. Voer de naam in van de primaire server die u wilt verwijderen. Selecteer **verwijderen** om te bevestigen dat de primaire server moet worden verwijderd.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::
 

## <a name="delete-a-replica"></a>Een replica verwijderen
U kunt een lees replica verwijderen die vergelijkbaar is met de manier waarop u een primaire server verwijdert.

- Open in de Azure Portal de pagina **overzicht** voor de Lees replica. Selecteer **Verwijderen**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-replica.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::
 
U kunt ook de replica lezen uit het venster **replicatie** verwijderen door de volgende stappen uit te voeren:

1. Selecteer in de Azure Portal uw primaire Azure Database for PostgreSQL-server.

2. Selecteer in het menu Server onder **instellingen**de optie **replicatie**.

3. Selecteer de replica lezen die u wilt verwijderen.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::
 
4. Selecteer **replica verwijderen**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-delete-replica.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::
 
5. Voer de naam in van de replica die u wilt verwijderen. Selecteer **verwijderen** om te bevestigen dat de replica moet worden verwijderd.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete-replica.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::
 

## <a name="monitor-a-replica"></a>Een replica bewaken
Er zijn twee metrische gegevens beschikbaar om Lees replica's te bewaken.

### <a name="max-lag-across-replicas-metric"></a>Maximale vertraging voor de metrische gegevens over Replica's
De **maximale vertraging** voor de metrische gegevens van replica's toont de vertraging in bytes tussen de primaire server en de meest bewaarde replica. 

1.  Selecteer de primaire Azure Database for PostgreSQL-server in het Azure Portal.

2.  Selecteer **Metrische gegevens**. In het venster **metrische gegevens** selecteert u **Max. vertraging in replica's**.

    :::image type="content" source="./media/howto-read-replicas-portal/select-max-lag.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::
 
3.  Voor uw **aggregatie**selecteert u **Max**.


### <a name="replica-lag-metric"></a>Metrische gegevens van replica vertraging
De metriek van de **replica vertraging** toont de tijd sinds de laatste opnieuw geplayte trans actie op een replica. Als er geen trans acties plaatsvinden in uw Master, weerspiegelt de metriek deze tijds periode.

1. Selecteer in de Azure Portal de Azure Database for PostgreSQL replica lezen.

2. Selecteer **Metrische gegevens**. Selecteer **replica vertraging**in het venster **metrische gegevens** .

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica-lag.png" alt-text="Azure Database for PostgreSQL replicatie: replica instellen en opslaan":::
 
3. Voor uw **aggregatie**selecteert u **Max**. 
 
## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [lezen van replica's in azure database for PostgreSQL](concepts-read-replicas.md).
* Meer informatie over het [maken en beheren van Lees replica's in azure CLI en rest API](howto-read-replicas-cli.md).