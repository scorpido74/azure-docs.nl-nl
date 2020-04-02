---
title: Logische decodering - Azure Database voor PostgreSQL - Single Server
description: Beschrijft logische decodering en wal2json voor het vastleggen van wijzigingen in Azure Database voor PostgreSQL - Single Server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522145"
---
# <a name="logical-decoding"></a>Logische decodering
 
[Met logische decodering in PostgreSQL](https://www.postgresql.org/docs/current/logicaldecoding.html) u gegevenswijzigingen streamen naar externe consumenten. Logische decodering wordt in de volksmond gebruikt voor het streamen van gebeurtenissen en het wijzigen van scenario's voor het vastleggen van gegevens.

Logische decodering maakt gebruik van een uitvoerplugin om de schrijflogboek (WAL) van Postgres om te zetten in een leesbaar formaat. Azure Database voor PostgreSQL biedt twee uitvoerplug-ins: [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) en [wal2json](https://github.com/eulerto/wal2json).
 

> [!NOTE]
> Logische decodering is in openbare preview op Azure Database voor PostgreSQL - Single Server.


## <a name="set-up-your-server"></a>Uw server instellen
Als u wilt beginnen met het gebruik van logische decodering, schakelt u de server in om de WAL op te slaan en te streamen. 

1. Azure.replication_support instellen `logical` op het gebruik van de Azure CLI. 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > Als u leesreplica's gebruikt, `logical` replication_support azure.ingesteld op het uitvoeren van replica's. Als u stopt met het gebruik van `replica`logische decodering, wijzigt u de instelling terug in . 


2. Start de server opnieuw om de wijzigingen toe te passen.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>Logische decodering starten

Logische decodering kan worden verbruikt via streaming protocol of SQL-interface. Beide methoden maken gebruik van [replicatiesleuven](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS). Een sleuf vertegenwoordigt een stroom van wijzigingen uit één database.

Voor het gebruik van een replicatiesleuf zijn de replicatiebevoegdheden van Postgres vereist. Op dit moment is de replicatiebevoegdheid alleen beschikbaar voor de beheerder van de server. 

### <a name="streaming-protocol"></a>Streamingprotocol
Het consumeren van wijzigingen met behulp van het streaming protocol heeft vaak de voorkeur. U uw eigen consument / connector, of gebruik maken van een tool als [Debezium](https://debezium.io/). 

Bezoek de wal2json documentatie voor [een voorbeeld met behulp van het streaming protocol met pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>SQL-interface
In het onderstaande voorbeeld gebruiken we de SQL-interface met de wal2json-plug-in.
 
1. Maak een sleuf.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. SQL-opdrachten uitgeven. Bijvoorbeeld:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Gebruik de wijzigingen.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   De uitvoer ziet eruit als volgt:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Laat de sleuf vallen zodra u klaar bent met het gebruik ervan.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Controlesleuven

U moet logische decodering controleren. Elke ongebruikte replicatiesleuf moet worden verwijderd. Slots houden Postgres WAL logs en relevante systeemcatalogi vast totdat wijzigingen zijn gelezen door een consument. Als uw consument faalt of niet goed is geconfigureerd, worden de niet-geconsumeerde logboeken opgevuld en uw opslag gevuld. Ook verhogen niet-geconsumeerde logboeken het risico op transactie-ID wraparound. In beide situaties kan de server niet meer beschikbaar zijn. Daarom is het van cruciaal belang dat logische replicatieslots continu worden verbruikt. Als een logische replicatiesleuf niet meer wordt gebruikt, laat u deze onmiddellijk vallen.

De kolom 'actief' in de pg_replication_slots weergave geeft aan of er een consument is aangesloten op een sleuf.
```SQL
SELECT * FROM pg_replication_slots;
```

[Stel waarschuwingen](howto-alert-on-metric.md) in op *Gebruikte opslag* en *Max-vertraging voor replica's* statistieken om u op de hoogte te stellen wanneer de waarden boven normale drempels uitkomen. 

> [!IMPORTANT]
> U moet ongebruikte replicatiesleuven laten vallen. Als u dit niet doet, kan dit leiden tot onbeschikbaarheid van de server.

## <a name="how-to-drop-a-slot"></a>Hoe een sleuf te laten vallen
Als u een replicatiesleuf niet actief verbruikt, moet u deze laten vallen.

Een replicatiesleuf laten `test_slot` vallen die SQL gebruikt:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Als u stopt met het gebruik van `replica` logische `off`decodering, wijzigt u azure.replication_support terug naar of . De WAL-details `logical` die door worden bewaard, zijn meer verbose en moeten worden uitgeschakeld wanneer logische decodering niet in gebruik is. 

 
## <a name="next-steps"></a>Volgende stappen

* Ga naar de Documentatie van Postgres voor [meer informatie over logische decodering.](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)
* Neem contact op met [ons team](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) als u vragen heeft over logische decodering.
* Meer informatie over [gelezen replica's](concepts-read-replicas.md).

